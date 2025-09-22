# Presentación: Insecure Direct Object Reference (IDOR)
## Diferentes ataques de seguridad en aplicaciones Web

---

## Diapositiva 1: Título
### Insecure Direct Object Reference (IDOR)
**Vulnerabilidad Crítica en Aplicaciones Web**

*Tema de Investigación: Ataques de Seguridad en Aplicaciones Web*

---

## Diapositiva 2: ¿Qué es IDOR?
### Definición
**IDOR** es una vulnerabilidad que surge cuando una aplicación proporciona acceso directo a objetos basándose en entrada del usuario, **sin validar la autorización**.

### Características Principales
- Fallo en el control de acceso
- Manipulación de referencias directas
- Parte del OWASP Top 10 2021 (#1 Broken Access Control)
- Afecta al 94% de aplicaciones web

---

## Diapositiva 3: El Problema Fundamental

### ¿Por qué ocurre?
- **Falta de validación de autorización** server-side
- Uso de **identificadores predecibles** (IDs secuenciales)
- **Confianza en controles client-side**
- Implementación incorrecta de controles de acceso

---

## Diapositiva 4: Tipos de Vulnerabilidades IDOR

### Clasificación Principal
1. **IDOR Básico** - Manipulación directa de IDs
2. **IDOR Ciego** - Modificación sin visualización
3. **IDOR en Archivos** - Acceso no autorizado a documentos
4. **IDOR en APIs** - Vulnerabilidades en endpoints REST/GraphQL
5. **IDOR por Parámetros** - Técnicas avanzadas de manipulación

---

## Diapositiva 5: Ejemplos Reales - Casos Documentados

### Casos de Alto Impacto
- **Facebook (2018)**: GraphQL IDOR - Takeover de URLs
- **Instagram (2021)**: Acceso a historias privadas - $49,500
- **PayPal (2020)**: Sistema de facturación comprometido
- **Snapchat (2014)**: IDs secuenciales - Scraping masivo

---

## Diapositiva 6: Ejemplo Práctico - URL Manipulation

### Escenario Típico
```
URL Legítima:
https://banco.com/cuenta?id=1001

URL Maliciosa:
https://banco.com/cuenta?id=1002
```

### ¿Qué pasa?
❌ **Sin IDOR**: Acceso denegado, error 403  
✅ **Con IDOR**: Datos de otra cuenta expuestos

---

## Diapositiva 7: IDOR en APIs Modernas

### REST API Vulnerable
```http
GET /api/users/123/orders
Authorization: Bearer [token]

↓ Manipulación ↓

GET /api/users/124/orders
Authorization: Bearer [token]
```

### GraphQL Vulnerable
```graphql
{
  user(id: "123") {
    email
    orders { amount }
  }
}
```

---

## Diapositiva 8: Técnicas de Detección

### Metodología Manual
1. **Mapear** endpoints con parámetros ID
2. **Crear** múltiples usuarios de prueba
3. **Interceptar** peticiones legítimas
4. **Modificar** identificadores sistemáticamente
5. **Analizar** respuestas del servidor

### Indicadores de IDOR
- Status Code 200 con datos diferentes
- Cambios en content-length
- Exposición de información no autorizada

---

## Diapositiva 9: Herramientas de Testing

### Burp Suite
- **Proxy**: Interceptación de tráfico
- **Intruder**: Fuzzing automatizado
- **Repeater**: Testing manual iterativo
- **Autorize Extension**: Testing específico de autorización

### Otras Herramientas
- **OWASP ZAP**: Scanner open source
- **Scripts Python**: Automatización personalizada
- **cURL**: Testing de línea de comandos

---

## Diapositiva 10: Demostración - Burp Suite

### Configuración Básica
1. Configurar proxy en puerto 8080
2. Importar certificado CA
3. Interceptar tráfico de aplicación
4. Enviar peticiones sospechosas a tools

### Proceso de Testing
- Interceptar → Modificar → Analizar → Documentar

---

## Diapositiva 11: Código Vulnerable vs Seguro

### ❌ Código Vulnerable (PHP)
```php
<?php
$file_id = $_GET['file_id'];
$file_path = "/uploads/file_" . $file_id . ".pdf";

if (file_exists($file_path)) {
    readfile($file_path); // Sin validación!
}
?>
```

### ✅ Código Seguro (PHP)
```php
<?php
session_start();
$file_id = $_GET['file_id'];
$user_id = $_SESSION['user_id'];

$stmt = $pdo->prepare("SELECT file_path FROM user_files 
                       WHERE id = ? AND user_id = ?");
$stmt->execute([$file_id, $user_id]);
$file = $stmt->fetch();

if ($file && file_exists($file['file_path'])) {
    readfile($file['file_path']);
}
?>
```

---

## Diapositiva 12: Métodos de Prevención

### Controles Fundamentales
1. **Validación de Autorización** en cada petición
2. **Referencias Indirectas** (tokens, UUIDs)
3. **Principio de Menor Privilegio**
4. **Deny by Default**

### Implementación
```java
@PreAuthorize("@authService.canAccess(authentication.name, #userId)")
public User getUser(@PathVariable int userId) {
    return userService.getUser(userId);
}
```

---

## Diapositiva 13: Práctica Guiada - Setup

### Entorno de Testing
1. **Burp Suite** configurado
2. **Aplicación vulnerable** (DVWA/WebGoat)
3. **Múltiples usuarios** de prueba
4. **Proxy configurado** en navegador

### Objetivos
- Identificar endpoints vulnerables
- Ejecutar ataques IDOR
- Documentar hallazgos
- Proponer remediaciones

---

## Diapositiva 14: Práctica - Identificación

### Endpoints Sospechosos
```
/profile.php?user_id=123
/documents/view?doc_id=456
/api/v1/orders/789
/admin/users/edit/321
```

### Testing Sistemático
1. Interceptar con Burp Proxy
2. Modificar parámetro ID
3. Analizar respuesta
4. Confirmar vulnerabilidad

---

## Diapositiva 15: Práctica - Automatización

### Burp Intruder Configuration
- **Target**: Endpoint sospechoso
- **Positions**: Parámetro ID marcado
- **Payloads**: Numbers (1-1000)
- **Options**: Follow redirects

### Análisis de Resultados
- Filtrar por Status Code 200
- Comparar response lengths
- Investigar anomalías

---

## Diapositiva 16: Impacto y Consecuencias

### Impacto Técnico
- **Confidencialidad**: Exposición de datos sensibles
- **Integridad**: Modificación no autorizada
- **Disponibilidad**: Eliminación de recursos

### Impacto Empresarial
- Multas regulatorias (GDPR: hasta 4% revenue)
- Pérdida de confianza del cliente
- Costos de investigación y remediación
- Daño reputacional

---

## Diapositiva 17: Casos Reales de Impacto

### Breach Documentados
- **Optus (2023)**: 10M registros expuestos → $12M multa
- **Instagram**: Historias privadas → $49,500 bounty
- **PayPal**: Sistema facturación → Reconfiguración total

### Lecciones Aprendidas
- Implementar controles server-side
- Testing continuo de autorización
- Monitoreo de accesos anómalos

---

## Diapositiva 18: Evolución y Tendencias

### IDOR en Arquitecturas Modernas
- **Microservicios**: Autorización distribuida
- **GraphQL**: Validación a nivel de campo
- **APIs Mobile**: Tokens JWT vulnerables
- **Cloud Applications**: IAM mal configurado

### Desafíos Emergentes
- Complejidad de autorización
- APIs sin documentar
- Desarrollo ágil vs seguridad

---

## Diapositiva 19: Prácticas Seguras

### Desarrollo Seguro
✅ **Validación del lado del servidor** siempre  
✅ **Principio de priviliegio mínimo**  
✅ **Referencias indirectas** (UUIDs)  
✅ **Pruebas de seguridad automatizadas**  
✅ **Auditorías de seguridad regulares**

### Pruebas Continuas
- Análisis estático y dinámico en CI/CD  
- Pruebas de penetración regulares  
- Programas de búsqueda de vulnerabilidades  
- Revisiones de código de seguridad

---

## Diapositiva 20: Conclusiones Clave

### 5 Puntos Críticos
1. **IDOR es prevalente** - Afecta 94% aplicaciones web
2. **Simple pero devastador** - Fácil explotación, alto impacto  
3. **Detección sistémica** - Requiere testing manual + automatizado
4. **Prevención integral** - Controles server-side obligatorios
5. **Evolución continua** - Adaptación a nuevas arquitecturas necesaria

### Llamada a la Acción
**"La seguridad no es un producto, es un proceso"** - Bruce Schneier

---

## Diapositiva 21: Conclusiones Generales

### Integrantes 1
- Las vulnerabilidades IDOR representan uno de los vectores de ataque más comunes en aplicaciones web modernas, formando parte integral del problema #1 del OWASP Top 10 2021 Broken Access Control), que afecta
al 94% de las aplicaciones analizadas.

### Integrantes 2
- A pesar de su simplicidad técnica, las vulnerabilidades IDOR pueden causar impactos devastadores, desde exposición masiva de datos hasta compromiso completo de sistemas, como demuestran los casos documentados de Facebook, Instagram y PayPal.

### Integrantes 3
- La detección efectiva de IDOR requiere una combinación de técnicas manuales y automatizadas, utilizando herramientas como Burp Suite y metodologías sistemáticas de testing de autorización, mientras que la
prevención exitosa depende de la implementación consistente de controles de acceso server-side.

### Integrantes 4
- Las vulnerabilidades IDOR están evolucionando hacia entornos más complejos como APIs RESTful, GraphQL y aplicaciones de microservicios, requiriendo enfoques de seguridad adaptativos y controles de autorización granulares a nivel de objeto.

### Integrantes 5
- La mitigación efectiva de IDOR no es solo una responsabilidad técnica, sino que requiere un enfoque holístico que incluya procesos de desarrollo seguro, capacitación continua de desarrolladores, auditorías regulares de seguridad y una cultura organizacional que priorice la seguridad desde el diseño.

---

## Diapositiva 22: Demostración Final

### Live Demo: IDOR Testing
1. **Setup**: Aplicación vulnerable + Burp Suite
2. **Discovery**: Identificar endpoint vulnerable
3. **Exploitation**: Manipular parámetros
4. **Impact**: Demostrar acceso no autorizado
5. **Remediation**: Mostrar código seguro

---

## Diapositiva 23: Referencias

1. OWASP Foundation. (2021). OWASP Top 10 2021 - A01: Control de acceso roto. https://owasp.org/Top10/A01_2021-Broken_Access_Control/
2. OWASP Foundation. (2023). Guía para la prevención de referencias directas inseguras a objetos. https://cheatsheetseries.owasp.org/cheatsheets/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.html
3. PortSwigger Web Security Academy. (2023). Referencias directas inseguras a objetos (IDOR). https://portswigger.net/web-security/access-control/idor
4. Ferreira, M. (2020). Investigación de vulnerabilidad IDOR en Facebook GraphQL. API Security Newsletter, 102.
5. Sharma, N. (2022). Vulnerabilidad crítica de IDOR en Instagram - Recompensa de errores de $49,500. InfoSec Write-ups.
6. Intigriti Research Team. (2024). IDOR: Una guía completa para explotar vulnerabilidades IDOR avanzadas. https://www.intigriti.com/blog/news/idor-a-complete-guide
7. Agencia de Ciberseguridad e Infraestructura (CISA), Centro Australiano de Ciberseguridad (ACSC), & Agencia de Seguridad Nacional (NSA). (2023). Aviso AA23-208A: Vulnerabilidades de referencias directas inseguras a objetos. Asesoría conjunta de ciberseguridad.
8. Wallarm Security Team. (2025). Vulnerabilidad IDOR (referencias directas inseguras a objetos). https://www.wallarm.com/what/what-is-the-insecure-direct-object-references-vulnerability
9. Snyk Security Research. (2023). Cómo encontrar y corregir referencias directas inseguras a objetos en Python. https://snyk.io/blog/insecure-direct-object-references-python/
10. Acunetix Security Team. (2025). Control de acceso roto: Un riesgo del Top 10 de OWASP. https://www.acunetix.com/blog/web-security-zone/broken-access-control/
11. Virtual Cyber Labs. (2025). Referencias directas inseguras a objetos (IDOR): Un análisis profundo. https://virtualcyberlabs.com/insecure-direct-object-references-idor/
12. Aptori Security. (2023). Prevención de vulnerabilidades de referencias directas inseguras a objetos (IDOR). https://www.aptori.com/blog/insecure-direct-object-reference-idor-vulnerability-prevention
13. Escape Technology. (2025). Vulnerabilidades IDOR en GraphQL: Qué son y cómo correglarlas. https://escape.tech/blog/idor-in-graphql/
14. LevelBlue Security. (2021). Pruebas manuales y semi-automatizadas para IDORs usando Burp Suite. https://levelblue.com/blogs/security-essentials/manual-and-semi-automated-testing-for-idors-using-burp-suite
15. CodeArsenal Community. (2024). Detección y corrección de referencias directas inseguras a objetos (IDOR) en aplicaciones Java. https://codearsenalcommunity.github.io/how-to-fix-idor-in-java/