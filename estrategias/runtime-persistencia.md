# Runtime y Persistencia

Estrategias para manejar la ejecución de procesos, el estado y la persistencia en sistemas que interactúan con herramientas externas como Git.

## Sidecar No Bloqueante

Cuando una operación no es crítica para la respuesta inmediata (enviar un email, notificar a un servicio externo), ejecutala en un sidecar fire-and-forget. El handler principal responde rápido y el sidecar procesa en segundo plano.

**Regla**: el sidecar debe tener su propio contexto de error y logging. Si falla, se loguea pero no bloquea al caller.

## Locks con Caminos Bypass

Cuando un recurso compartido necesita acceso exclusivo, usá locks. Pero siempre diseñá un **camino de bypass** para casos de emergencia: un admin que pueda forzar la liberación, un timeout que libere automáticamente, un mecanismo de heartbeat que detecte locks huérfanos.

## Pipeline Desacoplado

Separar la **generación** de la **ejecución** de un pipeline. Primero generá el plan completo de lo que hay que hacer, después ejecutalo. Esto permite:
- Validar el plan antes de ejecutar
- Reintentar pasos individuales
- Tener visibilidad de todo el proceso antes de que empiece

## Recovery con Hash de Orphan Commit

Cuando trabajás con Git y algo falla a mitad de un proceso, podés quedar con commits huérfanos. Usá el hash del commit como clave de recovery: si el proceso se reanuda, sabés qué commits ya se hicieron y cuáles no.

## Snapshot Inmutable para Plumbing Git

Antes de hacer operaciones complejas con Git, tomá un snapshot del estado actual. Si algo sale mal, podés restaurar desde el snapshot. El snapshot debe ser inmutable — una vez tomado, no se modifica.

## Cinco Fallas Silenciosas en el Pipeline Commit-Release

1. **Commit sin stage**: el pipeline asume que los archivos están staged pero no lo están
2. **Push sin fetch previo**: el remote avanzó y el push rebasa
3. **Tag duplicado**: el tag ya existe y el pipeline falla silenciosamente
4. **Branch desactualizada**: la branch local no refleja el remote
5. **Hook que falla**: un hook de git (pre-commit, pre-push) falla pero el pipeline no lo captura

Cada una de estas fallas debería tener un chequeo explícito en el pipeline, no un catch genérico.
