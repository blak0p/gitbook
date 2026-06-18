# Adaptadores y Sincronización

## El problema

Cuando tu código habla con el mundo exterior (bases de datos, APIs, comandos de consola), asumís que todo va a funcionar siempre. Pero la red se cae, el disco se llena, el servicio externo tarda más de la cuenta. Si no preparás tus adaptadores para eso, un timeout en un servicio externo puede tumbar todo tu sistema.

## Por qué timeout, retry y circuit breaker

**Timeout**: toda operación de I/O debería tener un límite de tiempo. Si el recurso externo no responde en X segundos, fallá rápido. No dejes al usuario esperando para siempre.

**Retry con backoff**: para operaciones idempotentes (leer, consultar), reintentá con backoff exponencial. Para operaciones no idempotentes (crear, pagar), no reintentés.

**Circuit breaker**: si el recurso externo falla consistentemente, dejá de intentar por un período. No tiene sentido seguir pegándole a un servicio que está caído.

## Sidecar no bloqueante

Cuando una operación no es crítica para la respuesta inmediata (enviar un email, notificar a un servicio), ejecutala en segundo plano. El handler principal responde rápido y el sidecar procesa después.

```go
go func() {
    err := sidecar.Do(ctx)
    if err != nil {
        log.Error("sidecar failed", "error", err)
    }
}()
```

**Regla**: el sidecar debe tener su propio contexto, logging y manejo de errores. Si falla, se loguea pero no bloquea al caller.

## Seed idempotente

Los datos iniciales deben poder ejecutarse N veces y producir el mismo resultado. Implementalos como un adaptador más, no como un script suelto. Esto permite ejecutarlos en el mismo ciclo de vida que la aplicación y testearlos como cualquier otro adaptador.

## Pool de adaptadores compartidos

Cuando varios handlers necesitan el mismo recurso (un cliente HTTP, una conexión a DB), creá un pool compartido. Evitás duplicación de configuración, conexiones múltiples innecesarias, e inconsistencias en timeouts y retries.

## Lo que aprendí

- **No asumas que el mundo exterior funciona**: toda operación de I/O puede fallar. Preparate para eso.
- **Los timeouts son obligatorios**: un adaptador sin timeout es una bomba de tiempo.
- **Los retries no son gratis**: cada reintento consume recursos. Usalos con criterio.
- **Los circuit breakers salvan el sistema**: si un servicio está caído, no le pegues 50 veces por segundo.

---

**Relacionado**: [Runtime](runtime) | [Contrato de Errores](../arquitectura/contrato-errores) | [Conocimientos](../index)
