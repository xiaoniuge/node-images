# MongoDB的常用方法封装

**JAVA实现**

```JAVA
package com.db.common.mongo;

import com.db.common.common.model.Paging;
import com.db.common.common.utils.HumpFormatUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.data.mongodb.core.MongoTemplate;
import org.springframework.data.mongodb.core.query.Criteria;
import org.springframework.data.mongodb.core.query.Query;
import org.springframework.data.mongodb.core.query.Update;

import java.lang.reflect.Field;
import java.lang.reflect.ParameterizedType;
import java.util.*;

/**
 * 建议 MongoDB 创建时 自动生成一个 id
 * @param <T>
 */
public class MongoBaseDao<T> {

    @Autowired
    private MongoTemplate mongoTemplate;

    private Class<T> clazz;

    public MongoBaseDao() {
        if (this.getClass().getGenericSuperclass() instanceof ParameterizedType){
            this.clazz = (Class<T>) ((ParameterizedType) this.getClass().getGenericSuperclass()).getActualTypeArguments()[0];
        } else
            this.clazz = (Class<T>) ((ParameterizedType) this.getClass().getSuperclass().getGenericSuperclass()).getActualTypeArguments()[0];
    }

    /**
     * 新插入一条记录
     * @param t 对象
     * @return T
     */
    public T create(T t){
        return mongoTemplate.insert(t);
    }

    /**
     * 新插入多条记录
     * @param ts 对象集合
     * @return List<T>
     */
    public List<T> creates(List<T> ts){
        if (ts == null || ts.isEmpty()){
            return new ArrayList<>();
        }
        Collection<T> result = mongoTemplate.insert(ts,clazz);
        return new ArrayList<>(result);
    }

    /**
     * 根据id 更新某一条记录
     *
     * @param t 对象
     * @param id mongoDB 的 id
     * @return Boolean
     */
    public Boolean update(T t,String id){
        Query query = getQuery(id);
        Update update = getUpdate(t);
        return mongoTemplate.updateFirst(query,update,clazz).getModifiedCount() == 1;
    }

    /**
     * 根据criteria更新某一条记录
     *
     * 推荐使用
     *
     * @param t 对象
     * @param criteria 为参数 （key 为MongoDB 数据表字段）
     * @return Boolean
     */
    public Boolean updateByCriteria(T t , Map<String,Object> criteria){
        Update update = getUpdate(t);
        Query query = getQuery(criteria);
        return mongoTemplate.updateFirst(query,update,clazz).getModifiedCount() == 1;
    }

    /**
     * 批量更新
     * @param set 要修改的内容
     * @param criteria 条件
     * @return Long 修改了多少
     */
    public Long updateByCriteria(Map<String,Object> set , Map<String,Object> criteria){
        return mongoTemplate.updateMulti(getQuery(criteria),getUpdate(set),clazz).getModifiedCount();
    }

    /**
     * 根据id 删除一条数据
     * @param id mongoDB 的 id
     * @return Boolean
     */
    public Boolean deleteById(String id){
       return mongoTemplate.remove(getQuery(id),clazz).getDeletedCount() == 1;
    }

    /**
     * 根据 ids 删除多条数据
     * @param ids mongoDB 的 id 的集合
     * @return Boolean
     */
    public Boolean deleteByIds(List<String> ids){
        return mongoTemplate.remove(getQuery(ids),clazz).getDeletedCount() == ids.size();
    }

    /**
     * 根据 参数 删除 记录
     *
     * 推荐使用
     *
     * @param criteria criteria （其key为数据库字段名）
     * @return Long
     */
    public Long deleteByCriteria(Map<String,Object> criteria){
        return mongoTemplate.remove(getQuery(criteria),clazz).getDeletedCount();
    }

    /**
     * 根据id查询一条记录
     * @param id mongoDB 的 id
     * @return T
     */
    public T findById(String id){
        return mongoTemplate.findById(id,clazz);
    }

    /**
     * 根据ids查询一条记录
     * @param ids mongoDB 的 id 的集合
     * @return T
     */
    public List<T> findByIds(List<String> ids){
        return mongoTemplate.find(getQuery(ids),clazz);
    }

    /**
     * 根据criteria查询多条记录
     * @param criteria criteria （其key为数据库字段名）
     * @return List<T>
     */
    public List<T> findByCriteria(Map<String,Object> criteria){
        return mongoTemplate.find(getQuery(criteria),clazz);
    }


    /**
     * 分页查询 不带任何参数
     * @param pageNo 页码
     * @param pageSize 条数
     * @return Paging<T>
     */
    public Paging<T> paging(Integer pageNo, Integer pageSize){
        return paging(pageNo,pageSize,null);
    }

    /**
     * 分页查询 参数
     *
     * 参数拼接 皆为 is
     *
     * @param pageNo 页码
     * @param pageSize 条数
     * @param criteria 查询条件 参数的 key 为 数据库字段
     * @return Paging<T>
     */
    public Paging<T> paging(Integer pageNo, Integer pageSize, Map<String,Object> criteria){
        Query query = new Query();
        if (criteria != null && !criteria.isEmpty()){
            query = getQuery(criteria);
        }
        Pageable pageable = PageRequest.of(pageNo,pageSize);
        Long total = mongoTemplate.count(query,clazz);
        List<T> data = mongoTemplate.find(query.with(pageable),clazz);
        return Paging.of(total,data);
    }


    /**
     * 创建需要更新的 update
     * @param t 对象 其内属性名 大驼峰 命名方式
     * @return Update
     */
    public Update getUpdate(T t) {
        Class tClass = t.getClass();
        Field[] fields = tClass.getFields();
        Update update = new Update();
        for (Field field : fields){
            field.setAccessible(true);
            try {
                //去除null值的字段 防止误更
                if (field.get(field.getName()) == null){
                    continue;
                }
                update.set(HumpFormatUtils.toSmall(field.getName()),field.get(field.getName()));
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            }
        }
        return update;
    }

    /**
     * 创建需要更新的 update
     * @param set set  key数据库字段名
     * @return Update
     */
    public Update getUpdate(Map<String,Object> set){
        Update update = new Update();
        set.forEach(update::set);
        return update;
    }

    /**
     * 根据 id 生成 查询条件
     * @param id id（mongodb 的id 非 实体类id）
     * @return Query
     */
    public Query getQuery(String id){
        return new Query(Criteria.where("_id").is(id));
    }

    /**
     * 根据 ids 生成 查询条件
     * @param ids ids（mongodb 的id 非 实体类id）
     * @return Query
     */
    public Query getQuery(List<String> ids){
        return getQuery(Collections.singletonList(ids),"_id");
    }

    /**
     * 根据 objects  key生成 查询条件
     * @param objects objects（mongodb 的某一字段值得集合）
     * @param key key mongodb 的某一字段
     * @return Query
     */
    public Query getQuery(List<Object> objects,String key){
        return new Query(Criteria.where(key).in(objects));
    }

    /**
     * 根据 param 生成 查询条件
     * @param param param 参数条件
     *
     * @return Query
     */
    public Query getQuery(Map<String,Object> param){
        Criteria criteria = new Criteria();
        param.forEach((key , value) -> criteria.and(key).is(value));
        return new Query(criteria);
    }


    public MongoTemplate mongoTemplate(){
        return mongoTemplate;
    }


}

```

**maven依赖**

```XML
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-mongodb</artifactId>
        </dependency>
```

