# Nivel 4 · Diagrama de código

> **Proyecto:** Marketplace · **Módulo detallado:** Compra (catálogo → carrito → pedido) · **Modelo:** C4 · Nivel 4 de 4 · **Notación:** UML

## 1. Objetivo

Mostrar cómo está organizado el código de un componente del nivel 3, usando el código real del repositorio en `tecnologia/boilerplate-clean-arquitecture/src/app/`.

| Aspecto | Descripción |
|---|---|
| Nivel anterior | `Nivel3-Diagrama-de-Componentes.md` |
| Por qué este módulo | Es el único con código de referencia ya escrito; concentra las reglas de compra y la integración con pagos. |
| Detalle completo | `docs/03-diseno-de-software/diseno-interno/diseno-interno-de-modulos.md` |

## 2. Clases por capa

| Capa | Clases | Regla |
|---|---|---|
| Dominio | `Producto`, `Carrito`, `Pedido`, `precios.ts` | Reglas de negocio puras; no importan nada de las otras capas. |
| Dominio (contratos) | `RepositorioProductos`, `RepositorioPedidos`, `ProcesadorPagos`, `NotificadorCliente` | Interfaces que el dominio define y la infraestructura cumple. |
| Aplicación | `ConsultarCatalogoCasoUso`, `AgregarAlCarritoCasoUso`, `RegistrarCompraCasoUso` | Coordinan el flujo; solo importan modelos y contratos del dominio. |
| Infraestructura | `RepositorioProductosMemoria/Http`, `ProcesadorPagosSimulado/Niubiz`, `NotificadorConsola/WhatsApp` | Implementan los contratos con una tecnología concreta. |

> **Regla de dependencia:** las flechas de código apuntan hacia el dominio. La infraestructura implementa interfaces del dominio; el dominio nunca conoce la infraestructura. Ver `docs/03-diseno-de-software/diseno-interno/diseno-interno-de-modulos.md`.

## 3. Notación UML utilizada

| Símbolo | Significado | Ejemplo |
|---|---|---|
| Línea discontinua + triángulo hueco | Realización: implementa una interfaz | `ProcesadorPagosNiubiz` ▷ `ProcesadorPagos` |
| Línea discontinua + flecha abierta | Dependencia: la usa o la crea | `RegistrarCompraCasoUso` «crea» `Pedido` |
| Nombre en cursiva con «interface» | Interfaz | `ProcesadorPagos` |

## 4. Secuencia: registrar una compra

El detalle paso a paso (verificar stock → calcular total → cobrar → descontar stock → crear pedido → persistir → notificar) ya está documentado en `docs/03-diseno-de-software/diseno-interno/diseno-interno-de-modulos.md`, sección "Diagrama de secuencia: registrar una compra".

## 5. Patrones y principios que se ven en este nivel

| Elemento | Patrón o principio |
|---|---|
| `ProcesadorPagosNiubiz`, `ProcesadorPagosSimulado` | Patrón Adapter |
| `RepositorioProductos` y sus implementaciones | Patrón Repository |
| `Pedido.crear(...)` con constructor privado | Patrón Factory Method |
| `RegistrarCompraCasoUso` recibe los contratos por constructor | Principio de inversión de dependencias (SOLID) |

> Detalle completo en `docs/03-diseno-de-software/diseno-interno/patrones-de-diseno.md` y `principios-de-diseno.md`.