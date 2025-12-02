
Estás trabajando en el repositorio QPlay (https://github.com/sasogu/qplay).

Objetivo general:
Modificar QPlay para que el campo **“URL Imagen”** del CSV acepte también imágenes embebidas en Base64 mediante data URLs del tipo:

data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA...

Mantén SIEMPRE compatibilidad con el comportamiento actual (URLs normales http/https deben seguir funcionando igual).

Tareas concretas (hazlas paso a paso, modificando el código que encuentres en este repo local):

1. LOCALIZA DÓNDE SE CARGA EL CSV Y SE MAPEAN LAS COLUMNAS
   - Abre los archivos relevantes, empezando por:
     - `index.js`
     - `editor.html`
     - `jugador.html`
     - y cualquier otro JS vinculado a la carga del CSV o al modelo de pregunta.
   - Encuentra el código donde:
     - Se lee el CSV (por ejemplo con `FileReader`).
     - Se parsean las líneas en un objeto pregunta con propiedades como Tipo, Pregunta, R1, R2, R3, R4, Tiempo, Correcta, URL Imagen (o nombres equivalentes).
   - Identifica la propiedad que representa la URL de la imagen (por ejemplo `urlImagen`, `imageUrl` o similar).

2. REVISAR CÓMO SE USA LA URL DE IMAGEN EN LA INTERFAZ
   - Localiza el código donde se crea / actualiza el `<img>` de la pregunta, tanto:
     - En la pantalla del presentador (donde se ve la pregunta con imagen).
     - Como en la pantalla del jugador, si aplica.
   - Normalmente será algo como:
     - `imgPregunta.src = pregunta.urlImagen;`
     - o similar.
   - Verifica si hay alguna lógica que:
     - Bloquee rutas que no empiecen por “http”,
     - o quite espacios,
     - o ignore ciertos formatos.

3. PERMITIR DATA URL EN BASE64 EN EL CAMPO “URL Imagen”
   - Asegúrate de que la aplicación acepte **dos tipos de valores** en la columna “URL Imagen”:
     1. URLs clásicas: `http://...` o `https://...`
     2. Data URLs en Base64: `data:image/png;base64,...`, `data:image/jpeg;base64,...`, etc.
   - Implementa la lógica de forma robusta:
     - Si el campo “URL Imagen” está vacío o solo contiene espacios, no muestres imagen (comportamiento actual).
     - Si empieza por `"data:image"` (o `"data:"` seguido de `image/`), úsalo directamente como `src` del `<img>`:
       - `img.src = valorCSV;`
     - Si empieza por `"http://"` o `"https://"` o cualquier otra cosa no vacía, trata el valor igual que hasta ahora (mantener compatibilidad).
   - No hagas ninguna validación restrictiva que impida usar data URLs.
   - Si existe algún tipo de sanitización, adáptala para que permita data URLs de imagen de forma segura (por ejemplo, comprobando que comienza por `data:image/`).

4. ACTUALIZAR EL EDITOR (editor.html) SI ES NECESARIO
   - Si el editor permite introducir el valor del campo “URL Imagen” en un `<input>`:
     - Asegúrate de que no haya validaciones que exijan `http` o una URL absoluta.
     - Permite pegar directamente una cadena larga de Base64.
   - Si se muestra una previsualización de la imagen en el editor:
     - Usa la misma lógica: si el valor empieza por `data:image`, úsalo directamente como `src`.

5. ACTUALIZAR LOS EJEMPLOS Y/O DOCUMENTACIÓN
   - Abre `ejemplo.csv` y añade una o dos filas de ejemplo que muestren el uso de data URLs en el campo “URL Imagen”.
     - Puedes utilizar un Base64 muy corto (una imagen mínima o un placeholder generado).
     - El objetivo es que quede claro el formato, por ejemplo:
       - `data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAAB...`
   - Si hay alguna referencia en `README.md` o en la interfaz que explique el formato del CSV:
     - Añade una breve nota indicando que el campo “URL Imagen” acepta:
       - URLs clásicas (http/https).
       - Y también data URLs en Base64 que empiecen por `data:image/...;base64,`.

6. PRUEBAS
   - Añade, al menos, estas pruebas manuales (pueden ser notas en comentarios o pequeños bloques de código de test):
     1. Cargar un CSV con una pregunta que tenga imagen por URL normal (`https://...`): debe funcionar como siempre.
     2. Cargar un CSV con una pregunta que tenga una imagen en data URL Base64: la imagen debe mostrarse correctamente.
     3. Cargar un CSV con el campo “URL Imagen” vacío: no debe dar error y no debe aparecer imagen.
   - Si existe algún mensaje de error en pantalla, evita romper el flujo actual:
     - Cualquier comportamiento nuevo debe ser compatible con los cuestionarios ya existentes.

7. ESTILO DE CÓDIGO
   - Respeta el estilo actual del proyecto:
     - Nombres de variables en el mismo idioma (español/inglés) que el resto.
     - Mismas convenciones de comillas, indentación, etc.
   - Añade comentarios breves explicando el soporte de data URLs, por ejemplo:
     - `// Soporte para imágenes embebidas en Base64 mediante data URLs (data:image/...).`

Resultado esperado:
- Tras tus cambios, QPlay debe poder tomar el valor de la columna “URL Imagen” del CSV y:
  - Si es una URL clásica → mostrar la imagen como antes.
  - Si es una data URL Base64 (`data:image/...;base64,...`) → mostrar la imagen embebida.
  - Si está vacío → no mostrar imagen, sin errores.

Por favor, realiza las modificaciones necesarias en los archivos que correspondan (principalmente `index.js`, `editor.html`, `jugador.html` u otros JavaScript implicados) y muéstrame el código final actualizado.

