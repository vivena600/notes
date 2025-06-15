# Стартер для тестов
К проекту необходимо подключить spring-boot-starter-test в виде зависимости
```
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
  </dependency>
```

## Аннотации @WebMvcTest и @MockBean
 @WebMvcTest позволяет инициализировать для теста Spring - контекст, состоящий только из тех бинов, которые требуются для работы тестируемого контроллера
 @MockBean - создает для данного бина будет создан мок-объект, который заменит реальный бин в контексте

 Пример 
```
  @WebMvcTest(controllers = UserController.class)
  class UserControllerTestWithContext {
  
      @Autowired
      ObjectMapper mapper;
  
      @MockBean
      UserService userService;
  
      @Autowired
      private MockMvc mvc;
  
      private UserDto userDto = new UserDto(
                  1L,
                  "john.doe@mail.com",
                  "John",
                  "Doe",
                  "2022.07.03 19:55:00",
                  UserState.ACTIVE);
  
      @Test
      void saveNewUser() throws Exception {
          when(userService.saveUser(any()))
                  .thenReturn(userDto);
  
          mvc.perform(post("/users")
                          .content(mapper.writeValueAsString(userDto))
                          .characterEncoding(StandardCharsets.UTF_8)
                          .contentType(MediaType.APPLICATION_JSON)
                          .accept(MediaType.APPLICATION_JSON))
                  .andExpect(status().isOk())
                  .andExpect(jsonPath("$.id", is(userDto.getId()), Long.class))
                  .andExpect(jsonPath("$.firstName", is(userDto.getFirstName())))
                  .andExpect(jsonPath("$.lastName", is(userDto.getLastName())))
                  .andExpect(jsonPath("$.email", is(userDto.getEmail())));
      }
  }
```
