# Conocimientos

Lo que fui aprendiendo a lo largo de los años, curado y explicado desde el por qué. Cada nota empieza con un problema real y te cuenta qué alternativas había, por qué elegí una, y qué aprendí en el proceso.

## Arquitectura

- [Arquitectura Hexagonal](arquitectura/hexagonal.md) — Por qué aislar el dominio del mundo exterior
- [Granularidad y Empaquetado](arquitectura/granularidad.md) — Cómo decidir el tamaño de los componentes
- [Contrato de Errores](arquitectura/contrato-errores.md) — Cómo manejar errores sin que se te filtren

## Seguridad

- [Dual-Token JWT con HttpOnly Cookie](seguridad/dual-token-jwt.md) — Cómo proteger sesiones sin miedo al XSS
- [Jerarquía de Roles](seguridad/jerarquia-roles.md) — Autorización que no se rompe cuando crece

## Testing

- [Testing de Calidad Humana](testing/testing-de-calidad.md) — Cómo testear para que un humano entienda la calidad real

## Procesos

- [Chained PRs](procesos/chained-prs.md) — Cómo dividir cambios grandes sin morir en el intento
- [Clasificación y Heurística](procesos/clasificacion.md) — Cómo decidir qué tan riesgoso es un cambio

## Vetanas

- [Interfaces para LLMs](vetanas/interfaces-llm.md) — Cómo diseñar APIs que una IA entienda
- [Adaptadores y Sincronización](vetanas/adaptadores.md) — Timeouts, retries, sidecars, circuit breakers
- [Runtime y Persistencia](vetanas/runtime.md) — Locks, pipelines, snapshots, recovery
