# Arquitectura Hexagonal

La arquitectura hexagonal (también llamada puertos y adaptadores) es un patrón de diseño que mantiene la lógica de negocio aislada de los detalles técnicos: bases de datos, APIs, frameworks, CLIs.

## El Modelo Conceptual

Imaginá un puerto pesquero real:

1. **El Barco (Puertos)** — Sale al mar con una misión clara. Define el contrato de lo que entra al sistema.
2. **El Almacén (Adaptadores)** — Es el muelle donde el barco descarga. Traduce datos técnicos (JSON, SQL) en algo que los trabajadores entienden.
3. **Los Trabajadores (Dominio)** — Son los expertos en tierra. Reciben el producto, lo validan y lo procesan siguiendo las reglas del negocio.

**Regla de oro**: el trabajador nunca sube al barco, y el barco nunca entra a la fábrica. Todo pasa por el almacén.

## Estructura por Feature

Cada feature del negocio tiene su propio paquete autocontenido. El código se organiza por **dominio funcional**, no por capa técnica:

```
feature/
  domain/         → entidades, value objects, lógica de negocio
  ports/          → interfaces (driving + driven)
  application/    → casos de uso (un struct, un método público)
  adapters/
    in/           → handlers (HTTP, CLI, MCP, listeners)
    out/          → implementaciones concretas (DB, exec, API)
```

## Comunicación Entre Features

Los features NO se importan entre sí directamente. Se comunican a través de una capa orquestadora:

```
app/
  use_cases/      → orquestación cross-feature
  wire.go         → Composition Root / ensamblado de DI
```

## Componentes Compartidos: core/

Conceptos verdaderamente universales van en `core/`:

```
core/
  domain/         → tipos base (ID, Timestamp, errores base)
  ports/          → interfaces compartidas (Logger, Metrics, ConfigProvider)
  infra/          → implementaciones compartidas
```

Reglas:
- Si algo lo usan 2+ features, considerar `core/`
- Si algo lo usa 1 feature, queda en el feature
- `core/` es complemento, no reemplazo de la estructura por feature

## Contrato de Errores

Los errores siguen un contrato de tres capas:

| Capa | Tipo | Responsabilidad |
|------|------|----------------|
| **Dominio** | Errores semánticos de negocio | Violaciones de reglas (`ErrInvalidName`, `ErrInsufficientFunds`) |
| **Aplicación** | Fallos de orquestación | Wrapea fallos de dependencias, orquesta retries/rollbacks |
| **Adapter** | Traducción técnico → dominio | Captura errores técnicos en la frontera, traduce a errores de dominio |

**Regla**: nunca dejar pasar `sql.ErrNoRows`, `os.PathError` o `exec.ExitError` más allá del adapter.

## Reglas Personales

| Regla | Por qué |
|-------|---------|
| Sin dependencias circulares | domain → application → adapters, siempre en una dirección |
| Errores como tipos de dominio | Errores y valores de retorno son parte del contrato |
| Tests con interfaces reales | Mockear interfaces, no structs |
| Config en struct tipado | Nada de maps mágicos. Validar en construcción |
| Logger como puerto | `ports.Logger` con nivel y estructura. No log global |
| Archivos chicos | Máximo ~600 líneas. Un archivo = una responsabilidad |

## Contenido Relacionado

- [Cómo modelar el dominio](hexagonal/dominio)
- [Cómo diseñar puertos](hexagonal/puertos)
- [Cómo implementar adaptadores](hexagonal/adaptadores)
- [Estrategia de testing](hexagonal/testing)
- [Dual-Token JWT con HttpOnly Cookie](hexagonal/dual-token-jwt)
- [Autorización por Jerarquía de Roles](hexagonal/jerarquia-roles)

**Cross-section**: [Estructura e Impacto](../../estrategias/estructura-impacto) | [Patrones de Testing](../../patrones/testing) | [Go](../../lenguajes/go/index)
