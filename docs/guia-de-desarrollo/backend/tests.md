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

## De carga

Los tests de carga se encuentran en `src/test/java/ispp/project/dondesiempre/DondeSiempreLoadTests.java`, y se saltan
por defecto. Para que funcionen correctamente, la base de datos tiene que estar seedeada. Para ejecutarlos, se pueden
usar los siguientes comandos:

```bash
# Empty test database
docker compose down -v postgres-test
docker compose up -d postgres-test

# Seed test database (the load test requires it)
./mvnw clean spring-boot:run -Dspring-boot.run.profiles=test,seed -Dspring-boot.run.arguments="--seed.uploadImages=false" ; \
mvnw.cmd clean spring-boot:run -Dspring-boot.run.profiles=test,seed -Dspring-boot.run.arguments="--seed.uploadImages=false" ; \

# Run tests
./mvnw test -Dexcluded.groups= -Dgroups=load
mvnw.cmd test -Dexcluded.groups= -Dgroups=load

# You'll want to empty the database again so that the normal tests work
docker compose down -v postgres-test
docker compose up -d postgres-test
```
