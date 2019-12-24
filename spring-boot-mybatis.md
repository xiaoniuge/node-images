# spring boot + mybatis

**1）、引入maven依赖**

```XML
 		<dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>${mybatis-boot-version}</version>
        </dependency>
```

**2）、配置yml**

```yaml
mybatis:
  mapper-locations: classpath:mapper/*Mapper.xml
  type-aliases-package: com.XXX.model
```

**3）、使用SqlSession 创建自己的 公共dao ；分页工具paging 和 pageinfo**

```java
package com.curry.common.mybatis;

import com.curry.common.model.Paging;
import com.google.common.collect.Lists;
import com.google.common.collect.Maps;
import org.apache.ibatis.session.SqlSession;
import org.springframework.beans.factory.annotation.Autowired;

import java.lang.reflect.ParameterizedType;
import java.util.List;
import java.util.Map;

public class MyBatisDao<T> {

    @Autowired
    private SqlSession sqlSession;

    private String namespace;

    private final static String CREATE = "create";
    private final static String UPDATE = "update";
    private final static String DELETE_BY_ID = "deleteById";
    private final static String FIND_BY_ID = "findById";
    private final static String FIND_BY_IDS = "findByIds";
    private final static String LIST = "list";
    private final static String PAGING = "paging";
    private final static String COUNT = "count";

    public MyBatisDao() {
        if (this.getClass().getGenericSuperclass() instanceof ParameterizedType){
            this.namespace = ((Class)((ParameterizedType) this.getClass().getGenericSuperclass()).getActualTypeArguments()[0]).getSimpleName();
        } else {
            this.namespace = ((Class)((ParameterizedType)this.getClass().getSuperclass().getGenericSuperclass()).getActualTypeArguments()[0]).getSimpleName();
        }
    }

    public Boolean create(T t){
        return this.sqlSession.insert(sqlId(CREATE),t) == 1;
    }

    public Boolean update(T t){
        return this.sqlSession.update(sqlId(UPDATE),t) == 1;
    }

    public Integer deleteById(Long id){
        return this.sqlSession.delete(sqlId(DELETE_BY_ID),id);
    }

    public T findById(Long id){
        return this.sqlSession.selectOne(sqlId(FIND_BY_ID),id);
    }

    public List<T> findByIds(List<Long> ids){
        if (ids == null || ids.isEmpty()){
            return Lists.newArrayList();
        }
        return this.sqlSession.selectList(sqlId(FIND_BY_IDS),ids);
    }

    public List<T> list(){
        return list(null);
    }

    public List<T> list(Map<String,Object> criteria){
        if (criteria == null){
            criteria = Maps.newHashMap();
        }
        return this.sqlSession.selectOne(sqlId(LIST),criteria);
    }

    public Paging<T> paging(Integer offset , Integer limit){
        return paging(offset,limit,null);
    }

    public Paging<T> paging(Integer offset , Integer limit , Map<String , Object> criteria){
        if (criteria == null){
            criteria = Maps.newHashMap();
        }
        Long total = this.sqlSession.selectOne(sqlId(COUNT),criteria);
        if (total <= 0L){
            return new Paging<T>(0L, Lists.newArrayList());
        }
        criteria.put("offset",offset);
        criteria.put("limit",limit);
        List<T> data = this.sqlSession.selectList(sqlId(PAGING),criteria);
        return new Paging<T>(total,data);
    }

    public SqlSession getSqlSession(){
        return sqlSession;
    }

    public String sqlId(String id){
        return namespace + "." + id;
    }
}



package com.curry.common.model;

import com.google.common.base.MoreObjects;
import com.google.common.base.Strings;
import com.google.common.collect.Maps;
import lombok.Data;

import java.util.Map;

@Data
public class PageInfo {

    private static final String LIMIT = "limit";

    private static final String OFFSET = "offset";

    private Integer offset;

    private Integer limit;

    public PageInfo(){}

    public PageInfo(Integer pageNo, Integer pageSize) {
        pageNo = MoreObjects.firstNonNull(pageNo, 1);
        pageSize = MoreObjects.firstNonNull(pageNo, 20);
        this.limit = pageSize > 0 ? pageSize : 20;
        this.offset = (pageNo - 1) * pageSize;
        this.offset = this.offset > 0 ? this.offset : 0;
    }

    public static PageInfo of(Integer pageNo, Integer pageSize) {
        return new PageInfo(pageNo, pageSize);
    }

    public Map<String, Object> toMap() {
        return this.toMap(null, null);
    }

    public Map<String, Object> toMap(String key1, String key2) {
        Map<String , Object> param = Maps.newHashMapWithExpectedSize(2);
        param.put(Strings.isNullOrEmpty(key1) ? OFFSET : key1, this.offset);
        param.put(Strings.isNullOrEmpty(key2) ? LIMIT : key2, this.limit);
        return param;
    }
}


package com.curry.common.model;


import lombok.Data;

import java.io.Serializable;
import java.util.List;

@Data
public class Paging<T> implements Serializable {

    private Long total;

    private List<T> data;

    public Paging(){}   

    public Paging(Long total, List<T> data) {
        this.total = total;
        this.data = data;
    }
    public Boolean isEmpty(){
        return data == null || data.isEmpty();
    }
}


```



​       

