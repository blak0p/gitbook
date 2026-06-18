# Jerarquía de Roles

## El problema

Tenés usuarios con distintos niveles de acceso: admin, usuario premium, usuario gratis. En cada endpoint necesitás verificar que el usuario tenga permiso suficiente. La solución instintiva es comparar strings: `if role != "admin"`.

Eso funciona al principio. Pero cuando tenés herencia (admin puede hacer todo lo que puede user, user premium puede hacer todo lo que puede user gratis), las comparaciones de strings se vuelven un desastre. Cada endpoint tiene su propia lógica, y cuando agregás un rol nuevo, tenés que tocar N archivos.

## Por qué jerarquía numérica

Modelo el rol como un valor con ranking interno y un solo método: `¿mi rol es al menos X?`

```go
func (r Role) AtLeast(target Role) bool {
    return r.rank() >= target.rank()
}
```

El middleware queda limpio:

```go
func requireRole(minRole domain.Role) gin.HandlerFunc {
    return func(c *gin.Context) {
        if !claims.Role.AtLeast(minRole) {
            c.AbortWithStatusJSON(403, "no tenés permiso")
            return
        }
        c.Next()
    }
}
```

Y el router simplemente dice lo que necesita:

```go
admin := r.Group("/admin", requireRole(RoleAdmin))
admin.GET("/users", handler.ListUsers)
```

## Lo que aprendí

- **Un solo lugar donde cambiar la jerarquía**: si mañana agregás un rol "moderador" entre admin y user, cambiás un switch y listo.
- **Los tests de roles son los más fáciles de escribir**: son tabulares, puros, sin mocks. Si la jerarquía cambia, los tests te avisan.
- **No sirve para todo**: si necesitás permisos cruzados (ej. "solo puede editar su propio perfil"), la jerarquía no alcanza. Ahí necesitás ABAC o políticas.

## Alternativas que consideré

| Opción | Por qué no |
|--------|-----------|
| Comparar strings en cada handler | No escala, herencia manual, fácil de olvidar |
| Lista de permisos granular (`can_create_user`) | Overkill para 3 roles, agrega complejidad prematura |

## Cuándo usarlo

- ✅ Cuando los roles forman una pirámide clara (admin > user > invitado)
- ✅ Cuando querés que el middleware sea de 5 líneas
- ❌ Cuando los permisos son cruzados o dependen del recurso
- ❌ Si tenés 2 roles sin herencia — una comparación directa es más honesta

---

**Relacionado**: [Dual-Token JWT](dual-token-jwt) | [Testing de Calidad](../testing/testing-de-calidad) | [Conocimientos](../index)
