# Guía Completa de Estudio de JavaScript

Esta guía está pensada para estudiantes que desean aprender **JavaScript** desde lo más básico hasta conceptos más avanzados como **Web Components** y **LocalStorage**.  
Cada tema está explicado con ejemplos de código comentados.

---

## 1. Introducción a JavaScript
JavaScript es un lenguaje de programación que se ejecuta principalmente en el navegador para dotar a las páginas web de interactividad.

- **Dónde se usa**:  
  - En el navegador (frontend).  
  - En servidores con Node.js (backend).  

Ejemplo:
```html
<!DOCTYPE html>
<html>
  <body>
    <h1 id="titulo">Hola</h1>
    <script>
      // Seleccionamos un elemento y lo cambiamos dinámicamente
      document.getElementById("titulo").innerText = "Hola desde JavaScript";
    </script>
  </body>
</html>

2. Variables y Constantes

Se usan para almacenar datos.

var (antiguo, no recomendado).

let (variable que puede cambiar).

const (constante que no cambia).

let nombre = "Laura"; // puede cambiar
const edad = 20;      // no cambia

console.log(nombre, edad);

3. Tipos de Datos

Principales tipos en JavaScript:

Primitivos: string, number, boolean, null, undefined, symbol, bigint.

Objetos: arrays, funciones, objetos literales.

let texto = "Hola";
let numero = 10;
let esActivo = true;
let lista = [1, 2, 3];
let persona = { nombre: "Ana", edad: 25 };

console.log(persona.nombre); // Acceder a propiedad

4. Operadores
Aritméticos
let a = 5, b = 2;
console.log(a + b); // 7
console.log(a * b); // 10

Dijiste:
del 2 para abajo no me lo estas dando en md
ChatGPT dijo:
Pensado durante 59s
# Guía Completa de Estudio de JavaScript (.md)

> **Objetivo:** aprender JavaScript desde cero hasta temas avanzados (incluye **Web Components** y **LocalStorage**) con explicaciones claras y ejemplos de código comentados, listos para copiar/pegar.

---

## 1) Introducción rápida a JavaScript
- **Qué es:** Lenguaje de programación interpretado, orientado a objetos y multiparadigma, principal en la web.
- **Dónde corre:** Navegador (frontend) y Node.js (backend).
- **Cómo se inserta en HTML:**
```html
<!DOCTYPE html>
<html lang="es">
  <head>
    <!-- "defer" asegura que el script cargue sin bloquear el render -->
    <script src="./app.js" defer></script>
  </head>
  <body>
    <h1 id="t">Hola</h1>
    <script>
      // Alternativa: script inline (menos recomendado para proyectos grandes).
      document.getElementById("t").textContent = "Hola desde JS";
    </script>
  </body>
</html>

2) Variables y Constantes

let: variable reasignable (ámbito de bloque).

const: constante (no reasignable; el objeto puede mutar).

var: obsoleto en la práctica (scope de función + hoisting confuso).

let contador = 0;          // puede cambiar
const APP_NAME = "Demo";   // no se puede reasignar

contador += 1;
console.log(APP_NAME, contador);

// Mutación de objeto referenciado por const (válido)
const user = { name: "Ana" };
user.name = "Ana María";   // ok
// user = {}                // ❌ no permitido (reasignación)

3) Tipos de Datos y Conversión

Primitivos: string, number, boolean, null, undefined, symbol, bigint.

Estructurados: object, array, function, date, etc.

const texto = "42";
const numero = Number(texto);       // 42
const esVerdad = Boolean("hola");   // true (string no vacío)

console.log(typeof numero);         // "number"
console.log(Number.isNaN(Number("x"))); // true

4) Operadores

Aritméticos: + - * / % **

Comparación: === (estricta), !==, <, <=, etc.

Lógicos: && (AND), || (OR), ! (NOT), ?? (nullish coalescing).

Coalescencia / Cortocircuito:

const a = 0 || 10;         // 10 (0 es falsy)
const b = 0 ?? 10;         // 0  (?? solo salta null/undefined)
console.log(5 === "5");    // false (estricta)
console.log(5 == "5");     // true  (coerción implícita, evita usarlo)

5) Control de Flujo
const n = 7;

if (n % 2 === 0) {
  console.log("par");
} else if (n % 3 === 0) {
  console.log("múltiplo de 3");
} else {
  console.log("otro");
}

for (let i = 0; i < 3; i++) console.log(i);

let j = 3;
while (j > 0) {
  console.log(j);
  j--;
}

switch (new Date().getDay()) {
  case 0: console.log("Domingo"); break;
  case 6: console.log("Sábado"); break;
  default: console.log("Día laboral");
}

6) Funciones

Declaración: hoisting (se pueden usar antes de declarar).

Expresión: asignadas a variables.

Arrow functions: sintaxis concisa, no tienen this propio.

// Declaración
function sumar(a, b = 0) { // b tiene valor por defecto
  return a + b;
}

// Expresión
const restar = function (a, b) {
  return a - b;
};

// Arrow
const multiplicar = (a, b) => a * b;

// Parámetros rest
const promedio = (...nums) => nums.reduce((acc, n) => acc + n, 0) / nums.length;

console.log(sumar(2, 3), restar(5, 1), multiplicar(2, 4), promedio(2, 4, 6));

7) Scope, Hoisting y Closures

Scope de bloque: let/const viven dentro de {}.

Hoisting: declaraciones de funciones y var “suben” (ojo con usar var).

Closure: función interna recuerda su entorno léxico.

function contadorFactory() {
  let count = 0; // variable privada gracias al closure
  return () => ++count;
}
const next = contadorFactory();
console.log(next()); // 1
console.log(next()); // 2

8) this, call, apply, bind
const persona = {
  nombre: "Laura",
  saludar() { console.log(`Hola, soy ${this.nombre}`); }
};

persona.saludar(); // "Laura"

const saludar = persona.saludar;
saludar(); // "Hola, soy undefined" (this perdido)

const saludarBien = persona.saludar.bind(persona);
saludarBien(); // "Laura"

// call/apply para fijar this y pasar argumentos
function presentar(role) { console.log(`${this.nombre} - ${role}`); }
presentar.call({ nombre: "Ana" }, "Dev");
presentar.apply({ nombre: "Mario" }, ["QA"]);

9) Objetos, Prototipos y Clases
// Objeto literal
const user = { id: 1, name: "Ada" };

// Prototipos
function Persona(nombre) {
  this.nombre = nombre;
}
Persona.prototype.saluda = function () {
  console.log(`Hola, soy ${this.nombre}`);
};
new Persona("Alan").saluda();

// Clases (azúcar sintáctica)
class Animal {
  constructor(nombre) { this.nombre = nombre; }
  hablar() { console.log(`${this.nombre} hace un sonido`); }
}
class Perro extends Animal {
  hablar() { console.log(`${this.nombre} ladra`); }
}
new Perro("Toby").hablar();

10) Arrays y Métodos Esenciales
const nums = [1, 2, 3, 4, 5];

const dobles = nums.map(n => n * 2);
const pares  = nums.filter(n => n % 2 === 0);
const suma   = nums.reduce((acc, n) => acc + n, 0);
const hayMayores = nums.some(n => n > 3);
const todosPos   = nums.every(n => n > 0);
const primero    = nums.find(n => n > 3);

console.log({ dobles, pares, suma, hayMayores, todosPos, primero });

11) Strings, Numbers, Dates, Intl
const s = "Hola JS";
console.log(s.includes("JS"), s.toUpperCase(), s.slice(0, 4)); // true "HOLA JS" "Hola"

const precio = 1234.5;
console.log(precio.toFixed(2)); // "1234.50"

const d = new Date("2025-09-10T09:00:00");
console.log(new Intl.DateTimeFormat("es-CO", { dateStyle: "full" }).format(d));
console.log(new Intl.NumberFormat("es-CO", { style: "currency", currency: "COP" }).format(19990));

12) Módulos ES (import/export)
// math.js
export const PI = 3.1416;
export function areaCirculo(r) { return PI * r * r; }

// main.js
import { PI, areaCirculo } from "./math.js";
console.log(PI, areaCirculo(2));

<script type="module" src="./main.js"></script>

13) DOM (Seleccionar, Crear, Modificar)
<div id="app"></div>
<script>
  const $app = document.getElementById("app");

  // Crear
  const p = document.createElement("p");
  p.textContent = "Hola DOM";

  // Insertar
  $app.appendChild(p);

  // Atributos/Clases
  p.setAttribute("data-id", "1");
  p.classList.add("destacado");

  // Remover
  // p.remove();
</script>

14) Eventos y Delegación
<ul id="lista">
  <li data-id="1">Item 1</li>
  <li data-id="2">Item 2</li>
</ul>
<script>
  // Delegación: escuchar en el contenedor
  document.getElementById("lista").addEventListener("click", (e) => {
    if (e.target.matches("li")) {
      console.log("Click en li id:", e.target.dataset.id);
    }
  });
</script>

15) Formularios y Validación
<form id="f">
  <input name="email" type="email" required />
  <input name="edad" type="number" min="1" max="120" required />
  <button>Enviar</button>
</form>
<script>
  const $f = document.getElementById("f");
  $f.addEventListener("submit", (e) => {
    e.preventDefault();
    if (!$f.reportValidity()) return; // validación nativa
    const data = Object.fromEntries(new FormData($f));
    console.log("Datos:", data);
  });
</script>

16) JSON y Fetch API (Promesas)
// GET
fetch("https://jsonplaceholder.typicode.com/todos/1")
  .then(res => {
    if (!res.ok) throw new Error("HTTP " + res.status);
    return res.json();
  })
  .then(data => console.log("Todo:", data))
  .catch(err => console.error(err))
  .finally(() => console.log("Listo"));

// POST
fetch("https://jsonplaceholder.typicode.com/todos", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ title: "Aprender JS", completed: false })
})
.then(r => r.json())
.then(console.log);

17) async/await y Manejo de Errores
async function cargarTodo(id) {
  try {
    const res = await fetch(`https://jsonplaceholder.typicode.com/todos/${id}`);
    if (!res.ok) throw new Error("HTTP " + res.status);
    const data = await res.json();
    return data;
  } catch (e) {
    console.error("Error:", e.message);
    return null;
  }
}

(async () => {
  const todo = await cargarTodo(1);
  console.log(todo);
})();

18) LocalStorage y SessionStorage

localStorage: persiste entre sesiones.

sessionStorage: se borra al cerrar pestaña.

Guarda strings; para objetos usa JSON.stringify()/JSON.parse().

// Guardar
localStorage.setItem("tema", "oscuro");
localStorage.setItem("user", JSON.stringify({ id: 1, nombre: "Laura" }));

// Leer
const tema = localStorage.getItem("tema"); // "oscuro"
const userLS = JSON.parse(localStorage.getItem("user") || "{}");

// Borrar
localStorage.removeItem("tema");
// localStorage.clear(); // ⚠️ borra todo


Patrón: estado UI persistente

const $check = document.querySelector("#toggle-tema"); // <input type="checkbox" id="toggle-tema">
$check.checked = localStorage.getItem("tema") === "oscuro";

function aplicarTema() {
  document.documentElement.dataset.tema = $check.checked ? "oscuro" : "claro";
  localStorage.setItem("tema", $check.checked ? "oscuro" : "claro");
}
$check.addEventListener("change", aplicarTema);
aplicarTema();

19) Manejando Errores con try/catch y throw
function dividir(a, b) {
  if (b === 0) throw new Error("División por cero");
  return a / b;
}

try {
  console.log(dividir(10, 2));
  console.log(dividir(10, 0));
} catch (e) {
  console.error("Ocurrió un error:", e.message);
}

20) Expresiones Regulares (básico)
const correo = "ana@example.com";
const ok = /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(correo);
console.log(ok); // true

21) Buenas Prácticas Esenciales

Usa const por defecto y let si necesitas reasignar.

Evita var.

Prefiere === sobre ==.

Funciones pequeñas y puras cuando sea posible.

Nombra variables de forma clara.

Maneja errores (promesas y async/await).

Evita mutaciones innecesarias (uso de map, filter, reduce).

Separa lógica de UI (módulos).

22) Introducción a Web Components

Componentes nativos del navegador con:

Custom Elements: etiquetas personalizadas.

Shadow DOM: encapsulación de HTML/CSS/JS.

Templates & Slots: plantillas y huecos de contenido.

22.1) Custom Element + Shadow DOM
<!-- index.html -->
<user-card name="Ada Lovelace" role="Matemática"></user-card>
<script type="module" src="./user-card.js"></script>

// user-card.js
const tpl = document.createElement("template");
tpl.innerHTML = `
  <style>
    :host {
      display: block; font-family: system-ui; border: 1px solid #ddd;
      padding: 12px; border-radius: 12px;
    }
    h3 { margin: 0 0 8px; font-size: 1.1rem; }
    .role { opacity: .7; }
    button { border: 0; padding: 8px 12px; border-radius: 8px; cursor: pointer; }
  </style>
  <h3></h3>
  <p class="role"></p>
  <slot></slot>
  <button id="btn">Saludar</button>
`;

class UserCard extends HTMLElement {
  static get observedAttributes() { return ["name", "role"]; }

  constructor() {
    super();
    // Encapsulación
    this.attachShadow({ mode: "open" }).appendChild(tpl.content.cloneNode(true));
    this.$h3  = this.shadowRoot.querySelector("h3");
    this.$rol = this.shadowRoot.querySelector(".role");
    this.$btn = this.shadowRoot.querySelector("#btn");
  }

  connectedCallback() {
    // Lifecycle hook: el elemento está en el DOM
    this.#render();
    this.$btn.addEventListener("click", this.#onClick);
  }

  disconnectedCallback() {
    // Limpieza
    this.$btn.removeEventListener("click", this.#onClick);
  }

  attributeChangedCallback() { this.#render(); }

  #onClick = () => {
    // Emite un evento personalizado hacia afuera del Shadow DOM
    this.dispatchEvent(new CustomEvent("saludo", {
      bubbles: true, composed: true, detail: { name: this.getAttribute("name") }
    }));
  }

  #render() {
    this.$h3.textContent  = this.getAttribute("name") || "Sin nombre";
    this.$rol.textContent = this.getAttribute("role") || "Sin rol";
  }
}

customElements.define("user-card", UserCard);


Uso con escucha de evento personalizado:

<user-card id="c1" name="Ada Lovelace" role="Matemática">
  <small>Contenido en <slot></slot> (queda fuera del Shadow DOM).</small>
</user-card>
<script type="module">
  document.getElementById("c1").addEventListener("saludo", (e) => {
    alert("Hola " + e.detail.name);
  });
</script>

22.2) Slots y Atributos Reactivos

observedAttributes permite reaccionar a cambios en atributos.

slot inserta contenido de quien usa el componente.

Se recomienda exponer props via atributos y eventos (unidireccional).

23) Web Components + LocalStorage (pequeño ejemplo)

Un componente <theme-toggle> que persiste el tema.

<theme-toggle></theme-toggle>
<script type="module" src="./theme-toggle.js"></script>
<script>
  // Aplicar el tema guardado al cargar (fuera del componente)
  const tema = localStorage.getItem("tema") || "claro";
  document.documentElement.dataset.tema = tema;
</script>

// theme-toggle.js
const TPL = document.createElement("template");
TPL.innerHTML = `
  <style>
    :host { display:inline-flex; gap:8px; align-items:center; font-family:system-ui }
    button { padding:6px 10px; border:1px solid #ccc; border-radius:8px; cursor:pointer }
  </style>
  <span>Tema:</span>
  <button id="btn"></button>
`;

class ThemeToggle extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: "open" }).appendChild(TPL.content.cloneNode(true));
    this.$btn = this.shadowRoot.querySelector("#btn");
  }
  connectedCallback() {
    this.#sync();
    this.$btn.addEventListener("click", this.#toggle);
  }
  disconnectedCallback() {
    this.$btn.removeEventListener("click", this.#toggle);
  }
  #sync() {
    const tema = localStorage.getItem("tema") || "claro";
    this.$btn.textContent = tema === "claro" ? "Cambiar a oscuro" : "Cambiar a claro";
  }
  #toggle = () => {
    const actual = localStorage.getItem("tema") || "claro";
    const nuevo = actual === "claro" ? "oscuro" : "claro";
    localStorage.setItem("tema", nuevo);
    document.documentElement.dataset.tema = nuevo; // aplica al documento
    this.#sync();
    this.dispatchEvent(new CustomEvent("themechange", { bubbles: true, composed: true, detail: { tema: nuevo }}));
  }
}
customElements.define("theme-toggle", ThemeToggle);

24) Patrón: Estado + Render (sin framework)
<div id="app"></div>
<script>
  const state = { count: Number(localStorage.getItem("count") || 0) };
  const $app = document.getElementById("app");

  function render() {
    $app.innerHTML = `
      <p>Contador: <strong>${state.count}</strong></p>
      <button id="inc">+1</button>
      <button id="reset">Reset</button>
    `;
    document.getElementById("inc").onclick = () => {
      state.count++; localStorage.setItem("count", state.count); render();
    };
    document.getElementById("reset").onclick = () => {
      state.count = 0; localStorage.setItem("count", 0); render();
    };
  }
  render();
</script>

25) Performance (tips básicos)

Minimiza accesos repetidos al DOM (usa fragmentos / actualizaciones agrupadas).

Usa delegación de eventos.

Evita trabajo pesado en el hilo principal (Web Workers si aplica).

Memoriza resultados caros si se repiten (caché en memoria/localStorage).

26) Testing manual simple (sin librerías)
function assert(desc, cond) {
  if (!cond) throw new Error("❌ " + desc);
  console.log("✅ " + desc);
}
const suma = (a,b)=>a+b;

assert("2 + 2 = 4", suma(2,2) === 4);
assert("0 + 0 = 0", suma(0,0) === 0);

27) Errores comunes a evitar

Usar == en vez de ===.

Olvidar await en funciones async.

No validar res.ok con fetch.

Manipular el DOM en bucles sin necesidad.

Mezclar responsabilidades (lógica + DOM + almacenamiento sin capas).

28) Mini-Proyecto Integrador: <todo-app> (Web Component + LocalStorage)

Características:

Añadir tareas, marcarlas como completadas, filtrarlas.

Persistencia en localStorage.

<todo-app></todo-app>
<script type="module" src="./todo-app.js"></script>

// todo-app.js
const T = document.createElement("template");
T.innerHTML = `
  <style>
    :host { display:block; max-width:520px; margin:auto; font-family:system-ui }
    h2 { margin: 0 0 12px }
    form { display:flex; gap:8px; margin-bottom:12px }
    input[type=text]{ flex:1; padding:8px; border:1px solid #ccc; border-radius:8px }
    button{ padding:8px 12px; border:1px solid #ccc; border-radius:8px; cursor:pointer }
    ul{ list-style:none; padding:0; margin:0; display:grid; gap:8px }
    li{ display:flex; align-items:center; gap:8px; border:1px solid #eee; padding:8px; border-radius:8px }
    li.done{ opacity:.6; text-decoration: line-through }
    .toolbar{ display:flex; gap:8px; margin:10px 0 }
    .empty{ opacity:.7; text-align:center; padding:12px }
  </style>
  <h2>📝 Mis Tareas</h2>
  <form id="f">
    <input id="txt" type="text" placeholder="Nueva tarea..." required />
    <button>Añadir</button>
  </form>

  <div class="toolbar">
    <button data-filter="all">Todas</button>
    <button data-filter="open">Pendientes</button>
    <button data-filter="done">Hechas</button>
    <button id="clear">Borrar hechas</button>
  </div>

  <ul id="list"></ul>
  <p class="empty" hidden>No hay tareas</p>
`;

class TodoApp extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: "open" }).appendChild(T.content.cloneNode(true));
    this.$f     = this.shadowRoot.getElementById("f");
    this.$txt   = this.shadowRoot.getElementById("txt");
    this.$list  = this.shadowRoot.getElementById("list");
    this.$empty = this.shadowRoot.querySelector(".empty");
    this.$clear = this.shadowRoot.getElementById("clear");
    this.filter = "all";
    this.state  = this.#load();
  }

  connectedCallback() {
    this.$f.addEventListener("submit", this.#onAdd);
    this.$list.addEventListener("click", this.#onListClick);
    this.shadowRoot.querySelectorAll("[data-filter]").forEach(b => {
      b.addEventListener("click", () => { this.filter = b.dataset.filter; this.#render(); });
    });
    this.$clear.addEventListener("click", this.#clearDone);
    this.#render();
  }
  disconnectedCallback() {
    this.$f.removeEventListener("submit", this.#onAdd);
    this.$list.removeEventListener("click", this.#onListClick);
    this.$clear.removeEventListener("click", this.#clearDone);
  }

  #onAdd = (e) => {
    e.preventDefault();
    const text = this.$txt.value.trim();
    if (!text) return;
    this.state.push({ id: crypto.randomUUID(), text, done: false });
    this.$txt.value = "";
    this.#save(); this.#render();
  }

  #onListClick = (e) => {
    const li = e.target.closest("li");
    if (!li) return;
    const id = li.dataset.id;

    if (e.target.matches("[data-action=toggle]")) {
      const item = this.state.find(t => t.id === id);
      item.done = !item.done;
      this.#save(); this.#render();
    }
    if (e.target.matches("[data-action=del]")) {
      this.state = this.state.filter(t => t.id !== id);
      this.#save(); this.#render();
    }
  }

  #clearDone = () => {
    this.state = this.state.filter(t => !t.done);
    this.#save(); this.#render();
  }

  #render() {
    const items = this.state.filter(t =>
      this.filter === "all"  ? true :
      this.filter === "open" ? !t.done : t.done
    );

    this.$list.innerHTML = items.map(t => `
      <li data-id="${t.id}" class="${t.done ? "done" : ""}">
        <button data-action="toggle">${t.done ? "✅" : "⬜"}</button>
        <span>${t.text}</span>
        <span style="flex:1"></span>
        <button data-action="del">🗑️</button>
      </li>
    `).join("");

    this.$empty.hidden = this.state.length > 0;
  }

  #load() {
    try { return JSON.parse(localStorage.getItem("todos") || "[]"); }
    catch { return []; }
  }
  #save() {
    localStorage.setItem("todos", JSON.stringify(this.state));
  }
}

customElements.define("todo-app", TodoApp);

29) Referencias clave (qué buscar luego)

MDN Web Docs: JavaScript, DOM, Web Components, Storage.

WHATWG/HTML Standard: Custom Elements, Shadow DOM, Templates.

CanIUse: compatibilidad de APIs modernas.

30) Ruta de Estudio Recomendada

Sintaxis base: variables, tipos, operadores, control de flujo.

Funciones, scope, closures, this.

Arrays/Objetos avanzados, métodos funcionales.

DOM y eventos, formularios.

Fetch/Promesas/async/await.

Almacenamiento (LocalStorage/SessionStorage).

Módulos ES y organización del código.

Web Components (Custom Elements + Shadow DOM + Slots).

Mini-proyectos integradores con persistencia y componentes.