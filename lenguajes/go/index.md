# Go

Convenciones, patrones y gotchas para escribir Go idiomático y mantenible.

## Arquitectura Hexagonal en Go

En Go, la arquitectura hexagonal se implementa con packages:

```
feature/
  domain/         → tipos puros, sin imports de infraestructura
  ports/          → interfaces (un archivo por interfaz)
  application/    → un struct por caso de uso
  adapters/
    in/           → handlers (CLI, HTTP, MCP)
    out/          → implementaciones (exec, db, api)
```

**Reglas**:
- Las interfaces se definen donde se **usan**, no donde se implementan
- Los adapters se nombran en minúscula: `execAdapter`, `sqlAdapter`
- Los errores de dominio son value objects: `var ErrInvalidName = errors.New("invalid name")`

## Testing

- Tests de dominio: table-driven, sin mocks, sin I/O
- Tests de aplicación: mock de interfaces de salida
- Tests de adaptador: dependencia real en entorno aislado (`httptest.Server`, temp dirs, tx rollback)
- Usá `t.Cleanup()` para limpieza, no `defer` en el cuerpo del test

## Gotchas Comunes

### `defer` en loops

```go
// MAL: los defers se acumulan hasta que la función termina
for _, f := range files {
    r, _ := os.Open(f)
    defer r.Close()
}

// BIEN: cerrar explícitamente
for _, f := range files {
    r, _ := os.Open(f)
    r.Close()
}
```

### `range` reusa la variable

```go
// MAL: todas las goroutines ven el último valor
for _, v := range items {
    go func() { fmt.Println(v) }()
}

// BIEN: crear una copia local
for _, v := range items {
    v := v
    go func() { fmt.Println(v) }()
}
```

### `nil` slice vs empty slice

```go
// Un nil slice se serializa como "null" en JSON
var s []string          // nil
s := []string{}         // empty, se serializa como "[]"
s := make([]string, 0)  // empty, se serializa como "[]"
```

### Interface nil vs concrete nil

```go
var r io.Reader = nil   // r es nil
var r *os.File = nil    // r no es nil como interface
var w io.Writer = r     // w NO es nil — tiene type info
```

## Convenciones

- **Naming**: `NewSomething()` para constructores, `MustSomething()` para versiones que panic
- **Errores**: `var ErrXxx = errors.New("xxx")` para centinelas, `fmt.Errorf("context: %w", err)` para wrapping
- **Logger como puerto**: `ports.Logger` con nivel y estructura. No log global
- **Config en struct tipado**: nada de maps mágicos. Validar en construcción
