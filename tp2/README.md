# TP2 - API de mascotas y paseos

Contrato OpenAPI 3.1 para una API que administra mascotas y sus registros individuales de paseos. No incluye servidor, base de datos ni implementación: el entregable es únicamente la descripción del contrato.

## Cómo leerlo

El archivo `openapi.yaml` puede abrirse en una herramienta compatible con OpenAPI 3.1, como Swagger Editor, para ver la documentación navegable.

El contrato define cinco operaciones en tres paths:

| Método | Path | Resultado principal |
| --- | --- | --- |
| `GET` | `/pets` | Lista todas las mascotas |
| `POST` | `/pets` | Registra una mascota |
| `GET` | `/pets/{petId}/walks` | Lista los paseos de una mascota |
| `POST` | `/pets/{petId}/walks` | Registra un paseo para una mascota |
| `DELETE` | `/pets/{petId}/walks/{walkId}` | Elimina un paseo |

## Qué me propuse construir

Elegí un dominio conocido y pequeño en el que la jerarquía tuviera significado real. En este modelo, un paseo es un registro individual de una mascota determinada y no existe de manera independiente. Eso permite que la relación entre los recursos se vea directamente en los paths.

El contrato se desarrolló en tres iteraciones dentro de una sola conversación. El registro se encuentra en `prompts.md`.

## Decisiones que tomé

### Anidar los paseos dentro de las mascotas

Usé `/pets/{petId}/walks` en lugar de `/walks?petId=...` porque la pertenencia es estructural: cada paseo del modelo corresponde a una única mascota. El path identifica primero a la mascota y después a su paseo.

### No repetir `pet_id` en el body

`Walk` incluye `pet_id` en las respuestas para mostrar la relación, pero `WalkInput` no lo acepta. Al crear un paseo, la mascota ya está identificada por `{petId}` en el path. Repetirla en el body permitiría enviar dos valores contradictorios.

### Separar schemas de entrada y salida

`PetInput` y `WalkInput` contienen datos aportados por el cliente. `Pet` y `Walk` agregan los identificadores y la fecha de creación generados por el servidor. Así queda claro quién es responsable de cada campo.

### Usar `204` al eliminar

El `DELETE` devuelve `204` sin body. Una vez eliminado el paseo, no tiene sentido devolver el recurso como si todavía existiera.

### Diferenciar validación y ausencia

Un body inválido devuelve `400`. Una mascota o un paseo inexistente devuelve `404`. Ambos errores comparten una estructura tipada con `code` y `message`, pero conservan significados distintos.

### Mantener campos realmente opcionales

El nombre y la especie son obligatorios para una mascota; la fecha de nacimiento no. Un paseo necesita fecha de inicio y duración, mientras que distancia y notas son opcionales porque pueden no registrarse.

### Hacer explícita una API pública y no implementada

El contrato usa `security: []` porque no se definió autenticación para este ejercicio. La URL `api.example.test` es ilustrativa y utiliza un dominio reservado: no representa un servidor real.

### No inventar un error para listar mascotas

El linter recomienda que toda operación tenga una respuesta `4xx`. No agregué una a `GET /pets`: la operación no recibe parámetros, es pública y una colección sin mascotas se representa correctamente como `200` con `[]`. Agregar un `400` o un `404` sólo para silenciar la advertencia empeoraría el contrato.

## Qué salió mal y cómo lo corregí

### Errores sin estructura

La primera versión nombraba respuestas `400` y `404`, pero sólo incluía una descripción. Un cliente podía saber que la operación fallaba, aunque no qué JSON recibiría. En la segunda iteración agregué un schema `Error` reutilizable, con `code` y `message`, más ejemplos para validación y recurso inexistente.

### Validaciones demasiado abiertas

Los schemas iniciales aceptaban nombres vacíos, duraciones negativas y propiedades no documentadas. La revisión agregó límites de longitud y rango, además de `additionalProperties: false` en los objetos.

### Metadatos omitidos

La primera validación con Redocly encontró que no se habían declarado `servers`, seguridad ni licencia. La tercera iteración hizo explícitas esas decisiones. Después de los cambios, el contrato quedó válido; sólo permanece la advertencia intencional sobre la ausencia de un `4xx` artificial en `GET /pets`.

## Validación

El YAML se parseó localmente y se validó con Redocly CLI usando su configuración recomendada.

Resultado final:

- OpenAPI 3.1 válido.
- 3 paths.
- 5 operaciones.
- Métodos `GET`, `POST` y `DELETE`.
- Jerarquía de recursos visible.
- Errores `400` y `404` tipados.
- Schemas con `type`, `required`, `format` y restricciones adicionales.
- Una advertencia semántica conservada de manera intencional para `GET /pets`.
