<properties
pageTitle="RSS | Microsoft Azure"
description="Crear aplicaciones de lógica con el servicio de aplicación de Azure. Conector RSS permite a los usuarios publicar y recuperar elementos de la fuente. También permite a los usuarios activar las operaciones cuando se publica un nuevo elemento a la fuente."
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

# <a name="get-started-with-the-rss-connector"></a>Empezar a trabajar con el conector RSS
RSS es un formato de distribución de web populares utilizado para publicar contenido actualizado con frecuencia, como las entradas de blog y titulares de noticias.  Muchos editores de contenido proporcionan una fuente RSS para permitir a los usuarios suscribirse a él.  Usar el conector RSS para recuperar los flujos de información y activar fuentes cuando se publican nuevos elementos en una fuente RSS.

>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2015-08-01-vista previa de aplicaciones de lógica. 

Puede empezar creando una aplicación de lógica ahora, vea [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones

El conector RSS puede utilizarse como una acción; tiene desencadenadores. Todos los conectores admiten datos en formatos XML y JSON. 

 El conector RSS tiene las siguientes acciones o desencadenadores disponibles:

### <a name="rss-actions"></a>Acciones de RSS
Puede realizar estas acciones:

|Acción|Descripción|
|--- | ---|
|[ListFeedItems](connectors-create-api-rss.md#listfeeditems)|Obtenga RSS todos los elementos de la fuente.|
### <a name="rss-triggers"></a>Desencadenadores RSS
Puede escuchar para estos eventos:

|Desencadenador | Descripción|
|--- | ---|
|Cuando se publica un nuevo elemento de fuente|Activa un flujo de trabajo cuando se publica una nueva fuente|


## <a name="create-a-connection-to-rss"></a>Crear una conexión a RSS

>[AZURE.INCLUDE [Steps to create a connection to an RSS feed](../../includes/connectors-create-api-rss.md)]

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones de lógica.

## <a name="reference-for-rss"></a>Referencia de RSS
Se aplica a la versión: 1.0

## <a name="onnewfeed"></a>OnNewFeed
Cuando se publica un nuevo elemento de fuente: activa un flujo de trabajo cuando se publica una nueva fuente 

```GET: /OnNewFeed``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|feedUrl|cadena|Sí|consulta|Ninguno|Dirección url de fuente|

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


## <a name="listfeeditems"></a>ListFeedItems
Lista todos los RSS fuentes elementos.: Obtenga RSS todos los elementos de la fuente. 

```GET: /ListFeedItems``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|feedUrl|cadena|Sí|consulta|Ninguno|Dirección url de fuente|

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

### <a name="triggerbatchresponsefeeditem"></a>TriggerBatchResponse [FeedItem]


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|valor|matriz|No |



### <a name="feeditem"></a>FeedItem


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|cadena|Sí |
|título|cadena|Sí |
|contenido|cadena|Sí |
|vínculos|matriz|No |
|updatedOn|cadena|No |


## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)