# JpaRepository
<https://www.baeldung.com/spring-data-repositories>
Для Spring cначала необходимо добавить к конфигурационному классу приложения аннотацию *@EnableJpaRepositories*
```
  @EnableJpaRepositories(basePackages = "ru.practicum")
  // остальные аннотации
  public class PersistenceConfig {
      // остальное содержимое конфигурационного класса
  }
```
Служебный интерфейс JpaRepository - входит в состав Spring Data JPA, расширяет базовый Repository
Чтобы создать репозиторий для работы с сущностью, нужно создать специальный интерфейс, который будет 
расширять специальны интерфейс *JPARepository*
```
    public interface UserRepository extends JpaRepository<User, Long> {
    } 
```
Интерфейс JpaRepository использует два параметра: 
- первый указывает с какой сущностью будем работать
- тип данных идентификатора. который использует данная сущность
## Методы и возможности
  - save — метод, объединяющий в себе операции Create и Update. Позволяет сохранить как одну, так и сразу несколько сущностей.
  - count() — позволяет узнать общее количество строк в таблице.
  - getById(ID) и findById(ID) — позволяют найти в базе данных сущность по её идентификатору. Реализуют операцию Read.
  - delete(ID) — реализует операцию удаления сущности Delete.
  - findAll() — позволяет запросить все сущности в таблице.

