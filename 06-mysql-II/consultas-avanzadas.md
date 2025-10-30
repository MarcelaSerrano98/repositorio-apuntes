

# 🧩 Guía de Estudio Completa — Archivo `02_Consultas_Avanzadas.sql`

---

## 📘 1. ¿Qué es una consulta avanzada?

Una **consulta avanzada** en SQL es toda aquella que:

* Usa más de una tabla (JOINs).
* Realiza cálculos o agrupaciones (`COUNT`, `SUM`, `AVG`, `MAX`, `MIN`).
* Aplica filtros (`WHERE`, `HAVING`).
* O incluye subconsultas dentro de otras consultas.

👉 En palabras simples:

> “Una consulta avanzada combina, agrupa y filtra datos para responder preguntas del negocio.”

---

### 💬 Preguntas con respuesta

> ❓ ¿Cuál es la diferencia entre una consulta simple y una avanzada?
> ✅ La simple consulta una tabla; la avanzada **relaciona varias** o realiza **operaciones de análisis**.

> ❓ ¿Qué es un JOIN?
> ✅ Es la unión de dos o más tablas según una relación entre ellas.

> ❓ ¿Qué función tiene GROUP BY?
> ✅ Agrupar registros por una o más columnas para aplicar funciones como `SUM` o `COUNT`.

---

## 🧱 2. Tipos de JOINS (uniones)

### 🔹 INNER JOIN

Devuelve solo las filas que **coinciden** en ambas tablas.

```sql
SELECT c.nombre, v.id_ventas, v.total
FROM clientes c
INNER JOIN ventas v ON c.id_cliente = v.clientes_id_cliente;
```

🧠 Solo muestra clientes **que tienen ventas**.

---

### 🔹 LEFT JOIN

Devuelve **todas las filas de la izquierda**, aunque no tengan coincidencia en la derecha.

```sql
SELECT c.nombre, v.total
FROM clientes c
LEFT JOIN ventas v ON c.id_cliente = v.clientes_id_cliente;
```

🧠 Muestra todos los clientes, incluso los que **nunca compraron** (los totales aparecerán `NULL`).

---

### 🔹 RIGHT JOIN

Devuelve todas las filas de la tabla derecha (rara vez se usa).

```sql
SELECT p.nombre, dv.cantidad
FROM productos p
RIGHT JOIN detalle_ventas dv ON p.id_producto = dv.productos_id_producto;
```

---

### 🔹 FULL OUTER JOIN (simulado)

MySQL no lo tiene directamente, pero se puede simular con `UNION`.

```sql
SELECT * FROM tablaA
LEFT JOIN tablaB ON ...
UNION
SELECT * FROM tablaA
RIGHT JOIN tablaB ON ...;
```

---

### 💬 Preguntas con respuesta

> ❓ ¿Qué JOIN muestra solo coincidencias?
> ✅ `INNER JOIN`.

> ❓ ¿Qué JOIN muestra todos los registros aunque no coincidan?
> ✅ `LEFT JOIN`.

> ❓ ¿Qué hace `ON` en un JOIN?
> ✅ Define la condición que conecta ambas tablas.

---

## 📊 3. Funciones de agregación

| Función   | Significado  | Ejemplo                        |
| --------- | ------------ | ------------------------------ |
| `COUNT()` | Cuenta filas | `COUNT(*) AS total_ventas`     |
| `SUM()`   | Suma valores | `SUM(total) AS ventas_totales` |
| `AVG()`   | Promedio     | `AVG(total)`                   |
| `MAX()`   | Mayor valor  | `MAX(precio)`                  |
| `MIN()`   | Menor valor  | `MIN(precio)`                  |

Ejemplo:

```sql
SELECT COUNT(*) AS total_clientes FROM clientes;
SELECT SUM(total) AS total_ventas FROM ventas;
SELECT AVG(total) AS promedio_ventas FROM ventas;
```

---

### 💬 Preguntas con respuesta

> ❓ ¿Cuál es la diferencia entre `COUNT(*)` y `COUNT(columna)`?
> ✅ `COUNT(*)` cuenta todas las filas, `COUNT(columna)` solo las que no son `NULL`.

> ❓ ¿Qué hace `AVG()`?
> ✅ Calcula el promedio de una columna numérica.

> ❓ ¿Qué función devuelve el precio más alto?
> ✅ `MAX(precio)`.

---

## ⚙️ 4. Agrupaciones con `GROUP BY` y `HAVING`

```sql
SELECT clientes_id_cliente, SUM(total) AS total_por_cliente
FROM ventas
GROUP BY clientes_id_cliente
HAVING SUM(total) > 500000;
```

🧠 `GROUP BY` agrupa, `HAVING` filtra resultados agregados.

---

### 💬 Preguntas con respuesta

> ❓ ¿Por qué no se usa `WHERE` en lugar de `HAVING`?
> ✅ `WHERE` filtra antes de agrupar, `HAVING` después de aplicar funciones agregadas.

> ❓ ¿Qué pasa si olvidas el `GROUP BY`?
> ✅ MySQL lanza error: “column not in GROUP BY”.

---

## 🔍 5. Subconsultas

Una **subconsulta** es una consulta dentro de otra.

Ejemplo:

```sql
SELECT nombre, total
FROM ventas
WHERE total > (SELECT AVG(total) FROM ventas);
```

🧠 Devuelve ventas **mayores al promedio general.**

---

Otro ejemplo:

```sql
SELECT nombre
FROM clientes
WHERE id_cliente IN (SELECT clientes_id_cliente FROM ventas);
```

👉 Muestra solo clientes que **sí realizaron compras.**

---

### 💬 Preguntas con respuesta

> ❓ ¿Qué es una subconsulta correlacionada?
> ✅ Es una subconsulta que depende de la fila actual de la consulta principal.

> ❓ ¿Qué operador se usa para comparar con subconsultas?
> ✅ `IN`, `EXISTS`, `>`, `<`, `=`.

---

## 🧮 6. Consultas comunes del archivo `02_Consultas_Avanzadas.sql`

---

### 🔹 1. Clientes con más de 3 compras

```sql
SELECT c.nombre, COUNT(v.id_ventas) AS compras
FROM clientes c
JOIN ventas v ON c.id_cliente = v.clientes_id_cliente
GROUP BY c.id_cliente
HAVING compras > 3;
```

🧠 Cuenta las ventas por cliente y filtra los que superan 3.

---

### 🔹 2. Producto más vendido

```sql
SELECT p.nombre, SUM(dv.cantidad) AS total_vendido
FROM productos p
JOIN detalle_ventas dv ON p.id_producto = dv.productos_id_producto
GROUP BY p.id_producto
ORDER BY total_vendido DESC
LIMIT 1;
```

🧠 Usa `SUM` + `GROUP BY` + `ORDER BY DESC` + `LIMIT 1`.

---

### 🔹 3. Total de ventas por mes

```sql
SELECT DATE_FORMAT(fecha_venta, '%Y-%m') AS mes, SUM(total) AS monto
FROM ventas
GROUP BY mes
ORDER BY mes;
```

🧠 `DATE_FORMAT` extrae mes y año, ideal para reportes.

---

### 🔹 4. Clientes sin compras

```sql
SELECT c.nombre
FROM clientes c
LEFT JOIN ventas v ON c.id_cliente = v.clientes_id_cliente
WHERE v.id_ventas IS NULL;
```

🧠 Usa `LEFT JOIN` + `IS NULL` para encontrar inactivos.

---

### 🔹 5. Ventas que superan el promedio

```sql
SELECT id_ventas, total
FROM ventas
WHERE total > (SELECT AVG(total) FROM ventas);
```

🧠 Subconsulta compara cada venta con el promedio global.

---

### 🔹 6. Promedio de productos por venta

```sql
SELECT AVG(cantidades) AS promedio
FROM (
  SELECT COUNT(*) AS cantidades
  FROM detalle_ventas
  GROUP BY ventas_id_ventas
) AS sub;
```

🧠 Subconsulta dentro del FROM — calcula promedio de productos por factura.

---

## 🧠 7. Funciones útiles de MySQL en consultas avanzadas

| Función              | Uso                     | Ejemplo                             |
| -------------------- | ----------------------- | ----------------------------------- |
| `DATE()`             | Extrae solo la fecha    | `DATE(fecha_venta)`                 |
| `YEAR()` / `MONTH()` | Extrae año/mes          | `YEAR(fecha_venta)`                 |
| `CONCAT()`           | Une cadenas             | `CONCAT(nombre, ' - ', correo)`     |
| `IFNULL()`           | Sustituye valores nulos | `IFNULL(telefono, 'No registrado')` |
| `ROUND()`            | Redondea valores        | `ROUND(total, 2)`                   |
| `NOW()`              | Fecha y hora actual     | `NOW()`                             |

---

## 💡 8. Ejercicios tipo examen con solución

---

### 🧩 Ejercicio 1 — Listar productos sin ventas

**Enunciado:**
Mostrar los productos que nunca se vendieron.

✅ **Solución:**

```sql
SELECT p.nombre
FROM productos p
LEFT JOIN detalle_ventas dv ON p.id_producto = dv.productos_id_producto
WHERE dv.productos_id_producto IS NULL;
```

🧠 Se usa `LEFT JOIN` con condición `IS NULL`.

---

### 🧩 Ejercicio 2 — Clientes que gastaron más de 1 millón

```sql
SELECT c.nombre, SUM(v.total) AS gasto_total
FROM clientes c
JOIN ventas v ON c.id_cliente = v.clientes_id_cliente
GROUP BY c.id_cliente
HAVING gasto_total > 1000000;
```

---

### 🧩 Ejercicio 3 — Producto con mayor ingreso total

```sql
SELECT p.nombre, SUM(dv.cantidad * dv.precio_unitario_congelado) AS ingresos
FROM productos p
JOIN detalle_ventas dv ON p.id_producto = dv.productos_id_producto
GROUP BY p.id_producto
ORDER BY ingresos DESC
LIMIT 1;
```

---

### 🧩 Ejercicio 4 — Promedio de ventas por cliente

```sql
SELECT c.nombre, AVG(v.total) AS promedio
FROM clientes c
JOIN ventas v ON c.id_cliente = v.clientes_id_cliente
GROUP BY c.id_cliente;
```

---

### 🧩 Ejercicio 5 — Ventas entre dos fechas

```sql
SELECT * FROM ventas
WHERE fecha_venta BETWEEN '2025-01-01' AND '2025-12-31';
```

---

### 🧩 Ejercicio 6 — Subconsulta para clientes top 3

```sql
SELECT nombre
FROM clientes
WHERE id_cliente IN (
    SELECT clientes_id_cliente
    FROM ventas
    GROUP BY clientes_id_cliente
    ORDER BY SUM(total) DESC
    LIMIT 3
);
```

---

## ⚠️ 9. Errores comunes

| Error                           | Causa                                 | Solución                              |
| ------------------------------- | ------------------------------------- | ------------------------------------- |
| “Unknown column”                | Alias mal escrito o fuera del alcance | Revisa nombres exactos                |
| “Invalid use of group function” | Usar `SUM()` dentro de `WHERE`        | Mover la función a `HAVING`           |
| “Column not in GROUP BY”        | Falta incluir columna en agrupación   | Añadirla en `GROUP BY`                |
| “NULL results”                  | Faltan coincidencias en JOIN          | Revisar condición `ON` y tipo de JOIN |

---

## 🧠 10. Posibles preguntas del examen (respuestas rápidas)

> ❓ ¿Qué diferencia hay entre INNER JOIN y LEFT JOIN?
> ✅ INNER devuelve coincidencias, LEFT incluye todos los de la izquierda.

> ❓ ¿Qué comando agrupa resultados?
> ✅ `GROUP BY`.

> ❓ ¿Para qué sirve HAVING?
> ✅ Filtra resultados después de agrupar.

> ❓ ¿Qué función devuelve el promedio?
> ✅ `AVG()`.

> ❓ ¿Qué comando sirve para ordenar?
> ✅ `ORDER BY`.

> ❓ ¿Qué hace LIMIT?
> ✅ Restringe la cantidad de resultados.

> ❓ ¿Qué comando muestra clientes sin compras?
> ✅ `LEFT JOIN` + `IS NULL`.

---

## 🎯 11. Resumen visual

| Tipo de consulta   | Objetivo                 | Palabras clave               |
| ------------------ | ------------------------ | ---------------------------- |
| Relacionar tablas  | Combinar datos           | `JOIN`, `ON`                 |
| Agrupar datos      | Sumar o contar           | `GROUP BY`, `SUM`, `COUNT`   |
| Filtrar resultados | Limitar valores          | `WHERE`, `HAVING`            |
| Anidar consultas   | Calcular comparaciones   | `IN`, `EXISTS`, subconsultas |
| Ordenar            | Mostrar de mayor a menor | `ORDER BY DESC`              |
| Limitar resultados | Mostrar top N            | `LIMIT`                      |

---

## 🧩 12. Consejos para examen

✅ Siempre verifica si la consulta requiere **JOIN o subconsulta**.
✅ Si hay funciones de resumen (`SUM`, `COUNT`), **debes usar `GROUP BY`**.
✅ Usa `HAVING` para filtrar resultados agregados.
✅ Domina las funciones `DATE_FORMAT`, `AVG`, `SUM`, `COUNT`.
✅ Practica combinaciones de consultas: JOIN + GROUP BY + HAVING.
✅ Si te piden “clientes sin compras”, **piensa en LEFT JOIN + IS NULL**.
✅ Si te piden “ventas mayores al promedio”, **usa subconsulta con AVG**.

---

