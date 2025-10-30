# Apuntes Completos de HTML y CSS

## HTML

### Estructura Básica

```html
<!DOCTYPE html>
<html lang="es">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="Descripción del sitio">
    <title>Mi sitio</title>
    <link rel="stylesheet" href="styles.css">
    <link rel="icon" type="image/png" href="favicon.png">
  </head>
  <body>
    <!-- Contenido aquí -->
  </body>
</html>
```

### Etiquetas de Input

* `<input type="text">`: entrada de texto.
* `<input type="email">`: entrada de correo.
* `<input type="password">`: entrada de contraseña.
* `<input type="checkbox">`: casilla de verificación.
* `<input type="radio">`: opción seleccionable.
* `<input type="range">`: control deslizante.
* `<input type="color">`: selector de color.
* `<input type="file">`: subir archivo.
* `<input type="datetime-local">`: fecha y hora local.
* `<input type="submit">`: botón para enviar formulario.
* `<textarea>`: caja de texto multilinea.
* `<select>` y `<option>`: menú desplegable.

### Atributos Comunes

* `alt`: descripción alternativa para imágenes.
* `title`: texto que aparece al pasar el mouse.
* `hidden`: oculta un elemento.
* `id`: identificador único.
* `class`: clase para aplicar estilos.
* `required`: campo obligatorio.
* `placeholder`: texto guía dentro de un input.
* `value`: valor por defecto.
* `name`: nombre del campo (usado en formularios).
* `readonly`: solo lectura.
* `disabled`: desactiva el campo.

### Diferencias entre Etiquetas

* `<b>`: negrita visual, no semántica. No se recomienda.
* `<strong>`: negrita con significado (énfasis importante).
* `<i>`: cursiva visual.
* `<em>`: cursiva semántica (énfasis leve).

### HTML Semántico

* Mejora la accesibilidad y SEO.
* Ejemplos: `<header>`, `<nav>`, `<main>`, `<article>`, `<section>`, `<aside>`, `<footer>`.
* Usar `<div>` y `<span>` solo cuando no hay una etiqueta semántica más apropiada.

### Formularios

```html
<form action="/enviar" method="post">
  <label for="nombre">Nombre:</label>
  <input type="text" id="nombre" name="nombre" required>
  <input type="submit" value="Enviar">
</form>
```

### Tablas

```html
<table>
  <thead>
    <tr><th>Nombre</th><th>Edad</th></tr>
  </thead>
  <tbody>
    <tr><td>Laura</td><td>25</td></tr>
  </tbody>
</table>
```

### Listas

* Ordenada: `<ol>`
* Desordenada: `<ul>`
* Elemento de lista: `<li>`

### Multimedia

* `<img src="" alt="">`
* `<audio controls>` y `<video controls>`
* `<iframe>`: incrustar contenido externo

### Entidades HTML

* `&nbsp;`: espacio en blanco
* `&copy;`: símbolo de copyright
* `&lt;` y `&gt;`: menor y mayor que

---

## CSS

### Formas de Incluir CSS

* En línea: `style="color:red;"`
* Interno: dentro de `<style>` en el head.
* Externo: archivo `.css` enlazado con `<link>`

### Cascada y Especificidad

* Orden de prioridad: estilos en línea > `id` > `class` > etiqueta
* El último estilo declarado tiene más peso si tienen la misma especificidad

### Selectores Comunes

* `*`: universal
* `div`, `p`: etiqueta
* `.clase`: por clase
* `#id`: por id
* `div p`: descendiente
* `div > p`: hijo directo
* `[type="text"]`: por atributo

### Propiedades Comunes

* `color`, `background-color`
* `font-size`, `font-family`, `font-weight`, `text-align`
* `margin`, `padding`, `border`, `width`, `height`
* `display`, `position`, `z-index`, `overflow`

### Colores

* Hex: `#ff0000`
* RGB: `rgb(255, 0, 0)`
* RGBA: `rgba(255, 0, 0, 0.5)`
* HSL: `hsl(0, 100%, 50%)`

### Unidades

* Absolutas: `px`, `cm`, `in`
* Relativas: `%`, `em`, `rem`, `vw`, `vh`

### Pseudoclases

* `:hover`: cuando se pasa el cursor
* `:focus`: cuando un elemento está enfocado
* `:active`: mientras se hace clic
* `:nth-child(2)`: segundo hijo

### Box Model

* `content` + `padding` + `border` + `margin`
* Usa `box-sizing: border-box` para controlar mejor el ancho total

### Flexbox

```css
.container {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
```

* Ejes: principal (horizontal por defecto) y cruzado (vertical)
* `justify-content`: alinea en el eje principal
* `align-items`: eje cruzado
* `flex-wrap`: permite que los elementos se ajusten en varias líneas

### Grid

```css
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1rem;
}
```

* Diseño en dos dimensiones (columnas y filas)
* `grid-template-rows`, `grid-column`, `grid-row`

### Media Queries

```css
@media (max-width: 768px) {
  body {
    background-color: lightgray;
  }
}
```

* Permite estilos adaptables a diferentes tamaños de pantalla

### Transiciones y Animaciones

```css
button {
  transition: background-color 0.3s ease;
}

@keyframes mover {
  0% { transform: translateX(0); }
  100% { transform: translateX(100px); }
}

.elemento {
  animation: mover 1s infinite alternate;
}
```

---

## Buenas Prácticas

* Usa HTML semántico
* Evita repetir estilos, usa clases reutilizables
* Usa `rem` y `em` para mantener la escalabilidad
* Agrupa estilos similares
* Organiza tu CSS (reset, layout, componentes)

---

## Recursos Recomendados

* [MDN Web Docs (Mozilla)](https://developer.mozilla.org/es/)
* [W3Schools](https://www.w3schools.com/)
* [HTML Validator](https://validator.w3.org/)
* [CSS Validator](https://jigsaw.w3.org/css-validator/)
* [Flexbox Froggy](https://flexboxfroggy.com/)
* [CSS Grid Garden](https://cssgridgarden.com/)

---

Este archivo contiene todo lo esencial sobre HTML y CSS, incluyendo estructura, atributos, formularios, multimedia, estilos, modelos de diseño y adaptabilidad. Ideal como referencia rápida o guía de repaso.
