# 🏛️ Plataforma Bancaria Digital Banco BP — Arquitectura Cloud en Azure

[![Azure Well-Architected](https://img.shields.io/badge/Architecture-Well--Architected%20Framework-0078D4?logo=microsoftazure)](https://learn.microsoft.com/azure/well-architected/)
[![C4 Model Standard](https://img.shields.io/badge/Modeling-C4%20Standard-blue)](https://c4model.com/)
[![Security Standards](https://img.shields.io/badge/Compliance-PCI--DSS%20%7C%20GDPR%20%7C%20WORM-success)](https://www.pcisecuritystandards.org/)

Propuesta formal de arquitectura de nivel Enterprise para la modernización de los canales digitales de **Banco BP**, migrando de una plataforma monolítica hacia un ecosistema desacoplado, resiliente y nativo de nube sobre **Microsoft Azure**, integrando de manera segura el Core Bancario Legacy on-premises.

---

## 📄 Documento Oficial de Entrega
El diseño técnico detallado, los diagramas C4 y las matrices completas de disponibilidad se encuentran consolidados en el informe técnico:

👉 **[Descargar PDF Oficial: BancoBP_Solucion_Arquitectura_Cloud.pdf](./BancoBP_Solucion_Arquitectura_Cloud.pdf)**

---

## 📌 Resumen de Decisiones Arquitectónicas (ADRs Clave)

* **Desacoplamiento de Canales (API Gateway + BFF):** Despliegue de Azure API Management y pods especializados (Mobile BFF en Flutter y Web BFF en React SPA) para optimizar payloads y aislar los contratos del Core Bancario.
* **Caché Multi-Nivel para Clientes Frecuentes:** Patrón híbrido *Cache-Aside* con Azure Cache for Redis en memoria (< 2 ms) y persistencia en Azure Cosmos DB, reduciendo en más del 80% la saturación sobre el Core Legacy.
* **Resiliencia en Transferencias (Saga Pattern Orquestada):** Máquina de estados sin bloqueos distribuidos (2PC Free) que ejecuta transacciones compensatorias automáticas ante caídas de la red interbancaria (ACH/SPEI).
* **Auditoría Inmutable y No Repudio (WORM):** Patrón *Transactional Outbox* con Azure Service Bus y volcado inmutable en Azure Blob Storage bajo directiva de retención legal (WORM) por 7 a 10 años.
* **Red Hub-and-Spoke y Confianza Cero (Zero Trust):** Aislamiento de cargas en VNet privada (AKS), inspección perimetral con Azure Firewall Premium L7, Private Endpoints para servicios PaaS y gestión de identidades efímeras vía Azure Workload Identity y Key Vault Managed HSM.

---

## 🛡️ Métricas Comprometidas de Resiliencia

| Métrica | Nivel Comprometido | Mecanismo de Implementación |
|---|---|---|
| **SLA Global** | 99.99% | Despliegue multi-zona (3 AZs) en AKS y Azure SQL Hyperscale ZRS. |
| **RTO (Disaster Recovery)** | < 15 minutos | Conmutación por Azure Front Door Premium hacia región secundaria. |
| **RPO (Transaccional)** | 0 (Cero pérdida) | Sincronización síncrona multi-zona para datos financieros críticos. |
| **Latencia de Consulta** | < 2 ms | Azure Cache for Redis Enterprise en memoria. |

---

## 📐 Vistas de Arquitectura Desarrolladas (Modelo C4)
1. **Nivel 1 (Contexto del Sistema):** Interacción entre el cliente, la banca digital, Core BP, IdP OAuth 2.0 y cámara ACH.
2. **Nivel 2 (Contenedores):** Despliegue de Front Door, APIM, VNet AKS (BFFs y Microservicios), Service Bus y bases de datos.
3. **Nivel 3 (Componentes):** Descomposición interna del Microservicio de Transferencias (Controllers, Idempotency, Saga Orchestrator y Adaptadores).
4. **Infraestructura Cloud:** Topología Hub-and-Spoke y modelo de seguridad Zero Trust.
