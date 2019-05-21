# Spring Boot issues

```
springBootVersion = '2.0.7.RELEASE'
```

## Swagger 2

build.gradle
```
implementation('io.springfox:springfox-swagger2:2.9.2')
```

When running tests:

```console
An attempt was made to call the method com.google.common.collect.FluentIterable.append(Ljava/lang/Iterable;)Lcom/google/common/collect/FluentIterable; but it does not exist. Its class, com.google.common.collect.FluentIterable, is available from the following locations:
...
Caused by: org.springframework.context.ApplicationContextException: Failed to start bean 'documentationPluginsBootstrapper'; nested exception is java.lang.NoSuchMethodError: com.google.common.collect.FluentIterable.append(Ljava/lang/Iterable;)Lcom/google/common/collect/FluentIterable;
```

Solution

Decrease swagger version to 2.8.0, add guava 27.0.1-jre

build.gradle
```
implementation('io.springfox:springfox-swagger2:2.8.0')
implementation('com.google.guava:guava:27.0.1-jre')
```

SwaggerConfiguration.java

```java
@Configuration
@EnableSwagger2
@EnableConfigurationProperties(InfoProperties.class)
public class Swagger {
    InfoProperties infoProperties;

    @Bean
    public Docket api(InfoProperties infoProperties) {
        this.infoProperties = infoProperties;

        return new Docket(DocumentationType.SWAGGER_2)
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.github.kdlug.example))
                .paths(regex("/api/v[0-9]+/.*"))
                .build()
                .apiInfo(apiInfo());
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder().title("Spring Boot REST API")
                .description("Employee Management REST API")
                .contact(new Contact("Name", "", "email@example.com"))
                .license("Apache 2.0")
                .licenseUrl("http://www.apache.org/licenses/LICENSE-2.0.html")
                .version("1.0.0")
                .build();
    }
}
```
