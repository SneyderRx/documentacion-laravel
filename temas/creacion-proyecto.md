# Instalar PHP, Composer y Laravel

[Installation - Laravel 12.x - The PHP Framework For Web Artisans](https://laravel.com/docs/12.x)

## Instalación

Si se desea instalar PHP en la computadora

- Para Windows en PowerShell como administrador

```powershell
# Run as administrator...
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://php.new/install/windows/8.4'))
```

- Para Linux

```bash
/bin/bash -c "$(curl -fsSL https://php.new/install/linux/8.4)"
```

## Creación de proyecto

- Comando con composer con PowerShell o en la consola de VSCode

```powershell
# Para crear dentro de una carpeta (se crea otra carpeta)
composer create-project laravel/laravel:^11.6.1 {nombre-proyecto}
cd {nombre-proyecto}

# Para crear los archivos dentro de esa carpeta
composer create-project laravel/laravel:^11.6.1 .

# Crear/copiar y configurar el archivo docker-compose.yml
environment:
      MYSQL_ROOT_PASSWORD: admin
      MYSQL_DATABASE: laravel
      MYSQL_USER: laravel
      MYSQL_PASSWORD: admin

# Levntar los servicios de Docker
docker compose up -d

# Si cometió un error, bajar el contenedor
docker-compose down

# Copiar .env si no existe y configurar
cp .env.example .env

# Instalar soporte API en Laravel 11 si no existe
php artisan install:api

# Ejecutra el servidor (si solo usa los servicios en Docker)
php artisan serve

# Ejecutar el servidor (si contruye el app en Docker)
php artisan serve --host=0.0.0.0 --port=8000

# Verificar la base de datos
php artisan migrate
php artisan tinker
> DB::connection()->getPdo();
```