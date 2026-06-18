# Cómo Modelar el Dominio

El dominio es el corazón de la aplicación. Contiene la lógica de negocio pura, sin ninguna dependencia del mundo exterior.

## Reglas de Oro

- **Fábricas (Factories) Obligatorias**: no existen entidades "zombies". Usá `NewSomething(...)` que valida todo en construcción.
- **Validación en Construcción**: un objeto inválido en el dominio es un bug de diseño.
- **Cero Dependencias**: el dominio no conoce bases de datos, frameworks ni APIs.

## Organización por Feature

En la estructura by-feature, el dominio vive en `{feature}/domain/`. Cada feature tiene su propio paquete de dominio con sus entidades, value objects y reglas de negocio. Los tipos base compartidos (Path, ID, Timestamp, errores base) van en `core/domain/`.

## Ejemplo

```go
func NewUser(name string) (User, error) {
    if name == "" {
        return User{}, ErrInvalidName
    }
    if len(name) > 255 {
        return User{}, ErrNameTooLong
    }
    return User{Name: name}, nil
}
```

Cada paquete de dominio es chico. Si `domain/` pasa de 3-4 archivos, dividí por concepto.

---

**Relacionado**: [Puertos](puertos) | [Adaptadores](adaptadores) | [Arquitectura Hexagonal](index)
