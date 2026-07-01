# Diseño: Blog jordimariezcurrena.com

**Fecha:** 2026-07-01
**Estado:** Aprobado, pendiente de plan de implementación

## Objetivo

Blog personal multidioma sobre tecnología/producto/carrera profesional (estilo
entre Jaime Gómez-Obregón y Amalia López Acera), con una página "Sobre mí" que
incluye una línea de tiempo profesional al estilo Manfred (cards de
experiencia laboral/formación con icono, fechas y descripción).

## Stack

- **Generador:** Hugo (extended, última versión estable)
- **Tema:** [PaperMod](https://github.com/adityatelange/hugo-PaperMod), instalado como Hugo Module
- **Repositorio:** GitHub, público, rama `main`
- **Hosting:** Cloudflare Pages, conectado al repo (deploy automático en cada push a `main`, preview URL en cada PR)
- **Dominio:** `jordimariezcurrena.com`, a comprar en Cloudflare Registrar y gestionar como dominio custom del proyecto de Pages (SSL automático)

### Por qué este stack (no reabrir salvo cambio de requisitos)

- Hugo: build instantáneo, sin toolchain de Node/JS, i18n nativo maduro. No se justifica un generador basado en componentes (Astro/Next.js) para contenido de texto sin interactividad compleja.
- Cloudflare Pages: bandwidth ilimitado en el plan gratuito, mismo proveedor que DNS/dominio (SSL sin fricción), margen para añadir Cloudflare Pages Functions si en el futuro se necesita algo dinámico (p. ej. formulario de contacto).
- Arquitectura JAMstack: sin servidor que mantener, sin base de datos, deploy vía git.

## Idiomas

| Idioma     | Código | Ruta                        |
|------------|--------|------------------------------|
| Castellano | `es`   | `/` (por defecto, sin prefijo) |
| Catalán    | `ca`   | `/ca/`                        |
| Inglés     | `en`   | `/en/`                         |

Cada idioma tiene título, descripción, menú y strings de UI propios en `hugo.toml` (`[languages.es]`, `[languages.ca]`, `[languages.en]`) y `i18n/*.toml`.

## Estructura de contenido

Traducciones por sufijo de idioma, enlazadas por `translationKey` en el front matter (permite publicar un post en un solo idioma sin romper nada):

```
content/
  posts/
    mi-primer-post.es.md
    mi-primer-post.ca.md
    mi-primer-post.en.md
  projects/
    mi-proyecto.es.md   (front matter: url, image, tags)
    mi-proyecto.ca.md
    mi-proyecto.en.md
  about.es.md / .ca.md / .en.md
  contact.es.md / .ca.md / .en.md
```

**Slugs traducidos por idioma** (prioridad SEO): cada traducción define su propio `slug` en el front matter, p. ej. página de proyectos:

```
jordimariezcurrena.com/proyectos/
jordimariezcurrena.com/ca/projectes/
jordimariezcurrena.com/en/projects/
```

## Páginas

1. **Blog** (`/posts/` o listado en home) — posts en Markdown, taxonomías estándar de PaperMod (tags)
2. **Sobre mí** — contenido + timeline profesional (ver abajo)
3. **Proyectos** — listado tipo grid de proyectos destacados, cada uno como entrada de contenido independiente (imagen, título, descripción corta, enlace)
4. **Contacto** — página estática con email y redes sociales. Sin formulario funcional en esta fase (se añadiría después con Cloudflare Pages Functions si se decide)

### Timeline de "Sobre mí"

Inspirada en la captura de referencia (perfil estilo Manfred: cards de
experiencia con logo, cargo, fechas y descripción expandible).

- Datos en `data/timeline.es.yaml`, `.ca.yaml`, `.en.yaml`: cada entrada con empresa, logo, cargo, fechas, descripción, tipo (trabajo/formación)
- Partial custom `layouts/partials/timeline.html` que renderiza las cards, con CSS propio superpuesto al de PaperMod (sin modificar el core del tema)
- Añadir una experiencia futura = añadir una entrada al YAML, sin tocar HTML

## SEO técnico (incluido desde el primer commit)

- `hreflang` automático entre traducciones vía `translationKey`
- Sitemap.xml multiidioma + robots.txt (generados por Hugo)
- URLs canónicas automáticas por página
- Meta `description` obligatoria por post/página (front matter)
- Open Graph + Twitter Cards con imagen de portada por post
- JSON-LD: `BlogPosting` en posts, `BreadcrumbList` global, `Person` en "Sobre mí"
- RSS feed por idioma
- Imágenes procesadas a WebP + `srcset` + lazy loading (Hugo image processing)
- `hugo --minify` en build + Brotli automático de Cloudflare
- Redirecciones forzadas `http`→`https` y `www`→apex (dominio canónico sin `www`, coherente con las URLs usadas en todo este documento) a nivel de Cloudflare
- Página 404 personalizada

Fuera del alcance del código (acciones post-despliegue): alta en Google Search Console y Bing Webmaster Tools, envío del sitemap.

## Despliegue

1. Repo GitHub público, rama `main`, en `https://github.com/jordimariezcurrena/jordimariezcurrena.com`
2. Cloudflare Pages conectado al repo: preset "Hugo", build command `hugo mod get && hugo --minify`, output dir `public`, variable de entorno `HUGO_VERSION` fijada a la última extended
3. Dominio comprado en Cloudflare Registrar, añadido como dominio custom del proyecto Pages (SSL automático)
4. Push a `main` → deploy a producción. Pull Request → preview URL única

## Fuera de alcance (por ahora)

- Formulario de contacto funcional (Cloudflare Pages Functions) — posible mejora futura
- CMS visual (Decap/Netlify CMS) para edición sin git — posible mejora futura
- Comentarios en posts (Disqus/utterances/giscus) — no solicitado, no se incluye
