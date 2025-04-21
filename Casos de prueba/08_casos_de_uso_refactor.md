# 08 - Casos de Uso

## Descripción General

Este documento describe los **casos de uso funcionales actuales** implementados en la plataforma educativa. Cada caso de
uso representa una interacción significativa entre un actor del sistema (usuario, profesor o administrador) y los
módulos funcionales. El propósito de este documento es proporcionar una especificación clara, completa y profesional que
sirva como guía para diseño, desarrollo, pruebas y mantenimiento.

---

## Formato del Caso de Uso

- **ID**: Identificador único del caso de uso
- **Nombre**: Título representativo
- **Actor principal**: Usuario / Profesor / Administrador
- **Precondiciones**: Estado necesario del sistema o del actor para iniciar el flujo
- **Flujo principal**: Secuencia detallada de pasos normales
- **Flujo alternativo**: Desviaciones previstas (errores, validaciones)
- **Postcondición**: Resultado esperado tras la ejecución exitosa
- **Microservicios involucrados**: Componentes del backend que gestionan este caso

---

## Casos de Uso Actuales

### UC-01 - Registro de Usuario

- **Actor principal**: Usuario
- **Precondiciones**:
    - El usuario no debe tener una cuenta existente con el mismo correo.
- **Flujo principal**:
    1. El usuario accede a la interfaz de registro.
    2. Ingresa nombre completo, correo, contraseña y rol deseado.
    3. El sistema valida los datos.
    4. Se crea una cuenta nueva en el sistema.
- **Flujo alternativo**:
    - Si el correo ya está registrado, se notifica al usuario.
- **Postcondición**: La cuenta queda registrada y lista para iniciar sesión.
- **Microservicios involucrados**: `auth-service`

---

### UC-02 - Inicio de Sesión

- **Actor principal**: Usuario / Profesor / Administrador
- **Precondiciones**:
    - La cuenta debe existir y estar habilitada.
- **Flujo principal**:
    1. El usuario accede al formulario de inicio de sesión.
    2. Ingresa correo, SMS, WhatsApp en el primer formulario.
    3. El sistema valida las credenciales.
    4. El usuario escribe su contraseña
    5. Se genera y devuelve un token JWT.
- **Flujo alternativo**:
    - Si la contraseña es incorrecta, se informa al usuario.
- **Postcondición**: El usuario obtiene acceso al sistema.
- **Microservicios involucrados**: `auth-service`

---

### UC-03 - Visualización del Catálogo de Cursos

- **Actor principal**: Usuario
- **Precondiciones**:
    - Estar autenticado.
- **Flujo principal**:
    1. El usuario accede al listado general de cursos.
    2. Aplica filtros por nivel, categoría u orden.
    3. Visualiza información resumida de cada curso.
- **Flujo alternativo**:
    - Si no hay cursos disponibles, se muestra un mensaje informativo.
- **Postcondición**: El usuario explora los cursos disponibles.
- **Microservicios involucrados**: `external-service`

---

### UC-04 - Inscripción a Curso

- **Actor principal**: Usuario
- **Precondiciones**:
    - Estar autenticado.
    - El curso debe estar publicado.
- **Flujo principal**:
    1. El usuario selecciona un curso.
    2. Si es gratuito, se realiza la inscripción automática.
    3. Si es de pago, se redirige al proceso de pago.
    4. Al completarse el pago, se registra la inscripción.
- **Flujo alternativo**:
    - Si el curso no está disponible, se muestra error.
- **Postcondición**: El usuario queda inscrito al curso.
- **Microservicios involucrados**: `external-service`, `user-service`, `payment-service`

---

### UC-05 - Seguimiento de Progreso del Curso

- **Actor principal**: Usuario
- **Precondiciones**:
    - Estar inscrito en el curso.
- **Flujo principal**:
    1. El usuario accede a su curso.
    2. Visualiza el avance por módulo.
    3. El sistema actualiza automáticamente el progreso.
- **Flujo alternativo**:
    - Si el curso fue eliminado, se notifica al usuario.
- **Postcondición**: El usuario puede ver su progreso actual.
- **Microservicios involucrados**: `user-service`, `external-service`

---

### UC-06 - Generación de Certificado

- **Actor principal**: Usuario
- **Precondiciones**:
    - Curso finalizado con éxito (evaluación aprobada).
- **Flujo principal**:
    1. El usuario solicita su certificado.
    2. El sistema verifica la finalización del curso.
    3. Se genera un certificado en PDF con código QR.
- **Flujo alternativo**:
    - Si el curso no está completo, se deniega la solicitud.
- **Postcondición**: El certificado queda disponible para descarga.
- **Microservicios involucrados**: `user-service`, `external-service`

---

### UC-07 - Creación de Curso por Profesor

- **Actor principal**: Profesor
- **Precondiciones**:
    - Estar validado como profesor.
- **Flujo principal**:
    1. El profesor accede al panel de creación de curso.
    2. Completa información general, módulos y recursos.
    3. Envía el curso para revisión.
- **Flujo alternativo**:
    - Si falta información obligatoria, se solicita completarla.
- **Postcondición**: El curso queda en estado "pendiente de aprobación".
- **Microservicios involucrados**: `external-service`

---

### UC-08 - Aprobación de Curso

- **Actor principal**: Administrador
- **Precondiciones**:
    - Curso creado por profesor en estado "pendiente".
- **Flujo principal**:
    1. El administrador accede al panel de revisión.
    2. Revisa contenido y recursos.
    3. Aprueba o rechaza el curso.
- **Flujo alternativo**:
    - Si se rechaza, se registra una observación.
- **Postcondición**: El curso cambia de estado a "publicado" o "rechazado".
- **Microservicios involucrados**: `external-service`

---

### UC-09 - Solicitar Asesoría

- **Actor principal**: Usuario
- **Precondiciones**:
    - Estar autenticado.
    - Tener créditos o suscripción activa.
- **Flujo principal**:
    1. El usuario consulta disponibilidad de profesores.
    2. Selecciona fecha y hora.
    3. Se agenda la sesión.
- **Flujo alternativo**:
    - Si el horario ya fue tomado, se ofrece otro.
- **Postcondición**: La asesoría queda programada.
- **Microservicios involucrados**: `external-service`

---

### UC-10 - Gestión de Usuarios

- **Actor principal**: Administrador
- **Precondiciones**:
    - Tener privilegios de administrador.
- **Flujo principal**:
    1. Accede al panel de usuarios.
    2. Visualiza y filtra usuarios.
    3. Puede editar roles, bloquear cuentas o eliminar usuarios.
- **Flujo alternativo**:
    - Si el usuario es un profesor con cursos activos, se solicita confirmación.
- **Postcondición**: Cambios aplicados a cuentas de usuario.
- **Microservicios involucrados**: `user-service`, `auth-service`

---

### UC-11 - Recuperación de Contraseña

- **Actor principal**: Usuario
- **Precondiciones**:
    - Tener una cuenta registrada.
- **Flujo principal**:
    1. El usuario accede al enlace “¿Olvidaste tu contraseña?”.
    2. Elige entre recibir un código temporal por le medio que haya elegido al registrarse (Correo, SMS, WhatsApp).
    3. El sistema envía un código de recuperación con una duración de 15 minutos.
    4. El usuario ingresa el código en el formulario.
    4. El usuario define una nueva contraseña.
- **Flujo alternativo**:
    - Si el correo, numero o WhatsApp no está registrado, se notifica al usuario.
- **Postcondición**: La contraseña es actualizada y el usuario puede iniciar sesión.
- **Microservicios involucrados**: `auth-service`, `notification-service`

---

### UC-12 - Edición de Perfil

- **Actor principal**: Usuario / Profesor
- **Precondiciones**:
    - Estar autenticado.
- **Flujo principal**:
    1. El usuario accede a su configuración de perfil.
    2. Modifica nombre, foto, bio, etc.
    3. Guarda los cambios.
- **Flujo alternativo**:
    - Si los datos no cumplen con validaciones, se muestran mensajes de error.
- **Postcondición**: El perfil del usuario se actualiza correctamente.
- **Microservicios involucrados**: `user-service`

---

### UC-13 - Cambio de Contraseña desde el Perfil

- **Actor principal**: Usuario
- **Precondiciones**:
    - Estar autenticado.
- **Flujo principal**:
    1. El usuario accede a la sección de seguridad.
    2. Ingresa su contraseña actual y una nueva.
    3. El sistema valida y guarda el nuevo valor.
- **Postcondición**: Se actualiza la contraseña del usuario.
- **Microservicios involucrados**: `auth-service`

---

### UC-14 - Revisión de Certificados Obtenidos

- **Actor principal**: Usuario
- **Precondiciones**:
    - Haber completado al menos un curso certificado.
- **Flujo principal**:
    1. El usuario ingresa al panel de certificados.
    2. Visualiza los cursos aprobados y sus fechas.
    3. Puede descargar o compartir el certificado.
- **Postcondición**: El usuario obtiene acceso a su historial de logros.
- **Microservicios involucrados**: `user-service`, `external-service`

---

### UC-15 - Consulta de Historial de Transacciones

- **Actor principal**: Usuario
- **Precondiciones**:
    - Haber realizado al menos un pago.
- **Flujo principal**:
    1. El usuario accede a la sección de facturación.
    2. Consulta las fechas, montos y conceptos de pago.
    3. Puede descargar el comprobante.
- **Postcondición**: El usuario puede visualizar su historial financiero.
- **Microservicios involucrados**: `payment-service`

---

### UC-16 - Cancelación de Cuenta

- **Actor principal**: Usuario
- **Precondiciones**:
    - Estar autenticado y no tener suscripciones activas.
- **Flujo principal**:
    1. El usuario accede a su perfil.
    2. Solicita la eliminación de su cuenta.
    3. El sistema elimina o anonimiza la información.
- **Flujo alternativo**:
    - Si tiene pagos activos, se impide la eliminación.
- **Postcondición**: La cuenta queda inhabilitada o eliminada.
- **Microservicios involucrados**: `user-service`, `auth-service`

---

### UC-17 - Visualización de Métricas por Administrador

- **Actor principal**: Administrador
- **Precondiciones**:
    - Estar autenticado como administrador.
- **Flujo principal**:
    1. Accede al panel de métricas.
    2. Visualiza gráficos de usuarios activos, ingresos, inscripciones.
- **Postcondición**: El administrador puede tomar decisiones informadas.
- **Microservicios involucrados**: `external-service`, `payment-service`

## Observaciones

- Todos los casos de uso están respaldados por requisitos funcionales definidos y alineados a los microservicios
  actuales.
- Los flujos alternativos deben estar cubiertos por pruebas de validación.
- Este documento debe actualizarse con cada nueva funcionalidad aprobada en producción.