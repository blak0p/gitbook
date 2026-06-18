# Interfaces para LLMs

## El problema

Cuando diseñás una API para que la consuma un humano, ponés documentación, ejemplos, y asumís que el humano va a leer todo. Cuando el consumidor es un LLM, eso no alcanza. El LLM no lee la documentación completa — infiere intención a partir de nombres, descripciones y estructura.

Si la herramienta se llama `writeFile` y la descripción es "escribe un archivo", el LLM la va a usar para todo: escribir código, modificar configuraciones, crear directorios. Y cuando no funcione, va a probar otra herramienta, y otra, y otra.

## Por qué diseñar distinto para LLMs

Un LLM no piensa en operaciones atómicas. Piensa en intenciones. "Quiero hacer un commit con estos cambios" no es "git add, git commit, git push". Es una sola intención.

**Agrupá por dominio, no por tecnología**: un grupo "git" con stage, commit, push es más fácil de entender que un grupo "write" con writeFile, writeGit, writeDB.

**La descripción es la interfaz**: el LLM no lee el código. Lee el nombre y la descripción. Una descripción pobre significa que la herramienta no se va a usar.

**Bien**: "Stage specified files for commit. Use this after creating or modifying files to prepare them for the next commit."

**Mal**: "Git add command"

**Granularidad con intención**: cada herramienta debe representar una intención completa, no una operación atómica.

## Cómo lo pienso

El LLM es un desarrollador junior muy poderoso pero con poca experiencia. Necesita herramientas que le digan exactamente qué hacen, cuándo usarlas, y qué esperar. Si una herramienta promete algo y no lo cumple, el LLM pierde confianza y empieza a hacer workarounds.

## Lo que aprendí

- **Promesas rotas**: si una herramienta dice que hace X pero hace X + Y, el LLM se confunde. Cada herramienta debe hacer exactamente lo que su descripción dice.
- **Safety gates**: validación centralizada antes de ejecutar operaciones destructivas. No es un filtro de permisos — es una confirmación explícita de que el LLM entiende lo que va a hacer.
- **Formato nativo**: si el LLM trabaja naturalmente con markdown, no lo forcés a generar JSON. Parseá del lado del adapter.
- **Default vacío**: cuando una herramienta devuelve una lista vacía, el LLM aprende que "no hay resultados" es válido. Esto evita falsos positivos.

---

**Relacionado**: [Adaptadores](adaptadores.md) | [Herramientas MCP](../../trabajar-con-ia/herramientas-mcp) | [Conocimientos](../index)
