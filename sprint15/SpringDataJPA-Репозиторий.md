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

## Запросные методы
    Это методы, которые выполняют операции с бд. Наименование методов в JPA указывает какой 
    запрос нужно выполнить. 
    Запросные методы объявляют в репозитории (без реализации)
    <https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html>

    | Ключевое слово  | Пример метода                          | Генерируемый SQL фрагмент                                                                 |
|-----------------|----------------------------------------|------------------------------------------------------------------------------------------|
| Distinct        | findDistinctByLastnameAndFirstname     | `select distinct … where x.lastname = ?1 and x.firstname = ?2`                          |
| And             | findByLastnameAndFirstname             | `… where x.lastname = ?1 and x.firstname = ?2`                                           |
| Or              | findByLastnameOrFirstname              | `… where x.lastname = ?1 or x.firstname = ?2`                                            |
| Is, Equals      | findByFirstname, findByFirstnameIs     | `… where x.firstname = ?1` (или `… where x.firstname IS NULL` если аргумент равен null)  |
| Between         | findByStartDateBetween                 | `… where x.startDate between ?1 and ?2`                                                  |
| LessThan        | findByAgeLessThan                      | `… where x.age < ?1`                                                                     |
| LessThanEqual   | findByAgeLessThanEqual                 | `… where x.age <= ?1`                                                                    |
| GreaterThan     | findByAgeGreaterThan                   | `… where x.age > ?1`                                                                     |
| GreaterThanEqual| findByAgeGreaterThanEqual              | `… where x.age >= ?1`                                                                    |
| After           | findByStartDateAfter                   | `… where x.startDate > ?1`                                                               |
| Before          | findByStartDateBefore                  | `… where x.startDate < ?1`                                                               |
| IsNull, Null    | findByAge(Is)Null                      | `… where x.age is null`                                                                  |
| IsNotNull, NotNull| findByAge(Is)NotNull                 | `… where x.age is not null`                                                              |
| Like            | findByFirstnameLike                    | `… where x.firstname like ?1`                                                            |
| NotLike         | findByFirstnameNotLike                 | `… where x.firstname not like ?1`                                                        |
| StartingWith    | findByFirstnameStartingWith            | `… where x.firstname like ?1` (параметр с добавленным `%`)                               |
| EndingWith      | findByFirstnameEndingWith              | `… where x.firstname like ?1` (параметр с добавленным `%`)                               |
| Containing      | findByFirstnameContaining              | `… where x.firstname like ?1` (параметр заключен в `%`)                                  |
| OrderBy         | findByAgeOrderByLastnameDesc           | `… where x.age = ?1 order by x.lastname desc`                                            |
| Not             | findByLastnameNot                      | `… where x.lastname <> ?1` (или `… where x.lastname IS NOT NULL` если аргумент равен null)|
| In              | findByAgeIn(Collection<Age> ages)      | `… where x.age in ?1`                                                                    |
| NotIn           | findByAgeNotIn(Collection<Age> ages)   | `… where x.age not in ?1`                                                                |
| True            | findByActiveTrue()                     | `… where x.active = true`                                                                |
| False           | findByActiveFalse()                    | `… where x.active = false`                                                               |
| IgnoreCase      | findByFirstnameIgnoreCase              | `… where UPPER(x.firstname) = UPPER(?1)`                                                |


    

