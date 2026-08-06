# Deploy — Verzet Landing

Sitio 100% estático (HTML/CSS/JS vanilla, sin build, sin dependencias). El deploy es literalmente copiar los archivos del repo tal cual — no hay paso de compilación de por medio.

## Arquitectura

- **Producción:** Hostinger (plan Business), sirviendo `verzet.com`, vía la integración Git de hPanel apuntando a este repo (rama `master`).
- **Staging / respaldo temporal:** GitHub Pages (https://alefernandez88.github.io/verzet-landing/) — se mantiene mientras se confirma que Hostinger funciona bien en producción. Decidir más adelante si se da de baja o se deja como espejo.
- **Fuente de verdad:** siempre el repo en GitHub. Nada de editar archivos a mano en el servidor ni subir por FTP suelto — todo cambio pasa por un commit.

## Setup inicial en Hostinger (una sola vez, todavía no se hizo)

1. En hPanel: **Sitios web → (verzet.com) → Avanzado → Git**.
2. Conectar el repositorio `https://github.com/aleFernandez88/verzet-landing`, rama `master`, carpeta destino = raíz del dominio (`public_html` o la que Hostinger asigne).
3. El repo es público, así que no debería hacer falta clave SSH; si hPanel la pide, generarla ahí y agregarla como *deploy key* en GitHub (Settings → Deploy keys del repo).
4. Activar **auto-deploy (webhook)** si el plan Business lo ofrece, para que cada push a `master` dispare el deploy solo. Si no está disponible, el deploy se dispara a mano con el botón "Pull"/"Deploy" del panel de Git.
5. **DNS:** apuntar `verzet.com` a Hostinger (nameservers o registros A/CNAME, según cómo esté hoy delegado el dominio).
6. **SSL:** activar el certificado automático (Let's Encrypt) de Hostinger para `verzet.com`.
7. Una vez confirmado que el dominio resuelve bien, actualizar en el código las referencias que hoy apuntan al deploy de GitHub Pages:
   - `canonical` y `og:url` en `index.html`
   - `sitemap.xml`
   - `robots.txt` (si referencia una URL absoluta)
   - `sameAs` del JSON-LD en `index.html`
8. Verificar visualmente el sitio en `verzet.com` (desktop + mobile, hard refresh) antes de dar la migración por terminada.

> Nota: esta guía es general — la primera vez que entremos juntos a hPanel puede que algún nombre de menú/botón varíe un poco respecto a lo de arriba; se ajusta ahí mismo.

## Flujo de cambios (día a día, una vez migrado)

1. Trabajar en local, probar con `python -m http.server` + hard refresh (`ctrl+shift+r`) — el server local cachea agresivo tanto CSS como imágenes.
2. `git add` + commit en `master` con mensaje descriptivo.
3. `git push origin master`.
4. Si el auto-deploy (webhook) está activo, Hostinger despliega solo en 1-2 minutos. Si no, entrar a hPanel → Git → "Pull latest" / "Deploy".
5. Verificar en `https://verzet.com` (hard refresh) que el cambio se ve bien, desktop y mobile.

## Si algo sale mal (rollback)

1. `git revert <commit>` (nunca `reset --hard` sobre historial ya pusheado) y push a `master`.
2. Con auto-deploy, Hostinger despliega el revert solo; si es manual, repetir "Pull latest" en hPanel.
3. Si el problema parece ser solo de caché (CDN/browser) y el código está bien, probar primero un hard refresh antes de asumir que hace falta revertir.

## Pendiente

- ~~Confirmar plan Business activo y dominio `verzet.com` apuntando a Hostinger.~~ ✅ hecho.
- ~~Conectar el repo vía Git en hPanel (setup inicial de arriba).~~ ✅ hecho — auto-deploy activo, cada push a `master` despliega en Hostinger.
- ~~Actualizar `canonical` / `og:url` / `sitemap.xml` / `robots.txt` / `sameAs` del JSON-LD una vez el dominio esté en vivo.~~ ✅ hecho.
- Decidir si se da de baja GitHub Pages o se deja como espejo/staging (sigue sirviendo el mismo `index.html`, ahora con `canonical` apuntando a `verzet.com`, así que no genera problema de contenido duplicado mientras tanto).
- Enviar `sitemap.xml` a Google Search Console (propiedad `verzet.com`) para acelerar indexación — no se hizo todavía.
- Cuando Brasil vuelva al sitio (ver contexto en el historial de commits, franja pospuesta el 2026-08-04), este flujo de deploy no cambia — solo cambia el contenido que se pushea.
