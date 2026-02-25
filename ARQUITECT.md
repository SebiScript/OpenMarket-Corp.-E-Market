# Resumen de Proyecto: OpenMarket Corp. E-Market Platform

**Equipo:** Jalasoft Jala-U GA SE G3
**Fecha de revisión:** 25 de febrero de 2026

## 1. Características de la Arquitectura
El sistema se diseñó priorizando tres características fundamentales ("Top 3 Driving Characteristics") para soportar un entorno de e-commerce de alto tráfico:

* **Elasticidad:** Esencial para manejar picos masivos de tráfico sin degradar la experiencia del usuario (Requerimiento 3.1).
* **Disponibilidad:** Crítica para evitar caídas del sistema durante temporadas altas de ventas (Requerimiento 3.3).
* **Seguridad:** Enfoque en la protección de datos financieros y personales bajo el estándar PCI-DSS (Requerimiento 3.4).

> **Nota:** Se priorizó la **Elasticidad** sobre la Escalabilidad debido a la necesidad de reducir recursos automáticamente cuando la demanda baja para optimizar costos.


## 2. Estilo Arquitectónico Seleccionado
Tras evaluar opciones como Monolitos y arquitecturas basadas en eventos pura, se seleccionó:
**Microservicios complementados con comunicación Event-Driven.**

### Justificación de la elección:
* **Escalamiento horizontal independiente:** Cada servicio crece según su propia demanda.
* **Aislamiento de fallas:** Si un servicio falla, el resto del sistema sigue operativo.
* **Despliegue independiente:** Permite ciclos de CI/CD constantes sin desconectar la tienda.


## 3. Representación Visual (Modelo C4)
El documento desglosa la estructura del sistema en tres niveles:

### Nivel 1: Contexto del Sistema (C1)
Muestra a los **Clientes** y **Administradores** interactuando con el sistema OpenMarket, el cual depende de servicios externos como Pasarelas de Pago, Sistemas de Proveedores (vía SFTP/REST) y APIs de Logística.

### Nivel 2: Contenedores (C2)
Se detalla un stack tecnológico diverso y moderno:
* **Frontend:** Web y Panel Admin en **React**, Mobile en **Flutter**.
* **Gateway:** **Kong API Gateway** centraliza las peticiones.
* **Servicios:** Order Service (**Node.js**), Catalog Service (**Python**) y Payment Service (**Java**).
* **Persistencia:** Mezcla de SQL (**PostgreSQL**) y NoSQL (**MongoDB**, **Redis**, **ElasticSearch**).
* **Mensajería:** **RabbitMQ** para la comunicación asíncrona entre servicios.

### Nivel 3: Componentes del Payment Service (C3)
El servicio de pagos utiliza patrones de resiliencia como **Circuit Breaker** (vía Resilience4j) para evitar bloqueos si las APIs externas (Stripe/PayPal) fallan. También incluye un **Fraud Detection Engine** para análisis de metadatos.


##