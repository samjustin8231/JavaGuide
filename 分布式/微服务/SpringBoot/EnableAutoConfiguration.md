# @EnableAutoConfiguration

@EnableAutoConfiguration注解用于启用自动配置，该注解会使Spring Boot根据项目中依赖的jar包自动配置项目的配置项。

例如：上篇中，我们在编写第一个WEB应用时，就在pom.xml中引入了spring-boot-starter-web的依赖，所以项目中就会引入SpringMVC的依赖，就会自动配置tomcat和SpringMVC。

还有后面使用事务时，会引入spring-boot-starter-jdbc的依赖，让Spring Boot自动配置DataSourceTransactionManager或JpaTransactionManager，等等。。

