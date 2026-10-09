# Arquitectura

## Resumen

El sitio original **grupopyh.com** es un **WordPress** que genera el HTML en el servidor con PHP.
Este repositorio contiene el **resultado ya generado** (HTML, CSS, JS e imágenes estáticos), así que no
tiene paso de compilación: no usa Node.js, Next.js ni `npm install`. Cualquier servidor de archivos estáticos
lo puede servir.

## Sitio original (producción)

```
Navegador
   │  HTTPS
   ▼
Servidor web (hosting compartido / VPS)
   │
   ▼
WordPress (PHP) ──► Base de datos MySQL (páginas, ajustes, menús)
   │
   ├─ Tema: Jupiter X
   ├─ Constructor visual: Elementor (+ Raven, el addon de Jupiter X)
   ├─ Plugins: JetElements, JetTabs, JetTricks, WWS (botón de WhatsApp)
   └─ Seguridad: rutas de WordPress renombradas
   │
   ▼
HTML generado + CSS/JS/imágenes
```

### Tecnologías identificadas

| Pieza | Evidencia en el repo |
|---|---|
| WordPress | sitemap `wp-sitemap.xml`, referencias a `wp-json` y `xmlrpc.php` |
| Tema Jupiter X | `myimages/jupiterx/`, `wp-content/themes/` |
| Elementor | `core/styleme/f65f29574d/` y `core/styleme/ccc473c329/` |
| Raven (addon de Jupiter X) | `core/styleme/ed9fefdaff/includes/extensions/raven/` |
| JetElements | `core/styleme/6e430aa1a8/` |
| JetTabs | `core/styleme/56256235cf/` |
| JetTricks | `core/styleme/ec0fa027d3/` |
| Botón de WhatsApp (WWS) | `core/styleme/6cff81c5b8/` |
| Librerías JS | jQuery, Swiper (sliders), SmartMenus, Sticky |
| Fuentes e iconos | Google Fonts, Font Awesome, Eicons |

### Rutas de WordPress renombradas (seguridad)

Las carpetas estándar de WordPress están renombradas para ocultar la plataforma y dificultar ataques
automatizados dirigidos a rutas conocidas:

| Nombre estándar | Nombre en este sitio |
|---|---|
| `wp-content/plugins/<plugin>` | `core/styleme/<hash>` |
| `wp-content/uploads` | `myimages/` (organizado por año) |
| `wp-includes` | `libary/` |

## Copia estática (este repositorio)

```
Grupo PyH/
├── index.html                 ← portada
├── nosotros/  contacto/  clientes/  equipamiento-industrial/
├── calderaspyh/  explotacion-petrolera-ph/  fertilizantes-pyh/
├── core/styleme/              ← CSS/JS de los plugins
├── myimages/                  ← imágenes y CSS compilado del tema
├── libary/                    ← jQuery y núcleo JS de WordPress
├── wp-content/themes/         ← recursos del tema
├── docs/                      ← documentación
└── README.md
```

Cada página es una carpeta con su `index.html`, igual que las URLs del sitio original
(`/nosotros/` → `nosotros/index.html`). Los enlaces entre páginas se reescribieron como rutas relativas
para que la navegación funcione sin conexión.

### Qué funciona y qué no

| Funciona | No funciona (necesita el backend original) |
|---|---|
| Navegación entre páginas | Formulario de contacto |
| Estilos, imágenes y fuentes | Búsqueda |
| Sliders, pestañas y animaciones | Panel de administración |
| Diseño responsive | Feeds RSS, `wp-json`, `xmlrpc.php` |

### Problemas conocidos heredados del sitio original

- `myimages/2026/02/CALDERAS-HORIZONTALES.webp` (página Calderas): en el servidor original este archivo es en
  realidad un documento HTML con extensión `.webp`, así que la imagen tampoco se muestra en producción.

### Qué no está incluido

- Código PHP de WordPress, del tema y de los plugins
- Base de datos MySQL
- Configuración del servidor

## Cómo se generó la copia

1. Se obtuvieron las URLs públicas del sitemap (`wp-sitemap.xml`), respetando `robots.txt`.
2. Se descargaron con `wget` (`--page-requisites --convert-links --adjust-extension`) con pausas entre peticiones.
3. Un script reescribió como rutas relativas los enlaces internos que `wget` no convirtió.
4. Se comprobó en un servidor local que las páginas respondían y que no faltaba ningún recurso local.
5. Se corrigió un fallo de conversión de `wget`: en el original, el `srcset` del logo móvil tiene espacios
   dentro de las comillas (`' https://…/Logo-Grupo-PyH-min.png '`) y `wget` lo reescribió como `…min.pngg`,
   lo que rompía el logo en móvil.
6. Se eliminaron las páginas de demostración del tema (`portfolio/`, `portfolio-category/`, `404-2/`), que no tenían contenido de la empresa ni enlaces desde el sitio.

## Cómo servirlo

```bash
python3 -m http.server 8000      # http://localhost:8000
# alternativas: npx serve .  |  Nginx  |  GitHub Pages
```
