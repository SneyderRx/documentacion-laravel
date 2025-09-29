## Paginación

```php
// En lugar de: Post::get()
return $this->ok("Todo ok...", Post::paginate(15)); // Devuelve 15 por página
```

## Creación de Modelo completo

```powershell
php artisan make:model nombreModelo -a
```

## Tabla de Visibilidad (public, protected, private)

Esta tabla resume quién puede acceder a las propiedades y métodos de una clase según su visibilidad.

| Visibilidad | ¿Se accede en la misma clase? | ¿Se accede en una clase hija (heredada)? | ¿Se accede desde fuera (a través de un objeto)? |
| --- | --- | --- | --- |
| public | ✅ Sí | ✅ Sí | ✅ Sí |
| protected | ✅ Sí | ✅ Sí | ❌ No |
| private | ✅ Sí | ❌ No | ❌ No |