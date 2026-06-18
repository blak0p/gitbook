# Granularidad y Empaquetado

Una de las decisiones más infravaloradas en arquitectura de software es cómo partir el código. El tamaño y la distribución de los componentes determinan la mantenibilidad, la testabilidad y la velocidad del equipo.

## Plumbing vs Porcelain

Tomado de Git: separar el código en dos niveles:

- **Plumbing** — Comandos/operaciones de bajo nivel, reutilizables, que hacen una sola cosa bien
- **Porcelain** — Orquestación de alto nivel, combina plumbing para lograr objetivos de usuario

Esta separación evita que la lógica de orquestación se mezcle con la implementación fina.

## Co-location por Afinidad de Pipeline

Agrupar código no por capa técnica, sino por **afinidad en el pipeline de ejecución**. Si dos componentes siempre se ejecutan juntos o comparten el mismo ciclo de vida, deberían estar cerca en el árbol de directorios.

## Trade-offs

| Enfoque | Ventaja | Costo |
|---------|---------|-------|
| Granularidad fina (muchos archivos chicos) | Fácil de testear, reutilizar, reemplazar | Más navegación, más imports |
| Granularidad gruesa (pocos archivos grandes) | Fácil de leer linealmente | Acoplamiento oculto, difícil de testear |
| Empaquetado estático | Predictible, fácil de entender | Menos flexible a cambios |
| Empaquetado dinámico | Se adapta al cambio | Complejidad cognitiva |

**Regla personal**: un archivo no debería pasar de ~600 líneas. Si lo hace, hay que partirlo. Un archivo = una responsabilidad clara. Si el nombre no alcanza para describirlo, está haciendo demasiado.

---

**Relacionado**: [Arquitectura Hexagonal](hexagonal/index) | [Estructura e Impacto](../estrategias/estructura-impacto) | [Arquitectura](index)
