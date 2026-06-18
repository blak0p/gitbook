# Dual-Token JWT con HttpOnly Cookie

## El problema

Tenés una aplicación web con frontend y backend separados. El usuario se loguea y necesitás mantener la sesión. La solución clásica es guardar el JWT en localStorage y mandarlo en cada request.

El problema: cualquier script injectado (XSS) puede leer localStorage y robar el token. Si te llegan a inyectar un script, perdiste la sesión y no hay forma de recuperarla.

## Por qué dual-token

La solución es separar responsabilidades en dos tokens con distinta vida y distinto nivel de exposición:

**Access Token** (15 minutos): vive en RAM, en una variable de JavaScript. El frontend lo necesita para armar el header `Authorization`. Si el usuario recarga la página, se pierde.

**Refresh Token** (7 días): vive en una cookie HttpOnly, Secure, SameSite=Strict. El JavaScript **no puede leerlo**. Solo el browser lo manda automáticamente al endpoint de refresh.

El refresh token es de un solo uso. Cada vez que se usa, se rota por uno nuevo. Si alguien roba el refresh token y lo usa, el próximo refresh legítimo falla porque el token ya fue consumido. Ahí sabés que te robaron la sesión.

## Cómo lo pienso

Pensá en un casino. El access token es la ficha que tenés en la mano mientras jugás. Si te la roban, perdés las fichas de esa mesa nomás. El refresh token es la tarjeta del casino que te permite sacar más fichas del cajero — pero la tarjeta está guardada en la caja fuerte (HttpOnly), no la tenés en el bolsillo. Y cada vez que la usás, el casino te da una tarjeta nueva. Si alguien usó tu tarjeta antes que vos, cuando vayas al cajero te van a decir "esta tarjeta ya fue usada".

## Lo que aprendí

- **El silent refresh en el frontend es obligatorio**: cuando el access token expira, el frontend tiene que pedir uno nuevo antes de que el usuario note algo. Hacelo en un interceptor de fetch/axios.
- **Queueá los 401s concurrentes**: si dos requests fallan al mismo tiempo por token expirado, no disparés dos refreshes. Queueá el segundo y reutilizá el primero.
- **La rotación atómica requiere transacción**: el refresh y la rotación tienen que ser atómicos. Si no, podés terminar con dos refresh tokens válidos.
- **SameSite=Strict requiere mismo dominio**: si frontend y backend están en dominios diferentes, necesitás un proxy o ajustar CORS.

## Alternativas que consideré

| Opción | Por qué no |
|--------|-----------|
| localStorage + token de 24h | XSS roba todo, sin revocación |
| Solo cookie HttpOnly (sin access token) | El frontend no puede armar headers, rompe la SPA |
| OAuth2 completo | Overkill para un monolith con un frontend |

## Cuándo usarlo

- ✅ Cuando tenés un monolith con frontend SPA
- ✅ Cuando frontend y backend comparten dominio o tenés un proxy
- ✅ Cuando querés que cerrar sesión invalide el token al toque
- ❌ Cuando necesitás social login o federación — ahí ya necesitás OAuth2
- ❌ Cuando frontend y backend están en dominios separados sin proxy
- ❌ Cuando tenés una API pública para terceros — ellos no pueden mandar cookies

---

**Relacionado**: [Jerarquía de Roles](jerarquia-roles.md) | [Conocimientos](../index)
