# Chained PRs con Feature Branch Chain

Cómo dividir cambios grandes en PRs encadenados que mantienen la revisión enfocada y el historial limpio.

## El Problema

Un cambio grande (>400 líneas) es difícil de revisar. El reviewer pierde contexto, se cansan, y los bugs se cuelan. Pero no podés mergear a main un cambio a medias.

## La Solución

Usar una **feature branch** como acumulador de integración. Cada PR apunta al PR anterior, no a main:

```
main
  └── feat/tracker-branch
        ├── PR #1: domain + ports    (apunta a feat/tracker)
        ├── PR #2: application       (apunta a PR #1)
        └── PR #3: adapters + wire   (apunta a PR #2)
```

Solo `feat/tracker-branch` se mergea a main al final. Cada PR individual se mergea a `feat/tracker-branch`.

## Beneficios

- **Diffs enfocados**: cada PR es revisable en ~30 minutos
- **Rollback controlado**: si PR #3 tiene problemas, no afecta a main
- **Review en paralelo**: distintos reviewers pueden ver distintos PRs
- **Historial limpio**: main solo ve el merge final de la feature branch

## Reglas

1. Cada PR debe ser **independientemente revisable** — no dejés código a medias
2. Cada PR debe **compilar y pasar tests** por sí solo
3. Los PRs se numeran en orden de dependencia: PR #1 no depende de nadie, PR #2 depende de PR #1, etc.
4. La feature branch se borra después del merge a main

## Alternativa: Stacked PRs to Main

Cada PR se mergea directamente a main en orden. Más rápido, pero sin rollback parcial. Elegí esta cuando:
- Los cambios son independientes (no comparten estado)
- Necesitás iterar rápido
- El equipo es chico y los merges son frecuentes

---

**Relacionado**: [Runtime y Persistencia](runtime-persistencia) | [Estrategias](index)
