# Correcciones de la revisión QA — 8 de noviembre de 2026

- **Adjuntos:** el servidor comprueba la idempotencia antes de guardar archivos nuevos. Los archivos base64 y el registro se confirman en la misma transacción; un formulario rechazado no deja archivos nuevos. Las subidas base64 comparten las cuotas de archivos y MB con multipart. Referenciar un archivo ya subido no vuelve a cobrar la cuota.
- **Respaldo SQL:** incluye la estructura de `pisco_mail`. Se restaura con la cola vacía, como el formato portable, para evitar reenvíos de avisos de la instalación anterior.
- **Notas:** se exige un periodo `20xx-I` o `20xx-II` y una unidad didáctica válida; se rechazan duplicados por alumno, curso normalizado y periodo. Es posible corregir una nota existente y registrar el mismo curso en otro periodo. La detección utiliza un índice por clave de negocio. Los datos históricos no se eliminan automáticamente.
- **Diseños:** se incorporó Editorial, existente en los archivos públicos de producción, con su CSS y validación de servidor. Se retiró la conversión automática Editorial → Moderno. Se mantienen los controles de colores de Clásico, Moderno y Universitario y el aviso de becas sin periodos que estaban en Git.

## Validación

- 23 comprobaciones de regresión en dos bases vacías independientes, incluida la importación real del SQL y el uso del servicio de correo restaurado.
- 99 comprobaciones funcionales locales de los flujos y perfiles revisados.
- Ocho diseños a 390 y 1440 px, sin excepciones JavaScript ni desbordamiento horizontal.
- Revisión de sintaxis PHP y JavaScript y `git diff --check`.

Las instrucciones de las pruebas reutilizables están en [tests/README.md](tests/README.md). Se usaron datos sintéticos y SMTP desactivado. Se restauró la base local inicial después de la regresión funcional.

Estos cambios están preparados en el checkout. No se han desplegado ni se ha sincronizado el sitio publicado. Antes de desplegar, conviene volver a comparar los archivos de producción si hubo cambios posteriores a la revisión.
