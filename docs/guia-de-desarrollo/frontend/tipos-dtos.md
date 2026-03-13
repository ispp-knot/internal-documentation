---
sidebar_position: 1
---

# 🎨 Gestión de Tipos (DTOs)

En este proyecto, la capa de tipos de TypeScript actúa como un "espejo" de los DTOs (Data Transfer Objects) definidos en el backend. Para mantener la coherencia, escalabilidad y evitar la duplicación de código, todos los modelos de datos deben seguir las siguientes directrices de arquitectura orientada al dominio (Domain-Driven Design).

## Estructura de Directorios

Todos los tipos de la aplicación se centralizan en el directorio `@/lib/types/` (o `@/types/`). Dentro de este, la estructura se divide estrictamente por dominios de negocio, coincidiendo con los módulos del backend (Auth, Stores, Products, Outfits, etc.).

Cada dominio debe tener su propia carpeta. Dentro de la carpeta del dominio, los archivos se organizan por su función:

```text
types/
├── auth/
│   ├── authDto.ts      # Interfaces que mapean payloads del backend
│   └── authHelper.ts   # Tipos utilitarios exclusivos del frontend
├── clients/
│   └── clientsDto.ts
├── stores/
│   └── storesDto.ts
└── ...
```

## Convenciones de Nomenclatura

### Nombres de Archivos

- Archivos DTO (`[dominio]Dto.ts`): Contienen exclusivamente las interfaces que se envían o reciben a través de la API. Se nombran en camelCase con el sufijo Dto (ej. `authDto.ts`, `productsDto.ts`).

- Archivos Auxiliares (`[dominio]Helper.ts`): Contienen tipos auxiliares, enumeraciones o interfaces que solo existen en el contexto del frontend (ej. estados de un formulario complejo, configuraciones de UI).

### Nombres de Interfaces

- Sufijo DTO: Toda interfaz que represente una petición (Request) o respuesta (Response) exacta del backend debe llevar el sufijo DTO en mayúsculas (ej. `LoginDTO`, `StoreDTO`, `RegisterStoreDTO`).

- Tipos internos de Frontend: Las interfaces que no viajan por red no llevan el sufijo DTO. (ej. `SessionCookiePayload`).

## Composición y Prevención de Duplicidad

Bajo ninguna circunstancia se debe duplicar la estructura de una entidad en múltiples archivos.

Si un DTO de un dominio incluye información de otro dominio, se debe importar la interfaz correspondiente utilizando rutas absolutas (`@/lib/types/...`).

```typescript
    import { StoreDTO } from '@/lib/types/stores/storesDto';
    import { ClientDTO } from '@/lib/types/clients/clientsDto';
    
    export interface UserResponseDTO {
        id: string;
        email: string;
        store: StoreDTO | null;   // Reutiliza el DTO existente
        client: ClientDTO | null; // Reutiliza el DTO existente
    }
```

Redefinir los atributos de store dentro del archivo de autenticación rompe el principio de única fuente de verdad.