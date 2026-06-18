# Adaptadores y Sincronización

Patrones para construir adaptadores robustos, manejar concurrencia y sincronizar operaciones.

## Adaptadores con Timeout y Retry

Todo adaptador que haga I/O (red, disco, ejecución de comandos) debería tener:

1. **Timeout**: un límite de tiempo para la operación. Si el recurso externo no responde, el adaptador falla rápido.
2. **Retry con backoff**: para operaciones idempotentes, reintentar con backoff exponencial. Para operaciones no idempotentes, no reintentar.
3. **Circuit breaker**: si el recurso externo falla consistentemente, dejar de intentar por un período.

## Sidecar No Bloqueante

Para operaciones que no son críticas para la respuesta inmediata:

```go
go func() {
    err := sidecar.Do(ctx)
    if err != nil {
        log.Error("sidecar failed", "error", err)
    }
}()
```

El sidecar debe tener su propio contexto, logging y manejo de errores. No bloquea al caller.

## Seed Idempotente como Adaptador de Sistema

Los seeds (datos iniciales) deben ser idempotentes: ejecutarlos N veces produce el mismo resultado. Implementalos como un adaptador más, no como un script suelto. Esto permite:

- Ejecutarlos en el mismo ciclo de vida que la aplicación
- Testearlos como cualquier otro adaptador
- Tener logging y manejo de errores consistente

## Pool de Adaptadores Compartidos

Cuando varios handlers necesitan el mismo recurso (un cliente HTTP, una conexión a DB, un executor de comandos), creá un pool de adaptadores compartidos. Un adapter reutilizable entre handlers evita:

- Duplicación de configuración
- Conexiones múltiples innecesarias
- Inconsistencias en timeouts y retries

---

**Relacionado**: [Adaptadores (Hexagonal)](../arquitectura/hexagonal/adaptadores) | [Runtime y Persistencia](../estrategias/runtime-persistencia) | [Patrones](index)
