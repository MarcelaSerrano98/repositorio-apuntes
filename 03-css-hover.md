
# 🖱️ Pseudoclase `:hover` en CSS

## 📌 ¿Qué es `:hover`?

La pseudoclase `:hover` en CSS se activa cuando el puntero del mouse pasa por encima de un elemento. Se utiliza para crear efectos visuales dinámicos e interactivos, como cambiar colores, escalar elementos, mostrar sombras, entre otros.

```css
selector:hover {
  /* estilos cuando el mouse está encima */
}
```

---

## 🔧 Propiedades más comunes que se usan con `:hover`

| Propiedad          | Descripción                                      |
|--------------------|--------------------------------------------------|
| `background-color` | Cambia el color de fondo                         |
| `color`            | Cambia el color del texto                        |
| `transform`        | Aplica escalas, rotaciones o desplazamientos     |
| `opacity`          | Cambia la opacidad (transparencia)               |
| `box-shadow`       | Añade sombra alrededor del elemento              |
| `border`           | Cambia color, estilo o grosor del borde          |
| `scale()`          | Escala el tamaño del elemento (`transform`)      |
| `transition`       | Hace que los cambios ocurran de forma suave      |

---

## 🎯 Ejemplos útiles

### ✅ Cambiar color de fondo

```css
button {
  background-color: #3498db;
  color: white;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #1abc9c;
}
```

### ✅ Aumentar tamaño de imagen al pasar el cursor

```css
img {
  width: 200px;
  transition: transform 0.3s ease;
}

img:hover {
  transform: scale(1.1);
}
```

### ✅ Mostrar sombra suave al pasar el mouse

```css
.card {
  box-shadow: none;
  transition: box-shadow 0.3s ease;
}

.card:hover {
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}
```

### ✅ Subir un poco un botón

```css
button {
  transform: translateY(0);
  transition: transform 0.2s ease;
}

button:hover {
  transform: translateY(-4px);
}
```

---

## 🧠 Buenas prácticas

- Siempre usa `transition` para suavizar el efecto.
- No abuses de `:hover` en móviles (no hay cursor).
- Usa efectos que ayuden al usuario, no solo por estética.
- Combina `:hover` con clases para efectos más controlados.

---

## 🧪 Extra: Mostrar un tooltip al pasar el mouse

```html
<div class="tooltip-container">Pasa el mouse
  <span class="tooltip-text">¡Hola! Soy un tooltip</span>
</div>

<style>
.tooltip-container {
  position: relative;
  display: inline-block;
}

.tooltip-text {
  visibility: hidden;
  position: absolute;
  background-color: black;
  color: white;
  padding: 5px;
  border-radius: 5px;
  top: 120%;
  left: 50%;
  transform: translateX(-50%);
  opacity: 0;
  transition: opacity 0.3s;
}

.tooltip-container:hover .tooltip-text {
  visibility: visible;
  opacity: 1;
}
</style>
```

---

## 📌 Resumen

- `:hover` es esencial para mejorar la interactividad de una web.
- Se usa con `transition` para efectos suaves.
- Funciona con casi cualquier propiedad visual.
