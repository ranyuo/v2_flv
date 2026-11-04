# Portal IESTP “Fernando León de Vivero” — La Tinguiña, Ica

Archivos para publicar en Hostinger:

- `portal-private/`: código PHP, vistas y datos. Va **fuera** de `public_html`, al mismo nivel.
- `public_html/`: raíz pública del dominio, con la página, la intranet, el CSS, el JavaScript y las imágenes.

El archivo `portal-private/config.php` (datos de la base de datos y clave de instalación) **no se guarda en GitHub**: el repositorio es público. Se crea a partir de `portal-private/config.example.php`.

## Instalación nueva

1. **Base de datos:** en hPanel › *Bases de datos › Bases de datos MySQL*, crea una base y un usuario con su contraseña. Anota los tres datos.
2. **Descarga el código:** en GitHub, *Code › Download ZIP*.
3. **Sube y extrae:** con el *Administrador de archivos*, sube el ZIP a la carpeta del dominio (la que contiene `public_html`) y extráelo. GitHub mete todo en una carpeta `flv-main/`; mueve `portal-private/` y `public_html/` desde ahí a la carpeta del dominio y reemplaza el `public_html` vacío.
4. **Configura:** copia `portal-private/config.example.php` como `portal-private/config.php` y completa:
   - `db_name`, `db_user` y `db_password`;
   - `install_key`: una clave privada aleatoria de 32 caracteres o más. Guárdala.
5. **Instala:** abre `https://tu-dominio/install.php`, escribe la `install_key` y crea la cuenta del administrador principal.
6. **Borra** `public_html/install.php`. Después de instalar ya no sirve; si lo vuelves a abrir responde "La aplicación ya está instalada".
7. **Permisos:** `public_html/assets/cache/` debe tener permisos **755**.
8. **Comprueba:**
   - Abre el sitio y entra a la intranet con la cuenta creada.
   - Revisa *Configuración*: datos institucionales y logo.
   - En *Mesa de partes › Áreas receptoras › Editar áreas*, carga las áreas reales con sus correos.
   - En *Usuarios y roles*, crea las cuentas del personal.

**Opcional, respaldos automáticos:** en hPanel › *Avanzado › Trabajos cron*, crea una tarea diaria con `php /home/<usuario>/domains/<dominio>/portal-private/cron.php`. Luego actívala en la intranet, en *Copias de seguridad*.

## Actualizar una instalación existente

Sube `portal-private/` y `public_html/` encima de las carpetas actuales y acepta **sobrescribir**. **No borres las carpetas antes:** así se conservan `config.php`, `backups/` e `install.lock`. Después, borra `public_html/install.php`.

## Dónde se edita

- **CSS y JavaScript del portal:** `public_html/assets/app/` (`portal.css` y `portal-1.js` a `portal-4.js`).
- **Armazón de la página:** `portal-private/views/portal.php`.
- **Contenido inicial** que carga la instalación: `portal-private/data/portal.json`.
- **Imágenes y documentos:** `public_html/assets/flv/`. Las imágenes grandes tienen una copia `.webp` que el servidor entrega a los navegadores compatibles.

### Diseños, carrusel de programas y galería de noticias

- **Diseño del portal:** en la intranet, *Temas y apariencia › Diseño del portal*, se elige entre **Clásico institucional**, **Moderno**, **Editorial** y **Dinámico** (estilo universitario de alto contraste: menú oscuro, titulares en mayúsculas resaltados, accesos con íconos circulares sobre la portada y franjas oscuras con trama). El diseño cambia la estructura visual del sitio público; los colores siguen el tema activo.
- **Programas de estudio en carrusel:** se muestran en carrusel por omisión, en la portada y en la página de programas. Para cambiar a cuadrícula, usa *Vista en la web* en el módulo *Programas de estudio* o *Presentación de los programas de estudio* en *Portada › Bloques, accesos y cifras*.
- **Noticias con varias fotos:** cada noticia admite la imagen principal y hasta 4 fotos adicionales (5 en total). En la web se muestran como galería con visor ampliado.
- **Accesos rápidos:** la instalación nueva incluye *Plataforma Virtual* (https://flv.iapp.pe) y *Correo Institucional* (Google Workspace, https://mail.google.com/a/iestpflv.edu.pe). Subir los archivos no cambia los datos ya guardados: en una instalación existente, agrega *Correo institucional* (identificador `correo`) en *Enlaces y servicios* y luego los dos accesos (`ext:aula` y `ext:correo`) en *Portada › Bloques, accesos y cifras*.

El sistema es una copia del portal de Nasca (`ranyuo/claudenasca`, commit `bf8264c`) adaptada con la información pública de https://iestpflv.edu.pe/, consultada el 30 de octubre de 2026. Las imágenes y los documentos se sirven desde `public_html/assets/flv/`, no desde la web original.

## Datos que debe confirmar la institución

- RUC, código modular, horario de atención y redes sociales.
- Documentos de gestión (PAT, PEI, PCI, RI, MPP, MPA): la web original no tiene enlaces a los archivos.
- Planes de estudio, horarios y horas de EFSRT de cada programa: la web original no tiene enlaces a los archivos.
- Coordinadores de Asistencia Administrativa y Mecatrónica Automotriz: la plana jerárquica y la plana docente indican personas distintas (se usó la plana jerárquica).
- Estadísticas, becas, matrícula e inversiones: las páginas de la web original están vacías.
