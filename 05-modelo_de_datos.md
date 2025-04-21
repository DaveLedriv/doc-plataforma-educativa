# Modelo de Datos

## Descripción General

Este documento define el **modelo de datos lógico** de la plataforma educativa, sirviendo como base estructural para el
desarrollo y mantenimiento del sistema. Contempla las principales **entidades del sistema**, sus **atributos**, *
*relaciones** y **restricciones**. Este modelo está optimizado para **bases de datos relacionales** como PostgreSQL, y
puede ser extendido a soluciones NoSQL (como MongoDB) para componentes de datos más flexibles o almacenamiento de
contenido multimedia.

---

## Entidades Principales

### Usuario (`usuarios`)

Representa a los individuos registrados en la plataforma. Puede ser estudiante, profesor o administrador.

| Campo          | Tipo         | Restricciones                          | Descripción                                        |
|----------------|--------------|----------------------------------------|----------------------------------------------------|
| id             | UUID         | PK, UNIQUE, NOT NULL                   | Identificador único privado para usuarios          |
| uuid           | UUID         | UNIQUE, NOT NULL                       | Identificador único publico para usuarios          |
| nombre         | VARCHAR(100) | NOT NULL                               | Nombre completo del usuario                        |
| correo         | VARCHAR(100) | UNIQUE, NOT NULL                       | Correo electrónico, único                          |
| contraseña     | TEXT         | NOT NULL                               | Contraseña encriptada (bcrypt, Argon2 recomendado) |
| rol            | ENUM         | CHECK (`usuario`, `profesor`, `admin`) | Rol asignado al usuario                            |
| verificado     | BOOLEAN      | DEFAULT FALSE                          | Indica si el correo ha sido verificado             |
| curp           | VARCHAR(18)  | UNIQUE                                 | Clave Única de Registro de Población (opcional)    |
| fecha_creacion | TIMESTAMP    | DEFAULT CURRENT_TIMESTAMP              | Fecha de registro en la plataforma                 |

**Relaciones**:

- Un usuario puede estar inscrito en múltiples cursos.
- Un usuario puede actuar como profesor en varios cursos.
- Un usuario puede generar múltiples transacciones, certificados y asesorías.

---

### Curso (`cursos`)

Representa una unidad formativa impartida en la plataforma.

| Campo          | Tipo         | Restricciones                                    | Descripción                                 |
|----------------|--------------|--------------------------------------------------|---------------------------------------------|
| id             | UUID         | PK, UNIQUE, NOT NULL                             | Identificador único privado para los cursos |
| uuid           | UUID         | UNIQUE, NOT NULL                                 | Identificador único publico para los cursos |
| titulo         | VARCHAR(150) | NOT NULL                                         | Título del curso                            |
| descripcion    | TEXT         | NOT NULL                                         | Descripción detallada del curso             |
| nivel          | ENUM         | CHECK (`introducción`, `intermedio`, `avanzado`) | Nivel de dificultad del curso               |
| profesor_id    | UUID         | FK → `usuarios.id`, NOT NULL                     | Profesor responsable del curso              |
| publicado      | BOOLEAN      | DEFAULT FALSE                                    | Estado de publicación                       |
| fecha_creacion | TIMESTAMP    | DEFAULT CURRENT_TIMESTAMP                        | Fecha de creación del curso                 |

**Relaciones**:

- Un curso puede contener múltiples módulos.
- Un curso tiene asociado un único profesor.

---

### Módulo (`modulos`)

Estructura intermedia que organiza los contenidos de un curso.

| Campo    | Tipo         | Restricciones              | Descripción                                  |
|----------|--------------|----------------------------|----------------------------------------------|
| id       | UUID         | PK, UNIQUE, NOT NULL       | Identificador único privado para los módulos |
| uuid     | UUID         | UNIQUE, NOT NULL           | Identificador único publico para los módulos |
| titulo   | VARCHAR(100) | NOT NULL                   | Título del módulo                            |
| curso_id | UUID         | FK → `cursos.id`, NOT NULL | Curso al que pertenece                       |
| orden    | INTEGER      | NOT NULL                   | Orden secuencial dentro del curso            |

**Relaciones**:

- Un módulo contiene múltiples recursos.

---

### Recurso (`recursos`)

Material de aprendizaje vinculado a un módulo.

| Campo     | Tipo         | Restricciones                            | Descripción                                    |
|-----------|--------------|------------------------------------------|------------------------------------------------|
| id        | UUID         | PK, UNIQUE, NOT NULL                     | Identificador único privado para los recursos  |
| uuid      | UUID         | UNIQUE, NOT NULL                         | Identificador único publico para los recursos  |
| tipo      | ENUM         | CHECK (`video`, `pdf`, `test`, `enlace`) | Tipo de recurso                                |
| url       | TEXT         | NOT NULL                                 | URL del recurso (almacenado en bucket externo) |
| modulo_id | UUID         | FK → `modulos.id`, NOT NULL              | Módulo relacionado                             |
| titulo    | VARCHAR(100) | NOT NULL                                 | Título descriptivo del recurso                 |

---

### Inscripción (`inscripciones`)

Registra la participación de un usuario en un curso.

| Campo        | Tipo    | Restricciones                                 | Descripción                              |
|--------------|---------|-----------------------------------------------|------------------------------------------|
| id           | UUID    | PK, UNIQUE, NOT NULL                          | Identificador único privado              |
| uuid         | UUID    | UNIQUE, NOT NULL                              | Identificador único publico              |
| usuario_id   | UUID    | FK → `usuarios.id`, NOT NULL                  | Usuario inscrito                         |
| curso_id     | UUID    | FK → `cursos.id`, NOT NULL                    | Curso correspondiente                    |
| fecha_inicio | DATE    | DEFAULT CURRENT_DATE                          | Fecha de inicio del curso                |
| completado   | BOOLEAN | DEFAULT FALSE                                 | Si el usuario completó todos los módulos |
| nota_final   | NUMERIC | CHECK (nota_final >= 0 AND nota_final <= 100) | Calificación final del curso             |

---

### Evaluación (`evaluaciones`)

Banco de preguntas para medir el aprendizaje.

| Campo      | Tipo         | Restricciones              | Descripción                          |
|------------|--------------|----------------------------|--------------------------------------|
| id         | UUID         | PK, UNIQUE, NOT NULL       | Identificador único privado          |
| uuid       | UUID         | UNIQUE, NOT NULL           | Identificador único publico          |
| curso_id   | UUID         | FK → `cursos.id`, NOT NULL | Curso al que pertenece la evaluación |
| pregunta   | TEXT         | NOT NULL                   | Pregunta de opción múltiple          |
| opciones   | JSONB        | NOT NULL                   | Arreglo con las opciones disponibles |
| respuesta  | VARCHAR(255) | NOT NULL                   | Respuesta correcta                   |
| puntuacion | INTEGER      | CHECK (puntuacion > 0)     | Puntaje si se responde correctamente |

---

### Certificado (`certificados`)

Acreditación oficial emitida por la plataforma.

| Campo         | Tipo | Restricciones                | Descripción                              |
|---------------|------|------------------------------|------------------------------------------|
| id            | UUID | PK, UNIQUE, NOT NULL         | Identificador único privado              |
| uuid          | UUID | UNIQUE, NOT NULL             | Identificador único publico              |
| usuario_id    | UUID | FK → `usuarios.id`, NOT NULL | Usuario que obtiene el certificado       |
| curso_id      | UUID | FK → `cursos.id`, NOT NULL   | Curso asociado                           |
| fecha_emision | DATE | DEFAULT CURRENT_DATE         | Fecha de emisión                         |
| qr_validacion | TEXT | UNIQUE, NOT NULL             | URL con QR de validación del certificado |

---

### Asesoría (`asesorias`)

Sesiones personalizadas entre usuario y profesor.

| Campo       | Tipo      | Restricciones                                  | Descripción                 |
|-------------|-----------|------------------------------------------------|-----------------------------|
| id          | UUID      | PK, UNIQUE, NOT NULL                           | Identificador único privado |
| uuid        | UUID      | UNIQUE, NOT NULL                               | Identificador único publico |
| usuario_id  | UUID      | FK → `usuarios.id`, NOT NULL                   | Solicitante de la asesoría  |
| profesor_id | UUID      | FK → `usuarios.id`, NOT NULL                   | Profesor asignado           |
| fecha_hora  | TIMESTAMP | NOT NULL                                       | Fecha y hora programada     |
| duracion    | INTEGER   | CHECK (duracion > 0)                           | Duración en minutos         |
| estado      | ENUM      | CHECK (`pendiente`, `confirmada`, `cancelada`) | Estado actual de la sesión  |

---

### Transacción (`transacciones`)

Registro de pagos efectuados dentro de la plataforma.

| Campo      | Tipo          | Restricciones                             | Descripción                     |
|------------|---------------|-------------------------------------------|---------------------------------|
| id         | UUID          | PK, UNIQUE, NOT NULL                      | Identificador único privado     |
| uuid       | UUID          | UNIQUE, NOT NULL                          | Identificador único publico     |
| usuario_id | UUID          | FK → `usuarios.id`, NOT NULL              | Usuario que realiza el pago     |
| monto      | NUMERIC(10,2) | CHECK (monto > 0), NOT NULL               | Valor pagado                    |
| concepto   | TEXT          | NOT NULL                                  | Curso, asesoría u otro servicio |
| fecha      | TIMESTAMP     | DEFAULT CURRENT_TIMESTAMP                 | Fecha de la transacción         |
| estado     | ENUM          | CHECK (`exitoso`, `fallido`, `pendiente`) | Estado de la transacción        |

---

## Diagrama de Relaciones

(fer)

---

## Recomendaciones Técnicas

- **Índices sugeridos**: `usuario_id`, `curso_id`, `fecha`, `estado`, `correo` y `profesor_id` para mejorar el
  rendimiento en consultas frecuentes.
- **Seguridad**: Las contraseñas deben almacenarse con algoritmos modernos como Argon2, bcrypt o scrypt.
- **Escalabilidad**: Se recomienda mantener los contenidos multimedia en servicios externos de almacenamiento (p.ej.,
  Amazon S3, GCP Bucket), manteniendo en la base de datos solo la referencia (`url`).
- **Extensibilidad**: La entidad `evaluaciones` puede adaptarse para permitir otros tipos de evaluaciones como respuesta
  abierta o adjuntos.
- **Internacionalización**: Considerar codificación `UTF-8` y soporte multilingüe para campos como `titulo` y
  `descripcion`.

---
