---
name: tif-imagen-publicacion
description: Generar o revisar imágenes de publicación de una Propiedad en The Inmo Factory. Usar cuando el usuario pide creatividades, plantillas de diseño o una pieza para redes a partir de una ficha.
---

# Imágenes de publicación

No publiques en Instagram: `render_publication_image` solo pinta JPEG y los guarda en una pieza de contenido del panel.

## Lectura

1. `get_agency_info` si no está claro el plan o `writes_enabled`.
2. `list_design_templates` para las plantillas ya guardadas.
3. `get_design_template` con el `id` real para ver formato, tamaño, preset e instrucciones.
4. `search_properties` / `get_property` para elegir la ficha. Las fotos que se pueden usar son las del panel, no archivos locales.

Si no hay plantillas, no inventes un diseño. Podés ofrecer `create_design_template` solo si el usuario pide guardar una plantilla nueva y el plan incluye imágenes sociales.

## Escritura (siempre confirmar)

`create_design_template` y `render_publication_image` escriben. La primera llamada **no** guarda ni pinta: devuelve `confirmation_token` (`mcf_…`) y un resumen. La segunda, solo con ese token, confirma.

Antes de llamarlas:

1. Confirmá con el usuario plantilla, Propiedad, formato y CTA.
2. No escribas si `writes_enabled` es `false` o si el plan no incluye `social_images`.
3. Mostrá el resumen del servidor y esperá una segunda confirmación humana antes de reenviar el token.
4. No reutilices tokens ni simules la confirmación.

### Render

`render_publication_image` pide `template_id` y `property_id`. Opcional: `cta_key` (`consultar`, `agendar_visita`, `ver_propiedad`, `escribinos`; por defecto `consultar`), `focus` (foco del texto) y `selected_image_indexes` (fotos de la Propiedad, orden del panel).

El resultado incluye URLs JPEG y, si el cliente lo muestra, las imágenes. No afirmes que ya se publicó en redes.

## Ejemplo ficticio

«Armame una pieza de Instagram para la propiedad 1042.»

1. `list_design_templates` y `get_property` con `property_id: 1042`.
2. Pedí que elija plantilla y CTA (si no hay preferencia, `consultar`).
3. `render_publication_image` sin token.
4. Mostrá el resumen (plantilla, propiedad, formato, CTA).
5. Solo si confirma, segunda llamada con `confirmation_token`.
