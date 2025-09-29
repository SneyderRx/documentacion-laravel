## Tabla de Rutas `apiResource`

Esta tabla desglosa todas las rutas que la instrucción `Route::apiResource('posts', PostController::class);` genera automáticamente para ti.

| Verbo HTTP | URI (/api/...) | Acción (Método del Controlador) | Nombre de la Ruta (Route Name) | Propósito |
| --- | --- | --- | --- | --- |
| GET | /posts | index | posts.index | Listar todos los posts (paginados). |
| POST | /posts | store | posts.store | Guardar un nuevo post. |
| GET | /posts/{post} | show | posts.show | Mostrar un post específico por su ID. |
| PUT/PATCH | /posts/{post} | update | posts.update | Actualizar un post específico por su ID. |
| DELETE | /posts/{post} | destroy | posts.destroy | Eliminar un post específico por su ID. |

## Tabla de Operaciones CRUD y Convenciones de Laravel

Esta tabla te ayuda a conectar el concepto de una operación CRUD con el verbo HTTP y el método del controlador que se usa por convención en Laravel.

| Operación CRUD | Verbo HTTP | Método del Controlador | Descripción de la Acción |
| --- | --- | --- | --- |
| Create | POST | store | Recibe datos y crea un nuevo recurso. |
| Read (all) | GET | index | Devuelve una lista de todos los recursos. |
| Read (one) | GET | show | Devuelve un único recurso específico. |
| Update | PUT/PATCH | update | Recibe datos y actualiza un recurso existente. |
| Delete | DELETE | destroy | Elimina un recurso existente. |

## Tabla de Métodos Comunes de Eloquent para Consultas

Aquí comparamos algunos de los métodos más comunes que usarás con tus modelos de Eloquent para obtener datos de la base de datos.

| Método Eloquent | ¿Qué Devuelve? | ¿Cuándo Usarlo? |
| --- | --- | --- |
| ::all() | Una Colección con todos los registros. | Para conjuntos de datos muy pequeños. (Evítalo en APIs). |
| ::get() | Una Colección con los registros que coinciden. | Cuando necesitas una lista de resultados de una consulta compleja. |
| ::find($id) | Un único Modelo o null si no lo encuentra. | Cuando buscas un registro por su clave primaria (ID). |
| ::first() | El primer Modelo que coincide o null. | Cuando solo necesitas un resultado de una consulta, no importa cuál. |
| ::paginate($n) | Un Paginador (objeto con resultados y meta-datos). | Ideal para APIs. Devuelve resultados en páginas para no sobrecargar el servidor. |
| ::create($array) | El Modelo recién creado y guardado. | Para crear y guardar un nuevo registro en un solo paso. |
| ::updateOrCreate() | El Modelo encontrado y actualizado o recién creado. | Para actualizar un registro si existe, o crearlo si no. |

## Tabla Ampliada de Métodos Comunes de Eloquent

| Método Eloquent | ¿Qué Devuelve? | Ejemplo de Uso | Punto Clave / Buena Práctica |
| --- | --- | --- | --- |
| --- Métodos de Obtención Final --- |  |  |  |
| ->get() | Colección de Modelos | Post::where('status', true)->get(); | Es el ejecutador final de una consulta. Úsalo después de encadenar condiciones como where. |
| ::all() | Colección de Modelos | Post::all(); | Devuelve todo sin filtros. Es rápido para datos pequeños, pero peligroso en producción. Prefiere get(). |
| ::find($id) | Un Modelo o null | Post::find(1); | Búsqueda súper optimizada por clave primaria. Perfecto para el método show de un controlador. |
| ::findOrFail($id) | Un Modelo o Error 404 | Post::findOrFail(1); | Igual que find, pero si no encuentra el modelo, lanza automáticamente una excepción que resulta en una página/respuesta 404. ¡Mucho más limpio para APIs! |
| ->first() | Un Modelo o null | Post::where('title', 'Mi Primer Post')->first(); | Obtiene solo el primer resultado que coincida con tu consulta. Muy eficiente cuando sabes que solo necesitas uno. |
| ::paginate($n) | Objeto LengthAwarePaginator | Post::where('status', true)->paginate(15); | La mejor opción para listas en APIs. Devuelve los resultados en bloques (páginas) e incluye metadatos (total, página actual, etc.). |
| --- Métodos para Construir Consultas (Encadenables) --- |  |  |  |
| ::where($col, $val) | Constructor de Consultas | Post::where('status', true)->where('user_id', 5); | El filtro más común. Puedes encadenar múltiples where para crear consultas AND. |
| ->orderBy($col, $dir) | Constructor de Consultas | Post::orderBy('created_at', 'desc')->get(); | Ordena los resultados. 'desc' es descendente (del más nuevo al más viejo), 'asc' es ascendente. |
| ->latest() | Constructor de Consultas | Post::latest()->get(); | Es un atajo para orderBy('created_at', 'desc'). Muy común para mostrar lo más reciente primero. |
| ->oldest() | Constructor de Consultas | Post::oldest()->paginate(10); | Es un atajo para orderBy('created_at', 'asc'). |
| --- Métodos de Creación y Actualización --- |  |  |  |
| ::create($array) | El Modelo creado | Post::create(['title' => 'Nuevo', 'content' => '...']); | Crea y guarda un nuevo registro en un solo paso. Requiere que los campos estén en la propiedad $fillable del modelo por seguridad. |
| ->update($array) | true o false | $post = Post::find(1); $post->update(['status' => false]); | Actualiza un modelo que ya has recuperado de la base de datos. También respeta la propiedad $fillable. |
| ::updateOrCreate() | El Modelo actualizado/creado | Post::updateOrCreate(['title' => 'Referencia'], ['status' => true]); | Híbrido: busca por el primer array, si lo encuentra, lo actualiza con el segundo. Si no, crea un nuevo registro con la unión de ambos arrays. |
| --- Métodos de Agregación y Conteo --- |  |  |  |
| ->count() | Número (entero) | Post::where('status', true)->count(); | Muy eficiente para solo contar filas. No recupera los datos de los modelos, solo devuelve el número total. |
| ->sum($col) | Número | User::where('active', true)->sum('revenue'); | Suma los valores de una columna específica para los resultados de la consulta. |
| --- Carga de Relaciones (Eager Loading) --- |  |  |  |
| ::with('relacion') | Constructor de Consultas | Post::with('user', 'comments')->get(); | Soluciona el problema N+1. Precarga las relaciones para evitar hacer cientos de consultas a la base de datos. ¡Fundamental para el rendimiento! |