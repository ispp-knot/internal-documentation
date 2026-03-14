---
sidebar_position: 2
---

# 📦 DTOs

En esta guía se detallan algunos aspectos a tener en cuenta sobre los DTO.

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

# Validaciones

Al trabajar en el backend hay problemas de validación que podrían detectarse desde su llegada al controlador sin necesidad de hacer una comprobación manual en un if. Por ello, es muy importante añadir validaciones de jakarta en los DTO correspondientes.

Un ejemplo podría ser ProductCreationDTO:

```java
public class ProductCreationDTO {

  @NotNull
  @Size(max = 255)
  private String name;

  @NotNull
  @Min(0)
  private Integer priceInCents;

  private String description;

  @NotNull private UUID typeId;
}
```

Gracias a haber colocado estas anotaciones podemos añadir @Valid en el controlador para que, cuando llegue la petición, se validen los atributos según las anotaciones. Por ejemplo, la creación en ProductController es:

```Java
public ResponseEntity<Product> createProduct(
      @RequestPart("dto") @Valid ProductCreationDTO dto,
      ...) {
    ...
}
```

Es **MUY IMPORTANTE** saber qué validaciones poner dependiendo del propósito del DTO. Por ejemplo, ProductCreation valida NotNull porque al crear un producto esos atributos no pueden ser null, pero, en el caso de actualizarlo no es necesario que todos los campos vengan modificados, sino que podrían ser null. Para ello, hay que usar validaciones que hagan comprobaciones sobre los valores de los campos en caso de que estén presentes los valores. Véase por ejemplo OutfitUpdateDTO:

```java
public class OutfitUpdateDTO {

  @Min(0)
  private Integer index;

  @Size(max = 255)
  private String name;

  @Size(max = 5000)
  private String description;

  @Min(0)
  private Integer discountedPriceInCents;
}
```

Durante la creación del outfit, muchos de estos parámetros serían obligatorios, pero en el caso de la actualización no. Por eso, se usan validaciones como las presentes. Estas validaciones aceptan que el valor sea null, pero en caso de que no, valida la restricción.
