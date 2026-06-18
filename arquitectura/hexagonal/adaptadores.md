# Cómo Implementar Adaptadores

Los adaptadores son el código de bajo nivel que conecta el dominio con el mundo exterior. Son un pasamanos: transforman datos técnicos en tipos del dominio y viceversa.

## Reglas de Oro

- **Pasamanos liviano**: el adaptador no decide nada. Solo transforma.
- **Implementación del Contrato**: sabe "cómo" se hace (usa `exec`, `http.Client`, SQL), pero lo oculta detrás de la interfaz del puerto.
- **Transformación en la Frontera**: si hay que mapear DTOs complejos, se hace acá para que el dominio reciba datos limpios.

## Organización

- `adapters/in/`: Los que "empujan" datos (handlers HTTP, CLI, MCP).
- `adapters/out/`: Los que "buscan" datos (repositorios SQL, adaptadores de exec, clientes HTTP).

## Traducción de Errores en la Frontera

Esta es una de las responsabilidades más críticas del adaptador. Nunca dejés que un error técnico crudo se filtre al dominio.

```go
func (r *userRepository) FindByID(ctx context.Context, id domain.UserID) (domain.User, error) {
    var row dto.UserRow
    err := r.db.QueryRow(ctx, "SELECT * FROM users WHERE id = $1", id).Scan(&row)
    if err != nil {
        if errors.Is(err, pgx.ErrNoRows) {
            return domain.User{}, fmt.Errorf("user %s not found: %w", id, domain.ErrUserNotFound)
        }
        return domain.User{}, fmt.Errorf("db error looking up user %s: %w", id, domain.ErrRepository)
    }
    return row.toDomain(), nil
}
```

```go
func (a *execAdapter) Stage(paths []string) error {
    cmd := exec.Command("git", append([]string{"add"}, paths...)...)
    output, err := cmd.CombinedOutput()
    if err != nil {
        return fmt.Errorf("git stage failed: %w", domain.ErrGitCommandFailed)
    }
    _ = output
    return nil
}
```

**Por qué es importante**:
- El dominio no debería importar `database/sql`, `os/exec`, `net/http`
- Los errores técnicos crudos son ruido para la lógica de negocio
- Si cambiás de Postgres a SQLite, solo cambiás el adapter; el dominio no se entera
