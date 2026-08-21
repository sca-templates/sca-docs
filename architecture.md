# Documento de Arquitectura de Plataforma

## 1. Introducción

El presente documento describe la arquitectura tecnológica objetivo para una plataforma de microservicios contenerizados, diseñada para operar de forma portátil en cualquier proveedor de nube pública (AWS, Azure, Google Cloud) o en infraestructura propia. La solución prioriza la confiabilidad, el rendimiento y la optimización de costos, evitando la dependencia de servicios propietarios mediante el uso de operadores Kubernetes de código abierto.

## 2. Principios de Diseño

- **Portabilidad multi-nube**: Todos los componentes críticos se ejecutan sobre Kubernetes con operadores open source, lo que permite migrar entre proveedores sin modificar las aplicaciones.
- **Confiabilidad**: Se implementan mecanismos de alta disponibilidad, auto-reparación, réplicas y respaldos automatizados.
- **Seguridad integral**: Gestión centralizada de secretos, identidad de servicios, cifrado en tránsito y autenticación de usuarios finales.
- **Observabilidad completa**: Métricas, logs y trazas distribuidas integradas en una única plataforma de visualización.
- **Eficiencia operativa**: Despliegues automatizados mediante GitOps y Trunk-Based Development, reduciendo errores manuales y acelerando la entrega de software.
- **Costo optimizado**: Autoalojamiento en Kubernetes para reducir costos directos, con opción de migrar a servicios gestionados cuando el costo operativo lo justifique.
- **Gestión por entornos**: Separación clara de entornos de desarrollo, pruebas y producción mediante configuración declarativa y versionada.

## 3. Stack Tecnológico

| Componente                      | Herramienta                                        | Justificación                                                                                     |
| ------------------------------- | -------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| **Orquestación**                | Kubernetes (distribución gestionada o autoalojada) | Estándar de facto para contenedores; portable entre nubes.                                        |
| **Descubrimiento de servicios** | Kubernetes DNS nativo                              | Resolución interna dentro del clúster.                                                            |
| **Service Mesh**                | Linkerd                                            | mTLS, reintentos, timeouts, telemetría de red; simple y portable.                                 |
| **API Gateway**                 | Kong Ingress Controller                            | Enrutamiento, rate limiting, autenticación en el borde; extensible mediante plugins.              |
| **Gestión de secretos**         | HashiCorp Vault (HA con Raft)                      | Fuente de verdad para secretos, secretos dinámicos, rotación automática.                          |
| **Sincronización de secretos**  | External Secrets Operator                          | Sincroniza secretos desde Vault hacia Secrets nativos de Kubernetes.                              |
| **Empaquetado de despliegues**  | Helm                                               | Plantillas parametrizables para entornos y servicios; integrado con ArgoCD.                       |
| **Mensajería / Eventos**        | Apache Kafka (Strimzi Operator)                    | Plataforma de streaming autoalojada, gestionada declarativamente dentro de Kubernetes.            |
| **CDC / Patrón Outbox**         | Debezium (Kafka Connect)                           | Captura cambios en PostgreSQL y los publica en Kafka; garantiza consistencia en eventos.          |
| **Caché / Pub-Sub**             | Redis (Redis Operator con Sentinel)                | Caché de alto rendimiento con alta disponibilidad.                                                |
| **Base de Datos**               | PostgreSQL (CloudNativePG)                         | Base de datos relacional autoalojada con replicación lógica, failover y PITR.                     |
| **Backup PostgreSQL**           | Barman (integrado con CloudNativePG)               | Respaldos físicos y Point-In-Time Recovery; almacenamiento en object storage compatible.          |
| **Métricas**                    | Prometheus + Grafana                               | Monitoreo y visualización estándar de la industria.                                               |
| **Logs**                        | Loki + Promtail / OpenTelemetry Collector          | Centralización de logs integrada con Grafana.                                                     |
| **Tracing**                     | Tempo + OpenTelemetry SDKs                         | Trazas distribuidas para diagnóstico de latencia y errores.                                       |
| **Feature Flags**               | Unleash (autoalojado en Kubernetes)                | Gestión de funcionalidades por entorno sin cambiar código; portable.                              |
| **Autenticación**               | Keycloak                                           | Identity Provider self-hosted compatible con OIDC/JWT, evita dependencia de proveedores externos. |
| **GitOps / Despliegue**         | ArgoCD                                             | Despliegues declarativos desde repositorios Git; auditoría y rollback simplificado.               |
| **CI/CD**                       | GitHub Actions + GitHub Container Registry         | Integración continua, pruebas y publicación de imágenes Docker.                                   |
| **Estrategia de ramas**         | Trunk-Based Development                            | Integración frecuente a la rama principal; despliegues rápidos y controlados.                     |
| **Backup de Kubernetes**        | Velero                                             | Respaldos de recursos y volúmenes persistentes a almacenamiento compatible.                       |

## 4. Organización de Repositorios y Entornos

- Los microservicios se mantienen en repositorios independientes dentro de una organización GitHub.
- Existe un repositorio central denominado `kubernetes-config` que contiene todos los manifiestos y charts de Helm, organizados por entorno y servicio.
- Se contemplan tres entornos: **desarrollo (dev)**, **control de calidad (qa)** y **producción (prod)**. Cada entorno es un espejo de la configuración base con diferencias en réplicas, recursos, secretos y feature flags.
- ArgoCD monitorea este repositorio y aplica los cambios declarados automáticamente a cada entorno.

**Estructura del repositorio `kubernetes-config`:**

```other
kubernetes-config/
├── charts/
│   ├── microservice/
│   ├── infrastructure/
│   └── ...
├── envs/
│   ├── dev/
│   │   ├── values.yaml
│   │   └── secrets.yaml (referencias a Vault)
│   ├── qa/
│   │   ├── values.yaml
│   │   └── secrets.yaml
│   └── prod/
│       ├── values.yaml
│       └── secrets.yaml
├── argocd/
│   ├── applications-dev.yaml
│   ├── applications-qa.yaml
│   └── applications-prod.yaml
└── feature-flags/
    ├── dev.yaml
    ├── qa.yaml
    └── prod.yaml
```

Cada aplicación de ArgoCD apunta a una ruta específica (`envs/dev`, `envs/qa`, `envs/prod`) dentro del repositorio. Los valores de Helm se parametrizan por entorno.

## 5. Estrategia de Despliegue (GitOps + Trunk-Based Development + Helm)

1. Los desarrolladores trabajan en ramas cortas y las integran frecuentemente a `main`.
2. Al fusionarse en `main`, GitHub Actions ejecuta pruebas, construye la imagen Docker y la publica en GitHub Container Registry.
3. El pipeline actualiza automáticamente la etiqueta de la imagen en los charts de Helm del repositorio `kubernetes-config` mediante un Pull Request.
4. Tras la revisión y fusión del PR, ArgoCD detecta el cambio y despliega la nueva versión en el entorno correspondiente (inicialmente `dev`, luego promovido a `qa` y `prod` mediante aprobaciones).
5. Los feature flags configurados en Unleash permiten habilitar o deshabilitar funcionalidades en cada entorno sin necesidad de desplegar código adicional.

Este flujo garantiza trazabilidad completa, revisiones de cambios y despliegues reproducibles.

## 6. Seguridad

- **Identidad de servicios**: Linkerd emite identidades TLS (mTLS) para cada pod, asegurando comunicación cifrada y autorizada entre microservicios. Además, recopila métricas de tráfico y aplica políticas de reintentos y timeouts.
- **Gestión de secretos**: Vault centraliza credenciales, genera secretos dinámicos para bases de datos y proveedores, y permite rotación automática. External Secrets Operator sincroniza estos secretos con Kubernetes.
- **Autenticación de usuarios**: Keycloak emite tokens OIDC/JWT; Kong valida dichos tokens en el borde y protege las APIs.
- **Políticas de acceso**: Kubernetes RBAC controla el acceso a recursos dentro del clúster.

## 7. Observabilidad

La plataforma integra cuatro pilares de observabilidad:

- **Métricas de infraestructura y aplicación**: Prometheus recopila métricas de nodos, pods y aplicaciones.
- **Métricas de red entre servicios**: Linkerd exporta automáticamente métricas de latencia, throughput y tasa de errores por cada par de servicios. Prometheus las recolecta y Grafana las visualiza en dashboards específicos.
- **Logs**: Loki almacena logs de contenedores y aplicaciones; Promtail/OpenTelemetry Collector los envía de forma eficiente.
- **Trazas**: Tempo recibe trazas distribuidas generadas por OpenTelemetry, permitiendo correlacionar peticiones entre servicios y detectar cuellos de botella.

Esta combinación permite detectar y diagnosticar fallos rápidamente, reduciendo el tiempo medio de resolución.

## 8. Gestión de Datos y Eventos

- **PostgreSQL** se ejecuta como clúster CloudNativePG con una réplica primaria y dos réplicas de lectura. El operador gestiona el failover automático, las actualizaciones y la replicación lógica necesaria para CDC.
- **Kafka** se despliega mediante Strimzi en modo alta disponibilidad, con replicación de particiones y almacenamiento persistente.
- **Redis** opera con Sentinel, ofreciendo conmutación automática ante fallos del maestro.
- **Debezium** se instala como conector de Kafka Connect dentro de Strimzi. Captura los cambios en las tablas de PostgreSQL (por ejemplo, la tabla `outbox`) y los publica en topics de Kafka. Esto implementa el patrón **outbox**, garantizando consistencia entre la base de datos y los eventos publicados.
- **Backups**:
  - **Barman** realiza respaldos físicos de PostgreSQL y archiva WALs para Point-In-Time Recovery.
  - **Velero** respalda los recursos de Kubernetes y volúmenes persistentes.
  - Ambos almacenan en object storage compatible (S3, Azure Blob, Google Cloud Storage).

## 9. Entornos y Promoción

- **Desarrollo (`dev`)**: Entorno para integración continua, pruebas unitarias y desarrollo activo. Configuración reducida, escalado mínimo y feature flags activas para pruebas.
- **Control de calidad (`qa`)**: Réplica exacta de producción en cuanto a configuración (con datos ficticios o anonimizados). Aquí se ejecutan pruebas de integración, rendimiento y aceptación.
- **Producción (`prod`)**: Entorno estable con alta disponibilidad, escalado automático y políticas estrictas de seguridad y auditoría.

La promoción entre entornos se realiza mediante Pull Requests en el repositorio `kubernetes-config`, con aprobaciones manuales para `prod` si así se define. Los feature flags facilitan la activación gradual de funcionalidades en cada entorno.

## 10. Portabilidad y Servicios Nativos

La arquitectura está diseñada para ser independiente del proveedor de nube. Todos los componentes críticos se ejecutan en Kubernetes con operadores open source, lo que permite migrar entre AWS, Azure y GCP sin cambios en el código de las aplicaciones.

Los servicios nativos de cada nube (por ejemplo, Amazon RDS, MSK, ElastiCache, Cognito) se consideran **opcionales por proyecto**. La decisión de usarlos como principal o como respaldo (failover) se basa en:

- **Costo total** (directo + operativo)
- **Requisitos de disponibilidad**
- **Tolerancia al lock-in**
- **Complejidad operativa**

Si un servicio nativo ofrece un ahorro significativo o simplifica la operación sin comprometer la portabilidad, puede integrarse como principal para ese proyecto específico. Alternativamente, puede actuar como sitio de recuperación ante desastres mediante replicación de datos y conmutación DNS.

## 11. Optimización de Costos

- **Autoalojamiento en Kubernetes** reduce los costos directos en comparación con servicios gestionados equivalentes.
- **Escalado automático** de nodos y réplicas evita pagar por recursos ociosos.
- **Instancias spot** pueden utilizarse para cargas sin estado.
- **Apagado programado** de entornos no productivos en horarios sin tráfico.
- **Monitoreo del costo operativo**: si el esfuerzo del equipo de DevOps supera el ahorro, se migra ese componente a un servicio gestionado o se usa como failover.

## 12. Hoja de Ruta de Implementación

1. **Infraestructura base**
   - Provisionar clúster Kubernetes.
   - Instalar Linkerd, Vault, External Secrets Operator, ArgoCD y Helm.
2. **Datos y mensajería**
   - Desplegar CloudNativePG (PostgreSQL) con replicación lógica y Barman.
   - Desplegar Strimzi (Kafka) y Redis Operator.
   - Configurar Debezium para capturar cambios en la tabla `outbox`.
3. **Tráfico y seguridad**
   - Configurar Kong Ingress Controller.
   - Instalar Keycloak y conectar con Kong (OIDC/JWT).
4. **Observabilidad**
   - Implementar Prometheus, Grafana, Loki y Tempo.
   - Configurar dashboards y alertas.
5. **Feature Flags**
   - Desplegar Unleash en Kubernetes.
   - Integrar SDK de Unleash en los microservicios.
   - Definir flags por entorno en el repositorio de configuración.
6. **CI/CD y GitOps**
   - Establecer repositorios y flujo Trunk-Based Development.
   - Integrar GitHub Actions con GHCR y ArgoCD.
   - Configurar entornos `dev`, `qa` y `prod` en el repositorio `kubernetes-config`.
7. **Backups y Recuperación**
   - Configurar Velero y Barman.
   - Realizar pruebas de restauración en un clúster alterno.
8. **Evaluación de servicios nativos**
   - Por proyecto, comparar costos y confiabilidad.
   - Integrar servicios nativos como principales o failover según corresponda.

## 13. Conclusión

La arquitectura propuesta combina la flexibilidad de Kubernetes con operadores open source para ofrecer una plataforma portable, confiable y eficiente en costos. La inclusión de Helm, entornos separados, feature flags, Debezium y la telemetría de red de Linkerd fortalece la capacidad de entregar software de calidad de manera continua, con visibilidad total y capacidad de adaptación a diferentes proveedores de nube o modelos de infraestructura sin reescrituras significativas.
