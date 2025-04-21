# Arquitectura del Sistema

## Descripción General

La arquitectura de la plataforma educativa se basa en un enfoque de **microservicios desacoplados**, orientada a la
nube, escalable mediante contenedores, y con **discovery dinámico** a través de Spring Cloud Eureka. Las interfaces
están desacopladas del backend, permitiendo interacción vía API Gateway para clientes web (Por definir tecnología que se
usará para web) y móviles (Flutter).

El backend está desarrollado con **Spring Boot**, asegurado con **Spring Security** y autenticación basada en **JWT**.
La comunicación entre servicios se realiza vía REST, con enrutamiento centralizado en el API Gateway.

---

## Estilo Arquitectónico

| Elemento      | Tecnología / Patrón                                |
|---------------|----------------------------------------------------|
| Frontend Web  | Por definir tecnología para web                    |
| App Móvil     | Flutter                                            |
| API Gateway   | Spring Cloud Gateway                               |
| Discovery     | Spring Cloud Eureka                                |
| Backend       | Microservicios en Spring Boot                      |
| Seguridad     | Spring Security + JWT                              |
| Contenedores  | Docker                                             |
| Base de Datos | PostgreSQL (cluster principal), SQLite (uso local) |
| CI/CD         | Adaptable a Jenkins, GitLab o GitHub Actions       |
| Comunicación  | REST sobre HTTP con autenticación JWT              |

---

## Componentes Principales

### 1. **Interfaces de Usuario (Frontend)**

- **Por definir tecnología para web** (aplicación web SPA)
- **Flutter** (aplicación móvil multiplataforma)
- Se conectan a través del **API Gateway**
- Manejan el token JWT para mantener sesión del usuario

---

### 2. **API Gateway**

- Implementado con **Spring Cloud Gateway**
- Punto único de entrada para todos los clientes
- Encargado del routing a microservicios
- Gestiona autenticación y autorización preliminar
- Conectado al **Discovery Server (Eureka)** para resolución dinámica de servicios

---

### 3. **Discovery Server (Spring Eureka)**

- Registra todos los microservicios disponibles
- Permite balanceo de carga dinámico y descubrimiento automático
- Utilizado por API Gateway y servicios entre sí

---

### 4. **Microservicios**

| Servicio             | Descripción                                                  |
|----------------------|--------------------------------------------------------------|
| **Auth-Service**     | Login, registro, generación/validación de JWT, seguridad     |
| **User-Service**     | Gestión de perfiles, roles y usuarios registrados            |
| **External-Service** | Integración con servicios externos (pagos, correo, terceros) |

Todos los servicios están desarrollados en **Spring Boot**, expuestos como contenedores Docker y conectados al **Eureka
Discovery Server**.

---

## Seguridad

- Autenticación mediante **JWT**
- Manejo de roles: `usuario`, `profesor`, `administrador`
- Endpoints protegidos con **Spring Security**
- Canales seguros mediante HTTPS (configurable según entorno)

---

## ️ Persistencia de Datos

- **PostgreSQL**: base de datos principal, en modo clúster para tolerancia a fallos
- **SQLite**: usado en contextos específicos de almacenamiento local (por ejemplo, app móvil offline)
- Cada microservicio puede tener su propia base de datos (arquitectura **Database per Service**)

---

## Contenedores y Despliegue

- Todos los servicios están empaquetados en **contenedores Docker**
- Posible despliegue en:
    - Local: Docker Compose
    - Cloud: Kubernetes (EKS, GKE o AKS)
- El balanceador de carga puede ser implementado con NGINX Ingress Controller

---

## Comunicación entre Componentes

- Cliente web/móvil → API Gateway
- API Gateway → servicios según ruta y descubrimiento
- Servicios → PostgreSQL Cluster o integración externa
- API Gateway y servicios → **Eureka** para registro y descubrimiento

---

## Patrones y Buenas Prácticas

- **Arquitectura de microservicios desacoplados**
- **DTOs y validación de entrada/salida**
- **Documentación automática con Swagger/OpenAPI**
- **Observabilidad y trazabilidad por logs estructurados**
- **Health endpoints** en cada servicio (`/actuator/health`)
- **Control de versiones** en endpoints REST

---

## ️ Diagrama

---

## Tecnologías empleadas

- **Spring Boot**
- **Spring Cloud (Eureka, Gateway)**
- **Spring Security**
- **JWT**
- **PostgreSQL / SQLite**
- **Docker**
- **Flutter / (Por definir tecnología para web)**

---

## Observaciones

- A futuro se puede extender esta arquitectura con:
    - **Kafka o RabbitMQ** para eventos asíncronos
    - **Grafana + Prometheus** para métricas
    - **Keycloak** si se desea externalizar la autenticación

---



