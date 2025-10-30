
---

# 🛡️ Guía de Estudio Completa — Archivo `04_Seguridad.sql`

---

## 📘 1. ¿Qué es la seguridad en bases de datos?

La **seguridad en bases de datos** consiste en **proteger los datos, los usuarios y las operaciones** para evitar accesos no autorizados, pérdidas o manipulaciones indebidas.

En MySQL, la seguridad se maneja a nivel de:

* 🔑 **Usuarios** → quién puede acceder.
* 🧱 **Privilegios** → qué puede hacer (leer, escribir, borrar, etc.).
* 🧩 **Roles y contraseñas** → cómo se autentica.
* 🔒 **Vistas y permisos limitados** → qué datos puede ver cada usuario.

---

### 💬 Preguntas con respuesta

> ❓ ¿Cuál es el objetivo principal de la seguridad en una BD?
> ✅ Proteger la **confidencialidad**, **integridad** y **disponibilidad** de los datos.

> ❓ ¿Qué significa “principio del mínimo privilegio”?
> ✅ Cada usuario solo debe tener los permisos **estrictamente necesarios** para su trabajo.

> ❓ ¿Qué tipo de seguridad maneja MySQL?
> ✅ Seguridad a nivel de **servidor, base de datos, tabla, columna y procedimiento**.

---

## 🧩 2. Usuarios en MySQL

Un usuario en MySQL tiene este formato:

```sql
'usuario'@'host'
```

Ejemplo:

```sql
CREATE USER 'juan'@'localhost' IDENTIFIED BY '1234';
CREATE USER 'maria'@'%' IDENTIFIED BY 'claveSegura';
```

* `'localhost'` → solo puede conectarse desde la misma máquina.
* `'%'` → puede conectarse desde cualquier host (menos seguro).
* La contraseña puede cambiarse con `ALTER USER`.

---

### 💬 Preguntas con respuesta

> ❓ ¿Qué diferencia hay entre `'user'@'localhost'` y `'user'@'%'`?
> ✅ `'localhost'` limita el acceso al servidor local, mientras que `'%'` permite acceso remoto.

> ❓ ¿Cómo cambias la contraseña de un usuario?
>
> ```sql
> ALTER USER 'juan'@'localhost' IDENTIFIED BY 'nuevaClave';
> ```

> ❓ ¿Qué pasa si no defines host?
> ✅ MySQL lo asume como `'%'` (acceso abierto).

---

## 🔐 3. Privilegios y permisos

Los privilegios definen qué puede hacer un usuario.

```sql
GRANT privilegio ON base.tabla TO 'usuario'@'host';
REVOKE privilegio ON base.tabla FROM 'usuario'@'host';
```

### Ejemplo:

```sql
GRANT SELECT, INSERT ON tienda.productos TO 'empleado'@'localhost';
REVOKE DELETE ON tienda.productos FROM 'empleado'@'localhost';
```

### Privilegios comunes:

| Privilegio       | Qué permite hacer       |
| ---------------- | ----------------------- |
| `SELECT`         | Leer datos              |
| `INSERT`         | Insertar registros      |
| `UPDATE`         | Modificar registros     |
| `DELETE`         | Eliminar registros      |
| `CREATE`         | Crear tablas o vistas   |
| `DROP`           | Borrar tablas o bases   |
| `ALTER`          | Cambiar estructura      |
| `EXECUTE`        | Ejecutar procedimientos |
| `ALL PRIVILEGES` | Todos los permisos      |

---

### 💬 Preguntas con respuesta

> ❓ ¿Qué comando da permisos?
> ✅ `GRANT`.

> ❓ ¿Qué comando los quita?
> ✅ `REVOKE`.

> ❓ ¿Qué hace `GRANT ALL PRIVILEGES ON *.* TO 'admin'@'localhost';`?
> ✅ Da **todos los permisos sobre todas las bases y tablas** al usuario `admin`.

> ❓ ¿Qué comando aplicas para ver los permisos actuales?
> ✅ `SHOW GRANTS FOR 'usuario'@'host';`

---

## 👥 4. Roles

Los **roles** son grupos de permisos que se pueden asignar a varios usuarios.
Ayudan a no repetir los mismos `GRANT` una y otra vez.

```sql
CREATE ROLE 'rol_vendedor';
GRANT SELECT, INSERT ON tienda.ventas TO 'rol_vendedor';
GRANT 'rol_vendedor' TO 'juan'@'localhost';
SET DEFAULT ROLE 'rol_vendedor' TO 'juan'@'localhost';
```

🧠 El rol actúa como una “plantilla” de permisos.

---

### 💬 Preguntas con respuesta

> ❓ ¿Qué ventaja tienen los roles?
> ✅ Permiten **asignar permisos en grupo**, facilitando la administración.

> ❓ ¿Qué diferencia hay entre un rol y un usuario?
> ✅ El rol **no inicia sesión**, solo agrupa permisos.

> ❓ ¿Cómo activas un rol?
> ✅ `SET ROLE nombre_rol;`

---

## 🔍 5. Ejemplo de implementación (extraído del archivo)

```sql
CREATE USER 'cajero'@'localhost' IDENTIFIED BY '1234';
CREATE USER 'supervisor'@'localhost' IDENTIFIED BY 'admin123';

GRANT SELECT, INSERT ON tienda.ventas TO 'cajero'@'localhost';
GRANT ALL PRIVILEGES ON tienda.* TO 'supervisor'@'localhost';

CREATE ROLE 'rol_admin';
GRANT ALL PRIVILEGES ON tienda.* TO 'rol_admin';
GRANT 'rol_admin' TO 'supervisor'@'localhost';
```

### Explicación:

1. Se crean dos usuarios (`cajero`, `supervisor`).
2. Se da acceso limitado al cajero y total al supervisor.
3. Se define un rol de administrador y se asigna al supervisor.

---

### 💬 Preguntas con respuesta

> ❓ ¿Qué diferencia hay entre `GRANT ALL PRIVILEGES` y asignar un rol?
> ✅ El `GRANT` da permisos directos al usuario, el **rol** puede reutilizarse en otros usuarios.

> ❓ ¿Por qué el cajero no tiene permisos de `DELETE`?
> ✅ Para evitar que elimine registros de ventas (seguridad por nivel de puesto).

> ❓ ¿Cómo revocar todos los permisos de un rol?
> ✅ `REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'rol_admin';`

---

## 🧠 6. Seguridad avanzada (bloques y vistas)

Para proteger datos sensibles, se pueden usar **vistas limitadas** o procedimientos.

Ejemplo:

```sql
CREATE VIEW vista_clientes_publica AS
SELECT nombre, correo FROM clientes;
GRANT SELECT ON vista_clientes_publica TO 'empleado'@'localhost';
```

👉 Así el empleado **ve solo columnas autorizadas**.

---

### 💬 Preguntas con respuesta

> ❓ ¿Por qué usar vistas en seguridad?
> ✅ Porque permiten **ocultar columnas confidenciales** (como contraseñas, salarios, etc.).

> ❓ ¿Qué comando permite ejecutar procedimientos a ciertos usuarios?
> ✅ `GRANT EXECUTE ON PROCEDURE nombre TO 'usuario';`

> ❓ ¿Qué ocurre si eliminas una vista que tiene permisos?
> ✅ Los permisos se eliminan automáticamente con la vista.

---

## ⚙️ 7. Auditoría y control

Para mantener trazabilidad, puedes crear una tabla de auditoría que registre quién hizo qué acción.

Ejemplo:

```sql
CREATE TABLE auditoria (
    id INT AUTO_INCREMENT PRIMARY KEY,
    usuario VARCHAR(50),
    accion VARCHAR(100),
    fecha TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TRIGGER tr_log_insert AFTER INSERT ON ventas
FOR EACH ROW
INSERT INTO auditoria (usuario, accion)
VALUES (CURRENT_USER(), 'INSERT en ventas');
```

🧠 Con esto, cada inserción deja rastro del usuario y fecha.

---

### 💬 Preguntas con respuesta

> ❓ ¿Qué devuelve `CURRENT_USER()`?
> ✅ El usuario MySQL que ejecuta la instrucción.

> ❓ ¿Cuál es la diferencia entre `CURRENT_USER()` y `USER()`?
> ✅ `CURRENT_USER()` devuelve el usuario **autenticado**,
> `USER()` muestra **cómo se conectó (nombre@host)**.

---

## 🚨 8. Errores comunes

| Error                            | Causa                          | Solución                                   |
| -------------------------------- | ------------------------------ | ------------------------------------------ |
| “Access denied for user…”        | Usuario sin permisos adecuados | Revisar `GRANT` y `FLUSH PRIVILEGES`       |
| “You must have SUPER privilege…” | Acción restringida             | Ejecutar como usuario root o administrador |
| “Unknown user”                   | Usuario no creado              | Revisar `CREATE USER` y nombres exactos    |
| “Can’t connect”                  | Host incorrecto                | Verificar `'localhost'` vs `'%'`           |

---

## 💡 9. Ejercicios tipo examen (con solución)

---

### 🧩 Ejercicio 1 — Crear un usuario y otorgar permisos de lectura

**Enunciado:**
Crea un usuario llamado `reportes` que solo pueda leer la tabla `ventas`.

**Solución:**

```sql
CREATE USER 'reportes'@'localhost' IDENTIFIED BY 'r3p0rtes';
GRANT SELECT ON tienda.ventas TO 'reportes'@'localhost';
```

---

### 🧩 Ejercicio 2 — Crear un rol de analista

```sql
CREATE ROLE 'rol_analista';
GRANT SELECT, UPDATE ON tienda.productos TO 'rol_analista';
GRANT 'rol_analista' TO 'sofia'@'localhost';
SET DEFAULT ROLE 'rol_analista' TO 'sofia'@'localhost';
```

🧠 El usuario “sofia” hereda permisos de lectura y actualización en productos.

---

### 🧩 Ejercicio 3 — Revocar permisos

```sql
REVOKE UPDATE ON tienda.productos FROM 'sofia'@'localhost';
```

💡 El usuario conserva solo `SELECT`.

---

### 🧩 Ejercicio 4 — Auditoría de acciones

Crea un trigger que registre quién borra productos.

```sql
CREATE TRIGGER tr_log_delete_producto
AFTER DELETE ON productos
FOR EACH ROW
INSERT INTO auditoria (usuario, accion)
VALUES (CURRENT_USER(), CONCAT('Eliminó producto ID: ', OLD.id_producto));
```

---

### 🧩 Ejercicio 5 — Usuario solo para ejecutar procedimientos

```sql
CREATE USER 'operador'@'localhost' IDENTIFIED BY 'proc123';
GRANT EXECUTE ON PROCEDURE sp_RegistrarVenta TO 'operador'@'localhost';
```

🧠 “operador” solo puede ejecutar el procedimiento, no modificar datos directamente.

---

## 🧾 10. Preguntas comunes de examen (con respuestas rápidas)

> ❓ ¿Qué comando crea un usuario?
> ✅ `CREATE USER 'nombre'@'host' IDENTIFIED BY 'contraseña';`

> ❓ ¿Qué diferencia hay entre `GRANT` y `REVOKE`?
> ✅ `GRANT` da permisos, `REVOKE` los quita.

> ❓ ¿Qué hace `FLUSH PRIVILEGES;`?
> ✅ Refresca los permisos sin reiniciar el servidor.

> ❓ ¿Qué es un rol?
> ✅ Un conjunto de permisos reutilizables para varios usuarios.

> ❓ ¿Cómo limito el acceso de un usuario solo a una base?
> ✅ `GRANT ALL PRIVILEGES ON tienda.* TO 'usuario'@'localhost';`

> ❓ ¿Cómo ver los permisos actuales de un usuario?
> ✅ `SHOW GRANTS FOR 'usuario'@'host';`

---

## 🎯 11. Resumen visual

| Concepto        | Comando                    | Ejemplo                                                   |
| --------------- | -------------------------- | --------------------------------------------------------- |
| Crear usuario   | `CREATE USER`              | `CREATE USER 'juan'@'localhost' IDENTIFIED BY '1234';`    |
| Dar permisos    | `GRANT`                    | `GRANT SELECT ON tienda.ventas TO 'juan'@'localhost';`    |
| Quitar permisos | `REVOKE`                   | `REVOKE DELETE ON tienda.ventas FROM 'juan'@'localhost';` |
| Crear rol       | `CREATE ROLE`              | `CREATE ROLE 'rol_admin';`                                |
| Asignar rol     | `GRANT rol TO usuario;`    | `GRANT 'rol_admin' TO 'maria'@'localhost';`               |
| Ver permisos    | `SHOW GRANTS FOR usuario;` | `SHOW GRANTS FOR 'maria'@'localhost';`                    |

---

## 🧩 12. Consejos para examen

✅ Menciona siempre el **principio del mínimo privilegio**.
✅ Practica escribir `CREATE USER`, `GRANT`, `REVOKE`, `CREATE ROLE`.
✅ Explica que los permisos pueden ser a nivel **global**, **base**, **tabla**, **columna** o **procedimiento**.
✅ Si te piden "restringir datos confidenciales", usa **vistas**.
✅ Si te preguntan por auditoría, menciona los **triggers** y `CURRENT_USER()`.

---
