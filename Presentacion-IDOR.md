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

[62]

### ¿Por qué ocurre?
- **Falta de validación de autorización** server-side
- Uso de **identificadores predecibles** (IDs secuenciales)
- **Confianza en controles client-side**
- Implementación incorrecta de controles de acceso

---

## Diapositiva 4: Tipos de Vulnerabilidades IDOR

[63]

### Clasificación Principal
1. **IDOR Básico** - Manipulación directa de IDs
2. **IDOR Ciego** - Modificación sin visualización
3. **IDOR en Archivos** - Acceso no autorizado a documentos
4. **IDOR en APIs** - Vulnerabilidades en endpoints REST/GraphQL
5. **IDOR por Parámetros** - Técnicas avanzadas de manipulación

---

## Diapositiva 5: Ejemplos Reales - Casos Documentados

[64]

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

[65]

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

## Diapositiva 19: Best Practices

### Desarrollo Seguro
✅ **Server-side validation** siempre  
✅ **Least privilege principle**  
✅ **Indirect references** (UUIDs)  
✅ **Automated security testing**  
✅ **Regular security audits**

### Testing Continuo
- SAST/DAST en CI/CD
- Penetration testing regular
- Bug bounty programs
- Security code reviews

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

## Diapositiva 21: Demostración Final

[66]

### Live Demo: IDOR Testing
1. **Setup**: Aplicación vulnerable + Burp Suite
2. **Discovery**: Identificar endpoint vulnerable
3. **Exploitation**: Manipular parámetros
4. **Impact**: Demostrar acceso no autorizado
5. **Remediation**: Mostrar código seguro

---

## Diapositiva 22: Recursos Adicionales

### Referencias Técnicas
- OWASP Top 10 2021
- PortSwigger Web Security Academy
- NIST Cybersecurity Framework
- CWE-639: Authorization Bypass

### Herramientas Recomendadas
- Burp Suite Professional
- OWASP ZAP
- Autorize Extension
- Custom Python Scripts

### Certificaciones
- CEH (Certified Ethical Hacker)
- OSCP (Offensive Security)
- CISSP (Security Professional)

---

## Diapositiva 23: Preguntas y Discusión

### Temas de Reflexión
- ¿Cómo implementar IDOR testing en su organización?
- ¿Qué herramientas son más efectivas en su contexto?
- ¿Cómo educar a equipos de desarrollo?
- ¿Qué medidas preventivas priorizarían?

### Contacto
- Email: security-research@universidad.edu
- GitHub: /security-examples
- LinkedIn: /cybersecurity-research

---

## Referencias Bibliográficas

[1] OWASP Foundation. (2021). *OWASP Top 10 2021 - A01 Broken Access Control*. https://owasp.org/Top10/A01_2021-Broken_Access_Control/

[2] OWASP Foundation. (2023). *Insecure Direct Object Reference Prevention Cheat Sheet*. https://cheatsheetseries.owasp.org/cheatsheets/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.html

[3] PortSwigger Web Security Academy. (2023). *Insecure direct object references (IDOR)*. https://portswigger.net/web-security/access-control/idor

[4] Ferreira, M. (2020). *Facebook GraphQL IDOR Vulnerability Research*. API Security Newsletter, Issue 102.

[5] Sharma, N. (2022). *Critical IDOR Vulnerability in Instagram - $49,500 Bug Bounty*. InfoSec Write-ups.

[6] Intigriti Research Team. (2024). *IDOR: A complete guide to exploiting advanced IDOR vulnerabilities*. https://www.intigriti.com/blog/news/idor-a-complete-guide

[7] CISA, ACSC, NSA. (2023). *Advisory AA23-208A: Insecure Direct Object Reference Vulnerabilities*. Joint Cybersecurity Advisory.

[8] Wallarm Security Team. (2025). *IDOR vulnerability (Insecure Direct Object References)*. https://www.wallarm.com/what/what-is-the-insecure-direct-object-references-vulnerability

[9] Snyk Security Research. (2023). *Finding and fixing insecure direct object references in Python*. https://snyk.io/blog/insecure-direct-object-references-python/

[10] Acunetix Security Team. (2025). *Broken Access Control: An OWASP Top 10 Risk*. https://www.acunetix.com/blog/web-security-zone/broken-access-control/

[11] Virtual Cyber Labs. (2025). *Insecure Direct Object References (IDOR): A Deep Dive*. https://virtualcyberlabs.com/insecure-direct-object-references-idor/

[12] Aptori Security. (2023). *Insecure Direct Object References (IDOR) Vulnerability Prevention*. https://www.aptori.com/blog/insecure-direct-object-reference-idor-vulnerability-prevention

[13] Escape Technology. (2025). *GraphQL IDOR Vulnerabilities: What They Are & How to Fix*. https://escape.tech/blog/idor-in-graphql/

[14] LevelBlue Security. (2021). *Manual and semi-automated testing for IDORs using Burp Suite*. https://levelblue.com/blogs/security-essentials/manual-and-semi-automated-testing-for-idors-using-burp-suite

[15] CodeArsenal Community. (2024). *Detecting & Fixing Insecure Direct Object References (IDOR) in Java Applications*. https://codearsenalcommunity.github.io/how-to-fix-idor-in-java/