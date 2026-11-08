# Revisión de estabilidad de la aplicación base

Fecha: 8 de noviembre de 2026. Alcance: `ranyuo/v2_flv`, antes de trasladar cambios a otras aplicaciones.

## Dictamen

Se corrigieron los defectos reproducidos y se dejaron pruebas repetibles y un flujo de integración continua. Esto refuerza la base de código, pero todavía **no autoriza por sí solo su replicación a producción en otras instituciones**: la versión debe pasar una instalación piloto en el hosting real, con SMTP, cron, archivos y respaldo/restauración de ese hosting comprobados. Las pruebas locales y la revisión de la versión pública no son equivalentes a haber desplegado y validado esta versión.

No se modificaron contenidos, cuentas, notas ni configuración de producción. Los inicios de sesión y las lecturas de producción pueden generar sus registros normales de auditoría. Todas las pruebas de escritura emplearon instalaciones locales desechables, datos sintéticos y correo retenido en un receptor local.

## Correcciones de esta revisión

| Área | Problema reproducido y corrección |
|---|---|
| Identidad de datos | El servidor aceptaba alumnos con el mismo ID y clases sin ID. Se exigen identidades válidas y únicas, incluidos los catálogos y perfiles académicos. |
| Datos académicos | Se aceptaban ciclos, secciones, turnos, periodos y días inexistentes. El servidor aplica los valores reales de la interfaz y rechaza tipos JSON incompatibles. |
| Carga docente | Se podía repetir la carga de una misma persona y guardar días de disponibilidad no válidos. Se valida la unicidad y la disponibilidad declarada. |
| Documentos emitidos | Un cliente de la API podía cambiar titular y referencia de un documento ya emitido. Su contenido queda protegido; se permite anular y se impide reactivar una emisión anulada. Los códigos tienen identidad estable y única. |
| Permisos | Había acciones de cuenta y de perfil propio que podían seguir autorizadas aunque se retirara el módulo del rol. La comprobación se aplica en el servidor, también al tipo concreto de documento. Un rol ausente falla cerrado. |
| Formularios | JSON con colecciones inválidas podía provocar errores internos o corromper formas esperadas por la interfaz. Se rechaza antes de sincronizar catálogos o persistir archivos. |
| Reclamos y archivos | Se unificó MIME/extensión entre las vías de archivo y se hizo coherente el límite anunciado de evidencia PDF/JPG/PNG de 10 MiB, tanto en navegador como en servidor. Se probó una evidencia real de 6 MiB. |
| Bibliotecas PDF | Se actualizaron jsPDF y AutoTable a 4.2.1 y 5.0.9, conservando las licencias. Se verificaron tarballs oficiales por SHA-512 y distribuciones locales por SHA-256. Los PDF generados fueron abiertos por un analizador y se extrajo su texto. |
| Regresión continua | Se añadieron suites con bases vacías, receptor SMTP STARTTLS local y pruebas de volumen, navegador y dependencias. GitHub Actions queda configurado para PHP 8.1 y 8.3. |

Los cambios de la revisión anterior —idempotencia y cuotas de adjuntos, respaldo SQL restaurable y notas sin duplicados— siguen incluidos en las pruebas.

## Evidencia automatizada

Las suites de backend se ejecutaron con MariaDB 10.11 y PHP 8.1 y 8.3. Cada suite parte de bases vacías y contiene también las 23 comprobaciones de regresión iniciales; **no se deben sumar esos 23 casos repetidos como pruebas diferentes**.

- `tests/stability.php`: instalación, validación, identidades, roles, aislamiento por programa, documentos históricos, sesiones, respaldos portables y cron. Comprueba también la combinación de ediciones de filas independientes y el conflicto 409 cuando dos editores cambian la misma fila.
- `tests/mail-regressions.php`: entrega con STARTTLS y certificado verificado, rechazo del destinatario, reintento programado y manual, cancelación por origen eliminado, cifrado de configuración, conservación de contraseña oculta y rechazo de configuración alterada o clave incorrecta. No se envía correo externo.
- `tests/load-smoke.php`: 401 alumnos y 2.502 notas, con conservación de registros y actualización individual. Se observaron aproximadamente 1–2 segundos y 24–26 MiB en el escenario local anterior; no es una prueba de concurrencia ni un benchmark de Hostinger.
- `tests/browser-smoke.cjs`: guardado real desde el editor, publicación, ocultamiento por fecha futura, retirada, PDF y XLSX, importación CSV UTF-8 con comillas y rechazo de duplicados.
- `tests/public-forms.cjs`: Mesa de Partes con subida multipart y descarga real del cargo PDF; reclamo con evidencia de 6 MiB y código de consulta.
- Navegación adicional: 43 rutas públicas y 46 rutas administrativas a 390 y 1440 px, 178 recorridos sin excepciones JavaScript ni desbordamiento horizontal. La navegación rápida del portal activó el límite del contador de visitas (429); se mantuvo la protección y las páginas siguieron renderizando.
- Revisión de sintaxis de PHP y JavaScript, integridad de bibliotecas y `git diff --check`.

Se analizaron los PDF descargados con `pdfinfo` y `pdftotext`: estructura válida, A4 y contenido de la institución y los registros sintéticos. Esto no sustituye una revisión institucional de los textos, firmas, sellos y requisitos normativos de cada documento.

## Comprobación de los datos públicos actuales

Se consultó la API autenticada de producción sin guardar registros: 411 alumnos, 2.505 notas y cinco clases. Las comprobaciones realizadas no detectaron IDs académicos repetidos, grupos de alumnos fuera de los valores examinados, días de clase inválidos ni códigos de documentos repetidos. No se descargó la base de producción ni se ejecutó allí la batería de escritura.

## Reproducción

Ver [tests/README.md](tests/README.md). `bash tests/run.sh` prepara y elimina su propia red y contenedores de prueba. Las pruebas de navegador requieren una instalación local desechable y la cuenta sintética generada por la suite; nunca se les debe proporcionar una URL de producción.

Las consultas de avisos npm dependen de la red y del estado del catálogo en el momento de la consulta. La respuesta actual no devolvió avisos para las versiones declaradas. El chequeo no cubre todo el código PHP, todos los posibles ataques ni vulnerabilidades futuras.

## Criterios pendientes antes de replicar

1. Desplegar esta versión en una copia piloto del hosting, preservando configuración y contenido propios de cada institución.
2. Verificar PHP/extensiones, límites efectivos de subida de hasta 40 MiB, tiempo/memoria, permisos de carpetas y ejecución de cron en ese hosting.
3. Verificar el SMTP real y recibir mensajes de trámite, reclamo y bolsa en buzones de prueba autorizados; probar una interrupción y el reintento del cron.
4. Crear y restaurar un respaldo en una base de prueba del hosting, comprobar sus adjuntos y volver a iniciar sesión. Conservar una copia externa antes de actualizar una instalación existente.
5. Validar textos institucionales, datos de contacto, destinatarios, firmas y documentos emitidos. El contenido inicial es de FLV y no debe publicarse sin adaptar en otra institución.
6. Comprobar los resultados reales de GitHub Actions después del push y repetir las pruebas al cambiar código o dependencias.

No se certifica una disponibilidad del 100 %, una auditoría exhaustiva de seguridad, accesibilidad normativa completa o comportamiento bajo carga real. Tampoco se ha aplicado esta versión a las demás aplicaciones.
