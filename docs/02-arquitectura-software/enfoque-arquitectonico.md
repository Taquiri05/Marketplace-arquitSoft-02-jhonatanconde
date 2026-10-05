# Enfoque arquitectónico: Clean Architecture

Las dependencias internas mediante Clean Architecture.

| Elemento | Descripción aplicada al Marketplace |
|---|---|
| Patrón / enfoque arquitectónico | Clean Architecture (Arquitectura Limpia). |
| Objetivo | Separar responsabilidades y controlar las dependencias hacia el dominio. |
| ¿Qué problema resuelve? | Evita el acoplamiento entre la interfaz Angular, las reglas del negocio y las tecnologías externas, como bases de datos, API y servicios de pago. |
| Capas definidas | Presentación, Aplicación, Dominio e Infraestructura. |
| Beneficios | Facilita el mantenimiento y las pruebas unitarias. Permite cambiar implementaciones técnicas sin modificar innecesariamente las reglas del negocio. Mejora la organización y separación de responsabilidades del código. |

## Diagrama

```mermaid
flowchart TD

    subgraph INFRA["Infraestructura — implementaciones intercambiables"]
        direction TB
        INFRA_T["RepositorioProductosMemoria / Http · ProcesadorPagosSimulado / Niubiz · NotificadorConsola / WhatsApp"]

        subgraph ADAPT["Adaptadores de interfaz — contratos"]
            direction TB
            ADAPT_T["RepositorioProductos · RepositorioPedidos · ProcesadorPagos · NotificadorCliente"]

            subgraph APP["Aplicación — casos de uso"]
                direction TB
                APP_T["ConsultarCatalogoCasoUso · AgregarAlCarritoCasoUso · RegistrarCompraCasoUso"]

                subgraph DOM["Dominio — entidades y reglas de negocio"]
                    direction TB
                    DOM_T["Producto · Carrito · Pedido"]
                end
            end
        end
    end

    DOM_T -.->|"las dependencias apuntan hacia adentro"| APP_T
    APP_T -.-> ADAPT_T
    ADAPT_T -.-> INFRA_T

    style INFRA fill:#222,stroke:#fff,stroke-width:2px,color:#fff
    style ADAPT fill:#222,stroke:#fff,stroke-width:2px,color:#fff
    style APP fill:#222,stroke:#fff,stroke-width:2px,color:#fff
    style DOM fill:#222,stroke:#fff,stroke-width:2px,color:#fff
    style INFRA_T fill:#222,stroke:#fff,color:#fff
    style ADAPT_T fill:#222,stroke:#fff,color:#fff
    style APP_T fill:#222,stroke:#fff,color:#fff
    style DOM_T fill:#222,stroke:#fff,color:#fff
```

## Comprobación

El proyecto incluye pruebas del dominio que corren con `npm run pruebas`, sin Angular ni navegador. Que
esas pruebas compilen y pasen es la prueba de que el dominio y los casos de uso no dependen del
framework, cumpliendo la regla de dependencias de Clean Architecture.