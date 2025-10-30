

# ⏰ Guía de Estudio Completa — Archivo `06_Eventos.sql`

*(Versión con todas las respuestas y ejercicios prácticos de examen)*

---

## 📘 1. ¿Qué es un EVENTO en MySQL?

Un **EVENTO** en SQL (específicamente en MySQL) es un **proceso programado que se ejecuta automáticamente** en momentos determinados.
Funciona como un **cron job interno**, pero está **dentro del servidor MySQL**.

> 💬 **Definición para examen:**
> “Un evento en MySQL es una tarea programada que ejecuta sentencias SQL automáticamente según una frecuencia o fecha establecida.”

Sirve para:

* Limpiar registros antiguos.
* Actualizar datos automáticamente.
* Recalcular estadísticas o reportes.
* Desactivar promociones vencidas.
* Mantener la base de datos optimizada.

---

### 🧠 Preguntas y respuestas teóricas

> ❓ ¿Cuál es la diferencia entre un **trigger** y un **evento**?
> ✅ El **trigger** se ejecuta por una **acción del usuario** (INSERT, UPDATE o DELETE).
> ✅ El **evento** se ejecuta **por tiempo programado**, sin intervención humana.

> ❓ ¿Por qué son útiles los eventos?
> ✅ Porque automatizan tareas repetitivas, reducen errores humanos y mantienen la base de datos en buen estado.

> ❓ ¿Dónde se almacenan los eventos?
> ✅ En la tabla de metadatos `information_schema.events`.

---

## 🧩 2. Estructura general de un EVENTO

```sql
DELIMITER $$
CREATE EVENT nombre_evento
ON SCHEDULE
    EVERY intervalo
    [STARTS 'YYYY-MM-DD HH:MM:SS']
    [ENDS 'YYYY-MM-DD HH:MM:SS']
DO
BEGIN
    -- Código SQL que se ejecutará automáticamente
END$$
DELIMITER ;
```

### 🧱 Explicación:

| Elemento            | Función                                                |
| ------------------- | ------------------------------------------------------ |
| `ON SCHEDULE EVERY` | Define la **frecuencia** (ej. cada día, semana, hora). |
| `STARTS` / `ENDS`   | Determina el **inicio** y **fin** del evento.          |
| `DO`                | Marca el **bloque de código** a ejecutar.              |
| `DELIMITER $$`      | Permite escribir varias sentencias dentro del bloque.  |

---

### 💬 Preguntas con respuesta

> ❓ ¿Qué pasa si no cambias el delimitador (`DELIMITER $$`)?
> ✅ El motor MySQL interpretará el primer `;` como fin del evento y generará error de sintaxis.

> ❓ ¿Qué tipos de eventos existen?
> ✅ *Recurrentes* (`EVERY 1 DAY/WEEK`) y *únicos* (`ON SCHEDULE AT '2025-03-10 10:00:00'`).

> ❓ ¿Dónde se guardan los metadatos del evento?
> ✅ En `information_schema.events`, con su nombre, estado, fecha de inicio, frecuencia, etc.

---

## 🧮 3. Activar el programador de eventos

```sql
SET GLOBAL event_scheduler = ON;
```

> ⚠️ Si no haces esto, **ningún evento se ejecutará** aunque esté creado.

Verifica su estado:

```sql
SHOW VARIABLES LIKE 'event_scheduler';
```

### 💬 Preguntas con respuesta

> ❓ ¿Qué valor debe tener `event_scheduler` para funcionar?
> ✅ Debe estar en `'ON'`.
> También puedes ponerlo en `'OFF'` (desactivado) o `'DISABLED'` (solo lectura).

> ❓ ¿Quién puede activarlo?
> ✅ Solo usuarios con privilegios de administrador (`SUPER` o `EVENT` global).

---

## ⚙️ 4. Eventos del archivo `06_Eventos.sql`

Vamos evento por evento 👇

---

### 📅 4.1. `ev_ReporteVentasSemanal`

```sql
DELIMITER $$
CREATE EVENT ev_ReporteVentasSemanal
ON SCHEDULE
    EVERY 1 WEEK
    STARTS CURRENT_TIMESTAMP
DO
BEGIN
    INSERT INTO reporte_ventas_semanal (semana, total_ventas, monto_total)
    SELECT
        WEEK(fecha_venta) AS semana,
        COUNT(*) AS total_ventas,
        SUM(total) AS monto_total
    FROM ventas
    WHERE YEAR(fecha_venta) = YEAR(CURDATE())
    GROUP BY WEEK(fecha_venta)
    ON DUPLICATE KEY UPDATE
        total_ventas = VALUES(total_ventas),
        monto_total = VALUES(monto_total);
END$$
DELIMITER ;
```

🧠 **Qué hace:**
Cada semana genera o actualiza un resumen de ventas semanales.

---

#### 💬 Preguntas con respuesta

> ❓ ¿Qué diferencia hay entre `CURRENT_TIMESTAMP` y `NOW()`?
> ✅ Son equivalentes en MySQL. Devuelven fecha y hora actuales. `CURRENT_TIMESTAMP` es estándar SQL.

> ❓ ¿Qué hace `ON DUPLICATE KEY UPDATE`?
> ✅ Si la fila ya existe (por la clave primaria o única), la **actualiza** en lugar de duplicarla.

> ❓ ¿Qué función obtiene la semana de una fecha?
> ✅ `WEEK(fecha)`.

---

### 🧹 4.2. `ev_LimpiezaDiariaTemporales`

```sql
DELIMITER $$
CREATE EVENT ev_LimpiezaDiariaTemporales
ON SCHEDULE EVERY 1 DAY
DO
BEGIN
    DELETE FROM logs_temporales WHERE fecha < DATE_SUB(NOW(), INTERVAL 7 DAY);
    DELETE FROM sesiones_temporales WHERE fecha_expiracion < NOW();
END$$
DELIMITER ;
```

🧠 **Qué hace:**
Borra registros viejos (más de 7 días) de logs y sesiones temporales.

---

#### 💬 Preguntas con respuesta

> ❓ ¿Por qué usar un evento?
> ✅ Evita hacerlo manualmente; mantiene la base limpia automáticamente.

> ❓ ¿Qué hace `DATE_SUB()`?
> ✅ Resta un intervalo de tiempo a una fecha.
> Ejemplo: `DATE_SUB(NOW(), INTERVAL 7 DAY)` = fecha de hace 7 días.

> ❓ ¿Qué pasa si el `event_scheduler` está apagado?
> ✅ El evento no se ejecuta hasta que se vuelva a activar.

---

### 💰 4.3. `ev_DesactivarPromocionesExpiradas`

```sql
DELIMITER $$
CREATE EVENT ev_DesactivarPromocionesExpiradas
ON SCHEDULE EVERY 1 HOUR
DO
BEGIN
    UPDATE promociones
    SET activa = 0
    WHERE fecha_fin < NOW() AND activa = 1;
END$$
DELIMITER ;
```

🧠 **Qué hace:**
Desactiva automáticamente promociones vencidas cada hora.

---

#### 💬 Preguntas con respuesta

> ❓ ¿Por qué cada hora y no diario?
> ✅ Las promociones pueden expirar a cualquier hora, no solo a medianoche.

> ❓ ¿Qué impacto tiene no tener índice en `fecha_fin`?
> ✅ Las consultas serán lentas (Full Table Scan).
> Se recomienda un índice en `fecha_fin`.

> ❓ ¿Qué ventaja tiene automatizar esto?
> ✅ Evita que queden promociones activas después de su fecha de fin.

---

### 📦 4.4. `ev_RecalcularNivelLealtadClientes`

```sql
DELIMITER $$
CREATE EVENT ev_RecalcularNivelLealtadClientes
ON SCHEDULE EVERY 1 DAY
STARTS CURRENT_DATE + INTERVAL 1 DAY
DO
BEGIN
    UPDATE clientes c
    JOIN (
        SELECT
            v.clientes_id_cliente AS id_cliente,
            SUM(dv.cantidad * dv.precio_unitario_congelado) AS total_gasto
        FROM ventas v
        JOIN detalle_ventas dv ON dv.ventas_id_ventas = v.id_ventas
        WHERE v.fecha_venta >= DATE_SUB(NOW(), INTERVAL 1 YEAR)
        GROUP BY v.clientes_id_cliente
    ) t ON t.id_cliente = c.id_cliente
    SET c.nivel_lealtad = CASE
        WHEN t.total_gasto <  500 THEN 'Bronce'
        WHEN t.total_gasto < 2000 THEN 'Plata'
        ELSE 'Oro'
    END;
END$$
DELIMITER ;
```

🧠 **Qué hace:**
Cada día recalcula el nivel de lealtad del cliente según su gasto del último año.

---

#### 💬 Preguntas con respuesta

> ❓ ¿Qué diferencia hay entre `CASE` y `IF`?
> ✅ `CASE` se usa para múltiples condiciones en bloque.
> `IF` es más simple, pero menos ordenado con varios rangos.

> ❓ ¿Por qué se usa `JOIN`?
> ✅ Porque permite actualizar todos los clientes de una sola vez con una subconsulta agregada.

> ❓ ¿Qué pasa si un cliente no tiene compras?
> ✅ No aparecerá en la subconsulta, y su nivel podría quedar igual o necesitar valor por defecto (ej. “Bronce”).

---

### 🏗️ 4.5. `ev_RebuildIndicesSemanal`

```sql
DELIMITER $$
CREATE EVENT ev_RebuildIndicesSemanal
ON SCHEDULE EVERY 1 WEEK
DO
BEGIN
    OPTIMIZE TABLE productos;
    OPTIMIZE TABLE ventas;
    OPTIMIZE TABLE detalle_ventas;
END$$
DELIMITER ;
```

🧠 **Qué hace:**
Reindexa y optimiza las tablas semanalmente para mejorar rendimiento.

---

#### 💬 Preguntas con respuesta

> ❓ ¿Qué hace `OPTIMIZE TABLE`?
> ✅ Reconstruye índices, libera espacio y mejora la velocidad de consultas.

> ❓ ¿Cuándo ejecutarlo?
> ✅ En horarios de baja carga, porque puede bloquear las tablas temporalmente.

---

### 🧾 4.6. `ev_RegistrarErrores_Eventos`

```sql
DELIMITER $$
CREATE EVENT ev_RegistrarErrores_Eventos
ON SCHEDULE EVERY 12 HOUR
DO
BEGIN
    INSERT INTO logs_eventos (nombre_evento, fecha, estado)
    SELECT event_name, NOW(), 'Revisado'
    FROM information_schema.events
    WHERE status = 'DISABLED';
END$$
DELIMITER ;
```

🧠 **Qué hace:**
Detecta eventos deshabilitados y los registra para control.

---

#### 💬 Preguntas con respuesta

> ❓ ¿Qué es `information_schema`?
> ✅ Un esquema especial que guarda información sobre todas las estructuras del servidor (tablas, triggers, eventos, etc.).

> ❓ ¿Por qué registrar eventos deshabilitados?
> ✅ Para detectar fallas o eventos desactivados accidentalmente.

---

### 🎂 4.7. `ev_MarcarCumpleaniosHoy`

```sql
DELIMITER $$
CREATE EVENT ev_MarcarCumpleaniosHoy
ON SCHEDULE EVERY 1 DAY
DO
BEGIN
    UPDATE clientes
    SET cumple_hoy = 1
    WHERE DATE_FORMAT(fecha_nacimiento, '%m-%d') = DATE_FORMAT(NOW(), '%m-%d');
END$$
DELIMITER ;
```

🧠 **Qué hace:**
Marca a los clientes que cumplen años hoy.

---

#### 💬 Preguntas con respuesta

> ❓ ¿Qué hace `DATE_FORMAT()`?
> ✅ Convierte la fecha a un texto con formato.
> Ejemplo: `DATE_FORMAT('2025-10-30', '%m-%d')` → `'10-30'`.

> ❓ ¿Por qué comparar solo `%m-%d` y no el año?
> ✅ Porque el año de nacimiento no cambia; solo interesa si **hoy coincide en mes y día**.

> ❓ ¿Cómo se “limpia” para el siguiente día?
> ✅ Se puede agregar al inicio:
>
> ```sql
> UPDATE clientes SET cumple_hoy = 0;
> ```

---

## ⚙️ 5. Posibles ejercicios de examen (con solución)

Estos son los **casos más típicos** que pueden pedirte crear en examen:

---

### 🧩 1. Crear un evento que elimine facturas con más de 5 años

```sql
CREATE EVENT ev_LimpiarFacturasAntiguas
ON SCHEDULE EVERY 1 DAY
DO
BEGIN
    DELETE FROM facturas
    WHERE fecha_emision < DATE_SUB(CURDATE(), INTERVAL 5 YEAR);
END;
```

**Explicación:** elimina cada día las facturas con más de 5 años.

---

### 🧩 2. Crear un evento que inserte un registro de auditoría diario

```sql
CREATE EVENT ev_AuditoriaDiaria
ON SCHEDULE EVERY 1 DAY
DO
BEGIN
    INSERT INTO auditoria (fecha, descripcion)
    VALUES (NOW(), 'Verificación diaria del sistema');
END;
```

**Explicación:** registra una marca diaria de actividad (útil para logs).

---

### 🧩 3. Crear un evento que calcule el total mensual de ventas

```sql
CREATE EVENT ev_ResumenMensualVentas
ON SCHEDULE EVERY 1 MONTH
DO
BEGIN
    INSERT INTO resumen_mensual (mes, total_ventas, monto_total)
    SELECT MONTH(fecha_venta), COUNT(*), SUM(total)
    FROM ventas
    WHERE YEAR(fecha_venta) = YEAR(CURDATE())
    GROUP BY MONTH(fecha_venta);
END;
```

**Explicación:** inserta cada mes los totales del mes anterior en una tabla resumen.

---

### 🧩 4. Crear un evento que desactive usuarios inactivos por más de 6 meses

```sql
CREATE EVENT ev_DesactivarInactivos
ON SCHEDULE EVERY 1 WEEK
DO
BEGIN
    UPDATE usuarios
    SET activo = 0
    WHERE ultimo_login < DATE_SUB(CURDATE(), INTERVAL 6 MONTH);
END;
```

**Explicación:** automatiza la gestión de usuarios inactivos.

---

## 🎯 6. Claves para responder en examen oral o escrito

1. Empieza con la definición:

   > “Un evento es una tarea programada dentro de MySQL que ejecuta instrucciones de forma automática según una frecuencia o fecha definida.”

2. Menciona que depende de `event_scheduler = ON`.

3. Da un ejemplo práctico (limpieza o actualización automática).

4. Explica brevemente la estructura (`CREATE EVENT`, `ON SCHEDULE`, `DO BEGIN…END`).

5. Cierra con una **buena práctica** (verificar logs o ejecutar fuera de horas pico).

---
