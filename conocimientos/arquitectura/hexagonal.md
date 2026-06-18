# Arquitectura Hexagonal

## El problema

Cuando arrancás un proyecto, todo es lindo. Después de seis meses, tenés lógica de negocio mezclada con llamadas a la base de datos, el handler HTTP tiene reglas de negocio, y cambiar cualquier cosa implica tocar cinco archivos. El código se vuelve frágil: un cambio en la base de datos rompe la lógica de negocio, y un cambio en la lógica de negocio rompe la API.

El problema de fondo es que no separaste lo que **es** el sistema de cómo **se comunica** con el mundo exterior.

## Por qué arquitectura hexagonal

La arquitectura hexagonal (puertos y adaptadores) resuelve esto con una regla simple: **el dominio no sabe nada del mundo exterior**. No importa si usás Postgres, MySQL, una API REST o un CLI — el dominio solo conoce interfaces.

Esto no es teoría. Es pragmatismo puro:
- Si cambiás de base de datos, solo cambiás un adaptador
- Si querés testear lógica de negocio, no necesitás una base de datos
- Si alguien nuevo llega al proyecto, sabe exactamente dónde está cada cosa

## Cómo lo pienso

El modelo mental que uso es el de un puerto pesquero:

1. **Los puertos** son los barcos. Definen qué se pesca (el contrato), no cómo se pesca.
2. **Los adaptadores** son el almacén en el muelle. Reciben el pescado crudo (JSON, SQL, lo que sea) y lo transforman en algo que los trabajadores entienden.
3. **El dominio** son los trabajadores en tierra. Conocen las reglas del negocio: qué pescado es bueno, cuál no, cómo empaquetarlo.

**Regla de oro**: el trabajador nunca sube al barco, y el barco nunca entra a la fábrica. Todo pasa por el almacén.

## Estructura que uso

Cada feature del negocio tiene su propio espacio:

```
feature/
  domain/         → entidades, reglas de negocio
  ports/          → interfaces (qué necesita el feature)
  application/    → casos de uso (un struct, un método)
  adapters/
    in/           → handlers (HTTP, CLI, lo que sea)
    out/          → implementaciones (DB, APIs, exec)
```

Los features no se conocen entre sí. Si necesito coordinar varios, lo hago desde una capa de orquestación aparte.

## Lo que aprendí

- **Interfaces chicas**: 1-3 métodos. Si una interfaz tiene más, la estás usando mal.
- **Errores como parte del dominio**: los errores técnicos (SQL, HTTP) se traducen en la frontera del adaptador. El dominio nunca ve un `sql.ErrNoRows`.
- **Archivos chicos**: si un archivo pasa de 600 líneas, hay que partirlo. Un archivo = una responsabilidad.
- **No te cases con una estructura**: la estructura por feature es mi default, pero si un proyecto chico no lo necesita, no lo fuerzo.

## Alternativas que consideré

| Enfoque | Por qué no |
|---------|-----------|
| Capas tradicionales (controllers, services, repositories) | Termina en god classes y lógica de negocio dispersa |
| MVC | Bueno para CRUDs simples, se queda corto cuando hay lógica de negocio |
| Sin estructura | Diversión los primeros 3 meses, infierno después |

## Cuándo usarlo

- ✅ Cuando el proyecto va a vivir más de 6 meses
- ✅ Cuando hay lógica de negocio que no es CRUD
- ✅ Cuando querés poder testear sin infraestructura
- ❌ Para un script de una sola vez
- ❌ Para un prototipo que va a cambiar completamente

---

**Relacionado**: [Granularidad](granularidad) | [Contrato de Errores](contrato-errores) | [Testing de Calidad](../testing/testing-de-calidad) | [Conocimientos](../index)
