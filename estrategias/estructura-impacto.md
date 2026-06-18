# Estructura de Código e Impacto Arquitectónico

Cómo la estructura del código afecta la mantenibilidad, el acoplamiento y la facilidad de hacer cambios.

## Acoplamiento y Flujo

El acoplamiento no es inherentemente malo — el **acoplamiento innecesario** sí lo es. La clave está en distinguir:

- **Acoplamiento por contrato**: dos componentes se conocen por una interfaz estable. Es aceptable.
- **Acoplamiento por implementación**: un componente conoce los detalles internos de otro. Es deuda.
- **Acoplamiento por convención**: dos componentes dependen de una convención no documentada. Es una bomba.

## Anti-Patrones Comunes

| Anti-Patrón | Por qué duele |
|-------------|---------------|
| **Fuga de Infraestructura** | Pasar tipos de datos externos (HTTP, SQL) directamente al dominio |
| **Lógica en Adaptadores** | Poner reglas de negocio fuera del dominio o la aplicación |
| **Dependencia Inversa** | Que una capa interna importe algo de una capa externa |
| **Import Cross-Feature** | Que el Feature A importe del domain/ del Feature B |
| **core/ inflado** | core/ crece más allá de tipos compartidos y se convierte en un dump |
| **Casos de uso huérfanos** | Use cases que no pertenecen a ningún feature |
| **Implementaciones parciales** | Si implementás 1 método de 5, la interfaz está mal |
| **Archivos con \_v1, \_v2, \_new, \_old** | Si hay dos versiones, borrá la vieja |
| **Servicios con 20 métodos** | Una clase con 20 métodos no es un caso de uso, es un desastre |

## Contrato de Errores

Los errores son parte del contrato entre capas. Cada capa tiene responsabilidades distintas:

- **Dominio**: errores semánticos de negocio, comparables con `errors.Is()`
- **Aplicación**: errores de orquestación que wrappean fallos de dependencias
- **Adapter**: traducción de errores técnicos a errores de dominio en la frontera

Un error de infraestructura crudo (`sql.ErrNoRows`, `os.PathError`) nunca debería llegar al dominio.
