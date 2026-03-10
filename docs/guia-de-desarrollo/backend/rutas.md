# Guía rápida de diseño RESTful

Esta es una guía breve para entender cómo diseñar APIs REST correctamente y por qué rutas como `/stores/all` no son una buena práctica.



## 1. Principio básico de REST

En REST, **las rutas representan recursos (sustantivos)** y **los métodos HTTP representan acciones**.

**Recurso:** `stores`  
**Acción:** `GET`, `POST`, `PUT`, `PATCH`, `DELETE`



## 2. Por qué `/stores/all` no es buena práctica

Una ruta como:

```
GET /stores/all
```

es redundante y rompe el estilo REST.

Problemas:

- **Duplica información**: `GET` ya significa obtener datos.
- **La colección ya está representada por `/stores`**.
- **Introduce verbos o acciones innecesarias en la URL**.

La forma REST correcta es:

```
GET /stores
```

Esto significa: **obtener todas las tiendas**.



## 3. Estructura correcta de rutas

### Obtener todos los recursos

Incorrecto

```
GET /stores/all
GET /getStores
GET /stores/list
```

Correcto

```
GET /stores
```



### Obtener un recurso específico

Incorrecto

```
GET /stores/get/5
GET /getStore/5
```

Correcto

```
GET /stores/5
```



### Crear un recurso

Incorrecto

```
POST /stores/create
POST /createStore
```
    
Correcto

```
POST /stores
```


## 4. Filtros y búsquedas

Cuando necesitas filtrar datos, se usan **query parameters**, no rutas nuevas.

Incorrecto

```
GET /stores/open
GET /stores/byCity/madrid
```

Correcto

```
GET /stores?status=open
GET /stores?city=madrid
```