# Clasificación y Heurística

## El problema

No todos los cambios en el código son iguales. Un cambio de 10 líneas que toca un contrato compartido puede ser más riesgoso que uno de 200 líneas en código interno. Pero si clasificás todo por líneas de código, te vas a equivocar.

Necesitás una forma de decidir qué tan grande es un cambio, qué impacto tiene, y cómo tratarlo.

## Por qué clasificar por semántica, no por tamaño

La granularidad semántica mira la intención del cambio, no su tamaño:

- **Atómico**: un cambio en una función, sin efectos secundarios
- **Acotado**: un feature completo dentro de un módulo
- **Estructural**: cambios que cruzan módulos o cambian contratos
- **Arquitectónico**: cambios que modifican la topología del sistema

Un cambio estructural de 50 líneas merece más atención que uno acotado de 300.

## Cómo lo pienso

**Peso sobre frecuencia**: el impacto potencial de un cambio importa más que su frecuencia. Un cambio de 10 líneas en una interfaz compartida puede romper 20 módulos. Un cambio de 200 líneas en código interno solo rompe ese módulo.

**Unanimidad sobre mayoría**: para clasificaciones críticas (¿esto rompe la API?), preferí unanimidad. Si un clasificador dice "esto es riesgoso" y los otros dicen que no, investigá. El costo de un falso positivo es una revisión; el costo de un falso negativo es un bug en producción.

## Lo que aprendí

- **La heurística inicial es un filtro grueso**: después de la clasificación automática, siempre debería haber un refinamiento humano.
- **El detector de dominio debe ser resiliente**: ignorá ruido en el diff (formateo, imports, renames). Enfocate en lo que cambia semánticamente.
- **No clasifiques solo por archivos tocados**: un cambio que toca 3 archivos pero cambia un contrato es más riesgoso que uno que toca 10 archivos de tests.

---

**Relacionado**: [Chained PRs](chained-prs.md) | [Conocimientos](../index)
