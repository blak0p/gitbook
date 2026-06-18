# Clasificación y Heurística

Cómo decidir qué tan grande es un cambio, qué impacto tiene y cómo clasificarlo para tomar decisiones informadas.

## Granularidad Semántica

No todos los cambios son iguales. Clasificar por granularidad semántica — no por líneas de código — permite decidir el nivel de review, testing y riesgo:

- **Atómico**: un cambio en una función, sin efectos secundarios
- **Acotado**: un feature completo dentro de un módulo
- **Estructural**: cambios que cruzan módulos o cambian contratos
- **Arquitectónico**: cambios que modifican la topología del sistema

## Peso sobre Frecuencia

Una heurística clave: el **peso** de un cambio (su impacto potencial) importa más que su **frecuencia**. Un cambio de 10 líneas que toca un contrato compartido puede ser más riesgoso que uno de 200 líneas en código interno.

## Resiliencia en Detección de Dominio

Cuando clasificás cambios automáticamente, el detector de dominio debe ser resiliente a:
- Ruido en el diff (formateo, imports, renames)
- Cambios que tocan múltiples dominios
- Falsos positivos por convenciones de naming

## Unanimidad vs Mayoría

Para clasificaciones críticas (¿esto rompe la API? ¿esto necesita approval?), preferí **unanimidad** sobre mayoría. Si un clasificador dice "esto es riesgoso" y los otros dicen que no, investigá. El costo de un falso positivo es una revisión; el costo de un falso negativo es un bug en producción.

## Refinamientos Post-Heurística

La heurística inicial es un filtro grueso. Después de la clasificación automática, siempre debería haber un refinamiento humano o semiautomático que ajuste los casos frontera.
