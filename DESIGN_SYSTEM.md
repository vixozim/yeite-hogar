# Yeite Hogar — Design System

> Relevado desde `index.html` + `assets/css/styles.css`. Sitio HTML/CSS vanilla, sin build step, sin framework ni JS más allá del toggle del menú mobile.

## 1. Stack técnico

- **HTML**: multipágina estática, sin build step. `index.html` en la raíz (secciones ancladas por `id`: `#hero`, `#servicios`, `#consorcios`, `#porque`, `#quienes`, `#cta-final`) + 5 páginas de servicio en `servicios/*.html` (`reparaciones-hogar`, `carpinteria-muebles`, `construccion-mejoras`, `electricidad`, `herreria`). Cada página de servicio repite nav/preloader/footer completos (no hay includes ni templating — se duplica a mano).
- **CSS**: 1 archivo compartido (`assets/css/styles.css`), sin preprocesador, sin metodología de nombres (BEM parcial, ad-hoc por sección). `assets/css/styles1.css` existe en el repo pero no está referenciado por ningún HTML (legacy, no tocar salvo que se confirme que se puede borrar).
- **JS**: inline y duplicado en cada HTML (index + 5 páginas de servicio). Maneja: preloader animado, toggle del menú mobile (`.nav-toggle` / `.nav-links.open`), dropdown "Servicios" (`.nav-dropdown` / clase `open`, toggle por click + hover en desktop vía CSS), y el fondo del nav al hacer scroll. Al editar este script hay que replicar el cambio en los 6 archivos.
- **Fuentes**: Google Fonts — `Fraunces` (serif, display/títulos) + `Inter` (sans, cuerpo).
- **Iconos**: SVG propios en `assets/img/ico-*.svg` (servicios) + SVG inline (sección "Por qué elegirnos" y checklist de tareas en páginas de servicio).
- **Imágenes**: `hero.png` y `nosotros.png` son placeholders pendientes de reemplazo (hay `.png.txt` con la instrucción de qué foto poner). `hero2.png` y `nosotros2.png` se usan como imágenes genéricas placeholder en las páginas de servicio nuevas, junto con `image0.png`/`image1.png`/`nosotros.png` reutilizadas — todas a reemplazar por fotos reales del servicio correspondiente.

## 2. Tokens de diseño

### Colores (`:root`)

| Variable | Valor | Uso |
|---|---|---|
| `--navy` | `#113F65` | Color de marca principal: títulos, nav links, footer bg, iconos outline |
| `--orange` | `#EF771E` | Acento/CTA: botones, hover de links, dividers, WhatsApp |
| `--cream` | `#F5F0EA` | Fondo de sección alterna (hero, consorcios, quiénes somos) |
| `--cream2` | `#EDE5D8` | Fondo CTA final (variante más oscura de cream) |
| `--white` | `#FFFFFF` | Fondo base, texto sobre navy/orange |
| `--ink` | `#1A1A1A` | Texto principal (body) |
| `--ink2` | `#444444` | Texto secundario/párrafos |
| `--muted` | `#888888` | Texto terciario (nota de WhatsApp en hero) |
| `--max-w` | `1160px` | Ancho máximo de contenido (`.wrap`, `*-inner`) |

Colores "sueltos" fuera de variables (candidatos a tokenizar):
- `#d4660f` — hover de `.btn-orange` / `.nav-cta` (navy quemado del orange)
- `#25D366` — verde WhatsApp oficial (`.wa-float`)
- `#E8E8E8` — borde de `.srv-card`
- `#eee` — bordes del menú mobile

### Tipografía

- **Display / Headings**: `'Fraunces', serif` — pesos 400/700, también 400 italic. Siempre en `--navy`, mayormente `text-transform: uppercase` + `letter-spacing: .06–.08em` en H2 de sección. Tamaños con `clamp()` fluido:
  - H1 hero: `clamp(2rem, 3.6vw, 3rem)`, weight 700, line-height 1.15
  - H2 sección (servicios/quiénes): `clamp(1.5rem, 2.5vw, 2rem)`
  - H2 consorcios: `clamp(1.6rem, 2.8vw, 2.2rem)`
  - H2 CTA final: `clamp(1.8rem, 3vw, 2.5rem)` (único sin uppercase)
- **Body**: `'Inter', sans-serif` — pesos 400/500/600/700. Tamaño base `16px`, `line-height: 1.6`.
  - Párrafo lead: `.92rem–1.08rem`, line-height `1.7–1.78`
  - Microcopy (nav, badges, botones): `.78rem–.9rem`, uppercase + letter-spacing en varios casos

### Navegación

- `.nav-dropdown` (li que envuelve "Servicios"): `.nav-drop-toggle` es un `<button>` (no `<a>`) que abre `.nav-dropdown-menu` (lista de las 5 páginas de servicio). Se abre con click (todas las resoluciones) y además con hover en desktop (`@media (hover:hover) and (pointer:fine)`). En mobile (`≤860px`) el submenú pasa a acordeón estático dentro del menú hamburguesa en vez de flotar.
- Los links "Consorcios y administraciones" y "Sobre nosotros" apuntan a anclas de `index.html` (`index.html#consorcios`, `index.html#quienes`) — esas secciones solo existen en la home, no en las páginas de servicio.
- Desde `servicios/*.html` todos los `href` a la raíz usan `../` (`../index.html`, `../assets/...`); entre páginas de servicio se usa ruta relativa directa (`./otra-pagina.html`).

### Espaciado y layout

- Sin escala de spacing formal (valores en `rem` ad-hoc: `.4rem .5rem .6rem .75rem .8rem 1rem 1.2rem 1.6rem 1.8rem 2rem 2.5rem 3.5rem 4rem 4.5rem 5rem`).
- Contenedor estándar: `.wrap` / `*-inner` → `max-width: 1160px; margin: 0 auto; padding: 0 2rem;`
- Secciones full-bleed con padding vertical propio (`4rem`, `4.5rem`, `5rem`).
- Grids:
  - Hero: `1fr 1fr` (texto / imagen)
  - Servicios (home, `.servicios-grid`, 5 cards): `repeat(5, 1fr)` → `repeat(3,1fr)` en 1024px → `repeat(2,1fr)` en 860px
  - Servicios (páginas de servicio, "Otros servicios", `.servicios-grid.servicios-grid--compact`, 4 cards): `repeat(4, 1fr)` → sigue las mismas media queries que `.servicios-grid` al achicar pantalla
  - Por qué elegirnos: `repeat(7, 1fr)` → `repeat(4,1fr)` → `repeat(3,1fr)` → `repeat(2,1fr)`
  - Consorcios / Quiénes somos: `1fr 1fr` → `1fr` (stack en mobile, imagen pasa a `order: -1` en "quiénes")

### Bordes, radios, sombras

- Radios: `12px` (cards, banner), `16px` (imágenes grandes), `50px` (botones/pill), `50%` (WA float), `2px` (dividers/mini-bars)
- Sombras:
  - Cards de imagen: `0 8px 32px rgba(0,0,0,.12)`
  - Hover de `.srv-card`: `0 4px 18px rgba(239,119,30,.12)` (sombra tintada con el color de marca — buen detalle)
  - WA float: `0 4px 16px rgba(0,0,0,.22)`
- Bordes: `1.5px solid #E8E8E8` (cards), `2px solid var(--navy)` (btn-outline), `3px solid var(--orange)` (blockquote `.q-bold`)

### Componentes (inventario)

| Componente | Clase | Notas |
|---|---|---|
| Botón primario | `.btn-orange` | Pill, icono + texto, hover cambia bg a `#d4660f` |
| Botón secundario | `.btn-outline` | Pill, borde navy, hover invierte a fill navy (parece no usarse actualmente en el HTML) |
| Nav | `nav`, `.nav-inner`, `.nav-links`, `.nav-cta` | Fixed, blur backdrop, CTA pill embebido en la lista |
| Menú mobile | `.nav-toggle` (hamburguesa CSS puro, sin SVG) | Transform-based (líneas giran a X) |
| Card de servicio | `.srv-card` | `<a>` (no `<div>`) con icono + título + `.srv-card-more` ("Ver más →"), hover con borde+shadow tintada. Usada en `.servicios-grid` (home, 5 cards) y `.servicios-grid.servicios-grid--compact` ("Otros servicios" en páginas de detalle, 4 cards) |
| Hero de página de servicio | `.service-hero` / `.service-hero-inner` | Igual patrón `1fr 1fr` que consorcios/quiénes, con `.service-breadcrumb`, `h1`, `.service-lead`, `.service-sub` |
| Lista de tareas incluidas | `#service-tareas` / `.service-tareas-list` / `.service-tarea-item` | Grid 2 columnas (1 en mobile) de items con check SVG + texto, mismo patrón visual que `.porque-item` pero en card con borde |
| Sección "Otros servicios" | `#otros-servicios` | Fondo cream, reutiliza `.servicios-grid--compact` con las 4 cards restantes para cross-link entre páginas de servicio |
| Banner CTA inline | `.banner-cta` | Bloque con emoji + texto, dentro de Servicios |
| Item "por qué elegirnos" | `.porque-item` | Icono SVG stroke + texto corto |
| Blockquote destacado | `.q-bold` | Borde izq. naranja + itálica |
| CTA final | `#cta-final` | Centrado, fondo cream2 |
| Footer | `footer` | Fondo navy, texto translúcido |
| Botón flotante WhatsApp | `.wa-float` | Fixed bottom-right, circular, verde WA real (no usa el token de marca) |

## 3. Animación / interactividad actual (diagnóstico)

Este es el estado real hoy — **prácticamente no hay animación**, solo transiciones de hover puntuales:

- `transition: background .2s` en botones
- `transition: box-shadow .2s, border-color .2s` en `.srv-card`
- `transition: color .2s` en links de nav
- `transition: transform .2s` en `.wa-float` (scale hover)
- `transition: transform .25s, opacity .25s` en las líneas del `.nav-toggle`
- `scroll-behavior: smooth` en `html`
- Ya existe `@media (prefers-reduced-motion: reduce)` que anula todas las transiciones — **hay que respetar este bloque al agregar cualquier animación nueva** (incluyendo keyframes, no solo transitions).

No hay: scroll reveal, parallax, stagger de listas, microinteracciones en cards más allá del hover, transición de entrada del menú mobile (aparece/desaparece sin fade), ni loading/skeleton states.

## 4. Estructura de página (orden de secciones)

**`index.html`:**
1. `nav` (fixed, con dropdown "Servicios")
2. `#hero` — headline + CTA + video/imagen
3. `#servicios` — grid de **5 cards-link** (una por página de servicio) + banner CTA
4. `#consorcios` — texto + imagen (fondo cream)
5. `#porque` — grid de 7 items con icono SVG
6. `#quienes` — imagen + texto institucional (fondo cream)
7. `#cta-final` — CTA centrado (fondo cream2)
8. `footer`
9. `.wa-float` (fixed, fuera de flujo)

**`servicios/*.html`** (5 páginas: reparaciones-hogar, carpinteria-muebles, construccion-mejoras, electricidad, herreria):
1. `nav` (fixed, idéntico a index con rutas `../`)
2. `.service-hero` — breadcrumb + h1 + lead + sub + CTA + imagen genérica
3. `#service-tareas` — grid de tareas incluidas en ese servicio
4. `#otros-servicios` — cards-link a los otros 4 servicios (fondo cream)
5. `#cta-final` — mismo CTA que la home
6. `footer`
7. `.wa-float`

## 5. Breakpoints

- `1024px`: servicios y "por qué" bajan de 4/7 a 4 columnas
- `860px`: nav se vuelve hamburguesa, hero/consorcios/quiénes pasan a 1 columna, servicios a 2 col, "por qué" a 3 col
- `560px`: "por qué" a 2 col, CTA final reduce tamaño de H2, botones full-width

## 6. Puntos a resolver

- **Resuelto**: las 5 cards de `#servicios` ahora son `<a>` que linkean a `servicios/*.html`, con botones "Ver más →". Se migró de 8 servicios a 5 categorías (ver mapeo de agrupación en el historial del proyecto).
- **No hay sistema de rutas/plantillas real** — al ser HTML estático plano sin build step, nav/preloader/footer/script se duplican a mano en los 6 archivos (`index.html` + 5 `servicios/*.html`). Cualquier cambio de nav o footer debe replicarse manualmente en todos.
- **Botón `.btn-outline` sigue definido pero sin uso visible** en el HTML actual.
- **Imágenes de las páginas de servicio son placeholders genéricos** (`hero2.png`, `nosotros2.png`, `image0.png`, `image1.png`, `nosotros.png` reutilizadas) — pendiente reemplazar por fotos reales de cada rubro.
- **Tags de Google Analytics/GTM duplicados en las 6 páginas** — si se cambia el ID o se agrega un tag nuevo, hay que actualizar los 6 archivos.

## 7. Dirección sugerida para animaciones (próximo paso, no implementado aún)

Pensado para mantenerse vanilla (sin librerías), liviano y coherente con la identidad ya establecida:

- **Scroll reveal** sutil (fade + translateY 16-24px) por sección vía `IntersectionObserver`, respetando `prefers-reduced-motion`.
- **Stagger** en grids (`.servicios-grid-2`, `.porque-grid`) — delay incremental por `nth-child` al entrar en viewport.
- **Micro-interacción de cards** más rica: icono con leve scale/rotate en hover, no solo el borde.
- **Nav**: transición de fade/slide al abrir el menú mobile (hoy es instantáneo).
- **Hero**: entrada inicial (no scroll-triggered) de headline + imagen con pequeño delay escalonado.
- **Botones**: mantener sutileza actual (no overdo), quizás un leve `translateY(-1px)` además del cambio de color.

---
*Documento generado a partir del estado actual del código. Actualizar si se tokeniza la paleta o se introduce un sistema de componentes nuevo.*
