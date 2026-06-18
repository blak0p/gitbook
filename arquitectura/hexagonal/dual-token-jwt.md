# Dual-Token JWT con HttpOnly Cookie

## El Problema

Tenés un frontend SPA que necesita saber si el usuario está autenticado, pero no querés que un XSS robe el token. El localStorage es tierra de nadie — cualquier script injectado se lleva la sesión.

## La Solución

Separar responsabilidades en dos tokens:

- **Access Token** (15 min): vive en RAM (variable JS). El frontend lo necesita para armar el header `Authorization: Bearer`. Se pierde al recargar la página.
- **Refresh Token** (7 días): vive en una cookie HttpOnly Secure SameSite=Strict. El JS no puede leerla. Solo el browser la manda automáticamente a `/auth/refresh`.

El refresh token es de **un solo uso** — cada vez que se usa, se rota por uno nuevo. Si alguien roba el refresh token y lo usa, el próximo refresh legítimo falla porque el token ya fue consumido. Ahí sabés que te robaron la sesión.

## Alternativas Consideradas

| Opción | Veredicto |
|--------|-----------|
| localStorage + 24h token | ❌ XSS = sesión robada, sin revocación |
| Access en RAM + Refresh en cookie | ✅ Elegido |
| Solo HttpOnly cookie (sin access token) | ❌ Rompe la arquitectura SPA |
| OAuth2 / OpenID Connect | ❌ Overkill para un monolith con un frontend |

## Tradeoffs

| Ganancia | Sacrificio |
|----------|-----------|
| XSS no roba tokens | Perdés el token al recargar — hay que implementar silent refresh |
| Rotación detecta replay attacks | La rotación atómica requiere una transacción de DB |
| SameSite=Strict bloquea CSRF | No funciona si frontend y backend están en dominios diferentes sin proxy |

## ¿Cuándo Usarlo?

- ✅ Cuando tengas un monolith con un frontend SPA y quieras auth decente sin OAuth2
- ✅ Cuando frontend y backend compartan el mismo dominio o tengas un proxy
- ✅ Cuando quieras que el usuario cierre sesión y el token quede inservible al toque
- ❌ Cuando necesites social login o federación — ahí ya necesitás OAuth2
- ❌ Cuando el frontend y backend estén en dominios separados sin proxy
- ❌ Cuando tengas una API pública que usen terceros — ellos no pueden mandar cookies

---

**Relacionado**: [Autorización por Jerarquía de Roles](jerarquia-roles) | [Arquitectura Hexagonal](index)
