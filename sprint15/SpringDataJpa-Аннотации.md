# @Entity
Используется для отметки классов-сущностей. У каждой сузности должен быть идентификатор, отражающий её уникальность
Для этого можно исспользовать оннотацию **@id**
Аннотацией **@Transient** помечают поля и методы, которые не должны проецироваться на таблицу в бд.
ORM будет игнорировать поля с такой аннтацией
# @Table и @Column
Эти аннотации используются для настройки сопоставления между классов сущности и таблицей в бд
Параметры в ссузности и поля в бд скорее всего будут называться по разному, поэтому таким образом мы показваем ORM 
какие данные подставлять при маппинге
```
  @Entity
  @Table(name = "users", schema = "public")
  public class User {
  
      @Id
      private Long id;
  
      @Column(name = "first_name", nullable = false)
      private String firstName;
  
      @Column(name = "last_name")
      private String lastName;
  
      private String email;
  
  } 
```
Также в аннотации @Column можно задавать ограничения
# @Enumerated
Указывает, что поле таблтцы будет иметь тип Enum 
Доступно два вида сохранения:
1. EnumType.STRING - полученные данные сохранятся  виде строки (state.name())
2. EnumType.ORDINAL - сохранит порядковый номер элемента перечисления (state.ordinal())
```
  @Enumerated(EnumType.STRING)
  private UserState state;
```
