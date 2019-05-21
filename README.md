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

## Error creating bean with name 'documentationPluginsBootstrapper'

```console
org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'documentationPluginsBootstrapper' defined in URL [jar:file:/home/kd/.gradle/caches/modules-2/files-2.1/io.springfox/springfox-spring-web/2.8.0/cb2ce464b07919158dde1ad40f72cca9c364559b/springfox-spring-web-2.8.0.jar!/springfox/documentation/spring/web/plugins/DocumentationPluginsBootstrapper.class]: Unsatisfied dependency expressed through constructor parameter 1; nested exception is org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'webMvcRequestHandlerProvider' defined in URL [jar:file:/home/kd/.gradle/caches/modules-2/files-2.1/io.springfox/springfox-spring-web/2.8.0/cb2ce464b07919158dde1ad40f72cca9c364559b/springfox-spring-web-2.8.0.jar!/springfox/documentation/spring/web/plugins/WebMvcRequestHandlerProvider.class]: Unsatisfied dependency expressed through constructor parameter 1; nested exception is org.springframework.beans.factory.NoSuchBeanDefinitionException: No qualifying bean of type 'java.util.List<org.springframework.web.servlet.mvc.method.RequestMappingInfoHandlerMapping>' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {}
        at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:733) ~[spring-beans-5.0.11.RELEASE.jar:5.0.11.RELEASE]
        at org.springframework.beans.factory.support.ConstructorResolver.autowireConstructor(ConstructorResolver.java:198) ~[spring-beans-5.0.11.RELEASE.jar:5.0.11.RELEASE]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.autowireConstructor(AbstractAutowireCapableBeanFactory.java:1266) ~[spring-beans-5.0.11.RELEASE.jar:5.0.11.RELEASE]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBeanInstance(AbstractAutowireCapableBeanFactory.java:1123) ~[spring-beans-5.0.11.RELEASE.jar:5.0.11.RELEASE]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:535) ~[spring-beans-5.0.11.RELEASE.jar:5.0.11.RELEASE]
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:495) ~[spring-beans-5.0.11.RELEASE.jar:5.0.11.RELEASE]
        at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:317) ~[spring-beans-5.0.11.RELEASE.jar:5.0.11.RELEASE]
        at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:222) ~[spring-beans-5.0.11.RELEASE.jar:5.0.11.RELEASE]
        at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:315) ~[spring-beans-5.0.11.RELEASE.jar:5.0.11.RELEASE]
        at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:199) ~[spring-beans-5.0.11.RELEASE.jar:5.0.11.RELEASE]
        at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:759) ~[spring-beans-5.0.11.RELEASE.jar:5.0.11.RELEASE]
        at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:867) ~[spring-context-5.0.11.RELEASE.jar:5.0.11.RELEASE]
        at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:548) ~[spring-context-5.0.11.RELEASE.jar:5.0.11.RELEASE]
        at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:754) ~[spring-boot-2.0.7.RELEASE.jar:2.0.7.RELEASE]
        at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:386) ~[spring-boot-2.0.7.RELEASE.jar:2.0.7.RELEASE]
        at org.springframework.boot.SpringApplication.run(SpringApplication.java:307) ~[spring-boot-2.0.7.RELEASE.jar:2.0.7.RELEASE]
        at org.springframework.boot.builder.SpringApplicationBuilder.run(SpringApplicationBuilder.java:137) [spring-boot-2.0.7.RELEASE.jar:2.0.7.RELEASE]
        at io.kinguin.safmoss.Application.main(Application.java:37) [main/:na]
```
Solution 
Remove the `@configuration` attribute from `SwaggerConfiguration` class, because SpringSwaggerConfig class contains @Configuration already.

If you're using the @Configuration annotation in `SwaggerConfiguration.java`, that gets picked up by the default spring context scanning which has no visibility of the RequestMappingHandlerMapping. That's why it's important that the SwaggerConfig is a bean defined in the MVC context.
