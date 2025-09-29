# Tablas de comandos

## Comandos Más Útiles y de Uso Diario

| Comando | Descripción |
| --- | --- |
| `php artisan serve` | Inicia un servidor de desarrollo local en `http://127.0.0.1:8000`. |
| `php artisan make:model [nombre]` | Crea un nuevo modelo Eloquent. Puedes añadir flags como `-m` para crear también la migración, `-c` para el controlador y `-f` para el factory. |
| `php artisan make:controller [nombre]` | Crea un nuevo controlador. Puedes usar `--resource` para generar un controlador con los métodos CRUD básicos. |
| `php artisan make:migration [nombre]` | Crea un nuevo archivo de migración para la base de datos. |
| `php artisan migrate` | Ejecuta las migraciones pendientes para actualizar el esquema de tu base de datos. |
| `php artisan migrate:fresh` | Elimina todas las tablas de la base de datos y luego ejecuta todas las migraciones desde cero. Es muy útil durante el desarrollo. |
| `php artisan migrate:rollback` | Revierte la última migración que se ejecutó. |
| `php artisan migrate:status` | Muestra el estado de cada migración (si se ha ejecutado o no). |
| `php artisan db:seed` | Ejecuta los *seeders* para poblar tu base de datos con datos de prueba. |
| `php artisan make:seeder [nombre]` | Crea una nueva clase de *seeder*. |
| `php artisan tinker` | Inicia una consola interactiva (REPL) para interactuar con tu aplicación Laravel, permitiéndote ejecutar código PHP y probar modelos y servicios directamente. |
| `php artisan route:list` | Muestra una tabla con todas las rutas registradas en tu aplicación. |
| `php artisan config:cache` | Crea un archivo de caché para la configuración, lo que mejora el rendimiento en producción. |
| `php artisan view:cache` | Compila todas las vistas de Blade en archivos PHP planos para mejorar el rendimiento. |
| `php artisan optimize` | Almacena en caché la configuración y las rutas para un mejor rendimiento. |
| `php artisan storage:link` | Crea un enlace simbólico desde `public/storage` a `storage/app/public`, permitiendo que los archivos almacenados sean accesibles públicamente. |

## Comandos para Generación de Código (Scaffolding)

| Comando | Descripción |
| --- | --- |
| `php artisan make:request [nombre]` | Crea una nueva clase de *Form Request* para la validación. |
| `php artisan make:middleware [nombre]` | Crea una nueva clase de *middleware*. |
| `php artisan make:factory [nombre]` | Crea un nuevo *model factory* para generar datos de prueba. |
| `php artisan make:policy [nombre]` | Crea una nueva clase de *policy* para la autorización. |
| `php artisan make:provider [nombre]` | Crea un nuevo *service provider*. |
| `php artisan make:test [nombre]` | Crea una nueva clase de prueba. Puedes usar el flag `--unit` para tests unitarios. |
| `php artisan make:job [nombre]` | Crea una nueva clase de *job* para procesar tareas en segundo plano. |
| `php artisan make:event [nombre]` | Crea una nueva clase de *event*. |
| `php artisan make:listener [nombre]` | Crea una nueva clase de *listener* para un evento. |
| `php artisan make:notification [nombre]` | Crea una nueva clase de notificación. |
| `php artisan make:component [nombre]` | Crea una nueva clase de componente de Blade. |

## Comandos de Mantenimiento y Depuración

| Comando | Descripción |
| --- | --- |
| `php artisan down` | Pone la aplicación en modo de mantenimiento. Los visitantes verán una página de error 503. |
| `php artisan up` | Saca la aplicación del modo de mantenimiento. |
| `php artisan cache:clear` | Borra la caché de la aplicación. |
| `php artisan config:clear` | Elimina el archivo de caché de configuración. |
| `php artisan route:clear` | Elimina el archivo de caché de rutas. |
| `php artisan view:clear` | Borra todos los archivos de vistas compiladas. |
| `php artisan schedule:run` | Ejecuta las tareas programadas (debes configurar un cron job que llame a este comando cada minuto). |
| `php artisan queue:work` | Inicia un *worker* para procesar los jobs de la cola. |
| `php artisan queue:failed` | Lista todos los jobs de la cola que han fallado. |
| `php artisan queue:retry [id]` | Reintenta la ejecución de un job que ha fallado. |

## Comandos Menos Comunes pero Útiles

| Comando | Descripción |
| --- | --- |
| `php artisan key:generate` | Genera una nueva clave de aplicación (`APP_KEY`) en tu archivo `.env`. |
| `php artisan about` | Muestra información detallada sobre el entorno de tu aplicación (versión de Laravel, PHP, drivers de caché, etc.). |
| `php artisan db:wipe` | Elimina todas las tablas, vistas y tipos de la base de datos. |
| `php artisan event:generate` | Genera los eventos y listeners que faltan a partir del registro en `EventServiceProvider`. |
| `php artisan model:prune` | Elimina los registros de modelos que ya no son necesarios (debes definir el método `prunable` en el modelo). |
| `php artisan package:discover` | Vuelve a descubrir los proveedores de servicios y fachadas de los paquetes instalados. |
| `php artisan session:table` | Crea una migración para la tabla de sesiones en la base de datos. |
| `php artisan vendor:publish` | Publica los recursos de un paquete (como archivos de configuración o vistas) en el directorio de tu aplicación para poder personalizarlos. |