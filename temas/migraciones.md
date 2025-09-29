## Orden y claves foráneas

El orden **IMPORTA**, primero se deben crear las tablas padres, que no tienen relaciones, luego las hijas, que se relacionan con las padres y así sucesivamente

## Tipos de migraciones

### Tipo 1: Migraciones de CREACIÓN de tabla

- **`up()`:** Usa `Schema::create('nombre_tabla', ...)` para construir una tabla desde cero.
- **`down()`:** El reverso lógico de "crear una tabla" es "borrar la tabla entera". Por eso, el método `down()` correcto y simple es `Schema::dropIfExists('nombre_tabla');`. Este es el caso de las migraciones que estamos haciendo ahora para tu taller (`create_plans_table`, `create_members_table`, etc.).

### Tipo 2: Migraciones de MODIFICACIÓN de tabla

Esto es lo que el código de tu profesor representa. En el desarrollo real, una tabla puede cambiar con el tiempo. Quizás empiezas con una tabla `posts` simple, y 3 meses después, decides que necesitas añadirle `slug`, `tags`, `softDeletes`, etc.

Para hacer eso, creas una **nueva** migración que *modifica* la tabla existente.

- **`up()`:** Usaría `Schema::table('posts', ...)` para **añadir** las nuevas columnas (`$table->string('slug');`, `$table->json('tags');`, `$table->softDeletes();`).
- **`down()`:** El reverso lógico de "añadir columnas" es "**quitar esas mismas columnas**". NO es borrar la tabla, porque la tabla ya existía antes de esta migración.

## Ejemplo tipo 2

```php
public function down(): void
{
    Schema::table('posts', function (Blueprint $table) {
        // Define la lista de columnas que se AÑADIERON en el método up() de ESTA migración.
        $columns = [
            'slug',
            'published_at',
            'cover_image',
            'meta',
            'tags',
            'deleted_at' // Esta la añade el helper softDeletes()
        ];

        // Itera sobre cada columna a eliminar.
        for ($i = 0; $i < count($columns); $i++) {
            $column = $columns[$i];
            // Esta es una BUENA PRÁCTICA. Antes de intentar borrar,
            // pregunta si la columna realmente existe. Evita errores.
            if (Schema::hasColumn('posts', $column)) {
                // Si existe, la elimina.
                $table->dropColumn($column);
            }
        }

        // ... (lógica similar para eliminar un índice de base de datos)
				if (Schema::hasIndex('posts', ['status', 'published_at', 'unique'])) {
                $table->dropIndex('posts_status_published_at_index');
            }
        
        // Esta línea implica que en el `up()` se cambió el tipo de la columna 'status'.
        // Por ejemplo, de `boolean` a `string`. El `down()` la revierte a `boolean`.
        $table->boolean('status')->change();
    });
}
```

## Tipos de datos

```php
// database/migrations/xxxx_xx_xx_xxxxxx_create_products_table.php
public function up(): void
{
    Schema::create('products', function (Blueprint $table) {

        // --- TIPOS DE ID ---
        $table->id(); // ID numérico autoincremental (1, 2, 3...). El más común.
        $table->uuid('uuid')->unique(); // Crea un ID de tipo UUID (ej: "a7a4f7e4-5b5c-4b1e-9b0a-9b8c7d6e5f4a"). Útil para APIs públicas donde no quieres exponer los IDs numéricos.

        // --- RELACIONES (CLAVES FORÁNEAS) ---
        // foreignId('columna')->constrained('tabla_remota'): Es la forma moderna y recomendada de crear una clave foránea.
        // Automáticamente enlaza 'category_id' con la columna 'id' de la tabla 'categories'.
        $table->foreignId('category_id')->constrained()->cascadeOnDelete(); // ->cascadeOnDelete(): Si se borra la categoría, se borran sus productos. ¡Cuidado al usar!

        // --- TIPOS DE TEXTO ---
        $table->string('name', 150)->unique(); // VARCHAR, ideal para títulos, nombres.
        $table->string('slug', 160)->unique(); // VARCHAR, para la URL amigable que ya explicamos.
        $table->text('description'); // TEXT, para descripciones largas, sin límite de caracteres práctico.
        $table->longText('full_specs')->nullable(); // LONGTEXT, para textos extremadamente largos. ->nullable(): Indica que este campo puede dejarse vacío (NULL).

        // --- TIPOS NUMÉRICOS ---
        $table->integer('stock')->default(0); // INTEGER, para números enteros (positivos o negativos).
        $table->decimal('price', 8, 2); // DECIMAL, la única opción correcta para manejar dinero. 8 dígitos totales, 2 decimales.
        $table->float('weight_kg')->nullable(); // FLOAT, para números con decimales que no requieren alta precisión.

        // --- TIPOS DE FECHA Y HORA ---
        $table->date('launch_date')->nullable(); // DATE, guarda solo la fecha (YYYY-MM-DD).
        $table->dateTime('available_from')->nullable(); // DATETIME, guarda fecha y hora (YYYY-MM-DD HH:MM:SS).
        $table->timestamp('last_ordered_at')->nullable(); // TIMESTAMP, similar a DATETIME pero con algunas diferencias a nivel de base de datos (zona horaria, etc.).

        // --- OTROS TIPOS COMUNES ---
        $table->boolean('is_active')->default(true); // BOOLEAN, guarda un valor verdadero/falso (generalmente como 0 o 1).
        $table->enum('condition', ['new', 'used', 'refurbished']); // ENUM, restringe el valor a una lista predefinida de opciones. Muy útil para estados, tipos, etc.
        $table->json('options')->nullable(); // JSON, para guardar datos estructurados como un array u objeto. Ej: {'color': 'rojo', 'talla': 'M'}.

        // --- ATAJOS DE LARAVEL ---
        $table->timestamps(); // Crea `created_at` y `updated_at`.
        $table->softDeletes(); // Crea la columna `deleted_at` para el "borrado suave". El registro no se borra de la DB, solo se marca como borrado.
    });
}
```