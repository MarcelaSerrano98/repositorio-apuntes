
# 🎨 Animaciones con CSS – Apuntes Completos

## 📌 ¿Qué es una animación en CSS?

Las animaciones en CSS permiten cambiar de forma gradual los estilos de un elemento. Se definen con reglas `@keyframes` y se aplican usando la propiedad `animation`.

---

## 🧱 Propiedades Básicas de Animación

### ✅ `@keyframes`
Define los pasos de la animación:
```css
@keyframes nombre-animacion {
  0% { /* estado inicial */ }
  100% { /* estado final */ }
}
```

### ✅ `animation`
Es un *shorthand* que agrupa todas las propiedades de la animación:
```css
selector {
  animation: nombre 2s ease-in-out 0s infinite alternate both;
}
```

### Propiedades individuales:

| Propiedad                  | Descripción                                                                 |
|---------------------------|-----------------------------------------------------------------------------|
| `animation-name`          | Nombre de la animación (`@keyframes`)                                      |
| `animation-duration`      | Duración total (ej. `2s`, `500ms`)                                          |
| `animation-timing-function` | Curva de velocidad (ej. `ease`, `linear`, `ease-in`, etc.)              |
| `animation-delay`         | Tiempo que espera antes de empezar                                          |
| `animation-iteration-count` | Cuántas veces se repite (`1`, `infinite`, `3`, etc.)                     |
| `animation-direction`     | Dirección de la animación (`normal`, `reverse`, `alternate`, `alternate-reverse`) |
| `animation-fill-mode`     | Define el estilo del elemento antes y después de la animación (`none`, `forwards`, `backwards`, `both`) |
| `animation-play-state`    | Controla si la animación está corriendo o pausada (`running`, `paused`)    |

---

## ⏳ Funciones de Tiempo (`animation-timing-function`)

| Función       | Descripción                            |
|---------------|----------------------------------------|
| `linear`      | Velocidad constante                    |
| `ease`        | Suave al inicio y final                |
| `ease-in`     | Comienza lentamente                    |
| `ease-out`    | Termina lentamente                     |
| `ease-in-out` | Lento al inicio y al final             |
| `cubic-bezier`| Curva personalizada, ejemplo: `cubic-bezier(0.1, 0.7, 1.0, 0.1)` |

---

## 💡 Ejemplos Prácticos

### 🎯 Fade In (Aparecer)
```css
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

.elemento {
  animation: fadeIn 1s ease-out forwards;
}
```

### ⬇️ Bounce (Rebote)
```css
@keyframes bounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-20px); }
}

.rebote {
  animation: bounce 0.8s ease infinite;
}
```

### 🌈 Fondo Degradado Animado
```css
@keyframes gradientMove {
  0% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
  100% { background-position: 0% 50%; }
}

.fondo-degradado {
  background: linear-gradient(270deg, #ff6ec4, #7873f5);
  background-size: 400% 400%;
  animation: gradientMove 8s ease infinite;
}
```

### ⌨️ Efecto Máquina de Escribir
```css
@keyframes typing {
  from { width: 0; }
  to { width: 100%; }
}

@keyframes blink {
  50% { border-color: transparent; }
}

.maquina {
  font-family: monospace;
  white-space: nowrap;
  overflow: hidden;
  border-right: 2px solid;
  width: 0;
  animation: typing 3s steps(30, end), blink 0.75s step-end infinite;
}
```

---

## 🧠 Buenas Prácticas

- Usa `transform` y `opacity` en vez de `width` o `height` para mejor rendimiento.
- Usa animaciones sutiles para no distraer.
- Usa `animation-fill-mode: forwards` si quieres que el elemento se quede en su estado final.

---

## 🧪 Ejemplo Completo

```html
<div class="boton-animado">Haz clic</div>

<style>
@keyframes pulse {
  0% { transform: scale(1); }
  50% { transform: scale(1.05); }
  100% { transform: scale(1); }
}

.boton-animado {
  background: #6c5ce7;
  color: white;
  padding: 15px 30px;
  border-radius: 25px;
  text-align: center;
  display: inline-block;
  animation: pulse 1.5s ease-in-out infinite;
  cursor: pointer;
}
</style>
```

---

## 📚 Recursos Recomendados

- [Animista](https://animista.net) – Galería de animaciones CSS.
- [Easings.net](https://easings.net) – Visualización de funciones `ease`.

---

## 🧪 Desafío Avanzado: Animaciones Combinadas

```css
@keyframes rotate {
  from { transform: rotate(0); }
  to { transform: rotate(360deg); }
}

@keyframes scale {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.1); }
}

.dual-anim {
  animation: rotate 4s linear infinite, scale 2s ease-in-out infinite;
}
```

---

## 🎬 Conclusión

CSS permite crear animaciones potentes, visuales y ligeras. Entender `@keyframes` y las propiedades de `animation` te da control total sobre efectos dinámicos en tus sitios web. ¡Experimenta y diviértete creando!

---
