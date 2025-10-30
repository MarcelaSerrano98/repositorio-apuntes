Perfecto 🔥
Vamos con la **guía completa y explicada del archivo `05_Triggers.sql`**, igual que las anteriores — escrita para que entiendas **desde cero qué es un trigger**, **cómo funciona cada uno**, **por qué se usan**, y **qué te podrían preguntar en el examen**.
Esta parte es una de las **más importantes en bases de datos**, porque los *triggers* representan **automatización e integridad lógica** dentro del modelo.

---

# ⚙️ Guía de Estudio Completa — Archivo `05_Triggers.sql`

---

## 📘 1. ¿Qué es un *trigger* (disparador) en SQL?

Un **trigger** (disparador) es un bloque de código que se **ejecuta automáticamente** cuando ocurre un evento en una tabla, como un **INSERT**, **UPDATE** o **DELETE**.
Su función es **mantener la integridad**, **auditar cambios** o **automatizar tareas** que deben suceder siempre.

👉 En palabras simples:

> “Cuando pasa *esto* (por ejemplo, se inserta una venta), ejecuta *esto otro* (por ejemplo, actualiza el stock o registra un log).”

---

## 🧩 2. Estructura general de un trigger

```sql
DELIMITER $$
CREATE TRIGGER nombre_trigger
{BEFORE | AFTER} {INSERT | UPDATE | DELETE}
ON nombre_tabla
FOR EACH ROW
BEGIN
    -- Aquí va la lógica
END$$
DELIMITER ;
```

### Explicación:

| Parte                      | Significado                                                                    |
| -------------------------- | ------------------------------------------------------------------------------ |
| `BEFORE`                   | El trigger se ejecuta **antes** de realizar la operación (sirve para validar). |
| `AFTER`                    | Se ejecuta **después** (sirve para registrar o actualizar consecuencias).      |
| `INSERT / UPDATE / DELETE` | Tipo de evento que lo activa.                                                  |
| `NEW`                      | Representa los **valores nuevos** (INSERT o UPDATE).                           |
| `OLD`                      | Representa los **valores anteriores** (UPDATE o DELETE).                       |

---

## 💡 3. Tipos de triggers más usados

1. **BEFORE INSERT:** Validar datos antes de guardarlos.
2. **BEFORE UPDATE:** Revisar cambios antes de confirmar la modificación.
3. **AFTER INSERT:** Actualizar otras tablas o logs después de guardar.
4. **AFTER UPDATE:** Registrar cambios históricos o actualizar totales.
5. **BEFORE DELETE:** Bloquear borrados no permitidos.
6. **AFTER DELETE:** Limpieza o auditoría de eliminaciones.

---

## 🧮 4. Triggers definidos en el archivo `05_Triggers.sql`

El archivo implementa varios triggers que **protegen y automatizan** comportamientos de negocio del sistema de ventas.
Vamos uno por uno 👇

---

### 🛒 4.1. `trg_VerificarStock_BeforeInsertDetalleVenta`

**Evento:** `BEFORE INSERT ON detalle_ventas`

**Objetivo:** Evitar que se registre una venta si el stock no es suficiente.

```sql
DELIMITER $$
CREATE TRIGGER trg_VerificarStock_BeforeInsertDetalleVenta
BEFORE INSERT ON detalle_ventas
FOR EACH ROW
BEGIN
    DECLARE v_stock_actual INT;
    SELECT stock INTO v_stock_actual
    FROM productos
    WHERE id_producto = NEW.productos_id_producto;

    IF v_stock_actual < NEW.cantidad THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Stock insuficiente para realizar la venta';
    END IF;
END $$
DELIMITER ;
```

### 🔍 Explicación:

* Antes de insertar una línea en `detalle_ventas`, verifica el `stock` actual del producto.
* Si el stock es menor que la cantidad solicitada, **lanza un error** con `SIGNAL`.

### Conceptos clave:

* `SIGNAL SQLSTATE '45000'`: interrumpe la transacción con un mensaje personalizado.
* `NEW.cantidad`: hace referencia al valor que se intenta insertar.
* Protege contra errores lógicos en el flujo de ventas.

### Ejemplo:

Si el producto tiene `stock = 5` y el cliente quiere `cantidad = 8`, la base lanza:

```
ERROR: Stock insuficiente para realizar la venta
```

### Preguntas posibles:

* ¿Cuál es la diferencia entre `BEFORE` y `AFTER`?
* ¿Por qué se usa `SIGNAL`?
* ¿Qué pasa si no existiera este trigger?

---

### 📦 4.2. `trg_ActualizarStock_AfterInsertDetalleVenta`

**Evento:** `AFTER INSERT ON detalle_ventas`

**Objetivo:** Descontar automáticamente del inventario la cantidad vendida.

```sql
DELIMITER $$
CREATE TRIGGER trg_ActualizarStock_AfterInsertDetalleVenta
AFTER INSERT ON detalle_ventas
FOR EACH ROW
BEGIN
    UPDATE productos
    SET stock = stock - NEW.cantidad
    WHERE id_producto = NEW.productos_id_producto;
END $$
DELIMITER ;
```

### Explicación:

* Una vez insertado el detalle, el trigger **actualiza el stock del producto**.
* Se ejecuta después para asegurar que la venta se haya registrado correctamente.

### Riesgos comunes:

* No validar si el stock queda negativo → se soluciona con el trigger anterior.
* Si se cancela la venta, hay que **restaurar stock** (otro trigger o SP lo haría).

### Preguntas:

* ¿Por qué conviene hacerlo en un `AFTER INSERT`?
* ¿Qué pasa si hay múltiples ventas simultáneas del mismo producto?

---

### 💰 4.3. `trg_AuditarCambioPrecio_AfterUpdateProducto`

**Evento:** `AFTER UPDATE ON productos`

**Objetivo:** Registrar cada cambio de precio para auditoría.

```sql
DELIMITER $$
CREATE TRIGGER trg_AuditarCambioPrecio_AfterUpdateProducto
AFTER UPDATE ON productos
FOR EACH ROW
BEGIN
    IF OLD.precio <> NEW.precio THEN
        INSERT INTO log_precios (id_producto, precio_anterior, precio_nuevo, fecha_cambio)
        VALUES (NEW.id_producto, OLD.precio, NEW.precio, NOW());
    END IF;
END $$
DELIMITER ;
```

### Explicación:

* Solo actúa si el precio realmente cambia (`OLD.precio <> NEW.precio`).
* Guarda el cambio en una tabla `log_precios`.

### Conceptos clave:

* `OLD` → valor anterior.
* `NEW` → nuevo valor.
* Auditoría = registro histórico de cambios importantes.

### Ejemplo:

| id_producto | precio_anterior | precio_nuevo | fecha_cambio        |
| ----------- | --------------- | ------------ | ------------------- |
| 3           | 25.00           | 28.00        | 2025-09-01 10:30:00 |

### Preguntas:

* ¿Qué diferencia hay entre `OLD` y `NEW`?
* ¿Por qué usar `AFTER UPDATE` y no `BEFORE`?
* ¿Qué pasa si cambias otro campo que no sea el precio?

---

### 👥 4.4. `trg_AuditarNuevoCliente_AfterInsert`

**Evento:** `AFTER INSERT ON clientes`

**Objetivo:** Registrar todos los clientes nuevos en una tabla de auditoría.

```sql
DELIMITER $$
CREATE TRIGGER trg_AuditarNuevoCliente_AfterInsert
AFTER INSERT ON clientes
FOR EACH ROW
BEGIN
    INSERT INTO auditoria_clientes (id_cliente, fecha_alta)
    VALUES (NEW.id_cliente, NOW());
END $$
DELIMITER ;
```

**Explicación:**

* Cada vez que se inserta un cliente, se registra su alta.
* Facilita el seguimiento de registros creados.

**Preguntas:**

* ¿Qué diferencia hay entre guardar logs en la app y en la BD?
* ¿Qué pasa si la tabla de auditoría no existe?

---

### 🔄 4.5. `trg_ActualizarTotalVenta_AfterDetalleVenta`

**Evento:** `AFTER INSERT ON detalle_ventas`

**Objetivo:** Recalcular el total de la venta cada vez que se agregue un detalle.

```sql
DELIMITER $$
CREATE TRIGGER trg_ActualizarTotalVenta_AfterDetalleVenta
AFTER INSERT ON detalle_ventas
FOR EACH ROW
BEGIN
    UPDATE ventas
    SET total = (
        SELECT SUM(dv.cantidad * dv.precio_unitario_congelado)
        FROM detalle_ventas dv
        WHERE dv.ventas_id_ventas = NEW.ventas_id_ventas
    )
    WHERE id_ventas = NEW.ventas_id_ventas;
END $$
DELIMITER ;
```

### Explicación:

* Cada nuevo detalle recalcula el total sumando sus líneas.
* Usa una subconsulta dependiente del `id_ventas`.

### Conceptos:

* `SELECT … INTO` → se usa para obtener valores, aquí está embebido directamente.
* `SUM()` agrega los subtotales.

### Preguntas:

* ¿Por qué conviene recalcular en trigger y no desde la aplicación?
* ¿Qué pasa si borras una línea del detalle?

---

### 🚫 4.6. `trg_BloquearEliminacionCategoria_IfTieneProductos`

**Evento:** `BEFORE DELETE ON categorias`

**Objetivo:** Evitar que se eliminen categorías que aún tengan productos asociados.

```sql
DELIMITER $$
CREATE TRIGGER trg_BloquearEliminacionCategoria_IfTieneProductos
BEFORE DELETE ON categorias
FOR EACH ROW
BEGIN
    IF EXISTS (SELECT 1 FROM productos WHERE categorias_id_categoria = OLD.id_categoria) THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'No se puede eliminar la categoría: tiene productos asociados';
    END IF;
END $$
DELIMITER ;
```

**Explicación:**

* Antes de eliminar una categoría, verifica si está en uso.
* Si hay productos vinculados, **lanza error**.

**Ejemplo:**

```
ERROR 1644 (45000): No se puede eliminar la categoría: tiene productos asociados
```

**Preguntas:**

* ¿Cuál es la función de `EXISTS`?
* ¿Por qué usar `BEFORE DELETE` y no `AFTER DELETE`?

---

### 🔄 4.7. `trg_NormalizarNombreCliente`

**Evento:** `BEFORE INSERT ON clientes`

**Objetivo:** Asegurar consistencia en formato de nombres.

```sql
DELIMITER $$
CREATE TRIGGER trg_NormalizarNombreCliente
BEFORE INSERT ON clientes
FOR EACH ROW
BEGIN
    SET NEW.nombre = CONCAT(UCASE(LEFT(NEW.nombre,1)), LCASE(SUBSTRING(NEW.nombre,2)));
    SET NEW.apellido = CONCAT(UCASE(LEFT(NEW.apellido,1)), LCASE(SUBSTRING(NEW.apellido,2)));
END $$
DELIMITER ;
```

**Explicación:**

* Convierte la primera letra en mayúscula y el resto en minúsculas.
* Garantiza uniformidad en la base de datos.

**Preguntas:**

* ¿Por qué conviene estandarizar nombres?
* ¿Qué diferencia hay entre `LEFT()` y `SUBSTRING()`?

---

## 🧠 5. Conceptos reforzados

| Concepto            | Descripción                    | Ejemplo                      |
| ------------------- | ------------------------------ | ---------------------------- |
| `NEW`               | Fila nueva (INSERT/UPDATE)     | `NEW.cantidad`               |
| `OLD`               | Fila anterior (UPDATE/DELETE)  | `OLD.precio`                 |
| `SIGNAL SQLSTATE`   | Lanza error personalizado      | `'45000'`                    |
| `EXISTS`            | Verifica existencia de filas   | `IF EXISTS(SELECT 1...)`     |
| `NOW()`             | Fecha y hora actual            | `NOW()`                      |
| `AFTER` vs `BEFORE` | Orden de ejecución del trigger | Antes o después de la acción |

---

## ⚠️ 6. Errores comunes

1. **No usar `FOR EACH ROW`:** el trigger no se aplicará por registro.
2. **Olvidar `DELIMITER`:** el script no se interpreta correctamente.
3. **Cambiar datos en `BEFORE UPDATE` sin necesidad.**
4. **Causar recursión:** un trigger que modifica la misma tabla y vuelve a activarse.
5. **No usar `SIGNAL` y permitir estados inválidos.**

---

## 🧾 7. Resumen visual

| Trigger                                             | Evento                       | Propósito                  | Palabras clave          |
| --------------------------------------------------- | ---------------------------- | -------------------------- | ----------------------- |
| `trg_VerificarStock_BeforeInsertDetalleVenta`       | BEFORE INSERT detalle_ventas | Validar stock              | `SIGNAL`, `NEW`         |
| `trg_ActualizarStock_AfterInsertDetalleVenta`       | AFTER INSERT detalle_ventas  | Descontar stock            | `UPDATE`, `NEW`         |
| `trg_AuditarCambioPrecio_AfterUpdateProducto`       | AFTER UPDATE productos       | Registrar cambio de precio | `OLD`, `NEW`            |
| `trg_AuditarNuevoCliente_AfterInsert`               | AFTER INSERT clientes        | Log de altas               | `INSERT INTO auditoría` |
| `trg_ActualizarTotalVenta_AfterDetalleVenta`        | AFTER INSERT detalle_ventas  | Recalcular total           | `SUM`, subconsulta      |
| `trg_BloquearEliminacionCategoria_IfTieneProductos` | BEFORE DELETE categorias     | Evitar borrado con FK      | `EXISTS`, `SIGNAL`      |
| `trg_NormalizarNombreCliente`                       | BEFORE INSERT clientes       | Formatear texto            | `UCASE`, `SUBSTRING`    |

---

## 🎯 8. Posibles preguntas de examen

* ¿Qué diferencia hay entre un **BEFORE** y un **AFTER trigger**?
* ¿Qué son `OLD` y `NEW` y cuándo se usan?
* ¿Qué pasa si un trigger lanza un error (`SIGNAL`)?
* ¿Cómo evitarías un *loop* entre triggers?
* ¿Por qué los triggers ayudan a la integridad referencial lógica?
* ¿Cuál es la desventaja de depender demasiado de ellos?
* ¿Qué sucede si fallan varios triggers dentro de una misma transacción?
* ¿Se puede tener más de un trigger del mismo tipo en una tabla?

---

## 🧩 9. Recomendaciones finales

* Memoriza al menos **un ejemplo de cada tipo de trigger**.
* Piensa siempre: “¿Esto debería ocurrir antes o después del cambio?”.
* Los triggers **no deben reemplazar la lógica de la aplicación**, solo asegurar que los datos **no se corrompan**.
* Prepárate para explicar **por qué cada trigger existe**:

  * *Integridad (stock)*
  * *Auditoría (cambios de precio)*
  * *Mantenimiento automático (totales, nombres)*

---
