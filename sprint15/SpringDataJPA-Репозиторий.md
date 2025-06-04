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

## Проекция
Для получения ограниченной информации из репозитория через запросные методы, нужно задействовать
механизм проекции

```
  public interface UserShort {
      String getFirstName();
      String getEmail();
  } 
```
Интерфейс-проекция при этом указывается в качестве типа результата в запросном методе.
```
  List<UserShort> findAllByEmailContainingIgnoreCase(String emailSearch); 
```

## Язык JPQL 
Язык является частью JPA, он похож на SQL, но привязан не к таблицам и колонкам, а к сущностями JPA 
и полям этих сущностей 
Пример :
```
  select it.userId, count(it.id) as count from Item as it where it.url like ?1 group by it.userId       order by count(it.id) desc
```

Методы репозитория, для которых должен выполняться особый зпарос помечаются аннотацией @Query

```
  interface ItemRepository extends JpaRepository<Item, Long> {    
    @Query("select new ru.practicum.item.ItemCountByUser(it.userId, count(it.id))" +
            "from Item as it "+
            "where it.url like ?1 "+
            "group by it.userId "+
            "order by count(it.id) desc")
    List<ItemCountByUser> countItemsByUser(String urlPart);
    } 
```

### Нативный запрос 
запрос, используеемый на языке используемой БД
Для использования нативных запросов в аннотации Query нужно указать параметр
@Query(nativeQuery = true)

Пример: 
```
  @Query(value = "select it.user_id, count(it.id) as count "+
        "from items as it left join users as us on it.user_id = us.id "+
        "where (cast(us.registration_date as date)) between ?1 and ?2 "+
                "group by it.user_id", nativeQuery = true)
  List<ItemCountByUser> countByUserRegistered(LocalDate dateFrom, LocalDate dateTo);
```

## QueryDSL
Если задача предполагает необходимость работы с сущностями и сложные условия поиска, но не требует особых агрегаций, можно использовать язык QueryDSL (Query Domain Specific Language). 

```
  <dependency>
      <groupId>com.querydsl</groupId>
      <artifactId>querydsl-jpa</artifactId>
      <classifier>jakarta</classifier>
      <version>5.1.0</version>
  </dependency>
```
Плагин querySQL в секуцию build
```
   <build>
      <plugins>
          <plugin>
              <groupId>com.mysema.maven</groupId>
              <artifactId>apt-maven-plugin</artifactId>
              <version>1.1.3</version>
              <executions>
                  <execution>
                      <goals>
                          <goal>process</goal>
                      </goals>
                      <configuration>
                          <outputDirectory>target/generated-sources/java</outputDirectory>
                          <processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
                      </configuration>
                  </execution>
              </executions>
              <dependencies>
                  <dependency>
                      <groupId>com.querydsl</groupId>
                      <artifactId>querydsl-apt</artifactId>
                      <classifier>jakarta</classifier>
                      <version>5.1.0</version>
                  </dependency>
              </dependencies>
          </plugin>
      </plugins>
  </build>
```

Если сущность помечена аннотациями JPA, то QueryDSL сформирует дополнительные вспомогательные 
классы (назовём их Q-классами). Классы с префиксом Q можно увидеть, если после компиляции проекта 
заглянуть в target/generated-sources/java

<http://querydsl.com/static/querydsl/latest/reference/html/index.html>

Чтобы добавить в ItemRepository методы для работы с QueryDSL, расширим его через интерфейс QueryDslPredicateExecutor.

```
  interface ItemRepository extends JpaRepository<Item, Long>, QuerydslPredicateExecutor<Item> {

  } 
```

Q-классы на освное сущностей учитывают типы данных и допустимые операции с ними. Это позволяет формировать выражения в стиле fluent для запроса данных на основе нужных критериев. Критерии и выражения для их описания называются предикатами или логическим иединицами. 

Пример сервиса: 
```
  class ItemServiceImpl implements ItemService {        
  
      //здесь остальные методы сервиса
  
      @Override
      @Transactional(readOnly = true)
      public List<ItemDto> getItems(long userId, Set<String> tags) {
          BooleanExpression byUserId = QItem.item.userId.eq(userId);
          BooleanExpression byAnyTag = QItem.item.tags.any().in(tags);
          Iterable<Item> foundItems = repository.findAll(byUserId.and(byAnyTag));
          return ItemMapper.mapToItemDto(foundItems);
      }
  }
```
QItem.item.userId.eq(userId) и QItem.item.tags.any().in(tags) — это и есть предикаты. 
