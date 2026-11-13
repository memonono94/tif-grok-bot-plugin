---
name: tif-leads-semana
description: Resumir y priorizar las Oportunidades de la semana en The Inmo Factory. Usar cuando el usuario pide leads, consultas, pipeline o el trabajo comercial de esta semana.
---

# Oportunidades de la semana

`search_leads` y `get_lead` **no** devuelven identidad del Contacto (nombre, email, teléfono, Instagram), ni notas, ni la consulta original. Identificá cada Oportunidad por su referencia opaca (`oref_…`), la Propiedad, la etapa, la fecha, el medio y el estado de gestión. Nunca inventes un cliente.

## Cómo armar la semana

1. Si hace falta, `get_agency_info` para confirmar Agencia, rol y `writes_enabled`.
2. Calculá el período en zona `America/Argentina/Buenos_Aires`. Por defecto, lunes a hoy (o al domingo, si el usuario pide la semana calendario completa), en formato `AAAA-MM-DD`.
3. Llamá `search_leads` con `from` y `to`. Otros filtros reales: `property_id`, `pipeline_stage_id`, `management_status`, `medium`, `outcome` (`won`, `lost`, `canceled`).

`search_leads` no pagina un listado:

- `matches: 0` — no hay Oportunidades en ese recorte.
- `matches: 1` — una ficha operativa y su `reference`. Seguí con `get_lead` para etapas del Flujo y fechas.
- `ambiguous: true` — hay dos o más. Usá `narrow_by` (propiedades, etapas, estados, medios, fechas `occurred_on`) y volvé a llamar `search_leads` hasta obtener coincidencias individuales. No inventes una lista de contactos.

Si el recorte semanal queda ambiguo, acotá por día o por Propiedad y reuní las fichas de a una.

## Cómo priorizar

Ordená con lo que el servidor sí devuelve:

1. Abiertas (`open: true`, `outcome` nulo) por encima de ganadas, perdidas o canceladas.
2. Más recientes (`occurred_at`) primero.
3. Estado de gestión (`management_status_label`) y etapa (`stage`).
4. Propiedad asociada, si existe.

Proponé un próximo paso operativo (revisar etapa, dejar nota interna, pedir más filtros) sin fingir que viste al Contacto.

## Escritura (siempre confirmar)

`add_lead_note` y `move_opportunity_stage` modifican el CRM.

1. Pedí confirmación explícita en esta conversación. Para una nota, el usuario tiene que dictar el texto. Para un movimiento, el `pipeline_stage_id` tiene que salir de `get_lead` (mismo Flujo).
2. No escribas si `writes_enabled` es `false`.
3. Primera llamada sin `confirmation_token`: el servidor no guarda; devuelve `confirmation_token` y un resumen (Propiedad y etapas, sin Contacto).
4. Mostrá el resumen. Segunda llamada solo con ese token, y solo si el usuario confirma.
5. No reutilices tokens. El comentario de pérdida (`loss_comment`) no se devuelve. Una nota no se envía al Contacto.

Para un Estado de pérdida, `move_opportunity_stage` exige `loss_reason`; si es `other`, también `loss_comment`.

## Ejemplo ficticio

«Priorizá las consultas de esta semana.»

1. `search_leads` con `from: "2026-11-09"` y `to: "2026-11-13"`.
2. Si es ambiguo, repetí por día o por `property_id` de `narrow_by`.
3. Con cada `oref_…`, `get_lead`.
4. Devolvé una tabla: referencia, Propiedad, etapa, fecha, medio, estado, prioridad. Sin nombres de clientes.
