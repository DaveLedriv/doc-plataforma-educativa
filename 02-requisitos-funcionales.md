# Requisitos Funcionales

## Descripción General

Este documento detalla los **requisitos funcionales** de la plataforma educativa orientada a cursos, asesorías y
certificaciones. La plataforma opera bajo una arquitectura de microservicios expuestos mediante un API Gateway,
accesible desde clientes web (Por definir) y móviles (Flutter). La autenticación se maneja mediante tokens JWT, y se
contemplan tres roles de usuario: `usuario`, `profesor` y `administrador`.

---

## Actores del Sistema

| Actor         | Descripción                                                                            |
|---------------|----------------------------------------------------------------------------------------|
| Usuario       | Persona interesada en aprender. Puede registrarse, tomar cursos, recibir certificados. |
| Profesor      | Profesional preparado para crear cursos, impartir asesorías y evaluar estudiantes.     |
| Administrador | Supervisor del sistema. Puede gestionar usuarios, cursos, pagos y generar reportes.    |

---

## Microservicios Involucrados

| Servicio             | Descripción funcional                                                       |
|----------------------|-----------------------------------------------------------------------------|
| **auth-service**     | Autenticación, registro, login, emisión de tokens JWT                       |
| **user-service**     | Gestión de usuarios, perfiles, roles y certificados                         |
| **external-service** | Integración con pagos, asesorías, notificaciones, y sistema de certificados |

---

## Requisitos Funcionales por Rol

### Usuario

#### RF-01 – Registro de cuenta

- **Descripción**: El usuario puede registrarse proporcionando correo, usuario, numero de telefono, contraseña, nombre
  completo y rol.
- **Validación**: El correo debe ser único. El rol por defecto será `usuario`, a menos que el administrador apruebe
  otro.
- **Servicio**: `auth-service`

#### RF-02 – Inicio de sesión

- **Descripción**: El usuario puede autenticarse ingresando cuealquiera de sus credenciales: correo, usuaario, numero de
  telefono y contraseña. Se retorna un JWT válido por sesión.
- **Servicio**: `auth-service`

#### RF-03 – Visualizar catálogo de cursos

- **Descripción**: El usuario puede navegar y filtrar cursos disponibles según categoría, nivel y popularidad.
- **Requiere token JWT**: Sí
- **Servicio**: `external-service`

#### RF-04 – Inscribirse a un curso

- **Descripción**: El usuario puede inscribirse a cursos gratuitos o pagos. Se registra la inscripción.
- **Validación**: Si el curso es de pago, se debe validar la transacción.
- **Servicios**: `external-service` (pago), `user-service` (registro)

#### RF-05 – Tomar curso y ver progreso

- **Descripción**: El usuario puede consumir lecciones, recursos, y realizar actividades. El sistema registra el
  progreso.
- **Servicio**: `external-service` (contenido), `user-service` (progreso)

#### RF-06 – Calificar y dejar reseña en un curso

- **Descripción**: El usuario puede calificar con "estrellas" y escribir una reseña después de completar un curso.
- **Beneficio**: Establece una reputación a los cursos e incentiva la calidad.
- **Servicio**: `external-service` (contenido), `user-service` (validación)

#### RF-07 – Guardar cursos en una lista de deseos

- **Descripción**: Permite a los usuarios marcar cursos de interés para inscribirse más tarde.
- **Servicio**: `user-service`

#### RF-08 – Agendar asesoría

- **Descripción**: El usuario puede ver la disponibilidad de un profesor y solicitar una asesoría.
- **Validación**: Requiere tener créditos o curso relacionado aprobado.
- **Servicio**: `external-service`

#### RF-09 – Recibir notificaciones personalizadas

- **Descripción**: El usuario recibe notificaciones de nuevos cursos, descuentos, o mensajes del profesor.
- **Servicio**: `external-service` (notificaciones)

#### RF-10 – Enviar preguntas en foros del curso

- **Descripción**: El usuario puede interactuar con otros alumnos y profesores a través de foros por curso.
- **Servicio**: `external-service`

#### RF-11 – Descargar certificado

- **Descripción**: Al finalizar un curso y aprobar evaluación, el usuario puede descargar un certificado en PDF con
  código QR de validación.
- **Servicios**: `user-service` (verificación), `external-service` (generación de PDF y QR)

---

### Profesor

#### RF-12 – Registrar cuenta cuenta como profesor

- **Descripción**: El usuario puede registrarse como profesor. Un administrador debe aprobar el contenido propuesto.
- **Servicio**: `user-service`

#### RF-13 – Crear cursos y contenidos

- **Descripción**: El profesor puede crear cursos con módulos, recursos multimedia y evaluaciones.
- **Validación**: Los cursos deben pasar por revisión del administrador antes de ser publicados.
- **Servicios**: `external-service` (contenido), `user-service` (asociación)

#### RF-14 – Definir disponibilidad de asesorías

- **Descripción**: El profesor puede establecer horarios disponibles para asesorías 1:1 o grupales.
- **Servicio**: `external-service`

#### RF-15 – Evaluar a estudiantes

- **Descripción**: El profesor puede revisar respuestas, asignar calificaciones y aprobar certificados.
- **Servicio**: `user-service`, `external-service`

#### RF-16 – Responder preguntas del foro

- **Descripción**: El profesor puede interactuar con los estudiantes respondiendo preguntas en los foros del curso.
- **Servicio**: `external-service`

#### RF-17 – Ver métricas del desempeño del curso

- **Descripción**: Puede acceder a estadísticas como tasa de finalización, calificaciones promedio, etc.
- **Servicio**: `external-service`

---

### Administrador

#### RF-12 – Gestionar usuarios

- **Descripción**: Puede listar, modificar, eliminar, aprobar cuentas de usuario o profesor.
- **Servicios**: `user-service`, `auth-service` (bloqueo de cuentas)

#### RF-12 – Moderar contenido o comentarios

- **Descripción**:  Puede ocultar o eliminar comentarios/reseñas inadecuadas en los cursos.
- **Servicios**: `user-service`, `auth-service` (bloqueo de tokens)

#### RF-13 – Revisar y aprobar cursos

- **Descripción**: Visualiza cursos creados por profesores y decide su aprobación para publicación.
- **Servicio**: `external-service`

#### RF-14 – Gestionar pagos y suscripciones

- **Descripción**: Accede al historial de transacciones, gestiona errores de cobro o devoluciones.
- **Servicio**: `external-service`

#### RF-15 – Generar reportes del sistema

- **Descripción**: Obtiene métricas de uso, ingresos, usuarios activos, cursos más demandados.
- **Servicio**: `external-service` + posible integración futura con dashboards analíticos

#### RF-15 – Auditar historial de acciones

- **Descripción**: Revisión de logs de acciones críticas (modificación de usuarios, pagos, cambios de rol, etc.).
- **Servicio**: `user-service`

---

## Observaciones

- Todos los endpoints requieren autenticación mediante JWT excepto `registro` y `login`.
- Los roles y permisos están controlados por el `auth-service` y distribuidos en el token JWT.
- Las validaciones adicionales son responsabilidad de cada microservicio (validación de horario, pagos, progreso, etc.).
- La gestión de notificaciones puede ser agregada como una función adicional del `external-service` o un nuevo
  microservicio.

---

