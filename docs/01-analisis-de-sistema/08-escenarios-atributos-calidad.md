# Escenarios de atributos de calidad

> Cada escenario convierte uno de los atributos de calidad (`04-atributos-de-calidad.md`) en una condición medible, usando la estructura de 6 partes: Fuente del estímulo, Estímulo, Artefacto, Entorno, Respuesta esperada y Medida de la respuesta.

## EQ-01. Rendimiento

| Campo | Descripción |
|---|---|
| **Atributo relacionado** | AC01 – Rendimiento |
| **Driver relacionado** | DA02 |
| **Fuente del estímulo** | Cliente |
| **Estímulo** | Consulta el catálogo de productos o el detalle de un producto. |
| **Artefacto** | Módulo Catálogo y módulo Carrito, a través de la API REST. |
| **Entorno** | Operación normal, con alta concurrencia (por ejemplo, durante una campaña comercial). |
| **Respuesta esperada** | El sistema devuelve los resultados sin errores inesperados. |
| **Medida de la respuesta** | *Propuesta a validar:* el 95 % de las consultas responde en 2 segundos o menos, con menos del 1 % de errores, con 200 usuarios simultáneos. |

## EQ-02. Disponibilidad

| Campo | Descripción |
|---|---|
| **Atributo relacionado** | AC02 – Disponibilidad |
| **Driver relacionado** | Ninguno de forma directa; se apoya en DA01 (escalabilidad), ya que ambos se activan en los mismos picos de carga. |
| **Fuente del estímulo** | Cliente o Seller |
| **Estímulo** | Intenta realizar una operación mientras un componente del sistema falla o está sobrecargado. |
| **Artefacto** | Aplicación web y API REST. |
| **Entorno** | Operación normal, incluyendo una posible falla parcial de un componente. |
| **Respuesta esperada** | El sistema mantiene disponibles las operaciones que sí puede atender y comunica el error de forma controlada cuando un servicio necesario no responde. |
| **Medida de la respuesta** | *Propuesta a validar:* disponibilidad mensual de 99,5 % para las operaciones críticas (login, catálogo, checkout). |

## EQ-03. Escalabilidad

| Campo | Descripción |
|---|---|
| **Atributo relacionado** | AC03 – Escalabilidad |
| **Driver relacionado** | DA01 |
| **Fuente del estímulo** | Incremento de usuarios durante una campaña comercial. |
| **Estímulo** | Aumenta significativamente la cantidad de usuarios conectados simultáneamente. |
| **Artefacto** | Módulos de la capa de lógica de negocio (Usuarios, Sellers, Catálogo, Carrito, Pedidos). |
| **Entorno** | Pico de carga documentado (prueba controlada). |
| **Respuesta esperada** | El sistema soporta el aumento sin superar los límites acordados de tiempo de respuesta ni de errores. |
| **Medida de la respuesta** | *Propuesta a validar:* soportar hasta 200 usuarios simultáneos, 95 % de respuestas bajo 3 segundos, menos del 1 % de errores. |

## EQ-04. Seguridad

| Campo | Descripción |
|---|---|
| **Atributo relacionado** | AC04 – Seguridad |
| **Driver relacionado** | DA03 |
| **Fuente del estímulo** | Usuario sin sesión, con credenciales inválidas o sin permisos suficientes. |
| **Estímulo** | Intenta acceder a una función protegida (datos de otro usuario, panel de administración, datos de pago) o ejecutar una operación no autorizada. |
| **Artefacto** | Módulo Usuarios (autenticación y autorización) y las rutas protegidas de la API REST. |
| **Entorno** | Operación normal. |
| **Respuesta esperada** | El sistema rechaza la operación, no ejecuta el cambio solicitado y no expone información sensible en la respuesta. |
| **Medida de la respuesta** | Rechazar el 100 % de los intentos no autorizados en las pruebas definidas, sin exponer datos sensibles. |

## EQ-05. Mantenibilidad

| Campo | Descripción |
|---|---|
| **Atributo relacionado** | AC05 – Mantenibilidad |
| **Driver relacionado** | DA06 |
| **Fuente del estímulo** | Equipo de desarrollo. |
| **Estímulo** | Se modifica una regla de negocio de un módulo, o se necesita reemplazar la pasarela de pago. |
| **Artefacto** | Módulo afectado (por ejemplo, Pedidos para el caso de la pasarela de pago). |
| **Entorno** | Desarrollo y mantenimiento, con el sistema ya organizado en módulos. |
| **Respuesta esperada** | El cambio se concentra en el módulo responsable, sin afectar funcionalidades no relacionadas. |
| **Medida de la respuesta** | Las pruebas del módulo modificado y las pruebas de regresión acordadas deben pasar; no debe requerirse modificar módulos que no dependen directamente del cambio. |

## Resumen

| Código | Atributo | Driver | Qué comprueba |
|---|---|---|---|
| EQ-01 | Rendimiento | DA02 | Tiempos de respuesta bajo concurrencia. |
| EQ-02 | Disponibilidad | — (apoyado en DA01) | Continuidad del servicio ante fallas o picos de carga. |
| EQ-03 | Escalabilidad | DA01 | Soporte de aumento de usuarios dentro de límites definidos. |
| EQ-04 | Seguridad | DA03 | Rechazo de accesos no autorizados. |
| EQ-05 | Mantenibilidad | DA06 | Cambios aislados al módulo correspondiente. |