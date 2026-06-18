# Testing de Calidad Humana

## El problema

La mayoría de los tests están escritos para que la máquina los pase, no para que un humano entienda la calidad del sistema. Cobertura del 90% no significa nada si los tests que tenés no prueban las cosas importantes.

El resultado: tests que pasan pero el sistema se rompe en producción, tests lentos que nadie quiere ejecutar, y una falsa sensación de seguridad.

## Por qué testear por capas

En arquitectura hexagonal, cada capa tiene una estrategia de testing distinta. No es lo mismo testear el dominio que testear un adaptador. Mezclarlos es el error más común.

**Tests de dominio**: son los más valiosos y los más baratos. Sin mocks, sin I/O, sin setup. Solo funciones puras y aserciones.

```go
func TestNewUser_InvalidName(t *testing.T) {
    _, err := domain.NewUser("")
    assert.ErrorIs(t, err, domain.ErrInvalidName)
}
```

**Tests de aplicación**: mockeás las interfaces de salida y verificás que la orquestación funcione. No probás lógica de negocio, probás que los casos de uso coordinen bien.

**Tests de adaptador**: usás la dependencia real en un entorno aislado. HTTP con `httptest.Server`, base de datos con transacciones que se rollbackean, archivos temporales que se limpian solos.

**Tests E2E**: son los más caros y los menos frecuentes. Probás caminos completos de usuario. Cada test mide y reporta latencia.

## Cómo lo pienso

Invertí proporcionalmente: muchos tests de dominio, algunos de aplicación, pocos de adaptador, y muy pocos E2E. La pirámide no es una metáfora, es una regla de presupuesto.

## El reporte obligatorio

Cuando el sistema involucra LLMs o procesos complejos, un test que pasa no alcanza. Necesitás:

1. **Traza de input**: qué datos exactos entraron
2. **Traza de output**: qué escupió el sistema
3. **Latencia**: cuánto tardó

Si no sabés qué entró y qué salió, no estás evaluando, estás adivinando.

## Lo que aprendí

- **Mock fresco por sub-test**: en tests tabulares, creá un mock nuevo para cada caso. Evitás state bleed.
- **Ningún test depende de otro**: paralelo por default, aislado por diseño.
- **Los tests de dominio son los más rápidos**: si un test de dominio tarda más de unos milisegundos, algo está mal.
- **No testees lo obvio**: no necesitás un test para cada getter y setter. Testeá reglas de negocio, no estructuras de datos.

## Alternativas que consideré

| Enfoque | Por qué no |
|---------|-----------|
| Tests manuales | No escalan, no se repiten, se olvidan |
| Solo E2E | Lentos, frágiles, no te dicen dónde falla |
| Cobertura como meta | 100% de cobertura no significa 100% de calidad |

---

**Relacionado**: [Arquitectura Hexagonal](../arquitectura/hexagonal) | [TDD](../../trabajar-con-ia/tdd) | [Conocimientos](../index)
