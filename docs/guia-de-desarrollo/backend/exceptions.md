---
sidebar_position: 3
---

# Exceptions

En este apartado se detallan varios aspectos a tener en cuenta sobre el uso de excepciones en el proyecto.

## Excepciones y transaccionalidad

En aquellos métodos de servicios del proyecto que sean transaccionales (es decir, aquellos que lleven asociada la anotación `@Transactional`) y que puedan lanzar excepciones, deberán añadirse todas las excepciones lanzadas:

- A la signatura del método, empleando la palabra clave `throws`.
- A la anotación `@Transactional`, mediante la propiedad `rollbackFor`.

Así, si tenemos un método que puede lanzar una `ResourceNotFoundException`, debería:

```java
@Transactional(readonly = true)
public void foo(Integer a) {
  if (a > 0) {
    throw new ResourceNotFoundException("Resource not found");
  }
}
```

Deberá añadirse dicha excepción de la siguiente manera:

```java
@Transactional(readonly = true, rollbackFor = ResourceNotFoundException.class)
public void foo(Integer a) throws ResourceNotFoundException {
  if (a > 0) {
    throw new ResourceNotFoundException("Resource not found");
  }
}
```

Si además añadimos la posibilidad de que el método lance una `InvalidRequestException`...

```java
@Transactional(readonly = true, rollbackFor = ResourceNotFoundException.class)
public void foo(Integer a) throws ResourceNotFoundException {
  if (a > 0) {
    throw new ResourceNotFoundException("Resource not found");
  } else {
    throw new InvalidRequestException("Invalid request");
  }
}
```

...dicha excepción deberá añadirse también:

```java
@Transactional(readonly = true, rollbackFor = {ResourceNotFoundException.class, InvalidRequestException.class})
public void foo(Integer a) throws ResourceNotFoundException, InvalidRequestException {
  if (a > 0) {
    throw new ResourceNotFoundException("Resource not found");
  } else {
    throw new InvalidRequestException("Invalid request");
  }
}
```
