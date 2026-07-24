# Una biblioteca para ti

Biblioteca virtual interactiva. La estantería es un mundo 3D voxel construido con [three.js](https://threejs.org/); la lectura (pasar páginas, sonidos, contenido) es exactamente la misma mecánica de siempre, solo que ahora arrancas desde una escena en 3D en vez de una estantería plana.

## Cómo abrirla

Necesita servirse con un servidor local (no `file://` directo) porque usa `fetch()` para leer `books/books.json`, y necesita internet la primera vez que carga porque trae **three.js** y **StPageFlip** desde un CDN.

```bash
python3 -m http.server 8000
# o
npx serve .
```

Abre `http://localhost:8000`.

## La estantería en 3D

- Estructura, libros, planta, lámpara y suelo son voxels (cajas) construidos por código en `js/voxel-scene.js` a partir de `books.json` — igual que antes, agregar un libro nuevo al JSON hace que aparezca solo, ahora como un libro voxel en la estantería.
- **Cámara fija**: no rota. Solo se puede arrastrar (mouse o dedo) para desplazarla en X y en Y, con suavizado (easing). No hay zoom ni rotación libre.
- Tocar/hacer clic en un libro lo hace sobresalir levemente y, después de esa animación, se abre exactamente con la misma experiencia de lectura que ya tenías (mismo `book.js`, mismo StPageFlip, mismos sonidos, mismo `descripcion` en la portada).
- Ambientación: partículas de polvo flotando, una lámpara con parpadeo suave y una planta que se mece — todo con movimientos lentos.
- Sin sombras dinámicas (por rendimiento en Safari/iPhone y Chrome/Android); la calidez viene de las luces cálidas y la niebla de fondo, no de shadow maps.

Si el navegador no soporta WebGL, aparece un mensaje de respaldo en vez de una pantalla rota.

## Contador de lectura (con memoria)

- Cada vez que terminas de leer un libro (llegas a la última página) se guarda en `localStorage`, así que el contador no se reinicia al recargar la página ni al cerrar el navegador.
- El contador ("X de Y libros leídos") aparece siempre visible arriba, sobre la escena 3D.
- Cuando terminas todos los libros normales, se agrega automáticamente un estante nuevo con un **libro secreto** (definido en `books.json` bajo `"libroSecreto"`) — ahí puedes dejar unas últimas palabras.
- El progreso vive solo en ese navegador/dispositivo (no hay backend), así que si la persona abre el link en otro celular, el contador empieza de nuevo ahí.

## Estructura del proyecto

```
/css         -> styles.css (todo el diseño visual)
/js          -> data.js, voxel-scene.js, book.js, progress.js, ornaments.js, main.js
/books       -> books.json (todo el contenido, incluyendo el libro secreto)
/audio       -> pull.wav, open.wav, page.wav, close.wav, ambient.wav
```

## Cómo agregar un libro nuevo

Igual que siempre: agrega un objeto dentro de `"libros"` en `books/books.json` (ver el ejemplo dentro del archivo). No hace falta tocar ningún `.js`.

## Cómo editar el libro secreto

Está en `books/books.json`, en el campo `"libroSecreto"` (fuera del arreglo `"libros"`). Cambia su `titulo`, `autor`, `color` y sobre todo el `contenido` de la página `"mensaje_final"` por tus propias palabras finales.

## Próximos pasos sugeridos

- **Más estantes/filas**: `voxel-scene.js` ya agrupa los libros de a 4 por fila automáticamente; agregar libros al JSON simplemente crea más filas (más recorrido en Y).
- **Categorías o buscador**: filtrar `data.libros` antes de pasarlo a `createVoxelLibrary` en `main.js`.
- **Portadas con imagen**: se puede aplicar una textura (`THREE.TextureLoader`) al material del libro en `voxel-scene.js` en vez de un color plano.
