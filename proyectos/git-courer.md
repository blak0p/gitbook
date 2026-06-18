# git-courer

## Qué es

git-courer es una herramienta MCP (Model Context Protocol) que permite a asistentes de IA interactuar con repositorios Git de forma estructurada. Básicamente, le da a un LLM la capacidad de hacer commits, crear branches, y gestionar el flujo de trabajo de Git sin tener que ejecutar comandos sueltos.

## Por qué lo hice

Los asistentes de IA existentes (GitHub Copilot, Cursor, etc.) trabajan bien con archivos individuales pero no entienden el ciclo completo de desarrollo: stage, commit, push, PR. Terminan generando código que el desarrollador tiene que commitear manualmente. git-courer cierra ese ciclo: la IA puede hacer el cambio, stagearlo, commitearlo y hasta crear un PR.

## Cómo lo pensé

Arquitectura hexagonal pura. Cada feature tiene su dominio, sus puertos y sus adaptadores:

```
git-courer/
  core/
    domain/         → tipos base (Path, Commit, Branch)
    ports/          → interfaces (Stager, Committer, LogProvider)
  git/
    domain/         → commit, diff, branch (tipos puros)
    ports/          → stager, log_provider
    application/    → stage_work, create_branch
    adapters/
      in/           → MCP handler
      out/          → exec adapter (ejecuta comandos git reales)
```

La comunicación con Git es a través de un adaptador que ejecuta comandos reales. No hay librerías mágicas — es `exec.Command` con todo lo que eso implica: timeouts, manejo de errores, parsing de output.

## Decisiones clave

- **MCP como interfaz de entrada**: en vez de crear una CLI tradicional, usé el protocolo MCP para que cualquier asistente de IA pueda usarlo sin configuración extra.
- **Testing por capas**: tests de dominio sin mocks, tests de aplicación con mocks de interfaces, tests de adaptador con repositorios git reales en carpetas temporales.
- **Chained PRs**: los cambios grandes se dividen en PRs encadenados. Cada PR es revisable en 30 minutos.

## Lo que aprendí

- **MCP es un game changer**: tener una interfaz estándar para que las IAs interactúen con herramientas cambia completamente el flujo de trabajo.
- **El adaptador de exec es la parte más delicada**: los comandos de git tienen mil edge cases. El parsing de output, los códigos de error, los timeouts — todo hay que manejarlo explícitamente.
- **Los tests con git real son lentos pero necesarios**: mockear git te da velocidad pero no te asegura que funcione en el mundo real. Tenés que tener ambos.

## Tecnologías

Go, MCP, Git, arquitectura hexagonal, table-driven tests, golden files.

---

**Relacionado**: [HITRO](hitro) | [Arquitectura Hexagonal](../conocimientos/arquitectura/hexagonal) | [Herramientas MCP](../trabajar-con-ia/herramientas-mcp) | [Proyectos](index)
