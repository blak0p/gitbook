# Contrato de Errores

## El problema

Los errores en el software suelen manejarse como si fueran todos iguales. Un error de base de datos, uno de validación y uno de red terminan en el mismo lugar, con el mismo formato, y el que los recibe no sabe si puede reintentar, si es culpa suya, o si el sistema está caído.

El resultado: errores genéricos, debugging lento, y sistemas frágiles que esconden problemas reales.

## Por qué importa

Un buen manejo de errores no es solo cuestión de "no crashear". Es parte del contrato entre capas. Cuando diseñás los errores con la misma intención que diseñás las interfaces, ganás:

- **Claridad**: sabés exactamente qué pasó y en qué capa
- **Testeabilidad**: podés testear caminos de error con la misma facilidad que caminos felices
- **Resiliencia**: sabés qué errores son recuperables y cuáles no

## Cómo lo pienso

Tres capas, tres tipos de error:

**Dominio**: errores semánticos de negocio. Son value objects, comparables, expresan una regla violada.

```go
var ErrInvalidName = errors.New("el nombre no puede estar vacío")
var ErrInsufficientFunds = errors.New("saldo insuficiente")
```

**Aplicación**: errores de orquestación. Wrapean fallos de dependencias y agregan contexto.

```go
ErrDependencyFailed{cause: err, dependency: "user_repository"}
```

**Adapter**: traducción de errores técnicos a errores de dominio. Esta es la frontera.

```go
// Nunca dejo pasar sql.ErrNoRows al dominio
if errors.Is(err, pgx.ErrNoRows) {
    return domain.User{}, fmt.Errorf("usuario no encontrado: %w", domain.ErrUserNotFound)
}
```

## Lo que aprendí

- **Los errores de dominio son parte de la API**: si cambiás un error, cambiás el contrato. Testealo.
- **No escondas errores**: traducilos, sí. Pero no los tragues. Un error silencioso es un bug esperando a ocurrir.
- **La frontera de errores es responsabilidad del adaptador**: el dominio nunca debería ver un `os.PathError` o un `exec.ExitError`.
- **Usá `errors.Is()` y `errors.As()`**: en Go, los errores centinela son comparables. Aprovechalo.

## Alternativas que consideré

| Enfoque | Por qué no |
|---------|-----------|
| Un tipo de error para todo | Perdés contexto, no sabés si es recuperable |
| Excepciones para flujo de control | Ocultás la intención, difícil de rastrear |
| Códigos de error numéricos | Crípticos, difíciles de mantener |
| Errores sin wrap | Perdés la causa raíz |

---

**Relacionado**: [Arquitectura Hexagonal](hexagonal) | [Adaptadores](../vetanas/adaptadores) | [Conocimientos](../index)
