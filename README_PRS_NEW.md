# 💼 Guía de Estándares para Microservicios

## <a name="contextos-de-referencia"></a>📑 CONTEXTOS DE REFERENCIA

### 🏢 PRS1: SIGEI (Sistema de Gestión Educativa Inicial)

**Gestión de Notas y Asistencia para Colegios Iniciales**

El proyecto **SIGEI** está diseñado para gestionar de manera integral los procesos administrativos y educativos en colegios de nivel inicial. A través de una arquitectura de microservicios escalable de forma horizontal, SIGEI facilita la administración de matrícula, gestión de aulas, control de asistencia, manejo de eventos (días festivos, incidentes como huaicos), gestión académica, atención psicológica, y registro de incidencias entre los alumnos. Además, permite una gestión eficiente de las instituciones educativas, todo con un enfoque modular que asegura un alto nivel de flexibilidad y crecimiento.

---

### 📦 PRS2: SIPREB (Sistema de Gestión de Bienes Patrimoniales)

**Control y Seguimiento de Bienes Institucionales**

El **Sistema de Gestión de Bienes Patrimonial (SIPREB)** es una solución integral para la administración del ciclo de vida de los bienes institucionales, que permite registrar, controlar y dar seguimiento a los activos desde su alta hasta su baja formal. Automatiza procesos clave como la depreciación, gestiona movimientos y transferencias entre áreas, y asegura la trazabilidad completa de cada bien mediante auditoría y control de estados. Su objetivo principal es garantizar la transparencia, el orden patrimonial y el cumplimiento normativo en la gestión de activos institucionales.

#### **🎯 Objetivos de SIPREB**

- 📍 **Trazabilidad**: Conocer la ubicación y responsable de cada bien en tiempo real.
- 🤖 **Automatización**: Eliminar cálculos manuales mediante algoritmos reactivos.
- 🔒 **Control**: Gestionar procesos de baja con resoluciones y dictámenes técnicos.
- 📊 **Transparencia**: Auditoría completa del ciclo de vida de los activos.

---



## 📋 Tabla de Contenidos

1. [Contextos de Referencia](#contextos-de-referencia)
2. [Resumen Ejecutivo](#resumen-ejecutivo)
3. [Tecnologías y Frameworks](#tecnologías-y-frameworks)
4. [Estructura del Proyecto](#estructura-del-proyecto)
5. [Estándares de Codificación](#estándares-de-codificación)
6. [Códigos de Estado HTTP](#códigos-de-estado-http)
7. [Seguridad y Autenticación](#seguridad-y-autenticación)
8. [Comunicación entre Microservicios](#comunicación-entre-microservicios)
9. [Arquitectura de los Contextos](#arquitectura-de-los-contextos)
10. [Infraestructura y Despliegue](#infraestructura-y-despliegue)

---

## <a name="resumen-ejecutivo"></a>🎯 Resumen Ejecutivo

### Arquitectura General Backend

- **Patrón**: Microservicios con Arquitectura Hexagonal (Domain-Driven Design Lite).
- **Comunicación**: HTTP/REST Reactivo (Spring WebFlux) + WebClient.
- **Seguridad**: OAuth2 Resource Server con JWT (Keycloak/Spring Security).
- **Base de Datos**: PostgreSQL (VPS) con persistencia reactiva (R2DBC).
- **Lenguaje**: Java 17 con Spring Boot 3.5.x.
- **Infraestructura**: Docker + Docker Compose para orquestación local.

---

## <a name="tecnologías-y-frameworks"></a>⚙️ Tecnologías y Frameworks

### Backend Technologies Stack

#### **Core Framework**
- **Spring Boot**: `3.5.x` (Versión estandarizada)
- **Java**: `17` (LTS)
- **Maven**: Gestor de dependencias y automatización de builds.

#### **Base de Datos y Persistencia**
```xml
<!-- PostgreSQL Reactive (R2DBC) -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-r2dbc</artifactId>
</dependency>
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>r2dbc-postgresql</artifactId>
</dependency>
```

#### **Herramientas de Desarrollo y Utilidades**
```xml
<!-- Lombok: Reducción de código boilerpate -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>

<!-- Validation: Validación de DTOs y Entidades -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

#### **Seguridad y Cloud (Integraciones)**
```xml
<!-- Cloudinary: Gestión de medios en la nube -->
<dependency>
    <groupId>com.cloudinary</groupId>
    <artifactId>cloudinary-http44</artifactId>
    <version>1.36.0</version>
</dependency>

<!-- OAuth2 Resource Server: Validación de tokens JWT -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-oauth2-resource-server</artifactId>
</dependency>
```

#### **Resiliencia y Configuración**
- **PostgreSQL / Supabase**: Conectividad mediante R2DBC (proporcionado en la sección de persistencia).
- **Resilience4j**: Manejo de fallos con Circuit Breaker.
- **Dotenv**: Gestión de variables de entorno para configuración local.

---

## <a name="estructura-del-proyecto"></a>📁 Estructura del Proyecto

La organización del código sigue los principios de la **Arquitectura Hexagonal**, dividiendo las responsabilidades en capas de Dominio, Aplicación e Infraestructura.

### 🏢 Estructura PRS1 (SIGEI)

```text
src/main/java/pe/edu/vallegrande/sigei/<modulo>/
│
├── domain/                           ← CAPA DE DOMINIO (pura, sin frameworks)
│   ├── models/                       ← Entidades y agregados
│   │   ├── Xxx.java                 ← Entidad raíz (POJO puro, SIN @Table/@Document)
│   │   └── valueobjects/            ← Enumeraciones y Value Objects
│   │       ├── XxxStatus.java       ← Enum ACTIVE/INACTIVE
│   │       └── XxxRole.java         ← Otras enumeraciones
│   ├── ports/                        ← Puertos (interfaces con prefijo I)
│   │   ├── in/                      ← Puertos de ENTRADA (casos de uso)
│   │   │   ├── ICreateXxxUseCase.java
│   │   │   ├── IGetXxxUseCase.java
│   │   │   ├── IUpdateXxxUseCase.java
│   │   │   ├── IDeleteXxxUseCase.java
│   │   │   └── IRestoreXxxUseCase.java
│   │   └── out/                     ← Puertos de SALIDA (repositorios, eventos)
│   │       ├── IXxxRepository.java  ← Interfaz pura (NO Spring Data)
│   │       └── IXxxEventPublisher.java ← Interfaz para eventos RabbitMQ
│   ├── exceptions/                   ← Excepciones de dominio
│   │   ├── DomainException.java              ← Base abstracta
│   │   ├── NotFoundException.java            ← Base para 404
│   │   ├── ConflictException.java            ← Base para 409
│   │   ├── XxxNotFoundException.java         extends NotFoundException
│   │   └── DuplicateXxxException.java        extends ConflictException
│   │
│   └── services/                     ← Servicios de dominio (opcional, lógica pura)
│       └── XxxDomainService.java
│
├── application/                      ← CAPA DE APLICACIÓN (orquestación)
│   ├── usecases/                    ← Implementación de casos de uso (1 clase = 1 caso)
│   │   ├── CreateXxxUseCaseImpl.java
│   │   ├── GetXxxUseCaseImpl.java
│   │   ├── UpdateXxxUseCaseImpl.java
│   │   ├── DeleteXxxUseCaseImpl.java
│   │   └── RestoreXxxUseCaseImpl.java
│   ├── dto/                         ← DTOs de entrada/salida
│   │   ├── common/                  ← Wrappers de respuesta API
│   │   │   ├── ApiResponse.java
│   │   │   └── ErrorResponse.java
│   │   ├── request/
│   │   │   ├── CreateXxxRequest.java
│   │   │   └── UpdateXxxRequest.java
│   │   └── response/
│   │       ├── XxxResponse.java
│   │       └── XxxDetailResponse.java
│   ├── events/                      ← Eventos de integración (RabbitMQ)
│   │   ├── XxxCreatedEvent.java     [record]
│   │   ├── XxxUpdatedEvent.java     [record]
│   │   ├── XxxDeletedEvent.java     [record]
│   │   └── XxxRestoredEvent.java    [record]
│   └── mappers/                     ← Mappers DTO ↔ Domain
│       └── XxxMapper.java
│
├── infrastructure/                   ← CAPA DE INFRAESTRUCTURA (frameworks/tecnología)
│   ├── adapters/
│   │   ├── in/
│   │   │   └── rest/               ← Adaptadores de ENTRADA
│   │   │       ├── XxxRest.java    ← Controller REST
│   │   │       └── GlobalExceptionHandler.java
│   │   └── out/
│   │       ├── persistence/        ← Adaptador de persistencia
│   │       │   └── XxxRepositoryImpl.java  ← implements IXxxRepository
│   │       ├── external/           ← Clientes HTTP a otros MS
│   │       │   └── XxxClientImpl.java
│   │       └── messaging/          ← Adaptadores de mensajería (RabbitMQ)
│   │           └── XxxEventPublisherImpl.java ← implements IXxxEventPublisher
│   ├── config/                     ← Configuración de Spring
│   │   ├── R2dbcConfig.java
│   │   ├── SecurityConfig.java
│   │   ├── RabbitMQConfig.java
│   │   └── WebClientConfig.java
│   ├── persistence/                ← Entidades y repos de BD (separados del adapter)
│   │   ├── entities/
│   │   │   └── XxxEntity.java      ← @Table("xxx") — entidad R2DBC
│   │   └── repositories/
│   │       └── XxxR2dbcRepository.java ← extends ReactiveCrudRepository
│   └── security/                   ← Seguridad (opcional)
│       └── SecurityContextAdapter.java
│
└── XxxApplication.java              ← @SpringBootApplication

src/main/resources/
├── application.yml                  ← Configuración base
├── db/migration/
│   ├── V1__create_xxx_table.sql
│   └── V2__add_xxx_indexes.sql

src/test/java/pe/edu/vallegrande/sigei/<modulo>/
├── domain/models/                   ← Tests unitarios del dominio
│   └── XxxTest.java
├── application/usecases/            ← Tests de casos de uso
│   └── CreateXxxUseCaseImplTest.java
└── infrastructure/adapters/in/rest/ ← Tests de integración
    └── XxxRestTest.java
```

### 📦 Estructura PRS2 (SIPREB)

```text
vg-ms-{service}/
├── 📄 pom.xml                            # Configuración Maven
├── 📄 Dockerfile                         # Imagen Docker
├── 📄 README.md                          # Documentación del servicio
├── 📄 .env.example                       # Variables de entorno ejemplo
├── 📁 src/
    ├── 📁 main/
    │   ├── 📁 java/pe/edu/vallegrande/{service}/
    │   │   ├── 📄 {Service}Application.java           # Clase principal
    │   │   │
    │   │   ├── 📁 domain/                             # 🎯 CAPA DE DOMINIO
    │   │   │   ├── 📁 model/                          # Entidades de dominio
    │   │   │   ├── 📁 exception/                      # Excepciones de dominio
    │   │   │   └── 📁 port/                           # Puertos (interfaces)
    │   │   │       ├── 📁 in/                         # Puertos de entrada
    │   │   │       └── 📁 out/                        # Puertos de salida
    │   │   │
    │   │   ├── 📁 application/                        # ⚙️ CAPA DE APLICACIÓN
    │   │   │   ├── 📁 dto/                            # Data Transfer Objects
    │   │   │   ├── 📁 service/                        # Implementación de casos de uso
    │   │   │   └── 📁 mapper/                         # Mappers
    │   │   │
    │   │   └── 📁 infrastructure/                     # 🔧 CAPA DE INFRAESTRUCTURA
    │   │       ├── 📁 adapters/
    │   │       │   ├── 📁 input/rest/                 # Controladores REST
    │   │       │   └── 📁 output/
    │   │       │       ├── 📁 persistence/            # Adaptadores de BD y Entidades
    │   │       │       └── 📁 client/                 # Clientes externos
    │   │       ├── 📁 config/                         # Configuración Spring
    │   │       ├── 📁 security/                       # Seguridad propia
    │   │       └── 📁 exception/                      # Manejo global de errores
    │   │
    │   └── 📁 resources/
    │       ├── 📄 application.yml                     # Configuración principal
    │       └── 📁 db/                                 # Scripts DDL/DML
    │
    └── 📁 test/                                       # Tests unitarios e integración
```

---

## <a name="estándares-de-codificación"></a>⚖️ Estándares de Codificación

### **Anotaciones de Calidad**
- `@Data`, `@Builder`, `@NoArgsConstructor`, `@AllArgsConstructor`: Mediante **Lombok** para reducir código repetitivo.
- `@Slf4j`: Para logging estandarizado.
- `@Valid`: Validación de DTOs en la capa de entrada.

### **Controladores Reactivos**
```java
@RestController
@RequestMapping("/api/v1/{context}")
public class AssetRestController {
    @PostMapping
    public Mono<ResponseEntity<AssetResponse>> create(@Valid @RequestBody AssetRequest request) {
        return useCase.execute(request)
            .map(res -> ResponseEntity.status(HttpStatus.CREATED).body(res));
    }
}
```

---

## <a name="códigos-de-estado-http"></a>🚥 Códigos de Estado HTTP

El sistema utiliza los siguientes códigos de estado estándar para las respuestas de la API:

| Código | Estado | Descripción |
| :--- | :--- | :--- |
| **200** | `OK` | La solicitud se procesó correctamente y se devuelve la información solicitada. |
| **201** | `Created` | El recurso ha sido creado exitosamente. |
| **204** | `No Content` | La solicitud se procesó con éxito pero no devuelve contenido. |
| **400** | `Bad Request` | La solicitud es inválida o contiene errores de validación. |
| **401** | `Unauthorized` | No se proporcionaron credenciales válidas o el token ha expirado. |
| **403** | `Forbidden` | El usuario no tiene permisos suficientes para realizar la acción. |
| **404** | `Not Found` | El recurso solicitado no pudo ser encontrado en el sistema. |
| **500** | `Internal Server Error` | Se produjo un error inesperado en el servidor. |

---

## <a name="seguridad-y-autenticación"></a>🔐 Seguridad y Autenticación

Los microservicios implementan seguridad basada en **OAuth2 Resource Server** para proteger los recursos de cada contexto.

- **Mecanismo**: Validación de tokens JWT emitidos por Keycloak.
- **Filtros**: Interceptores de seguridad para propagar el contexto de autenticación en llamadas internas.
- **Configuración**:
```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: ${KEYCLOAK_ISSUER_URI}
          jwk-set-uri: ${KEYCLOAK_JWK_SET_URI}
```

---

## <a name="comunicación-entre-microservicios"></a>🔄 Comunicación entre Microservicios

Se utiliza comunicación síncrona reactiva para interactuar con otros servicios del ecosistema (como Users Management o Configuration Service).

- **Herramienta**: `WebClient` de Spring WebFlux.
- **Propagación**: Los tokens JWT son propagados automáticamente en los headers de las peticiones salientes para mantener la identidad del usuario a través de la red.

---

## <a name="arquitectura-de-los-contextos"></a>🏗️ Arquitectura de los Contextos

Se aplica **Arquitectura Hexagonal (Puertos y Adaptadores)** para desacoplar el núcleo del negocio de los detalles técnicos.

```mermaid
graph TD
    subgraph Infrastructure_Layer ["🔧 Capa de Infraestructura"]
        REST["REST Adapters (WebFlux)"]
        Persistence["R2DBC Adapters (PostgreSQL)"]
        External["External Clients (Cloudinary, Auth)"]
    end

    subgraph Application_Layer ["⚙️ Capa de Aplicación"]
        UseCases["Casos de Uso (Service Implementation)"]
        Ports_Input["Puertos de Entrada (Interfaces)"]
        Ports_Output["Puertos de Salida (Interfaces)"]
    end

    subgraph Domain_Layer ["🎯 Capa de Dominio"]
        Models["Modelos de Dominio (Entities)"]
        Exceptions["Excepciones de Negocio"]
    end

    REST --> Ports_Input
    Ports_Input --> UseCases
    UseCases --> Models
    UseCases --> Ports_Output
    Ports_Output --> Persistence
    Ports_Output --> External
```

---

## <a name="infraestructura-y-despliegue"></a>🐳 Infraestructura y Despliegue

Se implementan Dockerfiles multi-stage para optimizar el tamaño de las imágenes finales (< 250 MiB).

```dockerfile
# Stage 1: Build
FROM maven:3.9.0-eclipse-temurin-17-alpine AS builder
COPY . .
RUN mvn clean package -DskipTests

# Stage 2: Runtime
FROM eclipse-temurin:17-jre-alpine
COPY --from=builder /app/target/*.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```


> **Nota**: Este documento ha sido generado siguiendo los estándares unificados de documentación de microservicios de Valle Grande.
