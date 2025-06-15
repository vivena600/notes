# Тестирование веб-приложений
Для проверки маппинга на конкретные методы контроллеры, сериализации и десериализации параметров запросов, валидации параметров и обработки исключений используется фреймворк MockMvc
MockMvc иммитирует выполнение HTTP запроса по определенному URL и проверяет корректность ответа.

<https://docs.spring.io/spring-framework/reference/testing/mockmvc.html>

Пример кода:
```
  package ru.practicum.user;
  
  import com.fasterxml.jackson.databind.ObjectMapper;
  import org.junit.jupiter.api.BeforeEach;
  import org.junit.jupiter.api.Test;
  import org.junit.jupiter.api.extension.ExtendWith;
  import org.mockito.InjectMocks;
  import org.mockito.Mock;
  import org.mockito.junit.jupiter.MockitoExtension;
  import org.springframework.http.MediaType;
  import org.springframework.test.web.servlet.MockMvc;
  import org.springframework.test.web.servlet.result.MockMvcResultHandlers;
  import org.springframework.test.web.servlet.setup.MockMvcBuilders;
  
  import java.nio.charset.StandardCharsets;
  
  import static org.hamcrest.Matchers.is;
  import static org.mockito.ArgumentMatchers.any;
  import static org.mockito.Mockito.when;
  import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
  import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
  import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
  
  @ExtendWith(MockitoExtension.class)
  class UserControllerTest {
      @Mock 
      private UserService userService;
  
      @InjectMocks 
      private UserController controller;
  
      private final ObjectMapper mapper = new ObjectMapper();
  
      private MockMvc mvc;
  
      private UserDto userDto;
  
      @BeforeEach
      void setUp() {
          mvc = MockMvcBuilders
              .standaloneSetup(controller)
              .build();
  
          userDto = new UserDto(
              1L,
              "john.doe@mail.com",
              "John",
              "Doe",
              "2022.07.03 19:55:00",
              UserState.ACTIVE);
      }
  
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

Аннотация @InjectMocks означает, что Mockito сам создаст экземпляр класса контролера и при этом внедрит моки во все поля, где это возможно.
Чтобы @InjectMocks работала, класс должен быть аннотирован @ExtendWith(MockitoExtension.class).

### Создание экземпеляра MockMvc 
Для этого используется один из двух статических методов класса MockMvcBuilders: standaloneSetup либо webAppContextSetup

- standaloneSetup - объекст MockMvc создается сам по себе, не на основе Spring  

Метод  mvc.perform имитирует выполнение запроса к эндпоинту, при обработке которого в дальнейшем вызывается соответствующий метод контроллера. Обязательно указывается путь эндпоинта — /users и метод — post. Строка mvc.perform(post("/users"))  означает, что к тестируемому контроллеру UserController будет выполнен post-запрос по адресу /users.
