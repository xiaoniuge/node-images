# spring boot + swagger2-ui

**一、添加依赖**

```xml
	   <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>${swagger.version}</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>${swagger.version}</version>
        </dependency>
```

**二、配置**

```java
@Configuration
@EnableSwagger2 //启用swagger2-ui
@Profile({"dev","test"}) // 启用的环境
public class SwaggerConfig {

    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo()).select()
                .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("API文档")
                .description("测试")
                .version("2.0.0")
                .build();
    }

}
```

**三、controller上使用的注解**

```java
@RestController
@Slf4j
@Api(tags = {"用户接口"}) // 分组标题
public class SwaggerController {
	
    //接口的描述，以及请求方式
    @ApiOperation(value = "map",httpMethod = "POST")
    //接口的参数描述，数据类型描述 （可以不写）
    @ApiImplicitParam(name = "user",value = "user实体类",required = true,dataType = "User")
    @PostMapping("/map")
    public User map(@RequestBody User user){
        return user;
    }
}
```

**四、实体类上使用的注解**

```java
@Data
@ApiModel(value="User",description="用户对象") //用对象来接收参数
public class User {

	// 实体类的属性描述
    @ApiModelProperty(value = "用户ID")
    private Long id;

    @ApiModelProperty(value = "用户名")
    private String name;

    @ApiModelProperty(value = "用户昵称")
    private String nick;
}
```