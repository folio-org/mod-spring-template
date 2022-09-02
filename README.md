# mod-spring-template

Copyright (C) 2022 The Open Library Foundation

This software is distributed under the terms of the Apache License,
Version 2.0. See the file "[LICENSE](LICENSE)" for more information.

The blueprint module that could be used as a template for FOLIO Spring-based backend modules.

## Table of Contents

- [Table of Contents](#table-of-contents)
- [Introduction](#introduction)
- [How-to guide](#how-to-guide)
- [Notes](#notes)

## Introduction

This is the blueprint module that should be used as a template for FOLIO Spring-based backend modules.
It provides the initial configuration and common data schemas used by the FOLIO platform.


## How-to guide
This guide shows how to create a new folio backend module using the mod-spring-template engine

1. Clone the mod-spring-template repository from the github https://github.com/folio-org/mod-spring-template
2. Rename the folder containing the project following FOLIO naming conventions and delete the git related folder .git
3. Edit the pom.xml and provide valid values for tags
   - artifactId
   - name
   - version
   - description
4. Edit the Dockerfile and provide the correct value for APP_FILE environment variable.
5. Add OpenAPI specification file with the API provided by this new module into the src/main/resources/swagger.api folder
6. Edit the pom.xml and uncomment the usage of org.openapitools plugin.
7. Edit *DeploymentDescriptor-template.json* & *ModuleDescriptor-template.json* providing correct APIs that the module provides and a set of permissions it requires.
8. Add a main class that represents the SpringBoot application. @SpringBootApplication annotation is a must to mark the main class as a SpringBoot application. Add @EnableFeignClients to activate feign client processing to call other modules and services.
9. Add Entity classes in the entity package which will map to the tables in your database.
   ```java
    @Entity
    @Data
    public class EntityName {
       @Id
       @GeneratedValue(strategy = GenerationType.AUTO)
       @Column(updatable = false)
       private UUID id;

       // Entity fields

       // Getter/Setter methods
    }
   ```
10. To access database from Spring, create repository interfaces in repository package.
    ```java
    @Repository
    public interface RepositoryName extends JpaRepositry<T, ID> {
       // Methods to read/write data to database
    }
    ```
    where:
    T: Domain type that repository manages (Generally the Entity/Model class name)
    ID: Type of the id of the entity that repository manages (Generally the wrapper class of your @Id that is created inside the Entity/Model class)

    For more information, follow:
    * [Spring Data JPA - Reference Documentation](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)
    * [Accessing Data with JPA](https://spring.io/guides/gs/accessing-data-jpa/)
11. DB objects(tables/columns) should be created by Liquibase and should not be created automatically by the Spring Data/Hibernate thats why in your configuration file (application.properties/application.yml), set this property to "none":
    ```yaml
    spring:
      jpa:
        hibernate:
          ddl-auto: none
    ```
12. Configure Liquibase and create Liquibase changelog  
    1. Create a liquibase.properties text file to specify your driver class path, URL, and user authentication information for the database you want to capture.
    
    2. Here’s an example of a properties file for a PostgreSQL database.
    ```
    changeLogFile:dbchangelog.xml  
    url:  jdbc:postgresql://localhost:5432/mydatabase
    username:  postgres  
    password:  password****
    classpath:  postgresql-42.2.8.jar
    ```
    3. Create master changelog file under resources/db/changelog folder.
    
       ![changelog](src/main/resources/changelog-master.png)
    
    4. SQL files should be kept in resources/db/changelog/change<version>/sql with version name.

       ![sqlfolder](src/main/resources/sql-folder.png)    
    
14. Provide correct values to the application.properties file.
15. run mvn clean package to check that the build process completes successfully.
16. The skeleton for your new module is ready for further business functionality development.
17. Generated API controllers and DTOs will ****be stored in the **target/generated-sources/src/main/java** folder. The content of that folder will be automatically included in the list of source folders.
18. Note that the default implementation for TenantAPI is already provided by the folio-spring-base library. If you need to customize it or provide your own implementation please reach https://github.com/folio-org/folio-spring-base#custom-_tenant-logic for details.

## Notes

A detailed description of the functionality provided by folio-spring-base java library you can find on https://github.com/folio-org/folio-spring-base page.

Spring-based folio modules must follow best practices for spring boot applications. The folio-spring-base has been intentionally designed to provide the minimum of FOLIO specific functionality. The Spring projects should be used to cover the common functionality like DB connection, integration with Kafka, Elasticsearch, etc.

It is highly recommended to use only community approved Spring projects in the FOLIO backend modules.
If you need to use the Spring project that is not still used in FOLIO please discuss that with the community first.

The database that is used in FOLIO for general persistence tasks is PostgreSQL. If you need to use different storage for your functionality, please discuss it with the community first.

Here are the links to some production backend modules based on the folio-spring-base. You can check various details of implementations there.

- https://github.com/folio-org/mod-tags
- https://github.com/folio-org/mod-password-validator
- https://github.com/folio-org/mod-quick-marc
- https://github.com/folio-org/mod-inn-reach
- https://github.com/folio-org/mod-search


### Spring projects used in FOLIO

- Spring Framework https://spring.io/projects/spring-framework that includes:
  - Core technologies: dependency injection, events, resources, i18n, validation, data binding, type conversion, SpEL, AOP.
  - Testing: mock objects, TestContext framework, Spring MVC Test, WebTestClient.
  - Data Access: transactions, DAO support, JDBC, ORM, Marshalling XML.
  - Spring MVC and Spring WebFlux web frameworks.
  - Integration: remoting, JMS, JCA, JMX, email, tasks, scheduling, cache.
- Spring Boot https://spring.io/projects/spring-boot
- Spring Data JPA https://spring.io/projects/spring-data-jpa
- Spring for Apache Kafka https://spring.io/projects/spring-kafka
- Spring Batch https://spring.io/projects/spring-batch

