# Granularidad y Empaquetado

## El problema

Una de las decisiones más difíciles y menos discutidas en arquitectura es cómo partir el código. ¿Un archivo gigante o muchos archivos chicos? ¿Todo en un paquete o separado por capas? ¿Agrupo por funcionalidad o por tipo de archivo?

La respuesta no es única, pero hay principios que ayudan.

## Por qué importa

La granularidad determina:
- **Mantenibilidad**: qué tan fácil es encontrar y cambiar una pieza de código
- **Testeabilidad**: qué tan fácil es aislar y probar una unidad
- **Velocidad del equipo**: cuánto hay que navegar para entender un cambio

Un archivo de 2000 líneas es difícil de leer, testear y cambiar. Pero 200 archivos de 10 líneas cada uno también son difíciles de navegar. El equilibrio está en el medio.

## Cómo lo pienso

Uso dos conceptos que tomé de Git:

**Plumbing vs Porcelain**: separo el código en dos niveles:
- **Plumbing**: operaciones de bajo nivel, reutilizables, que hacen una sola cosa bien
- **Porcelain**: orquestación de alto nivel, combina plumbing para lograr objetivos

**Co-location por afinidad**: agrupo código no por capa técnica, sino por qué tan seguido se ejecutan juntos. Si dos componentes siempre aparecen en el mismo pipeline, deberían estar cerca en el árbol de directorios.

## Lo que aprendí

- **Un archivo, una responsabilidad**: si el nombre del archivo no alcanza para describirlo, está haciendo demasiado.
- **Máximo ~600 líneas**: es un límite blando. Si llegás, preguntate si podés partirlo.
- **No anticipes**: no crees archivos y carpetas para algo que todavía no existe. Dejá que la estructura crezca con el código.
- **Los índices ayudan**: un `index.md` o `README.md` en cada carpeta ahorra horas de navegación.

## Trade-offs que encontré

| Enfoque | Ventaja | Costo |
|---------|---------|-------|
| Muchos archivos chicos | Fácil de testear y reemplazar | Más navegación, más imports |
| Pocos archivos grandes | Fácil de leer linealmente | Acoplamiento oculto, difícil de testear |
| Empaquetado estático | Predictible, fácil de entender | Menos flexible a cambios |
| Empaquetado dinámico | Se adapta al cambio | Complejidad cognitiva |

No hay respuesta correcta. La clave es ser consciente del trade-off y ajustar cuando empieza a doler.

---

**Relacionado**: [Arquitectura Hexagonal](hexagonal) | [Conocimientos](../index)
