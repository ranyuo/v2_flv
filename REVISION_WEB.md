# Revisión del portal

Revisión de código y de una copia local con PHP 8.4, MariaDB 11.8 y Chromium. No se ha desplegado a producción.

## Cambios

- Las fuentes de Google se cargan sin bloquear el contenido. Se conserva la alternativa sin JavaScript.
- Los cinco scripts principales usan `defer`, manteniendo su orden y ejecutándose antes de `DOMContentLoaded`.
- El carrusel principal respeta la preferencia de reducir movimiento y se pausa cuando la pestaña está oculta.
- Al cambiar de ruta se libera el temporizador del carrusel y el observador de animaciones de la vista anterior.
- Los textos largos del contenido pueden partirse para caber en móviles. La página Campus e Informes excedía el ancho de 390 px hasta 440 px.

## Límites y próximas mejoras

- El CSS y el JavaScript principal suman aproximadamente 1,19 MB sin compresión. Separar los módulos administrativos de la web pública requiere una refactorización y pruebas específicas de sus dependencias globales.
- Hay imágenes originales grandes; el repositorio ya incluye variantes WebP y reglas Apache que las sirven. El servidor PHP local no interpreta `.htaccess`, por lo que sus tamaños de transferencia no representan los de Hostinger.
- No se han comprobado los encabezados, compresión, TLS, entrega de correo, trabajos cron ni configuración de producción.
- La revisión funcional local no equivale a una auditoría completa de seguridad ni a una medición de Core Web Vitals con usuarios reales.

## Validación completada

- 37 rutas públicas del menú en Chromium, a 390 px y 1440 px: contenido renderizado, sin excepciones JavaScript ni desbordamiento horizontal.
- Carrusel: no avanza automáticamente con movimiento reducido; los controles manuales siguen funcionando.
- Intranet: inicio de sesión y renderizado del panel de administrador.
- La portada se renderiza aunque las solicitudes de Google Fonts queden pendientes.
- Siete grupos de pruebas HTTP: portada, API pública, rechazo de solicitudes sin CSRF, login, logout, recursos estáticos y bloqueo del instalador/acceso privado.
- Sintaxis de 25 archivos PHP, scripts JavaScript y revisión de espacios del diff aprobadas.
- La navegación automatizada rápida activó respuestas 429 del contador de visitas (límite existente de 60 por minuto). No se desactivó ni modificó esa protección.
