# Generator

```JAVA
package com.mybatis.plus.generator;

import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.core.toolkit.StringPool;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.InjectionConfig;
import com.baomidou.mybatisplus.generator.config.*;
import com.baomidou.mybatisplus.generator.config.po.TableInfo;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;
import com.baomidou.mybatisplus.generator.engine.FreemarkerTemplateEngine;

import java.util.Collections;
import java.util.HashMap;
import java.util.Map;

public class PlusGenerator {

    private static final String DATABASE = "curry";
    private static final String URL = "jdbc:mysql://localhost/" + DATABASE + "?useSSL=false&serverTimezone=Hongkong&allowPublicKeyRetrieval=true";
    private static final String DRIVER = "com.mysql.cj.jdbc.Driver";
    private static final String USER_NAME = "root";
    private static final String PASSWORD = "1234567890";

    private static String[] tables = new String[]{"users","user_profiles"};

    private static String basePath = "";
    private static String mapperPath = "";

    public static void main(String[] args) {
        String author = "curry";
        String packageName = "com.mybatis.plus";
        String[] tablePrefix = null;
        execute(author,packageName,tablePrefix);
    }

    private static void execute(String author , String packageName , String... tablePrefix){
        AutoGenerator autoGenerator = new AutoGenerator()
                .setDataSource(initDataSourceConfig())
                .setGlobalConfig(initGlobalConfig(author,packageName))
                .setStrategy(initStrategyConfig())
                .setPackageInfo(initPackageConfig(packageName))
                .setCfg(initInjectionConfig(packageName))
                .setTemplate(new TemplateConfig().setXml(null).setMapper(null))
                .setTemplateEngine(new FreemarkerTemplateEngine());
        autoGenerator.execute();
    }

    /**
     * 配置数据源
     * @return DataSourceConfig
     */
    private static DataSourceConfig initDataSourceConfig() {
        return new DataSourceConfig()
                .setDbType(DbType.MYSQL)
                .setUrl(URL)
                .setDriverName(DRIVER)
                .setUsername(USER_NAME)
                .setPassword(PASSWORD);
    }

    /**
     * 全局配置
     * @return GlobalConfig
     */
    private static GlobalConfig initGlobalConfig(String author, String packageName) {
        GlobalConfig gc = new GlobalConfig();
        String tmp = PlusGenerator.class.getResource("").getPath();
        String codeDir = tmp.substring(0, tmp.indexOf("/target"));
        basePath = codeDir + "/src/main/java";
        mapperPath = basePath + "/" + packageName.replace(".", "/") + "/dao";
        System.out.println("basePath = " + basePath + "\nmapperPath = " + mapperPath);
        gc.setOutputDir(basePath);
        gc.setAuthor(author);
        gc.setOpen(false);
        gc.setFileOverride(true);
        gc.setFileOverride(true);// 是否覆盖文件
        gc.setActiveRecord(true);// 开启 activeRecord 模式
        gc.setEnableCache(false);// XML 二级缓存
        gc.setBaseResultMap(true);// XML ResultMap
        gc.setBaseColumnList(true);// XML columList
        gc.setOpen(false);//生成后打开文件夹
        gc.setMapperName("%sDao");// 自定义文件命名，注意 %s 会自动填充表实体属性！
        gc.setXmlName("%sMapper");
        gc.setServiceName("%sService");
        gc.setServiceImplName("%sServiceImpl");
        gc.setControllerName("%sController");
        return gc;
    }

    private static StrategyConfig initStrategyConfig(){
        return new StrategyConfig()
                .setNaming(NamingStrategy.underline_to_camel)// 表名生成策略
                .setInclude(tables) // 需要生成的表
                .setRestControllerStyle(true)
                .setEntityLombokModel(true);
    }

    private static PackageConfig initPackageConfig(String packageName){
        return new PackageConfig()
                .setParent(packageName)// 自定义包路径
                .setController("controller")// 这里是控制器包名，默认 web
                .setEntity("model")
                .setMapper("dao")
                .setService("service")
                .setServiceImpl("service.impl");
    }

    private static InjectionConfig initInjectionConfig(String packageName){
        return new InjectionConfig() {// 注入自定义配置，可以在 VM 中使用 cfg.abc 设置的值
                    @Override
                    public void initMap() {
                        Map<String, Object> map = new HashMap<>();
                        map.put("abc", this.getConfig().getGlobalConfig().getAuthor() + "-mp");
                        this.setMap(map);
                    }
                }.setFileOutConfigList(Collections.<FileOutConfig>singletonList(new FileOutConfig(
                        "/templates/mapper.xml.ftl" ) {
                    // 自定义输出文件目录
                    @Override
                    public String outputFile(TableInfo tableInfo) {
                        // 自定义输入文件名称
                        String s = "E:\\work\\mybatis-plus" + "/src/main/resources/mapper/" +
                                tableInfo.getEntityName() + "Mapper" + StringPool.DOT_XML;
                        return s;
                    }
                }));
    }
}

```





**依赖**

```XML
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-generator</artifactId>
            <version>3.3.0</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-freemarker</artifactId>
        </dependency>
```



# mybatis-plus

```JAVA
@SpringBootApplication
@MapperScan("com.mybatis.plus.dao") //
public class MybatisPlusApplication {

    public static void main(String[] args) {
        SpringApplication.run(MybatisPlusApplication.class, args);
    }

}
```



```yaml
mybatis-plus:
  type-aliases-package: com.mybatis.plus.model
  mapper-locations: classpath*:mapper/*Mapper.xml
```



