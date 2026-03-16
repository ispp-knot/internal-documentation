---
sidebar_position: 5
---

# 🧪 Consideraciones sobre los tests

Esta es una breve guía con algunos aspectos sobre los tests.

## Unitarios

Al hacer los tests unitarios se ha de probar exclusivamente la **funcionalidad concreta**, si esta depende de algún **modulo externo**, ya sea servicio, repositorio o lo que sea(que no se haya implementado para esa funcionalidad concreta) ese módulo debe de ser mockeado con `@MockitoBean`.

### Controlador

Al hacer las pruebas de controlador lo correcto es hacerlos con la anotación `@WebMvcTest`, que está pensada para probar los controladores.

A continuación, un ejemplo:

```java

@WebMvcTest(controllers = AuthController.class)
class AuthControllerTest {

  @Autowired private MockMvc mockMvc;
  @Autowired private ObjectMapper objectMapper;

  @MockitoBean private AuthService authService;

  @Test
  void logIn_shouldReturn403_whenCredentialsAreInvalid() throws Exception {
    when(authService.logIn(any(), any()))
        .thenThrow(new UnauthorizedException("Invalid credentials."));

    LoginRequestDTO dto = new LoginRequestDTO("user@test.com", "wrong");

    mockMvc
        .perform(
            post("/api/v1/auth/login")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(dto)))
        .andExpect(status().isForbidden());
  }
}
```
