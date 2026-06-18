# Cómo Diseñar Puertos

Los puertos son interfaces que definen cómo el mundo exterior interactúa con el dominio y viceversa.

## Reglas de Oro

- **Interfaces quirúrgicas**: 1-3 métodos. Si una interfaz tiene más, probablemente está haciendo demasiado.
- **Excepción — Puertos de Lifecycle**: cuando el puerto define las transiciones de una state machine (lock → plan → blocker → release), todos los métodos son del mismo concepto. 7 métodos en un lifecycle port es cohesivo, no acoplado.
- **Naming de Propósito**: terminá en `Doer`, `Provider`, `Stager`. Describe la misión, no la implementación.
- **Manejo de Errores**: el puerto devuelve errores descriptivos de la frontera. No los escondas.

## Tipos de Puertos

1. **Driving Ports (Entrada)** — Casos de uso. Definen qué puede hacer el sistema.
2. **Driven Ports (Salida)** — Lo que la aplicación necesita del exterior. Definen qué necesita el sistema para funcionar.

## Organización

En estructura por feature, los puertos viven en `{feature}/ports/`. Las interfaces compartidas entre features (Logger, Metrics, ConfigProvider) van en `core/ports/`.

## Ejemplo

```go
type GitStager interface {
    Stage(paths []string) error
}

type UserRepository interface {
    FindByID(ctx context.Context, id UserID) (User, error)
    Save(ctx context.Context, user User) error
}
```
