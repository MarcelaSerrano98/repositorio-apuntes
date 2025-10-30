---

# 🧠 Guía de Estudio Completa – Archivo `03_Funciones.sql` (Versión con Preguntas Respondidas)

---

## 📘 1. ¿Qué son las funciones en SQL?

Una **función en SQL** es un bloque de código almacenado en la base de datos que **recibe parámetros**, **realiza cálculos o consultas**, y **devuelve un resultado**.

Sirven para:

* Automatizar cálculos repetitivos.
* Centralizar la lógica del negocio (por ejemplo, descuentos, IVA, estados de cliente).
* Mantener consistencia en los resultados y reglas de negocio.

### 🔹 Tipos:

1. **Funciones predefinidas:** Vienen con SQL (ej. `SUM()`, `AVG()`, `NOW()`, `ROUND()`).
2. **Funciones definidas por el usuario (UDF):** Creadas manualmente con `CREATE FUNCTION`.

---

## 🧩 2. Estructura general de una función

```sql
DELIMITER $$
CREATE FUNCTION nombre_funcion(parametros)
RETURNS tipo_de_dato
[DETERMINISTIC | NOT DETERMINISTIC]
[READS SQL DATA | MODIFIES SQL DATA]
BEGIN
   DECLARE variable DATATYPE;
   -- Operaciones o consultas
   SELECT ... INTO variable FROM ...;
   RETURN variable;
END$$
DELIMITER ;
```

### 🧠 Explicación:

* `DELIMITER $$` → permite escribir varias sentencias dentro del bloque sin que se cierre antes de tiempo.
* `RETURNS` → especifica el tipo de dato que devuelve.
* `DETERMINISTIC` → garantiza que con los mismos parámetros siempre devuelve el mismo resultado.
* `READS SQL DATA` → indica que solo lee datos, no los modifica.
* `RETURN` → define el valor final devuelto.

---

## 🧮 3. Funciones del archivo explicadas y con ejemplos

---

### 🧱 3.1. `fn_CalcularCostoEnvio`

**Objetivo:** Calcular el costo total de envío según el peso total de una venta.

```sql
CREATE FUNCTION fn_CalcularCostoEnvio(p_venta_id INT)
RETURNS DECIMAL(12,2)
DETERMINISTIC
READS SQL DATA
BEGIN
   DECLARE v_base DECIMAL(10,2) DEFAULT 10000.00;
   DECLARE v_por_kg DECIMAL(10,2) DEFAULT 4000.00;
   DECLARE v_peso_total DECIMAL(10,3);
   DECLARE v_costo DECIMAL(12,2);

   SELECT COALESCE(SUM(dv.cantidad * p.peso_kg), 0)
     INTO v_peso_total
   FROM detalle_ventas dv
   JOIN productos p ON p.id_producto = dv.productos_id_producto
   WHERE dv.ventas_id_ventas = p_venta_id;

   SET v_costo = v_base + (v_por_kg * CEIL(v_peso_total));
   RETURN v_costo;
END $$
```

**Explicación paso a paso:**

1. Recibe el ID de una venta.
2. Calcula el peso total con `SUM()`.
3. Usa una tarifa base más un costo por kilo (`v_por_kg`).
4. Redondea hacia arriba con `CEIL()`.
5. Devuelve el costo final.

**Ejemplo:**

```sql
SELECT fn_CalcularCostoEnvio(101);
-- Resultado → 28000.00
```

**Preguntas y respuestas:**

> ❓ ¿Qué hace la función `COALESCE()`?
> ✅ Devuelve el primer valor que **no sea NULL**. Aquí se usa para evitar errores si no hay productos (devuelve 0 en vez de NULL).

> ❓ ¿Qué diferencia hay entre `ROUND()` y `CEIL()`?
> ✅ `ROUND()` redondea según decimales (ej. 1.6 → 2, 1.4 → 1).
> ✅ `CEIL()` siempre redondea hacia arriba (ej. 1.1 → 2).

> ❓ ¿Por qué se usa `READS SQL DATA`?
> ✅ Porque la función **solo lee datos** de tablas, no los modifica.

---

### 💰 3.2. `fn_AplicarDescuento`

**Objetivo:** Aplicar un porcentaje de descuento sobre un monto.

```sql
CREATE FUNCTION fn_AplicarDescuento(p_monto DECIMAL(12,2), p_porcentaje DECIMAL(5,2))
RETURNS DECIMAL(12,2)
DETERMINISTIC
BEGIN
   DECLARE v_pct DECIMAL(7,4);
   SET v_pct = CASE
                 WHEN p_porcentaje < 0   THEN 0
                 WHEN p_porcentaje > 100 THEN 100
                 ELSE p_porcentaje
               END;

   RETURN CASE
            WHEN p_monto IS NULL THEN NULL
            ELSE ROUND(p_monto * (1 - (v_pct/100)), 2)
          END;
END $$
```

**Ejemplo:**

```sql
SELECT fn_AplicarDescuento(50000, 15);
-- Resultado → 42500.00
```

**Preguntas y respuestas:**

> ❓ ¿Qué ocurre si el porcentaje es -10 o 150?
> ✅ La función ajusta automáticamente: si es menor que 0 usa 0, si es mayor que 100 usa 100.

> ❓ ¿Por qué se usa `ROUND()`?
> ✅ Para redondear a dos decimales (evitar números largos en precios).

> ❓ ¿Qué significa `DETERMINISTIC`?
> ✅ Que siempre devuelve el mismo resultado si se le pasan los mismos parámetros.

---

### 📅 3.3. `fn_ObtenerUltimaFechaCompra`

**Objetivo:** Obtener la fecha de la última compra de un cliente.

```sql
CREATE FUNCTION fn_ObtenerUltimaFechaCompra(p_id_cliente INT)
RETURNS DATETIME
DETERMINISTIC
BEGIN
    DECLARE ultima_fecha DATETIME;
    SELECT MAX(fecha_venta)
    INTO ultima_fecha
    FROM ventas
    WHERE clientes_id_cliente = p_id_cliente;
    RETURN ultima_fecha;
END$$
```

**Ejemplo:**

```sql
SELECT fn_ObtenerUltimaFechaCompra(3);
-- Resultado → '2025-09-15 13:40:00'
```

**Preguntas y respuestas:**

> ❓ ¿Qué función obtiene el valor máximo?
> ✅ `MAX()` devuelve el valor más grande de una columna (en este caso, la fecha más reciente).

> ❓ ¿Qué devuelve si el cliente nunca ha comprado?
> ✅ Devuelve `NULL`, porque no existe ningún registro que cumpla la condición.

---

### ⏳ 3.4. `fn_CalcularDiasDesdeUltimaCompra`

**Objetivo:** Calcular cuántos días han pasado desde la última compra de un cliente.

```sql
CREATE FUNCTION fn_CalcularDiasDesdeUltimaCompra(p_id_cliente INT)
RETURNS INT
DETERMINISTIC
BEGIN
    DECLARE ultima_fecha DATETIME;
    DECLARE dias INT;
    SELECT MAX(fecha_venta)
    INTO ultima_fecha
    FROM ventas
    WHERE clientes_id_cliente = p_id_cliente;
    SET dias = DATEDIFF(CURRENT_DATE, ultima_fecha);
    RETURN IFNULL(dias, NULL);
END $$
```

**Ejemplo:**

```sql
SELECT fn_CalcularDiasDesdeUltimaCompra(3);
-- Resultado → 42
```

**Preguntas y respuestas:**

> ❓ ¿Qué hace `DATEDIFF()`?
> ✅ Calcula la diferencia en días entre dos fechas (`CURRENT_DATE` y `ultima_fecha`).

> ❓ ¿Qué pasa si el cliente no tiene compras?
> ✅ `ultima_fecha` será `NULL`, y `IFNULL()` evita error devolviendo `NULL` directamente.

---

### 🏅 3.5. `fn_DeterminarEstadoLealtad`

**Objetivo:** Clasificar al cliente según su gasto total (Bronce, Plata, Oro).

```sql
CREATE FUNCTION fn_DeterminarEstadoLealtad(p_id_cliente INT)
RETURNS VARCHAR(10)
DETERMINISTIC
BEGIN
    DECLARE gasto_total DECIMAL(10,2);
    DECLARE estado VARCHAR(10);
    SELECT SUM(dv.cantidad * dv.precio_unitario_congelado)
    INTO gasto_total
    FROM detalle_ventas dv
    JOIN ventas v ON dv.ventas_id_ventas = v.id_ventas
    WHERE v.clientes_id_cliente = p_id_cliente;

    IF gasto_total IS NULL OR gasto_total <= 500 THEN
        SET estado = 'Bronce';
    ELSEIF gasto_total <= 2000 THEN
        SET estado = 'Plata';
    ELSE
        SET estado = 'Oro';
    END IF;

    RETURN estado;
END $$
```

**Ejemplo:**

```sql
SELECT fn_DeterminarEstadoLealtad(11);
-- Resultado → 'Plata'
```

**Preguntas y respuestas:**

> ❓ ¿Qué estructura condicional usa SQL?
> ✅ `IF … ELSEIF … ELSE`, igual que otros lenguajes de programación.

> ❓ ¿Qué pasa si el cliente no tiene compras?
> ✅ `SUM()` devuelve `NULL`, así que cae en la primera condición y el cliente será "Bronce".

---

### 🧾 3.6. `fn_GenerarSKU`

**Objetivo:** Generar un código único para un producto según su nombre y categoría.

```sql
CREATE FUNCTION fn_GenerarSKU(p_nombre VARCHAR(200), p_categoria_id INT)
RETURNS VARCHAR(32)
DETERMINISTIC
READS SQL DATA
BEGIN
   DECLARE v_cat VARCHAR(50);
   DECLARE v_cat3 VARCHAR(3);
   DECLARE v_nom3 VARCHAR(3);
   DECLARE v_hash6 VARCHAR(6);

   SELECT nombre INTO v_cat
   FROM categorias
   WHERE id_categoria = p_categoria_id
   LIMIT 1;

   SET v_cat3  = UPPER(LEFT(REPLACE(TRIM(v_cat),  ' ', ''), 3));
   SET v_nom3  = UPPER(LEFT(REPLACE(TRIM(p_nombre),' ', ''), 3));
   SET v_hash6 = UPPER(LEFT(MD5(TRIM(p_nombre)), 6));

   RETURN CONCAT(v_cat3, '-', v_nom3, '-', v_hash6);
END $$
```

**Ejemplo:**

```sql
SELECT fn_GenerarSKU('Camisa Deportiva Azul', 2);
-- Resultado → 'DEP-CAM-A12F4E'
```

**Preguntas y respuestas:**

> ❓ ¿Qué hace `MD5()`?
> ✅ Genera un código hash único de 32 caracteres; aquí se usa una porción (6) para que el SKU sea irrepetible.

> ❓ ¿Por qué se usa `LIMIT 1`?
> ✅ Para asegurar que solo se obtenga una categoría aunque existan duplicadas (por error).

> ❓ ¿Qué ventaja tiene generar SKUs automáticos?
> ✅ Evita errores humanos y garantiza identificadores únicos para los productos.

---

## 🧩 4. Conceptos teóricos reforzados

| Concepto                                     | Explicación                                                                                                                             |
| -------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| **Diferencia entre función y procedimiento** | La función **devuelve un valor** y puede usarse en un `SELECT`; el procedimiento **no devuelve valor directo** y se ejecuta con `CALL`. |
| **DETERMINISTIC**                            | Indica que la función **siempre devuelve el mismo resultado** con los mismos parámetros.                                                |
| **READS SQL DATA**                           | Declara que la función **solo lee información**, no modifica tablas.                                                                    |
| **DELIMITER $$**                             | Evita que el intérprete cierre el bloque antes de terminar el cuerpo de la función.                                                     |

---

## 🧭 5. Consejos para examen

1. Memoriza la estructura general de una función (`CREATE FUNCTION`, `RETURN`, `DELIMITER`).
2. Entiende el uso de palabras clave (`DECLARE`, `SET`, `INTO`, `RETURN`).
3. Ten al menos **dos funciones memorizadas** (una con cálculos y otra con texto).
4. Repasa funciones de fecha (`DATEDIFF`, `CURRENT_DATE`) y texto (`UPPER`, `LEFT`, `TRIM`).
5. Practica leer funciones ajenas y explicar qué hacen paso a paso.

---

## ✅ 6. Resumen visual

| Función                            | Propósito                     | Palabras clave             | Ejemplo                               | Resultado          |
| ---------------------------------- | ----------------------------- | -------------------------- | ------------------------------------- | ------------------ |
| `fn_CalcularCostoEnvio`            | Calcular costo total por peso | `COALESCE`, `CEIL`         | `fn_CalcularCostoEnvio(101)`          | `28000.00`         |
| `fn_AplicarDescuento`              | Aplicar % de descuento        | `CASE`, `ROUND`            | `fn_AplicarDescuento(50000,15)`       | `42500.00`         |
| `fn_ObtenerUltimaFechaCompra`      | Última compra del cliente     | `MAX`, `INTO`              | `fn_ObtenerUltimaFechaCompra(3)`      | `'2025-09-15'`     |
| `fn_CalcularDiasDesdeUltimaCompra` | Días desde última compra      | `DATEDIFF`, `CURRENT_DATE` | `fn_CalcularDiasDesdeUltimaCompra(3)` | `42`               |
| `fn_DeterminarEstadoLealtad`       | Clasificar cliente            | `IF`, `ELSEIF`, `SUM`      | `fn_DeterminarEstadoLealtad(11)`      | `'Plata'`          |
| `fn_GenerarSKU`                    | Crear código único            | `UPPER`, `MD5`, `CONCAT`   | `fn_GenerarSKU('Camisa Azul',2)`      | `'CAM-DEP-AB12F3'` |

---
