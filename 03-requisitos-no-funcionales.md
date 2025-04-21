# Requisitos No Funcionales

## Descripción General

Este documento especifica los requisitos no funcionales de la plataforma educativa. Estos requisitos definen las propiedades de calidad esperadas del sistema y su comportamiento operativo bajo condiciones normales y excepcionales.

---

## Categorías

| Código | Categoría          | Descripción                                       |
|--------|--------------------|---------------------------------------------------|
| RNF-A  | Disponibilidad      | Tiempo operativo esperado del sistema             |
| RNF-B  | Rendimiento         | Tiempo de respuesta, concurrencia, eficiencia     |
| RNF-C  | Escalabilidad       | Capacidad de crecimiento ante mayor demanda       |
| RNF-D  | Seguridad           | Protección contra accesos no autorizados y ataques |
| RNF-E  | Usabilidad          | Facilidad de uso, navegación, accesibilidad       |
| RNF-F  | Compatibilidad      | Navegadores, dispositivos, entornos soportados    |
| RNF-G  | Mantenibilidad      | Facilidad de modificación y extensión del sistema |
| RNF-H  | Portabilidad        | Facilidad de desplegar en diferentes entornos     |
| RNF-I  | Recuperación        | Comportamiento ante fallos y capacidad de recuperación |
| RNF-J  | Legal               | Cumplimiento de normativas y legislación vigente  |

---

## Requisitos No Funcionales

### RNF-A – Disponibilidad

- RNF-A1: El sistema debe estar disponible al menos el 99.9% del tiempo mensual.
- RNF-A2: El sistema debe contar con mecanismos de failover automático ante caídas de servicios clave.
- RNF-A3: Se deben emplear balanceadores de carga para distribuir tráfico entre instancias de servicio.
- RNF-A4: Las ventanas de mantenimiento deben programarse fuera del horario de alto tráfico (00:00 - 06:00 GMT-6).

### RNF-B – Rendimiento

- RNF-B1: El API Gateway debe responder en menos de 300 ms para el 95% de las peticiones.
- RNF-B2: Cada microservicio debe poder atender al menos 500 peticiones concurrentes sin degradación significativa.
- RNF-B3: Las operaciones de autenticación no deben exceder los 200 ms de respuesta bajo carga normal.
- RNF-B4: Las consultas a base de datos deben tener índices optimizados con tiempos de respuesta menores a 100 ms en promedio.

### RNF-C – Escalabilidad

- RNF-C1: El sistema debe poder escalar horizontalmente cada microservicio mediante contenedores Docker.
- RNF-C2: Debe ser posible escalar los servicios de forma independiente usando Kubernetes.
- RNF-C3: El sistema debe escalar automáticamente (auto-scaling) basado en CPU, memoria o número de peticiones por segundo.
- RNF-C4: Las nuevas instancias deben integrarse al sistema sin necesidad de reiniciar otros servicios.

### RNF-D – Seguridad

- RNF-D1: Todas las comunicaciones deben realizarse sobre HTTPS con soporte para TLS 1.2 o superior.
- RNF-D2: La autenticación debe ser gestionada con JWT firmados, con expiración configurable.
- RNF-D3: El sistema debe implementar roles y permisos definidos para usuarios, profesores y administradores.
- RNF-D4: Se debe mitigar ataques comunes (CSRF, XSS, brute force, inyección) usando Spring Security.
- RNF-D5: Se debe implementar autenticación de dos factores (2FA) opcional para usuarios y obligatoria para administradores.
- RNF-D6: Los datos sensibles (contraseñas, tokens, información de pago, datos personales identificables) deben almacenarse cifrados en reposo y transmitirse cifrados en tránsito, siguiendo estándares como AES-256 para almacenamiento y TLS 1.2+ para transmisión.
- RNF-D7: Todo acceso a datos sensibles debe estar auditado y registrado con timestamp, IP y acción realizada.

### RNF-E – Usabilidad

- RNF-E1: La interfaz debe estar diseñada bajo principios de UX y accesibilidad básica (WCAG nivel AA).
- RNF-E2: Los flujos de usuario (registro, inscripción, navegación) deben poder realizarse en 3 pasos o menos.
- RNF-E3: El diseño debe ser responsivo en todas las plataformas móviles y web.
- RNF-E4: La interfaz debe tener soporte para modo oscuro y claro configurable por el usuario.
- RNF-E5: Debe ofrecer soporte multilingüe (mínimo español e inglés), con posibilidad de extenderse.

### RNF-F – Compatibilidad

- RNF-F1: El frontend debe ser funcional en las dos últimas versiones de Chrome, Firefox, Edge y Safari.
- RNF-F2: La app móvil debe soportar Android 10+ y iOS 13+ mediante Flutter.
- RNF-F3: La API debe mantener consistencia en versiones (v1, v2) y en sus respuestas.
- RNF-F4: La aplicación debe comportarse correctamente en pantallas desde 320px (smartphones) hasta 1920px (escritorio).
- RNF-F5: Los cursos deben poder visualizarse sin problemas desde navegadores con soporte limitado (gracia progresiva).

### RNF-G – Mantenibilidad

- RNF-G1: El sistema debe seguir principios de diseño limpio (clean architecture) y separación de capas.
- RNF-G2: Cada microservicio debe estar documentado mediante Swagger/OpenAPI.
- RNF-G3: El sistema debe soportar pruebas automatizadas con al menos 80% de cobertura en cada módulo.
- RNF-G4: Las reglas de linting y estilo deben estar automatizadas y aplicarse en cada commit (CI/CD).
- RNF-G5: Debe mantenerse documentación técnica y funcional versionada en un repositorio accesible al equipo.

### RNF-H – Portabilidad

- RNF-H1: El sistema debe poder desplegarse en cualquier entorno Docker compatible.
- RNF-H2: El clúster PostgreSQL debe ser desplegable tanto en entornos cloud como locales.
- RNF-H3: El API Gateway debe permitir configuración externa a través de archivos `.yaml` o `.properties`.
- RNF-H4: Todos los entornos deben poder replicarse localmente mediante `docker-compose`.

### RNF-I – Recuperación ante fallos

- RNF-I1: Debe existir monitoreo activo para detectar caídas en menos de 30 segundos.
- RNF-I2: En caso de fallo, el sistema debe recuperarse automáticamente en menos de 5 minutos (RTO).
- RNF-I3: Se deben realizar respaldos automáticos de la base de datos al menos una vez al día.
- RNF-I4: Las copias de respaldo deben probarse al menos una vez al mes en un entorno aislado.
- RNF-I5: Los errores críticos deben generar alertas en tiempo real vía Slack o correo al equipo de soporte.

### RNF-J – Legalidad y normativas

- RNF-J1: El sistema debe registrar la aceptación de términos y condiciones por parte del usuario.
- RNF-J2: El sistema debe implementar y documentar medidas de protección de datos alineadas con las obligaciones de seguridad establecidas por la LFPDPPP y GDPR, incluyendo cifrado, minimización de datos y control de acceso.- RNF-J3: Las transacciones deben ser registradas conforme a normativas fiscales aplicables.
- RNF-J4: Debe existir trazabilidad del consentimiento de marketing (opt-in/out) de cada usuario.
- RNF-J5: La plataforma debe permitir a los usuarios solicitar la eliminación completa de su cuenta y datos asociados ("derecho al olvido").

---

## Observaciones

- Estos requisitos deben ser validados durante el ciclo de pruebas funcionales y no funcionales.
- La observabilidad debe implementarse mediante logs estructurados, health endpoints y métricas.
- Las decisiones arquitectónicas y de calidad deben ser revisadas en cada iteración de desarrollo.

