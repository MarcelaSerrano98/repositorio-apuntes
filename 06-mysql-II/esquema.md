
---

# 🧩 Guía de Estudio Completa — Archivo `01_Esquema_y_Datos.sql`

---

## 📘 1. ¿Qué es el esquema de base de datos?

El **esquema** es el **plano estructural** de la base de datos.
Define **cómo se organizan los datos**, **qué tablas existen**, **cómo se relacionan entre sí**, y **qué tipo de información guarda cada una**.

👉 En palabras simples:

> “El esquema es como el esqueleto de la base de datos: contiene las tablas, columnas, tipos de datos y relaciones.”

---

### 💬 Preguntas con respuesta

> ❓ ¿Qué diferencia hay entre base de datos y esquema?
> ✅ La base de datos es el **contenedor completo**, el esquema es **su estructura lógica interna**.

> ❓ ¿Qué representa cada tabla?
> ✅ Una entidad del negocio (clientes, productos, ventas, etc.).

> ❓ ¿Qué representa cada columna?
> ✅ Un atributo o propiedad de esa entidad (nombre, precio, fecha, cantidad...).

---

## 🧱 2. Estructura típica del archivo `01_Esquema_y_Datos.sql`

Generalmente, el archivo contiene sentencias como:

```sql
CREATE DATABASE tienda;
USE tienda;

CREATE TABLE clientes (
    id_cliente INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    correo VARCHAR(100) UNIQUE,
    telefono VARCHAR(20),
    fecha_nacimiento DATE
);

CREATE TABLE productos (
    id_producto INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    precio DECIMAL(10,2) NOT NULL,
    stock INT DEFAULT 0,
    categoria VARCHAR(50)
);

CREATE TABLE ventas (
    id_ventas INT AUTO_INCREMENT PRIMARY KEY,
    clientes_id_cliente INT,
    fecha_venta DATETIME,
    total DECIMAL(10,2),
    FOREIGN KEY (clientes_id_cliente) REFERENCES clientes(id_cliente)
);

CREATE TABLE detalle_ventas (
    id_detalle INT AUTO_INCREMENT PRIMARY KEY,
    ventas_id_ventas INT,
    productos_id_producto INT,
    cantidad INT,
    precio_unitario_congelado DECIMAL(10,2),
    FOREIGN KEY (ventas_id_ventas) REFERENCES ventas(id_ventas),
    FOREIGN KEY (productos_id_producto) REFERENCES productos(id_producto)
);
```

---

## 🧮 3. Explicación de cada tabla y sus relaciones

---

### 👥 Tabla `clientes`

**Propósito:**
Guardar la información de los clientes que realizan compras.

| Campo              | Tipo de dato             | Propósito                            |
| ------------------ | ------------------------ | ------------------------------------ |
| `id_cliente`       | INT (PK, AUTO_INCREMENT) | Identificador único                  |
| `nombre`           | VARCHAR(100)             | Nombre del cliente                   |
| `correo`           | VARCHAR(100), `UNIQUE`   | Correo electrónico único             |
| `telefono`         | VARCHAR(20)              | Número de contacto                   |
| `fecha_nacimiento` | DATE                     | Fecha para cumpleaños o segmentación |

🧠 **Claves:**

* PK: `id_cliente`
* Restricción `UNIQUE` en `correo`.

📌 **Ejemplo de inserción:**

```sql
INSERT INTO clientes (nombre, correo, telefono, fecha_nacimiento)
VALUES ('Juan Pérez', 'juanp@gmail.com', '3121234567', '1995-08-15');
```

---

### 📦 Tabla `productos`

**Propósito:**
Registrar los artículos o productos disponibles para la venta.

| Campo         | Tipo          | Propósito                         |
| ------------- | ------------- | --------------------------------- |
| `id_producto` | INT (PK)      | Identificador único               |
| `nombre`      | VARCHAR(100)  | Nombre del producto               |
| `precio`      | DECIMAL(10,2) | Precio actual                     |
| `stock`       | INT           | Cantidad disponible               |
| `categoria`   | VARCHAR(50)   | Clasificación (ej. “Electrónica”) |

💡 **Buenas prácticas:**

* Siempre declarar el stock con `DEFAULT 0`.
* No permitir precios nulos (`NOT NULL`).

📌 **Ejemplo:**

```sql
INSERT INTO productos (nombre, precio, stock, categoria)
VALUES ('Teclado Gamer', 120000, 20, 'Accesorios');
```

---

### 💰 Tabla `ventas`

**Propósito:**
Registrar cada transacción realizada por los clientes.

| Campo                 | Tipo          | Propósito                     |
| --------------------- | ------------- | ----------------------------- |
| `id_ventas`           | INT (PK)      | ID único de venta             |
| `clientes_id_cliente` | INT (FK)      | Cliente que realiza la compra |
| `fecha_venta`         | DATETIME      | Fecha y hora                  |
| `total`               | DECIMAL(10,2) | Monto total de la venta       |

🔗 **Relación:**
Cada venta pertenece a **un cliente**, pero **un cliente puede tener muchas ventas** (relación 1:N).

📌 **Ejemplo:**

```sql
INSERT INTO ventas (clientes_id_cliente, fecha_venta, total)
VALUES (1, NOW(), 350000);
```

---

### 🧾 Tabla `detalle_ventas`

**Propósito:**
Guardar los productos asociados a cada venta.

| Campo                       | Tipo          | Propósito                                     |
| --------------------------- | ------------- | --------------------------------------------- |
| `id_detalle`                | INT (PK)      | Identificador único                           |
| `ventas_id_ventas`          | INT (FK)      | Venta a la que pertenece                      |
| `productos_id_producto`     | INT (FK)      | Producto vendido                              |
| `cantidad`                  | INT           | Número de unidades                            |
| `precio_unitario_congelado` | DECIMAL(10,2) | Precio del producto en el momento de la venta |

🧠 **Claves:**

* `FOREIGN KEY (ventas_id_ventas)` → `ventas(id_ventas)`
* `FOREIGN KEY (productos_id_producto)` → `productos(id_producto)`

💬 **Importante:**
Este diseño permite guardar el **precio congelado** (para evitar que si el precio cambia luego, se altere el historial).

📌 **Ejemplo:**

```sql
INSERT INTO detalle_ventas (ventas_id_ventas, productos_id_producto, cantidad, precio_unitario_congelado)
VALUES (1, 3, 2, 120000);
```

---

## 🧠 4. Relaciones del modelo (DER lógico)

Visualmente el modelo es así:

```
CLIENTES (1) ────< (N) VENTAS (1) ────< (N) DETALLE_VENTAS >──── (N) PRODUCTOS
```

Esto significa:

* Un cliente puede tener muchas ventas.
* Una venta puede tener varios productos.
* Un producto puede aparecer en muchas ventas.

📚 Es un **modelo relacional clásico de facturación o tienda.**

---

### 💬 Preguntas con respuesta

> ❓ ¿Qué tipo de relación hay entre `clientes` y `ventas`?
> ✅ Relación **uno a muchos (1:N)**.

> ❓ ¿Qué hace una clave foránea (`FOREIGN KEY`)?
> ✅ Establece la relación entre tablas y garantiza **integridad referencial** (no se pueden insertar IDs que no existan en la tabla padre).

> ❓ ¿Qué pasa si elimino un cliente con ventas asociadas?
> ✅ Si no hay acción definida, dará error (`foreign key constraint fails`).
> Se puede usar `ON DELETE CASCADE` para eliminar también las ventas relacionadas.

> ❓ ¿Qué es la integridad referencial?
> ✅ Es la garantía de que los datos relacionados sean válidos y consistentes entre tablas.

---

## 🔑 5. Tipos de claves

| Tipo               | Descripción                         | Ejemplo               |
| ------------------ | ----------------------------------- | --------------------- |
| **PRIMARY KEY**    | Identifica de forma única una fila. | `id_cliente`          |
| **FOREIGN KEY**    | Conecta tablas relacionadas.        | `clientes_id_cliente` |
| **UNIQUE**         | No permite duplicados.              | `correo`              |
| **AUTO_INCREMENT** | Genera ID automático.               | `id_ventas`           |

---

## ⚙️ 6. Buenas prácticas en el diseño

✅ Siempre usar `NOT NULL` en campos obligatorios.
✅ Establecer relaciones con `FOREIGN KEY`.
✅ Mantener nombres claros (`clientes_id_cliente`, `productos_id_producto`).
✅ Guardar precios como `DECIMAL(10,2)`.
✅ No almacenar datos calculables (como totales) sin justificación.
✅ Usar `ON DELETE CASCADE` o `SET NULL` cuando corresponda.

---

## 🧾 7. Ejercicios tipo examen con solución

---

### 🧩 Ejercicio 1 — Crear una tabla de proveedores

```sql
CREATE TABLE proveedores (
    id_proveedor INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    telefono VARCHAR(20),
    correo VARCHAR(100)
);
```

📌 **Posible pregunta:**

> “Crea una tabla para proveedores con un campo ID autoincremental y correo único.”
> ✅ Solución:

```sql
CREATE TABLE proveedores (
    id_proveedor INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    correo VARCHAR(100) UNIQUE
);
```

---

### 🧩 Ejercicio 2 — Relacionar productos con proveedores

```sql
ALTER TABLE productos
ADD COLUMN id_proveedor INT,
ADD FOREIGN KEY (id_proveedor) REFERENCES proveedores(id_proveedor);
```

📌 **Explicación:**
Cada producto tiene un proveedor (1:N).

---

### 🧩 Ejercicio 3 — Eliminar en cascada

```sql
ALTER TABLE ventas
ADD CONSTRAINT fk_cliente
FOREIGN KEY (clientes_id_cliente)
REFERENCES clientes(id_cliente)
ON DELETE CASCADE;
```

🧠 Si eliminas un cliente, **también se eliminan sus ventas automáticamente.**

---

### 🧩 Ejercicio 4 — Insertar datos iniciales

```sql
INSERT INTO clientes (nombre, correo, telefono) VALUES
('Ana López', 'ana@gmail.com', '321654987'),
('Carlos Ruiz', 'carlos@hotmail.com', '315777888');

INSERT INTO productos (nombre, precio, stock, categoria) VALUES
('Mouse Óptico', 35000, 100, 'Periféricos'),
('Laptop HP', 2300000, 15, 'Computadores');
```

---

### 🧩 Ejercicio 5 — Crear relación entre ventas y detalle_ventas

> **Pregunta típica:**
> “Crea una relación para que cada venta tenga varios productos y cada producto pueda estar en varias ventas.”

✅ **Solución:**

```sql
ALTER TABLE detalle_ventas
ADD FOREIGN KEY (ventas_id_ventas) REFERENCES ventas(id_ventas),
ADD FOREIGN KEY (productos_id_producto) REFERENCES productos(id_producto);
```

---

## 🧠 8. Posibles preguntas de examen (con respuestas)

> ❓ ¿Qué comando crea una base de datos?
> ✅ `CREATE DATABASE nombre;`

> ❓ ¿Qué comando selecciona una base para trabajar?
> ✅ `USE nombre;`

> ❓ ¿Qué comando crea una tabla?
> ✅ `CREATE TABLE nombre (...);`

> ❓ ¿Qué hace `AUTO_INCREMENT`?
> ✅ Genera automáticamente un número único en cada registro nuevo.

> ❓ ¿Qué diferencia hay entre `PRIMARY KEY` y `UNIQUE`?
> ✅ Ambas evitan duplicados, pero **solo puede haber una PRIMARY KEY** y se usa como identificador principal.

> ❓ ¿Qué es una clave foránea?
> ✅ Es un campo que conecta dos tablas y asegura integridad referencial.

> ❓ ¿Qué pasa si elimino una fila referenciada por una FK sin `ON DELETE CASCADE`?
> ✅ MySQL genera error por violar la integridad.

---

## ⚠️ 9. Errores comunes

| Error                                    | Causa                                                            | Solución                                        |
| ---------------------------------------- | ---------------------------------------------------------------- | ----------------------------------------------- |
| “Cannot add foreign key constraint”      | Tipos de datos diferentes entre columnas relacionadas            | Asegurar que ambas columnas sean del mismo tipo |
| “Duplicate entry for key UNIQUE”         | Valor duplicado en campo `UNIQUE`                                | Cambiar valor o quitar restricción              |
| “Table doesn’t exist”                    | Orden incorrecto de creación (relaciones dependen de otra tabla) | Crear tablas padres primero                     |
| “Column count doesn’t match value count” | Faltan o sobran valores en `INSERT`                              | Revisar cantidad de columnas                    |

---

## 🎯 10. Resumen visual

| Tabla            | Propósito                     | Clave primaria | Relación principal          |
| ---------------- | ----------------------------- | -------------- | --------------------------- |
| `clientes`       | Información de clientes       | `id_cliente`   | 1:N con `ventas`            |
| `productos`      | Catálogo de artículos         | `id_producto`  | N:M con `ventas`            |
| `ventas`         | Transacciones realizadas      | `id_ventas`    | 1:N con `detalle_ventas`    |
| `detalle_ventas` | Detalle de productos vendidos | `id_detalle`   | FK a `ventas` y `productos` |

---

## 🧩 11. Consejos para examen

✅ Siempre menciona **“integridad referencial”** cuando hables de relaciones.
✅ Aprende a **leer un modelo ER (Entidad-Relación)**: saber quién es “padre” y quién es “hijo”.
✅ Recuerda que las **claves primarias identifican**, y las **foráneas conectan**.
✅ En una relación **N:M**, siempre hay una tabla intermedia (como `detalle_ventas`).
✅ Practica escribir los `CREATE TABLE` desde cero con PK, FK y `NOT NULL`.

---

