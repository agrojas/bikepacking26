# Diseño: Rediseño Web + Exportar PDF

**Fecha:** 2026-04-17  
**Archivo afectado:** `index.html` (único archivo del proyecto)

---

## Objetivo

Convertir el documento de impresión A4 en una página web navegable con tabs, manteniendo la capacidad de exportar a PDF mediante `window.print()`.

---

## Enfoque

Un único archivo `index.html` con dos capas de CSS:
- **`@media screen`**: layout web con tabs, hero, cards, spacing generoso
- **`@media print`**: restaura el layout A4 original (oculta nav, muestra todo en secuencia)

Un botón "Exportar PDF" en el header llama a `window.print()`.

---

## Estructura general

```
┌─────────────────────────────────────┐
│ Header fijo: logo + botón PDF       │
├─────────────────────────────────────┤
│ Tab bar sticky: 6 tabs              │
├─────────────────────────────────────┤
│ Content area: solo tab activo       │
│ (scrolleable)                       │
└─────────────────────────────────────┘
```

### Header
- Fondo `#2d4a2d`, height ~50px
- Izquierda: "ESQUEL LOOP 520" en `#c8852a`
- Derecha: botón "🖨 Exportar PDF" (llama `window.print()`)
- En mobile (< 768px): hamburger que despliega tabs como menú vertical

### Tab bar
- Fondo `#3d6b3a`, sticky debajo del header
- Tab activo: texto `#c8852a` + border-bottom 2px `#c8852a`
- Tabs inactivos: texto `rgba(255,255,255,0.5)`
- 6 tabs: Portada · Mapa · Itinerario · Descanso · Fronteras · Equipo
- JS puro: click en tab → oculta todos los `.tab-content`, muestra el seleccionado

---

## Contenido por tab

### Tab 1 — Portada
Hero oscuro full-width:
- Degradado `#1a3a1a → #2d4a2d → #3d3010`
- Título "ESQUEL / LOOP 520" en blanco y `#c8852a`
- Subtítulo en italic semitransparente
- Franja de 4 stats (520 km · 8 días · ~80% ripio · 3.800m D+)
- Alerta naranja (`border-left: 3px solid #c8852a`)
- Trazado de ruta en monospace debajo del hero sobre fondo `#f5f0e8`

### Tab 2 — Mapa
- Google Maps embed ocupa toda la altura disponible (`height: calc(100vh - 100px)`)
- Footer con texto de ruta + link "Ver en Google Maps →"

### Tab 3 — Itinerario
- Sección label + título "7 Días de Pedaleo"
- Cada día: card con `border-left` según dificultad (verde/naranja/rojo)
  - Número de día, nombre de etapa, descripción breve, tags (km · terreno · dificultad)
  - Tip/alerta en bloque monospace con `border-left: 2px solid #c8852a`
- Box de clima al final (fondo `#fff8f0`, borde `#c8852a`)

### Tab 4 — Descanso
- Sección label + título "Día de Descanso"
- Intro explicativo
- 3 cards de opciones (Opción 1 recomendada con borde `#c8852a` y badge)
- Tabla resumen de itinerario 8 días al final (fondo `#2d4a2d`)

### Tab 5 — Fronteras & Servicios
- Sección label + título "Cruces de Frontera"
- 2 cards en grid (Paso Futaleufú · Paso Río Encuentro), cada una con header `#2d4a2d`
- Doc box documentación
- Separador con título "Puntos de Servicio"
- Tabla de servicios con dots de colores (verde/amarillo/rojo)

### Tab 6 — Equipo
- Sección label + título "Equipo Esencial"
- Grid 4 columnas de items de equipo (fondo `#f5f0e8`, borde `#c4b89a`)
- Separador con título "Puntos de Interés"
- Grid 3 columnas de POI cards (border-top `#2d4a2d`)

---

## Print / Exportar PDF

`@media print`:
- Header y tab bar: `display: none`
- Todos los `.tab-content`: `display: block` (visible independientemente del tab activo)
- Cada sección: `page-break-before: always` (excepto la primera)
- Fuentes y layouts revierten a los tamaños compactos del diseño original A4
- El botón PDF desaparece del print

---

## Paleta de colores

Misma paleta actual, sin cambios:

| Variable | Valor | Uso |
|----------|-------|-----|
| `--pine` | `#2d4a2d` | Header, elementos primarios |
| `--ochre` | `#c8852a` | Acentos, tabs activos, stats |
| `--paper` | `#f5f0e8` | Fondos de contenido |
| `--border` | `#c4b89a` | Bordes |
| `--muted` | `#7a6e5e` | Texto secundario |

---

## Mobile

- Breakpoint: 768px
- Tab bar colapsa a hamburger `☰` → despliega menú vertical sobre el contenido
- Grids de 4 cols → 2 cols, grids de 3 cols → 1 col
- Hero stats: 2×2 en lugar de 4 en fila

---

## No incluido en este diseño

- Animaciones de transición entre tabs
- Modo oscuro
- Filtros o búsqueda dentro del itinerario
