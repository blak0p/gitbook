# Estrategia de Testing

El testing en arquitectura hexagonal se organiza por capas. Cada capa tiene su propia estrategia, herramientas y nivel de aislamiento.

## Pirámide por Capa

### 1. Tests de Dominio — Pure Function Tests

Validar construcción, reglas de negocio, edge cases. Sin mocks, sin I/O, sin setup.

```go
func TestNewUser_InvalidName(t *testing.T) {
    _, err := domain.NewUser("")
    assert.ErrorIs(t, err, domain.ErrInvalidName)
}
```

### 2. Tests de Aplicación — Component Tests

Lógica de orquestación, caminos de error, gates de auth. Mock de interfaces de salida.

```go
func TestStageWork_AuthFailed(t *testing.T) {
    auth := newMockAuthProvider()
    auth.authorizeReturns(domain.ErrInsufficientPermissions)
    uc := application.NewStageWork(auth, stager)
    err := uc.Execute(ctx, input)
    assert.ErrorIs(t, err, domain.ErrInsufficientPermissions)
}
```

### 3. Tests de Adaptador — Integration Tests

Verificación de contrato con dependencia real en entorno aislado:
- **HTTP**: `httptest.Server`
- **CLI**: golden files
- **DB**: transacción con rollback
- **Filesystem**: temp dirs con `t.Cleanup()`

### 4. Tests E2E — System Boundary Tests

Comportamiento a nivel feature, caminos de usuario reales. En directorio separado (`e2e/`). Cada test mide y reporta latencia.

## Principios Clave

- **Cobertura por capa**: cada capa tiene su propia cobertura. No mezclar responsabilidades.
- **Qué NO testear en cada capa**:
  - **Dominio**: no testear I/O, serialización, networking
  - **Aplicación**: no testear lógica de negocio (eso es del dominio)
  - **Adaptador**: no testear lógica de negocio ni orquestación
  - **E2E**: no testear cada combinación posible (para eso están los tests de dominio)
- **Mock fresco por sub-test**: en tests tabulares, creá un mock nuevo para cada caso.
- **Ningún test depende de otro**: paralelo por default, aislado por diseño.
- **Los tests de dominio son los más rápidos y valiosos**: invertí proporcionalmente.

## El Reporte Obligatorio

Para tests que involucran LLMs o sistemas complejos, el reporte debe permitir auditar la calidad:

1. **Traza de Input**: ¿Qué datos exactos entraron al sistema?
2. **Traza de Output**: ¿Qué escupió el sistema exactamente?
3. **Latencia**: ¿Cuánto tiempo tardó?

Si no sabemos qué entró y qué salió, no estamos evaluando; estamos adivinando.

---

**Relacionado**: [Patrones de Testing](../../patrones/testing) | [Arquitectura Hexagonal](index)
