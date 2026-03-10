---
sidebar_position: 4
---

# Responsabilidad en Repositorios

En este apartado se detallan varios aspectos sobre el manejo de responsabilidades de los repositorios.

En Spring Data JPA, cada interfaz de repositorio (por ejemplo, `JpaRepository<T, ID>`) está diseñada para estar estrictamente vinculada a una única entidad de dominio (T). Por regla de arquitectura y diseño de software, los repositorios solo deben contener consultas (queries) que devuelvan, gestionen o afecten a su propia entidad gestionada.

Existen tres razones principales para mantener esta restricción:

- **Principio de Responsabilidad Única (SRP):** Cada repositorio debe ser la única fuente de acceso para una tabla/entidad específica. Si un repositorio empieza a gestionar consultas de otras entidades, se convierte en una clase "todoterreno" gigante, lo que dificulta su mantenimiento y lectura.

- **Integridad del Mapeo y Contexto JPA:** Spring Data y el EntityManager de Hibernate esperan que los resultados de un repositorio se mapeen a su entidad base. Devolver entidades diferentes puede provocar problemas de casting (ClassCastException), conflictos en la caché de primer nivel de Hibernate o comportamientos impredecibles con las asociaciones Lazy.

- **Desacoplamiento arquitectónico:** Aislar las consultas por entidad facilita el testing unitario y la refactorización. Cuando se requiere información cruzada de varios dominios, es la capa de Servicios la encargada de inyectar los repositorios necesarios y orquestar la lógica, no la capa de datos.

## Ejemplo Práctico

Imaginemos que necesitamos obtener todos los Outfits (conjuntos de ropa) creados por un Usuario específico.

### Práctica Incorrecta (Antipatrón)
Hacer la consulta de Outfits dentro del repositorio de Usuarios. Esto acopla fuertemente ambos dominios en la capa de datos.

```java
    @Repository
        public interface UserRepository extends JpaRepository<User, UUID> {
            // MAL: Un UserRepository no debería devolver listas de Outfits.
            @Query("SELECT o FROM Outfit o WHERE o.user.id = :userId")
            List<Outfit> findOutfitsByUserId(@Param("userId") UUID userId);
        }
```

### Buena Práctica
La consulta pertenece al dominio de lo que se está buscando (Outfits). Por lo tanto, debe ir en el OutfitRepository.

```java
    @Repository
        public interface OutfitRepository extends JpaRepository<Outfit, UUID> {
            // BIEN: El repositorio de Outfit devuelve y gestioniona Outfits.
            List<Outfit> findByUserId(UUID userId);
        }
```

### Orquestación en el Servicio
Si un proceso de negocio requiere datos de ambos, el servicio correspondiente utiliza los repositorios adecuados:

```java
    @Service
    @RequiredArgsConstructor
    public class UserProfileService {
        private final UserRepository userRepository;
        private final OutfitRepository outfitRepository; // Se inyecta el repositorio correcto
    
        public UserProfileDTO getUserProfileWithOutfits(UUID userId) {
            User user = userRepository.findById(userId).orElseThrow();
            List<Outfit> userOutfits = outfitRepository.findByUserId(userId);
    
            return new UserProfileDTO(user, userOutfits);
        }
    }
```