---
sidebar_position: 2
---

# 📦 DTOs

En esta guía se detallan algunos aspectos a tener en cuenta sobre los DTO tras aplicar la refactorización.

## Aspectos generales

Los DTOs creados deben acabar con DTO el nombre. Ejemplo: `ProductDTO.java`.

En general, en caso de que no se defina lo contrario, las clases usadas de parámetros de entrada en los controladores requieren de tener definido un **constructor vacío** porque por defecto se inicia la clase vacía y luego se hace **set** de todas sus propiedades. Usar `@NoArgsConstructor` de lombok para generar un constructor vacío.

## Contruccion a partir de Entidades

Para construir DTOs a partir de entidades y/u otros parámetros, hacer uso de constructores.
Ejemplo:

```java
public class ProductDTO {

  private UUID id;
  private String name;
  ...

  public ProductDTO(Product product) {
    this.id = product.getId();
    this.name = product.getName();
    ...
  }
}
```
