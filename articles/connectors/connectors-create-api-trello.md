<properties
pageTitle="Trello | Microsoft Azure"
description="Crear aplicaciones de lógica con el servicio de aplicación de Azure. Trello ofrece perspectiva sobre todos los proyectos, en el trabajo y en casa.  Es una forma sencilla, libre, flexible y visual para administrar sus proyectos y organizar nada.  Conectarse a Trello para administrar los paneles, listas y tarjetas"
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

# <a name="get-started-with-the-trello-connector"></a>Empezar a trabajar con el conector de Trello

Trello ofrece perspectiva sobre todos los proyectos, en el trabajo y en casa.  Es una forma sencilla, libre, flexible y visual para administrar sus proyectos y organizar nada.  Conectarse a Trello para administrar los paneles, listas y tarjetas.

>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2015-08-01-vista previa de aplicaciones de lógica. 

Puede empezar creando una aplicación de lógica ahora, vea [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones

El conector de Trello puede utilizarse como una acción; tiene desencadenadores. Todos los conectores admiten datos en formatos XML y JSON. 

 El conector Trello tiene las siguientes acciones o desencadenadores disponibles:

### <a name="trello-actions"></a>Acciones de Trello
Puede realizar estas acciones:

|Acción|Descripción|
|--- | ---|
|[ListCards](connectors-create-api-trello.md#listcards)|Tarjetas de lista en el panel|
|[GetCard](connectors-create-api-trello.md#getcard)|Obtener tarjeta por Id.|
|[UpdateCard](connectors-create-api-trello.md#updatecard)|Tarjeta de actualización|
|[DeleteCard](connectors-create-api-trello.md#deletecard)|Eliminar tarjeta|
|[CreateCard](connectors-create-api-trello.md#createcard)|Crea una tarjeta nueva en su cuenta de trello|
|[ListBoards](connectors-create-api-trello.md#listboards)|Paneles de la lista|
|[GetBoard](connectors-create-api-trello.md#getboard)|Obtiene el panel por Id.|
|[ListLists](connectors-create-api-trello.md#listlists)|Listas de tarjeta en el panel de lista|
|[GetList](connectors-create-api-trello.md#getlist)|Obtiene la lista por Id.|
### <a name="trello-triggers"></a>Trello desencadenadores
Puede escuchar para estos eventos:

|Desencadenador | Descripción|
|--- | ---|
|Cuando se agrega una tarjeta nueva a un panel|Activa un flujo cuando se agrega una tarjeta nueva a un panel|
|Cuando se agrega una tarjeta nueva a una lista|Activa un flujo cuando se agrega una tarjeta nueva a una lista|


## <a name="create-a-connection-to-trello"></a>Crear una conexión a Trello
Para crear aplicaciones de lógica con Trello, primero debe crear una **conexión** y proporcionar los detalles de las siguientes propiedades: 

|(Propiedad)| Obligatorio|Descripción|
| ---|---|---|
|Token|Sí|Proporcionar credenciales Trello|
Después de crear la conexión, puede usarlo para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo. 

>[AZURE.INCLUDE [Steps to create a connection to Trello](../../includes/connectors-create-api-trello.md)]

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones de lógica.

## <a name="reference-for-trello"></a>Referencia para Trello
Se aplica a la versión: 1.0

## <a name="onnewcardinboard"></a>OnNewCardInBoard
Cuando se agrega una tarjeta nueva a un panel: activa un flujo cuando se agrega una tarjeta nueva a un panel 

```GET: /trigger/boards/{board_id}/cards``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board_id|cadena|Sí|ruta de acceso|Ninguno|Identificador exclusivo de los paneles para capturar tarjetas en|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación|


## <a name="onnewcardinlist"></a>OnNewCardInList
Cuando se agrega una tarjeta nueva a una lista: activa un flujo cuando se agrega una tarjeta nueva a una lista 

```GET: /trigger/lists/{list_id}/cards``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board_id|cadena|Sí|consulta|Ninguno|Identificador exclusivo de los paneles para capturar tarjetas en|
|list_id|cadena|Sí|ruta de acceso|Ninguno|Identificador exclusivo de la lista para capturar tarjetas en|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación|


## <a name="listcards"></a>ListCards
Tarjetas en el panel de lista: tarjetas en el panel de lista 

```GET: /boards/{board_id}/cards``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board_id|cadena|Sí|ruta de acceso|Ninguno|Id. del panel para capturar todas las cartas|
|acciones|cadena|No|consulta|Ninguno|Lista de las acciones para devolver. Especificar 'all' o una lista de separados por comas de valores válidos|
|datos adjuntos|valor booleano|No|consulta|Ninguno|Mostrar datos adjuntos|
|attachment_fields|cadena|No|consulta|Ninguno|Lista de los campos de datos adjuntos para devolver. Especificar 'all' o una lista de separados por comas de valores válidos|
|etiquetas adhesivas|valor booleano|No|consulta|Ninguno|Mostrar etiquetas|
|miembros|valor booleano|No|consulta|Ninguno|Mostrar miembros|
|memeber_fields|cadena|No|consulta|Ninguno|Lista de los campos de miembro para devolver. Especificar 'all' o una lista de separados por comas de valores válidos|
|CheckItemStates|valor booleano|No|consulta|Ninguno|Devolver los Estados de tarjeta|
|Listas de comprobación|cadena|No|consulta|Ninguno|Mostrar listas de comprobación|
|límite|entero|No|consulta|Ninguno|El número máximo de resultados para devolver, entre 1 y 1000|
|desde|cadena|No|consulta|Ninguno|Buscar todas las cartas después de esa fecha|
|antes de|cadena|No|consulta|Ninguno|Buscar todas las tarjetas antes de esta fecha|
|filtro|cadena|No|consulta|Ninguno|Filtrar la respuesta|
|campos|cadena|No|consulta|Ninguno|Lista de los campos de la tarjeta para devolver. Especificar 'all' o una lista de separados por comas de valores válidos|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación|


## <a name="getcard"></a>GetCard
Obtener tarjeta por id: Get tarjeta por Id. 

```GET: /cards/{card_id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board_id|cadena|Sí|consulta|Ninguno|Id. del panel para capturar tarjetas en|
|card_id|cadena|Sí|ruta de acceso|Ninguno|Identificador de la tarjeta para capturar|
|acciones|cadena|No|consulta|Ninguno|Lista de las acciones para devolver. Especificar 'all' o una lista de separados por comas de valores válidos|
|actions_entities|valor booleano|No|consulta|Ninguno|Devolver entidades de acción|
|actions_display|valor booleano|No|consulta|Ninguno|Muestra de acción de devolución|
|actions_limit|entero|No|consulta|Ninguno|Número máximo de acciones para devolver|
|action_fields|cadena|No|consulta|Ninguno|Lista de campos de acción para devolver por cada acción. Especificar 'all' o una lista de separados por comas de valores válidos|
|action_memberCreator_fields|cadena|No|consulta|Ninguno|Lista de campos de creador de miembro de acción para devolver. Especificar 'all' o una lista de separados por comas de valores válidos|
|datos adjuntos|valor booleano|No|consulta|Ninguno|Devolver datos adjuntos|
|attachement_fields|cadena|No|consulta|Ninguno|Lista de campos de datos adjuntos para devolver para cada archivo adjunto. Especificar 'all' o una lista de separados por comas de valores válidos|
|miembros|valor booleano|No|consulta|Ninguno|Devolver miembros|
|member_fields|cadena|No|consulta|Ninguno|Lista de campos de miembro para devolver para cada miembro. Especificar 'all' o una lista de separados por comas de valores válidos|
|membersVoted|valor booleano|No|consulta|Ninguno|Devolver miembros elegida como "|
|memberVoted_fields|cadena|No|consulta|Ninguno|Lista de campos de miembro elegida como "para devolver para cada miembro voted. Especificar 'all' o una lista de separados por comas de valores válidos|
|checkItemStates|valor booleano|No|consulta|Ninguno|Estados de tarjeta devuelto|
|checkItemState_fields|cadena|No|consulta|Ninguno|Lista de campos de estado para devolver para cada uno del estado del elemento de tarjeta. Especificar 'all' o una lista de separados por comas de valores válidos|
|listas de comprobación|cadena|No|consulta|Ninguno|Devolver listas de comprobación|
|checklist_fields|cadena|No|consulta|Ninguno|Lista de campos de la lista de comprobación para devolver para cada lista de comprobación. Especificar 'all' o una lista de separados por comas de valores válidos|
|panel|valor booleano|No|consulta|Ninguno|Devolver los paneles que pertenece la tarjeta|
|board_fields|cadena|No|consulta|Ninguno|Lista de los campos de panel para devolver. Especificar 'all' o una lista de separados por comas de valores válidos|
|lista|valor booleano|No|consulta|Ninguno|Volver a la lista a la que pertenece la tarjeta|
|list_fields|cadena|No|consulta|Ninguno|Lista de la lista de campos para volver. Especificar 'all' o una lista de separados por comas de valores válidos|
|etiquetas adhesivas|valor booleano|No|consulta|Ninguno|Etiquetas de remite|
|sticker_fields|cadena|No|consulta|Ninguno|Lista de los campos de etiqueta para devolver para cada etiqueta. Especificar 'all' o una lista de separados por comas de valores válidos|
|campos|cadena|No|consulta|Ninguno|Lista de los campos de la tarjeta para devolver. Especificar 'all' o una lista de separados por comas de valores válidos|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación|


## <a name="updatecard"></a>UpdateCard
Tarjeta de actualización: tarjeta de actualización 

```PUT: /cards/{card_id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board_id|cadena|Sí|consulta|Ninguno|Id. del panel para capturar tarjetas de|
|card_id|cadena|Sí|ruta de acceso|Ninguno|Identificador de la tarjeta de actualizar|
|updateCard| |Sí|cuerpo|Ninguno|Valores de la tarjeta actualizado|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación|


## <a name="deletecard"></a>DeleteCard
Eliminar tarjeta: Eliminar tarjeta 

```DELETE: /cards/{card_id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board_id|cadena|Sí|consulta|Ninguno|Id. del panel para capturar tarjetas de|
|card_id|cadena|Sí|ruta de acceso|Ninguno|Identificador de la tarjeta para eliminar|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación|


## <a name="createcard"></a>CreateCard
Crear tarjeta: crea una tarjeta nueva en su cuenta de trello 

```POST: /cards``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board_id|cadena|Sí|consulta|Ninguno|Identificador exclusivo de los paneles para crear tarjetas en|
|newCard| |Sí|cuerpo|Ninguno|Tarjeta de nuevo para agregar al panel trello|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación|


## <a name="listboards"></a>ListBoards
Lista de los paneles: lista de los paneles 

```GET: /member/me/boards``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|filtro|cadena|No|consulta|Ninguno|Filtros de la lista Aplicar a los resultados de panel. Especificar 'all' o una lista de separados por comas de valores válidos|
|campos|cadena|No|consulta|Ninguno|Lista de los campos de panel para devolver. Especificar 'all' o una lista de separados por comas de valores válidos|
|acciones|cadena|No|consulta|Ninguno|Lista de los campos de acción para devolver. Especificar 'all' o una lista de separados por comas de valores válidos|
|actions_entities|valor booleano|No|consulta|Ninguno|Devolver entidades de acción|
|actions_limit|entero|No|consulta|Ninguno|Número máximo de acciones para devolver|
|actions_format|cadena|No|consulta|Ninguno|Especificar el formato de las acciones para devolver|
|actions_since|cadena|No|consulta|Ninguno|Acciones de devolución después de la fecha especificada|
|action_fields|cadena|No|consulta|Ninguno|Lista de los campos de la acción para devolver. Especificar 'all' o una lista de separados por comas de valores válidos|
|pertenencias a grupos|cadena|No|consulta|Ninguno|Especificar información de pertenencia a devolver. Especificar 'all' o una lista de separados por comas de valores válidos|
|organización|valor booleano|No|consulta|Ninguno|Especificar para devolver información de organización|
|organization_fields|cadena|No|consulta|Ninguno|Lista de los campos de la organización para devolver. Especificar 'all' o una lista de separados por comas de valores válidos|
|listas|cadena|No|consulta|Ninguno|Especifique si desea volver a las listas que pertenecen a los paneles|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación|


## <a name="getboard"></a>GetBoard
Obtiene el panel por Id: Obtiene panel por Id. 

```GET: /boards/{board_id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board_id|cadena|Sí|ruta de acceso|Ninguno|Identificador exclusivo de los paneles para obtener|
|acciones|cadena|No|consulta|Ninguno|Lista de las acciones para devolver. Especificar 'all' o una lista de separados por comas de valores válidos|
|action_entities|valor booleano|No|consulta|Ninguno|Especificar si se devuelven entidades de acción|
|actions_display|valor booleano|No|consulta|Ninguno|Especifique si desea volver a mostrar acciones|
|actions_format|cadena|No|consulta|Ninguno|Especificar el formato de las acciones para devolver|
|actions_since|cadena|No|consulta|Ninguno|Devolver solo las acciones después de esa fecha|
|actions_limit|entero|No|consulta|Ninguno|Número máximo de acciones para devolver|
|action_fields|cadena|No|consulta|Ninguno|Lista de los campos para devolver con cada campo. Especificar 'all' o una lista de separados por comas de valores válidos|
|action_memeber|valor booleano|No|consulta|Ninguno|Especifique si desea devolver a elementos de acción|
|action_member_fields|cadena|No|consulta|Ninguno|Lista de los campos de miembro para volver a cada miembro de acción. Especificar 'all' o una lista de separados por comas de valores válidos|
|action_memberCreator|valor booleano|No|consulta|Ninguno|Especifique si desea volver creador de miembro de acción|
|action_memberCreator_fields|cadena|No|consulta|Ninguno|Lista de los campos de creador de miembro de acción para devolver. Especificar 'all' o una lista de separados por comas de valores válidos|
|tarjetas|cadena|No|consulta|Ninguno|Especificar las tarjetas para devolver|
|card_fields|cadena|No|consulta|Ninguno|Lista de los campos para devolver para cada tarjeta. Especificar 'all' o una lista de separados por comas de valores válidos|
|card_attachments|valor booleano|Sí|consulta|Ninguno|Especifique si desea devolver datos adjuntos en las tarjetas|
|card_attachment_fields|cadena|No|consulta|Ninguno|Lista de los campos de datos adjuntos para devolver para cada archivo adjunto. Especificar 'all' o una lista de separados por comas de valores válidos|
|card_checklists|cadena|No|consulta|Ninguno|Especifique las listas de comprobación para devolver para cada tarjeta|
|card_stickers|valor booleano|No|consulta|Ninguno|Especificar si se devuelven etiquetas adhesivas|
|boardStarts|cadena|No|consulta|Ninguno|Especificar las estrellas de panel para devolver|
|etiquetas|cadena|No|consulta|Ninguno|Especifique las etiquetas para devolver|
|label_fields|cadena|No|consulta|Ninguno|Lista de los campos de etiqueta para devolver para cada etiqueta. Especificar 'all' o una lista de separados por comas de valores válidos|
|labels_limits|entero|No|consulta|Ninguno|Número máximo de etiquetas para devolver|
|listas|cadena|No|consulta|Ninguno|Especificar las listas para devolver|
|list_fields|cadena|No|consulta|Ninguno|Lista de los campos de la lista para devolver para cada lista. Especificar 'all' o una lista de separados por comas de valores válidos|
|pertenencias a grupos|cadena|No|consulta|Ninguno|Lista de las pertenencias a devolver. Especificar 'all' o una lista de separados por comas de valores válidos|
|memberships_member|valor booleano|No|consulta|Ninguno|Especifique si desea volver a la pertenencia|
|memberships_member_fields|cadena|No|consulta|Ninguno|Lista de los campos de miembro para devolver para cada miembro de pertenencia. Especificar 'all' o una lista de separados por comas de valores válidos|
|miembros|cadena|No|consulta|Ninguno|Lista de los miembros para devolver|
|member_fields|cadena|No|consulta|Ninguno|Lista de los campos de miembro para devolver para cada miembro. Especificar 'all' o una lista de separados por comas de valores válidos|
|membersInvited|cadena|No|consulta|Ninguno|Especifique los usuarios invitados para devolver|
|membersInvited_fields|cadena|No|consulta|Ninguno|Lista de los campos para devolver para cada uno. Especificar 'all' o una lista de separados por comas de valores válidos|
|listas de comprobación|cadena|No|consulta|Ninguno|Especifique las listas de comprobación para devolver|
|checklist_fields|cadena|No|consulta|Ninguno|Lista de los campos de la lista de comprobación para devolver para cada lista de comprobación. Especificar 'all' o una lista de separados por comas de valores válidos|
|organización|valor booleano|No|consulta|Ninguno|Especifique si desea volver a la información de la organización|
|organization_fields|cadena|No|consulta|Ninguno|Lista de los campos de la organización para devolver para cada organización. Especificar 'all' o una lista de separados por comas de valores válidos|
|organization_memberships|cadena|No|consulta|Ninguno|Lista de los miembros de la organización para devolver. Especificar 'all' o una lista de separados por comas de valores válidos|
|myPerfs|valor booleano|No|consulta|Ninguno|Especifique si desea volver a mi perfs|
|campos|cadena|No|consulta|Ninguno|Lista de los campos para devolver. Especificar 'all' o una lista de separados por comas de valores válidos|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación|


## <a name="listlists"></a>ListLists
Listas de tarjeta en el panel de lista: listas de tarjeta en el panel de lista 

```GET: /boards/{board_id}/lists``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board_id|cadena|Sí|ruta de acceso|Ninguno|Identificador exclusivo de los paneles para obtener listas|
|tarjetas|cadena|No|consulta|Ninguno|Especificar las tarjetas para devolver|
|card_fields|cadena|No|consulta|Ninguno|Los campos de la tarjeta para devolver a partir de la lista. Especificar 'all' o una lista de separados por comas de valores válidos|
|filtro|cadena|No|consulta|Ninguno|Especifique la propiedad de filtro para las listas|
|campos|cadena|No|consulta|Ninguno|Lista de los campos para devolver. Especificar 'all' o una lista de separados por comas de valores válidos|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación|


## <a name="getlist"></a>GetList
Lista de obtiene por Id: Obtiene lista por Id. 

```GET: /lists/{list_id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|board_id|cadena|Sí|consulta|Ninguno|Identificador exclusivo de los paneles para capturar las listas de|
|list_id|cadena|Sí|ruta de acceso|Ninguno|Identificador exclusivo de la lista de búsqueda|
|tarjetas|cadena|No|consulta|Ninguno|Especificar las tarjetas para devolver|
|card_fields|cadena|No|consulta|Ninguno|Lista de los campos de la tarjeta para devolver para cada tarjeta. Especificar 'all' o una lista de separados por comas de valores válidos|
|panel|valor booleano|No|consulta|Ninguno|Especificar si se va a devolver información de panel|
|board_fields|cadena|No|consulta|Ninguno|Lista de los campos de panel para devolver. Especificar 'all' o una lista de separados por comas de valores válidos|
|campos|cadena|No|consulta|Ninguno|Lista de la lista de campos para volver. Especificar 'all' o una lista de separados por comas de valores válidos|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación|


## <a name="object-definitions"></a>Definiciones de objeto 

### <a name="card"></a>Tarjeta


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|cadena|No |
|checkItemStates|matriz|No |
|cerrado|valor booleano|No |
|dateLastActivity|cadena|No |
|DESC|cadena|No |
|idBoard|cadena|No |
|Lista_id|cadena|No |
|idMembersVoted|matriz|No |
|idShort|entero|No |
|idAttachmentCover|cadena|No |
|manualCoverAttachment|valor booleano|No |
|idLabels|matriz|No |
|nombre|cadena|No |
|OC|entero|No |
|shortLink|cadena|No |
|teclas|no definido|No |
|vencimiento|cadena|No |
|Correo electrónico|cadena|No |
|shortUrl|cadena|No |
|suscrito|valor booleano|No |
|dirección URL|cadena|No |



### <a name="badges"></a>Teclas


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Votos|entero|No |
|ViewingMemberVoted|valor booleano|No |
|Suscrito|valor booleano|No |
|Fogbugz|cadena|No |
|CheckItems|entero|No |
|CheckItemsChecked|entero|No |
|Comentarios|entero|No |
|Datos adjuntos|entero|No |
|Descripción|valor booleano|No |
|Vencimiento|cadena|No |



### <a name="object"></a>Objeto


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|



### <a name="createcard"></a>CreateCard


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Lista_id|cadena|Sí |
|nombre|cadena|Sí |
|DESC|cadena|No |
|OC|cadena|No |
|idMembers|cadena|No |
|idLabels|cadena|No |
|urlSource|cadena|No |
|fileSource|cadena|No |
|idCardSource|cadena|No |
|keepFromSource|cadena|No |



### <a name="updatecard"></a>UpdateCard


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|nombre|cadena|No |
|DESC|cadena|No |
|cerrado|valor booleano|No |
|idMembers|cadena|No |
|idAttachmentCover|cadena|No |
|Lista_id|cadena|No |
|idBoard|cadena|No |
|OC|cadena|No |
|vencimiento|cadena|No |
|suscrito|valor booleano|No |



### <a name="board"></a>Panel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|cadena|No |
|cerrado|valor booleano|No |
|dateLastActivity|cadena|No |
|dateLastView|cadena|No |
|DESC|cadena|No |
|idOrganization|cadena|No |
|invitaciones|matriz|No |
|invitado|valor booleano|No |
|labelNames|no definido|No |
|pertenencias a grupos|matriz|No |
|nombre|cadena|No |
|anclados|valor booleano|No |
|powerUps|matriz|No |
|perfs|no definido|No |
|shortLink|cadena|No |
|shortUrl|cadena|No |
|o de estrella|cadena|No |
|suscrito|cadena|No |
|dirección URL|cadena|No |



### <a name="label"></a>Etiqueta


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|verde|cadena|No |
|amarillo|cadena|No |
|naranja|cadena|No |
|rojo|cadena|No |
|púrpura|cadena|No |
|azul|cadena|No |
|cielo|cadena|No |
|Lima|cadena|No |
|Rosa|cadena|No |
|negro|cadena|No |



### <a name="membership"></a>Pertenencia


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|cadena|No |
|idMember|cadena|No |
|memberType|cadena|No |
|sin confirmar|valor booleano|No |



### <a name="perfs"></a>Perfs


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|permissionLevel|cadena|No |
|votos|cadena|No |
|comentarios|cadena|No |
|invitaciones|cadena|No |
|selfJoin|valor booleano|No |
|cardCovers|valor booleano|No |
|calendarFeedEnabled|valor booleano|No |
|fondo|cadena|No |
|backgroundColor|cadena|No |
|backgroundImage|cadena|No |
|backgroundImageScaled|cadena|No |
|backgroundTile|valor booleano|No |
|backgroundBrightness|cadena|No |
|canBePublic|valor booleano|No |
|canBeOrg|valor booleano|No |
|canBePrivate|valor booleano|No |
|canInvite|valor booleano|No |



### <a name="list"></a>Lista


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|cadena|No |
|nombre|cadena|No |
|cerrado|valor booleano|No |
|idBoard|cadena|No |
|OC|número|No |
|suscrito|valor booleano|No |
|tarjetas|matriz|No |
|panel|no definido|No |


## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)