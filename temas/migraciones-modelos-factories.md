## Relaciones

- **La Migración (`create_posts_table.php`): El Arquitecto de la Base de Datos.**
    - **Su ÚNICA Misión:** Definir la estructura de la tabla en la base de datos. Es como el plano de una casa. Dice cuántas columnas (habitaciones) hay, cómo se llaman (`title`, `content`) y de qué tipo son (`string`, `text`, `boolean`).
    - **La Ley:** Si una columna no está definida aquí, **físicamente no existe** en la base de datos. Punto.
- **El Modelo (`Post.php`): El Gerente de Operaciones.**
    - **Su Misión:** Interactuar con la tabla que el "Arquitecto" (la migración) ya construyó. Sabe cómo leer, escribir y actualizar registros en esa tabla.
    - **Sus Herramientas:**
        - `$fillable`: Es una "lista de permisos". Le dice a Laravel: "Cuando alguien intente crear un post con muchos datos a la vez (asignación masiva), solo voy a prestar atención y permitir que se guarden estos campos: `title`, `slug`, `content`, etc.". Es una medida de seguridad.
        - `$casts`: Es un "traductor". Le dice a Laravel: "Cuando leas la columna `tags` de la base de datos, que está en formato de texto JSON, conviértela a un `array` de PHP para que yo pueda usarla fácilmente. Y viceversa al guardar".
    - **Su Confianza Ciega:** El Modelo *asume* que las columnas que mencionas en `$fillable` y `$casts` ya existen en la base de datos porque confía en que la migración hizo su trabajo.
- **El Factory (`PostFactory.php`): El Generador de Datos de Prueba.**
    - **Su Misión:** Crear datos falsos (pero con una estructura realista) para llenar la base de datos durante las pruebas o el sembrado inicial (`seeding`).
    - **Su Proceso:** El Factory crea una instancia del Modelo (`Post`) y le asigna los datos que tú defines. Luego, el Modelo intenta guardar esa información en la base de datos.