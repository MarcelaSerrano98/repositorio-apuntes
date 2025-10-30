Guía de Estudio – Proyecto MiniStore (FakeStore API)
1. Arquitectura del proyecto

Frontend: HTML, CSS, JavaScript con ES Modules.

API externa: FakeStore API (https://fakestoreapi.com/products).

Archivos principales:

app.js → orquesta la app en Home.

api.js → funciones para consumir la API.

state.js → guarda productos, vista, página, pageSize.

products.js → renderizado de productos.

pager.js → paginación (renderPage, botones).

filters.js → filtros y ordenamientos.

events.js → delegación de eventos (carrito, ver detalles).

details.js → modal con info del producto.

toast.js → notificaciones flotantes.

favorites.js → página de favoritos.

2. Flujo general

app.js escucha DOMContentLoaded.

Llama a fetchProducts() → carga desde la API.

Guarda en state.products y copia a state.view.

renderPage() muestra la página inicial.

Eventos:

registerPagerEvents() → botones Back/Next.

registerFilters() → ordenamiento/filtrado.

registerEvents() → clicks en carrito/detalles.

3. Estado (state.js)

products: lista original de la API.

view: lista filtrada/ordenada.

page: página actual.

pageSize: productos por página.

👉 Importante: nunca filtrar sobre view directamente, siempre partir de products.

4. Delegación de eventos

Por qué se usa:

Los productos se regeneran con innerHTML.

Si agregas listeners individuales, se pierden.

Solución: un solo addEventListener en #products y usar closest.

Ejemplo:

productsEl.addEventListener('click', (e) => {
  const add = e.target.closest('.add-to-cart');
  if (add) { /* lógica de carrito */ }

  const detail = e.target.closest('.see-details');
  if (detail) { showDetail(detail.dataset.id, state.view); }
});

5. Paginación

Lógica:

const totalPages = Math.ceil(state.view.length / state.pageSize);
const start = (state.page - 1) * state.pageSize;
const end = start + state.pageSize;
const pageItems = state.view.slice(start, end);


👉 Importante:

Controlar límites (state.page >= 1 && state.page <= totalPages).

Resetear a página 1 tras un filtro/orden.

6. Ordenamiento
Precio
state.view.sort((a, b) => a.price - b.price); // asc
state.view.sort((a, b) => b.price - a.price); // desc

Título
a.title.localeCompare(b.title); // A–Z
b.title.localeCompare(a.title); // Z–A

Categoría
a.category.localeCompare(b.category); // A–Z
b.category.localeCompare(a.category); // Z–A

7. Filtrado

Categoría:

if (cat === 'all') {
  state.view = state.products.slice();
} else {
  state.view = state.products.filter(p => p.category === cat);
}
state.page = 1;
renderPage();


Búsqueda:

const q = search.value.toLowerCase();
state.view = state.products.filter(p => p.title.toLowerCase().includes(q));

8. Modal de detalles (details.js)

Pasos:

Botón con data-id.

Buscar producto con .find().

Crear overlay .modal-detail.

Insertar HTML con info del producto.

Agregar cierres:

Botón ✖.

Click en fondo (if(ev.target===overlay)).

Tecla Esc.

Antes de abrir: eliminar modal previo.

Ejemplo de búsqueda:

const p = list.find(item => Number(item.id) === Number(id));

9. Toasts (toast.js)

Pasos:

Crear div .toast en #toast-root.

Mostrar mensaje.

Autocierre con setTimeout.

Cierre manual con click.

Ejemplo:

const el = document.createElement('div');
el.className = 'toast toast--success';
el.textContent = message;
root.appendChild(el);

const t = setTimeout(() => el.remove(), 2000);
el.addEventListener('click', () => { clearTimeout(t); el.remove(); });

10. Favoritos

Lógica:

Guardar ids en localStorage:

localStorage.setItem('favorites', JSON.stringify(ids));


En favorites.html, traer productos y filtrar con esos ids.

Llamar registerEvents({ list: favorites }).

11. Carrito

Lógica:

Guardar { id, qty } en localStorage.

Si existe el id, sumar cantidad; si no, crear nuevo.

Leer en cart.html y pintar.

12. Cosas que podrían pedir implementar

Búsqueda en tiempo real con input.

Ordenar por rating (b.rating.rate - a.rating.rate).

Mostrar solo top 5 productos (slice).

Agregar favoritos/carrito en localStorage.

Ofertas: filtrar por precio < X o categoría específica.

Login simulado: guardar user en localStorage.

13. Preguntas típicas de examen

Explica la diferencia entre state.products y state.view.

¿Por qué usar closest en delegación de eventos?

¿Cómo funciona .sort() con números y .localeCompare() con strings?

¿Cómo filtrar productos por categoría desde la API?

¿Cómo haces la paginación con .slice()?

¿Cómo cierras un modal con tecla Esc?

¿Cómo guardar datos en localStorage?

¿Qué pasa si registras el mismo listener varias veces?


Kit de soluciones – MiniStore (FakeStore API)
1) Cargar productos desde la API

Objetivo: Traer datos y pintarlos.

Dónde: core/api.js + app.js

// core/api.js
export async function fetchProducts() {
  const res = await fetch('https://fakestoreapi.com/products');
  if (!res.ok) throw new Error('API error');
  return await res.json();
}

// app.js (fragmento)
import { fetchProducts } from './core/api.js';
import { state } from './core/state.js';
import { renderPage } from './ui/pager.js';

document.addEventListener('DOMContentLoaded', init);

async function init() {
  try {
    const data = await fetchProducts();
    state.products = data;
    state.view = data.slice(); // base de render
    state.page = 1;
    renderPage();
  } catch (e) {
    console.error(e);
    // showError(...)
  }
}

2) Paginación (siguiente / anterior)

Objetivo: Mostrar N por página.

Dónde: ui/pager.js (+ usar en app.js)

// core/state.js (asegúrate de tener)
export const state = { products: [], view: [], page: 1, pageSize: 8 };

export function totalPages() {
  return Math.max(1, Math.ceil((state.view?.length || 0) / state.pageSize));
}
export function pageSlice() {
  const start = (state.page - 1) * state.pageSize;
  return state.view.slice(start, start + state.pageSize);
}

// ui/pager.js
import { state, totalPages, pageSlice } from '../core/state.js';
import { renderProducts } from './products.js';

export function renderPage() {
  const total = totalPages();
  state.page = Math.min(Math.max(state.page, 1), total);
  renderProducts(pageSlice());
  const info = document.getElementById('pager-info');
  if (info) info.textContent = `Página ${state.page} / ${total}`;
  const prev = document.querySelector('.pass .back');
  const next = document.querySelector('.pass .Next');
  if (prev) prev.disabled = state.page <= 1;
  if (next) next.disabled = state.page >= total;
}

export function registerPagerEvents() {
  const prev = document.querySelector('.pass .back');
  const next = document.querySelector('.pass .Next');
  prev?.addEventListener('click', () => { if (state.page > 1) { state.page--; renderPage(); }});
  next?.addEventListener('click', () => { if (state.page < totalPages()) { state.page++; renderPage(); }});
}

3) Ordenar (precio, nombre, categoría)

Objetivo: Sort asc/desc.

Dónde: ui/filters.js

// ui/filters.js
import { state } from '../core/state.js';
import { renderPage } from './pager.js';

export function registerSort() {
  const sortEl = document.getElementById('sort');
  if (!sortEl) return;

  sortEl.addEventListener('change', () => {
    const opt = sortEl.value;

    if (opt === 'price-asc')  state.view.sort((a,b)=> a.price - b.price);
    if (opt === 'price-desc') state.view.sort((a,b)=> b.price - a.price);
    if (opt === 'title-asc')  state.view.sort((a,b)=> a.title.localeCompare(b.title));
    if (opt === 'title-desc') state.view.sort((a,b)=> b.title.localeCompare(a.title));
    if (opt === 'category-asc')  state.view.sort((a,b)=> a.category.localeCompare(b.category));
    if (opt === 'category-desc') state.view.sort((a,b)=> b.category.localeCompare(a.category));

    state.page = 1;
    renderPage();
  });
}

4) Filtrar por categoría

Objetivo: Mostrar solo una categoría.

Dónde: ui/filters.js

export function registerCategoryFilter() {
  const filterEl = document.getElementById('filter');
  if (!filterEl) return;

  filterEl.addEventListener('change', () => {
    const cat = String(filterEl.value).trim().toLowerCase();
    if (cat === 'all') state.view = state.products.slice();
    else state.view = state.products.filter(p => String(p.category).trim().toLowerCase() === cat);
    state.page = 1;
    renderPage();
  });
}

5) Búsqueda por título

Objetivo: Filtrar al escribir.

Dónde: ui/search.js

// ui/search.js
import { state } from '../core/state.js';
import { renderPage } from './pager.js';

export function registerSearch() {
  const search = document.getElementById('search');
  if (!search) return;

  search.addEventListener('input', () => {
    const q = search.value.trim().toLowerCase();
    state.view = state.products.filter(p => p.title.toLowerCase().includes(q));
    state.page = 1;
    renderPage();
  });
}

6) Delegación de eventos (carrito + detalles)

Objetivo: Un solo listener que funciona aunque repintes.

Dónde: ui/events.js

// ui/events.js
import { showToast } from './toast.js';
import { showDetail } from './details.js';
import { state } from '../core/state.js';

let eventsBound = false;

export function registerEvents({ list } = {}) {
  if (eventsBound) return;
  eventsBound = true;

  const productsEl = document.getElementById('products');
  if (!productsEl) return;

  const source = (Array.isArray(list) && list.length) ? list : state.products;

  productsEl.addEventListener('click', (e) => {
    const add = e.target.closest('.add-to-cart');
    if (add) {
      showToast('Producto agregado al 🛒 con éxito ✔️', { type: 'success' });
      return;
    }

    const details = e.target.closest('.see-details');
    if (details) {
      const id = Number(details.dataset.id);
      showDetail(id, source);
      return;
    }
  });
}

7) Modal de “Ver detalles”

Objetivo: Abrir modal, cerrar con ✖, fondo y Esc.

Dónde: ui/details.js

// ui/details.js
export function showDetail(id, list = []) {
  const root = document.getElementById('modal');
  if (!root) return;

  // Evita duplicados
  const existing = root.querySelector('.modal-detail');
  if (existing) existing.remove();

  const p = list.find(x => Number(x.id) === Number(id));
  if (!p) return;

  const el = document.createElement('div');
  el.className = 'modal-detail';
  el.innerHTML = `
    <div class="modal-content" role="dialog" aria-modal="true">
      <button class="modal-close" aria-label="Cerrar">✖</button>
      <div class="modal-body">
        <div class="detail-left"><img src="${p.image}" alt="${p.title}" loading="lazy"></div>
        <div class="detail-right">
          <h3>${p.title}</h3>
          <p><strong>Categoría:</strong> ${p.category}</p>
          <p><strong>Precio:</strong> $ ${p.price} USD</p>
          <p>${p.description}</p>
        </div>
      </div>
    </div>
  `;
  root.appendChild(el);

  const btnClose = el.querySelector('.modal-close');
  const close = () => {
    el.removeEventListener('click', onBackdrop);
    document.removeEventListener('keydown', onEsc);
    el.remove();
  };
  const onBackdrop = (ev) => { if (ev.target === el) close(); };
  const onEsc = (ev) => { if (ev.key === 'Escape') close(); };

  btnClose.addEventListener('click', close);
  el.addEventListener('click', onBackdrop);
  document.addEventListener('keydown', onEsc);
}


HTML necesario (una vez):

<div id="modal"></div>

8) Toasts (confirmaciones)

Objetivo: Mostrar notificación.

Dónde: ui/toast.js

// ui/toast.js
export function showToast(message, { type = 'success', timeout = 2000 } = {}) {
  const root = document.getElementById('toast-root');
  if (!root) return;

  const el = document.createElement('div');
  el.className = `toast toast--${type}`;
  el.textContent = message;
  root.appendChild(el);

  const t = setTimeout(() => close(), timeout);
  el.addEventListener('click', close);

  function close() {
    clearTimeout(t);
    el.style.transition = 'opacity .15s ease';
    el.style.opacity = '0';
    setTimeout(() => el.remove(), 160);
  }
}


HTML necesario (una vez):

<div id="toast-root" aria-live="polite" aria-atomic="true"></div>

9) Favoritos con localStorage

Objetivo: Guardar ids, renderizar en favorites.html y abrir modal.

Dónde: core/favorites.js + favorites.js + events.js

// core/favorites.js
const KEY = 'favorites';
export function getFavorites() {
  return JSON.parse(localStorage.getItem(KEY) || '[]');
}
export function toggleFavorite(id) {
  const favs = getFavorites();
  const i = favs.indexOf(id);
  if (i >= 0) favs.splice(i, 1); else favs.push(id);
  localStorage.setItem(KEY, JSON.stringify(favs));
}

// ui/favorites.js
import { getFavorites } from '../core/favorites.js';
import { registerEvents } from './events.js';

async function fetchAll() {
  const res = await fetch('https://fakestoreapi.com/products');
  return res.json();
}
function renderFavoriteProducts($container, list) {
  $container.innerHTML = list.length
    ? list.map(p => `
      <article class="product-card">
        <img src="${p.image}" alt="${p.title}">
        <h3>${p.title}</h3>
        <p>$ ${p.price} USD</p>
        <div class="actions">
          <button class="secondary see-details" data-id="${p.id}">Ver detalles</button>
          <button class="add-to-cart" data-id="${p.id}">Agregar al carrito</button>
        </div>
      </article>`).join('')
    : `<p>No hay favoritos aún.</p>`;
}

document.addEventListener('DOMContentLoaded', async () => {
  const $products = document.getElementById('products');
  const all = await fetchAll();
  const favIds = getFavorites();
  const favorites = all.filter(p => favIds.includes(p.id));
  renderFavoriteProducts($products, favorites);
  registerEvents({ list: favorites }); // clave
});

10) Carrito simple (localStorage)

Objetivo: Agregar y persistir { id, qty }.

Dónde: core/cart.js + usar en events.js

// core/cart.js
const KEY = 'cart';
export function getCart() {
  return JSON.parse(localStorage.getItem(KEY) || '[]');
}
export function addToCart(id) {
  const cart = getCart();
  const i = cart.findIndex(x => x.id === id);
  if (i >= 0) cart[i].qty++;
  else cart.push({ id, qty: 1 });
  localStorage.setItem(KEY, JSON.stringify(cart));
}

// ui/events.js (en la rama add-to-cart)
import { addToCart } from '../core/cart.js';
...
if (add) {
  const id = Number(add.dataset.id);
  addToCart(id);
  showToast('Producto agregado al 🛒 con éxito ✔️', { type: 'success' });
  return;
}

11) Ordenar por rating / Top N

Objetivo: Más criterios “rápidos”.

Dónde: filters.js o donde te pidan

// rating desc (mejor a peor)
state.view.sort((a,b)=> (b.rating?.rate || 0) - (a.rating?.rate || 0));

// top 5 por rating
const top5 = [...state.products]
  .sort((a,b)=> (b.rating?.rate || 0) - (a.rating?.rate || 0))
  .slice(0,5);

12) Ofertas simuladas

Objetivo: Colección “ofertas”.

// ejemplo: precio < 50
const offers = state.products.filter(p => p.price < 50);
// o una etiqueta visual si quieres marcar algunas random

13) Login simulado (localStorage)

Objetivo: Guardar sesión simple.

Dónde: auth.js

// auth.js
const KEY = 'session';
export function login(user) {
  localStorage.setItem(KEY, JSON.stringify({ user }));
}
export function logout() {
  localStorage.removeItem(KEY);
}
export function getSession() {
  return JSON.parse(localStorage.getItem(KEY) || 'null');
}

14) Orden recomendado de transformaciones (para no liarte)

base = state.products

búsqueda (si hay)

filtro (categoría)

orden

state.view = resultado

paginación con slice

renderPage()

15) Errores típicos y cómo evitarlos

Listeners duplicados → usa bandera (eventsBound) o registra una sola vez.

No existe #modal / #toast-root → añade en el HTML.

getElementById('sort ') con espacio → debe ser 'sort' exacto.

Modal que requiere 2–3 clics → eliminar modal previo y quitar listeners al cerrar.

Filtrar sobre view varias veces → siempre parte de products.