# Sprint 15 - Spring Data JPA
ORM (англ. Object-Relational Mapping, «объектно-реляционное отображение») — это способ представить данные, полученные от реляционных СУБД (таблицы, строки, столбцы и их взаимосвязи), в виде классов, объектов и списков. То есть в виде, удобном для работы в Java-коде.
К ORM-фреймворкам относят Hibernate, EclipseLink и другие. В результате их развития в Java возник стандарт JPA (изначально аббревиатура расшифровывалась как Java Persistence API, но текущее название этой технологии — Jakarta Persistence).
![image](https://github.com/user-attachments/assets/7dbebb0a-db6b-40c8-b214-6475edc18acc)
По аннотациям фреймворк устанавливает соответствия — между названием таблицы и классом, а также между колонками и полями класса Post. В некоторых случаях можно обойтись без указания аннотаций @Table и @Column — фреймворк сам вычислит названия колонок по названиям полей класса. Мы указываем эти аннотации для наглядности.
# Hibernate в Spring
**Зависимости**
<https://docs.spring.io/spring-data/jpa/reference/repositories/create-instances.html>
- spring-data-jpa - обеспечивает интеграцию Spring и JPA
- hibernate-core — отвечает за реализацию интерфейсов JPA
- postgresql — содержит драйвер БД PostgreSQL.
```
  <dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-jpa</artifactId>
    <version>3.3.0</version>
</dependency>

<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-core</artifactId>
    <version>6.5.1.Final</version>
</dependency>

<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <version>42.7.3</version>
</dependency> 
```
настройка компонентов 
```
  package ru.practicum.config;
  
  import lombok.RequiredArgsConstructor;
  import org.springframework.context.annotation.Configuration;
  import org.springframework.core.env.Environment;
  
  @Configuration
  @RequiredArgsConstructor
  public class PersistenceConfig {
      private final Environment environment; // внедряем экземпляр Environment
  
      // здесь будут определения бинов
  }
```

Все наобходимые бины опреелим в классе PersistenceConfig

```
  @Bean
  public DataSource dataSource() {
    DriverManagerDataSource dataSource = new DriverManagerDataSource();
    dataSource.setDriverClassName(environment.getRequiredProperty("jdbc.driverClassName"));
    dataSource.setUrl(environment.getRequiredProperty("jdbc.url"));
    dataSource.setUsername(environment.getRequiredProperty("jdbc.username"));
    dataSource.setPassword(environment.getRequiredProperty("jdbc.password"));
    return dataSource;
} 
```
Следующий компонент для настройки — сам Hibernate. Опишем простой метод, возвращающий объект типа Properties. Он будет содержать важные свойства для настройки Hibernate

```
  private Properties hibernateProperties() {
    Properties properties = new Properties();
    properties.put("hibernate.jdbc.time_zone",
            environment.getRequiredProperty("hibernate.jdbc.time_zone"));
    properties.put("hibernate.show_sql",
            environment.getProperty("hibernate.show_sql", "false"));
    return properties;
} 
```

hibernate.jdbc.time_zone - указывает Hibernate, что ноебходимо использовать заданный часовой пояс ри чтении и записи столбоц в стипом timestamp
hibernate.show_sql - параметр show_sql принимает значение true или false, он отвечает за включение или отключение режима отладочного вывода SQL запроса

**EntityManager**
- отвечает за управление всеми сущностями, которые юудут сохраняться в бд и выгружаться из неё
```
  @Bean
  public LocalContainerEntityManagerFactoryBean entityManagerFactory(DataSource dataSource) {
    final HibernateJpaVendorAdapter vendorAdapter = 
            new HibernateJpaVendorAdapter();

    final LocalContainerEntityManagerFactoryBean emf =
            new LocalContainerEntityManagerFactoryBean();

    emf.setDataSource(dataSource);
    emf.setJpaVendorAdapter(vendorAdapter);
    emf.setPackagesToScan("ru.practicum");
    emf.setJpaProperties(hibernateProperties());

    return emf;
} 
```
LocalContainerEntityManagerFactoryBean - представляет собой фабрику EntityManager 
Метод entityManagerFactory(DataSource dataSource) - позваляет для определения бина использовать другой 
HibernateJpaVendorAdapter - связывает интерфейсы JPA и их реализацию внутри фреймворка
setPackagesToScan - указываем пакет внутри которого будут искаться сущности (@Entity) 
**JpaTransactionManager**
```
  @Bean
public JpaTransactionManager transactionManager(EntityManagerFactory entityManagerFactory) {
    JpaTransactionManager transactionManager = new JpaTransactionManager();
    transactionManager.setEntityManagerFactory(entityManagerFactory);
    return transactionManager;
} 
```
# Hibernate в Spring Boot
1. Добавляем зависимости
   ```
    <dependency>
     <groupId>org.postgresql</groupId>
     <artifactId>postgresql</artifactId>
   </dependency>
   
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-jpa</artifactId>
   </dependency>
   ```
2. Указываем настройки 
  ```
   spring:
     application.name: later
     main.banner-mode: OFF
     jpa:
       show-sql: true
       properties:
           hibernate.jdbc.time_zone: UTC
     datasource:
       username: "dbuser"
       password: "12345"
       url: "jdbc:postgresql://localhost:5432/later"
       driver-class-name: "org.postgresql.Driver"
  ```
   
