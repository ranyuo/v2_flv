# QA del despliegue — 8 de noviembre de 2026

Sitio comprobado: https://flv2.aplicacion.io/. Versión de referencia: `7e1c902` de `main`. El usuario autorizó registrar pruebas funcionales en la aplicación desplegada y omitir las comprobaciones bloqueadas por permisos o acceso.

## Resultado

Pasaron las 36 comprobaciones funcionales de servidor ejecutadas. Se crearon **12 registros sintéticos QA**, más una copia manual, y se comprobaron formularios, consultas, documentos, permisos y recuperación de los respaldos descargados. No se reprodujo un nuevo defecto de código que requiriera parche en esta ejecución.

Se detectaron pendientes de configuración: SMTP está desactivado y sin contraseña guardada; los respaldos automáticos están desactivados; no están definidos los correos responsables de Reclamos y Bolsa Laboral. Se omitieron el envío real y la ejecución del cron del hosting. No se modificó la configuración SMTP ni se sustituyeron los destinatarios institucionales.

## Registros creados

Todos se identificaron como **QA TÉCNICO / PRUEBA AUTORIZADA**, con datos sintéticos. No deben tratarse como gestiones, estudiantes o documentos reales.

| N.º | Módulo | Registro / estado final |
|---|---|---|
| 1 | Mesa de Partes | `MPV-IESTPFLV-2026-000001`, con PDF de prueba y cargo descargado. |
| 2 | Libro de Reclamaciones | `RCL-2026-00001`, identificado como prueba técnica, con evidencia PDF de 6 MiB. |
| 3 | Contacto | Mensaje técnico QA, asunto «Otros», registrado en la intranet. |
| 4 | Alumnos | Alumno sintético «QA TECNICO AUTORIZADO - ALUMNO SINTETICO». |
| 5 | Noticias | Borrador técnico QA; se confirmó que no aparece en la API pública. |
| 6 | Notas | Nota 15 del alumno QA, periodo 2026-II y unidad sintética. |
| 7 | Módulos formativos | `QA-DEP-20261108`. |
| 8 | Unidades didácticas | `QA-DEP-UD-20261108`, vinculada al módulo y programa correctos. |
| 9 | Ambientes | `QA-DEP-AULA`. |
| 10 | Horarios | Clase QA, lunes de 08:00 a 09:00; grupo QA publicado. |
| 11 | Certificación modular | `QA-DEP-CERT-20261108-001`; se emitió, consultó y **anuló** al terminar la prueba. |
| 12 | Usuarios | Perfil Directivo temporal QA; se comprobó el cambio de contraseña y **se desactivó** la cuenta al terminar. |

Los registros permanecen para su revisión en la intranet. Los códigos privados de verificación, las contraseñas, la dirección del destinatario de pruebas y las copias de datos no se publican en este documento.

## Comprobación punto por punto

| Área | Prueba | Resultado |
|---|---|---|
| Despliegue | Comparación SHA-256 de diez recursos públicos, incluidos scripts, CSS y las dos bibliotecas PDF. | Coinciden con `main`. No se dispone del hash de los PHP remotos. |
| Acceso | Inicio de sesión con administrador y navegación de sus módulos. | Correcto. |
| Navegación | 43 rutas públicas y 43 administrativas a 390 y 1440 px: 172 recorridos. | Sin excepciones JavaScript, respuestas HTTP fallidas del sitio ni desbordamiento horizontal detectado. |
| Formularios ciudadanos | Registro desde los formularios reales de Mesa, Reclamos y Contacto. | Correcto; los tres registros aparecen en la intranet. |
| Consultas | Trámite y reclamo con documento correcto e incorrecto. | Consulta correcta 200; documento incorrecto 404. |
| Adjuntos | Subida multipart, PDF de 6 MiB, acceso privado y hash del archivo descargado. | Correcto; otro visitante recibe 403 y el administrador descarga bytes con el SHA-256 esperado. |
| PDF | Cargo y listado de alumnos generados en el navegador del sitio desplegado. | Archivos A4 válidos, analizados con `pdfinfo` y extracción de texto con `pdftotext`. |
| Excel | Exportación real de alumnos y lectura del libro descargado. | Libro válido; conserva el alumno QA y los ceros iniciales de su documento. |
| Publicación | Guardado de noticia en borrador y proyección anónima. | El borrador no se publica. No se publicó una noticia QA en la portada. |
| Validación académica | Ciclo inexistente, periodo de nota inválido y nota duplicada. | Se rechazan con 422. La nota válida se conserva. |
| Catálogos | Módulo, unidad, aula y clase con referencias reales. | Se guardan correctamente. |
| Horarios | Publicación del grupo QA e intento de insertar una clase superpuesta. | Publicación correcta; solapamiento rechazado con 422. |
| Certificados | Emisión, consulta pública por código, intento de alterar el número y anulación. | Emisión y consulta correctas; cambio del número rechazado; consulta posterior muestra «Anulado». |
| Contraseña temporal | Acceso del nuevo usuario Directivo e intento de guardar antes de cambiar la clave. | Escritura rechazada con 403; cambio obligatorio de contraseña correcto. |
| Permisos | Directivo intenta consultar SMTP y modificar un alumno. | Ambas operaciones se rechazan con 403. |
| Revocación | Desactivar el usuario QA y reutilizar su sesión. | La sesión deja de estar autenticada. |
| Privacidad | Consultar la API sin sesión después de las pruebas. | Usuarios, expedientes, reclamos, mensajes y certificados privados siguen excluidos. |
| Respaldo portable | Crear y descargar una copia manual del hosting; validar gzip, final, conteos y SHA-256. | Correcto. |
| Respaldo SQL | Descargar el SQL del hosting y revisar la estructura de correo. | Incluye `pisco_mail` y excluye los mensajes pendientes de la restauración. |
| Recuperación | Importar el SQL y restaurar el portable descargados en una base local aislada. | Ocho comprobaciones correctas: instalación, administrador, servicio de correo, cola vacía, archivos, hashes y cierre de sesión. Se eliminó esa base de prueba después. |

La restauración se ejecutó en aislamiento; **no se revirtió ni reemplazó la base activa del hosting**. Las copias descargadas contienen datos privados y permanecen fuera del repositorio, con permisos restringidos.

## Pruebas omitidas y pendientes

- **Entrega SMTP y recepción en buzón:** omitidas porque el envío está desactivado y no hay contraseña SMTP configurada. Se observan tres notificaciones de las pruebas en estado «Pendiente»; dos corresponden al destinatario autorizado. No se afirma que se hayan enviado o recibido.
- **Cron de correo y respaldos en hPanel:** no se dispone de acceso al panel ni a la ejecución del proceso del hosting. Los respaldos programados están desactivados.
- **Restauración sobre la base activa:** omitida para conservar el estado actual. Se probó la recuperación de las copias reales en una base aislada.
- **Tamaño máximo de 40 MiB, carga concurrente sostenida y todas las combinaciones de importación/documentos:** no se forzaron en esta ejecución sobre el hosting. La evidencia de estas pruebas no constituye una garantía de funcionamiento del 100 %.

## Cambios en GitHub

En esta ejecución se incorpora el informe de QA. No se modifica la lógica de la aplicación: los casos ejecutados no reprodujeron un nuevo error de código. Los pendientes de SMTP, destinatarios y cron deben completarse en la configuración segura del hosting antes de considerar validada la operación completa o replicar la aplicación a otras instituciones.
