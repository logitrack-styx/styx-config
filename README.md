# logitrack-config

Repositorio de configuraciones externas de LogiTrack, servido por `config-server` (Spring Cloud Config).

## Convención de nombres

| Archivo                        | Se aplica a                     |
|--------------------------------|---------------------------------|
| `<servicio>.yaml`              | todos los perfiles              |
| `<servicio>-dev.yaml`          | perfil `dev` (IDE / local)      |
| `<servicio>-prod.yaml`         | perfil `prod` (Docker Compose)  |

`<servicio>` = valor de `spring.application.name` del cliente: `shipment-service`, `courier-service`, `security-service`.
El perfil sobrescribe al archivo base.

## Regla de oro: NO SECRETOS

Acá solo va configuración no sensible. Las claves se escriben como placeholders (`${DB_PASSWORD}`) y
cada contenedor las recibe por variable de entorno (ver `.env.example` del Compose).

## Verificación rápida

```
curl http://localhost:8888/courier-service/dev
curl http://localhost:8888/courier-service/prod
```

Los placeholders aparecen sin resolver (`${DB_PASSWORD}`): es lo esperado, los resuelve cada cliente con su entorno.

## Cambios

Sprint 4 agrega el enrutamiento a Payment, los orígenes CORS por entorno y los flags de correo y asignación. Los clientes mantienen sus emisores JWT y las rutas legadas. Los parámetros SMTP, RabbitMQ y Mercado Pago se resuelven desde el entorno de cada cliente. Los valores predeterminados mantienen correo y mensajería deshabilitados, y seleccionan FAKE para pagos.

El Compose de backend monta este repositorio en un Config Server con perfil native. La prueba de Shipment `scripts/test-sprint4-backend.ps1` verifica el comportamiento de estas propiedades mediante Gateway, Security, Shipment, Courier y Payment.

Los clientes leen la configuración al arrancar. Tras un commit a `main`, reiniciá el servicio que quieras actualizar.
