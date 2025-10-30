¡Hecho! Aquí tienes **“otras opciones”** (variantes y extras) para **cada archivo** de tu proyecto. Son alternativas de diseño, enfoques distintos que también serían válidos en un examen, y ejercicios adicionales con código listo para copiar/pegar.

---

# 01_Esquema_y_Datos.sql — Otras opciones

## 🔁 Modelado y claves

* **Claves naturales vs. surrogate keys (AUTO_INCREMENT):**

  ```sql
  -- Natural: correo único como PK (siempre y cuando sea realmente único y estable)
  CREATE TABLE clientes (
    correo VARCHAR(100) PRIMARY KEY,
    nombre  VARCHAR(100) NOT NULL,
    telefono VARCHAR(20),
    fecha_nacimiento DATE
  );
  ```
* **PK compuesta en detalle de ventas (evita id_detalle artificial):**

  ```sql
  CREATE TABLE detalle_ventas (
    ventas_id_ventas     INT,
    productos_id_producto INT,
    PRIMARY KEY (ventas_id_ventas, productos_id_producto),
    cantidad INT NOT NULL,
    precio_unitario_congelado DECIMAL(10,2) NOT NULL,
    FOREIGN KEY (ventas_id_ventas) REFERENCES ventas(id_ventas),
    FOREIGN KEY (productos_id_producto) REFERENCES productos(id_producto)
  );
  ```

## 🧱 Integridad y reglas

* **Acciones en cascada explícitas:**

  ```sql
  ALTER TABLE ventas
    ADD CONSTRAINT fk_ventas_cliente
    FOREIGN KEY (clientes_id_cliente) REFERENCES clientes(id_cliente)
    ON DELETE RESTRICT ON UPDATE CASCADE;
  ```
* **CHECKs (MySQL 8.0.16+):**

  ```sql
  ALTER TABLE productos
    ADD CONSTRAINT chk_precio_pos CHECK (precio >= 0),
    ADD CONSTRAINT chk_stock_pos  CHECK (stock >= 0);
  ```

## ⚡ Rendimiento e índices

* **Índices compuestos según consultas típicas:**

  ```sql
  CREATE INDEX ix_ventas_cliente_fecha ON ventas (clientes_id_cliente, fecha_venta);
  CREATE INDEX ix_detalle_ventas_prod ON detalle_ventas (productos_id_producto, ventas_id_ventas);
  ```
* **Columnas generadas para búsqueda (por ejemplo, SKU “normalizado”):**

  ```sql
  ALTER TABLE productos
    ADD nombre_slug VARCHAR(120) GENERATED ALWAYS AS (REPLACE(LOWER(nombre),' ','-')) STORED,
    ADD INDEX ix_slug (nombre_slug);
  ```

## 🗂️ Extras de modelado

* **Soft-delete con marca lógica:**

  ```sql
  ALTER TABLE clientes ADD activo TINYINT(1) NOT NULL DEFAULT 1;
  ```
* **Historial de precios de producto (para auditoría de cambios):**

  ```sql
  CREATE TABLE historial_precios (
    id INT AUTO_INCREMENT PRIMARY KEY,
    id_producto INT NOT NULL,
    precio DECIMAL(10,2) NOT NULL,
    vigente_desde DATETIME NOT NULL,
    FOREIGN KEY (id_producto) REFERENCES productos(id_producto)
  );
  ```

---

# 02_Consultas_Avanzadas.sql — Otras opciones

## 🪟 Ventanas y ranking (MySQL 8+)

* **Top-N por grupo (producto más vendido por mes):**

  ```sql
  WITH ventas_mes AS (
    SELECT
      DATE_FORMAT(v.fecha_venta,'%Y-%m') AS mes,
      p.id_producto, p.nombre,
      SUM(dv.cantidad) AS total_unidades,
      ROW_NUMBER() OVER (PARTITION BY DATE_FORMAT(v.fecha_venta,'%Y-%m')
                         ORDER BY SUM(dv.cantidad) DESC) AS rn
    FROM ventas v
    JOIN detalle_ventas dv ON dv.ventas_id_ventas = v.id_ventas
    JOIN productos p ON p.id_producto = dv.productos_id_producto
    GROUP BY mes, p.id_producto, p.nombre
  )
  SELECT * FROM ventas_mes WHERE rn = 1;
  ```
* **Acumulados (rolling sum) por cliente:**

  ```sql
  SELECT
    v.clientes_id_cliente,
    v.fecha_venta,
    v.total,
    SUM(v.total) OVER (PARTITION BY v.clientes_id_cliente
                       ORDER BY v.fecha_venta
                       ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS acumulado
  FROM ventas v;
  ```

## 🔄 Anti-joins y EXISTS

* **Clientes sin compras (alternativa a LEFT JOIN … IS NULL):**

  ```sql
  SELECT c.*
  FROM clientes c
  WHERE NOT EXISTS (
    SELECT 1 FROM ventas v WHERE v.clientes_id_cliente = c.id_cliente
  );
  ```

## 🧮 Pivot simple con agregación condicional

```sql
SELECT
  p.categoria,
  SUM(CASE WHEN MONTH(v.fecha_venta)=1 THEN dv.cantidad ELSE 0 END) AS jan,
  SUM(CASE WHEN MONTH(v.fecha_venta)=2 THEN dv.cantidad ELSE 0 END) AS feb,
  ...
FROM productos p
JOIN detalle_ventas dv ON dv.productos_id_producto = p.id_producto
JOIN ventas v ON v.id_ventas = dv.ventas_id_ventas
GROUP BY p.categoria;
```

## ⚡ Rendimiento

* Preferir filtros sargables:

  ```sql
  -- Evita WHERE DATE(fecha_venta) = '2025-10-30'
  -- Mejor:
  WHERE fecha_venta >= '2025-10-30' AND fecha_venta < '2025-10-31'
  ```

---

# 03_Funciones.sql — Otras opciones

## 🧩 Funciones utilitarias extra

* **Edad desde fecha de nacimiento (años completos):**

  ```sql
  DELIMITER $$
  CREATE FUNCTION fn_Edad(p_fecha DATE) RETURNS INT DETERMINISTIC
  RETURN TIMESTAMPDIFF(YEAR, p_fecha, CURDATE()) - (DATE_FORMAT(CURDATE(),'%m-%d') < DATE_FORMAT(p_fecha,'%m-%d'));
  $$
  DELIMITER ;
  ```
* **Normalizar texto (trim + colapsar espacios):**

  ```sql
  DELIMITER $$
  CREATE FUNCTION fn_NormalizarTexto(p_txt VARCHAR(255))
  RETURNS VARCHAR(255) DETERMINISTIC
  RETURN REGEXP_REPLACE(TRIM(p_txt),'\\s+',' ');
  $$
  DELIMITER ;
  ```

## 🔐 Seguridad de funciones

* **Ejecución con definidor/invocador:**

  ```sql
  CREATE DEFINER=`admin`@`%` FUNCTION fn_...() RETURNS INT
  SQL SECURITY DEFINER
  ...
  ```

  > *Útil cuando el invocador no tiene permisos de lectura sobre ciertas tablas.*

## ⚠️ Nota de MySQL

* **Funciones no deben modificar datos** (usa SP/trigger/evento si necesitas DML).
* Marca **DETERMINISTIC** si no dependes de hora/estado; **READS SQL DATA** si solo lees.

---

# 04_Seguridad.sql — Otras opciones

## 🔐 Políticas y roles

* **Política de contraseñas y caducidad:**

  ```sql
  ALTER USER 'juan'@'localhost'
    IDENTIFIED BY 'C0ntra$eGuRa'
    PASSWORD EXPIRE INTERVAL 90 DAY
    FAILED_LOGIN_ATTEMPTS 5
    PASSWORD_LOCK_TIME 2; -- horas
  ```
* **Requerir SSL:**

  ```sql
  ALTER USER 'reportes'@'%' REQUIRE SSL;
  ```

## 👤 Definición y vistas seguras

* **SQL SECURITY en vistas/procedimientos:**

  ```sql
  CREATE DEFINER=`admin`@`%`
  SQL SECURITY DEFINER
  VIEW vista_clientes_publica AS
  SELECT nombre, correo FROM clientes;
  ```
* **Row-level security “manual” con vistas filtradas por rol/atributo:**

  ```sql
  CREATE VIEW ventas_region AS
  SELECT * FROM ventas WHERE region = SUBSTRING_INDEX(CURRENT_USER(),'@',1); -- ej. si usuario contiene región
  ```

## 🧾 Auditoría mínima sin plugins

* **Tabla + triggers de auditoría (DML):** ya tienes ejemplos; agrega UPDATE/DELETE en ventas/productos con `CURRENT_USER()`.

---

# 05_Triggers.sql — Otras opciones

## 🕒 Timestamps automáticos

```sql
ALTER TABLE productos
  ADD created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
  ADD updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP;
-- O con trigger si necesitas lógica adicional:
DELIMITER $$
CREATE TRIGGER tr_set_updated_at
BEFORE UPDATE ON productos
FOR EACH ROW
BEGIN
  SET NEW.updated_at = NOW();
END $$
DELIMITER ;
```

## 🧰 Validaciones adicionales

* **Precio no negativo y redondeo:**

  ```sql
  DELIMITER $$
  CREATE TRIGGER tr_val_precio
  BEFORE INSERT ON productos
  FOR EACH ROW
  BEGIN
    IF NEW.precio < 0 THEN
      SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT='Precio inválido';
    END IF;
    SET NEW.precio = ROUND(NEW.precio,2);
  END$$
  DELIMITER ;
  ```

## 🚫 Anti-recursión / colisiones

* Evita que un trigger actualice la **misma tabla** que lo dispara si puede provocar bucles.
* Usa una **tabla de cola** si necesitas procesos asíncronos (y luego un EVENT que vacíe la cola).

---

# 06_Eventos.sql — Otras opciones

## ⏰ Variantes de programación

* **Evento único en fecha/hora exacta:**

  ```sql
  CREATE EVENT ev_cierre_trimestre
  ON SCHEDULE AT '2025-12-31 23:59:00'
  DO BEGIN
    CALL sp_CerrarTrimestre();
  END;
  ```
* **Evento con ventana temporal (STARTS/ENDS):**

  ```sql
  CREATE EVENT ev_promo_navidad
  ON SCHEDULE EVERY 1 DAY
  STARTS '2025-12-01 00:00:00'
  ENDS   '2025-12-26 00:00:00'
  DO BEGIN
    UPDATE promociones SET activa=1 WHERE codigo='XMAS';
  END;
  ```

## 🧷 Idempotencia y logs

* **Evitar duplicados (marca de control):**

  ```sql
  INSERT INTO procesos_ejecutados (proceso, fecha) 
  SELECT 'resumen_mensual', CURDATE()
  WHERE NOT EXISTS (
    SELECT 1 FROM procesos_ejecutados WHERE proceso='resumen_mensual' AND fecha=CURDATE()
  );
  ```

## 🧯 Control operativo

* **Habilitar/Deshabilitar sin borrar:**

  ```sql
  ALTER EVENT ev_ReporteVentasSemanal DISABLE;
  ALTER EVENT ev_ReporteVentasSemanal ENABLE;
  ```

---

# 07_Procedimientos_Almacenados.sql — Otras opciones

## 🔁 Transacciones + manejo de errores

```sql
DELIMITER $$
CREATE PROCEDURE sp_RegistrarVentaCompleta(
  IN p_cliente INT,
  IN p_items_json JSON   -- por ejemplo: [{"prod":1,"cant":2,"precio":10000}, ...]
)
BEGIN
  DECLARE EXIT HANDLER FOR SQLEXCEPTION
  BEGIN
    ROLLBACK;
    SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT='Error registrando la venta';
  END;

  START TRANSACTION;

  INSERT INTO ventas (clientes_id_cliente, fecha_venta, total) VALUES (p_cliente, NOW(), 0);
  SET @id := LAST_INSERT_ID();

  -- iterar JSON (MySQL 8+) con JSON_TABLE si aplica, o análogamente con tabla temporal
  -- ... insertar en detalle_ventas, actualizar stock, etc.

  -- recalcular total
  UPDATE ventas
  SET total = (
    SELECT SUM(cantidad * precio_unitario_congelado)
    FROM detalle_ventas WHERE ventas_id_ventas=@id
  )
  WHERE id_ventas=@id;

  COMMIT;
END $$
DELIMITER ;
```

## 🧵 Cursors y loops (ejemplo simple)

```sql
DELIMITER $$
CREATE PROCEDURE sp_RevaluarPrecios(IN p_factor DECIMAL(5,2))
BEGIN
  DECLARE done INT DEFAULT 0;
  DECLARE v_id INT;
  DECLARE cur CURSOR FOR SELECT id_producto FROM productos;
  DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

  OPEN cur;
  read_loop: LOOP
    FETCH cur INTO v_id;
    IF done THEN LEAVE read_loop; END IF;
    UPDATE productos SET precio = ROUND(precio * p_factor, 2) WHERE id_producto = v_id;
  END LOOP;
  CLOSE cur;
END $$
DELIMITER ;
```

## 🧰 SQL dinámico (cuando la tabla/columna es parámetro)

```sql
DELIMITER $$
CREATE PROCEDURE sp_ReporteDinamico(IN p_tabla VARCHAR(64))
BEGIN
  SET @sql = CONCAT('SELECT COUNT(*) AS filas FROM ', QUOTE_IDENTIFIER(p_tabla));
  PREPARE stmt FROM @sql;
  EXECUTE stmt;
  DEALLOCATE PREPARE stmt;
END $$
DELIMITER ;
```

> *NOTA:* `QUOTE_IDENTIFIER` no existe como tal; en MySQL puro se usan backticks con cuidado (valida lista blanca de tablas antes de concatenar para evitar inyección).

---

## 🧪 Ejercicios adicionales (todos evaluables en examen)

* **Esquema:** agrega `ON DELETE SET NULL` en `detalle_ventas` y explica por qué (preservar facturas aunque se borre el producto).
* **Consultas:** “Top 3 productos por categoría” con `DENSE_RANK()`.
* **Funciones:** `fn_ClasificarTicket(total)` que devuelva `'Bajo'/'Medio'/'Alto'`.
* **Seguridad:** crea un **rol de solo-reportes** con `SELECT` sobre vistas pero sin acceso a tablas base.
* **Triggers:** `BEFORE UPDATE` que bloquee cambiar `clientes_id_cliente` en `ventas`.
* **Eventos:** evento mensual que cierre el período y envíe resumen a una tabla `salida_reportes`.
* **Procedimientos:** `sp_ReponerStockBajo(umbral, cantidad)` que reponga a `cantidad` los productos con `stock < umbral`.

---
