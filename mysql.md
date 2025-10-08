# Guía SQL completa — `taller_sql` (todo en 1 archivo)

**Objetivo:** Tener en un único documento todos los scripts y consultas SQL esenciales — SELECTs básicos y avanzados, DML, DDL, transacciones, índices, vistas, procedimientos, triggers, backups, performance y ejemplos aplicados a la base `taller_sql`.

> Esta guía está pensada para que la copies/pegues directamente en tu cliente MySQL (Workbench, DBeaver, CLI). Cada bloque tiene una explicación breve y un ejemplo usando el esquema de `taller_sql`.

---

## Índice rápido

1. Consultas SELECT básicas
2. Filtros y patrones (`WHERE`, `LIKE`)
3. Orden, límite y paginación (`ORDER BY`, `LIMIT`, `OFFSET`)
4. Agregaciones y agrupamiento (`GROUP BY`, `HAVING`)
5. Joins: resumen y ejemplos (INNER/LEFT/RIGHT/CROSS)
6. Subconsultas y `EXISTS`
7. Funciones útiles (string, fecha, numéricas, agregados)
8. Ventanas (Window Functions) — MySQL 8+
9. Insertar datos (INSERT simple, múltiple, INSERT ... SELECT)
10. Actualizar (UPDATE) — con y sin JOIN
11. Eliminar (DELETE) — precauciones
12. DDL: CREATE, ALTER, DROP (tablas, índices, constraints)
13. Índices y performance (CREATE INDEX, EXPLAIN)
14. Transacciones (BEGIN, COMMIT, ROLLBACK)
15. Vistas (CREATE VIEW)
16. Procedimientos almacenados y funciones (CREATE PROCEDURE/FUNCTION)
17. Triggers (CREATE TRIGGER)
18. Import / Export / Backup (mysqldump, LOAD DATA)
19. Seguridad mínima: usuarios y permisos (CREATE USER, GRANT)
20. Buenas prácticas y check-list antes de ejecutar
21. Ejercicios prácticos y resultados esperados

---

## 1) SELECT básicos

**Qué hace:** recuperar columnas/filas.

```sql
-- Todos los datos
SELECT * FROM usuarios;

-- Columnas específicas
SELECT nombre, email FROM usuarios;

-- Distintos (sin duplicados)
SELECT DISTINCT ciudad FROM usuarios;
```

**Ejemplo (taller_sql):** verás la lista completa de clientes y empleados mezclados (tipo_id distingue).

---

## 2) WHERE y LIKE (filtros y patrones)

```sql
-- Filtrar por igualdad y comparadores
SELECT nombre FROM productos WHERE precio > 100000;

-- Igualdad de texto
SELECT nombre, email FROM usuarios WHERE ciudad = 'Madrid';

-- LIKE para patrones (case insensitive dependiendo de collation)
SELECT nombre FROM productos WHERE nombre LIKE 'A%';   -- empieza con A
SELECT nombre FROM productos WHERE nombre LIKE '%desk%'; -- contiene 'desk'

-- NOT LIKE
SELECT nombre FROM productos WHERE nombre NOT LIKE '%Pro%';
```

**Tips:** usar `%` para comodines; `_` para un único carácter.

---

## 3) ORDER BY, LIMIT, OFFSET (orden y paginación)

```sql
-- Orden ascendente/descendente
SELECT nombre, precio FROM productos ORDER BY precio ASC;
SELECT nombre, precio FROM productos ORDER BY precio DESC;

-- Limitar resultados
SELECT nombre FROM usuarios ORDER BY fecha_registro DESC LIMIT 10;

-- Paginación: page 3 de 10 filas
SELECT * FROM productos ORDER BY producto_id LIMIT 10 OFFSET 20;
```

---

## 4) AGREGACIONES: COUNT, SUM, AVG, MIN, MAX y GROUP BY

```sql
-- Contar filas
SELECT COUNT(*) AS total_productos FROM productos;

-- Suma y promedio
SELECT SUM(precio) AS total_valor, AVG(precio) AS precio_promedio FROM productos;

-- Agrupar por categoría
SELECT categoria, COUNT(*) AS num_productos, ROUND(AVG(precio),2) AS precio_promedio
FROM productos
GROUP BY categoria
ORDER BY num_productos DESC;

-- HAVING filtra grupos
SELECT categoria, COUNT(*) AS num_productos
FROM productos
GROUP BY categoria
HAVING COUNT(*) > 2;
```

---

## 5) JOINS (resumen + ejemplos)

**INNER JOIN**: solo coincidencias.

```sql
SELECT u.nombre AS cliente, p.pedido_id
FROM usuarios u
INNER JOIN pedidos p ON u.usuario_id = p.cliente_id;
```

**LEFT JOIN**: todos de la izquierda.

```sql
SELECT p.pedido_id, dp.producto_id, pr.nombre
FROM pedidos p
LEFT JOIN detalles_pedidos dp ON p.pedido_id = dp.pedido_id
LEFT JOIN productos pr ON dp.producto_id = pr.producto_id;
```

**RIGHT JOIN**: todos de la derecha (puedes invertir y usar LEFT para legibilidad).

```sql
SELECT pr.producto_id, dp.pedido_id
FROM productos pr
LEFT JOIN detalles_pedidos dp ON pr.producto_id = dp.producto_id; -- preferible
```

**CROSS JOIN**: producto cartesiano

```sql
SELECT u.usuario_id, pr.producto_id
FROM usuarios u
CROSS JOIN productos pr
WHERE u.tipo_id = 1
LIMIT 100;
```

**JOIN en 3 o más tablas**

```sql
SELECT u.nombre AS cliente, pr.nombre AS producto, e.nombre AS empleado
FROM usuarios u
JOIN pedidos p ON u.usuario_id = p.cliente_id
JOIN empleados em ON p.empleado_id = em.empleado_id
JOIN usuarios e ON em.usuario_id = e.usuario_id
JOIN detalles_pedidos dp ON p.pedido_id = dp.pedido_id
JOIN productos pr ON dp.producto_id = pr.producto_id;
```

---

## 6) SUBCONSULTAS y EXISTS

```sql
-- SUBQUERY en SELECT: obtener cliente con más pedidos (ejemplo conceptual)
SELECT usuario_id, nombre
FROM usuarios
WHERE usuario_id = (
  SELECT cliente_id
  FROM pedidos
  GROUP BY cliente_id
  ORDER BY COUNT(*) DESC
  LIMIT 1
);

-- EXISTS (eficiente para existencia)
SELECT u.*
FROM usuarios u
WHERE EXISTS (
  SELECT 1 FROM pedidos p WHERE p.cliente_id = u.usuario_id
);
```

---

## 7) FUNCIONES útiles

**String:** `CONCAT`, `LOWER`, `UPPER`, `TRIM`, `SUBSTRING`, `REPLACE`.

```sql
SELECT CONCAT(nombre, ' <', email, '>') AS contacto FROM usuarios;
```

**Fecha:** `CURDATE()`, `DATE_ADD`, `DATEDIFF`, `YEAR`, `MONTH`.

```sql
SELECT nombre FROM usuarios WHERE fecha_registro > DATE_SUB(CURDATE(), INTERVAL 1 YEAR);
```

**Números:** `ROUND`, `CEIL`, `FLOOR`.

**NULL handling:** `COALESCE`, `IFNULL`.

```sql
SELECT COALESCE(pr.precio, 0) FROM productos pr;
```

**CASE** (condicional)

```sql
SELECT nombre,
  CASE
    WHEN precio > 1000000 THEN 'Carísimo'
    WHEN precio > 200000 THEN 'Caro'
    ELSE 'Económico'
  END AS rango_precio
FROM productos;
```

---

## 8) WINDOW FUNCTIONS (MySQL 8+)

```sql
-- Total acumulado por pedido
SELECT p.pedido_id, dp.producto_id, dp.cantidad,
  SUM(dp.cantidad * dp.precio_unitario) OVER (PARTITION BY p.pedido_id ORDER BY dp.detalle_id) AS acumulado
FROM pedidos p
JOIN detalles_pedidos dp ON p.pedido_id = dp.pedido_id;

-- RANK entre productos por precio dentro de cada categoría
SELECT producto_id, nombre, categoria, precio,
  RANK() OVER (PARTITION BY categoria ORDER BY precio DESC) AS rank_categoria
FROM productos;
```

---

## 9) INSERT

```sql
-- Insert simple
INSERT INTO proveedores (nombre, email, ciudad, pais) VALUES
('Proveedor Demo', 'demo@example.com', 'Bogotá', 'Colombia');

-- Insert multiple
INSERT INTO productos (nombre, categoria, precio, stock) VALUES
('Prod A','Electrónica',100000.00, 10),
('Prod B','Hogar',50000.00, 5);

-- INSERT ... SELECT (copiar o crear en lote)
INSERT INTO proveedores_productos (proveedor_id, producto_id)
SELECT prov.proveedor_id, pr.producto_id
FROM proveedores prov
JOIN productos pr ON pr.categoria = 'Electrónica'
WHERE prov.nombre LIKE '%Tech%';
```

**Advertencia:** valida integridad referencial antes de insertar FK.

---

## 10) UPDATE

```sql
-- Actualizar precio de un producto
UPDATE productos SET precio = precio * 1.05 WHERE categoria = 'Electrónica';

-- UPDATE con JOIN (MySQL sintaxis)
UPDATE productos pr
JOIN proveedores_productos pp ON pr.producto_id = pp.producto_id
SET pr.stock = pr.stock + 10
WHERE pp.proveedor_id = 1;
```

**Precaución:** haz siempre un SELECT con la misma condición antes de ejecutar el UPDATE.

---

## 11) DELETE

```sql
-- Eliminar un registro
DELETE FROM proveedores WHERE proveedor_id = 99;

-- Borrar en cascada depende de FK; para borrar muchos registros:
DELETE FROM detalles_pedidos WHERE pedido_id = 999;
```

**Siempre** ejecutar `SELECT` con la misma `WHERE` antes de borrar, y si puedes, usar `BEGIN` / `ROLLBACK`.

---

## 12) DDL: CREATE, ALTER, DROP

```sql
-- Crear tabla (ejemplo simplificado)
CREATE TABLE categorias (
  categoria_id INT AUTO_INCREMENT PRIMARY KEY,
  nombre VARCHAR(100) NOT NULL
);

-- Añadir columna
ALTER TABLE productos ADD COLUMN descripcion TEXT;

-- Modificar tipo
ALTER TABLE usuarios MODIFY COLUMN telefono VARCHAR(30);

-- Eliminar tabla
DROP TABLE IF EXISTS categorias;
```

---

## 13) Índices y performance

```sql
-- Crear índice
CREATE INDEX idx_productos_categoria ON productos (categoria);

-- Índice compuesto
CREATE INDEX idx_detalle_pedido_prod ON detalles_pedidos (pedido_id, producto_id);

-- EXPLAIN para ver plan
EXPLAIN SELECT pr.* FROM productos pr WHERE pr.categoria = 'Electrónica';
```

**Consejo:** índices aceleran lecturas pero penalizan escrituras; crea índices en columnas usadas en JOINS y WHERE frecuentes.

---

## 14) Transacciones (ACID)

```sql
START TRANSACTION;
-- operaciones DML
UPDATE productos SET stock = stock - 2 WHERE producto_id = 1;
INSERT INTO detalles_pedidos (pedido_id, producto_id, cantidad, precio_unitario) VALUES (30,1,2,4148678.51);
-- si todo ok
COMMIT;
-- si falla
ROLLBACK;
```

---

## 15) Vistas

```sql
CREATE VIEW vista_pedidos_completos AS
SELECT p.pedido_id, u.nombre AS cliente, u2.nombre AS empleado, p.fecha_pedido, p.estado
FROM pedidos p
JOIN usuarios u ON p.cliente_id = u.usuario_id
JOIN empleados e ON p.empleado_id = e.empleado_id
JOIN usuarios u2 ON e.usuario_id = u2.usuario_id;

-- Usar la vista
SELECT * FROM vista_pedidos_completos WHERE estado = 'Pendiente';
```

---

## 16) PROCEDIMIENTOS y FUNCIONES

```sql
-- Procedimiento simple
DELIMITER $$
CREATE PROCEDURE sp_incrementar_stock(IN p_producto_id INT, IN p_cantidad INT)
BEGIN
  UPDATE productos SET stock = stock + p_cantidad WHERE producto_id = p_producto_id;
END$$
DELIMITER ;

-- Llamar
CALL sp_incrementar_stock(1, 10);

-- Función que devuelve precio en USD (suponiendo trm)
DELIMITER $$
CREATE FUNCTION fn_precio_usd(p_precio DECIMAL(10,2)) RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
  RETURN ROUND(p_precio / 3853.19, 2);
END$$
DELIMITER ;

SELECT nombre, fn_precio_usd(precio) FROM productos;
```

---

## 17) TRIGGERS

```sql
-- Trigger BEFORE INSERT para normalizar email
DELIMITER $$
CREATE TRIGGER trg_normaliza_email BEFORE INSERT ON usuarios
FOR EACH ROW
BEGIN
  SET NEW.email = LOWER(TRIM(NEW.email));
END$$
DELIMITER ;

-- Trigger AFTER INSERT para auditar
CREATE TABLE auditoria_pedidos (
  id INT AUTO_INCREMENT PRIMARY KEY,
  pedido_id INT,
  accion VARCHAR(50),
  fecha TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

DELIMITER $$
CREATE TRIGGER trg_auditar_pedido AFTER INSERT ON pedidos
FOR EACH ROW
BEGIN
  INSERT INTO auditoria_pedidos (pedido_id, accion) VALUES (NEW.pedido_id, 'INSERT');
END$$
DELIMITER ;
```

**Atención:** triggers pueden afectar performance; documenta su comportamiento.

---

## 18) Backup / Import / Export

```bash
# Backup completo de la base taller_sql
mysqldump -u usuario -p taller_sql > taller_sql_backup.sql

# Restaurar
mysql -u usuario -p taller_sql < taller_sql_backup.sql

# Export a CSV desde MySQL CLI
SELECT * INTO OUTFILE '/tmp/productos.csv' FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '"' LINES TERMINATED BY '\n' FROM productos;

# Cargar CSV
LOAD DATA INFILE '/tmp/productos.csv' INTO TABLE productos FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '"' LINES TERMINATED BY '\n' (producto_id, nombre, categoria, precio, stock);
```

---

## 19) Seguridad básica: usuarios y permisos

```sql
-- Crear usuario
CREATE USER 'analista'@'localhost' IDENTIFIED BY 'contraseñaSegura123';
-- Permisos lectura en toda la DB
GRANT SELECT ON taller_sql.* TO 'analista'@'localhost';

-- Permisos completos para admin
GRANT ALL PRIVILEGES ON taller_sql.* TO 'adminuser'@'localhost';
FLUSH PRIVILEGES;
```

**Consejo:** no uses root para tareas diarias; crea roles y usuarios con privilegios mínimos.

---

## 20) Buenas prácticas y checklist antes de ejecutar

* Hacer `SELECT` con la misma `WHERE` antes de `UPDATE`/`DELETE`.
* Realizar backups antes de cambios de esquema.
* Usar transacciones para operaciones multi-step.
* Revisar `EXPLAIN` en consultas lentas.
* Evitar `SELECT *` en producción.
* Documentar triggers y procedimientos.

---

## 21) Ejercicios prácticos (5) con resultados esperados

1. **Listar clientes en Madrid**

```sql
SELECT nombre, email FROM usuarios WHERE ciudad = 'Madrid';
```

*Esperado:* filas con Ana Pérez, Elena Ramírez, Nuria Gil (según datos).

2. **Top 3 productos más caros**

```sql
SELECT nombre, precio FROM productos ORDER BY precio DESC LIMIT 3;
```

*Esperado:* Laptop, Smartphone, Televisor (por precio en datos de ejemplo).

3. **Total gastado por pedido (pedido_id=1)**

```sql
SELECT ROUND(SUM(cantidad*precio_unitario),2) AS total
FROM detalles_pedidos WHERE pedido_id = 1;
```

*Esperado:* 2 * 4148678.51 = 8297357.02

4. **Clientes sin pedidos**

```sql
SELECT u.usuario_id, u.nombre
FROM usuarios u
LEFT JOIN pedidos p ON u.usuario_id = p.cliente_id
WHERE p.pedido_id IS NULL AND u.tipo_id = 1;
```

*Esperado:* usuarios que no aparecen en la tabla pedidos.

5. **Crear vista de productos en stock bajo (stock < 20)**

```sql
CREATE VIEW vista_stock_bajo AS
SELECT producto_id, nombre, stock FROM productos WHERE stock < 20;
SELECT * FROM vista_stock_bajo;
```

*Esperado:* listado de productos con stock bajo (por ejemplo Bicicleta con 15).

---

### Últimos consejos (honestos y directos)

* Practica todas estas consultas en una copia de tu base antes de ejecutar en producción.
* Si la consulta tarda, ejecuta `EXPLAIN` y revisa índices.
* Usa control de versiones para scripts DDL (git).

Si quieres, te convierto este mismo documento en un archivo `.md` descargable o en un `.sql` con todos los bloques listos para ejecutar. ¿Lo dejo también como `.sql` para que puedas correrlo de una vez?


