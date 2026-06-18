# Patrones de Testing

Estrategias y patrones para escribir tests que realmente aporten valor.

## Co-location de Tests

Los tests viven junto al código que prueban:

```
feature/
  domain/
    user.go
    user_test.go
  application/
    create_user.go
    create_user_test.go
  adapters/
    out/
      postgres.go
      postgres_test.go
```

Esto no es solo organización — es una señal de que el código es testeable. Si no podés poner el test al lado del código, probablemente el código está mal diseñado.

## Carga Diferida de Componentes Pesados

Para componentes que son costosos de inicializar (conexiones a DB, clientes HTTP, executors), usá carga diferida con inicialización perezosa. El componente se crea cuando se necesita, no cuando se inyecta.

Esto es especialmente útil en tests: no necesitás inicializar un adapter pesado si el test solo prueba el dominio.

## Default On con Degradación Graciosa

Cuando un componente opcional no está disponible, el sistema debería seguir funcionando con funcionalidad reducida. Por ejemplo:

- Si el servicio de métricas no está disponible, seguí sin métricas
- Si el cache no responde, seguí sin cache
- Si el servicio de email falla, logueá el error y seguí

Esto evita que fallos en componentes no críticos derriben todo el sistema.

## Pestanas con Degradación Graciosa

En interfaces de usuario, si una pestaña no puede cargar su contenido, no rompas toda la página. Mostrá un placeholder, un mensaje de error localizado, y dejá que el resto de la UI siga funcionando.

## Ya que Estamos No Es Justificación

"No es justificación para meter funcionalidad extra en un cambio. Si estás tocando un archivo y ves algo que podrías mejorar, preguntate: ¿esto es parte del cambio o es scope creep? Si es scope creep, anotalo para después y segui con lo tuyo."

---

**Relacionado**: [Testing (Hexagonal)](../arquitectura/hexagonal/testing) | [Componentes](componentes) | [Patrones](index)
