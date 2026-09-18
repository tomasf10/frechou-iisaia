# Prompts utilizados

Los dos prompts se enviaron en orden dentro de una única conversación de Gemini Canvas.

## Prompt 1

```text
Creá en Canvas una aplicación web llamada “Permiso Vecinal” para solicitar un permiso municipal simulado de estacionamiento.

La aplicación debe parecer un portal público sobrio y confiable. La mala experiencia no debe venir de una estética fea, sino de una mecánica deliberadamente incómoda para verificar la patente del vehículo.

Flujo general:

La aplicación tiene tres pasos dentro de una sola página, sin recargar:

1. Datos de la solicitud.
2. Verificación mecánica de la patente.
3. Confirmación.

Mostrar en el header el nombre “Permiso Vecinal”, el texto “Solicitud simulada” y el paso actual. El footer debe aclarar que no se envían ni almacenan datos.

Paso 1: datos

Crear un formulario con:

- Nombre y apellido.
- Email.
- Patente del vehículo.
- Tipo de permiso: residente, visita o carga y descarga.

Reglas:

- Todos los campos son obligatorios.
- El email debe tener validación básica.
- La patente admite entre 6 y 7 caracteres alfanuméricos.
- Normalizar la patente a mayúsculas y eliminar espacios.
- No exigir un formato argentino específico. Debe aceptar patentes antiguas y nuevas.
- Al continuar, guardar los datos solamente en memoria y pasar al paso 2.
- No hacer requests ni usar storage.

Paso 2: verificación de patente

El usuario debe reconstruir la patente que ingresó usando dos cintas transportadoras:

- Una cinta contiene botones con las letras A-Z.
- Otra cinta contiene botones con los números 0-9.
- Todos los caracteres deben ser elementos reales del DOM. No usar canvas ni SVG.
- Las cintas se mueven horizontalmente de manera continua.
- En el centro de cada cinta hay una zona fija de escaneo de aproximadamente 72 px de ancho.
- Un carácter solo puede capturarse si el centro visual de su botón está dentro de la zona de escaneo cuando el usuario lo presiona.
- Calcular esa condición con getBoundingClientRect.

Mostrar:

- La patente objetivo.
- El progreso actual, ocultando los caracteres pendientes con guiones bajos.
- El próximo carácter esperado.
- Cantidad de errores.
- Velocidad actual de las cintas.
- Una región aria-live para mensajes.

Comportamiento:

- Si el usuario captura el carácter esperado dentro de la zona, agregarlo al progreso.
- Después de cada acierto, aumentar la velocidad un 10 por ciento e invertir la dirección de ambas cintas.
- Si captura un carácter incorrecto dentro de la zona, eliminar el último carácter ya verificado, si existe, sumar un error e invertir las cintas.
- Si presiona un carácter fuera de la zona, mostrar “Fuera de la zona de escaneo” sin modificar el progreso.
- Incluir un botón “Invertir recorrido”. Usarlo invierte las cintas y suma una demora administrativa al contador de errores.
- Incluir “Reiniciar verificación”, que vacía solamente el progreso del paso 2.
- Incluir “Volver a los datos”. Debe conservar el formulario, pero reiniciar la verificación.
- Bloquear clicks repetidos durante unos 180 ms después de cada captura para evitar dobles activaciones.
- Cuando la patente esté completa, detener las cintas y habilitar “Confirmar solicitud”.
- Confirmar pasa al paso 3.

Animación:

- Usar un solo loop global de requestAnimationFrame.
- El loop mueve ambas cintas leyendo un estado compartido.
- Los caracteres deben reaparecer de forma continua cuando salen del área visible.
- No crear un setInterval por carácter.
- No dejar animaciones duplicadas al cambiar de paso.
- Animar solamente transform y opacity.

Movimiento reducido:

- Respetar prefers-reduced-motion.
- En ese modo, detener el movimiento automático.
- Mostrar un botón “Avanzar cinta” para cada cinta, que mueve los caracteres una posición.
- La regla de capturar solamente dentro de la zona de escaneo se mantiene.

Paso 3: confirmación

Mostrar:

- “Solicitud registrada”.
- Nombre.
- Email.
- Patente.
- Tipo de permiso.
- Cantidad de errores administrativos.
- Aclaración de que es una simulación y no se registró ningún permiso real.
- Botón “Nueva solicitud” que reinicia completamente la aplicación.

Estado mínimo:

- paso.
- datos.
- patenteObjetivo.
- caracteresVerificados.
- direccion.
- velocidad.
- offsetLetras.
- offsetNumeros.
- capturaBloqueada.
- cantidadErrores.
- movimientoReducido.

Estilo:

- Portal municipal ficticio, sin copiar la identidad de una ciudad real.
- Fondo gris muy claro, texto azul petróleo oscuro y un único acento amarillo.
- Tipografía del sistema, sin fuentes externas.
- Radios de 8 px consistentes.
- Header compacto y formulario claro.
- La zona de escaneo debe verse como un marco mecánico amarillo.
- Las cintas pueden tener marcas industriales, pero la interfaz general debe seguir siendo sobria.
- Botones con contraste suficiente, estados hover, active, disabled y focus-visible.
- Diseño responsive para escritorio y móvil.
- No usar em dash ni en dash en ningún texto visible.

Constraints:

- Entregar un solo archivo index.html completo.
- CSS dentro de style y JavaScript dentro de script.
- Vanilla JavaScript.
- Sin frameworks, librerías, imágenes, fuentes ni dependencias externas.
- Sin canvas.
- Sin backend, requests, cookies, localStorage ni sessionStorage.
- Todo debe funcionar abriendo index.html con doble click.
- No omitir código ni reemplazar partes con comentarios.
- Crear también la vista previa funcional dentro de Canvas.
```

## Prompt 2

```text
La primera versión funciona correctamente en términos generales. Quiero una segunda iteración sobre el mismo archivo, sin cambiar el concepto, la estructura de tres pasos ni la estética municipal actual.

Corregí estos puntos:

1. El botón “Reiniciar verificación” debe reiniciar completamente el desafío:
   - borrar los caracteres verificados;
   - volver la dirección al valor inicial;
   - volver la velocidad al valor inicial;
   - poner errores y demoras en 0;
   - desbloquear cualquier captura pendiente;
   - llevar ambas cintas a su posición inicial;
   - actualizar toda la interfaz y mostrar un mensaje informativo.
   Reutilizá la función de reinicio existente para no duplicar lógica. No debe iniciarse un segundo loop de requestAnimationFrame.

2. Corregí la propiedad CSS inválida:
   box-sizing: border-border;
   Debe ser:
   box-sizing: border-box;

3. Revisá toda la copia visible y corregí la ortografía en español. Por ejemplo:
   “electrónico”, “vehículo”, “verificación”, “próximo”, “carácter”, “éxito”, “confirmación”, “trámite”, “simulación”, “aplicación”, “pública”, “únicamente”, “número” y “clic”.
   Los identificadores internos de JavaScript pueden conservarse sin tildes para no romper la implementación.

4. Conservá exactamente el comportamiento ya logrado:
   - un único loop con requestAnimationFrame;
   - botones DOM reales en las cintas;
   - comprobación con getBoundingClientRect;
   - bloqueo de captura de 180 ms;
   - aumento de velocidad del 10% y cambio de dirección después de un acierto;
   - retroceso, error y cambio de dirección después de una selección incorrecta;
   - clic fuera del escáner sin modificar progreso, velocidad, dirección ni errores;
   - modo prefers-reduced-motion con cintas detenidas y controles manuales;
   - formulario preservado al volver al paso anterior;
   - diseño responsive;
   - sin dependencias, imágenes externas, fuentes externas, almacenamiento ni backend.

5. No uses guion largo ni guion mediano en ningún texto visible.

Devolvé el archivo index.html completo y actualizado, con CSS y JavaScript inline. No devuelvas fragmentos ni explicaciones fuera del archivo.
```
