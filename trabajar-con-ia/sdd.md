# Spec-Driven Development (SDD)

## Qué es

SDD es una forma de planificar cambios grandes con la ayuda de una IA. En vez de arrancar a codificar y ver qué pasa, primero definís el cambio en etapas: exploración, propuesta, especificación, diseño, tareas, implementación y verificación.

Cada etapa produce un artefacto que la siguiente consume. La IA hace el trabajo pesado de escribir, pero el humano revisa y decide en cada paso.

## Por qué funciona

El problema de pedirle a una IA que "haga un feature" es que no tiene contexto. No sabe qué arquitectura usás, qué convenciones seguís, qué trade-offs ya decidiste. SDD resuelve eso dándole contexto progresivo:

1. **Exploración**: la IA investiga el código existente y entiende cómo está estructurado
2. **Propuesta**: define qué va a cambiar y por qué
3. **Especificación**: escribe los requisitos detallados
4. **Diseño**: define la arquitectura del cambio
5. **Tareas**: parte el cambio en unidades implementables
6. **Implementación**: escribe el código siguiendo las tareas
7. **Verificación**: corre tests y valida que cumpla la especificación

## Cómo lo uso

Para cambios chicos (arreglar un bug, agregar un endpoint), no uso SDD. Para cambios grandes (un feature nuevo, una refactorización), SDD me ahorra horas de ida y vuelta.

El flujo típico:

```
/sdd-nuevo "agregar autenticación con Google"
  → la IA explora el código de auth existente
  → propone cómo integrar Google OAuth
  → escribo la spec
  → diseña los cambios
  → parte en tareas
  → implementa de a una
  → verifica que todo funcione
```

## Lo que aprendí

- **No uses SDD para todo**: para cambios de un archivo, es overkill. Para cambios que cruzan múltiples módulos, es indispensable.
- **El humano siempre revisa**: la IA puede escribir la spec, pero vos sabés si tiene sentido en tu contexto.
- **El contexto es todo**: cuanto mejor contexto le des a la IA (código existente, convenciones, arquitectura), mejores resultados obtenés.
- **Las tareas chicas funcionan mejor**: una tarea = un PR revisable. Si una tarea es muy grande, partila.

---

**Relacionado**: [TDD](tdd.md) | [Herramientas MCP](herramientas-mcp.md) | [Chained PRs](../conocimientos/procesos/chained-prs) | [Trabajar con IA](index.md)
