# Prompts utilizados

Las tres intervenciones se realizaron en orden dentro de una única conversación de IA.

## Prompt 1 - Contrato inicial

```text
Necesito un archivo openapi.yaml con OpenAPI 3.1 para una API de mascotas y registros individuales de paseos.

El dominio tiene estos recursos:

Pet:
- id: string con format uuid, generado por el servidor.
- name: string requerido.
- species: string requerido con valores dog, cat u other.
- birth_date: string opcional con format date.
- created_at: string con format date-time, generado por el servidor.

Walk:
- id: string con format uuid, generado por el servidor.
- pet_id: string con format uuid, deducido de la mascota del path.
- started_at: string requerido con format date-time.
- duration_minutes: integer requerido.
- distance_km: number opcional con format double.
- notes: string opcional.
- created_at: string con format date-time, generado por el servidor.

Endpoints:

- GET /pets devuelve 200 con la lista de mascotas.
- POST /pets devuelve 201, o 400 si los datos son inválidos.
- GET /pets/{petId}/walks devuelve 200 con los paseos, o 404 si la mascota no existe.
- POST /pets/{petId}/walks devuelve 201, 400 si el body es inválido o 404 si la mascota no existe.
- DELETE /pets/{petId}/walks/{walkId} devuelve 204 sin body o 404 si no existe la mascota o el paseo.

Separá los schemas de entrada y salida. PetInput no debe incluir id ni created_at. WalkInput no debe incluir id, pet_id ni created_at porque pet_id ya está identificado por el path.

Definí los parámetros de path como requeridos y reutilizables. No implementes servidor, base de datos ni código ejecutable. Devolvé solamente el contrato completo.
```

**Qué buscaba:** obtener el recorte mínimo completo, hacer visible la jerarquía y evitar desde el inicio que `pet_id` viajara tanto en el path como en el body.

## Prompt 2 - Tipar errores y cerrar validaciones

```text
Releé el contrato completo como si fueras a generar un cliente a partir de él.

Los responses 400 y 404 actualmente tienen sólo una descripción. Agregá un schema Error reutilizable y tipado con:

- code: string requerido, limitado a validation_error o not_found.
- message: string requerido.

Creá responses reutilizables BadRequest y NotFound con application/json, referencia a Error y un ejemplo para cada caso. Reemplazá las respuestas de error repetidas por referencias a esos componentes.

Además, hacé explícitas estas validaciones:

- Todos los objetos deben usar additionalProperties: false.
- name debe tener entre 1 y 80 caracteres.
- duration_minutes debe estar entre 1 y 1440.
- distance_km no puede ser negativa.
- notes admite hasta 500 caracteres.

No cambies los paths ni agregues endpoints. Conservá WalkInput sin pet_id porque la mascota ya está identificada en el path. Devolvé el openapi.yaml completo actualizado.
```

**Qué buscaba:** convertir las descripciones de error en un contrato consumible y evitar valores absurdos o propiedades desconocidas.

## Prompt 3 - Corregir el resultado del linter

```text
La validación con Redocly encontró que el contrato no declara servers, seguridad ni licencia.

Actualizalo con estas decisiones:

- Agregá como server ilustrativo https://api.example.test y aclarar que la API no está implementada.
- Declaralo público con security: [] en la raíz.
- Agregá una licencia llamada “Uso académico” con identifier LicenseRef-Academic-Use.

El linter también recomienda una respuesta 4xx en GET /pets. No inventes un 400 ni un 404 para esa operación: no recibe parámetros, es pública y una colección vacía debe responder 200 con un array vacío. Conservá esa advertencia como una decisión consciente.

No cambies los recursos, endpoints ni schemas ya definidos. Devolvé el openapi.yaml completo.
```

**Qué buscaba:** resolver los errores reales del validador sin deformar la semántica del endpoint de listado sólo para obtener cero advertencias.
