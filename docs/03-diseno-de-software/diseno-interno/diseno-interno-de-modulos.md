# Diseño interno de módulos

> Detalle del módulo de compra (catálogo → carrito → pedido), usando el código real de referencia en `tecnologia/boilerplate-clean-arquitecture/src/app/`.

## Estructura de carpetas
src/app/
├── dominio/
│   ├── modelos/
│   │   ├── producto.modelo.ts
│   │   ├── carrito.modelo.ts
│   │   ├── pedido.modelo.ts
│   │   └── precios.ts
│   └── contratos/
│       ├── repositorio-productos.contrato.ts
│       ├── repositorio-pedidos.contrato.ts
│       ├── procesador-pagos.contrato.ts
│       └── notificador-cliente.contrato.ts
├── aplicacion/
│   ├── consultar-catalogo.caso-uso.ts
│   ├── agregar-al-carrito.caso-uso.ts
│   └── registrar-compra.caso-uso.ts
├── infraestructura/
│   ├── repositorio-productos-memoria.ts / repositorio-productos-http.ts
│   ├── repositorio-pedidos-memoria.ts
│   ├── procesador-pagos-simulado.ts / procesador-pagos-niubiz.ts
│   ├── notificador-consola.ts / notificador-whatsapp.ts
│   └── tokens.ts
├── presentacion/
│   ├── estado-carrito.servicio.ts
│   ├── catalogo/catalogo.component.ts
│   └── carrito/carrito.component.ts
└── app.config.ts   (raiz de composicion)

## Archivos por capa y responsabilidad

| Archivo | Capa | Responsabilidad |
|---|---|---|
| `producto.modelo.ts` | Dominio | Entidad `Producto`: valida sus propios datos, controla el stock (`hayStockPara`, `descontar`). |
| `carrito.modelo.ts` | Dominio | Entidad `Carrito`, inmutable: agregar/quitar devuelve un carrito nuevo; calcula subtotal y total. |
| `pedido.modelo.ts` | Dominio | Entidad `Pedido`: se crea con `Pedido.crear()` (valida cliente, líneas y total); controla su propio estado (`puedeCancelarse`). |
| `precios.ts` | Dominio | Reglas de negocio de precio: comisión del marketplace (10 %) e IGV (18 %). |
| `*.contrato.ts` | Dominio | Interfaces `RepositorioProductos`, `RepositorioPedidos`, `ProcesadorPagos`, `NotificadorCliente` — el dominio define qué necesita, no cómo se cumple. |
| `consultar-catalogo.caso-uso.ts` | Aplicación | Lista productos disponibles a través del contrato `RepositorioProductos`. |
| `agregar-al-carrito.caso-uso.ts` | Aplicación | Busca el producto y delega la regla de stock al `Carrito`. |
| `registrar-compra.caso-uso.ts` | Aplicación | Orquesta el flujo completo de compra (ver diagrama de secuencia). No contiene reglas de negocio: se las pide a las entidades. |
| `repositorio-productos-memoria.ts` / `-http.ts` | Infraestructura | Implementan `RepositorioProductos` con un arreglo en memoria o una API HTTP real. |
| `procesador-pagos-simulado.ts` / `-niubiz.ts` | Infraestructura | Implementan `ProcesadorPagos`; el de Niubiz traduce la respuesta del proveedor (`ACTION_CODE`) al vocabulario del dominio (`aprobado`). |
| `notificador-consola.ts` / `-whatsapp.ts` | Infraestructura | Implementan `NotificadorCliente`. |
| `tokens.ts` | Infraestructura | `InjectionToken` de Angular para poder inyectar una interfaz (las interfaces de TypeScript no existen en tiempo de ejecución). |
| `app.config.ts` | Infraestructura (raíz de composición) | Único lugar donde se decide qué implementación concreta cumple cada contrato. |

## Regla de dependencias

| Desde | Hacia | ¿Permitido? |
|---|---|---|
| Dominio | Aplicación, Infraestructura, Presentación | No — el dominio no importa nada de las otras capas. |
| Aplicación | Dominio | Sí — los casos de uso solo importan modelos y contratos. |
| Infraestructura | Dominio | Sí — los adaptadores implementan los contratos del dominio. |
| Presentación | Aplicación, Dominio | Sí — los componentes usan los casos de uso. |

## Diagrama de secuencia: registrar una compra

| Paso | Origen → destino | Acción |
|---|---|---|
| 1 | `RegistrarCompraCasoUso.ejecutar()` | Verifica que el carrito no esté vacío. |
| 2 | → `Producto.hayStockPara()` | Verifica stock de cada línea, sin descontarlo todavía. |
| 3 | → `Carrito.calcularTotal()` | Calcula el total (subtotal con comisión + IGV). |
| 4 | → `ProcesadorPagos.cobrar()` (contrato) | Solicita el cobro. Si es rechazado, el flujo se detiene aquí y el stock no se toca. |
| 5 | → `Producto.descontar()` | Recién con el pago aprobado, descuenta el stock. |
| 6 | → `Pedido.crear()` | La entidad valida sus propios datos antes de nacer. |
| 7 | → `RepositorioProductos.guardar()`, `RepositorioPedidos.guardar()` (contratos) | Persiste los cambios. |
| 8 | → `NotificadorCliente.confirmarPedido()` (contrato) | Avisa al cliente. |

## Raíz de composición

`app.config.ts` es el único archivo de todo el proyecto donde aparecen los nombres concretos (`RepositorioProductosMemoria`, `ProcesadorPagosNiubiz`, etc.). Cambiar de un adaptador en memoria a uno real, o de proveedor de pagos, es cambiar una línea ahí — ni el dominio ni los casos de uso se enteran.