# Myutopia - n8n workflows base

Fecha de generación: 2026-06-18T17:39:04.796046Z

Estos archivos son plantillas importables para n8n con placeholders pendientes.
Están diseñados para la prueba técnica de seguimiento postdiagnóstico de leads con Jira, Gmail, IA y toques proactivos.

## Archivos

1. `myutopia_01_gmail_inbound_ia_jira.json`
   - Gmail Trigger
   - Búsqueda de lead en Jira por Email del lead
   - Clasificación con OpenAI o4-mini vía HTTP Request
   - Motor de reglas
   - Actualización de campos Jira
   - Comentario histórico
   - Transición de estado por nombre
   - Auto-respuestas seguras
   - Alertas al administrador

2. `myutopia_02_scheduler_toques.json`
   - Scheduler diario 09:00 Europe/Madrid
   - Busca tickets vencidos por `cf[10047] <= now()`
   - Ejecuta Toque 1, Toque 2, Toque 3
   - Toque 3 va por WhatsApp simulado
   - Cierre automático a Descartado tras silencio

3. `myutopia_03_whatsapp_inbound_simulado.json`
   - Webhook POST para simular mensajes WhatsApp
   - Búsqueda de lead por Teléfono WhatsApp
   - Clasificación IA
   - Actualización Jira
   - Alerta al administrador

## Placeholders a reemplazar

- `REEMPLAZAR_PROJECT_KEY`: clave del proyecto Jira.
- `REEMPLAZAR_ADMIN_EMAIL`: correo para alertas internas.
- `REEMPLAZAR_CREDENCIAL_GMAIL`: nombre/ID de la credencial Gmail en n8n.
- `REEMPLAZAR_CREDENCIAL_JIRA_BASIC`: nombre/ID de la credencial Jira Basic Auth en n8n.
- `REEMPLAZAR_OPENAI_API_KEY_O_USAR_CREDENCIAL`: usar credencial segura o variable de entorno; no dejar la API key embebida.
- `REEMPLAZAR_WHATSAPP_WEBHOOK_URL`: webhook de prueba o endpoint de proveedor WhatsApp.

## Mapeo Jira integrado

- Canal de seguimiento: customfield_10040
- Fecha diagnóstico: customfield_10041
- Tier propuesto: customfield_10042
- Agente IA: customfield_10043
- Importe setup: customfield_10044
- Nº de toque: customfield_10045
- Próxima acción: customfield_10046
- Fecha de vencimiento: customfield_10047
- Notas: customfield_10048
- Sentimiento: customfield_10049
- Resumen conversación: customfield_10050
- Fecha último contacto: customfield_10051
- Email del lead: customfield_10052
- Teléfono WhatsApp: customfield_10053
- Empresa: customfield_10054
- Nombre del contacto: customfield_10055
- Gmail Thread ID: customfield_10056
- Último Message ID procesado: customfield_10057
- Error técnico: customfield_10058

## Observaciones

Estas plantillas están pensadas para importarse, asignar credenciales y completar placeholders.
Antes de la demo, probar:
1. Actualización de campo texto en Jira.
2. Actualización de campo fecha.
3. Actualización de campo select.
4. Transiciones por nombre hacia En conversación, Propuesta aceptada, Reunión devs pendiente, A la espera, Firmado y Descartado.
5. Gmail inbound con un ticket que tenga Email del lead cargado.
6. Scheduler con Fecha de vencimiento vencida.
