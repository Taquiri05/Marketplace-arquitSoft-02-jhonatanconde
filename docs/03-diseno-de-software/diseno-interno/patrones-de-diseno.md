# Patrones de diseño

## Patrones aplicados

| Patrón | Dónde se usa | Problema que resuelve |
|---|---|---|
| **Adapter** | `RepositorioProductosMemoria` / `RepositorioProductosHttp` implementan `RepositorioProductos`; `ProcesadorPagosSimulado` / `ProcesadorPagosNiubiz` implementan `ProcesadorPagos`; `NotificadorConsola` / `NotificadorWhatsApp` implementan `NotificadorCliente`. | Cada proveedor externo (Niubiz, WhatsApp, una API HTTP) habla su propio idioma. El adaptador lo traduce al vocabulario del dominio (`cobrar()`, `confirmarPedido()`) sin que el caso de uso lo note. |
| **Repository** | `RepositorioProductos`, `RepositorioPedidos` (contrato en el dominio + implementación en infraestructura). | El dominio no debe saber si los datos viven en memoria, en una API o en una base de datos. |
| **Factory Method** | `Pedido.crear(...)` — constructor privado, creación solo a través de un método estático que valida antes de construir. | Evita que exista un `Pedido` inválido (sin cliente, vacío o con total ≤ 0); toda la validación de nacimiento queda en un solo lugar. |
| **Composition Root** | `app.config.ts`, con `useFactory` para cada contrato. | Centraliza en un único archivo la decisión de qué implementación concreta se usa, en vez de que cada clase decida por su cuenta. |

## Cómo funciona cada patrón

| Patrón | En pocas palabras |
|---|---|
| Adapter | El caso de uso dice `cobrar(monto, medioPago)`; el adaptador de Niubiz lo convierte en una llamada HTTP a `/authorization` y traduce `ACTION_CODE === '000'` a `aprobado: true`. |
| Repository | El caso de uso dice `guardar(producto)`; el repositorio en memoria lo guarda en un `Map`, el HTTP lo manda a la API. |
| Factory Method | En vez de `new Pedido(...)` desde cualquier lugar, solo `Pedido.crear(...)` puede construir uno, y revisa cliente, líneas y total antes. |
| Composition Root | `app.config.ts` es el único archivo que importa las clases concretas de infraestructura; todo lo demás solo conoce los contratos. |

## Beneficio

Cambiar la pasarela de pago (por ejemplo, de Niubiz a otra) o pasar de datos en memoria a una API real es escribir un adaptador nuevo y cambiar una línea en `app.config.ts`. El dominio y los casos de uso no se modifican.

## Patrones previstos para siguientes iteraciones

| Patrón | Dónde se aplicaría | Cuándo |
|---|---|---|
| Observer | Notificar a varios canales a la vez (consola + WhatsApp) cuando se confirma un pedido. | Si se necesita notificar por más de un canal simultáneamente. |
| Decorator | Cachear las consultas del catálogo. | Si el catálogo crece y se vuelve costoso consultarlo siempre en memoria/API. |