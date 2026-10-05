# Principios de diseño (SOLID)

## Aplicados al módulo de compra

| Principio | Cómo se aplica |
|---|---|
| **S — Responsabilidad única** | `ConsultarCatalogoCasoUso` solo consulta; `AgregarAlCarritoCasoUso` solo agrega; `RegistrarCompraCasoUso` solo orquesta la compra. Cada entidad valida solo sus propias reglas (`Producto` su stock, `Carrito` sus líneas, `Pedido` su estado). |
| **O — Abierto/cerrado** | Agregar un proveedor de pagos nuevo es crear una clase que implemente `ProcesadorPagos` (por ejemplo, otra pasarela) sin modificar `RegistrarCompraCasoUso`. |
| **L — Sustitución de Liskov** | `ProcesadorPagosSimulado` y `ProcesadorPagosNiubiz` son intercambiables: `RegistrarCompraCasoUso` funciona igual con cualquiera de los dos, porque ambos cumplen el mismo contrato (`cobrar(monto, medioPago): Promise<ResultadoCobro>`). |
| **I — Segregación de interfaces** | Hay cuatro contratos separados (`RepositorioProductos`, `RepositorioPedidos`, `ProcesadorPagos`, `NotificadorCliente`) en vez de uno solo gigante; cada caso de uso depende solo de los que realmente necesita. |
| **D — Inversión de dependencias** | `RegistrarCompraCasoUso` recibe los cuatro contratos por constructor; nunca hace `new RepositorioProductosMemoria()` ni conoce esa clase. Quien decide la implementación concreta es `app.config.ts`. |

## Qué pasaría sin SOLID

| Sin el principio | Consecuencia |
|---|---|
| Sin SRP | `RegistrarCompraCasoUso` validando stock, calculando IGV y armando el HTML del recibo en un mismo método: un cambio de interfaz rompería la lógica de negocio. |
| Sin OCP | Cambiar de pasarela de pago obligaría a editar `RegistrarCompraCasoUso` con un `if (proveedor === 'niubiz')`. |
| Sin DIP | El caso de uso haría `new RepositorioProductosMemoria()` directamente: no se podría probar con datos falsos ni cambiar a una API sin tocar la lógica de compra. |

## Relación con Clean Architecture y los patrones

El cumplimiento de SOLID —sobre todo DIP— es lo que hace posible la regla de dependencias de Clean Architecture (`enfoque-arquitectonico.md`): como los casos de uso dependen de contratos y no de clases concretas, la capa de dominio y aplicación nunca necesita importar nada de infraestructura. Los patrones Adapter y Repository (`patrones-de-diseno.md`) son la forma concreta en que ese principio se cumple en el código.