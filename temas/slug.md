## ¿Qué es un slug?

Imagina que tienes un blog y escribes un artículo con el título: **"Las 10 Mejores Salas de Reunión en 2025"**.

Si usas el ID del artículo en la URL, se vería así:
`https://micoworking.com/articulos/123`

Pero si usas un "slug", se vería así:
`https://micoworking.com/articulos/las-10-mejores-salas-de-reunion-en-2025`

**Un slug es, simplemente, una versión de un texto (como un título) formateada para ser amigable y legible en una URL.**

Generalmente, esto implica:

- Convertir todo a minúsculas.
- Reemplazar los espacios con guiones ().
- Eliminar caracteres especiales (acentos, signos de interrogación, etc.).

**¿Por qué es útil y se usa tanto en Laravel y otros frameworks?**

1. **SEO (Optimización para Motores de Búsqueda):** Google y otros buscadores leen las URLs para entender de qué trata una página. La segunda URL, con el slug, contiene palabras clave (`salas`, `reunión`, `2025`), lo que ayuda a que tu página se posicione mejor en los resultados de búsqueda.
2. **Usabilidad y Claridad:** Es mucho más fácil para un usuario leer, recordar y compartir la URL con el slug. A simple vista, ya sabe de qué trata el enlace.
3. **Identificador Único Legible:** En lugar de buscar un `Plan` por su `id` (que es solo un número), podrías buscarlo por su slug `pro`, `basic`, `enterprise`.

En Laravel, es muy fácil generar slugs a partir de un string usando el "helper" de la clase `Str` (String).

```php
use Illuminate\Support\Str;

$titulo = "Las 10 Mejores Salas de Reunión en 2025";
$slug = Str::slug($titulo);

// $slug ahora contiene: "las-10-mejores-salas-de-reunion-en-2025"
```

La buena práctica es generar y guardar el slug automáticamente cuando creas o actualizas un registro. Por ejemplo, cuando creas una `Room` con el nombre "Sala de Juntas Principal", guardas automáticamente el slug `sala-de-juntas-principal`.