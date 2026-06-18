# Herramientas MCP

## Qué es MCP

MCP (Model Context Protocol) es un protocolo estándar que permite a asistentes de IA interactuar con herramientas externas. En vez de que cada IA tenga su propia forma de conectarse con Git, la base de datos o el sistema de archivos, MCP define una interfaz común.

## Por qué importa

Sin MCP, cada asistente de IA tiene que reinventar la rueda: cómo ejecutar comandos, cómo leer archivos, cómo interactuar con APIs. Con MCP, cualquier asistente que soporte el protocolo puede usar las mismas herramientas.

## Cómo lo uso

Tengo varias herramientas MCP configuradas:

- **git-courer**: mi propia herramienta para que la IA interactúe con Git (stage, commit, branch, PR)
- **vault**: conexión con mi Obsidian vault personal para leer y escribir notas
- **vault-Git-book**: conexión con el vault de GitBook para publicar documentación
- **GitBook**: para consultar y gestionar spaces de GitBook
- **GitHub**: para issues, PRs, y gestión del repositorio
- **Engram**: memoria persistente entre sesiones de IA

## Cómo diseñar una herramienta MCP

Basado en mi experiencia con git-courer:

1. **Agrupá por dominio**: no mezcles operaciones de Git con operaciones de archivos. Cada herramienta hace una cosa.
2. **Descripciones claras**: la IA no lee el código, lee la descripción. Una descripción pobre = herramienta infrautilizada.
3. **Granularidad con intención**: cada operación debe representar una intención completa, no un comando atómico.
4. **Validación en la herramienta**: no asumas que la IA va a mandar datos válidos. Validá todo en el adapter.

## Lo que aprendí

- **MCP cambia la forma de trabajar**: tener una interfaz estándar para que la IA interactúe con tus herramientas hace que el flujo sea mucho más natural.
- **Las herramientas chicas funcionan mejor**: una herramienta que hace una cosa bien es mejor que una que hace muchas cosas más o menos.
- **El feedback es importante**: la IA necesita saber si la operación fue exitosa o no. Devolvé errores claros y descriptivos.
- **No todas las herramientas necesitan ser MCP**: para operaciones simples (leer un archivo), a veces es más simple usar las capacidades nativas de la IA.

---

**Relacionado**: [SDD](sdd.md) | [Interfaces para LLMs](../conocimientos/vetanas/interfaces-llm) | [git-courer](../proyectos/git-courer) | [Trabajar con IA](index.md)
