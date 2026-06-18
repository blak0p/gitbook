# TypeScript

Patrones, convenciones y gotchas para escribir TypeScript mantenible.

## Arquitectura Hexagonal en TypeScript

En TypeScript, la estructura por feature se implementa con directorios y barrels:

```
feature/
  domain/         → interfaces de dominio, tipos, errores
  ports/          → interfaces de puertos
  application/    → casos de uso
  adapters/
    in/           → handlers (API routes, event listeners)
    out/          → implementaciones (repositorios, clientes HTTP)
```

Los barrels (`index.ts`) exportan solo lo necesario hacia afuera. El dominio nunca importa de adapters.

## Patrones

### Value Objects con `type` o `class`

```typescript
type UserId = string & { readonly __brand: 'UserId' }
type Email = string & { readonly __brand: 'Email' }
```

O con clases para validación:

```typescript
class Email {
  private constructor(readonly value: string) {}
  static create(raw: string): Result<Email> {
    if (!raw.includes('@')) return err(ErrInvalidEmail)
    return ok(new Email(raw))
  }
}
```

### Result Type para errores

```typescript
type Result<T, E = Error> = { ok: true; value: T } | { ok: false; error: E }
```

Evitá excepciones para flujo de control. Usá Result para operaciones que pueden fallar.

## Gotchas

### `undefined` vs `null`

TypeScript tiene ambos. Decidí una convención y mantenela. Recomendación: `undefined` para "no está presente", `null` solo para interoperación con APIs externas que lo requieran.

### `as` type assertion

```typescript
// MAL: mentís al compilador
const user = data as User

// BIEN: validás en runtime
const user = validateUser(data)
```

### `any` vs `unknown`

```typescript
// MAL: any desactiva todo type checking
function parse(data: any): string { return data.name }

// BIEN: unknown fuerza a validar antes de usar
function parse(data: unknown): string {
  if (typeof data !== 'object' || !data) return ''
  if (!('name' in data)) return ''
  return String(data.name)
}
```

## Convenciones

- **Naming**: `IUserRepository` para interfaces (aunque en la comunidad hay debate), o `UserRepository` sin prefijo
- **Barrels**: `index.ts` exporta solo la API pública del módulo
- **Config**: tipada con `as const` o `satisfies`, no `any`
- **Tests**: Vitest o Jest, co-located con el código
