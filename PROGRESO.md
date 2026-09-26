# Progreso del catálogo de joyas

## 26/09/2026

### Qué construimos
- Un catálogo web de joyas en un solo archivo `index.html`.
- Tarjetas con foto, nombre, precio y estado (disponible / vendida / reservada).
- Los datos de las joyas están en una lista (array) de JavaScript, y las tarjetas se generan solas recorriendo esa lista con `forEach`.
- Un filtro "solo disponibles" con un checkbox estilo interruptor.
- Un modal (lightbox) para ver la joya en grande al hacer clic en su tarjeta.
- Diseño profesional y minimalista: fuentes de Google (Playfair + Poppins), efecto hover en las tarjetas, y diseño responsive para que se vea bien en el celular.

### Conceptos que aprendí

**HTML**
- Estructura de tarjetas (repetir el mismo bloque para cada joya).
- Diferencia entre `class` (para un grupo de elementos que comparten estilo) e `id` (para un elemento único).

**CSS**
- Cómo aplicar colores usando clases.
- Diferencia entre `background-color` (fondo) y `color` (texto).
- La sintaxis `propiedad: valor;` y que CSS es sensible a mayúsculas/minúsculas.
- `hover` para efectos al pasar el mouse, y `transition` para que esos cambios sean suaves.
- `flexbox` para acomodar elementos en fila/columna.
- Diseño responsive (que se adapta a pantallas chicas como el celular).

**JavaScript**
- Arrays y objetos (cómo guardar los datos de cada joya).
- `forEach` para recorrer la lista y generar las tarjetas automáticamente.
- `filter` (y que por dentro hace un bucle) para mostrar solo las joyas disponibles.
- Template literals (los `` `texto ${variable}` ``) para armar HTML con datos.
- `addEventListener` para reaccionar a clics y otros eventos.

### Depuración (bugs que encontré y corregí)
- Un error por escribir `:` en vez de `;` en CSS.
- Un typo en el nombre de una carpeta que rompía la ruta de un archivo.

### Próximos pasos pendientes
- Agregar un botón de WhatsApp en cada tarjeta.
- Subir las fotos reales de las joyas.
- Llevar el catálogo a producción con GitHub Pages.

---

**Dónde quedamos:** el catálogo funciona completo en local (tarjetas, filtro y modal), con diseño responsive terminado. La próxima sesión arrancamos agregando el botón de WhatsApp en las tarjetas.

## 26/09/2026 — Versión 2 del catálogo

### Cambios de hoy
- Saqué los precios de las tarjetas y del modal (ya no se muestran en ningún lado).
- Agregué categorías a los datos: cada joya ahora tiene también `material` y `tipo`.
- Armé la navegación en dos niveles: primero se elige el tipo (anillo, cadena, arito, pulsera, dije) y después el material (plata, oro, perlas). El catálogo se filtra combinando ambos.
- Los botones de tipo se muestran en plural (Anillos, Cadenas, Aritos...) usando un objeto traductor llamado `etiquetasTipo`, pero el dato guardado en el array sigue en singular (`anillo`, `cadena`, etc.) — el traductor solo cambia lo que se ve, no el dato.
- Unifiqué el tipo `"collar"` como `"cadena"`, para que todas las joyas de ese tipo usen siempre la misma palabra (antes había una inconsistencia entre el nombre del tipo y cómo se llamaba en la vida real).

### Conceptos que aprendí hoy
- **Filtros en cadena:** se puede aplicar `filter` varias veces seguidas sobre la misma lista (primero por tipo, después por material, después por disponible) y cada uno reduce un poco más el resultado. Así se combinan varios criterios sin repetir lógica.
- **Dato vs. cómo se muestra:** el valor que se guarda (singular, ej. `"anillo"`) puede ser distinto del texto que ve la clienta (plural, ej. "Anillos"). Un objeto traductor conecta uno con el otro sin mezclar ambas cosas.
- **Consistencia de datos:** conviene que el mismo concepto se escriba siempre igual en todo el código (por eso unifiqué `"collar"` → `"cadena"`), para no tener que andar traduciendo o comparando cosas que en realidad son lo mismo.
- **`let` vs `const`:** `let` se usa para variables que van a cambiar de valor (como el tipo o material que la clienta va eligiendo), y `const` para las que no cambian.
- **`push` como operación de pila:** agregar un elemento al final de un array con `push` es la misma idea que "apilar" un elemento, algo que ya había visto en la facultad con pilas.

### Decisiones tomadas
- Las joyas vendidas se van a **borrar** del catálogo (la dueña ya lleva el registro de ventas en una libreta aparte, no hace falta guardarlas acá).
- Después de esta versión, voy a pasar al **Camino 3**: conectar el catálogo con Google Sheets, para que la dueña pueda cargar y borrar joyas ella sola, sin tocar código.

### Próximo paso
- Crear la planilla en Google Sheets con las columnas: `nombre`, `material`, `tipo`, `estado`, `imagen`.
- Cargar ahí las joyas de prueba que hoy están en el array de JavaScript.
- Después: publicar la planilla y programar la web para que lea los datos desde ahí en vez del array fijo.

---

**Retomar acá: crear la planilla de Google Sheets.**

## 26/09/2026 — Versión 3: catálogo conectado a Google Sheets

### Cambios de hoy
- La planilla de Google Sheets ya está creada y publicada como CSV.
- El catálogo ya no tiene las joyas escritas a mano en el código: ahora las trae con `fetch()` desde el link del CSV publicado.
- Mientras llegan los datos, se muestra el mensaje "Cargando joyas..." y si algo falla (sin internet, planilla despublicada, etc.) se muestra "No se pudieron cargar las joyas.".
- Escribí una función propia (`csvAJoyas`) para convertir el texto del CSV en el mismo tipo de array de objetos que usaba antes, así el resto del código (`renderJoyas`, los filtros de tipo/material/disponibles) no tuvo que cambiar.

### Conceptos que aprendí hoy

**`fetch` y promesas**
- `fetch(url)` hace un pedido HTTP y devuelve una *promesa* (una especie de "recibo" de que la respuesta va a llegar más adelante, no de inmediato).
- Se encadena con `.then()` para decir "cuando llegue la respuesta, hacé esto", y con `.catch()` para decir "si algo sale mal, hacé esto otro".
- `respuesta.text()` también devuelve una promesa (leer el cuerpo de la respuesta lleva un instante), por eso hay dos `.then()` seguidos: uno para obtener el texto y otro para procesarlo.
- Como el fetch tarda, todo el código que arma los botones y las tarjetas (`renderBotonesTipo`, `actualizarCatalogo`) se llama *adentro* del segundo `.then()`, no al final del script como antes. Si no fuera así, se ejecutaría antes de que llegaran los datos.

**Convertir CSV en objetos**
- Un CSV es texto plano: una fila por línea, columnas separadas por comas. La primera línea son los encabezados (`nombre,material,tipo,estado,imagen`).
- `dividirLineaCSV` recorre una línea caracter por caracter y corta por cada coma, salvo que esté "dentro de comillas" (por si algún dato tuviera una coma adentro, cosa que Google Sheets hace poniendo comillas).
- `csvAJoyas` separa el texto en líneas, usa la primera como nombres de propiedades, y por cada línea siguiente arma un objeto emparejando cada encabezado con su valor en esa posición (por eso las columnas de la planilla tienen que llamarse igual que antes: `nombre`, `material`, `tipo`, `estado`, `imagen`).

**Async por naturaleza vs. datos fijos**
- Antes `tipos` y `materiales` se calculaban una sola vez al principio porque `joyas` ya existía. Ahora se calculan recién cuando el fetch trae los datos, así que pasaron de `const` a `let` y se recalculan dentro del `.then()`.

### Nota sobre las fotos
- La columna `imagen` de la planilla sigue guardando un número (no una URL), igual que en la versión anterior con el array fijo. Por eso las tarjetas siguen usando ese número contra picsum.photos como foto de relleno. Cuando quieran subir fotos reales, van a tener que cambiar esa columna para que tenga el link de cada foto, y ahí sí habrá que ajustar el `src` de la imagen para que use `joya.imagen` directamente como URL.

### Próximos pasos pendientes
- Subir fotos reales de las joyas y decidir dónde alojarlas (¿link directo?, ¿Google Drive?, ¿otro servicio de imágenes?).
- Agregar un botón de WhatsApp en cada tarjeta.
- Llevar el catálogo a producción con GitHub Pages.

---

**Dónde quedamos:** el catálogo lee las joyas desde Google Sheets (fetch + parseo de CSV), con mensajes de carga y error. La próxima sesión: decidir cómo van a alojar las fotos reales.
