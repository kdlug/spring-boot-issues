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

Add guava 20.0

build.gradle
```
implementation('io.springfox:springfox-swagger2:2.9.2')
implementation('com.google.guava:guava:20.0')
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

In idea:

```console
***************************
APPLICATION FAILED TO START
***************************

Description:

An attempt was made to call the method com.google.common.collect.FluentIterable.append(Ljava/lang/Iterable;)Lcom/google/common/collect/FluentIterable; but it does not exist. Its class, com.google.common.collect.FluentIterable, is available from the following locations:

    jar:file:/home/kdlug/.gradle/caches/modules-2/files-2.1/com.google.guava/guava/15.0/ed727a8d9f247e2050281cb083f1c77b09dcb5cd/guava-15.0.jar!/com/google/common/collect/FluentIterable.class
    jar:file:/home/kdlug/.gradle/caches/modules-2/files-2.1/com.google.guava/guava/27.0.1-jre/bd41a290787b5301e63929676d792c507bbc00ae/guava-27.0.1-jre.jar!/com/google/common/collect/FluentIterable.class

It was loaded from the following location:

    file:/home/kdlug/.gradle/caches/modules-2/files-2.1/com.google.guava/guava/15.0/ed727a8d9f247e2050281cb083f1c77b09dcb5cd/guava-15.0.jar


Action:

Correct the classpath of your application so that it contains a single, compatible version of com.google.common.collect.FluentIterable
```
For multi project application make sure to add guava to all projects / or subprojects.

build.gradle
```
allprojects {
    implementation('com.google.guava:guava:20.0')
}
```

## Couldn't find PersistentEntity
Problem
```
java.lang.IllegalArgumentException: Couldn't find PersistentEntity for type class com.github.kdlug.model.Person!
```

If you want to enable mongo auditing, you need to remove your MongoConfig class, use config file to define your mongodb connection and everything will work.

## Remove _class from Mongo documents - Spring Data MongoDB

```java
@Configuration
@EnableMongoAuditing
public class MongoDB {
    /**
     * Removes _class field in mongo document
     * Fixes Couldn't find PersistentEntity from auditing https://jira.spring.io/browse/DATAMONGO-1999
     *
     * @param mongoDbFactory must not be {@literal null}.
     * @return MongoTemplate
     * @throws Exception
     */
    @Bean
    public MappingMongoConverter mappingMongoConverter(MongoDbFactory mongoDbFactory, MongoMappingContext mongoMappingContext) {

        DbRefResolver dbRefResolver = new DefaultDbRefResolver(mongoDbFactory);
        MappingMongoConverter converter = new MappingMongoConverter(dbRefResolver, mongoMappingContext);
        converter.setTypeMapper(new DefaultMongoTypeMapper(null));

        return converter;
    }
}
```

