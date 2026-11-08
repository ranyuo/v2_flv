# Pruebas de regresión

Requisitos: PHP 8.1+, `pdo_mysql` y una instancia MySQL/MariaDB de pruebas. No ejecutarlas con la configuración de producción.

1. Crea dos bases **vacías** con el mismo prefijo: por ejemplo `flv_qa_test` y `flv_qa_restore_test`.
2. Prepara un archivo PHP privado fuera del repositorio que devuelva la configuración de `portal-private/config.example.php`, con `db_name => 'flv_qa_test'`, acceso a ambas bases, una `install_key` aleatoria de al menos 32 caracteres y `smtp => ['enabled' => false]`.
3. Ejecuta desde la raíz del checkout:

   ```sh
   PORTAL_TEST_CONFIG_FILE=/ruta/privada/qa-config.php php tests/regressions.php
   ```

El programa rechaza nombres de base que no terminan en `_test` y bases con tablas existentes. No elimina ni vacía bases automáticamente. Para repetirlo, prepara dos bases vacías de nuevo.

Comprueba idempotencia y rollback de adjuntos, cuotas compartidas con subidas multipart, validación y unicidad normalizada de notas, periodos históricos y restauración real del respaldo SQL con cola vacía y adjuntos conservados. Los datos son sintéticos y no se envía correo.
