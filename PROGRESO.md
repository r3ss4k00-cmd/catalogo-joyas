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

## 06/10/2026 — Crítica de diseño + filtros y modal mejorados

### Qué encontró la crítica (impeccable)
Lo que más le quita profesionalismo **no es el diseño, es el contenido**:
1. 4 de 5 joyas muestran "Sin imagen": en la planilla la columna `imagen` tiene `5`, `4`, `6`, `7` en vez de links.
2. El número de WhatsApp sigue siendo falso (`5490000000000`).
3. No hay precio (se decidió mostrar "Consultar precio").
4. Faltan señales de confianza: logo, frase propia, pie de página con Instagram, envíos y medios de pago.

### Cambios de hoy (filtros y modal)
- **"Solo disponibles":** ahora todo el texto es tocable (el texto está dentro del `<label>`) y mide 44px de alto.
- **Rótulos "Tipo" y "Material"** sobre cada fila de botones. La fila de material es más liviana (más chica, fondo papel, borde suave) para que se entienda que es un segundo nivel.
- **Materiales según el tipo elegido:** si elegís Anillos, solo aparecen los materiales que hay en anillos. Si hay uno solo, la fila se esconde.
- **Estado vacío:** si los filtros no dejan ninguna joya, aparece "No hay piezas disponibles con estos filtros por ahora" y un botón "Ver todas las joyas".
- **"Consultar precio"** en cada tarjeta y en el modal, siempre en el mismo lugar.
- **El modal muestra estado y material.** Si la joya está vendida o reservada, el botón dice "Consultar por una similar" y el mensaje de WhatsApp cambia.
- **El botón "Atrás" del celular cierra el modal** en vez de sacarte de la página.
- **El fondo no se desplaza** mientras el modal está abierto.
- **Accesibilidad:** el modal se anuncia como diálogo, la × dice "Cerrar" y el foco va a la × al abrir y vuelve a la tarjeta al cerrar.
- Si una joya no trae estado en la planilla, ya no aparece "undefined".

### Conceptos que aprendí
- **Jerarquía visual:** el orden en que el ojo lee la página. Lo importante tiene que pesar más (tamaño, color, posición) que lo secundario.
- **Estado vacío (empty state):** qué se muestra cuando no hay nada para mostrar. Una pantalla en blanco parece un error; un mensaje con una salida tranquiliza.
- **`hidden`:** atributo de HTML para esconder algo. Desde JS: `elemento.hidden = true`. Se agregó `[hidden] { display: none !important; }` porque si una clase pone `display: flex`, le gana al `hidden`.
- **`role="dialog"` y `aria-modal`:** le dicen al lector de pantalla "esto es una ventana encima de la página".
- **`history.pushState` y `popstate`:** `pushState` agrega una "página falsa" al historial al abrir el modal. Cuando apretás Atrás, el navegador dispara el evento `popstate` y ahí cerramos el modal.
- **Trampa de foco:** con Tab, el foco da vueltas dentro del modal (× ↔ WhatsApp) en vez de irse a la página de atrás.

### Pendiente
- Cargar links reales de fotos en la planilla.
- Poner el número real de WhatsApp.
- Capa de marca: logo en la barra, frase en vez de "Catálogo" y pie de página.

---

**Dónde quedamos:** filtros y modal mejorados (crítica de diseño hecha). Próximo: **contenido** (fotos reales + número de WhatsApp) y después marca y confianza (logo, frase, pie de página).

## 06/10/2026 — Animaciones y microinteracciones

### Cambios de hoy
- **Curva de movimiento única** `--ease-out: cubic-bezier(0.23, 1, 0.32, 1)` en `:root`, usada en todas las animaciones de movimiento.
- **El modal ahora sí se anima:** abre en 240ms y cierra en 160ms (cerrar es más rápido que abrir), con un fundido del fondo y un leve `scale(0.97)` del contenido.
- **El foco vuelve a la tarjeta cuando termina la animación de cierre**, también al reabrir rápido o al usar Atrás.
- **Respuesta al presionar:** las tarjetas (`scale(0.985)`), los filtros y el botón de WhatsApp (`scale(0.97)`) se achican apenas al tocarlos.
- **Hover solo con mouse** (`@media (hover: hover) and (pointer: fine)`) en tarjetas y filtros.
- **Fotos con fundido:** arrancan invisibles y JS les pone `.cargada` al terminar de bajar (o al fallar, para que se vea el "Sin imagen").
- **La fila de material y el estado vacío** aparecen con un fundido corto (`@starting-style`).
- **`scrollbar-gutter: stable`:** en la compu la página ya no "salta" al abrir el modal.
- **Interruptor:** la bolita se mueve con la curva nueva y además cambia de color con transición.
- **`loading = "lazy"` va antes que `src`:** antes el navegador podía empezar a bajar la foto antes de enterarse de que podía esperar.
- Se probó con Chromium: la trampa de foco sigue andando, y con el modal cerrado el Tab nunca entra al modal.

### Conceptos que aprendí
- **`display: none` no se anima:** si un elemento estaba en `display: none`, el navegador no tiene un "antes" desde donde animar. Por eso la transición vieja del modal nunca se veía.
- **`visibility` con retraso:**
  - `transition: visibility 0s linear 160ms` espera a que termine el fundido y recién ahí oculta.
  - `visibility: hidden` también saca los botones del recorrido con Tab.
- **Curvas de easing (aceleración):**
  - `ease-out` arranca rápido y frena suave: se siente con respuesta inmediata.
  - `ease-in` arranca lento y se siente pesado; no se usa en interfaces.
- **`transitionend`:** un evento que avisa cuando terminó una transición. Se suma un `setTimeout` de respaldo por si el aviso no llega.
- **`@starting-style`:** le dice al navegador "desde dónde arranca" un elemento que recién aparece, para animar su entrada sin JavaScript.
- **Movimiento reducido ≠ cero animación:** a quien pidió menos movimiento se le sacan los zooms y desplazamientos (que marean), pero se le dejan los fundidos de opacidad y color.

---

**Dónde quedamos:** filtros, modal y animaciones listos (sin commit todavía). Próximo: **contenido** (fotos reales + número de WhatsApp) y después marca y confianza (logo, frase, pie de página).

## 06/10/2026 — Capa de marca: textos, barra, pie y favicon

### Cambios de hoy
- **Frase nueva** en la portada: "Piezas elegidas una por una" (antes decía "Catálogo").
- **Barra superior:** ahora muestra "Kalo" a la izquierda (al tocarlo vuelve arriba). El interruptor "Solo disponibles" se mudó a la zona de filtros, arriba de "Tipo".
- **Botones del modal:** "Consultar esta pieza" y "Buscar una parecida". Los lectores de pantalla además escuchan "por WhatsApp".
- **Pie de página nuevo:** cuidado de las joyas, atención personal en primera persona ("Escribime y te ayudo a elegir"), entrega en mano y un botón de WhatsApp que usa `WHATSAPP_NUMERO`.
- **Título y vista previa** (lo que se ve al compartir el link por WhatsApp) con la frase nueva, más `og:url` con la dirección de GitHub Pages.
- **Favicon:** una "K" champagne sobre ciruela.
- Se probó con Chromium: recorrido con Tab, color del foco, textos del modal, link del pie e interruptor.

### Conceptos que aprendí
- **Copywriting:** un texto específico ("Piezas elegidas una por una") vende más que uno genérico ("Catálogo"), porque dice algo que solo es verdad de Kalo. Y no se promete nada que no exista (por eso no hay "garantía").
- **Texto solo para lectores de pantalla (`.solo-lectores`):** se ve "Consultar esta pieza", pero una persona ciega escucha "Consultar esta pieza por WhatsApp". El texto existe, pero mide 1px y queda recortado.
- **El contraste del foco depende del fondo:** el dorado oscuro se ve bien sobre lo claro (5.1:1), pero sobre el ciruela da 2.8:1 y casi desaparece. Por eso en la barra y en el pie el contorno es champagne.
- **Especificidad en CSS:** `.pie p` le gana a `.pie-final` porque tiene más "puntaje" (clase + etiqueta contra una sola clase). Para ganarle se usa `.pie .pie-final`.
- **Pie siempre abajo:** con `body` en columna (`display: flex; flex-direction: column`) y `flex: 1` en la zona de joyas, esa zona estira lo que falte y el pie no queda flotando a mitad de pantalla.
- **Favicon en un data URI:** el dibujo SVG va escrito dentro del HTML y no hace falta un archivo aparte. La K está hecha con líneas (no con una letra) para que se vea igual en todos los equipos.

### Pendiente
- Poner el usuario de Instagram en `INSTAGRAM_USUARIO` (con eso el link aparece solo).
- Confirmar si vende oro. Si no, sacar "oro, " de las dos descripciones del `<head>`.
- Medios de pago (hay un TODO en el pie) y la política de cambios.
- Ícono para la pantalla de inicio del iPhone (`apple-touch-icon`, necesita un PNG).
- Sigue pendiente lo de antes: fotos reales y el número real de WhatsApp.

---

**Dónde quedamos:** capa de marca lista (sin commit todavía). Próximo: **contenido** (fotos reales, número de WhatsApp, usuario de Instagram).

## 06/10/2026 — Revisión final (diseño, calidad y seguridad)

### Qué encontró la revisión
- **Lector del CSV frágil:** cortaba el texto por líneas *antes* de mirar las comillas. Un Enter dentro de una celda (la fila 1 de la planilla ya tenía uno al final del link) podía hacer desaparecer una joya sin aviso. Además, `Anillo "Luna"` se mostraba `Anillo Luna`.
- **Foco perdido con teclado:** al elegir un filtro, los botones se borraban y se creaban de nuevo, y el foco quedaba "en el aire" (`<body>`). Se comprobó en Chromium.
- **Planilla:** estaba publicado el documento completo, incluida la pestaña INSTRUCCIONES. Ahora solo se publica la hoja CATALOGO JOYAS (`URL_CSV` cambió a `…pub?gid=0&single=true&output=csv`).
- **Seguridad, lo que ya estaba bien:** ningún dato de la planilla llega a `innerHTML`, los links de WhatsApp usan `encodeURIComponent`, y un `javascript:` en una imagen no se ejecuta.

### Cambios de hoy
- **CSV:** una función nueva, `csvAFilas`, recorre todo el texto caracter por caracter. Entiende comillas, `""`, comas y Enters dentro de celdas, y finales de línea de Windows.
- **Filtros:** los botones de tipo se crean una sola vez. Al elegir uno, `marcarActivo` solo cambia la clase `activo` y `aria-pressed`, así el foco no se mueve. "Ver todas…" pasa el foco al primer filtro.
- **Aviso para lectores de pantalla:** un texto invisible con `aria-live` dice "2 joyas", "1 joya" o el mensaje de estado vacío cada vez que cambian los filtros.
- **Modal:**
  - La foto se oculta hasta que baja la nueva. Ya no se ve un instante la joya anterior.
  - Mientras el modal está abierto, el fondo queda `inert`.
- **Estructura:** la barra superior pasó a `<nav>`, la zona de joyas a `<main>`, y hay un `<h2>Joyas</h2>` solo para lectores de pantalla.
- **Imágenes:** si la planilla no trae un link `https://` (por ejemplo `5`), se muestra "Sin imagen" sin pedir nada al servidor (antes daba 404). Las primeras 4 fotos cargan enseguida y el resto con `lazy`.
- **Detalles:**
  - `preconnect` a la planilla y a ImgBB.
  - `theme-color` y `canonical` en el `<head>`.
  - `noreferrer` en los links externos.
  - Hover de × y WhatsApp solo con mouse.
  - `overscroll-behavior` en el modal.
  - `scroll-padding-top` para que la barra no tape lo enfocado.
  - "Cargando joyas…" con el caracter `…`.
  - `botonVolver` con `hidden`.
  - Grilla con `minmax(0, 1fr)` para nombres larguísimos.
- **Pruebas:** se probó todo en Chromium a 390px (celular) y 1280px (compu). Pasaron 44 de 44 pruebas en cada tamaño.

### Conceptos que aprendí
- **Por qué se pierde el foco:** si borrás el elemento que tiene el foco (por ejemplo con `innerHTML = ""`), el navegador no sabe dónde ponerlo y lo manda a `<body>`. Quien usa teclado o lector de pantalla pierde su lugar. La solución es no recrear: cambiar solo las clases o atributos del botón que ya existe.
- **`aria-pressed`:** convierte un botón en un "interruptor" para el lector de pantalla ("Anillos, botón, presionado"). El color solo no alcanza, porque una persona ciega no lo ve.
- **`aria-live="polite"`:** marca una zona cuyo texto el lector anuncia cuando cambia, sin mover el foco. "Polite" significa que espera a que termine de hablar.
- **`inert`:** un atributo que vuelve una parte de la página "intocable". No se puede hacer clic ni enfocarla con Tab, y el lector de pantalla no la lee. Es ideal para el fondo de un modal.
- **Leer un CSV caracter por caracter:** es una pequeña "máquina de estados" con un interruptor `dentroDeComillas`. Dentro de comillas todo es texto (incluso comas y Enters). Fuera de comillas, la coma corta el valor y el Enter corta la fila. `""` adentro de comillas significa una comilla de verdad.
- **`dataset`:** `boton.dataset.valor = "anillo"` guarda un dato propio en el elemento (en el HTML queda como `data-valor="anillo"`). Sirve para saber qué representa cada botón sin depender del texto visible ("Anillos").
- **`minmax(0, 1fr)`:** `1fr` solo no deja que una columna sea más angosta que su contenido más largo. Con `minmax(0, 1fr)` la columna respeta su ancho y el texto se corta (`overflow-wrap: anywhere`).
- **`preconnect` con y sin `crossorigin`:** el navegador usa conexiones distintas para los pedidos CORS (fetch, fuentes) y para los normales (fotos). Por eso la planilla lleva `crossorigin` y ImgBB no.

### Pendiente
- Contenido: fotos reales en la planilla, número real de WhatsApp y usuario de Instagram.
- Se dejaron afuera a propósito:
  - CSP (una política de seguridad extra).
  - Que `PROGRESO.md` y `CLAUDE.md` se pueden leer en GitHub Pages.
  - Alojar las fuentes en el repo en vez de Google Fonts.
- Hacer el commit de esta tanda.

---

**Dónde quedamos:** revisión final aplicada y probada en Chromium (celular y compu), sin commit todavía. Próximo: commit y **contenido** (fotos reales, número de WhatsApp, usuario de Instagram).

## 07/10/2026 — Ajustes del modal vistos en un iPhone

### Cambios de hoy
- **Foco inicial:** al abrir el modal, el foco va al contenedor (`tabindex="-1"`, sin outline) y no a la ×. Así, al abrir con el dedo, Safari ya no dibuja el anillo de foco.
- **Trampa de foco:** con teclado sigue igual. Tab desde el contenedor va a la ×, y después a WhatsApp. Se agregó un caso: Shift+Tab desde el contenedor va a WhatsApp, porque si no se escapaba del modal.
- **La ×:**
  - Ahora está a 8px del borde (antes 4px).
  - Su anillo de foco se dibuja hacia adentro (`outline-offset: -2px`), así no se sale de la esquina.
  - El modal tiene 4px más de espacio arriba (56px).
- **Foto del modal:** como máximo `50vh` (antes `60vh`). En un celular de 375×667 se ven la foto, el nombre y el botón sin scroll, incluso con una foto vertical y un nombre de dos renglones.
- **dvh con fallback:** la foto (`50dvh`) y el modal (`90dvh`) usan el alto que se ve de verdad en el celular. Arriba de cada una quedó la línea con `vh`, para navegadores viejos.
- **Pruebas:** en Chromium a 375×667 y 1280×800 pasaron 32 de 32.

### Conceptos que aprendí
- **`tabindex="-1"`:** permite enfocar un elemento desde JavaScript (`.focus()`), pero el Tab no se detiene en él. Sirve para "pararse" en el contenedor de un diálogo. El lector de pantalla anuncia el diálogo y el próximo Tab va al primer botón de adentro.
- **`outline-offset` negativo:** con un valor positivo, el anillo se dibuja afuera del elemento. Con uno negativo, se dibuja adentro. Sirve cuando el elemento está pegado a un borde y el anillo "se saldría".
- **`vh`:** 1vh es el 1% del alto de la pantalla. `50vh` es la mitad, sea cual sea el celular.
- **`dvh` y fallback en CSS:** en Safari de iPhone, `vh` mide la pantalla como si las barras del navegador estuvieran ocultas. `dvh` mide solo lo que se ve. Si se escribe `max-height: 50vh;` y debajo `max-height: 50dvh;`, el navegador usa la última línea que entiende. Uno viejo ignora la de `dvh` y se queda con `vh`.

---

**Dónde quedamos:** ajustes del modal (foco y altura de la foto) hechos, probados y subidos a `main`. Próximo: **contenido** (fotos reales, número de WhatsApp, usuario de Instagram).

## 07/10/2026 — Rendimiento: de 69 a 100 en Lighthouse (celular)

### Cómo se midió
- Lighthouse por línea de comandos, modo celular, con el Chromium de `/usr/bin/chromium`. Se hicieron 3 corridas y se tomó la del medio (la mediana).
- "Antes" y "después" se midieron igual: las dos versiones servidas en la compu.
- La página publicada (todavía sin estos cambios) dio 67.

| | Antes | Después |
|---|---|---|
| Performance | 69 | **100** |
| Accesibilidad / Buenas prácticas / SEO | 100 / 100 / 100 | 100 / 100 / 100 |
| Primer dibujo (FCP) | 2,93 s | 0,94 s |
| Elemento más grande (LCP) | 2,93 s | 1,66 s |
| Saltos de diseño (CLS) | 0,47 | **0** |

Solo quedan dos avisos: caché y compresión. Dependen del servidor (GitHub Pages ya comprime; la caché no se puede cambiar ahí).

### Cambios de hoy
- **Fuentes en el repo:** Bodoni Moda y Jost ahora están en la carpeta `fuentes/`, con su licencia OFL al lado. Ya no se usa Google Fonts, que frenaba el primer dibujo casi 2 segundos.
  - Son las mismas fuentes que mandaba Google, recortadas a los grosores 400–500 (73 KB → 55 KB).
  - Bodoni conserva el eje `opsz`: el título grande se sigue viendo igual.
- **Preload:** el `<head>` avisa de entrada que hacen falta las 2 fuentes y `fondo.avif`. El fondo además va con `fetchpriority="high"`.
- **Nada salta al cargar:**
  - La fila de botones de Tipo reserva su alto (`min-height: 44px`).
  - "Cargando joyas…" ahora se muestra en el lugar de esos botones.
  - Mientras carga, la grilla ocupa una pantalla (`.cargando`). Así el pie arranca fuera de la vista.
  - Hay fuentes de respaldo con `size-adjust`, para que el cambio a la fuente real no mueva el texto.
- **Fotos achicadas con wsrv.nl:**
  - La tarjeta pide la foto cuadrada de 400 px en WebP (85 KB → 22 KB), o la de 800 px en pantallas muy nítidas (`srcset`). El modal pide hasta 900 px.
  - Si wsrv.nl falla, se usa la foto original de ImgBB. Si esa también falla, se muestra "Sin imagen".
- **Pruebas:** en Chromium pasaron 56 de 56, a 390 px y 1280 px:
  - filtros,
  - el modal con teclado,
  - la foto con wsrv caído y con wsrv e ImgBB caídos,
  - la planilla caída.

  Las capturas de antes y después son iguales (solo cambia la compresión de la foto).

### Conceptos que aprendí
- **Recurso que bloquea el dibujo:** un `<link rel="stylesheet">` en el `<head>` frena todo. El navegador no dibuja nada hasta tener ese CSS, y si viene de otro servidor (Google), primero tiene que conectarse a él.
- **Preload:** `<link rel="preload">` le dice al navegador "esto lo vas a necesitar, bajalo ya". Sin eso, recién se entera de que existe `fondo.avif` cuando aplica el CSS de la portada.
  - Las fuentes siempre llevan `crossorigin` en el preload, aunque sean del mismo sitio.
- **LCP (Largest Contentful Paint):** cuánto tarda en verse lo más grande de la pantalla. Acá es el fondo de la portada, no el título. Se averiguó midiendo, no adivinando.
- **CLS (Cumulative Layout Shift):** suma cuánto se mueven las cosas que ya estaban en pantalla.
  - Ejemplo: estás por tocar "Escribime por WhatsApp", llegan las joyas, el botón baja y tocás otra cosa.
  - Se arregla reservando el lugar antes de que llegue el contenido.
- **Fuente variable:** un solo archivo con todos los grosores (y en Bodoni, también el "tamaño óptico"), en vez de un archivo por grosor.
- **`size-adjust`:** agranda o achica una fuente del sistema para que ocupe lo mismo que la fuente real mientras esta baja.
- **`srcset` y `sizes`:**
  - `srcset` es la lista de versiones de la foto, con su ancho (`400w`, `800w`).
  - `sizes` dice qué tan ancha se va a ver la foto en cada pantalla.
  - Con esos dos datos, el navegador elige solo cuál bajar.
  - Si hay `srcset`, le gana a `src`: para usar el respaldo hay que sacarlo.
- **Proxy de imágenes (wsrv.nl):** un servicio que baja la foto, la achica y la manda en otro formato.
  - Ventaja: pesa mucho menos.
  - Riesgo: depende de un servicio gratuito de terceros. Por eso tiene respaldo.

### Pendiente
- Cuando se publique, medir con PageSpeed Insights la página real en GitHub Pages.
- Si wsrv.nl algún día anda mal o lento, se saca `urlAchicada` y se vuelve a ImgBB directo.

---

**Dónde quedamos:** cambios de rendimiento hechos y probados, **sin commit**. Próximo: revisar, hacer el commit, publicar y medir la página real. Después, **contenido** (fotos reales, número de WhatsApp, usuario de Instagram).

## 07/10/2026 — README para el portfolio

### Cambios de hoy
- Se creó `README.md` en inglés, pensado para mostrar el proyecto al buscar trabajo remoto.
- Tiene: el problema real, cómo funciona (con un diagrama), funcionalidades, decisiones técnicas, rendimiento (PageSpeed 100/100/100/100 y la tabla de antes y después), qué aprendí, limitaciones, cómo correrlo y cómo se hizo con Claude Code.
- Dato corregido: las joyas vendidas **no se borran** de la planilla, quedan con estado "vendida".

### Conceptos que aprendí
- **README de portfolio:** quien lo lee quiere saber rápido qué problema resuelve, cómo está hecho y por qué se eligió así. Las limitaciones dichas con honestidad suman confianza.
- **Mermaid:** un diagrama escrito como texto dentro del Markdown. GitHub lo dibuja solo. Ejemplo: `A[Planilla] --> B[fetch]` dibuja dos cajas unidas por una flecha.
- **`<details>` y `<summary>`:** un bloque que se abre y se cierra al tocarlo. Sirve para acortar el README sin borrar información.
- **Medir con la herramienta correcta:** la tabla de antes y después es de Lighthouse en la compu; el 100/100/100/100 es de PageSpeed Insights sobre la página publicada. En el README se aclara cuál es cuál.

### Pendiente
- Agregar capturas al README cuando estén las fotos reales (hay un TODO).
- Hacer el commit del README.

---

**Dónde quedamos:** README listo, **sin commit**. Próximo: revisarlo en GitHub y, después, **contenido** (fotos reales, número de WhatsApp, usuario de Instagram).

## 07/10/2026 — Historial limpio de datos personales

### Cambios de hoy
- Se reescribió todo el historial con `git filter-repo --replace-text` para sacar los datos personales de la dueña (su nombre, cómo se la nombraba y la característica del número de prueba).
- El título viejo de las primeras versiones pasó a ser "Joyería Kalo".
- Ahora hay una sola rama (`main`) y no hay remoto: el repo se va a recrear en GitHub.
- Se comprobó que los archivos finales son idénticos a los de antes de reescribir y que la página carga las 5 joyas servida en la compu.

### Conceptos que aprendí
- **Git guarda todas las versiones:** borrar un texto en un commit nuevo no alcanza, porque sigue en los commits anteriores. Para sacarlo de verdad hay que reescribir el historial.
- **Reescribir cambia los hashes:** cada commit nuevo tiene otro hash, así que hay que hacer `push --force` a un repo nuevo o limpio. Y antes, siempre una copia de seguridad.
- **Reemplazos con cuidado:** una palabra corta también aparece dentro de otras (si reemplazás "pan", también cambia "pantalla"). Por eso se reemplazan frases completas, y las largas van primero ("mi X" antes que "X").
- **Commits que quedan vacíos se descartan:** el commit que reemplazaba los textos quedó sin cambios (los commits anteriores ya los tenían) y `filter-repo` lo sacó solo.
- **Comparar árboles:** el hash del árbol (`HEAD^{tree}`) resume todos los archivos de un commit. Si dos árboles tienen el mismo hash, los archivos son idénticos.

### Pendiente
- Crear el repo nuevo en GitHub, agregar el remoto y hacer push (con GitHub Pages activado de nuevo).
- Hacer el commit de esta entrada.

---

**Dónde quedamos:** historial limpio, solo en la compu, **sin push**. Próximo: recrear el repo en GitHub y publicar. Después, **contenido** (fotos reales, número de WhatsApp, usuario de Instagram).
