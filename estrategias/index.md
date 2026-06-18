# Estrategias de Diseño

Las estrategias son patrones de decisión — no código, sino el **por qué** detrás de elegir un camino sobre otro. Cada nota preserva el trade-off y el contexto que lo justifica.

## Secciones

- [Interfaces LLM](interfaces-llm) — Diseño de herramientas MCP, granularidad, higiene de contexto
- [Clasificación y Heurística](clasificacion) — Lógica de decisión, pesos de impacto, taxonomía de cambios
- [Estructura e Impacto](estructura-impacto) — Mantenibilidad, acoplamiento, análisis de impacto arquitectónico
- [Runtime y Persistencia](runtime-persistencia) — Optimización de ejecución, locks, sidecars, pipelines
- [Chained PRs](chained-prs) — PRs encadenados con feature branch chain

---

## Criterio General

| Pregunta | Respuesta |
|----------|-----------|
| ¿Es una decisión con alternativas? | → [Estrategia](index) |
| ¿Es un principio estructural? | → [Arquitectura](../arquitectura/index) |
| ¿Es un patrón de implementación? | → [Patrones](../patrones/index) |
