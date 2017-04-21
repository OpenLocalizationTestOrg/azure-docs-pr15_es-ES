<properties
pageTitle="GitHub | Microsoft Azure"
description="Crear aplicaciones de lógica con el servicio de aplicación de Azure. GitHub es un repositorio de Git basados en web en el servicio de hospedaje. Ofrece toda la revisión distribuido control y origen de código management (SCM) funcionalidad de Git, así como agregar sus propias características."
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

# <a name="get-started-with-the-github-connector"></a>Empezar a trabajar con el conector de GitHub

GitHub es un repositorio de Git basados en web en el servicio de hospedaje. Ofrece toda la revisión distribuido control y origen de código management (SCM) funcionalidad de Git, así como agregar sus propias características.

>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2015-08-01-vista previa de aplicaciones de lógica. 

Puede empezar creando una aplicación de lógica ahora, vea [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones

El conector de GitHub puede utilizarse como una acción; tiene desencadenadores. Todos los conectores admiten datos en formatos XML y JSON. 

 El conector GitHub tiene las siguientes acciones o desencadenadores disponibles:

### <a name="github-actions"></a>Acciones de GitHub
Puede realizar estas acciones:

|Acción|Descripción|
|--- | ---|
|[CreateIssue](connectors-create-api-github.md#createissue)|Crea un problema|
### <a name="github-triggers"></a>GitHub desencadenadores
Puede escuchar para estos eventos:

|Desencadenador | Descripción|
|--- | ---|
|Cuando se abre un problema|Se abre un problema|
|Cuando se cierra un problema|Un problema se cierra|
|Cuando se asigna un problema|Se asigna un problema|


## <a name="create-a-connection-to-github"></a>Crear una conexión a GitHub
Para crear aplicaciones de lógica con GitHub, primero debe crear una **conexión** y proporcionar los detalles de las siguientes propiedades: 

|(Propiedad)| Obligatorio|Descripción|
| ---|---|---|
|Token|Sí|Proporcionar credenciales GitHub|
Después de crear la conexión, puede usarlo para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo. 

>[AZURE.INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones de lógica.

## <a name="reference-for-github"></a>Referencia para GitHub
Se aplica a la versión: 1.0

## <a name="createissue"></a>CreateIssue
Crear un problema: crea un problema 

```POST: /repos/{repositoryOwner}/{repositoryName}/issues``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|repositoryOwner|cadena|Sí|ruta de acceso|Ninguno|Propietario del repositorio|
|repositoryName|cadena|Sí|ruta de acceso|Ninguno|Nombre del repositorio|
|issueBasicDetails| |Sí|cuerpo|Ninguno|Detalles del problema|

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


## <a name="issueopened"></a>IssueOpened
Cuando se abre un problema: se abre un problema 

```GET: /trigger/issueOpened``` 

No hay parámetros para esta llamada
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


## <a name="issueclosed"></a>IssueClosed
Cuando se cierra un problema: un problema se cierra 

```GET: /trigger/issueClosed``` 

No hay parámetros para esta llamada
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


## <a name="issueassigned"></a>IssueAssigned
Cuando se asigna un problema: se ha asignado un problema 

```GET: /trigger/issueAssigned``` 

No hay parámetros para esta llamada
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


## <a name="object-definitions"></a>Definiciones de objeto 

### <a name="issuebasicdetailsmodel"></a>IssueBasicDetailsModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|título|cadena|Sí |
|cuerpo|cadena|Sí |
|encargado|cadena|Sí |



### <a name="issuedetailsmodel"></a>IssueDetailsModel


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|título|cadena|Sí |
|cuerpo|cadena|Sí |
|encargado|cadena|Sí |
|número|cadena|No |
|estado|cadena|No |
|created_at|cadena|No |
|repository_url|cadena|No |


## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)