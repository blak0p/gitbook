# Chained PRs

## El problema

Un cambio de 800 líneas llega a review. El reviewer abre el diff, ve 20 archivos modificados, y no sabe por dónde empezar. Se cansa, aprueba rápido, y se cuelan bugs. O peor, el cambio queda días en review porque nadie quiere masticar ese elefante.

El problema no es el reviewer. Es que el cambio es demasiado grande para revisarlo de una sola vez.

## Por qué encadenar PRs

La solución es partir el cambio en PRs más chicos que se encadenan. Cada PR es revisable en 30 minutos, compila solo, y pasa tests solo.

```
main
  └── feat/mi-feature
        ├── PR #1: dominio + puertos
        ├── PR #2: aplicación
        └── PR #3: adaptadores + wiring
```

Cada PR se mergea a la feature branch, no a main. Solo la feature branch se mergea a main al final.

## Cómo lo pienso

Cada PR es una capa de la cebolla. PR #1 no depende de nadie. PR #2 depende de PR #1. PR #3 depende de PR #2. Si PR #3 tiene problemas, no afecta a main — solo a la feature branch.

## Lo que aprendí

- **Cada PR debe ser independientemente revisable**: no dejés código a medias. Si un PR no tiene sentido solo, está mal partido.
- **Cada PR debe compilar y pasar tests**: si PR #1 rompe algo, no podés mergear PR #2 hasta que esté arreglado.
- **Numerá los PRs en orden de dependencia**: PR #1, PR #2, PR #3. El reviewer sabe por dónde empezar.
- **La feature branch se borra después del merge**: no dejés ramas muertas.

## Alternativa: stacked PRs to main

Cada PR se mergea directamente a main en orden. Más rápido, pero sin rollback parcial. Elegí esta cuando:
- Los cambios son independientes (no comparten estado)
- Necesitás iterar rápido
- El equipo es chico y los merges son frecuentes

## Cuándo usarlo

- ✅ Cuando un cambio pasa de 400 líneas
- ✅ Cuando el cambio tiene capas claras (dominio → aplicación → adaptadores)
- ✅ Cuando querés reviews enfocados
- ❌ Para cambios chicos de un par de archivos
- ❌ Cuando los cambios son independientes y no comparten estado

---

**Relacionado**: [Clasificación](clasificacion) | [SDD](../../trabajar-con-ia/sdd) | [Conocimientos](../index)
