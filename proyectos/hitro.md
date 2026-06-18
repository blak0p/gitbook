# HITRO

## Qué es

HITRO es una plataforma web completa con backend en Go y frontend en TypeScript. Incluye autenticación con dual-token JWT, jerarquía de roles (admin, user físico, user digital), gestión de productos, y un sistema de tienda.

## Por qué lo hice

Necesitaba una plataforma donde los usuarios pudieran registrarse digitalmente y después, si necesitaban funcionalidades más avanzadas (pagos, compras), hacer un upgrade a usuario físico sin perder su historial ni tener que crearse otra cuenta. El desafío no era técnico — era de diseño: cómo modelar la evolución del usuario sin romper nada.

## Cómo lo pensé

Arquitectura hexagonal por feature, con un módulo de auth que después sirvió de espejo para el módulo de productos:

```
hitro-backend/
  internal/
    auth/
      domain/         → User, Role, jerarquía
      ports/          → AuthProvider, UserRepository
      application/    → login, register, refresh, change-password
      adapters/
        in/           → handlers HTTP (Gin)
        out/          → repositorio Postgres, JWT service
    store/
      product/        → mirror de auth pero con dominio de producto
```

El frontend en TypeScript sigue la misma estructura: hooks personalizados para auth, silent refresh, y manejo de estado en RAM (nada de localStorage para tokens).

## Decisiones clave

- **Dual-token JWT**: access token en RAM (15 min), refresh token en cookie HttpOnly (7 días) con rotación. Si te roban el refresh token, la rotación lo detecta.
- **Fusión de perfiles**: un solo modelo User con role evolutivo. El admin puede convertir un user digital en físico sin perder datos. Es un UPDATE, no un INSERT.
- **Jerarquía de roles con `AtLeast()`**: un método, un switch, cero comparaciones de strings en los handlers.
- **Mirror de módulo hexagonal**: el módulo de productos copió la estructura del módulo de auth. Consistencia > creatividad.

## Lo que aprendí

- **La fusión de identidades es más política que técnica**: el modelo es simple (UPDATE), pero las reglas de negocio (cuándo se puede fusionar, qué pasa con el historial) son lo complejo.
- **El silent refresh en el frontend es más tricky de lo que parece**: queuear 401s concurrentes, no disparar N refreshes, manejar el edge case de que el refresh también falle.
- **El mirror de módulos funciona si el original está sano**: copiar un desastre es acelerar el desastre. Asegurate de que el módulo espejado sea un buen ejemplo.

## Tecnologías

Go (Gin, GORM, JWT), TypeScript (React), Postgres, Docker, arquitectura hexagonal, dual-token auth.

---

**Relacionado**: [git-courer](git-courer) | [Dual-Token JWT](../conocimientos/seguridad/dual-token-jwt) | [Jerarquía de Roles](../conocimientos/seguridad/jerarquia-roles) | [Proyectos](index)
