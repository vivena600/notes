# Sprint 15 - Spring Data JPA
ORM (англ. Object-Relational Mapping, «объектно-реляционное отображение») — это способ представить данные, полученные от реляционных СУБД (таблицы, строки, столбцы и их взаимосвязи), в виде классов, объектов и списков. То есть в виде, удобном для работы в Java-коде.
К ORM-фреймворкам относят Hibernate, EclipseLink и другие. В результате их развития в Java возник стандарт JPA (изначально аббревиатура расшифровывалась как Java Persistence API, но текущее название этой технологии — Jakarta Persistence).
![image](https://github.com/user-attachments/assets/7dbebb0a-db6b-40c8-b214-6475edc18acc)
По аннотациям фреймворк устанавливает соответствия — между названием таблицы и классом, а также между колонками и полями класса Post. В некоторых случаях можно обойтись без указания аннотаций @Table и @Column — фреймворк сам вычислит названия колонок по названиям полей класса. Мы указываем эти аннотации для наглядности.
# Hibernate в Spring
**Зависимости**
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


# Hibernate в Spring Boot
