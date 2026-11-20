# JOB HUNTER — Checkpoint 4 | IA Automation — Coderhouse

**Autor:** Ariel Aquino  
**Tecnología principal:** n8n (self-hosted)  
**Proyecto:** Automatización de búsqueda y gestión de postulaciones laborales mediante agentes de IA

## Descripción

JOB HUNTER es un sistema de automatización desarrollado en n8n para procesar oportunidades laborales recibidas por Gmail. Su arquitectura coordina agentes especializados que analizan la relevancia de las ofertas, evalúan su compatibilidad con un perfil profesional y redactan propuestas de postulación.

El sistema integra Airtable para el seguimiento de oportunidades, Google Sheets como soporte de memoria y Slack para las notificaciones. Las postulaciones se preparan como **borradores de Gmail**: el envío queda sujeto a revisión y decisión humana.

## Objetivo

Reducir el trabajo manual de revisión de correos laborales, evaluación de ofertas, redacción de postulaciones y seguimiento de oportunidades, manteniendo trazabilidad y control humano sobre las comunicaciones externas.

## Arquitectura del sistema

El proyecto se organiza en cuatro workflows independientes:

|Archivo|Componente|Responsabilidad|
|-|-|-|
|`checkpoint4\_ariel\_aquino.json`|Manager|Recibe y clasifica entradas, coordina los workflows especializados, consulta el CRM y gestiona borradores y notificaciones|
|`JOB\_HUNTER\_WORKER\_1\_ANALIZADOR.json`|Worker 1 — Analizador|Examina las ofertas laborales y determina su compatibilidad con el perfil profesional|
|`JOB\_HUNTER\_WORKER\_2\_REDACTOR.json`|Worker 2 — Redactor|Genera el asunto y el texto de una postulación a partir de la información disponible|
|`JOB\_HUNTER\_MEMORIA.json`|Memoria|Gestiona información contextual persistente mediante Google Sheets|

**Flujo funcional:** Gmail / Chat → Manager → análisis de la oferta → evaluación de compatibilidad → redacción de postulación → registro y seguimiento en Airtable → borrador de Gmail → notificación en Slack → revisión humana.

## Tecnologías e integraciones

|Tecnología|Aplicación en el proyecto|Autenticación|
|-|-|-|
|n8n|Orquestación de workflows y agentes|Instalación self-hosted|
|Gmail|Recepción de correos y creación de borradores|OAuth2|
|Airtable|CRM de oportunidades laborales|OAuth2|
|Slack|Notificaciones operativas|OAuth2|
|Google Sheets|Memoria y persistencia de contexto|OAuth2|
|Groq|Inferencia del modelo de lenguaje|Credencial de API|

## Lógica de funcionamiento y controles

* **Clasificación de entradas:** el Manager distingue los mensajes relevantes de las comunicaciones que no corresponden a oportunidades laborales.
* **Análisis de compatibilidad:** el Worker 1 evalúa las ofertas y produce una recomendación que orienta el procesamiento posterior.
* **Redacción asistida:** el Worker 2 prepara el asunto y el cuerpo del mensaje de postulación.
* **Seguimiento:** Airtable permite localizar oportunidades por identificador de correo, registrar información y actualizar estados.
* **Prevención de duplicados:** el sistema consulta el identificador del correo y comprueba la existencia de un borrador registrado antes de generar uno nuevo.
* **Validación previa:** la creación de borradores está condicionada a la presencia de los datos requeridos.
* **Supervisión humana:** el workflow crea borradores; no ejecuta el envío automático de postulaciones.

## Portabilidad de los archivos publicados

Los workflows se presentan en formato JSON exportado desde n8n. Para evitar la exposición de referencias específicas de la instalación, la versión pública no incluye credenciales y utiliza marcadores `CONFIGURAR\_\*` en determinados identificadores de recursos y subworkflows. Por este motivo, la importación en otra instancia requiere vincular las credenciales, los recursos externos y las referencias entre workflows correspondientes a ese entorno.

## Alcance y limitaciones

* La deduplicación está orientada al reprocesamiento secuencial de mensajes y no equivale a una garantía transaccional frente a ejecuciones simultáneas.
* Las ofertas descartadas por baja compatibilidad no se registran actualmente como oportunidades en Airtable.
* La validación del destinatario comprueba la estructura del correo electrónico, pero no verifica la identidad de la persona receptora.
* El envío definitivo de una postulación permanece fuera del flujo automatizado.
* La gestión de errores mediante un workflow independiente no forma parte de los cuatro archivos principales de esta publicación.
