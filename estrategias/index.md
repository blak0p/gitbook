# Estrategias de Diseño

Las estrategias son patrones de decisión — no código, sino el **por qué** detrás de elegir un camino sobre otro. Cada nota preserva el trade-off y el contexto que lo justifica.

## Secciones

- **Interfaces LLM** — Diseño de herramientas MCP, granularidad, higiene de contexto
- **Clasificación y Heurística** — Lógica de decisión, pesos de impacto, taxonomía de cambios
- **Estructura e Impacto** — Mantenibilidad, acoplamiento, análisis de impacto arquitectónico
- **Runtime y Persistencia** — Optimización de ejecución, locks, sidecars, pipelines

---

## Criterio General

| Pregunta | Respuesta |
|----------|-----------|
| ¿Es una decisión con alternativas? | → Estrategia |
| ¿Es un principio estructural? | → Arquitectura |
| ¿Es un patrón de implementación? | → Patrones |
