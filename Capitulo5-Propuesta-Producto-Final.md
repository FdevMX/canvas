# Capítulo 5. Propuesta de producto final

El presente capítulo expone la propuesta técnica y metodológica para el desarrollo del Sistema de Gestión de Programas Sociales basado en Software Libre y de Código Abierto (FLOSS), como respuesta integral al problema identificado en los capítulos anteriores. A partir del análisis contextual, la fundamentación teórica y los hallazgos del diseño metodológico, se presenta una arquitectura de solución que combina principios de gobierno abierto, soberanía tecnológica y diseño centrado en el usuario.

La propuesta se estructura en cinco componentes principales: la arquitectura tecnológica del sistema, el diseño de la interfaz de usuario basado en los mockups desarrollados, la especificación de requisitos funcionales y no funcionales, el modelo de implementación y despliegue, y finalmente, las consideraciones de seguridad y cumplimiento normativo. Cada elemento ha sido diseñado considerando las necesidades específicas identificadas durante la investigación, con especial atención a la accesibilidad para adultos mayores y la transparencia institucional.

## 5.1 Arquitectura tecnológica del sistema

La arquitectura propuesta para el Sistema de Gestión de Programas Sociales FLOSS se fundamenta en un modelo de microservicios que garantiza modularidad, escalabilidad y mantenibilidad a largo plazo. Como señala Newman (2020), esta aproximación "permite construir sistemas no como un gran bloque inflexible, sino como un ecosistema de componentes independientes y especializados" (p. 12), lo cual resulta estratégico para el contexto gubernamental mexicano donde la interoperabilidad y la soberanía tecnológica son prioridades.

### 5.1.1 Modelo arquitectónico general

El sistema se compone de tres capas principales que interactúan mediante interfaces bien definidas:

**Capa de presentación (Frontend):** Constituida por la aplicación móvil multiplataforma para beneficiarios y facilitadores, así como el portal web administrativo. Ambos componentes se desarrollarán utilizando tecnologías FLOSS que garanticen accesibilidad y usabilidad. Para la aplicación móvil se propone el uso de Flutter, un framework de código abierto desarrollado por Google que "permite crear aplicaciones nativas para Android e iOS desde una única base de código" (Flutter, 2025, párr. 1) y que según LeanCode (2025) "continúa su fuerte evolución, ofreciendo desarrolladores una plataforma cada vez más poderosa para construir aplicaciones multiplataforma" (párr. 2). En cuanto al portal administrativo web, se propone React como biblioteca JavaScript de código abierto, ampliamente adoptada en el desarrollo de interfaces de usuario modernas.

**Capa de servicios (Backend):** Implementada mediante una arquitectura de microservicios que incluye servicios especializados para autenticación y autorización, gestión de usuarios y perfiles, procesamiento de solicitudes, validación de documentos, gestión de notificaciones y auditoría de accesos. Cada microservicio operará de forma autónoma, comunicándose a través de una API Gateway que centraliza y asegura las peticiones. Para el desarrollo del backend se propone el uso de Node.js con el framework Express para la construcción de APIs RESTful, o alternativamente, Python con FastAPI, ambas opciones plenamente FLOSS y con amplio soporte comunitario.

**Capa de datos:** Conformada por un sistema gestor de bases de datos relacionales y un sistema de almacenamiento de archivos. Para la base de datos relacional se propone PostgreSQL, considerado "el sistema de base de datos relacional de código abierto más avanzado del mundo" (PostgreSQL Global Development Group, 2025, párr. 1), ofreciendo características empresariales como transacciones ACID, integridad referencial y extensibilidad. Según ARDURA Consulting (2024), PostgreSQL "ha dejado de ser solo una 'alternativa' y se ha convertido en el estándar de oro, ofreciendo funcionalidad y confiabilidad de clase empresarial en un modelo de código abierto" (párr. 3). Para el almacenamiento de documentos digitales (fotografías de credenciales, actas de nacimiento, comprobantes de domicilio) se propone MinIO, un sistema de almacenamiento de objetos de código abierto compatible con el protocolo S3 que "permite almacenamiento escalable y seguro sin depender de servicios propietarios de nube" (MinIO, 2025, párr. 2).

Esta arquitectura por capas permite la separación de responsabilidades y facilita el mantenimiento evolutivo del sistema. Como establecen Belman (2022) y Gavilánez et al. (2022), "una arquitectura definida correctamente facilita la implementación, flexibilidad, escalabilidad, robustez y adaptabilidad de nuevas soluciones" (p. 2).

### 5.1.2 Stack tecnológico FLOSS

En coherencia con el objetivo de promover la soberanía tecnológica y cumplir con las políticas nacionales establecidas en el Acuerdo publicado en el DOF (2021) para "fortalecer el uso del software libre y los estándares abiertos" (p. 7), se propone el siguiente stack tecnológico:

**Sistema operativo:** GNU/Linux en distribución Ubuntu Server LTS (Long Term Support) o Debian Stable para los servidores de backend, y GobLin para las estaciones de trabajo administrativas. Como señala el proyecto GobLin (Morales et al., 2021), esta distribución ha sido "desarrollada según la filosofía del Software Libre" y "optimizada para las necesidades de la administración pública" (p. 3).

**Contenedorización y orquestación:** Docker para la creación de contenedores que encapsulen cada microservicio con sus dependencias, y Kubernetes para la orquestación, gestión y escalamiento automático de los contenedores en producción.

**Servidor web y proxy inverso:** NGINX como servidor web de alto rendimiento y proxy inverso, que actuará también como balanceador de carga para distribuir el tráfico entre las instancias de los microservicios.

**Sistema de mensajería:** RabbitMQ o Apache Kafka para la comunicación asíncrona entre microservicios, garantizando la resiliencia del sistema ante fallos temporales de componentes individuales.

**Monitoreo y observabilidad:** Prometheus para la recolección de métricas de rendimiento, Grafana para la visualización de dashboards de monitoreo, y ELK Stack (Elasticsearch, Logstash, Kibana) para la agregación, análisis y visualización de logs del sistema.

**Control de versiones:** Git como sistema de control de versiones distribuido, con repositorios alojados en GitLab Community Edition, una plataforma FLOSS para gestión de código fuente, integración continua y despliegue continuo (CI/CD).

Este stack tecnológico ha sido seleccionado considerando los criterios de madurez tecnológica, soporte comunitario activo, seguridad, escalabilidad y cumplimiento con estándares internacionales. Como afirma Sánchez (2023), "por el bien de la soberanía tecnológica, los Estados deben hacer uso de software libre a fin de mantener el control de la información y de los procesos internos" (p. 389).

### 5.1.3 Patrones arquitectónicos aplicados

Para garantizar la calidad arquitectónica del sistema, se aplicarán los siguientes patrones de diseño:

**Patrón API Gateway:** Un único punto de entrada que centraliza las peticiones de los clientes (aplicación móvil y portal web) hacia los diferentes microservicios del backend. Este patrón "simplifica el lado del cliente, proporciona un lugar centralizado para aplicar políticas de seguridad, y facilita el monitoreo y registro de todas las interacciones" (Richardson, 2018, p. 234).

**Patrón MVVM (Model-View-ViewModel):** Para la aplicación móvil Flutter, se implementará el patrón MVVM que separa la lógica de presentación de la lógica de negocio. Como se ilustra en la Figura 2.3 del marco teórico, este patrón "facilita la prueba unitaria de los componentes y mejora la mantenibilidad del código" (Gavilánez et al., 2022, p. 13).

**Patrón Repository:** Para abstraer la lógica de acceso a datos, permitiendo que los servicios de negocio interactúen con los datos a través de una interfaz consistente, independientemente del origen de datos subyacente.

**Patrón Circuit Breaker:** Para manejar fallos en las llamadas a microservicios externos o dependencias, evitando que un fallo en cascada afecte todo el sistema. Este patrón es fundamental en arquitecturas distribuidas y mejora la resiliencia general del sistema (Nygard, 2018).

## 5.2 Diseño de interfaz de usuario

El diseño de la interfaz de usuario ha sido conceptualizado considerando los hallazgos del Capítulo 4, donde se identificó que el 75% de los encuestados "preferiría procesos simples y no repetitivos para los trámites, información clara y asistencia humana capacitada". Asimismo, se priorizó la accesibilidad visual y cognitiva, atendiendo a que el usuario primario es un adulto mayor con edad promedio de 74 años.

### 5.2.1 Principios de diseño aplicados

El diseño se rige por los siguientes principios fundamentales:

**Simplicidad y claridad:** Interfaces minimalistas que presentan solo la información esencial en cada pantalla, evitando la sobrecarga cognitiva. Como señala UX Rigo (2025), "la memoria de trabajo procesa solo 7 ± 2 elementos simultáneos, por lo que el diseño debe respetar estas limitaciones para evitar la sobrecarga cognitiva" (párr. 4).

**Alto contraste y tipografía legible:** Uso de combinaciones de color con relación de contraste mínima de 4.5:1 según las pautas WCAG 2.1 nivel AA (W3C, 2018). Tamaños de fuente ajustables con un mínimo de 16sp para texto principal y 20sp para encabezados. Tipografías sans-serif de alta legibilidad como Roboto o Open Sans.

**Navegación lineal y predecible:** Flujos de trabajo secuenciales que guían al usuario paso a paso, con indicadores visuales claros del progreso (por ejemplo, "Paso 1 de 3"). Esto reduce la ansiedad digital identificada en las entrevistas del estudio.

**Affordances y feedback visual:** Botones con tamaño mínimo de 48x48dp según las directrices de Material Design, garantizando áreas táctiles adecuadas para usuarios con limitaciones motoras. Feedback inmediato ante cada acción del usuario mediante animaciones sutiles y mensajes de confirmación.

**Lenguaje claro y humano:** Eliminación de jerga técnica y burocrática. Uso de instrucciones en segunda persona del singular (tú/usted según el contexto cultural) y mensajes de error constructivos que indican cómo resolver el problema.

### 5.2.2 Flujo de usuario y mockups principales

El diseño contempla tres flujos principales de usuario, representados en los mockups de Figma adjuntos (imágenes 1-10):

**Flujo de registro y autenticación:** La pantalla de inicio de sesión presenta un diseño limpio con los elementos esenciales: campos para CURP o usuario y contraseña, botón principal "Iniciar Sesión", enlace para recuperación de contraseña y opción de registro para nuevos usuarios. El uso del logotipo oficial del Gobierno de México refuerza la confianza institucional.

**Flujo de solicitud de pensión:** El proceso de solicitud se divide en tres pasos claramente identificados:

*Paso 1 - Datos Básicos del Beneficiario:* Formulario guiado que solicita CURP (con validación de formato), nombre completo y fecha de nacimiento mediante un selector de calendario accesible. La interfaz incluye un indicador de progreso "Paso 1 de 3" en la parte superior y un botón "Siguiente: Documentación" que indica claramente la acción subsiguiente.

*Paso 2 - Carga de Documentos:* Pantalla dedicada a la captura fotográfica o selección de documentos requeridos (INE/CURP, acta de nacimiento, comprobante de domicilio). Cada documento tiene su propia sección con un icono de cámara y un estado visual ("Cargado Correctamente" o "Pendiente"). Este diseño responde al requisito FUN-006 del SRS de "permitir la captura y carga de documentos utilizando la cámara del dispositivo móvil".

*Paso 3 - Revisión Final y Envío:* Pantalla de confirmación que presenta un resumen de los datos ingresados y documentos adjuntados, permitiendo al usuario revisar antes de enviar. Al confirmar, el sistema genera un acuse de recibo digital con un número de folio único (formato F-XXXXXXXX) que el usuario puede utilizar para dar seguimiento a su solicitud.

**Flujo de seguimiento de estatus:** Una vez enviada la solicitud, el usuario puede consultar su estatus mediante el folio asignado. La interfaz muestra un indicador visual de progreso con tres estados:

- *Recibida:* La solicitud ha sido registrada exitosamente en el sistema.
- *En Revisión:* El personal administrativo está validando la documentación. Se muestra el mensaje "Tu documentación está siendo validada. El personal administrativo está revisando la legibilidad y consistencia de los documentos cargados."
- *Aprobada o Rechazada:* Estado final con información específica. En caso de aprobación, se presentan los próximos pasos (entrega de tarjeta del bienestar, fecha del primer depósito). En caso de rechazo, se muestra el motivo oficial detallado (por ejemplo, "Falta de Legibilidad del Documento de Identificación (INE). La imagen cargada de la Credencial para Votar es borrosa y no permite la validación de la CURP").

Este diseño de flujos atiende directamente las recomendaciones de los participantes identificadas en la Tabla 4.2 del diseño metodológico, particularmente en las categorías de "Tutorías", "Soporte humano" y "Simplificación de interfaz".

### 5.2.3 Portal administrativo web

Para el personal administrativo de la Secretaría de Bienestar, se ha diseñado un portal web con las siguientes funcionalidades principales:

**Bandeja de solicitudes pendientes:** Vista tabular que presenta todas las solicitudes en estatus "En Revisión", mostrando folio, nombre del beneficiario, fecha de recepción y acciones disponibles (Revisar). Esta interfaz permite al personal priorizar y gestionar eficientemente el volumen de solicitudes.

**Módulo de revisión de solicitudes:** Interfaz detallada que presenta:

- Datos del beneficiario en un panel lateral (nombre, CURP, facilitador si aplica, estatus actual).
- Visor de documentos activo que permite visualizar cada documento cargado en tamaño completo, con controles para cambiar entre INE, Acta de Nacimiento y Comprobante de Domicilio mediante pestañas.
- Panel de resolución con dos botones de acción claramente diferenciados: "Aprobar" (color verde) y "Rechazar" (color rojo). En caso de rechazo, se despliega un campo obligatorio para ingresar el motivo oficial.
- Registro de auditoría que muestra un log inmutable de todas las acciones realizadas sobre la solicitud (creación, carga de documentos, consultas por personal administrativo).

Este diseño cumple con los requisitos FUN-011 a FUN-014 del SRS y facilita la trazabilidad completa del proceso, un aspecto fundamental para la transparencia gubernamental.

## 5.3 Especificación de requisitos del sistema

La Especificación de Requisitos de Software (SRS) detallada se encuentra en el documento adjunto "DOC-20251115-WA0235-2.docx" (SRS Versión 1.1). A continuación se presenta un resumen estructurado de los requisitos más críticos:

### 5.3.1 Requisitos funcionales principales

Los requisitos funcionales definen las capacidades que el sistema debe proporcionar a sus usuarios. Estos han sido organizados en cinco módulos principales:

**Gestión de cuentas de usuario (FUN-001 a FUN-004):** El sistema permitirá el registro y autenticación de tres tipos de usuarios: facilitadores, beneficiarios y personal administrativo. Se implementará un mecanismo de recuperación de contraseña seguro y la capacidad para que un facilitador gestione múltiples beneficiarios desde una única cuenta, respondiendo a la realidad identificada en el estudio de que los adultos mayores frecuentemente dependen de familiares para realizar trámites digitales.

**Proceso de solicitud (FUN-005 a FUN-008):** El sistema guiará al usuario paso a paso en el llenado del formulario mediante un asistente intuitivo. Permitirá la captura de documentos utilizando la cámara del dispositivo móvil, con validaciones básicas de formato (por ejemplo, validación del formato de 18 caracteres de la CURP). Al completar la solicitud, generará automáticamente un acuse de recibo digital con un folio único que servirá para el seguimiento posterior.

**Seguimiento de estatus (FUN-009 a FUN-010):** Los usuarios podrán consultar en tiempo real el estatus de su solicitud mediante el folio asignado. El sistema mostrará cuatro posibles estados: "Recibida", "En Revisión", "Aprobada" y "Rechazada", este último siempre acompañado de un motivo detallado que oriente al solicitante sobre los pasos a seguir.

**Módulo de administración web (FUN-011 a FUN-014):** El personal administrativo accederá a un portal web seguro que presentará una bandeja de solicitudes pendientes de revisión. Podrán visualizar todos los datos y documentos de cada solicitud, y cambiar el estatus a "Aprobada" o "Rechazada" con un campo obligatorio para justificar la decisión en caso de rechazo. Esta funcionalidad cumple con el principio de transparencia y motivación de actos administrativos.

**Notificaciones (FUN-015):** El sistema enviará notificaciones automáticas a los usuarios cada vez que el estatus de su solicitud cambie, manteniéndolos informados sin necesidad de consultas activas constantes. Esto reduce la ansiedad identificada en las entrevistas y mejora la experiencia del usuario.

### 5.3.2 Requisitos no funcionales

Los requisitos no funcionales establecen las cualidades del sistema en términos de rendimiento, usabilidad, seguridad y otros atributos de calidad:

**Rendimiento (PER-001 a PER-003):** El sistema debe ser capaz de almacenar y gestionar millones de registros de beneficiarios, considerando que el programa actual atiende a más de 12 millones de derechohabientes (Programas para el Bienestar, 2024). Los tiempos de carga de las vistas principales de la aplicación móvil no deben exceder los 5 segundos incluso en conexiones de baja velocidad (3G), atendiendo a la realidad de la brecha digital donde "3 de cada 10 personas aún no cuentan con acceso a internet" (IFT, 2022, párr. 2).

**Usabilidad y accesibilidad (USA-001 a USA-005):** La interfaz debe utilizar alto contraste y iconografía clara para facilitar la lectura a usuarios con limitaciones visuales. El tamaño de fuente debe ser ajustable en al menos tres niveles. El lenguaje debe ser simple y directo, evitando tecnicismos burocráticos. El flujo de registro debe ser lineal y sin repeticiones innecesarias. Fundamentalmente, la aplicación debe incluir un botón de "Ayuda" siempre visible que conecte con soporte humano, respondiendo a la recomendación de los participantes sobre la importancia del acompañamiento personalizado.

**Seguridad (SEC-001 a SEC-002):** Todos los datos personales deben estar cifrados tanto en reposo (en la base de datos) como en tránsito (durante la comunicación entre cliente y servidor) utilizando algoritmos estándar de la industria como AES-256 para cifrado en reposo y TLS 1.3 para comunicaciones. El acceso al módulo administrativo web debe requerir autenticación de dos factores (2FA) para prevenir accesos no autorizados. Estas medidas cumplen con la Ley General de Protección de Datos Personales en Posesión de Sujetos Obligados (Cámara de Diputados, 2025).

**Disponibilidad y fiabilidad:** El sistema debe tener una alta disponibilidad operativa de al menos 99.9%, lo que equivale a un tiempo de inactividad máximo de aproximadamente 8.7 horas por año. Esto se logrará mediante arquitectura redundante, balanceo de carga y respaldos automáticos.

**Auditabilidad (DB-003):** El sistema debe implementar un registro de auditoría inmutable que registre todos los accesos, consultas y modificaciones a los datos de los beneficiarios por parte del personal administrativo. Este log debe incluir: usuario que realizó la acción, fecha y hora con precisión de segundos, tipo de acción, datos modificados (antes y después) y dirección IP de origen. Esta capacidad es fundamental para la transparencia y permite detectar usos indebidos del sistema.

### 5.3.3 Restricciones de diseño y cumplimiento normativo

El diseño del sistema debe cumplir con las siguientes restricciones y normativas:

**Uso exclusivo de FLOSS (CON-001):** Toda la arquitectura de la solución debe estar basada en Software Libre y de Código Abierto, desde el sistema operativo hasta las aplicaciones y librerías utilizadas. Esta restricción responde al objetivo estratégico de soberanía tecnológica y cumple con el Acuerdo publicado en el DOF (2021).

**Arquitectura de microservicios (CON-003):** El backend debe seguir un patrón de microservicios que facilite la escalabilidad horizontal, el mantenimiento independiente de componentes y la posibilidad de replicar servicios específicos en otros programas sociales sin necesidad de rediseñar todo el sistema.

**Cumplimiento de la LGPDPPSO (Restricción implícita):** El sistema debe cumplir estrictamente con todos los principios de protección de datos personales establecidos en la Ley General de Protección de Datos Personales en Posesión de Sujetos Obligados, particularmente los principios de licitud, finalidad, lealtad, consentimiento, calidad, proporcionalidad, información y responsabilidad (Cámara de Diputados, 2025, art. 3).

**Interoperabilidad (Requisito derivado del contexto):** El sistema debe implementar APIs REST bien documentadas siguiendo estándares internacionales (OpenAPI Specification 3.0) para facilitar la interoperabilidad futura con otros sistemas de la Administración Pública Federal, como el Sistema de Identificación Ciudadana o plataformas de pagos gubernamentales.

## 5.4 Modelo de implementación y despliegue

La implementación del sistema propuesto requiere una estrategia metodológica que considere tanto los aspectos técnicos como los organizacionales y de gestión del cambio.

### 5.4.1 Metodología de desarrollo

Se propone la adopción de una metodología ágil basada en Scrum, adaptada al contexto gubernamental. El proceso de desarrollo se organizará en sprints de dos semanas, con los siguientes roles:

**Product Owner:** Representante de la Secretaría de Bienestar con conocimiento profundo del programa de Pensión para el Bienestar y autoridad para la toma de decisiones sobre requisitos y prioridades.

**Scrum Master:** Facilitador del proceso ágil, encargado de eliminar impedimentos y garantizar el cumplimiento de las ceremonias de Scrum.

**Equipo de desarrollo:** Conformado por desarrolladores backend (especializados en Node.js/Python y arquitectura de microservicios), desarrolladores frontend (especializados en Flutter y React), especialistas en seguridad y ciberseguridad, especialistas en accesibilidad y UX para adultos mayores, administradores de sistemas (SysAdmins) con experiencia en FLOSS y DevOps.

### 5.4.2 Estrategia de despliegue

El despliegue del sistema seguirá un enfoque progresivo en tres fases:

**Fase 1 - Piloto controlado (Meses 1-3):** Despliegue en un estado piloto con una población limitada de beneficiarios (aproximadamente 10,000 usuarios). Esta fase permitirá validar la funcionalidad del sistema en condiciones reales, identificar problemas de usabilidad o rendimiento no detectados en las pruebas, ajustar la capacitación del personal administrativo y recopilar métricas de uso y satisfacción.

**Fase 2 - Expansión regional (Meses 4-8):** Despliegue gradual en 5-10 estados adicionales, incrementando la población de usuarios a aproximadamente 500,000 beneficiarios. En esta fase se validará la escalabilidad de la infraestructura y se refinará el proceso de soporte y capacitación a gran escala.

**Fase 3 - Despliegue nacional (Meses 9-12):** Despliegue completo en todo el territorio nacional, migrando progresivamente a los más de 12 millones de beneficiarios actuales del programa. Esta fase incluirá un período de operación dual donde coexistan el proceso manual tradicional y el sistema digital, permitiendo una transición gradual y minimizando riesgos.

### 5.4.3 Infraestructura de despliegue

Se propone una arquitectura de infraestructura basada en tres ambientes separados:

**Ambiente de desarrollo:** Cluster Kubernetes local o en nube privada donde el equipo de desarrollo realiza pruebas de integración continua. Este ambiente se actualiza constantemente con cada commit al repositorio de código.

**Ambiente de pruebas (staging):** Réplica exacta del ambiente de producción donde se realizan pruebas de aceptación de usuario (UAT) y pruebas de carga antes de cada despliegue a producción. Este ambiente permite validar cambios sin riesgo para los usuarios finales.

**Ambiente de producción:** Infraestructura de alta disponibilidad con redundancia geográfica. Se propone un modelo híbrido que combina servidores en centros de datos gubernamentales (para mantener soberanía de datos) con capacidad de cloud bursting en nubes públicas compatibles con FLOSS en caso de picos de demanda.

La infraestructura de producción se compondrá de:

- Cluster Kubernetes multi-nodo: Mínimo 6 nodos workers distribuidos en al menos 2 zonas de disponibilidad diferentes para garantizar alta disponibilidad ante fallos de infraestructura.
- Balanceadores de carga: NGINX configurado como balanceador de carga L7 (capa de aplicación) que distribuirá el tráfico entre las instancias de los microservicios.
- Base de datos PostgreSQL: Configuración de alta disponibilidad en modo maestro-esclavo con replicación sincrónica y failover automático utilizando herramientas FLOSS como Patroni.
- Almacenamiento de objetos MinIO: Cluster distribuido con replicación de datos para garantizar durabilidad y disponibilidad de los documentos cargados por los usuarios.
- Sistema de respaldos: Respaldos automatizados diarios de bases de datos y almacenamiento de objetos, con retención de 30 días y almacenamiento cifrado fuera del sitio primario.
- Monitoreo y alertas: Stack de monitoreo con Prometheus, Grafana y Alertmanager configurado para detectar y notificar proactivamente sobre problemas de rendimiento, disponibilidad o seguridad.

### 5.4.4 Pipeline de CI/CD

Para garantizar la calidad y agilidad en los despliegues, se implementará un pipeline de integración continua y despliegue continuo (CI/CD) utilizando GitLab CI/CD:

**Etapa de construcción (Build):** Al realizar un commit al repositorio, se ejecutan automáticamente las pruebas unitarias y de integración. Si todas las pruebas pasan, se construye la imagen Docker del microservicio correspondiente.

**Etapa de análisis de calidad:** Se ejecutan herramientas de análisis estático de código (SonarQube Community Edition) para detectar vulnerabilidades de seguridad, code smells y violaciones de estándares de codificación.

**Etapa de seguridad:** Se escanean las imágenes Docker generadas en busca de vulnerabilidades conocidas utilizando herramientas FLOSS como Trivy.

**Etapa de despliegue:** Si todas las etapas anteriores son exitosas, la imagen se despliega automáticamente al ambiente de desarrollo. Los despliegues a staging y producción requieren aprobación manual del Product Owner o del líder técnico.

Este pipeline garantiza que solo código de calidad y seguro llegue a producción, reduciendo el riesgo de errores y vulnerabilidades.

## 5.5 Seguridad y cumplimiento normativo

La seguridad de la información es un aspecto crítico del sistema propuesto, dado que manejará datos personales sensibles de millones de ciudadanos. La estrategia de seguridad se fundamenta en el Marco de Ciberseguridad del NIST presentado en la Figura 2.1 del marco teórico.

### 5.5.1 Medidas de seguridad implementadas

**Cifrado de datos:**

- *En reposo:* Todos los datos sensibles en la base de datos PostgreSQL se cifrarán utilizando Transparent Data Encryption (TDE) con algoritmo AES-256. Las contraseñas de usuario se almacenarán utilizando funciones de hashing criptográfico bcrypt con factor de trabajo (cost factor) de al menos 12.
- *En tránsito:* Toda comunicación entre componentes del sistema utilizará TLS 1.3, deshabilitando versiones anteriores del protocolo que presentan vulnerabilidades conocidas. Los certificados SSL/TLS serán emitidos por Let's Encrypt, una autoridad certificadora FLOSS y gratuita.

**Control de acceso:**

- Autenticación de múltiples factores (MFA): El personal administrativo deberá autenticarse utilizando contraseña y un segundo factor (código TOTP generado por aplicación autenticadora como FreeOTP o Google Authenticator).
- Control de acceso basado en roles (RBAC): Se implementará un sistema granular de permisos donde cada usuario tiene asignados roles específicos (por ejemplo, "Validador de Documentos", "Aprobador de Solicitudes", "Administrador del Sistema") con privilegios mínimos necesarios para su función.
- Tokens JWT: Las sesiones de usuario se gestionarán mediante JSON Web Tokens con tiempo de expiración corto (15 minutos para acceso, 7 días para refresh token), minimizando el riesgo de secuestro de sesión.

**Protección contra amenazas comunes:**

- Firewall de aplicaciones web (WAF): Se implementará ModSecurity, un WAF FLOSS que protege contra ataques comunes como inyección SQL, cross-site scripting (XSS) y cross-site request forgery (CSRF).
- Rate limiting: Límites de tasa de peticiones por IP y por usuario para prevenir ataques de denegación de servicio (DoS) y fuerza bruta contra los endpoints de autenticación.
- Validación y sanitización de entradas: Toda entrada de usuario será validada en el lado del servidor y sanitizada para prevenir inyecciones de código.
- Actualizaciones de seguridad: Proceso automatizado de aplicación de parches de seguridad a todos los componentes del sistema, con ventanas de mantenimiento programadas semanalmente.

**Auditoría y trazabilidad:**

- Logs de auditoría inmutables: Implementación de un sistema de logging centralizado donde cada acción crítica (acceso a datos de beneficiarios, aprobación/rechazo de solicitudes, cambios de configuración) queda registrada con timestamp, usuario, IP origen y detalles de la acción.
- Integridad de logs: Los logs se firmarán criptográficamente para garantizar su integridad y detectar intentos de manipulación.
- Retención de logs: Los logs de auditoría se conservarán por un período mínimo de 5 años según lo establece la LGPDPPSO para datos de interés público.

### 5.5.2 Cumplimiento normativo

El sistema ha sido diseñado para cumplir con la legislación mexicana aplicable:

**Ley General de Protección de Datos Personales en Posesión de Sujetos Obligados (LGPDPPSO):**

- Principio de licitud: El sistema procesará datos personales exclusivamente para el fin legítimo de gestionar el programa de Pensión para el Bienestar.
- Principio de consentimiento: Se solicitará consentimiento explícito e informado a los beneficiarios para el tratamiento de sus datos, mediante un aviso de privacidad claro y accesible.
- Principio de información: Los beneficiarios tendrán acceso en todo momento a sus datos personales y podrán ejercer sus derechos ARCO (Acceso, Rectificación, Cancelación y Oposición) mediante funcionalidad integrada en la aplicación.
- Principio de seguridad: Las medidas de seguridad descritas en la sección anterior garantizan la confidencialidad, integridad y disponibilidad de los datos personales.

**Estrategia Digital Nacional 2021-2024:**
El sistema se alinea con los objetivos de la Estrategia Digital Nacional de "fomentar una cultura de seguridad de la información en la sociedad y en el gobierno" y "fortalecer la coordinación y colaboración entre las dependencias y entidades de la Administración Pública Federal" (Gobierno de la República, 2021, p. 12).

**Norma Técnica para la Interoperabilidad entre los Sistemas Automatizados de Control de Gestión y Seguimiento de Trámites y Servicios del Gobierno Federal:**
El sistema implementará APIs REST documentadas según el estándar OpenAPI 3.0, facilitando la interoperabilidad futura con otros sistemas gubernamentales.

### 5.5.3 Evaluación y certificación de seguridad

Se recomienda que, previo al despliegue en producción, el sistema sea sometido a:

**Auditoría de seguridad externa:** Contratación de una empresa especializada en seguridad informática que realice pruebas de penetración (pentesting) sobre la aplicación móvil, el portal web y la API backend, identificando vulnerabilidades antes de que puedan ser explotadas.

**Evaluación de cumplimiento normativo:** Revisión por parte del área jurídica de la Secretaría de Bienestar y del Instituto Nacional de Transparencia, Acceso a la Información y Protección de Datos Personales (INAI) para validar el cumplimiento de la LGPDPPSO.

**Certificación ISO 27001 (opcional):** Aunque no es obligatorio para el sector público, obtener la certificación ISO 27001 en Sistemas de Gestión de Seguridad de la Información demostraría el compromiso institucional con la seguridad y facilitaría la confianza ciudadana en el sistema.

## 5.6 Consideraciones de sostenibilidad y escalabilidad

La propuesta contempla mecanismos para garantizar la sostenibilidad técnica, económica y social del sistema a largo plazo.

### 5.6.1 Sostenibilidad técnica

**Documentación exhaustiva:** Todo el código fuente estará documentado siguiendo estándares de la industria (JSDoc para JavaScript, docstrings para Python). Además, se mantendrá documentación arquitectónica actualizada utilizando el modelo C4 (Context, Containers, Components, Code) que facilita la comprensión del sistema a diferentes niveles de abstracción.

**Capacitación y transferencia de conocimiento:** El equipo técnico de la Secretaría de Bienestar recibirá capacitación especializada en las tecnologías FLOSS utilizadas, garantizando que puedan mantener y evolucionar el sistema de forma autónoma sin dependencia de proveedores externos.

**Comunidad y soporte:** Al utilizar tecnologías FLOSS ampliamente adoptadas (PostgreSQL, Kubernetes, Flutter, etc.), la Secretaría podrá acceder a soporte comunitario activo, documentación extensa y un ecosistema de profesionales capacitados en el mercado laboral mexicano.

### 5.6.2 Sostenibilidad económica

**Eliminación de licencias propietarias:** El uso exclusivo de FLOSS elimina los costos recurrentes de licenciamiento de software, liberando recursos que pueden destinarse a mejorar la infraestructura o ampliar funcionalidades.

**Reducción de costos operativos:** La automatización del proceso de registro y validación reducirá significativamente los costos operativos asociados al procesamiento manual de solicitudes, liberando personal administrativo para tareas de mayor valor agregado como atención personalizada a casos especiales.

**Escalabilidad económica:** La arquitectura basada en microservicios y contenedores permite escalar horizontalmente solo los componentes que requieren mayor capacidad, optimizando el uso de recursos computacionales y reduciendo costos innecesarios.

### 5.6.3 Replicabilidad y escalabilidad programática

Como se mencionó en la justificación del Capítulo 1, uno de los beneficios estratégicos de esta propuesta es su replicabilidad. El sistema ha sido diseñado con una arquitectura modular que facilita su adaptación a otros programas sociales:

**Módulos genéricos reutilizables:** Servicios como autenticación, gestión de usuarios, carga de documentos y notificaciones son suficientemente genéricos para ser reutilizados en otros programas con mínimas adaptaciones.

**Configuración por programa:** El sistema permitirá configurar parámetros específicos de cada programa social (requisitos documentales, criterios de elegibilidad, flujos de aprobación) sin necesidad de modificar el código fuente, mediante archivos de configuración o bases de datos.

**Licenciamiento abierto:** El código fuente del sistema se licenciará bajo una licencia FLOSS permisiva (GPL v3 o AGPL v3) que permitirá a otras dependencias gubernamentales, gobiernos estatales o municipales adoptarlo, adaptarlo y mejorarlo libremente, creando un efecto multiplicador de la inversión inicial.

Esta visión de escalabilidad programática se alinea con el objetivo estratégico de "establecer un precedente de cómo la tecnología, específicamente el Software Libre y de Código Abierto (FLOSS), puede ser una herramienta estratégica para construir un gobierno más transparente, eficiente y humano" planteado en el Capítulo 1.

## Conclusión del capítulo

El presente capítulo ha presentado una propuesta técnica integral para el Sistema de Gestión de Programas Sociales basado en FLOSS, abordando los aspectos arquitectónicos, de diseño de interfaz, especificación de requisitos, implementación, seguridad y sostenibilidad. La propuesta responde directamente a los objetivos planteados en el Capítulo 1 y se fundamenta en los hallazgos del estudio cualitativo presentado en el Capítulo 4.

La arquitectura de microservicios basada exclusivamente en tecnologías FLOSS garantiza la soberanía tecnológica, la transparencia y la sostenibilidad económica a largo plazo. El diseño de interfaz centrado en el usuario, con especial atención a la accesibilidad para adultos mayores, dignifica el proceso de acceso a servicios gubernamentales y reduce las barreras identificadas en la investigación.

Las especificaciones técnicas detalladas en el SRS, combinadas con las medidas robustas de seguridad y cumplimiento normativo, aseguran que el sistema no solo sea funcional, sino que proteja adecuadamente los datos personales sensibles de millones de ciudadanos, cumpliendo con la legislación mexicana aplicable.

Finalmente, la estrategia de implementación progresiva y la visión de replicabilidad a otros programas sociales posicionan a esta propuesta no solo como una solución puntual al problema del programa de Pensión para el Bienestar, sino como un modelo escalable de modernización de la gestión pública en México mediante el uso estratégico de Software Libre y de Código Abierto.

---

## Referencias adicionales del Capítulo 5

ARDURA Consulting. (2024). *PostgreSQL Database: A Strategic Guide 2025*. https://ardura.consulting/postgresql-database-strategic-guide-2025/

Flutter. (2025). *Flutter: Build apps for any screen*. GitHub. https://github.com/flutter/flutter

LeanCode. (2025, 6 de enero). *What is Flutter Framework: Cross-Platform Technology Explained*. https://leancode.co/blog/what-is-flutter-framework

MinIO. (2025). *MinIO: S3 Compatible, Exascale Object Store for AI*. https://min.io/

Morales, F., Díaz, G., & Tolaba, F. (2021). GobLin: El Sistema Operativo GNU/Linux para los Gobiernos. *Revista Latinoamericana de Ingeniería de Software, 9*(3), 1-8.

Newman, S. (2020). *Building Microservices: Designing Fine-Grained Systems* (2.ª ed.). O'Reilly Media.

Nygard, M. T. (2018). *Release It!: Design and Deploy Production-Ready Software* (2.ª ed.). Pragmatic Bookshelf.

PostgreSQL Global Development Group. (2025, 12 de noviembre). *PostgreSQL: The World's Most Advanced Open Source Relational Database*. https://www.postgresql.org/

Richardson, C. (2018). *Microservices Patterns: With Examples in Java*. Manning Publications.
