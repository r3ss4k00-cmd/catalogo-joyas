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
