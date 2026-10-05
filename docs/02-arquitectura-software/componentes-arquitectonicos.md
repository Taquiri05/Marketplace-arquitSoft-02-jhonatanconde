# Componentes arquitectónicos

## Diagrama de componentes

```mermaid
flowchart TD

    subgraph ACTORES["ACTORES"]
        Cliente["Cliente"]
        Seller["Seller"]
        Admin["Administrador"]
    end

    subgraph WEB["APLICACIÓN WEB"]
        AppWeb["SPA — interfaz para clientes, sellers y administrador"]
    end

    subgraph API["API MARKETPLACE (monolito modular en capas)"]
        APIREST["API REST<br/>rutas /api, autenticación y validación de entrada"]
        Usuarios["Usuarios<br/>registro, autenticación y roles"]
        Sellers["Sellers<br/>gestión de vendedores"]
        Catalogo["Catálogo<br/>productos, precio y stock"]
        Carrito["Carrito<br/>ítems y totales"]
        Pedidos["Pedidos<br/>checkout, estado e integraciones"]
    end

    subgraph DATOS["DATOS"]
        BD["Base de datos"]
    end

    subgraph EXTERNOS["SISTEMAS EXTERNOS"]
        Pago["Pasarela de pago"]
        ERP["ERP"]
        Envio["Servicio de envío"]
        Facturacion["Servicio de Facturación"]
    end

    Cliente --> AppWeb
    Seller --> AppWeb
    Admin --> AppWeb
    AppWeb -->|"HTTPS/JSON"| APIREST

    APIREST --> Usuarios
    APIREST --> Sellers
    APIREST --> Catalogo
    APIREST --> Carrito
    APIREST --> Pedidos

    Usuarios ~~~ Sellers
    Sellers ~~~ Catalogo
    Catalogo ~~~ Carrito
    Carrito ~~~ Pedidos

    Sellers -.->|"valida cuenta"| Usuarios
    Catalogo -.->|"asocia a seller"| Sellers
    Carrito -.->|"precio y stock"| Catalogo
    Pedidos -.->|"convierte en pedido"| Carrito

    Usuarios --> BD
    Sellers --> BD
    Catalogo --> BD
    Carrito --> BD
    Pedidos --> BD

    Pedidos -->|"HTTPS/REST"| Pago
    Pedidos -->|"HTTPS/REST"| ERP
    Pedidos -->|"HTTPS/REST"| Envio
    Pedidos -->|"HTTPS/REST"| Facturacion

    style ACTORES fill:#222,stroke:#fff,stroke-width:2px,color:#fff
    style WEB fill:#222,stroke:#fff,stroke-width:2px,color:#fff
    style API fill:#222,stroke:#fff,stroke-width:2px,color:#fff
    style DATOS fill:#222,stroke:#fff,stroke-width:2px,color:#fff
    style EXTERNOS fill:#222,stroke:#fff,stroke-width:2px,color:#fff

    style Cliente fill:#222,stroke:#fff,color:#fff
    style Seller fill:#222,stroke:#fff,color:#fff
    style Admin fill:#222,stroke:#fff,color:#fff
    style AppWeb fill:#222,stroke:#fff,color:#fff
    style APIREST fill:#222,stroke:#fff,color:#fff
    style Usuarios fill:#222,stroke:#fff,color:#fff
    style Sellers fill:#222,stroke:#fff,color:#fff
    style Catalogo fill:#222,stroke:#fff,color:#fff
    style Carrito fill:#222,stroke:#fff,color:#fff
    style Pedidos fill:#222,stroke:#fff,color:#fff
    style BD fill:#222,stroke:#fff,color:#fff
    style Pago fill:#222,stroke:#fff,color:#fff
    style ERP fill:#222,stroke:#fff,color:#fff
    style Envio fill:#222,stroke:#fff,color:#fff
    style Facturacion fill:#222,stroke:#fff,color:#fff
```

## Descripción

El contenedor **API Marketplace** (monolito modular en capas) expone una **API REST** que distribuye las peticiones hacia sus módulos internos: **Usuarios**, **Sellers**, **Catálogo**, **Carrito** y **Pedidos**. Cada módulo lee y escribe sus propias tablas en la **base de datos**. El módulo **Pedidos** es el único que se integra con los sistemas externos: **pasarela de pago**, **ERP**, **servicio de envío** y **servicio de Facturación**.