# Componentes y Documentación

Patrones para organizar componentes y su documentación asociada.

## Co-location de Documentación de Herramientas

La documentación de una herramienta (MCP, CLI, API) debería vivir cerca del código de la herramienta, no en un wiki separado. Esto asegura que:

- La documentación se actualice cuando cambia el código
- Sea fácil encontrar la documentación relevante
- Los PRs incluyan tanto el cambio como su documentación

## Carga Diferida de Componentes Pesados

Para componentes costosos de inicializar (conexiones a DB, clientes HTTP, modelos de ML), usá carga diferida. El componente se construye cuando se necesita, no cuando se inyecta.

```go
type Service struct {
    db   *sql.DB
    dbOnce sync.Once
}

func (s *Service) DB() *sql.DB {
    s.dbOnce.Do(func() {
        s.db = connectToDB()
    })
    return s.db
}
```

## Default On con Degradación Graciosa

Cuando un componente opcional no está disponible, el sistema sigue funcionando con funcionalidad reducida. No es un error fatal — es una capacidad reducida.

## Pestanas con Degradación Graciosa

En UIs con pestañas, si una pestaña falla al cargar, mostrá un error localizado en esa pestaña. No rompas toda la página ni bloquees las otras pestañas.

---

**Relacionado**: [Testing](testing) | [Patrones](index)
