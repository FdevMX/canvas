# Presentación: Seguridad en Transacciones de Base de Datos

## Diapositiva 1: Portada
**SEGURIDAD EN TRANSACCIONES DE BASE DE DATOS**
*Vulnerabilidades y Ataques*

**Tema 4: Transacción de Base de Datos**

*[Información del equipo]*
*[Fecha de presentación]*

---

## Diapositiva 2: Agenda
1. **Introducción a Transacciones ACID**
2. **Principales Vulnerabilidades**
3. **Vectores de Ataque Específicos**
4. **Ejemplos Prácticos de Explotación**
5. **Herramientas de Análisis**
6. **Laboratorios Demostrativos**
7. **Medidas de Mitigación**
8. **Conclusiones y Recomendaciones**

---

## Diapositiva 3: ¿Qué son las Transacciones ACID?
### Definición
Una **transacción** es una unidad lógica de trabajo que accede y modifica datos en una base de datos de manera controlada.

### Propiedades ACID
- **🔒 Atomicidad**: Todo o nada - operación indivisible
- **✅ Consistencia**: Mantiene integridad de datos
- **🔐 Aislamiento**: Transacciones no interfieren entre sí
- **💾 Durabilidad**: Cambios permanentes tras confirmación

### ¿Por qué son Críticas?
- Garantizan integridad en sistemas concurrentes
- Base de aplicaciones financieras y críticas
- **Pero también... vectores de ataque**

---

## Diapositiva 4: Principales Vulnerabilidades

### 1. **Inyección SQL Transaccional**
- Manipulación de transacciones completas
- Bypass de controles transaccionales
- Escalación de privilegios

### 2. **Condiciones de Carrera (Race Conditions)**
- Acceso simultáneo sin sincronización
- Explotación de ventanas temporales
- Estados inconsistentes

### 3. **Problemas de Aislamiento**
- Lecturas sucias (Dirty Reads)
- Lecturas no repetibles
- Lecturas fantasma (Phantom Reads)

### 4. **Deadlocks Maliciosos**
- Bloqueo mutuo de recursos
- Ataques de denegación de servicio

---

## Diapositiva 5: SQL Injection en Transacciones

### Escenario Típico: Sistema Bancario
```sql
-- Código Vulnerable
BEGIN TRANSACTION;
UPDATE accounts SET balance = balance - $amount 
WHERE account_id = '$from_account';
UPDATE accounts SET balance = balance + $amount 
WHERE account_id = '$to_account';
COMMIT;
```

### Ataque
```sql
-- Payload Malicioso
from_account = "1; UPDATE accounts SET balance = 999999 
WHERE account_id = 1; --"
```

### Impacto
- ✗ Manipulación de saldos bancarios
- ✗ Comprometimiento de múltiples registros
- ✗ Bypass de validaciones

---

## Diapositiva 6: Race Conditions - El Problema

### ¿Qué son?
Vulnerabilidad cuando múltiples procesos acceden **simultáneamente** a recursos compartidos sin sincronización adecuada.

### Ejemplo: Retiro Bancario
```python
def withdraw_money(account_id, amount):
    balance = get_balance(account_id)      # Paso 1
    if balance >= amount:                  # Paso 2
        # ⚠️ VENTANA VULNERABLE ⚠️
        set_balance(account_id, balance - amount)  # Paso 3
        return True
```

### La Ventana de Vulnerabilidad
- **Tiempo entre verificación y acción**
- Múltiples procesos pueden pasar la verificación
- Resultado: **Estado inconsistente**

---

## Diapositiva 7: Race Conditions - Explotación

### Escenario de Ataque
- **Cuenta con $1,000**
- **Atacante**: Envía 2 solicitudes simultáneas de $800

### Timeline del Ataque
| Tiempo | Proceso A | Proceso B |
|--------|-----------|-----------|
| T1 | Lee saldo: $1,000 | - |
| T2 | - | Lee saldo: $1,000 |
| T3 | Verifica: ✅ $1,000 ≥ $800 | - |
| T4 | - | Verifica: ✅ $1,000 ≥ $800 |
| T5 | Retira $800 | - |
| T6 | - | Retira $800 |

### Resultado
**Saldo final: -$600** (Retiró $1,600 de $1,000)

---

## Diapositiva 8: Niveles de Aislamiento y Vulnerabilidades

### Problemas por Nivel
| Nivel | Dirty Reads | Non-Repeatable | Phantom Reads |
|-------|-------------|----------------|---------------|
| **Read Uncommitted** | ❌ Permitido | ❌ Permitido | ❌ Permitido |
| **Read Committed** | ✅ Bloqueado | ❌ Permitido | ❌ Permitido |
| **Repeatable Read** | ✅ Bloqueado | ✅ Bloqueado | ❌ Permitido |
| **Serializable** | ✅ Bloqueado | ✅ Bloqueado | ✅ Bloqueado |

### Implicaciones de Seguridad
- **Dirty Reads**: Exposición de datos no confirmados
- **Non-Repeatable**: Decisiones basadas en datos cambiantes
- **Phantom Reads**: Inconsistencias en análisis de rangos

---

## Diapositiva 9: Deadlocks Maliciosos

### ¿Qué es un Deadlock?
Situación donde **dos o más transacciones** se bloquean mutuamente esperando recursos que la otra posee.

### Ejemplo de Deadlock
```
Transacción 1: LOCK A → Espera B
Transacción 2: LOCK B → Espera A
```

### Explotación Maliciosa
- **DoS Attack**: Crear deadlocks intencionalmente
- **Resource Exhaustion**: Agotar recursos del sistema
- **Service Disruption**: Afectar disponibilidad

### Detección
- Timeout automático
- Algoritmos de detección de ciclos
- Selección de víctimas

---

## Diapositiva 10: Vectores de Ataque Específicos

### 1. **Sistemas Financieros**
- Double Spending
- Transaction Malleability
- Balance Manipulation

### 2. **E-commerce**
- Inventory Manipulation
- Price Manipulation
- Stock Depletion

### 3. **Autenticación**
- Session Fixation
- Privilege Escalation
- Account Takeover

### 4. **Aplicaciones Web**
- Data Corruption
- Business Logic Bypass
- Audit Trail Manipulation

---

## Diapositiva 11: Demostración Práctica - Setup

### Laboratorio: SQL Injection en Transacciones

#### Base de Datos de Prueba
```sql
CREATE DATABASE bank_lab;
CREATE TABLE accounts (
    account_id INT PRIMARY KEY,
    username VARCHAR(50),
    balance DECIMAL(10,2)
);

INSERT INTO accounts VALUES 
(1, 'alice', 1000.00),
(2, 'bob', 500.00);
```

#### Aplicación Vulnerable
```php
// Transferencia vulnerable
$query = "BEGIN; 
          UPDATE accounts SET balance = balance - $amount 
          WHERE account_id = '$from'; 
          UPDATE accounts SET balance = balance + $amount 
          WHERE account_id = '$to'; 
          COMMIT;";
```

---

## Diapositiva 12: Demostración - Explotación

### Payload de Ataque
```sql
-- Input malicioso en campo 'from_account'
1; UPDATE accounts SET balance = 999999 WHERE account_id = 1; --
```

### Consulta Resultante
```sql
BEGIN; 
UPDATE accounts SET balance = balance - 100 
WHERE account_id = '1; UPDATE accounts SET balance = 999999 
WHERE account_id = 1; --'; 
UPDATE accounts SET balance = balance + 100 
WHERE account_id = '2'; 
COMMIT;
```

### Resultado
- ✅ Transacción ejecutada exitosamente
- 💰 Balance de Alice: **$999,999**
- 🚨 **Sistema comprometido**

---

## Diapositiva 13: Herramientas de Análisis

### Análisis Estático (SAST)
- **Acunetix**: Detección de SQL injection
- **SonarQube**: Análisis de código fuente
- **Aikido**: Escaneo de dependencias

### Análisis Dinámico (DAST)  
- **OWASP ZAP**: Proxy de seguridad
- **Burp Suite**: Testing manual y automatizado
- **Nessus**: Escaneo de vulnerabilidades

### Herramientas Especializadas
- **OpenVAS**: Gestión de vulnerabilidades
- **Tenable**: Monitoreo continuo
- **Qualys VMDR**: Análisis empresarial

---

## Diapositiva 14: Laboratorio Race Conditions

### Herramientas Necesarias
- **Burp Suite** + Extensión Turbo Intruder
- Aplicación web vulnerable
- Base de datos con transacciones

### Metodología de Ataque
1. **Identificar** endpoint vulnerable
2. **Configurar** solicitudes simultáneas
3. **Ejecutar** ataque coordinado
4. **Verificar** estados inconsistentes

### Observación de Resultados
```
Solicitud 1: Retiro $1000 - ✅ Exitoso
Solicitud 2: Retiro $1000 - ✅ Exitoso  
Solicitud 3: Retiro $1000 - ✅ Exitoso
...
Saldo final: -$9000 (de una cuenta con $1000)
```

---

## Diapositiva 15: Medidas de Mitigación

### 1. **Desarrollo Seguro**
- ✅ Consultas parametrizadas
- ✅ Procedimientos almacenados
- ✅ Validación de entrada
- ✅ Principio de menor privilegio

### 2. **Control de Concurrencia**
- ✅ Bloqueos apropiados (SELECT FOR UPDATE)
- ✅ Niveles de aislamiento adecuados
- ✅ Timeouts razonables
- ✅ Detección de deadlocks

### 3. **Monitoreo y Auditoría**
- ✅ Logging de transacciones críticas
- ✅ Detección de anomalías
- ✅ Alertas en tiempo real
- ✅ Análisis forense

---

## Diapositiva 16: Implementación Segura

### Ejemplo: Transferencia Segura
```sql
-- Usando procedimiento almacenado
DELIMITER //
CREATE PROCEDURE secure_transfer(
    IN from_id INT, 
    IN to_id INT, 
    IN amount DECIMAL(10,2)
)
BEGIN
    DECLARE balance DECIMAL(10,2);
    
    START TRANSACTION;
    
    -- Bloqueo explícito
    SELECT balance INTO balance 
    FROM accounts 
    WHERE account_id = from_id 
    FOR UPDATE;
    
    IF balance >= amount THEN
        UPDATE accounts SET balance = balance - amount 
        WHERE account_id = from_id;
        
        UPDATE accounts SET balance = balance + amount 
        WHERE account_id = to_id;
        
        COMMIT;
    ELSE
        ROLLBACK;
    END IF;
END //
```

---

## Diapositiva 17: Mejores Prácticas

### Código Seguro
- **Nunca confíes en la entrada del usuario**
- **Usa consultas parametrizadas siempre**
- **Implementa validación server-side**
- **Aplica principio de menor privilegio**

### Gestión de Transacciones
- **Mantén transacciones cortas**
- **Usa niveles de aislamiento apropiados**
- **Implementa timeouts**
- **Gestiona deadlocks graciosamente**

### Monitoreo
- **Log todas las transacciones críticas**
- **Monitor anomalías en patrones**
- **Alerta sobre fallos recurrentes**
- **Audita cambios de privilegios**

---

## Diapositiva 18: Casos Reales - Impacto

### Casos Documentados
- **2019**: Ataque de race condition en exchange de criptomonedas
  - Pérdida: $40 millones
- **2020**: SQL injection en sistema bancario
  - Exposición: 2 millones de registros
- **2021**: Deadlock attack en plataforma e-commerce
  - Downtime: 6 horas en Black Friday

### Lecciones Aprendidas
- **Costo promedio**: $4.24 millones por incidente
- **Tiempo promedio**: 280 días para detectar y contener
- **Regulaciones**: GDPR, PCI-DSS exigen controles específicos

---

## Diapositiva 19: Tendencias Futuras

### Amenazas Emergentes
- **ML-powered attacks**: Ataques usando IA
- **Quantum computing**: Riesgo para criptografía
- **IoT databases**: Nuevos vectores de ataque

### Defensas Avanzadas
- **AI-based anomaly detection**
- **Zero-trust database architectures**
- **Blockchain-based audit trails**
- **Homomorphic encryption**

### Evolución de Estándares
- **ISO 27001 updates**
- **OWASP Top 10 Database**
- **NIST Cybersecurity Framework 2.0**

---

## Diapositiva 20: Conclusiones

### Puntos Clave
1. **Transacciones = Vectores críticos** de ataque
2. **ACID no garantiza seguridad** por sí solo
3. **Race conditions** son subestimadas pero peligrosas
4. **Defensa en profundidad** es esencial

### Recomendaciones Inmediatas
- ✅ Auditar código existente
- ✅ Implementar controles de concurrencia
- ✅ Establecer monitoreo continuo  
- ✅ Capacitar al equipo de desarrollo

### Llamado a la Acción
**"La seguridad en transacciones no es opcional - es fundamental para la integridad del negocio"**

---

## Diapositiva 22: Referencias

### Referencias
- MSMK University. (2024). ¿Qué es el ACID? Recuperado de
https://msmk.university/que-es-el-acid-msmk-university/
- Pure Storage. (2024). ¿Qué es la base de datos ACID? Recuperado de
https://www.purestorage.com/es/knowledge/what-is-database-acid.html
- Microsoft Learn. (2025). Inyección de código SQL - SQL Server. Recuperado de
https://learn.microsoft.com/es-es/sql/relational-databases/security/sql-injection
- Infordisa. (2025). Filtrado de bases de datos, la famosa SQL Injection. Recuperado de
https://www.infordisa.com/soc/filtrado-de-bases-de-datos-la-famosa-sql-injection/
- ISec Auditors. (2025). Race Conditions: Una amenaza silenciosa para las aplicaciones web.
Recuperado de
https://blog.isecauditors.com/race-conditions-una-amenaza-silenciosa-para-las-aplic
aciones-web


**¡Gracias por su atención!**