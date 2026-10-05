# Nivel 3 · Diagrama de componentes

> **Proyecto:** Marketplace · **Contenedor detallado:** API Marketplace · **Modelo:** C4 · Nivel 3 de 4

## 1. Objetivo

Abrir el contenedor API Marketplace y mostrar sus módulos internos.

| Aspecto | Descripción |
|---|---|
| Nivel anterior | `Nivel2-DiagramadeContenedores.md` |
| Siguiente nivel | `Nivel4-DiagramadeCodigo.md` |

> El diagrama completo y la descripción ya están en `docs/02-arquitectura-software/componentes-arquitectonicos.md` (Paso 03). Aquí se detalla en formato de tabla, que es lo propio de este nivel del C4.

## 2. Componentes

| Componente | Responsabilidad | Requisitos relacionados |
|---|---|---|
| API REST | Punto de entrada HTTP, autenticación y validación de entrada | RC03 |
| Usuarios | Registro, autenticación y roles | — (soporte para los demás módulos) |
| Sellers | Gestión de vendedores | RF07 |
| Catálogo | Productos, precio y stock | RF01, RF02, RF03 |
| Carrito | Ítems y totales | RF04 |
| Pedidos | Checkout, estado del pedido e integraciones externas | RF05, RF06, RF08 |

## 3. Relaciones entre componentes

| Origen | Destino | Tipo de comunicación |
|---|---|---|
| API REST | Todos los módulos | Llamada en memoria |
| Sellers | Usuarios | Llamada en memoria (valida cuenta) |
| Catálogo | Sellers | Llamada en memoria (asocia producto a seller) |
| Carrito | Catálogo | Llamada en memoria (consulta precio y stock) |
| Pedidos | Carrito | Llamada en memoria (convierte carrito en pedido) |

## 4. Relaciones con sistemas externos

| Componente | Destino | Protocolo | Patrón aplicado |
|---|---|---|---|
| Pedidos | Pasarela de pago | HTTPS/REST | Adapter |
| Pedidos | ERP | HTTPS/REST | Adapter |
| Pedidos | Servicio de envío | HTTPS/REST | Adapter |
| Pedidos | Servicio de Facturación | HTTPS/REST | Adapter |

## 5. Reglas del monolito modular

| # | Regla |
|---|---|
| 1 | Todos los módulos se despliegan juntos en el contenedor API Marketplace. |
| 2 | Un módulo usa a otro solo a través de su interfaz pública, nunca accediendo directo a sus datos. |
| 3 | Cada sistema externo se integra en un único módulo (Pedidos), para que un cambio de proveedor quede aislado ahí. |

> **Nota:** estos módulos de backend son el diseño propuesto (Node.js + Express, Nivel 2); el código de referencia que sí existe en el repositorio (`tecnologia/boilerplate-clean-arquitecture/`) ilustra las mismas reglas de Clean Architecture sobre un frontend Angular, por eso el Nivel 4 detalla ese código real en vez de un backend que todavía no se ha escrito.