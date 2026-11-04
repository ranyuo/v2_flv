# Mejoras de los módulos incorporadas desde v2_palpa

Fecha: 4 de noviembre de 2026. Se llevaron a este portal las mejoras de los módulos de la intranet de `ranyuo/v2_palpa` (12 commits del 3 y 4 de noviembre). Se adaptaron a la estructura de FLV:

- La web pública, sus cuatro diseños y la administración de contenidos se mantienen sin cambios.
- Las tablas siguen siendo `pisco_*`.
- El cambio obligatorio de contraseña temporal sigue usando el mecanismo propio de FLV.

## Qué cambia

| Módulo | Mejora |
| --- | --- |
| Configuración académica general (`ix-config`) | Reúne en pestañas los programas de estudios, los módulos formativos (con horas totales y tipo de horas), las unidades didácticas (vinculadas a su módulo), las asignaciones docentes, los ambientes, los turnos, bloques y recesos, el periodo académico y los formatos documentales. Horarios y Certificación modular usan estos datos maestros. |
| Certificación modular y Grados y títulos | Estado del documento (Válido/Anulado), institución emisora, horas y denominación. Los datos emitidos son históricos: para corregirlos se anula el documento y se registra una nueva emisión. El registro de verificación se sincroniza en el servidor (`AcademicDocuments.php`). |
| Constancias / Certificados | Registro de constancias y certificados de cursos y actividades, con alumno opcional, horas e institución. |
| Consulta de certificados | La búsqueda por DNI exige la verificación anti-spam del servidor; la búsqueda por código o QR no la exige. |
| Mesa de partes, Libro de reclamaciones, Bolsa laboral y Certificados | Cabecera compacta y formularios a todo el ancho. El envío muestra su progreso, no bloquea la página y se puede reintentar sin duplicar el registro. |
| Enlaces propios de los servicios | `/mesa-de-partes.php`, `/libro-de-reclamaciones.php`, `/bolsa-laboral.php` y `/certificados.php` abren cada servicio en un marco propio, sin el menú del portal. Sirven para enlazarlos desde otras webs. En el portal (`/`) los servicios se ven igual que siempre. |
| Expedientes | Código y número de dígitos del correlativo configurables en *Mesa de partes › Configuración*, con protección para que la secuencia no se repita (`Numbering.php`). |
| Notificaciones por correo | Avisos SMTP de nuevos trámites, reclamos, empresas, ofertas y postulaciones de Bolsa laboral, derivaciones y cambios de estado. La cola `pisco_mail` hace reintentos. El primer intento se envía después de responder al usuario; el cron `mail-worker.php` reintenta los pendientes. Al derivar o cambiar de estado, el aviso al ciudadano es opcional. |
| Configuración (administrador) | Formulario SMTP, con la contraseña cifrada en `portal-private/.smtp-settings.json` mediante `install_key`, y tabla de estado de las notificaciones con botón *Reintentar*. |
| Seguridad y QA | Al anular un reclamo queda en estado Anulado y ya no admite cambios. Los accesos denegados se registran en la auditoría. Las claves temporales son aleatorias y la nueva clave debe ser distinta de la actual. Un estudiante solo ve las sesiones de horario del periodo vigente. |

## Activar el correo

1. En la intranet, entra con la cuenta administradora y abre *Configuración › Configurar correo notificador (SMTP)*. *Usar Gmail / Google Workspace* completa el servidor, el puerto y el cifrado. Indica la cuenta, el remitente y una **contraseña de aplicación**, no la clave personal de la cuenta. Marca *Activar envío* y guarda.
2. Revisa los correos receptores: Mesa de partes (*Mesa de partes › Configuración*), Libro de reclamaciones (*Configuración*) y Bolsa laboral (configuración del módulo).
3. En hPanel › *Avanzado › Trabajos cron*, programa **cada minuto**:
   `php /home/<usuario>/domains/<dominio>/portal-private/mail-worker.php`
4. En *Configuración › Estado de las notificaciones por correo* comprueba que los avisos pasen a *Enviado*. *Enviado* significa que el servidor de correo aceptó el mensaje; revisa también la carpeta de spam.

Como alternativa al formulario, el bloque `smtp` de `config.php` (ver `config.example.php`) acepta la misma configuración. Lo guardado en el formulario tiene prioridad.

## Actualizar una instalación existente

Sube los archivos como de costumbre (ver README). La primera visita aplica la actualización de la base, del esquema 5 al 7: crea `pisco_mail` y da al Responsable académico permiso de edición en Configuración académica y a Dirección permiso de consulta. Esto se hace una sola vez y está protegido contra peticiones simultáneas.

## Verificación realizada

Sobre una copia local con MariaDB 10.11 y PHP 8.3:

- Se instaló la versión anterior de FLV y luego se abrió el sitio con esta versión. La base pasó del esquema 5 al 7 y se conservaron los datos.
- Pruebas en el navegador (Chromium), sin errores de JavaScript:
  - portada pública;
  - Mesa de partes en el portal y en `/mesa-de-partes.php`;
  - registro real de un trámite con su cargo `MPV-IESTPFLV-2026-000001`;
  - consulta de certificados por DNI;
  - los módulos de administración: Configuración, Configuración académica general, Constancias, Mesa de partes, Reclamos, Certificación modular, Horarios y Bolsa laboral;
  - guardado de la configuración SMTP.
- Envío real a un servidor SMTP local con STARTTLS: el cargo para el ciudadano y el aviso a Mesa de partes quedaron en estado *Enviado*.
- Una consulta por DNI sin verificación se rechaza con 422.

Las pruebas automáticas de v2_palpa no se copiaron porque dependen del modo «solo intranet» y de las tablas `intranet_*`.
