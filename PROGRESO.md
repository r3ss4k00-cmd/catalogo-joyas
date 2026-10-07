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

## 26/09/2026 — Sesión 2

### Logros
- Conecté el catálogo a Google Sheets: ahora las joyas se leen desde una planilla publicada en CSV, no del código.
- La dueña puede agregar o borrar joyas editando directamente la planilla, sin tocar código.
- Agregué la columna `id` en la planilla (pensada para el futuro panel de administración).
- Agregué un fondo con una imagen delicada: un velo claro semitransparente superpuesto a la imagen, hecho con `linear-gradient`. Saqué `background-attachment: fixed` porque en iPhone no se veía (Safari no lo respeta bien).
- Validación de datos: una joya solo aparece en el catálogo si tiene `nombre` **y** `imagen` (usando `&&`). Las filas incompletas del CSV se ignoran en vez de mostrar una tarjeta rota.

### Conceptos que aprendí
- **`fetch` y promesas (`.then`):** cómo pedirle datos a otro servidor que tardan en llegar, sin bloquear el resto de la página mientras se espera.
- **CORS:** por qué el catálogo no cargaba los datos al abrir `index.html` directo desde el disco (`file://`), pero sí funciona servido por `https` (como en GitHub Pages). El navegador bloquea pedidos entre orígenes distintos salvo que el servidor lo permita explícitamente, y `file://` no cuenta como un origen válido para eso.
- **IDs únicos:** un id tiene que ser permanente (no cambiar nunca para la misma joya), puede tener huecos en la numeración (si se borra una joya, ese número no se reutiliza), y nunca se reasigna a otra fila. Es la misma lógica que usan las bases de datos para identificar filas.
- **Validación de entrada:** nunca hay que confiar en que los datos externos (como una planilla editada a mano) vienen completos. Siempre conviene chequear antes de usarlos.
- **Truthy/falsy y operadores `&&` (Y) / `||` (O):** en JavaScript un string vacío `""` se evalúa como falso y uno con contenido como verdadero. `&&` exige que ambos lados sean verdaderos para que la condición completa lo sea; `||` alcanza con que uno solo lo sea.
- **Ocultar no es proteger:** los datos de un CSV publicado son públicos y cualquiera con el link los puede ver, aunque no se muestren en la interfaz. Por eso lo seguro es borrar la joya de la planilla, no solo "esconderla" con un filtro.

### Próximos pasos pendientes
- **Fotos reales (lo más importante):** la dueña las saca con el celular, así que hay que resolver cómo darles una URL (subirlas a algún hosting) y ajustar el código para que `joya.imagen` se use como link directo en vez de picsum.photos.
- Compartir la planilla con la dueña con permiso de Editor.
- Cargar las joyas reales en la planilla.
- Opcional a futuro: botón de WhatsApp en las tarjetas, panel administrable con login y base de datos (Camino 2).

---

**Dónde quedamos:** catálogo online, funcional y robusto ante datos incompletos. Próximo paso: resolver el tema de las fotos del celular (hosting + URLs).

## 26/09/2026 — Rediseño estilo "cálido boutique"

### Cambios de hoy
- Rediseño visual completo (solo CSS, ninguna función se tocó): paleta cálida con variables de color (`:root`) en tonos crema, terracota y dorado apagado, en vez de colores sueltos repetidos por todo el archivo.
- Tarjetas con más aire (más `gap`, más `padding`), esquinas más redondeadas, sombra con tinte marrón cálido en vez de negro puro, y zoom suave de la imagen al pasar el mouse.
- Botones de categoría con degradé terracota→dorado cuando están activos, en vez de un marrón plano.
- Etiquetas de estado (disponible/vendida/reservada) con colores más suaves, manteniendo el mismo significado.
- Modal con una pequeña animación de aparición (escala + fade) usando `transition` en CSS, sin tocar el JavaScript que lo abre/cierra.
- Título con una línea decorativa fina debajo (hecha con `::after`, sin imágenes) para dar sensación de marca.

### Conceptos que aprendí
- **Variables CSS (`:root` y `var()`):** definir los colores una sola vez con un nombre (ej. `--terracota`) y reutilizarlos en todo el archivo. Si mañana quiero cambiar el tono principal, edito un solo lugar en vez de buscar el color por todo el CSS.
- **Separar estética de funcionalidad:** todo el rediseño fue solo en la etiqueta `<style>`; el `<script>` con la lógica (fetch, filtros, modal) no se tocó, porque el diseño y el comportamiento son cosas independientes.
- **Pseudo-elementos (`::after`):** se puede agregar un elemento visual (como la línea decorativa bajo el título) sin agregar una etiqueta HTML nueva, solo con CSS.
- **Transiciones en estados (`.activo`):** la animación del modal no es JavaScript animando nada; es CSS diciendo "cuando tengas la clase `.activo`, cambiá de escala 0.94 a 1 con una transición de 0.25s", y el JavaScript solo pone/saca esa clase.

### Próximos pasos pendientes
- Fotos reales de las joyas (hosting + URLs).
- Botón de WhatsApp en cada tarjeta.
- Llevar el catálogo a producción con GitHub Pages.

---

**Dónde quedamos:** catálogo con diseño boutique (cálido, elegante, con más aire y detalles cuidados), funcionalidad intacta. Próximo paso: resolver el tema de las fotos del celular.

## 26/09/2026 — Botón de WhatsApp en el modal

### Cambios de hoy
- Agregué un botón "Consultar por WhatsApp" **solo dentro del modal** (las tarjetas del catálogo siguen sin botón, como se pidió).
- Al tocarlo, abre WhatsApp en una pestaña nueva hacia un número fijo, con un mensaje ya escrito que incluye el nombre de la joya que está abierta.
- Estilo del botón coherente con la paleta boutique (terracota oscuro, no el verde típico de WhatsApp) para que no desentone.

### Cómo el botón sabe qué joya está abierta
- El link (`<a id="enlaceWhatsapp">`) arranca en el HTML con un `href="#"` que no sirve para nada todavía — es solo un lugar en la página.
- Cada vez que se abre una joya, se ejecuta `abrirModal(joya)`, que ya recibía los datos de esa joya (así es como siempre completó la imagen y el nombre del modal).
- Adentro de esa misma función, ahora armo el mensaje con un template literal: `` `¡Hola! Me interesa esta joya: ${joya.nombre}, ¿me pasás más info?` `` — igual que ya se hacía para meter `joya.imagen` en la URL de la foto.
- Ese mensaje se mete en la URL de WhatsApp (`https://wa.me/NUMERO?text=...`) pasado por `encodeURIComponent()`. Esta función es necesaria porque una URL no puede tener espacios, acentos ni signos como `¿`/`¡` sueltos — los convierte en código seguro para URL (por ejemplo el espacio pasa a `%20`).
- Por último, actualizo el `href` real del link: `enlaceWhatsapp.href = ...`. Como esto pasa *cada vez* que se abre una joya distinta, el botón siempre apunta a la joya que está viendo la clienta en ese momento — no hace falta un botón por joya, alcanza con uno solo que se actualiza dinámicamente.

### Conceptos que aprendí
- **Actualizar un atributo desde JavaScript:** un link no tiene que tener su destino fijo en el HTML; se puede cambiar en cualquier momento con `elemento.href = "..."`, igual que ya hacía con `.src` para la imagen del modal.
- **`encodeURIComponent`:** sirve para meter texto "libre" (con espacios, tildes, signos) dentro de una URL sin romperla.
- **Reutilizar un patrón que ya conocía:** el truco de guardar la joya actual y usar sus datos dentro de `abrirModal` ya lo venía haciendo para la imagen y el nombre; el botón de WhatsApp usa exactamente la misma idea.

### Próximos pasos pendientes
- Fotos reales de las joyas (hosting + URLs).
- Llevar el catálogo a producción con GitHub Pages.

---

**Dónde quedamos:** catálogo con diseño boutique + botón de WhatsApp funcional en el modal. Próximo paso: resolver el tema de las fotos del celular.

## 26/09/2026 — Fotos reales (ImgBB) en vez de picsum

### Cambios de hoy
- Ahora la columna `imagen` de la planilla trae un link real (de ImgBB) en vez de un número, y el catálogo usa ese link directo (`joya.imagen`) como `src` de la foto, tanto en la tarjeta como en el modal. Se sacó `picsum.photos` de los dos lugares.
- Antes de usar el link, se lo "limpia" con una función `normalizarUrlImagen`:
  - Le saca espacios de más al principio/final con `.trim()` (por si al pegar el link en la planilla quedó un espacio colado).
  - Si empieza con `http://` (sin la "s"), lo cambia a `https://`, para que el navegador no lo bloquee por contenido inseguro.
- Las fotos en las tarjetas se ven todas parejas (mismo tamaño, encuadradas) gracias a `object-fit: cover`, que ya estaba puesto — no hizo falta CSS nuevo ahí; ahora simplemente funciona con fotos de tamaños reales en vez de las cuadradas de picsum.
- Si un link de foto no carga (rota, borrada de ImgBB, etc.), en vez de mostrar el ícono de "imagen rota" del navegador, se reemplaza automáticamente por un cuadro con fondo beige y el texto "Sin imagen" — hecho con una imagen SVG generada en el momento, no con una foto externa.
- Se agregó `loading="lazy"` a las fotos de las tarjetas: el navegador solo las descarga cuando están por entrar en pantalla, así el catálogo abre más rápido si hay muchas joyas.
- La validación sigue igual: una joya solo se muestra si tiene `nombre` **y** `imagen`.

### Conceptos que aprendí

**Manejo de errores en imágenes (`onerror`)**
- Un `<img>` tiene un evento `onerror` que se dispara si el link no carga (404, link roto, etc.). Ahí se puede reaccionar cambiando el `src` a otra imagen de emergencia, en vez de dejar que el navegador muestre el ícono roto.
- Para el modal usé `modalImagen.onerror = function () {...}` una sola vez (fuera de `abrirModal`), porque el modal es un único elemento `<img>` que se reutiliza para todas las joyas — no hace falta reconectar el evento cada vez que se abre una joya distinta.
- Para las tarjetas usé `onerror="manejarErrorImagen(this)"` directo en el HTML de cada `<img>`, porque cada tarjeta es un elemento nuevo que se crea de cero al recorrer la lista de joyas.

**Placeholder con SVG en vez de una imagen externa**
- En vez de guardar un archivo de imagen para el "Sin imagen", generé un SVG chiquito (un rectángulo con texto) directamente en JavaScript como texto, y lo convertí en una URL válida para `src` con el prefijo `data:image/svg+xml;utf8,` + `encodeURIComponent(...)`. Así no depende de internet ni de un archivo aparte — siempre está disponible.

**Prevenir bucles infinitos**
- Si el placeholder también fallara, `onerror` se volvería a disparar sin parar. Por eso `manejarErrorImagen` primero chequea `if (img.src !== IMAGEN_SIN_FOTO)` antes de cambiar el `src` — así, aunque se dispare el evento de nuevo, no hace nada si ya está mostrando el placeholder.

**HTTP vs HTTPS**
- Un sitio servido por `https` (como GitHub Pages) bloquea por seguridad las imágenes que vengan de un link `http` sin cifrar ("contenido mixto"). Por eso conviene forzar `https://` en los links antes de usarlos, en vez de confiar en cómo los pegó cada uno en la planilla.

**`loading="lazy"`**
- Es un atributo nativo del HTML (no hace falta JavaScript ni librerías): le dice al navegador "no descargues esta imagen todavía, esperá a que el usuario esté por verla". Con pocas joyas no se nota, pero si el catálogo crece a decenas de fotos, evita que la página tarde en cargar todo de una.

### Próximos pasos pendientes
- Compartir la planilla con la dueña con permiso de Editor (si no se hizo aún) y que cargue las fotos reales vía ImgBB.
- Llevar el catálogo a producción con GitHub Pages.

---

**Dónde quedamos:** catálogo con el sistema de fotos reales ya programado (limpieza de URL, HTTPS forzado, tamaño uniforme, fallback "Sin imagen" y lazy loading), pero sin verificar todavía con una foto real (la de prueba de ImgBB se perdió). Próximo paso: conseguir un link de foto real y confirmar que carga bien.

## 26/09/2026 — Cierre de sesión: repaso general

### Resumen de lo hecho hoy
- **Fotos:** el código ya usa URLs reales de ImgBB en vez de picsum, con manejo de imagen rota (placeholder "Sin imagen"), lazy loading y forzado de `https`. El sistema **funciona** — se probó y el placeholder aparece correctamente cuando no hay una foto válida.
- **WhatsApp:** se agregó el botón "Consultar por WhatsApp" dentro del modal, con el mensaje armado dinámicamente según la joya abierta.
- **Google Sheets:** se mejoró la planilla por fuera del código — listas desplegables para `material`, `tipo` y `estado` (para que la dueña no escriba mal esos valores), formato prolijo en los títulos de columna, fila de encabezado congelada, y una pestaña nueva de "Instrucciones" para que ella sepa cómo cargar joyas sola.

### Pendiente para la próxima
- **Verificar el sistema de fotos con una foto real que cargue** (la de prueba de ImgBB se perdió). Pasos:
  1. Crear una cuenta en ImgBB (para que las fotos no se borren solas).
  2. Subir una foto de prueba.
  3. Copiar el link **directo** a la imagen — el que sale en la opción "HTML completo enlazado" de ImgBB, con formato `i.ibb.co/.../nombre.jpg` (no el link de la página de ImgBB, sino el de la imagen en sí).
  4. Pegar ese link directo en la columna `imagen` de la planilla y confirmar que la foto aparece en el catálogo.
- Resolver cómo copiar/pegar links entre Windows y Kali (o, alternativa, editar la planilla directamente desde el celular).
- Completar la pestaña de "Instrucciones" con el flujo completo de fotos para la dueña — es el paso más confuso de todo el proceso, así que conviene explicarlo bien con capturas o pasos bien concretos.

---

**Dónde quedamos:** catálogo completo y funcional (diseño boutique, Sheets, filtros, categorías, modal, botón de WhatsApp, sistema de fotos programado y andando). Solo falta verificar el sistema de fotos con un link real de ImgBB, y terminar de dejarle todo fácil a la dueña (instrucciones de fotos + cómo cargar la planilla desde su celular o PC).

## 06/10/2026 — Auditoría de calidad y tanda 1 de arreglos

### Cambios de hoy
- Se hizo una auditoría (accesibilidad, rendimiento, SEO, buenas prácticas) con 29 puntos priorizados. Esta tanda arregla los más importantes.
- El negocio ahora se llama **Joyería Kalo**: cambiaron el `<title>` ("Joyería Kalo · Catálogo") y el `<h1>`.
- Se agregaron `meta description` y etiquetas **Open Graph** (`og:title`, `og:description`, `og:image`...).
- El número de WhatsApp quedó en la constante `WHATSAPP_NUMERO`, arriba de todo en el script, con un `TODO` para cambiarlo por el real.
- `renderJoyas` se reescribió: cada tarjeta es un `<button>` armado con `createElement` + `textContent`, sin `innerHTML`.

### Conceptos que aprendí
- **Open Graph:** son `<meta>` que lee WhatsApp/Instagram/Facebook para armar la "vista previa" cuando se pega un link (foto + título + descripción). No se ven en la página, solo al compartir.
- **`textContent` vs `innerHTML`:** `innerHTML` interpreta el texto como HTML; si un nombre trae comillas o `<`, rompe la tarjeta (o deja meter código). `textContent` lo pone como texto puro, siempre. Se probó con el nombre `Anillo "Luna" <b>x</b>` y se mostró tal cual, sin romper nada.
- **`<button>` en vez de `<div>` clickeable:** un botón recibe foco con Tab y se activa con Enter/Espacio sin programar nada extra; un `<div>` no. Por eso las tarjetas ahora se pueden usar con teclado y lector de pantalla.
- **Dentro de un `<button>` solo va contenido "en línea"** (`<span>`, `<img>`), no `<div>` ni `<p>`. Para que un `<span>` se comporte como bloque (respete márgenes y padding) se le pone `display: block` en el CSS.
- **Botones con estilo propio:** un `<button>` trae borde, relleno y fuente por defecto; se resetean con `border: none; padding: 0; font: inherit; color: inherit;`.
- **Constantes de configuración arriba:** los datos que hay que cambiar (link del CSV, número de WhatsApp) conviene tenerlos juntos al principio, para no buscarlos por todo el código.

### Pendiente
- Reemplazar `WHATSAPP_NUMERO` por el número real de la dueña.
- Agregar `og:url` cuando el sitio esté en GitHub Pages.
- Siguientes tandas de la auditoría: contraste de colores, foco visible, interruptor accesible, modal accesible, mensaje cuando no hay resultados.

---

**Dónde quedamos:** tanda 1 de la auditoría lista (título, Open Graph, constante de WhatsApp, tarjetas como botones seguras). Próximo: número real de WhatsApp y tanda 2 (contraste y foco visible).

## 06/10/2026 — Nueva identidad visual de Joyería Kalo

### Cambios de hoy
- Rediseño inspirado (no copiado) en una referencia de joyería: banda oscura arriba + zona clara para las joyas + detalles dorados finos.
- Paleta nueva: **ciruela** `#24131d`, **champagne** `#d8bf8a`, **lino** `#efe6da`, **papel** `#faf6f0`, **tinta** `#2b1d24`.
- Tipografías nuevas: **Bodoni Moda** (títulos) y **Jost** (texto).
- El `<header>` salió de `.contenido` a su propia banda (`.portada`) con `fondo.avif` oscurecido detrás.
- Tarjetas con foto cuadrada, borde fino y estado con puntito de color (sin mayúsculas).
- Grilla: 2 columnas en celular, 3 en tablet, 4 en compu.
- No se tocó nada del JavaScript.

### Conceptos que aprendí
- **Contraste WCAG AA:** el texto tiene que tener al menos 4.5:1 de contraste con su fondo. Se calcula con una fórmula a partir de los colores; antes de elegir la paleta se verificó cada par (el peor quedó en 5.1:1).
- **Mobile first:** el CSS base es para el celular, y con `@media (min-width: 640px)` / `(min-width: 960px)` se agregan cambios para pantallas más grandes. Al revés que antes.
- **`:focus-visible`:** dibuja un contorno solo cuando se navega con teclado (no al tocar con el dedo o hacer clic). Así nadie se pierde en la página.
- **`@media (hover: hover)`:** aplica el efecto hover solo en dispositivos con mouse; en el celular el hover quedaba "pegado" después de tocar.
- **`@media (prefers-reduced-motion: reduce)`:** respeta a quien pidió en su celular/compu menos animaciones.
- **`aspect-ratio: 1 / 1`:** hace que la foto sea cuadrada sin importar el ancho de la tarjeta (en vez de una altura fija en px).
- **Variables CSS (`--ciruela`, `--serif`...):** cambiar un color en `:root` lo cambia en toda la página.

### Pendiente
- El "Sin imagen" (SVG en JavaScript) sigue con los colores viejos; se puede actualizar a la paleta nueva.
- Interruptor "Solo disponibles" sin nombre accesible (punto #6 de la auditoría).

---

**Dónde quedamos:** catálogo con la nueva identidad visual (ciruela + champagne, Bodoni Moda + Jost), mobile first, contraste AA y foco visible. Próximo: número real de WhatsApp, colores del "Sin imagen" y seguir con la auditoría (modal accesible, estado vacío).
