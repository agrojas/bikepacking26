# Web Redesign — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Convertir `index.html` de documento A4 a sitio web con navegación por tabs, manteniendo exportación a PDF vía `window.print()`.

**Architecture:** CSS dual — estilos existentes se dejan como base, se agrega bloque `@media screen` con header fijo + tabs + layout web, y bloque `@media print` que oculta la navegación y restaura A4. JS puro inline para el tab switching.

**Tech Stack:** HTML + CSS + JS vanilla. Sin build system, sin dependencias nuevas.

---

## Archivos

| Archivo | Acción | Descripción |
|---------|--------|-------------|
| `index.html` | Modificar | Único archivo — HTML + CSS + JS |

---

### Task 1: HTML — header, nav y IDs de tabs

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Agregar header y tab bar al inicio de `<body>`**

Insertar inmediatamente después de `<body>`:

```html
<header id="site-header">
  <span class="site-logo">ESQUEL LOOP 520</span>
  <button class="pdf-btn" onclick="window.print()">🖨 Exportar PDF</button>
  <button class="hamburger" id="hamburger-btn" onclick="toggleMenu()" aria-label="Menú">☰</button>
</header>
<nav id="tab-bar">
  <button class="tab-btn active" data-tab="portada">Portada</button>
  <button class="tab-btn" data-tab="mapa">Mapa</button>
  <button class="tab-btn" data-tab="itinerario">Itinerario</button>
  <button class="tab-btn" data-tab="descanso">Descanso</button>
  <button class="tab-btn" data-tab="fronteras">Fronteras</button>
  <button class="tab-btn" data-tab="equipo">Equipo</button>
</nav>
```

- [ ] **Step 2: Agregar `id` y clase `tab-content` a cada `.page`**

Modificar los seis divs de página:

```html
<!-- Page 1 — era: <div class="page cover-page"> -->
<div class="page cover-page tab-content" id="portada">

<!-- Page 2 — era: <div class="page map-page"> -->
<div class="page map-page tab-content" id="mapa">

<!-- Page 3+4 — era: <div class="page itin-page"> -->
<div class="page itin-page tab-content" id="itinerario">

<!-- Page 5 — era: <div class="page"> (descanso) -->
<div class="page tab-content" id="descanso">

<!-- Page 6 — era: <div class="page"> (fronteras) -->
<div class="page tab-content" id="fronteras">

<!-- Page 7 — era: <div class="page"> (equipo) -->
<div class="page tab-content" id="equipo">
```

- [ ] **Step 3: Verificar en browser**

Abrir `index.html`. El header y los tabs deben aparecer superpuestos al contenido. El layout se va a ver mal — eso está bien, el CSS viene en el próximo task.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "restructure: add header, tab nav, and tab-content ids to pages"
```

---

### Task 2: JS — tab switching

**Files:**
- Modify: `index.html` (agregar `<script>` antes de `</body>`)

- [ ] **Step 1: Agregar el script de tab switching**

Insertar antes de `</body>`:

```html
<script>
  function showTab(tabId) {
    document.querySelectorAll('.tab-content').forEach(function(el) {
      el.style.display = 'none';
    });
    document.querySelectorAll('.tab-btn').forEach(function(btn) {
      btn.classList.remove('active');
    });
    var target = document.getElementById(tabId);
    if (target) target.style.display = 'block';
    var activeBtn = document.querySelector('[data-tab="' + tabId + '"]');
    if (activeBtn) activeBtn.classList.add('active');
    window.scrollTo(0, 0);
  }

  function toggleMenu() {
    document.getElementById('tab-bar').classList.toggle('open');
  }

  document.querySelectorAll('.tab-btn').forEach(function(btn) {
    btn.addEventListener('click', function() {
      showTab(btn.dataset.tab);
      document.getElementById('tab-bar').classList.remove('open');
    });
  });

  // Mostrar primer tab al cargar
  showTab('portada');
</script>
```

- [ ] **Step 2: Verificar en browser**

Abrir `index.html`. Al hacer click en los tabs, el contenido debe cambiar. El header y los tabs se verán superpuestos al contenido — normal por ahora.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add tab switching JS"
```

---

### Task 3: CSS screen — header, tab bar y layout base

**Files:**
- Modify: `index.html` (agregar bloque `@media screen` al final del `<style>`)

- [ ] **Step 1: Agregar el bloque `@media screen` al final del bloque `<style>`, antes del cierre `</style>`**

```css
/* ── SCREEN LAYOUT ──────────────────────────────── */
@media screen {
  body {
    padding-top: 90px;
  }

  /* Header fijo */
  #site-header {
    position: fixed;
    top: 0; left: 0; right: 0;
    background: #2d4a2d;
    height: 50px;
    display: flex;
    align-items: center;
    padding: 0 16px;
    z-index: 100;
    gap: 12px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.3);
  }
  .site-logo {
    color: #c8852a;
    font-size: 14px;
    font-weight: 900;
    letter-spacing: 1px;
    flex: 1;
    font-family: Arial, sans-serif;
  }
  .pdf-btn {
    background: rgba(200,133,42,0.15);
    border: 1px solid rgba(200,133,42,0.6);
    color: #c8852a;
    padding: 6px 14px;
    border-radius: 3px;
    cursor: pointer;
    font-size: 12px;
    font-family: 'Courier New', monospace;
    white-space: nowrap;
  }
  .pdf-btn:hover { background: rgba(200,133,42,0.3); }
  .hamburger {
    display: none;
    background: none;
    border: none;
    color: rgba(255,255,255,0.7);
    font-size: 22px;
    cursor: pointer;
    line-height: 1;
    padding: 0 4px;
  }

  /* Tab bar sticky */
  #tab-bar {
    position: fixed;
    top: 50px; left: 0; right: 0;
    background: #3d6b3a;
    display: flex;
    z-index: 99;
    border-bottom: 1px solid rgba(0,0,0,0.2);
    overflow-x: auto;
  }
  .tab-btn {
    background: none;
    border: none;
    border-bottom: 2px solid transparent;
    color: rgba(255,255,255,0.5);
    padding: 10px 18px;
    font-size: 13px;
    cursor: pointer;
    white-space: nowrap;
    font-family: Arial, sans-serif;
    transition: color 0.15s;
  }
  .tab-btn:hover { color: rgba(255,255,255,0.85); }
  .tab-btn.active {
    color: #c8852a;
    border-bottom-color: #c8852a;
    font-weight: bold;
  }

  /* Tabs ocultos por defecto — JS controla display */
  .tab-content {
    display: none;
    min-height: calc(100vh - 90px);
  }

  /* Eliminar page-breaks en pantalla */
  .page {
    page-break-before: auto;
    page-break-after: auto;
    min-height: auto;
  }
}
```

- [ ] **Step 2: Verificar en browser**

Abrir `index.html`. Header fijo verde arriba, tabs abajo. Al hacer click en cada tab el contenido cambia. El botón "Exportar PDF" visible en el header. El contenido de cada tab empieza justo debajo de la barra de tabs.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add screen CSS for fixed header and tab bar"
```

---

### Task 4: CSS screen — portada hero y contenido

**Files:**
- Modify: `index.html` (continuar el bloque `@media screen`)

- [ ] **Step 1: Agregar al bloque `@media screen` el CSS de la portada y del contenido**

Agregar dentro del bloque `@media screen` existente, al final:

```css
  /* Portada — hero full width */
  .cover {
    padding: 36px 24px 28px;
    min-height: auto;
  }
  .cover-title { font-size: 64px; }
  .cover-stats { margin-top: 24px; }
  .cover-stat { padding: 16px 12px 0 0; }
  .snum { font-size: 38px; }

  /* Mapa — ocupa toda la altura disponible */
  .map-page {
    min-height: calc(100vh - 90px);
    display: flex;
    flex-direction: column;
  }
  .map-img-wrap {
    flex: 1;
    height: calc(100vh - 140px);
  }
  .map-img-wrap iframe {
    height: 100%;
  }

  /* Itinerario — day cards mejoradas */
  .days-table tr {
    display: block;
    border: 1px solid #c4b89a;
    border-left: 4px solid #2d4a2d;
    border-radius: 4px;
    margin-bottom: 10px;
    overflow: hidden;
  }
  .days-table tr.itin-break { display: none; }
  .days-table tr:last-child { border-bottom: 1px solid #c4b89a; }
  .days-table td.day-n {
    display: inline-block;
    width: auto;
    padding: 8px 14px;
    vertical-align: middle;
  }
  .days-table td.day-body { display: block; padding: 12px 16px; }
  .day-desc { font-size: 13px; margin-bottom: 8px; }
  .day-tip { font-size: 12px; }
  .day-route-name { font-size: 15px; }

  /* Secciones de contenido — spacing web */
  .page:not(.cover-page):not(.map-page) {
    padding: 24px 28px;
    max-width: 900px;
    margin: 0 auto;
    width: 100%;
  }
  .sec-title { font-size: 24px; margin-bottom: 18px; }
  .sec-label { font-size: 11px; }

  /* Descanso */
  .rest-opt { margin-bottom: 14px; }
  .rest-opt-body { font-size: 13px; }
  .rest-opt-tip { font-size: 11px; }
  .ri-table td { font-size: 13px; }

  /* Fronteras */
  .fron-grid { display: flex; gap: 16px; margin: 0 0 16px; }
  .fron-col { display: block; flex: 1; }
  .fron-body ul li { font-size: 12px; }
  .doc-box p { font-size: 13px; }

  /* Servicios */
  .svc-table { font-size: 13px; }
  .svc-note { font-size: 11px; }

  /* Equipo — gear grid */
  .gear-cell { font-size: 12px; padding: 10px 12px; }
  .poi-card h3 { font-size: 13px; }
  .poi-card ul li { font-size: 12px; }

  /* Footer de página — solo para print */
  .page-footer { display: none; }
```

- [ ] **Step 2: Verificar en browser**

Abrir cada tab y verificar:
- **Portada**: hero verde oscuro con título grande, stats, alerta — se ve bien en pantalla completa
- **Mapa**: iframe ocupa la mayor parte de la pantalla
- **Itinerario**: cada día como card con borde izquierdo, buen espaciado
- **Descanso**: opciones con cards diferenciadas
- **Fronteras**: dos cards de pasos lado a lado, tabla de servicios
- **Equipo**: grid de gear + POI cards

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add screen CSS for content areas and sections"
```

---

### Task 5: CSS screen — mobile responsive

**Files:**
- Modify: `index.html` (agregar bloque `@media (max-width: 768px)` dentro de `<style>`)

- [ ] **Step 1: Agregar bloque mobile al final del bloque `<style>`, antes de `</style>`**

```css
/* ── MOBILE ─────────────────────────────────────── */
@media screen and (max-width: 768px) {
  .hamburger { display: block; }
  .pdf-btn { padding: 5px 10px; font-size: 11px; }

  #tab-bar {
    flex-direction: column;
    top: 50px;
    max-height: 0;
    overflow: hidden;
    transition: max-height 0.25s ease;
  }
  #tab-bar.open {
    max-height: 400px;
    border-bottom: 1px solid rgba(0,0,0,0.2);
  }
  .tab-btn {
    padding: 12px 20px;
    border-bottom: 1px solid rgba(255,255,255,0.08);
    border-left: 3px solid transparent;
    text-align: left;
  }
  .tab-btn.active {
    border-left-color: #c8852a;
    border-bottom-color: transparent;
  }

  body { padding-top: 50px; }
  .tab-content { min-height: calc(100vh - 50px); }

  .cover-title { font-size: 44px; }
  .snum { font-size: 28px; }
  .cover-stat { width: 33.3%; }

  .page:not(.cover-page):not(.map-page) { padding: 16px; }

  .fron-grid { flex-direction: column; }

  .days-table td.day-n { font-size: 18px; }

  /* Gear grid: 2 columnas en mobile */
  table[style*="border-spacing:5px"] tr {
    display: flex;
    flex-wrap: wrap;
  }
  table[style*="border-spacing:5px"] td {
    width: 48% !important;
  }

  /* POI grid: 1 columna en mobile */
  .poi-grid, .poi-col { display: block !important; width: 100% !important; }
  .poi-col { padding: 0 !important; }
}
```

- [ ] **Step 2: Verificar en browser**

Reducir el ancho del browser a < 768px (o usar DevTools device mode). Verificar:
- El botón ☰ aparece en el header
- Clicking ☰ despliega el menú de tabs verticalmente
- Clicking un tab cierra el menú y muestra el contenido
- La portada se ve bien en mobile (stats en 3 columnas, título más chico)
- Las cards de fronteras se apilan verticalmente

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add mobile responsive CSS with hamburger menu"
```

---

### Task 6: CSS print — restaurar layout A4

**Files:**
- Modify: `index.html` (agregar/expandir bloque `@media print`)

- [ ] **Step 1: Agregar bloque `@media print` al final del `<style>`, antes de `</style>`**

```css
/* ── PRINT / PDF ────────────────────────────────── */
@media print {
  /* Ocultar navegación web */
  #site-header,
  #tab-bar { display: none !important; }

  /* Mostrar todos los tabs en secuencia */
  .tab-content {
    display: block !important;
    min-height: auto;
  }

  /* Restaurar page breaks */
  .page {
    page-break-before: always;
    min-height: 240mm;
  }
  .page:first-of-type,
  .cover-page { page-break-before: auto; }

  /* Restaurar padding body */
  body { padding-top: 0; }

  /* Restaurar layout original de itinerario */
  .days-table tr {
    display: table-row !important;
    border: none;
    border-bottom: 1px solid #c4b89a;
    border-radius: 0;
    margin-bottom: 0;
  }
  .days-table tr.itin-break { display: table-row !important; }
  .days-table td.day-n { display: table-cell !important; }
  .days-table td.day-body { display: table-cell !important; }

  /* Restaurar padding de páginas de contenido */
  .page:not(.cover-page):not(.map-page) {
    padding: 14px 16px;
    max-width: none;
    margin: 0;
  }

  /* Restaurar grid de fronteras */
  .fron-grid { display: table !important; }
  .fron-col { display: table-cell !important; }

  /* Footer de página visible en print */
  .page-footer { display: table !important; }

  /* Mapa: altura fija en print */
  .map-img-wrap { height: 228mm; }
}
```

- [ ] **Step 2: Verificar exportación PDF**

En el browser, hacer click en "🖨 Exportar PDF". En el diálogo de impresión verificar:
- El header y la barra de tabs no aparecen
- Las 6 secciones se ven en secuencia, cada una en su propia página
- El mapa ocupa la página completa
- El layout es compacto (no el espaciado web)
- El footer de página verde aparece en cada página

- [ ] **Step 3: Verificar también que la vista web sigue funcionando correctamente**

Cerrar el diálogo de impresión y navegar entre tabs — todo debe seguir funcionando.

- [ ] **Step 4: Commit final**

```bash
git add index.html
git commit -m "feat: add print CSS to restore A4 layout for PDF export"
```

---

## Resumen de commits esperados

1. `restructure: add header, tab nav, and tab-content ids to pages`
2. `feat: add tab switching JS`
3. `feat: add screen CSS for fixed header and tab bar`
4. `feat: add screen CSS for content areas and sections`
5. `feat: add mobile responsive CSS with hamburger menu`
6. `feat: add print CSS to restore A4 layout for PDF export`
