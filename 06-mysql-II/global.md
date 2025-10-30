Perfecto 🌟  
Aquí tienes el **resumen maestro final** — el **mapa de estudio integral** de todo tu proyecto de base de datos.  
Está hecho en formato **Markdown**, con una estructura clara, jerárquica y 100 % enfocada en **repaso previo a examen**: incluye **conceptos clave**, **resumen por módulo**, **mapa mental textual**, **errores típicos** y **preguntas globales**.

---

# 🧭 MAPA DE ESTUDIO INTEGRAL — Proyecto de Base de Datos Completo

> 🧠 Objetivo: dominar todos los temas del proyecto — desde diseño hasta seguridad — para **entender, explicar y defender** cada parte en examen.

---

## 🏗️ 1. ESQUEMA Y DATOS (01_Esquema_y_Datos.sql)

### 📘 Concepto
El **esquema** es la estructura base de la BD: define **tablas**, **campos**, **relaciones** y **restricciones**.

### 🧩 Contenido clave
- **Tablas principales**: `clientes`, `productos`, `categorias`, `ventas`, `detalle_ventas`, `promociones`, `carrito`.
- **Relaciones**:
  - `1:N` → un cliente puede tener muchas ventas.  
  - `N:M` → productos ↔ promociones.  
- **Claves primarias (PK)** → `id_*`.  
- **Claves foráneas (FK)** → conectan tablas.  
- **Restricciones**: `NOT NULL`, `UNIQUE`, `CHECK`, `DEFAULT`, `AUTO_INCREMENT`.

### ⚙️ Ejemplo
```sql
CREATE TABLE clientes (
  id_cliente INT AUTO_INCREMENT PRIMARY KEY,
  nombre VARCHAR(50) NOT NULL,
  correo VARCHAR(100) UNIQUE,
  fecha_nacimiento DATE
);
```

### 📚 Conceptos reforzados
- Integridad referencial.  
- Tipos de datos (`INT`, `DECIMAL`, `DATE`, `VARCHAR`).  
- Normalización (1FN, 2FN, 3FN).

### ⚠️ Errores comunes
- No definir claves primarias.  
- Repetir datos que deberían normalizarse.  
- Usar `CHAR` en lugar de `VARCHAR`.

### 🎯 Posibles preguntas
- ¿Qué es una clave primaria?  
- ¿Qué diferencia hay entre PK y FK?  
- ¿Qué significa normalizar una base de datos?

---

## 🔍 2. CONSULTAS AVANZADAS (02_Consultas_Avanzadas.sql)

### 📘 Concepto
Consultas que combinan tablas, agregan resultados y generan reportes o KPIs.

### 🧩 Temas clave
- `JOIN` (INNER, LEFT, RIGHT).  
- `GROUP BY`, `HAVING`.  
- Funciones de agregación: `SUM`, `AVG`, `COUNT`.  
- Subconsultas.  
- `ORDER BY`, `LIMIT`, `BETWEEN`.

### ⚙️ Ejemplo
```sql
SELECT c.nombre, SUM(v.total) AS gasto_total
FROM clientes c
JOIN ventas v ON v.clientes_id_cliente = c.id_cliente
GROUP BY c.id_cliente
ORDER BY gasto_total DESC;
```

### 💡 Conceptos reforzados
- Agregaciones por grupo.  
- Joins para combinar datos relacionados.  
- Filtrado sobre resultados agregados (`HAVING`).

### ⚠️ Errores comunes
- Usar `WHERE` en lugar de `HAVING`.  
- Olvidar `GROUP BY`.  
- No limitar resultados (`LIMIT`).

### 🎯 Preguntas
- ¿Qué diferencia hay entre `JOIN` y `INNER JOIN`?  
- ¿Qué función usarías para promedios o totales?  
- ¿Qué pasa si quitas el `GROUP BY`?

---

## 🧮 3. FUNCIONES (03_Funciones.sql)

### 📘 Concepto
Bloques de código SQL que **reciben parámetros y devuelven un valor**.

### 🧩 Temas clave
- `CREATE FUNCTION nombre(...) RETURNS tipo`.  
- Variables locales (`DECLARE`).  
- Sentencias `RETURN`.  
- Uso dentro de consultas (`SELECT nombre_funcion()`).

### ⚙️ Ejemplo
```sql
DELIMITER $$
CREATE FUNCTION fn_CalcularIVA(precio DECIMAL(10,2))
RETURNS DECIMAL(10,2)
BEGIN
  RETURN precio * 0.16;
END$$
DELIMITER ;
```

### 💡 Conceptos reforzados
- Funciones deterministas (mismo input → mismo output).  
- Diferencias entre funciones y procedimientos.  
- Tipos de retorno.

### ⚠️ Errores comunes
- No definir el tipo de retorno (`RETURNS`).  
- Olvidar `RETURN`.  
- Usar `SELECT` dentro de funciones sin `INTO`.

### 🎯 Preguntas
- ¿Qué diferencia hay entre una función y un procedimiento?  
- ¿Qué hace el `DELIMITER`?  
- ¿Por qué no deben modificar datos las funciones?

---

## ⚙️ 4. TRIGGERS (05_Triggers.sql)

### 📘 Concepto
Bloques que se **disparan automáticamente** al producirse eventos en una tabla.

### 🧩 Tipos
- `BEFORE INSERT/UPDATE/DELETE`  
- `AFTER INSERT/UPDATE/DELETE`

### ⚙️ Ejemplo
```sql
CREATE TRIGGER trg_ActualizarStock
AFTER INSERT ON detalle_ventas
FOR EACH ROW
UPDATE productos
SET stock = stock - NEW.cantidad
WHERE id_producto = NEW.productos_id_producto;
```

### 💡 Conceptos reforzados
- `NEW` y `OLD` para acceder a valores nuevos o anteriores.  
- `SIGNAL SQLSTATE '45000'` para lanzar errores.  
- Triggers de auditoría o integridad.

### ⚠️ Errores comunes
- No usar `FOR EACH ROW`.  
- Olvidar `DELIMITER`.  
- Crear bucles recursivos involuntarios.

### 🎯 Preguntas
- ¿Qué diferencia hay entre `BEFORE` y `AFTER`?  
- ¿Qué son `NEW` y `OLD`?  
- ¿Cómo lanzarías un error personalizado?

---

## ⏰ 5. EVENTOS (06_Eventos.sql)

### 📘 Concepto
Tareas **programadas** que ejecutan sentencias automáticamente cada cierto tiempo.

### ⚙️ Ejemplo
```sql
CREATE EVENT ev_LimpiezaDiaria
ON SCHEDULE EVERY 1 DAY
DO
DELETE FROM logs_temporales WHERE fecha < DATE_SUB(NOW(), INTERVAL 7 DAY);
```

### 💡 Conceptos reforzados
- `SET GLOBAL event_scheduler = ON`.  
- `EVERY`, `STARTS`, `ENDS`.  
- `NOW()`, `DATE_SUB()`, `CURDATE()`.

### ⚠️ Errores comunes
- Olvidar activar el event scheduler.  
- No usar `DELIMITER` en eventos con `BEGIN…END`.  
- No controlar conflictos entre eventos simultáneos.

### 🎯 Preguntas
- ¿Qué diferencia hay entre un evento y un trigger?  
- ¿Cómo se activa el programador de eventos?  
- ¿Qué tareas automatizarías con un evento?

---

## 🧩 6. PROCEDIMIENTOS ALMACENADOS (07_Procedimientos_Almacenados.sql)

### 📘 Concepto
Bloques SQL **almacenados y ejecutables** que realizan operaciones complejas.

### ⚙️ Ejemplo
```sql
CREATE PROCEDURE sp_RegistrarVenta(
    IN p_id_cliente INT,
    IN p_fecha DATE,
    OUT p_id_venta INT
)
BEGIN
    INSERT INTO ventas (clientes_id_cliente, fecha_venta, total)
    VALUES (p_id_cliente, p_fecha, 0);
    SET p_id_venta = LAST_INSERT_ID();
END;
```

### 💡 Conceptos reforzados
- Tipos de parámetros: `IN`, `OUT`, `INOUT`.  
- Uso de `DECLARE`, `SET`, `IF`, `CASE`.  
- Control transaccional.  
- Reutilización de lógica.

### ⚠️ Errores comunes
- No usar `DELIMITER`.  
- Falta de `INTO` en `SELECT`.  
- No manejar `NULL` ni errores.

### 🎯 Preguntas
- ¿Qué diferencia hay entre un SP y una función?  
- ¿Qué hace `LAST_INSERT_ID()`?  
- ¿Por qué se prefieren SPs para lógica de negocio?

---

## 🔐 7. SEGURIDAD (04_Seguridad.sql)

### 📘 Concepto
Controla **quién puede hacer qué** dentro del sistema de base de datos.

### 🧩 Comandos principales
- `CREATE USER 'nombre'@'host' IDENTIFIED BY 'pass';`  
- `GRANT permisos ON base.tabla TO 'usuario'@'host';`  
- `REVOKE permisos ON base.tabla FROM 'usuario'@'host';`  
- `CREATE ROLE rol; GRANT rol TO usuario;`

### 💡 Conceptos reforzados
- Tipos de privilegios (`SELECT`, `INSERT`, `DELETE`, `EXECUTE`).  
- Roles (`CREATE ROLE`, `GRANT`, `SET DEFAULT ROLE`).  
- Principio del menor privilegio.  
- Auditoría de accesos.

### ⚠️ Errores comunes
- Usar `root` para todo.  
- Olvidar `REVOKE` o `DROP USER`.  
- Permitir conexiones desde `%` sin control.

### 🎯 Preguntas
- ¿Qué hace `GRANT` y `REVOKE`?  
- ¿Qué diferencia hay entre usuario y rol?  
- ¿Por qué es peligroso usar `%`?

---

## 🧩 8. RESUMEN GENERAL DEL PROYECTO

| Módulo | Qué hace | Palabras clave | Ejemplo práctico |
|---------|-----------|----------------|------------------|
| Esquema y Datos | Define estructura y relaciones | `CREATE TABLE`, `FK`, `PK` | Tablas `clientes`, `productos` |
| Consultas Avanzadas | Extrae y analiza datos | `JOIN`, `GROUP BY`, `HAVING` | Top clientes |
| Funciones | Devuelven un valor | `CREATE FUNCTION`, `RETURN` | `fn_CalcularIVA()` |
| Procedimientos | Ejecutan lógica | `CREATE PROCEDURE`, `CALL` | `sp_RegistrarVenta()` |
| Triggers | Se disparan por acción | `BEFORE`, `AFTER`, `SIGNAL` | Validar stock |
| Eventos | Automatizan tareas | `CREATE EVENT`, `EVERY` | Limpiar logs diarios |
| Seguridad | Controla acceso | `CREATE USER`, `GRANT`, `ROLE` | Usuario vendedor |

---

## 🧠 9. PREGUNTAS GLOBALES DE EXAMEN

1. ¿Cuál es la diferencia entre una **función**, un **procedimiento** y un **trigger**?  
2. ¿Qué cláusula usarías para agrupar resultados en una consulta?  
3. ¿Qué diferencia hay entre **`WHERE`** y **`HAVING`**?  
4. ¿Qué tipos de **relaciones** existen entre tablas?  
5. ¿Cómo automatizas tareas en la base de datos sin intervención humana?  
6. ¿Qué es el **principio del menor privilegio**?  
7. ¿Cómo aseguras la **integridad referencial**?  
8. ¿Qué comando activa el **event scheduler**?  
9. ¿Qué diferencia hay entre un **rol** y un **usuario**?  
10. ¿Por qué se deben usar **triggers y SPs** para garantizar consistencia lógica?  

---

## 🧩 10. MAPA MENTAL TEXTUAL (Resumen rápido)

```
📦 Esquema → Tablas → PK/FK → Relaciones
🔍 Consultas → JOINs → GROUP BY → Funciones agregadas
🧮 Funciones → RETURN → lógica puntual (devuelven valor)
⚙️ Procedimientos → CALL → lógica completa (transacciones)
🚨 Triggers → BEFORE/AFTER → validan o auditan acciones
⏰ Eventos → tareas automáticas → limpieza, reportes
🔐 Seguridad → usuarios, roles, permisos → GRANT/REVOKE
```

---

## ⚡ 11. ERRORES MÁS COMUNES EN TODO EL PROYECTO

- No usar `DELIMITER` en SPs, triggers o eventos.  
- Confundir `HAVING` con `WHERE`.  
- Olvidar `GROUP BY`.  
- Permitir `root` o `%` en usuarios.  
- No actualizar totales o stock.  
- Ignorar auditoría o backups.  

---

## 🏁 12. CONSEJO FINAL PARA SACAR 100

🔹 Aprende **al menos un ejemplo práctico** de cada módulo.  
🔹 Memoriza la **sintaxis base** de:
  - `CREATE FUNCTION`
  - `CREATE PROCEDURE`
  - `CREATE TRIGGER`
  - `CREATE EVENT`
  - `GRANT / REVOKE`
🔹 Usa palabras clave en tus respuestas:  
  - “Automatización”, “Integridad”, “Auditoría”, “Control de acceso”.  
🔹 Muestra siempre **relaciones entre partes**:  
  - Triggers → controlan integridad.  
  - Procedimientos → agrupan lógica.  
  - Eventos → automatizan.  
  - Seguridad → protege todo.

---
