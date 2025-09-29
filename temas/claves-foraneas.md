## 3 formas de crear claves foráneas

Las tres maneras principales de crear relaciones de claves foráneas en Laravel, desde las más simples hasta las más avanzadas, son:

1. **En una sola migración:** La forma más directa de definir una clave foránea usando la sintaxis fluida de Laravel.
2. **En migraciones separadas:** Una forma más robusta de manejar dependencias, previniendo errores de orden.
3. **Con una tabla y modelo pivote:** El método más avanzado, usado para relaciones de muchos a muchos que tienen datos adicionales.

---

### 1. En una sola migración

Este es el método más simple y el más común. Se usa cuando la tabla a la que haces referencia ya ha sido creada en una migración anterior. La sintaxis `foreignId` de Laravel es una forma compacta de hacerlo.

### **Ejemplo (`create_posts_table.php`):**

Supongamos que ya tienes una tabla `users`. La migración para la tabla `posts` podría usar la columna `user_id` para hacer referencia a `users`.

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->foreignId('user_id')->constrained(); // <-- Aquí se crea la clave foránea
            $table->string('title');
            $table->text('body');
            $table->timestamps();
        });
    }
};
```

---

### 2. En migraciones separadas

Este método es ideal para evitar errores de orden, especialmente en proyectos grandes. Te permite crear primero todas las tablas y luego, en una migración posterior, definir las claves foráneas.

### **Ejemplo (`add_foreign_keys_to_posts_table.php`):**

Primero, la migración de la tabla `posts` se crea sin la clave foránea. Luego, creas una nueva migración solo para añadir la clave foránea.

```php
// Migración 1 (anterior): 'create_posts_table.php'
Schema::create('posts', function (Blueprint $table) {
    $table->id();
    $table->unsignedBigInteger('user_id'); // Solo la columna, sin relación
    // ...
});

// Migración 2 (posterior): 'add_foreign_key_to_posts_table.php'
// Creada con 'php artisan make:migration add_foreign_key_to_posts_table'
Schema::table('posts', function (Blueprint $table) {
    $table->foreign('user_id')
          ->references('id')
          ->on('users');
});
```

---

### 3. Con una tabla y modelo pivote

Este es un método avanzado para manejar relaciones de **muchos a muchos** cuando necesitas guardar datos adicionales en la tabla intermedia. En este caso, se crea un modelo específico para la tabla pivote.

### **Ejemplo (`create_category_post_table.php`):**

Supongamos que tienes una tabla `posts` y una tabla `categories`, y la relación `many-to-many` tiene una columna extra llamada `active`.

**Paso 1: La migración de la tabla pivote**

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('category_post', function (Blueprint $table) {
            $table->foreignId('category_id')->constrained()->onDelete('cascade');
            $table->foreignId('post_id')->constrained()->onDelete('cascade');
            $table->boolean('active')->default(true); // <-- Columna adicional
            $table->timestamps();
        });
    }
};
```

**Paso 2: La relación en el modelo `Category`**

El modelo `Category` ahora usa el método `belongsToMany` con `using` y `withPivot` para referenciar la tabla pivote y sus columnas.

```php
// En app/Models/Category.php
use App\Models\Post;

public function posts()
{
    return $this->belongsToMany(Post::class, 'category_post')
                ->using(CategoryPost::class) // Modelo pivote
                ->withPivot('active')       // Columna extra
                ->withTimestamps();         // Columnas de tiempo
}
```

## Cuadro Comparativo

| Característica | 1. En una sola migración | 2. En migraciones separadas | 3. Con tabla y modelo pivote |
| --- | --- | --- | --- |
| Uso Principal | Relaciones de uno a uno o uno a muchos. | Relaciones de uno a uno o uno a muchos, para evitar errores de orden. | Relaciones de muchos a muchos con datos adicionales. |
| Complejidad | Baja | Media | Alta |
| Archivos requeridos | 1 migración | 2 o más migraciones | 1 migración de tabla pivote y 1 modelo pivote |
| Ventajas | Rápido, simple, y sintaxis elegante (constrained()). | Previene errores de dependencias de tablas, más robusto en grandes proyectos. | Permite almacenar datos en la relación (precio, estado), dando más control. |
| Desventajas | Puede fallar si la tabla referenciada no existe. | Requiere un paso de migración adicional que puede ser redundante. | Exige más código y configuración; es excesivo para relaciones simples. |
| Comando Artisan | make:migration ... | make:migration ... (dos veces) | make:model --pivot, make:migration ... |