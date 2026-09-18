# TP1 - Permiso Vecinal

Aplicación web creada con Gemini Canvas para simular la solicitud de un permiso municipal de estacionamiento. La interfaz conserva una apariencia pública sobria, pero introduce una experiencia deliberadamente incómoda al exigir que la patente se reconstruya capturando caracteres en movimiento.

## Cómo probarla

Abrir `index.html` en un navegador. No requiere instalación, servidor, dependencias ni conexión a Internet.

El flujo tiene tres pasos:

1. Completar los datos personales, la patente y el tipo de permiso.
2. Verificar la patente seleccionando letras y números cuando pasan por el escáner amarillo.
3. Confirmar la solicitud simulada y revisar el resumen.

## Mecánica principal

- Hay una cinta para letras y otra para números.
- Cada carácter es un botón real del DOM.
- Un carácter sólo se acepta cuando el centro del botón está dentro de la zona de escaneo.
- Cada acierto aumenta la velocidad un 10 % e invierte el recorrido.
- Un carácter incorrecto elimina el último acierto, suma un error e invierte las cintas.
- La inversión manual suma una demora administrativa.
- El movimiento se ejecuta con un único loop de `requestAnimationFrame`.
- Con `prefers-reduced-motion`, las cintas se detienen y aparecen controles manuales.

## Decisiones de diseño

- La mala experiencia se concentra en la interacción, no en una estética rota o ilegible.
- Se eligió una identidad municipal ficticia para no copiar la de una ciudad real.
- La interfaz usa fondo gris claro, azul petróleo y un único acento amarillo.
- La patente acepta entre 6 y 7 caracteres alfanuméricos para admitir formatos antiguos y nuevos.
- Los datos existen sólo en memoria y nunca se envían ni almacenan.
- Todo el proyecto está contenido en un único archivo HTML con CSS y JavaScript internos.

## Proceso con IA

El artefacto se creó en una única conversación de Gemini Canvas. La primera intervención definió la aplicación completa y la segunda corrigió problemas encontrados durante las pruebas. Los textos exactos se encuentran en `prompts.md`.

## Problemas encontrados y correcciones

### Reinicio parcial confuso

El primer prompt pedía que el botón de reinicio vaciara solamente el progreso. Al probar la aplicación, la velocidad, la dirección y las demoras anteriores seguían activas, lo que hacía que el resultado pareciera un reinicio incompleto. En la segunda iteración se cambió la decisión: ahora el botón restablece todo el estado de la verificación.

### Propiedad CSS inválida

Gemini generó `box-sizing: border-border` para la zona de escaneo. Aunque la regla global evitaba un problema visual grave, el valor no era válido. Se pidió reemplazarlo por `border-box`.

### Ortografía inconsistente

La primera versión omitía varias tildes en los textos visibles. La segunda intervención corrigió la copia en español sin modificar los identificadores internos de JavaScript.

## Verificación realizada

Se probó manualmente:

- validación del formulario;
- normalización de la patente;
- selección fuera de la zona de escaneo;
- captura correcta e incorrecta;
- aumento de velocidad y cambio de dirección;
- retroceso del progreso y contador de errores;
- inversión manual;
- reinicio completo;
- preservación del formulario al volver;
- habilitación de la confirmación;
- resumen final y nueva solicitud;
- diseño de escritorio y vista móvil de 390 por 844 px.

También se verificó que el archivo no incluya dependencias, requests, canvas, fuentes externas ni almacenamiento del navegador.
