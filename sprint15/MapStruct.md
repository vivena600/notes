Библиотека для автогенерации код маппера 
<https://web.archive.org/web/20240619130052/https://rukovodstvo.net/posts/id_799/>

## Подключение 
```
 <dependencies> 
   <dependency> 
   <groupId>org.mapstruct</groupId> 
   <artifactId>mapstruct</artifactId> 
   <version>${org.mapstruct.version}</version> 
   </dependency> 
 </dependencies> 
```
Плагин, добавляем в <build>
```
 <build> 
   <plugins> 
     <plugin> 
       <groupId>org.apache.maven.plugins</groupId> 
       <artifactId>maven-compiler-plugin</artifactId> 
       <version>3.5.1</version> 
       <configuration> 
         <source>1.8</source> 
         <target>1.8</target> 
         <annotationProcessorPaths> 
           <path> 
             <groupId>org.mapstruct</groupId> 
             <artifactId>mapstruct-processor</artifactId> 
             <version>${org.mapstruct.version}</version> 
           </path> 
         </annotationProcessorPaths> 
       </configuration> 
     </plugin> 
   </plugins> 
 </build> 
```

## Применение
Рассмотрим пример: создадим сущность Doctor и класс DoctorDto
```
   public class Doctor { 
     private int id; 
     private String name; 
   } 
```

```
   public class DoctorDto { 
     private int id; 
     private String name; 
   } 
```

Чтобы создатьрабочий маппер добавляем интерфейс с аннотацией @Mapper
```
 @Mapper 
 public interface DoctorMapper { 
   DoctorMapper INSTANCE = Mappers.getMapper(DoctorMapper.class); 
   DoctorDto toDto(Doctor doctor); 
 } 
```

Если в сущности и dto есть различимые поля, то их необходимо указать в качестве параметров аннотации.
К примеру добавим в сущность поле speciality, а в dto поле specialization 

```
 @Mapper 
 public interface DoctorMapper { 
   DoctorMapper INSTANCE = Mappers.getMapper(DoctorMapper.class); 
   
   @Mapping(source = "doctor.specialty", target = "specialization") 
   DoctorDto toDto(Doctor doctor); 
 } 
```

 ## Сопоставление дочерних сущностей 
 Добавим сущность и dto для неё Patient. В сущность Doctor добавим поле `private List<Patient> patientList;`
 Для обновления маппера доктора следует: 
 1. Создать инттерфейс - маппер для сущности Patient
 2. В аннотации интерфейса указать флаг uses с классом маппера дочерней сущности

```
  @Mapper(uses = {PatientMapper.class}) 
   public interface DoctorMapper { 
     DoctorMapper INSTANCE = Mappers.getMapper(DoctorMapper.class); 
     
     @Mapping(source = "doctor.patientList", target = "patientDtoList") 
     @Mapping(source = "doctor.specialty", target = "specialization") 
     DoctorDto toDto(Doctor doctor); 
   } 
```

  ## Использование enum
  не поняла ( 

  ## Добавление собственных методов 
  Для добавления собственных методов используются default в интерфейсе

  ```
   @Mapper 
   public interface DoctorMapper { 
   
     default DoctorPatientSummary toDoctorPatientSummary(Doctor doctor, Education education) { 
       return DoctorPatientSummary.builder() 
       .doctorId(doctor.getId()) 
       .doctorName(doctor.getName()) 
       .patientCount(doctor.getPatientList().size()) 
                      .patientIds(doctor.getPatientList() 
         .stream() 
         .map(Patient::getId) 
         .collect(Collectors.toList())) 
       .institute(education.getInstitute()) 
       .specialization(education.getDegreeName()) 
       .build(); 
     } 
   } 
  ```

  ## MapStruct в Spring 
  в классе маппера необходимо указать ` @Mapper(componentModel = "spring")`
  Добавление (componentModel = "spring") в @Mapper сообщает MapStruct, что при создании класса реализации 
  сопоставителя мы хотели бы, чтобы он создавался с поддержкой внедрения зависимостей через Spring. Теперь
  нет необходимости добавлять INSTANCE в интерфейс.
