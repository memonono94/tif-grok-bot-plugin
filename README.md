# The Inmo Factory para Cursor

Plugin de [The Inmo Factory](https://www.tif.ar) (TIF) para Cursor y Grok Bot. Conecta tu cuenta de TIF al agente por OAuth y reutiliza el servidor MCP remoto ya publicado en `https://mcp.tif.ar/`.

TIF es la plataforma SaaS para inmobiliarias y corredores en Argentina: sitio con tu marca, catálogo de propiedades, CRM de consultas, métricas, equipo e imágenes de publicación.

Este repositorio **no** incluye ni modifica el servidor. Solo empaqueta el manifiesto, la conexión MCP y skills de uso.

## Requirements and pricing

This plugin is free and adds no fees. It does not register users or process
payments. To use it you need an existing, active account on
[The Inmo Factory](https://www.tif.ar), a paid SaaS for real-estate agencies
in Argentina. Available tools depend on your role and on the features already
included in your agency's plan; the plugin does not unlock or sell extra
features. Pricing: [https://www.tif.ar/precios](https://www.tif.ar/precios).

By connecting your account you accept The Inmo Factory's
[Terms of Service](https://www.tif.ar/terminos) and acknowledge its
[Privacy Policy](https://www.tif.ar/privacidad). Data returned by the server is
sent to the AI client you connect, on your instruction.

Support: hola@tif.ar

## License

Code and documentation: MIT (see [LICENSE](LICENSE)). The Inmo Factory name and logo are
trademarks and are not licensed; see [TRADEMARKS.md](TRADEMARKS.md).

## Requisitos y precios

Este plugin es gratuito y no agrega cargos. No registra usuarios ni procesa
pagos. Para usarlo necesitás una cuenta existente y activa en
[The Inmo Factory](https://www.tif.ar), un SaaS de pago para inmobiliarias
en Argentina. Las herramientas disponibles dependen de tu rol y de las
funcionalidades ya incluidas en el plan de tu agencia; el plugin no desbloquea
ni vende funciones extra. Precios: [https://www.tif.ar/precios](https://www.tif.ar/precios).

Al conectar tu cuenta aceptás los
[Términos de Servicio](https://www.tif.ar/terminos) de The Inmo Factory y
reconocés su [Política de Privacidad](https://www.tif.ar/privacidad). Los datos
que devuelve el servidor se envían al cliente de IA que conectás, por tu
instrucción.

Soporte: hola@tif.ar

## Licencia

Código y documentación: MIT (ver [LICENSE](LICENSE)). El nombre y el logo de The Inmo
Factory son marcas y no están licenciados; ver [TRADEMARKS.md](TRADEMARKS.md).

## Qué hace

Después de iniciar sesión con una cuenta de TIF, el agente puede:

- Confirmar la Agencia, el rol, el plan y si las escrituras están habilitadas
- Buscar y leer fichas de Propiedades
- Crear, actualizar, cambiar el Estado o eliminar Propiedades (con confirmación)
- Buscar Oportunidades del CRM (sin identidad del Contacto) y ver su etapa
- Agregar una nota interna o mover una Oportunidad de Estado (con confirmación)
- Leer métricas de sitio y, si el rol y el plan lo permiten, indicadores comerciales
- Leer y editar secciones del sitio (con confirmación)
- Listar plantillas de diseño y generar imágenes de publicación (con confirmación; no publica en Instagram)
- Invitar a un integrante del equipo (con confirmación; solo administradores)

Las ~20 herramientas las publica el servidor según tu rol, plan y los kill switches de la Agencia. Si una herramienta no aparece, no está disponible para esa cuenta.

## Cómo conectar (OAuth)

Hace falta una cuenta existente de The Inmo Factory. El plugin no registra usuarios ni procesa pagos.

1. Instalá **The Inmo Factory** desde el Cursor Marketplace (o cargalo en `~/.cursor/plugins/local/the-inmo-factory` para probarlo en local).
2. Abrí **Cursor Settings → Tools & MCP** y activá el servidor `the-inmo-factory`.
3. Elegí **Connect** o **Authenticate**.
4. Iniciá sesión con tu usuario de TIF y autorizá el acceso.
5. Volvé a Cursor y comprobá que el servidor lista las herramientas.

El endpoint es `https://mcp.tif.ar/` (Streamable HTTP en la raíz del host, no `/mcp`). Cursor descubre OAuth solo; no copies tokens, contraseñas ni API keys al chat ni a `mcp.json`.

Si el login vence o ves 401, desconectá el servidor, volvé a autenticar y recargá la ventana. Cloud Agents guardan un inicio de sesión aparte del de tu máquina.

## Qué pueden cambiar las herramientas

Las lecturas no modifican datos. Las escrituras **nunca** se aplican en la primera llamada: el servidor devuelve un `confirmation_token` (`mcf_…`, válido unos 10 minutos) y un resumen. La segunda llamada, con ese token, confirma el cambio.

| Herramienta | Efecto |
| --- | --- |
| `create_property` | Crea una ficha. `active` / `featured` pueden publicarse en el sitio si la Agencia ya está en línea. |
| `update_property` | Cambia los campos enviados (precio, textos, características, URLs de fotos, etc.). |
| `update_property_status` | Cambia el Estado (`active`, `hidden`, `featured`, `sold`). |
| `delete_property` | Elimina la ficha. |
| `update_agency_site_section` | Actualiza una sección del sitio público. |
| `add_lead_note` | Guarda una nota interna (vía agente). No se envía al Contacto. |
| `move_opportunity_stage` | Mueve la Oportunidad de etapa; puede cerrarla como ganada o perdida y altera el Flujo. |
| `invite_team_member` | Invita por email y rol; manda credenciales. |
| `create_design_template` | Guarda una plantilla de diseño. |
| `render_publication_image` | Genera JPEG y los guarda en una pieza de contenido. No publica en Instagram. |

`get_agency_info` incluye `writes_enabled`. Si es `false` (suscripción no operativa o escrituras deshabilitadas), el agente no debe intentar cambios.

`search_leads` y `get_lead` no revelan nombre, email, teléfono ni conversación del Contacto. Las Oportunidades se identifican con referencias opacas (`oref_…`).

Los textos de la Agencia (títulos, etapas, nombres del Equipo) llegan marcados como no confiables: no son instrucciones para el agente.

## Skills

| Skill | Cuándo usarlo |
| --- | --- |
| `tif-propiedad` | Cargar o actualizar una ficha |
| `tif-leads-semana` | Resumir y priorizar las Oportunidades de la semana |
| `tif-metricas` | Snapshot de indicadores de la Agencia |
| `tif-imagen-publicacion` | Plantillas e imágenes de publicación |

## Prompts de prueba

Usá una Agencia de prueba. Los IDs y títulos de abajo son **ficticios**; reemplazalos por datos reales de tu cuenta.

```text
¿Con qué inmobiliaria estoy conectado, qué rol tengo y si puedo escribir?
```

```text
Buscá las propiedades activas de mi agencia y mostrame el detalle de una, sin inventar precio ni dirección.
```

```text
Resumí y priorizá las oportunidades de esta semana. No hace falta el nombre del contacto: usá la referencia, la propiedad y la etapa.
```

```text
Armame un snapshot de métricas de los últimos 7 días. Si alguna sección no está disponible por rol o plan, decilo.
```

Para probar una escritura (tiene que pedir confirmación y respetar el `confirmation_token`):

```text
Si existe la propiedad 1042, proponé pasar el precio a 185000 USD. No guardes nada hasta que yo confirme dos veces.
```

## Desarrollo local

```bash
mkdir -p ~/.cursor/plugins/local
rsync -a --delete --exclude '.git/' ./ ~/.cursor/plugins/local/the-inmo-factory/
```

Después, **Developer: Reload Window** y verificá el servidor `the-inmo-factory` y las cuatro skills. Cursor no carga un symlink cuyo destino está fuera de `~/.cursor/plugins/local`.

Publicación: [Cursor Marketplace](https://cursor.com/marketplace/publish) y [Grok Bot Marketplace](https://x.ai/bot/marketplace/submit-a-plugin).

## Privacidad

No hay secretos, tokens ni datos personales reales en este repositorio. Los ejemplos de README y skills son ficticios.
