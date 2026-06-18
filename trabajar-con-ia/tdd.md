# Strict TDD

## Qué es

TDD (Test-Driven Development) con IA no es lo mismo que TDD tradicional. Cuando la IA escribe el código, la tentación es saltarse los tests y pedirle que "haga todo". Strict TDD es una disciplina para evitar eso: primero el test, después el código, siempre.

## Por qué es necesario

Cuando trabajás con IA, el riesgo no es que la IA escriba mal código — es que escriba código que parece funcionar pero no está testeado. Sin tests, no sabés si el cambio rompió algo. Con tests, tenés una red de seguridad que te permite iterar rápido.

## Cómo funciona

El ciclo es el mismo que TDD clásico, pero con IA:

1. **Escribí el test primero**: definí qué comportamiento querés, después pedile a la IA que lo implemente
2. **La IA implementa**: con el test como especificación, la IA sabe exactamente qué tiene que hacer
3. **Corré el test**: si falla, la IA ajusta. Si pasa, siguiente
4. **Refactorizá**: con tests verdes, podés refactorizar tranquilo

## Reglas que sigo

- **Nunca código sin test**: si la IA escribe código sin test, no lo aceptes. El test es la especificación.
- **Un test por comportamiento**: no un test gigante que prueba todo. Tests chicos, enfocados, que fallen por una razón.
- **Tests de dominio primero**: son los más rápidos y los que más valor aportan. Dejá los tests de integración para después.
- **Mockeá interfaces, no implementaciones**: la IA tiende a mockear structs concretos. Corregila.

## Lo que aprendí

- **La IA respeta los tests**: si el test está bien escrito, la IA va a implementar exactamente lo que el test pide. El test es la mejor especificación que le podés dar.
- **Los tests de dominio son los más fáciles de delegar**: son puros, sin I/O, sin mocks complejos. La IA los entiende bien.
- **No dejes que la IA escriba los tests después del código**: si el código ya está, la IA va a escribir tests que pasen, no tests que prueben algo valioso.
- **Strict TDD es más lento al principio, más rápido después**: el tiempo que perdés escribiendo tests lo recuperás cuando cambiás algo y los tests te avisan que rompiste todo.

---

**Relacionado**: [SDD](sdd.md) | [Testing de Calidad](../conocimientos/testing/testing-de-calidad) | [Trabajar con IA](index.md)
