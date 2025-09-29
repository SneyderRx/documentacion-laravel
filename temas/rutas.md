## Rutas y parámetros

### Rutas (Route Parameter)

Es parte de la estructura de la URL y se usa para identificar un **recurso único**.

- Ejemplo: `GET /api/plans/{identifier}`
- Uso: Cargar **UN** plan específico, ya sea por su ID (`/plans/2`) o su slug (`/plans/pro`).

### Parámetro de consulta (Query Parameter)

Va al final de la URL después de un `?` y se usa para **filtrar, ordenar o paginar** una **colección de recursos**.

- Ejemplo: `GET /api/plans?status=active&sort_by=price`
- Uso: Cargar **UNA LISTA** de planes que cumplan ciertas condiciones.

## Filtros y parámetros

Imagina que quieres poder llamar a estas URLs desde Postman:

- `GET /api/plans` (Devuelve todos los planes)
- `GET /api/plans?is_active=1` (Devuelve solo los planes activos)
- `GET /api/plans?is_active=0&sort_by=price` (Devuelve los inactivos, ordenados por precio)

El método `index` en tu `PlanController.php` se vería así:

```php
use Illuminate\Http\Request; // ¡No olvides importar la clase Request!

// ...

public function index(Request $request)
{
    // 1. Empezamos con una consulta base, igual que en el método show.
    $query = Plan::query();

    // 2. Revisamos si el parámetro de consulta 'is_active' está presente en la URL.
    // $request->has('is_active') comprueba si vino en la URL (?is_active=...)
    if ($request->has('is_active')) {
        // Si está, añadimos un filtro 'where' a nuestra consulta.
        // $request->input('is_active') obtiene el valor del parámetro (ej: "1" o "0").
        $query->where('is_active', $request->input('is_active'));
    }

    // 3. Podemos añadir más filtros. ¿Y si queremos buscar por nombre?
    if ($request->has('name')) {
        // Usamos 'like' y '%' para búsquedas de texto parciales.
        // Si la URL es ?name=pro, buscará cualquier plan que contenga "pro".
        $query->where('name', 'like', '%' . $request->input('name') . '%');
    }
    
    // 4. ¿Y si queremos ordenar los resultados?
    if ($request->has('sort_by')) {
        // Obtenemos la dirección del orden, con 'asc' (ascendente) como valor por defecto.
        $direction = $request->input('sort_dir', 'asc');
        // Ordenamos la consulta por la columna especificada en 'sort_by'.
        $query->orderBy($request->input('sort_by'), $direction);
    }

    // 5. Finalmente, ejecutamos la consulta. Para el reto de PAGINACIÓN,
    // en lugar de ->get(), usamos ->paginate(). ¡Laravel se encarga del resto!
    // Automáticamente revisa si hay un parámetro ?page= en la URL.
    $plans = $query->paginate(10); // Por ejemplo, 10 resultados por página.

    return response()->json($plans);
}
```

¡Y ya está! Has creado un endpoint de listado súper potente y flexible. El patrón es siempre el mismo:

1. Inicia una consulta.
2. Revisa los parámetros de la petición (`$request`).
3. Añade condiciones (`where`, `orderBy`, etc.) a la consulta si los parámetros existen.
4. Ejecuta la consulta final (`get()` o `paginate()`).

# ID y Slug

### 1. Búsqueda con Slug y/o ID en Postman

La respuesta corta es: **Sí, ¡absolutamente!** Puedes diseñar tu API para que acepte ambos, pero no es algo que Laravel haga automáticamente. Tú, como programador, debes indicarle cómo hacerlo.

Aquí tienes el desglose de cómo funciona y cómo lograrlo.

### Opción A: Búsqueda SÓLO por Slug (la más fácil)

Laravel tiene un mecanismo increíble llamado **Route Model Binding**. Por defecto, cuando defines una ruta como esta en `routes/api.php`:

```php
// Laravel asume que {plan} es el ID
Route::get('/plans/{plan}', [PlanController::class, 'show']);
```

Y un método en tu controlador así:

```php
// Laravel busca automáticamente Plan::find($plan) y lo inyecta
public function show(Plan $plan) {
    return response()->json($plan);
}
```

Para cambiar este comportamiento y que busque por `slug` en lugar de `id`, solo tienes que añadir este método a tu modelo `app/Models/Plan.php`:

```php
// app/Models/Plan.php

public function getRouteKeyName()
{
    return 'slug'; // ¡Y ya está!
}
```

**Resultado:** A partir de ahora, para esa ruta, Laravel buscará por la columna `slug`.

- `GET /api/plans/pro` → **Funcionará.**
- `GET /api/plans/2` → **Fallará** (con un error 404 Not Found), porque intentará buscar un plan con el *slug* "2".

### Opción B: Búsqueda por ID o por Slug (¡La solución que buscas!)

Para permitir ambas formas, necesitamos hacer el controlador un poco más inteligente. No podemos usar el "binding" automático, sino que debemos manejar la lógica nosotros mismos.

**Paso 1:** Modifica la ruta en `routes/api.php` para que quede claro que el parámetro puede ser cualquier cosa. Lo llamaremos `identifier`.

```php
// routes/api.php
Route::get('/plans/{identifier}', [PlanController::class, 'show']);
```

**Paso 2:** Modifica el método `show` en tu `PlanController.php`.

```php
// app/Http/Controllers/PlanController.php

public function show($identifier)
{
    // Usamos una consulta base para el modelo Plan
    $query = Plan::query();

    // Ahora, detectamos si el identificador que nos pasaron es numérico (un ID) o no (un slug)
    if (is_numeric($identifier)) {
        // Si es un número, buscamos en la columna 'id'
        $query->where('id', $identifier);
    } else {
        // Si no es un número, buscamos en la columna 'slug'
        $query->where('slug', $identifier);
    }

    // Ejecutamos la consulta. firstOrFail() es genial:
    // - Devuelve el primer resultado que encuentra.
    // - Si no encuentra NADA, automáticamente lanza un error 404 Not Found.
    // ¡Perfecto para una API!
    $plan = $query->firstOrFail();

    return response()->json($plan);
}
```

**Resultado:** ¡Ahora tu endpoint es súper flexible!

- `GET /api/plans/pro` → **Funcionará.**
- `GET /api/plans/2` → **Funcionará.**