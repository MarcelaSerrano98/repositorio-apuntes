

# ⚙️ Guía de Estudio Completa — Archivo `07_Procedimientos_Almacenados.sql`

---

## 📘 1. ¿Qué es un procedimiento almacenado?

Un **procedimiento almacenado (Stored Procedure)** es un **bloque de código SQL guardado en la base de datos** que se puede ejecutar con una simple instrucción:

```sql
CALL nombre_procedimiento(parámetros);
```

En palabras simples:

> “Un procedimiento almacenado es una función **que no devuelve un valor directo**, pero **ejecuta una o varias operaciones SQL** automáticamente.”

Sirve para:

* Automatizar procesos repetitivos.
* Ejecutar varias consultas o actualizaciones a la vez.
* Asegurar reglas de negocio dentro del servidor.
* Reducir tráfico entre la aplicación y la base de datos.

---

## 🧩 2. Estructura general de un procedimiento

```sql
DELIMITER $$
CREATE PROCEDURE nombre_procedimiento(
    IN parametro1 TIPO,
    OUT parametro2 TIPO,
    INOUT parametro3 TIPO
)
BEGIN
    -- Bloque de código
    DECLARE variable_local TIPO;
    SET variable_local = valor;
    SELECT ...;
END$$
DELIMITER ;
```

### 🧠 Explicación:

| Elemento  | Significado                              |
| --------- | ---------------------------------------- |
| `IN`      | Parámetro de entrada (solo lectura).     |
| `OUT`     | Parámetro de salida (devuelve un valor). |
| `INOUT`   | Entrada y salida al mismo tiempo.        |
| `DECLARE` | Declara variables internas.              |
| `SET`     | Asigna valores.                          |
| `SELECT`  | Consulta o devuelve datos.               |
| `CALL`    | Ejecuta el procedimiento.                |

---

### 💬 Preguntas con respuesta

> ❓ ¿Cuál es la diferencia entre un **procedimiento** y una **función**?
> ✅ Una **función** devuelve un valor y puede usarse dentro de un `SELECT`.
> ✅ Un **procedimiento** no devuelve valor directo, sino que **ejecuta acciones completas** (INSERT, UPDATE, etc.).

> ❓ ¿Para qué sirve `DELIMITER $$`?
> ✅ Permite definir el bloque completo sin que el `;` cierre prematuramente el código.

> ❓ ¿Qué ventaja tiene usar procedimientos?
> ✅ Permiten **centralizar la lógica de negocio**, **reducir errores** y **mejorar el rendimiento** en operaciones repetitivas.

---

## 🧮 3. Procedimientos del archivo `07_Procedimientos_Almacenados.sql`

Vamos uno por uno 👇

---

### 🧾 3.1. `sp_RegistrarVenta`

**Objetivo:** Registrar una nueva venta con sus detalles.

```sql
DELIMITER $$
CREATE PROCEDURE sp_RegistrarVenta(
    IN p_cliente_id INT,
    IN p_fecha DATETIME,
    IN p_total DECIMAL(10,2),
    OUT p_id_venta INT
)
BEGIN
    INSERT INTO ventas (clientes_id_cliente, fecha_venta, total)
    VALUES (p_cliente_id, p_fecha, p_total);

    SET p_id_venta = LAST_INSERT_ID();
END$$
DELIMITER ;
```

### 🔍 Explicación:

1. Inserta una nueva venta en la tabla `ventas`.
2. Usa `LAST_INSERT_ID()` para obtener el ID generado automáticamente.
3. Devuelve ese ID por el parámetro `OUT`.

**Ejemplo de uso:**

```sql
CALL sp_RegistrarVenta(5, NOW(), 125000.00, @id);
SELECT @id AS id_generado;
```

**Resultado:**
→ Inserta la venta y devuelve el ID generado.

---

#### 💬 Preguntas con respuesta

> ❓ ¿Qué hace `LAST_INSERT_ID()`?
> ✅ Devuelve el último valor `AUTO_INCREMENT` generado en la sesión actual (por ejemplo, el ID de la venta recién creada).

> ❓ ¿Por qué se usa `OUT`?
> ✅ Para **devolver** un valor al final del procedimiento (como si fuera un “retorno”).

> ❓ ¿Qué pasaría si olvido `DELIMITER $$`?
> ✅ MySQL intentará cerrar el `CREATE PROCEDURE` antes de tiempo y dará error de sintaxis.

---

### 💰 3.2. `sp_AplicarDescuentoVenta`

**Objetivo:** Aplicar un descuento a una venta existente.

```sql
DELIMITER $$
CREATE PROCEDURE sp_AplicarDescuentoVenta(
    IN p_id_venta INT,
    IN p_porcentaje DECIMAL(5,2)
)
BEGIN
    UPDATE ventas
    SET total = total - (total * (p_porcentaje / 100))
    WHERE id_ventas = p_id_venta;
END$$
DELIMITER ;
```

**Ejemplo:**

```sql
CALL sp_AplicarDescuentoVenta(102, 10);
```

👉 Reduce el total de la venta 102 en un 10%.

---

#### 💬 Preguntas con respuesta

> ❓ ¿Qué diferencia hay entre hacerlo manualmente y con un procedimiento?
> ✅ Con el procedimiento aseguras que el cálculo se haga **igual siempre** y puedes aplicar validaciones o auditorías adicionales.

> ❓ ¿Qué pasa si el porcentaje es negativo?
> ✅ No hay control aquí, pero se podría agregar con una condición `IF p_porcentaje < 0 THEN SET p_porcentaje = 0; END IF;`

---

### 📦 3.3. `sp_ActualizarStockProducto`

**Objetivo:** Actualizar el stock después de una venta.

```sql
DELIMITER $$
CREATE PROCEDURE sp_ActualizarStockProducto(
    IN p_producto_id INT,
    IN p_cantidad_vendida INT
)
BEGIN
    UPDATE productos
    SET stock = stock - p_cantidad_vendida
    WHERE id_producto = p_producto_id;
END$$
DELIMITER ;
```

**Ejemplo:**

```sql
CALL sp_ActualizarStockProducto(7, 3);
```

---

#### 💬 Preguntas con respuesta

> ❓ ¿Qué pasa si el stock se vuelve negativo?
> ✅ Se podría agregar una validación con `IF stock - p_cantidad_vendida < 0 THEN SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Stock insuficiente'; END IF;`

> ❓ ¿Qué ventaja tiene un procedimiento aquí?
> ✅ Permite **validar y centralizar** la lógica del control de inventario sin depender de la aplicación.

---

### 👥 3.4. `sp_ClientesFrecuentes`

**Objetivo:** Listar los clientes con más de N compras.

```sql
DELIMITER $$
CREATE PROCEDURE sp_ClientesFrecuentes(IN p_minimo_compras INT)
BEGIN
    SELECT c.id_cliente, c.nombre, COUNT(v.id_ventas) AS compras
    FROM clientes c
    JOIN ventas v ON v.clientes_id_cliente = c.id_cliente
    GROUP BY c.id_cliente
    HAVING compras >= p_minimo_compras
    ORDER BY compras DESC;
END$$
DELIMITER ;
```

**Ejemplo:**

```sql
CALL sp_ClientesFrecuentes(5);
```

---

#### 💬 Preguntas con respuesta

> ❓ ¿Qué hace `HAVING` en lugar de `WHERE`?
> ✅ `HAVING` se usa con funciones agregadas (`COUNT`, `SUM`) **después del GROUP BY**.
> `WHERE` filtra antes de agrupar.

> ❓ ¿Por qué usar procedimientos en reportes?
> ✅ Para reutilizar consultas frecuentes y mantener un formato estándar.

---

### 🧮 3.5. `sp_ResumenDiarioVentas`

**Objetivo:** Calcular el total de ventas de un día específico.

```sql
DELIMITER $$
CREATE PROCEDURE sp_ResumenDiarioVentas(IN p_fecha DATE)
BEGIN
    SELECT 
        COUNT(*) AS total_ventas,
        SUM(total) AS monto_total
    FROM ventas
    WHERE DATE(fecha_venta) = p_fecha;
END$$
DELIMITER ;
```

**Ejemplo:**

```sql
CALL sp_ResumenDiarioVentas('2025-10-30');
```

---

#### 💬 Preguntas con respuesta

> ❓ ¿Qué hace `DATE(fecha_venta)`?
> ✅ Convierte el campo `fecha_venta` a solo fecha (sin hora) para comparar con `p_fecha`.

> ❓ ¿Por qué usar `COUNT` y `SUM` juntos?
> ✅ `COUNT` da la cantidad de registros (ventas) y `SUM` el total monetario, dos métricas complementarias.

---

## 🧠 4. Conceptos teóricos importantes

| Concepto                     | Explicación                                                          |
| ---------------------------- | -------------------------------------------------------------------- |
| **Procedimiento almacenado** | Bloque SQL guardado en la BD que ejecuta varias sentencias a la vez. |
| **CALL**                     | Ejecuta el procedimiento.                                            |
| **IN / OUT / INOUT**         | Modos de parámetros: entrada, salida, o ambos.                       |
| **DELIMITER**                | Cambia el símbolo que indica fin de bloque.                          |
| **LAST_INSERT_ID()**         | Devuelve el último ID autogenerado.                                  |
| **SIGNAL**                   | Lanza un error personalizado dentro del procedimiento.               |

---

## ⚙️ 5. Ejercicios tipo examen con solución 💡

---

### 🧩 Ejercicio 1: Crear un procedimiento para registrar un nuevo producto

**Enunciado:**
Crea un procedimiento llamado `sp_RegistrarProducto` que reciba el nombre, precio y stock inicial de un producto, lo inserte en la tabla `productos` y devuelva su ID generado.

**Solución:**

```sql
DELIMITER $$
CREATE PROCEDURE sp_RegistrarProducto(
    IN p_nombre VARCHAR(100),
    IN p_precio DECIMAL(10,2),
    IN p_stock INT,
    OUT p_id_producto INT
)
BEGIN
    INSERT INTO productos (nombre, precio, stock)
    VALUES (p_nombre, p_precio, p_stock);

    SET p_id_producto = LAST_INSERT_ID();
END$$
DELIMITER ;

-- Ejemplo de uso
CALL sp_RegistrarProducto('Teclado Gamer', 250000.00, 15, @nuevo);
SELECT @nuevo;
```

---

### 🧩 Ejercicio 2: Crear un procedimiento que aumente precios en un porcentaje

```sql
DELIMITER $$
CREATE PROCEDURE sp_AumentarPrecios(IN p_porcentaje DECIMAL(5,2))
BEGIN
    UPDATE productos
    SET precio = precio * (1 + (p_porcentaje / 100));
END$$
DELIMITER ;

CALL sp_AumentarPrecios(10);
```

🧠 **Qué hace:** Aumenta todos los precios en un 10%.

---

### 🧩 Ejercicio 3: Crear un procedimiento para eliminar clientes sin compras

```sql
DELIMITER $$
CREATE PROCEDURE sp_EliminarClientesInactivos()
BEGIN
    DELETE FROM clientes
    WHERE id_cliente NOT IN (SELECT DISTINCT clientes_id_cliente FROM ventas);
END$$
DELIMITER ;

CALL sp_EliminarClientesInactivos();
```

🧠 **Qué hace:** Limpia clientes que nunca han comprado.

---

### 🧩 Ejercicio 4: Crear un procedimiento que valide stock antes de vender

```sql
DELIMITER $$
CREATE PROCEDURE sp_ValidarVenta(
    IN p_producto INT,
    IN p_cantidad INT
)
BEGIN
    DECLARE v_stock INT;
    SELECT stock INTO v_stock FROM productos WHERE id_producto = p_producto;

    IF v_stock < p_cantidad THEN
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Stock insuficiente';
    ELSE
        UPDATE productos SET stock = stock - p_cantidad WHERE id_producto = p_producto;
    END IF;
END$$
DELIMITER ;

CALL sp_ValidarVenta(10, 3);
```

---

## 🧾 6. Errores comunes y cómo responder

| Error                                  | Causa                             | Respuesta                                              |
| -------------------------------------- | --------------------------------- | ------------------------------------------------------ |
| “You have an error in your SQL syntax” | Falta `DELIMITER $$` o `END$$`    | Cambia el delimitador antes de crear el procedimiento. |
| “OUT or INOUT parameter not set”       | No usaste `SET` dentro del bloque | Asegúrate de asignar valor al parámetro `OUT`.         |
| Bloquea tablas                         | Haces UPDATE sin índice           | Agrega índices o usa filtros.                          |
| Variables locales no inicializadas     | Falta `SET`                       | Declara y asigna siempre antes de usar.                |

---

## 🧭 7. Preguntas comunes de examen (con respuestas rápidas)

> ❓ ¿Cómo ejecuto un procedimiento almacenado?
> ✅ Con `CALL nombre_procedimiento(parámetros);`

> ❓ ¿Cómo devuelvo un valor desde un procedimiento?
> ✅ Mediante un parámetro `OUT` y `SET nombre_parametro = valor;`.

> ❓ ¿Puedo usar `SELECT` dentro de un procedimiento?
> ✅ Sí, para devolver resultados o hacer consultas internas.

> ❓ ¿Qué diferencia hay entre procedimiento y función?
> ✅ La **función devuelve un valor**, el **procedimiento ejecuta acciones**.

> ❓ ¿Se puede lanzar un error manual dentro del procedimiento?
> ✅ Sí, con `SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'mensaje';`.

> ❓ ¿Qué ventajas tiene centralizar la lógica en procedimientos?
> ✅ Reutilización, seguridad, rendimiento y consistencia en reglas de negocio.

---

## 🎯 8. Resumen visual

| Procedimiento                | Propósito             | Palabras clave                      | Ejemplo de uso                              |
| ---------------------------- | --------------------- | ----------------------------------- | ------------------------------------------- |
| `sp_RegistrarVenta`          | Registrar nueva venta | `INSERT`, `OUT`, `LAST_INSERT_ID()` | `CALL sp_RegistrarVenta(3,NOW(),10000,@id)` |
| `sp_AplicarDescuentoVenta`   | Aplicar descuento     | `UPDATE`, `SET`, `%`                | `CALL sp_AplicarDescuentoVenta(101,15)`     |
| `sp_ActualizarStockProducto` | Reducir stock         | `UPDATE`, `WHERE`                   | `CALL sp_ActualizarStockProducto(5,2)`      |
| `sp_ClientesFrecuentes`      | Listar top clientes   | `GROUP BY`, `HAVING`                | `CALL sp_ClientesFrecuentes(5)`             |
| `sp_ResumenDiarioVentas`     | Total diario          | `COUNT`, `SUM`, `DATE()`            | `CALL sp_ResumenDiarioVentas('2025-10-30')` |

---

## 🧩 9. Consejos para examen oral o escrito

* Si te piden **“crea un procedimiento”**, sigue este orden mental:

  1. `DELIMITER $$`
  2. `CREATE PROCEDURE nombre`
  3. Define parámetros `IN / OUT`.
  4. Escribe el bloque `BEGIN … END`.
  5. Cierra con `DELIMITER ;`

* Explica con tus palabras **qué hace cada parte**.

* Siempre que uses un parámetro de salida, menciona `OUT` y `LAST_INSERT_ID()` si corresponde.

* Si puedes, agrega **validaciones** (`IF`, `SIGNAL`) para mostrar dominio del tema.

---
