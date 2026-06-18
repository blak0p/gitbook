# Diseño de Interfaces para LLMs

Cuando un LLM es el consumidor de tu API o herramienta, el diseño de la interfaz cambia radicalmente. No estás diseñando para un humano que lee documentación — estás diseñando para un modelo que infiere intención a partir de nombres, descripciones y estructura.

## Principios

### 1. Agrupación por Dominio, no por Tecnología

Las herramientas MCP deben agruparse por **dominio funcional**, no por tipo de operación. Un grupo "git" con stage, commit, push es más fácil de entender para un LLM que un grupo "write" con writeFile, writeGit, writeDB.

### 2. Descripciones como Interfaz

La descripción de una herramienta es su interfaz. Un LLM no lee el código — lee el nombre y la descripción para decidir si usarla. Una descripción pobre significa que la herramienta no se va a usar.

**Bien**: `"Stage specified files for commit. Use this after creating or modifying files to prepare them for the next commit."`

**Mal**: `"Git add command"`

### 3. Granularidad con Intención

Cada herramienta debe representar una **intención completa**, no una operación atómica. El LLM piensa en términos de "quiero hacer un commit con estos cambios", no "quiero ejecutar git add, después git commit, después git push".

### 4. Capacidad Opcional via Type Assertion

Cuando una herramienta puede hacer más de lo que su interfaz básica expone, usá type assertion para exponer capacidades adicionales sin romper el contrato base. Esto permite que herramientas simples sigan siendo simples y las complejas ofrezcan más sin acoplar.

### 5. Formato Nativo del LLM

Si el LLM trabaja naturalmente con markdown o texto estructurado, no lo forcés a generar JSON. Dejá que devuelva lo que le es natural y parsealo del lado del adapter. Menos fricción = mejores resultados.

## Higiene de Contexto

- **Promesas Rotas**: si una herramienta promete hacer algo y no lo cumple, el LLM pierde confianza y empieza a hacer workarounds. Cada herramienta debe hacer exactamente lo que su descripción dice.
- **Safety Gates**: validación centralizada antes de ejecutar operaciones destructivas. Un safety gate no es un filtro de permisos — es una confirmación explícita de que el LLM entiende lo que va a hacer.
- **Zero Regression con Default Vacío**: cuando una herramienta devuelve una lista vacía por defecto, el LLM aprende que "no hay resultados" es un caso válido y no un error. Esto evita falsos positivos en la detección de problemas.

---

**Relacionado**: [Estructura e Impacto](estructura-impacto) | [Runtime y Persistencia](runtime-persistencia) | [Estrategias](index)
