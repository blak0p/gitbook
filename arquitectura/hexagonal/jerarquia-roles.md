# Autorización por Jerarquía de Roles

## El Problema

Tenés autenticación con JWT pero necesitás controlar quién puede hacer qué. La pregunta no es "¿está logueado?", sino "¿tiene privilegios suficientes?". Y cuando los roles se superponen (admin > user_fisico > user_digital), comparar strings en el middleware se vuelve frágil.

## La Solución

Modelar el rol como un value object con ranking interno y un método `AtLeast(target Role) bool`. El middleware recibe el rol mínimo requerido y delega la decisión en el dominio.

```go
type Role string

const (
    RoleAdmin        Role = "admin"
    RoleUserPhysical Role = "user_physical"
    RoleUserDigital  Role = "user_digital"
)

func (r Role) AtLeast(target Role) bool {
    return r.rank() >= target.rank()
}

func (r Role) rank() int {
    switch r {
    case RoleAdmin:        return 3
    case RoleUserPhysical: return 2
    case RoleUserDigital:  return 1
    }
    return 0
}
```

El middleware queda limpio:

```go
func requireRole(minRole domain.Role) gin.HandlerFunc {
    return func(c *gin.Context) {
        claims, ok := auth.FromContext(c)
        if !ok || !claims.Role.AtLeast(minRole) {
            c.AbortWithStatusJSON(403, newErrResponse(domain.ErrForbidden))
            return
        }
        c.Next()
    }
}
```

## Alternativas Consideradas

| Opción | Veredicto |
|--------|-----------|
| Comparación de strings en cada handler | ❌ No escala, herencia manual |
| Lista de permisos granular | ❌ Overkill para 3 roles |
| Jerarquía numérica con `AtLeast()` | ✅ Elegido |

## Tradeoffs

| Ganancia | Sacrificio |
|----------|-----------|
| Un solo lugar donde cambiar la jerarquía | No sirve para permisos no jerárquicos (ej. "solo puede editar su propio perfil") |
| Middleware de 6 líneas | Requiere que el rol venga en los claims del JWT |
| Tests de dominio baratos — table-driven | Si agregás un rol que no encaja en la jerarquía, el modelo se rompe |

## ¿Cuándo Usarlo?

- ✅ Cuando los roles formen una pirámide clara (admin > manager > user)
- ✅ Cuando quieras evitar que cada handler reinvente la lógica de autorización
- ❌ Cuando los permisos sean cruzados, negados o dependan del recurso (ownership) — ahí necesitás ABAC/RBAC
- ❌ Si tenés 2 roles sin relación de herencia — una comparación directa es más honesta

## Testing

Los tests de dominio son puros y tabulares:

```go
func TestRole_AtLeast(t *testing.T) {
    tests := []struct {
        name   string
        have   domain.Role
        want   domain.Role
        expect bool
    }{
        {"admin satisfies admin", domain.RoleAdmin, domain.RoleAdmin, true},
        {"admin satisfies user_physical", domain.RoleAdmin, domain.RoleUserPhysical, true},
        {"user_physical denies admin", domain.RoleUserPhysical, domain.RoleAdmin, false},
    }
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            assert.Equal(t, tt.expect, tt.have.AtLeast(tt.want))
        })
    }
}
```

Cualquier cambio en la jerarquía rompe un test. Eso es exactamente lo que querés.

---

**Relacionado**: [Dual-Token JWT con HttpOnly Cookie](dual-token-jwt) | [Arquitectura Hexagonal](index) | [Testing](testing)
