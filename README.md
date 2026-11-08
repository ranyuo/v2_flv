# Portal IESTP “Fernando León de Vivero” — La Tinguiña, Ica

Archivos para publicar en Hostinger:

- `portal-private/`: código PHP, vistas y datos. Va **fuera** de `public_html`, al mismo nivel.
- `public_html/`: raíz pública del dominio, con la página, la intranet, el CSS, el JavaScript y las imágenes.

El archivo `portal-private/config.php` (datos de la base de datos y clave de instalación) **no se guarda en GitHub**: el repositorio es público. Se crea a partir de `portal-private/config.example.php`.

## Requisitos del hosting

- **PHP 8.1 o superior** con las extensiones `pdo_mysql`, `fileinfo`, `openssl`, `zlib`, `json` y `session` (Hostinger las trae activas). `mbstring` es recomendable.
- **MariaDB 10.4+ o MySQL 8** (en hPanel, «MySQL» es MariaDB). **Una base de datos por sitio:** las tablas usan el prefijo fijo `pisco_`.
- El sitio debe estar en la **raíz de un dominio o subdominio** (no en una subcarpeta).
- Carpetas con permiso de escritura: `portal-private/` (bloqueo de instalación, `backups/` y la configuración SMTP guardada desde la intranet) y `public_html/assets/cache/`.

## Instalación nueva

1. **Base de datos:** en hPanel › *Bases de datos › Bases de datos MySQL*, crea una base y un usuario con su contraseña. Anota los tres datos.
2. **Descarga el código:** en GitHub, *Code › Download ZIP*.
3. **Sube y extrae:** con el *Administrador de archivos*, sube el ZIP a la carpeta del dominio (la que contiene `public_html`) y extráelo. GitHub mete todo en una carpeta `v2_flv-main/`; mueve `portal-private/` y `public_html/` desde ahí a la carpeta del dominio y reemplaza el `public_html` vacío.
4. **Configura:** copia `portal-private/config.example.php` como `portal-private/config.php` y completa:
   - `db_name`, `db_user` y `db_password`;
   - `install_key`: una clave privada aleatoria de 32 caracteres o más. Guárdala.
5. **Instala:** abre `https://tu-dominio/install.php`, escribe la `install_key` y crea la cuenta del administrador principal.
6. **Borra** `public_html/install.php`. Después de instalar ya no sirve: si lo vuelves a abrir responde «El portal ya está instalado» (código 410). Mientras el portal no esté instalado, la portada muestra un aviso que indica abrir `/install.php`.
7. **Permisos:** `public_html/assets/cache/` debe tener permisos **755**.
8. **Comprueba:**
   - Abre el sitio y entra a la intranet con la cuenta creada.
   - Revisa *Configuración*: datos institucionales y logo.
   - En *Mesa de partes › Áreas receptoras › Editar áreas*, carga las áreas reales con sus correos.
   - En *Usuarios y roles*, crea las cuentas del personal.

**Correo de notificaciones (recomendado):** en la intranet, *Configuración › Configurar correo notificador (SMTP)*, indica el servidor, la cuenta y su contraseña de aplicación y activa el envío. Después, en hPanel › *Avanzado › Trabajos cron*, crea una tarea **cada minuto** con `php /home/<usuario>/domains/<dominio>/portal-private/mail-worker.php` para los reintentos. Detalles en [MEJORAS-MODULOS.md](MEJORAS-MODULOS.md).

**Opcional, respaldos automáticos:** en hPanel › *Avanzado › Trabajos cron*, crea una tarea diaria con `php /home/<usuario>/domains/<dominio>/portal-private/cron.php`. Luego actívala en la intranet, en *Copias de seguridad*.

## Actualizar una instalación existente

Sube `portal-private/` y `public_html/` encima de las carpetas actuales y acepta **sobrescribir**. **No borres las carpetas antes:** así se conservan `config.php`, `backups/` y la configuración SMTP guardada (`.smtp-settings.json`). Después, borra `public_html/install.php`.

La base de datos se actualiza sola la primera vez que se abre el sitio con la versión nueva (por ejemplo, crea la cola de correo `pisco_mail`); no se pierden datos ni cuentas.

## Dónde se edita

- **CSS y JavaScript del portal:** `public_html/assets/app/` (`portal.css` y `portal-1.js` a `portal-4.js`).
- **Armazón de la página:** `portal-private/views/portal.php`.
- **Contenido inicial** que carga la instalación: `portal-private/data/portal.json`.
- **Imágenes y documentos:** `public_html/assets/flv/`. Las imágenes grandes tienen una copia `.webp` que el servidor entrega a los navegadores compatibles.

### Diseños, carrusel de programas y galería de noticias

- **Diseño del portal:** en la intranet, *Temas y apariencia › Diseño del portal*, se elige entre **Clásico institucional**, **Moderno**, **Universitario**, **Editorial**, **Impacto**, **Impacto FLV**, **Horizonte** y **Campus**. *Universitario* reproduce la estructura de un portal universitario moderno con los datos y colores del instituto: cabecera oscura con el logotipo en un bloque blanco y el mismo menú principal, botón *Accesos institucionales* (Plataforma Virtual, correo, biblioteca, intranet) y buscador desplegable; portada con titular grande y fila de programas con íconos circulares; franja oscura con los servicios en línea; mensaje de la Dirección; cifras; paneles blancos con carruseles de programas, noticias y plana jerárquica; «¿Por qué estudiar?» en acordeón; franja de plataformas del Estado y pie oscuro. Los bloques siguen el orden y la visibilidad configurados en *Portada*. *Impacto* sigue la estructura de un portal universitario de alto contraste (referencia de diseño: upn.edu.pe), con los datos, el logotipo y las fotos del instituto: logotipo en bloque amarillo, menú en negrita con flechas, botón *Postula*, portada a pantalla completa con indicador de desplazamiento, títulos condensados en mayúsculas, mosaico de programas, recuadros amarillos con «+», franja negra de cifras, tarjetas de noticias y eventos, y pie negro. El amarillo es el dorado del tema activo. *Horizonte* sigue la estructura de un portal de universidad moderna (referencia de diseño: cientifica.edu.pe): logotipo en bloque de color con esquina redondeada, tarjeta translúcida sobre la portada, panel de oferta formativa superpuesto, títulos en dos colores con flecha, cifras grandes, beneficios con pestañas, servicios con pestañas y flechas, noticias sobre franja de color y pie con columnas. En todos los diseños los colores se ajustan en *Temas y apariencia › Colores del diseño* (con combinaciones sugeridas); si no se cambian, siguen el tema activo. El diseño cambia la estructura visual del sitio público; los colores siguen el tema activo.
- **Programas de estudio en carrusel:** se muestran en carrusel por omisión, en la portada y en la página de programas. Para cambiar a cuadrícula, usa *Vista en la web* en el módulo *Programas de estudio* o *Presentación de los programas de estudio* en *Portada › Bloques, accesos y cifras*.
- **Noticias con varias fotos:** cada noticia admite la imagen principal y hasta 4 fotos adicionales (5 en total). En la web se muestran como galería con visor ampliado.
- **Accesos rápidos:** la instalación nueva incluye *Plataforma Virtual* (https://flv.iapp.pe) y *Correo Institucional* (Google Workspace, https://mail.google.com/a/iestpflv.edu.pe). Subir los archivos no cambia los datos ya guardados: en una instalación existente, agrega *Correo institucional* (identificador `correo`) en *Enlaces y servicios* y luego los dos accesos (`ext:aula` y `ext:correo`) en *Portada › Bloques, accesos y cifras*.

El sistema es una copia del portal de Nasca (`ranyuo/claudenasca`, commit `bf8264c`) adaptada con la información pública de https://iestpflv.edu.pe/, consultada el 30 de octubre de 2026. Las imágenes y los documentos se sirven desde `public_html/assets/flv/`, no desde la web original.

## Usar este sistema para otro instituto

El código es el mismo para todos los institutos; lo propio de cada uno está en la base de datos (editable desde la intranet) y en el contenido inicial.

1. **Contenido inicial:** `portal-private/data/portal.json` es el de FLV (personal, noticias, TUPA, enlaces, colores). Reemplázalo por el del nuevo instituto antes de instalar, o instala y edita todo desde la intranet. Las imágenes y los PDF de `public_html/assets/flv/` son de FLV: no los publiques en otro sitio (algunos PDF contienen datos de postulantes); deja solo `sin-foto.jpg` y los archivos del nuevo instituto.
2. **Base de datos propia** y `config.php` propio, con una `install_key` nueva (no copies `portal-private/` de otro sitio: la configuración SMTP guardada está cifrada con esa clave e incluye la cuenta del otro instituto). Opcionalmente, la ruta del archivo de configuración se puede indicar con la variable de entorno `PORTAL_CONFIG_FILE`.
3. Instala como se indica arriba y completa en la intranet:
   - *Configuración*: nombre, nombre corto, sigla del logo, logo, dirección, **distrito y región** (se usan en el pie de la intranet, en los títulos emitidos y en los datos para buscadores), teléfono, correo, redes, SEO y WhatsApp.
   - *Temas y apariencia*: diseño del portal, tema y colores.
   - *Enlaces y servicios*: DRE o GRE de la región (el pie muestra el nombre del enlace `dre`), plataforma virtual, correo y biblioteca.
   - *Mesa de partes*: prefijo de numeración, correo, teléfono, áreas y feriados regionales. Los feriados nacionales del Perú (incluida Semana Santa) se aplican solos cada año.
   - *Libro de reclamaciones*, *Bolsa laboral* y el correo notificador (SMTP).
4. Programa los trabajos cron de `mail-worker.php` (cada minuto) y `cron.php` (diario).

## Datos que debe confirmar la institución

- RUC, código modular, horario de atención y redes sociales.
- Documentos de gestión (PAT, PEI, PCI, RI, MPP, MPA): la web original no tiene enlaces a los archivos.
- Planes de estudio, horarios y horas de EFSRT de cada programa: la web original no tiene enlaces a los archivos.
- Coordinadores de Asistencia Administrativa y Mecatrónica Automotriz: la plana jerárquica y la plana docente indican personas distintas (se usó la plana jerárquica).
- Estadísticas, becas, matrícula e inversiones: las páginas de la web original están vacías.
