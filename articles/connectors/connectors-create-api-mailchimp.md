<properties
pageTitle="MailChimp | Microsoft Azure"
description="Crear aplicaciones de lógica con el servicio de aplicación de Azure. MailChimp es un servicio de SaaS que permite a las empresas administrar y automatizar las actividades de marketing de correo electrónico, incluido el envío de correos electrónicos de marketing, mensajes automatizados y campañas destinadas."
services="logic-apps"
documentationCenter=".net,nodejs,java"  
authors="msftman"
manager="erikre"
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-mailchimp-connector"></a>Empezar a trabajar con el conector de MailChimp

MailChimp es un servicio de SaaS que permite a las empresas administrar y automatizar las actividades de marketing de correo electrónico, incluido el envío de correos electrónicos de marketing, mensajes automatizados y campañas destinadas.


>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2015-08-01-vista previa de aplicaciones de lógica.

Puede empezar creando una aplicación de lógica ahora, vea [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones

El conector de MailChimp puede utilizarse como una acción; tiene desencadenadores. Todos los conectores admiten datos en formatos XML y JSON.

 El conector MailChimp tiene las siguientes acciones o desencadenadores disponibles:

### <a name="mailchimp-actions"></a>Acciones de MailChimp
Puede realizar estas acciones:

|Acción|Descripción|
|--- | ---|
|[newcampaign](connectors-create-api-mailchimp.md#newcampaign)|Crear una nueva campaña basada en un tipo de campaña, la lista de destinatarios y configuración de campaña (línea de asunto, título, from_name y reply_to)|
|[NewList](connectors-create-api-mailchimp.md#newlist)|Crear una lista nueva en su cuenta de MailChimp|
|[AddMember](connectors-create-api-mailchimp.md#addmember)|Agregar o actualizar a un miembro de la lista|
|[RemoveMember](connectors-create-api-mailchimp.md#removemember)|Eliminar a un miembro de una lista.|
|[updatemember](connectors-create-api-mailchimp.md#updatemember)|Actualizar la información de un miembro de la lista específica|
### <a name="mailchimp-triggers"></a>MailChimp desencadenadores
Puede escuchar para estos eventos:

|Desencadenador | Descripción|
|--- | ---|
|Cuando se ha agregado un miembro a una lista|Activa un flujo de trabajo cuando se ha agregado un miembro nuevo a una lista|
|Cuando se crea una nueva lista|Activa un flujo de trabajo cuando se crea una nueva lista|


## <a name="create-a-connection-to-mailchimp"></a>Crear una conexión a MailChimp
Para crear aplicaciones de lógica con MailChimp, primero debe crear una **conexión** y proporcionar los detalles de las siguientes propiedades:

|(Propiedad)| Obligatorio|Descripción|
| ---|---|---|
|Token|Sí|Proporcionar credenciales MailChimp|

>[AZURE.INCLUDE [Steps to create a connection to MailChimp](../../includes/connectors-create-api-mailchimp.md)]

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones de lógica.

## <a name="reference-for-mailchimp"></a>Referencia para MailChimp
Se aplica a la versión: 1.0

## <a name="newcampaign"></a>newcampaign
Nueva campaña: Crear una nueva campaña basada en un tipo de campaña, la lista de destinatarios y configuración de campaña (línea de asunto, título, from_name y reply_to)

```POST: /campaigns```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|newCampaignRequest| |Sí|cuerpo|Ninguno|Objeto JSON para enviar en el cuerpo con los parámetros de solicitud de campaña nueva|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación.|


## <a name="newlist"></a>NewList
Nueva lista: Crear una lista nueva en su cuenta de MailChimp

```POST: /lists```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|newListRequest| |Sí|cuerpo|Ninguno|Objeto JSON para enviar en el cuerpo con los parámetros de solicitud de campaña nueva|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación.|


## <a name="addmember"></a>AddMember
Agregar miembros a la lista: agregar o actualizar un miembro de la lista

```POST: /lists/{list_id}/members```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list_id|cadena|Sí|ruta de acceso|Ninguno|Identificador exclusivo de la lista|
|newMemberInList| |Sí|cuerpo|Ninguno|Objeto JSON para enviar en el cuerpo con la nueva información de miembro|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación.|


## <a name="removemember"></a>RemoveMember
Quitar miembro de lista: eliminar un miembro de una lista.

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list_id|cadena|Sí|ruta de acceso|Ninguno|Identificador exclusivo de la lista|
|member_email|cadena|Sí|ruta de acceso|Ninguno|La dirección de correo electrónico de los miembros para eliminar|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación.|


## <a name="updatemember"></a>updatemember
Actualizar la información del miembro: actualizar la información de un miembro de la lista específica

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list_id|cadena|Sí|ruta de acceso|Ninguno|Identificador exclusivo de la lista|
|member_email|cadena|Sí|ruta de acceso|Ninguno|La dirección de correo electrónico del miembro actualizar|
|updateMemberInListRequest| |Sí|cuerpo|Ninguno|Objeto JSON para enviar en el cuerpo con la información actualizada de miembro|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación.|


## <a name="onmembersubscribed"></a>OnMemberSubscribed
Cuando se ha agregado un miembro a una lista: activa un flujo de trabajo cuando se ha agregado un miembro nuevo a una lista

```GET: /trigger/lists/{list_id}/members```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list_id|cadena|Sí|ruta de acceso|Ninguno|Identificador exclusivo de la lista|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|202|Aceptado|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación.|


## <a name="oncreatelist"></a>OnCreateList
Cuando se crea una nueva lista: Active un flujo de trabajo cuando se crea una nueva lista

```GET: /trigger/lists```

No hay parámetros para esta llamada
#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|202|Aceptado|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación.|


## <a name="object-definitions"></a>Definiciones de objeto

### <a name="newcampaignrequest"></a>NewCampaignRequest


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|tipo|cadena|Sí |
|destinatarios|no definido|Sí |
|Configuración|no definido|Sí |
|variate_settings|no definido|No |
|seguimiento|no definido|No |
|rss_opts|no definido|No |
|social_card|no definido|No |



### <a name="recipient"></a>Destinatario


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|list_id|cadena|Sí |
|segment_opts|no definido|No |



### <a name="settings"></a>Configuración


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|subject_line|cadena|Sí |
|título|cadena|No |
|from_name|cadena|Sí |
|reply_to|cadena|Sí |
|use_conversation|valor booleano|No |
|to_name|cadena|No |
|folder_id|entero|No |
|autenticar|valor booleano|No |
|auto_footer|valor booleano|No |
|inline_css|valor booleano|No |
|auto_tweet|valor booleano|No |
|auto_fb_post|matriz|No |
|fb_comments|valor booleano|No |



### <a name="variatesettings"></a>Variate_Settings


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|winner_criteria|cadena|No |
|tiempoespera|entero|No |
|test_size|entero|No |
|subject_lines|matriz|No |
|send_times|matriz|No |
|from_names|matriz|No |
|reply_to_addresses|matriz|No |



### <a name="tracking"></a>Seguimiento


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|se abre|valor booleano|No |
|html_clicks|valor booleano|No |
|text_clicks|valor booleano|No |
|goal_tracking|valor booleano|No |
|ecomm360|valor booleano|No |
|google_analytics|cadena|No |
|clicktale|cadena|No |
|Salesforce|no definido|No |
|highrise|no definido|No |
|Cápsula|no definido|No |



### <a name="rssopts"></a>RSS_Opts


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|feed_url|cadena|No |
|frecuencia|cadena|No |
|constrain_rss_img|cadena|No |
|programación|no definido|No |



### <a name="socialcard"></a>Social_Card


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|image_url|cadena|No |
|Descripción|cadena|No |
|título|cadena|No |



### <a name="segmentopts"></a>Segment_Opts


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|saved_segment_id|entero|No |
|coincidir|cadena|No |



### <a name="salesforce"></a>Salesforce


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|campaña|valor booleano|No |
|notas|valor booleano|No |



### <a name="highrise"></a>Highrise


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|campaña|valor booleano|No |
|notas|valor booleano|No |



### <a name="capsule"></a>Cápsula


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|notas|valor booleano|No |



### <a name="schedule"></a>Programación


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|hora|entero|No |
|daily_send|no definido|No |
|weekly_send_day|cadena|No |
|monthly_send_date|número|No |



### <a name="dailysend"></a>Daily_Send


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|domingo|valor booleano|No |
|lunes|valor booleano|No |
|Martes|valor booleano|No |
|Miércoles|valor booleano|No |
|Jueves|valor booleano|No |
|viernes|valor booleano|No |
|sábado|valor booleano|No |



### <a name="campaignresponsemodel"></a>CampaignResponseModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|cadena|No |
|tipo|cadena|No |
|create_time|cadena|No |
|archive_url|cadena|No |
|estado|cadena|No |
|emails_sent|entero|No |
|send_time|cadena|No |
|content_type|cadena|No |
|destinatario|matriz|No |
|Configuración|no definido|No |
|variate_settings|no definido|No |
|seguimiento|no definido|No |
|rss_opts|no definido|No |
|ab_split_opts|no definido|No |
|social_card|no definido|No |
|report_summary|no definido|No |
|delivery_status|no definido|No |
|_links|matriz|No |



### <a name="absplitopts"></a>AB_Split_Opts


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|split_test|cadena|No |
|pick_winner|cadena|No |
|wait_units|cadena|No |
|tiempoespera|entero|No |
|split_size|entero|No |
|from_name_a|cadena|No |
|from_name_b|cadena|No |
|reply_email_a|cadena|No |
|reply_email_b|cadena|No |
|subject_a|cadena|No |
|subject_b|cadena|No |
|send_time_a|cadena|No |
|send_time_b|cadena|No |
|send_time_winner|cadena|No |



### <a name="reportsummary"></a>Report_Summary


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|se abre|entero|No |
|unique_opens|entero|No |
|open_rate|número|No |
|Haga clic en|entero|No |
|subscriber_clicks|número|No |
|click_rate|número|No |



### <a name="deliverystatus"></a>Delivery_Status


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|habilitado|valor booleano|No |
|can_cancel|valor booleano|No |
|estado|cadena|No |
|emails_sent|entero|No |
|emails_canceled|entero|No |



### <a name="link"></a>Vínculo


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|rel|cadena|No |
|referencia de|cadena|No |
|método|cadena|No |
|targetSchema|cadena|No |
|esquema|cadena|No |



### <a name="newlistrequest"></a>NewListRequest


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|nombre|cadena|Sí |
|Póngase en contacto con|no definido|Sí |
|permission_reminder|cadena|Sí |
|use_archive_bar|valor booleano|No |
|campaign_defaults|no definido|Sí |
|notify_on_subscribe|cadena|No |
|notify_on_unsubscribe|cadena|No |
|email_type_option|valor booleano|Sí |
|visibilidad|cadena|No |



### <a name="contact"></a>Contacto


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|compañía|cadena|Sí |
|dirección 1|cadena|Sí |
|dirección 2|cadena|No |
|Ciudad|cadena|Sí |
|estado|cadena|Sí |
|ZIP|cadena|Sí |
|país|cadena|Sí |
|teléfono|cadena|Sí |



### <a name="campaigndefaults"></a>Campaign_Defaults


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|from_name|cadena|Sí |
|from_email|cadena|Sí |
|Asunto|cadena|No |
|idioma|cadena|Sí |



### <a name="createnewlistresponsemodel"></a>CreateNewListResponseModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|cadena|Sí |
|nombre|cadena|Sí |
|Póngase en contacto con|no definido|Sí |
|permission_reminder|cadena|Sí |
|use_archive_bar|valor booleano|No |
|campaign_defaults|no definido|Sí |
|notify_on_subscribe|cadena|No |
|notify_on_unsubscribe|cadena|No |
|Date_Created|cadena|No |
|list_rating|entero|No |
|email_type_option|valor booleano|Sí |
|subscribe_url_short|cadena|No |
|subscribe_url_long|cadena|No |
|beamer_address|cadena|No |
|visibilidad|cadena|No |
|módulos|matriz|No |
|estadísticas|no definido|No |
|_links|matriz|No |



### <a name="stats"></a>Estadísticas


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|member_count|entero|No |
|unsubscribe_count|entero|No |
|cleaned_count|entero|No |
|member_count_since_send|entero|No |
|unsubscribe_count_since_send|entero|No |
|cleaned_count_since_send|entero|No |
|campaign_count|entero|No |
|campaign_last_sent|entero|No |
|merge_field_count|entero|No |
|avg_sub_rate|número|No |
|avg_unsub_rate|número|No |
|target_sub_rate|número|No |
|open_rate|número|No |
|click_rate|número|No |
|last_sub_date|cadena|No |
|last_unsub_date|cadena|No |



### <a name="getlistsresponsemodel"></a>GetListsResponseModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|listas|matriz|No |
|total_items|entero|No |



### <a name="newmemberinlistrequest"></a>NewMemberInListRequest


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|email_type|cadena|No |
|estado|cadena|Sí |
|merge_fields|no definido|No |
|intereses|cadena|No |
|idioma|cadena|No |
|VIP|valor booleano|No |
|ubicación|no definido|No |
|Email_Address|cadena|Sí |



### <a name="firstandlastname"></a>FirstAndLastName


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|FNAME|cadena|No |
|APELLIDOS|cadena|No |



### <a name="location"></a>Ubicación


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|latitud|número|No |
|latitud|número|No |



### <a name="memberresponsemodel"></a>MemberResponseModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|cadena|No |
|Email_Address|cadena|No |
|unique_email_id|cadena|No |
|email_type|cadena|No |
|estado|cadena|No |
|merge_fields|no definido|No |
|intereses|cadena|No |
|estadísticas|no definido|No |
|ip_signup|cadena|No |
|timestamp_signup|cadena|No |
|ip_opt|cadena|No |
|timestamp_opt|cadena|No |
|member_rating|entero|No |
|last_changed (últimoCambio)|cadena|No |
|idioma|cadena|No |
|VIP|valor booleano|No |
|email_client|cadena|No |
|ubicación|no definido|No |
|last_note|no definido|No |
|list_id|cadena|No |
|_links|matriz|No |



### <a name="lastnote"></a>Last_Note


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|note_id|entero|No |
|created_at|cadena|No |
|created_by|cadena|No |
|Nota|cadena|No |



### <a name="getallmembersresponsemodel"></a>GetAllMembersResponseModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|miembros|matriz|No |
|list_id|cadena|No |
|total_items|entero|No |



### <a name="object"></a>Objeto






### <a name="updatememberinlistrequest"></a>UpdateMemberInListRequest


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Email_Address|cadena|No |
|email_type|cadena|No |
|estado|cadena|Sí |
|merge_fields|no definido|No |
|intereses|cadena|No |
|idioma|cadena|No |
|VIP|valor booleano|No |
|ubicación|no definido|No |



### <a name="getmembersresponsemodel"></a>GetMembersResponseModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|miembros|matriz|No |
|list_id|cadena|No |
|total_items|entero|No |



### <a name="adduserresponsemodel"></a>AddUserResponseModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|cadena|Sí |
|Email_Address|cadena|Sí |
|unique_email_id|cadena|No |
|email_type|cadena|No |
|estado|cadena|No |
|merge_fields|no definido|Sí |
|intereses|cadena|No |
|estadísticas|no definido|No |
|ip_signup|cadena|No |
|timestamp_signup|cadena|No |
|ip_opt|cadena|No |
|timestamp_opt|cadena|No |
|member_rating|entero|No |
|last_changed (últimoCambio)|cadena|No |
|idioma|cadena|No |
|VIP|valor booleano|No |
|email_client|cadena|No |
|ubicación|no definido|No |
|last_note|no definido|No |
|list_id|cadena|No |
|_links|matriz|No |


## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)
