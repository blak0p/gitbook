# Runtime y Persistencia

## El problema

Cuando trabajás con procesos largos o sistemas que interactúan con herramientas externas como Git, las fallas a mitad de camino son comunes. Un commit que falla, un push que rebasa, un lock que queda huérfano. Si no tenés mecanismos de recovery, perdés trabajo y no sabés en qué estado quedó el sistema.

## Locks con caminos bypass

Cuando un recurso compartido necesita acceso exclusivo, usá locks. Pero siempre diseñá un camino de escape:

- Un admin que pueda forzar la liberación
- Un timeout que libere automáticamente
- Un heartbeat que detecte locks huérfanos

Un lock sin bypass es un lock que va a quedar trabado en algún momento.

## Pipeline desacoplado

Separar la generación del plan de su ejecución. Primero generá el plan completo de lo que hay que hacer, después ejecutalo. Esto permite:

- Validar el plan antes de ejecutar
- Reintentar pasos individuales
- Tener visibilidad de todo el proceso antes de que empiece

## Recovery con hash de commit huérfano

Cuando trabajás con Git y algo falla a mitad de un proceso, podés quedar con commits huérfanos. Usá el hash del commit como clave de recovery: si el proceso se reanuda, sabés qué commits ya se hicieron y cuáles no.

## Snapshot inmutable

Antes de hacer operaciones complejas, tomá un snapshot del estado actual. Si algo sale mal, restaurás desde el snapshot. El snapshot debe ser inmutable — una vez tomado, no se modifica.

## Fallas silenciosas comunes

Estas cinco fallas aparecen siempre en pipelines de commit y release:

1. **Commit sin stage**: el pipeline asume que los archivos están staged pero no lo están
2. **Push sin fetch previo**: el remote avanzó y el push rebasa
3. **Tag duplicado**: el tag ya existe y el pipeline falla silenciosamente
4. **Branch desactualizada**: la branch local no refleja el remote
5. **Hook que falla**: un hook de git falla pero el pipeline no lo captura

Cada una debería tener un chequeo explícito, no un catch genérico.

## Lo que aprendí

- **Los locks siempre se liberan**: si no tenés un mecanismo de liberación automática, vas a tener locks huérfanos.
- **Validá el plan antes de ejecutar**: es más barato corregir un plan que deshacer una ejecución.
- **Los snapshots son tu red de seguridad**: tomalos antes de operaciones destructivas.
- **Las fallas silenciosas son las peores**: si algo falla, que se sepa. Un error silencioso es un bug esperando.

---

**Relacionado**: [Adaptadores](adaptadores.md) | [Chained PRs](../procesos/chained-prs) | [Conocimientos](../index)
