<properties
pageTitle="Wunderlist | Microsoft Azure"
description="Crear aplicaciones de lógica con el servicio de aplicación de Azure. Wunderlist proporcionan un administrador de tareas y la lista de todo para ayudar a otras personas a su terminar el trabajo.  Si comparte una lista de la compra con alguien estimado, trabajar en un proyecto o planea las vacaciones, Wunderlist facilita capturar, compartir y completar la to¬dos. Wunderlist al instante sincroniza entre su teléfono, tableta y su PC, por lo que puede tener acceso a todas sus tareas desde cualquier lugar."
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

# <a name="get-started-with-the-wunderlist-connector"></a>Empezar a trabajar con el conector de Wunderlist

Wunderlist proporcionan un administrador de tareas y la lista de todo para ayudar a otras personas a su terminar el trabajo.  Si comparte una lista de la compra con alguien estimado, trabajar en un proyecto o planea las vacaciones, Wunderlist facilita capturar, compartir y completar la to¬dos. Wunderlist al instante sincroniza entre su teléfono, tableta y su PC, por lo que puede tener acceso a todas sus tareas desde cualquier lugar.

>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2015-08-01-vista previa de aplicaciones de lógica. 

Puede empezar creando una aplicación de lógica ahora, vea [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones

El conector de Wunderlist puede utilizarse como una acción; tiene desencadenadores. Todos los conectores admiten datos en formatos XML y JSON. 

 El conector Wunderlist tiene las siguientes acciones o desencadenadores disponibles:

### <a name="wunderlist-actions"></a>Acciones de Wunderlist
Puede realizar estas acciones:

|Acción|Descripción|
|--- | ---|
|[RetrieveLists](connectors-create-api-wunderlist.md#retrievelists)|Recuperar las listas asociadas a su cuenta.|
|[CreateList](connectors-create-api-wunderlist.md#createlist)|Crear una lista.|
|[ListTasks](connectors-create-api-wunderlist.md#listtasks)|Recuperar las tareas de una lista específica.|
|[CreateTask](connectors-create-api-wunderlist.md#createtask)|Crear una tarea|
|[ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks)|Recuperar las subtareas de una lista específica o de una tarea específica.|
|[CreateSubTask](connectors-create-api-wunderlist.md#createsubtask)|Crear una subtareas dentro de una tarea específica|
|[ListNotes](connectors-create-api-wunderlist.md#listnotes)|Recuperar notas para una lista específica o una tarea específica.|
|[CreateNote](connectors-create-api-wunderlist.md#createnote)|Agregar una nota a una tarea específica|
|[ListComments](connectors-create-api-wunderlist.md#listcomments)|Recuperar comentarios de tareas de una lista específica o de una tarea específica.|
|[CreateComment](connectors-create-api-wunderlist.md#createcomment)|Agregar un comentario a una tarea específica|
|[RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders)|Recuperar avisos para una lista específica o una tarea específica.|
|[CreateReminder](connectors-create-api-wunderlist.md#createreminder)|Establecer un aviso.|
|[RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles)|Recuperar archivos de una lista específica o una tarea específica.|
|[GetList](connectors-create-api-wunderlist.md#getlist)|Recupera una lista específica|
|[DeleteList](connectors-create-api-wunderlist.md#deletelist)|Elimina una lista|
|[UpdateList](connectors-create-api-wunderlist.md#updatelist)|Actualizar una lista específica|
|[GetTask](connectors-create-api-wunderlist.md#gettask)|Recupera una tarea específica|
|[UpdateTask](connectors-create-api-wunderlist.md#updatetask)|Actualiza una tarea específica|
|[DeleteTask](connectors-create-api-wunderlist.md#deletetask)|Elimina una tarea específica|
|[GetSubTask](connectors-create-api-wunderlist.md#getsubtask)|Recupera las subtareas específica|
|[UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask)|Actualiza las subtareas específica|
|[DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask)|Elimina las subtareas específica|
|[GetNote](connectors-create-api-wunderlist.md#getnote)|Recuperar una nota específica|
|[ActualizaciónNota](connectors-create-api-wunderlist.md#updatenote)|Actualizar una nota específica|
|[DeleteNote](connectors-create-api-wunderlist.md#deletenote)|Eliminar una nota específica|
|[GetComment](connectors-create-api-wunderlist.md#getcomment)|Recuperar un comentario de tarea específica|
|[UpdateReminder](connectors-create-api-wunderlist.md#updatereminder)|Actualizar un aviso específico|
|[DeleteReminder](connectors-create-api-wunderlist.md#deletereminder)|Eliminar un aviso específico|
### <a name="wunderlist-triggers"></a>Wunderlist desencadenadores
Puede escuchar para estos eventos:

|Desencadenador | Descripción|
|--- | ---|
|Cuando vence una tarea|Activa un flujo de nuevo cuando vence una tarea en la lista|
|Cuando se crea una nueva tarea|Activa un flujo de nuevo cuando se crea una nueva tarea en la lista|
|Cuando se produce un aviso|Activa un flujo de nuevo cuando se produce un aviso|


## <a name="create-a-connection-to-wunderlist"></a>Crear una conexión a Wunderlist
Para crear aplicaciones de lógica con Wunderlist, primero debe crear una **conexión** y proporcionar los detalles de las siguientes propiedades: 

|(Propiedad)| Obligatorio|Descripción|
| ---|---|---|
|Token|Sí|Proporcionar credenciales Wunderlist|
Después de crear la conexión, puede usarlo para ejecutar las acciones y escuchar los desencadenadores descritos en este artículo. 


>[AZURE.INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)] 


>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones de lógica.

## <a name="reference-for-wunderlist"></a>Referencia para Wunderlist
Se aplica a la versión: 1.0

## <a name="triggertaskdue"></a>TriggerTaskDue
Cuando vence una tarea: activa un flujo de nuevo cuando vence una tarea en la lista 

```GET: /trigger/tasksdue``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list_id|entero|Sí|consulta|Ninguno|Id. de lista|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación correcta|


## <a name="triggertasknew"></a>TriggerTaskNew
Cuando se crea una tarea nueva: activa un flujo de nuevo cuando se crea una nueva tarea en la lista 

```GET: /trigger/tasksnew``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list_id|entero|Sí|consulta|Ninguno|Id. de lista|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación correcta|


## <a name="triggerreminder"></a>TriggerReminder
Cuando se produce un aviso: activa un flujo de nuevo cuando se produce un aviso 

```GET: /trigger/reminders``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list_id|entero|Sí|consulta|Ninguno|Id. de lista|
|TASK_ID|entero|No|consulta|Ninguno|Id. de tarea|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación correcta|


## <a name="retrievelists"></a>RetrieveLists
Obtener listas: recuperar las listas asociadas a su cuenta. 

```GET: /lists``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Solicitud incorrecta|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación.|


## <a name="createlist"></a>CreateList
Crear una lista: crear una lista. 

```POST: /lists``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Exponer| |Sí|cuerpo|Ninguno|Crear una nueva lista|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|predeterminado|Error en la operación.|


## <a name="listtasks"></a>ListTasks
Obtener las tareas: recuperar las tareas de una lista específica. 

```GET: /tasks``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list_id|entero|Sí|consulta|Ninguno|Id. de lista|
|completado|valor booleano|No|consulta|Ninguno|Completado|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Solicitud incorrecta|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación.|


## <a name="createtask"></a>CreateTask
Crear una tarea: crear una tarea 

```POST: /tasks``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Exponer| |Sí|cuerpo|Ninguno|Crear una tarea nueva|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|201|Creado|


## <a name="listsubtasks"></a>ListSubTasks
Obtener las subtareas: recuperar las subtareas de una lista específica o de una tarea específica. 

```GET: /subtasks``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list_id|entero|Sí|consulta|Ninguno|Id. de lista|
|TASK_ID|entero|No|consulta|Ninguno|Id. de tarea|
|completado|valor booleano|No|consulta|Ninguno|Completado|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Solicitud incorrecta|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación.|


## <a name="createsubtask"></a>CreateSubTask
Crear una subtareas: crear las subtareas dentro de una tarea específica 

```POST: /subtasks``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Exponer| |Sí|cuerpo|Ninguno|Subtareas nueva creación|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|201|Creado|


## <a name="listnotes"></a>ListNotes
Disponer notas: recuperar notas para una lista específica o una tarea específica. 

```GET: /notes``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list_id|entero|Sí|consulta|Ninguno|Id. de lista|
|TASK_ID|entero|No|consulta|Ninguno|Id. de tarea|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Solicitud incorrecta|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación.|


## <a name="createnote"></a>CreateNote
Crear una Nota: agregar una nota a una tarea específica 

```POST: /notes``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Exponer| |Sí|cuerpo|Ninguno|Nueva nota que debe crearse|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|201|Creado|


## <a name="listcomments"></a>ListComments
Obtener comentarios sobre tareas: recuperar comentarios de tareas de una lista específica o de una tarea específica. 

```GET: /task_comments``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list_id|entero|Sí|consulta|Ninguno|Id. de lista|
|TASK_ID|entero|No|consulta|Ninguno|Id. de tarea|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Solicitud incorrecta|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación.|


## <a name="createcomment"></a>CreateComment
Agregar un comentario a una tarea: agregar un comentario a una tarea específica 

```POST: /task_comments``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Exponer| |Sí|cuerpo|Ninguno|Nuevo comentario de tarea que debe crearse|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|201|Creado|


## <a name="retrievereminders"></a>RetrieveReminders
Obtener avisos: recuperar avisos para una lista específica o una tarea específica. 

```GET: /reminders``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list_id|entero|Sí|consulta|Ninguno|Id. de lista|
|TASK_ID|entero|No|consulta|Ninguno|Id. de tarea|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Solicitud incorrecta|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación.|


## <a name="createreminder"></a>CreateReminder
Establecer un aviso: establecer un aviso. 

```POST: /reminders``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Exponer| |Sí|cuerpo|Ninguno|Aviso nueva creación|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|predeterminado|Error en la operación.|


## <a name="retrievefiles"></a>RetrieveFiles
Obtener archivos: recuperar archivos de una lista específica o una tarea específica. 

```GET: /files``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list_id|entero|Sí|consulta|Ninguno|Id. de lista|
|TASK_ID|entero|No|consulta|Ninguno|Id. de tarea|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Operación correcta|
|400|Solicitud incorrecta|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación.|


## <a name="getlist"></a>GetList
Obtener lista: recupera una lista específica 

```GET: /lists/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id.|cadena|Sí|ruta de acceso|Ninguno|Id. de lista|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|


## <a name="deletelist"></a>DeleteList
Eliminar lista: elimina una lista 

```DELETE: /lists/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id.|entero|Sí|ruta de acceso|Ninguno|Id. de lista|
|revisión|entero|Sí|consulta|Ninguno|Revisión|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|204|Sin contenido|


## <a name="updatelist"></a>UpdateList
Actualizar una lista: actualizar una lista específica 

```PATCH: /lists/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id.|entero|Sí|ruta de acceso|Ninguno|Id. de lista|
|Exponer| |Sí|cuerpo|Ninguno|Detalles de lista|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|


## <a name="gettask"></a>GetTask
Obtener tarea: recupera una tarea específica 

```GET: /tasks/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list_id|entero|Sí|consulta|Ninguno|Id. de lista|
|Id.|entero|Sí|ruta de acceso|Ninguno|Id. de tarea|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|


## <a name="updatetask"></a>UpdateTask
Actualizar una tarea: actualiza una tarea específica 

```PATCH: /tasks/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list_id|entero|Sí|consulta|Ninguno|Id. de lista|
|Id.|entero|Sí|ruta de acceso|Ninguno|Id. de tarea|
|Exponer| |Sí|cuerpo|Ninguno|Detalles de la tarea|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|


## <a name="deletetask"></a>DeleteTask
Eliminar tarea: elimina una tarea específica 

```DELETE: /tasks/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|list_id|entero|Sí|consulta|Ninguno|Id. de lista|
|Id.|entero|Sí|ruta de acceso|Ninguno|Id. de tarea|
|revisión|entero|Sí|consulta|Ninguno|Revisión|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|204|Sin contenido|


## <a name="getsubtask"></a>GetSubTask
Obtener las subtareas: recupera las subtareas específica 

```GET: /subtasks/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id.|cadena|Sí|ruta de acceso|Ninguno|Id. de subtareas|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|


## <a name="updatesubtask"></a>UpdateSubTask
Actualizar una subtareas: actualiza las subtareas específica 

```PATCH: /subtasks/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id.|entero|Sí|ruta de acceso|Ninguno|Id. de subtareas|
|Exponer| |Sí|cuerpo|Ninguno|Detalles de las subtareas|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|


## <a name="deletesubtask"></a>DeleteSubTask
Eliminar una subtareas: elimina las subtareas específica 

```DELETE: /subtasks/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id.|entero|Sí|ruta de acceso|Ninguno|Id. de subtareas|
|revisión|entero|Sí|consulta|Ninguno|Revisión|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|204|Sin contenido|


## <a name="getnote"></a>GetNote
Obtener una Nota: recuperar una nota específica 

```GET: /notes/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id.|cadena|Sí|ruta de acceso|Ninguno|Id. de nota|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|


## <a name="updatenote"></a>ActualizaciónNota
Actualizar una Nota: actualizar una nota específica 

```PATCH: /notes/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id.|entero|Sí|ruta de acceso|Ninguno|Id. de nota|
|Exponer| |Sí|cuerpo|Ninguno|Detalles de la nota|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|


## <a name="deletenote"></a>DeleteNote
Eliminar una Nota: eliminar una nota específica 

```DELETE: /notes/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id.|entero|Sí|ruta de acceso|Ninguno|Id. de nota|
|revisión|entero|Sí|consulta|Ninguno|Revisión|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|204|Sin contenido|


## <a name="getcomment"></a>GetComment
Obtener comentarios de tarea: recuperar un comentario de tarea específica 

```GET: /task_comments/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id.|cadena|Sí|ruta de acceso|Ninguno|Id. de comentario|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|


## <a name="updatereminder"></a>UpdateReminder
Actualizar un aviso: actualizar un aviso específico 

```PATCH: /reminders/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id.|entero|Sí|ruta de acceso|Ninguno|Id. de aviso|
|Exponer| |Sí|cuerpo|Ninguno|Detalles de aviso|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|Vale|


## <a name="deletereminder"></a>DeleteReminder
Eliminar un aviso: eliminar un aviso específico 

```DELETE: /reminders/{id}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|Id.|entero|Sí|ruta de acceso|Ninguno|Id. del aviso.|
|revisión|entero|Sí|consulta|Ninguno|Revisión|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|204|Sin contenido|


## <a name="object-definitions"></a>Definiciones de objeto 

### <a name="list"></a>Lista


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|entero|No |
|created_at|cadena|No |
|título|cadena|No |
|list_type|cadena|No |
|tipo|cadena|No |
|revisión|entero|No |



### <a name="createdlist"></a>CreatedList


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|entero|No |
|created_at|cadena|No |
|título|cadena|No |
|revisión|entero|No |
|tipo|cadena|No |



### <a name="task"></a>Tarea


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|entero|No |
|assignee_id|entero|No |
|assigner_id|entero|No |
|created_at|cadena|No |
|created_by_id|entero|No |
|fecha de vencimiento|cadena|No |
|list_id|entero|No |
|revisión|entero|No |
|o de estrella|valor booleano|No |
|título|cadena|No |



### <a name="subtask"></a>Subtareas


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|entero|No |
|TASK_ID|entero|No |
|created_at|cadena|No |
|created_by_id|entero|No |
|revisión|cadena|No |
|título|cadena|No |



### <a name="note"></a>Nota


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|entero|No |
|TASK_ID|entero|No |
|contenido|cadena|No |
|created_at|cadena|No |
|updated_at|cadena|No |
|revisión|entero|No |



### <a name="comment"></a>Comentario


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|entero|No |
|TASK_ID|entero|No |
|revisión|entero|No |
|texto|cadena|No |
|tipo|cadena|No |
|created_at|cadena|No |



### <a name="reminder"></a>Aviso


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|entero|No |
|fecha|cadena|No |
|TASK_ID|entero|No |
|revisión|entero|No |
|tipo|cadena|No |
|created_at|cadena|No |
|updated_at|cadena|No |



### <a name="createdreminder"></a>CreatedReminder


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|entero|No |
|fecha|cadena|No |
|TASK_ID|entero|No |
|revisión|entero|No |
|created_at|cadena|No |
|updated_at|cadena|No |



### <a name="file"></a>Archivo


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Id.|entero|No |
|dirección URL|cadena|No |
|TASK_ID|entero|No |
|list_id|entero|No |
|USER_ID|entero|No |
|file_name|cadena|No |
|content_type|cadena|No |
|FILE_SIZE|entero|No |
|local_created_at|cadena|No |
|created_at|cadena|No |
|updated_at|cadena|No |
|tipo|cadena|No |
|revisión|entero|No |



### <a name="newtask"></a>Nueva tarea


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|list_id|entero|Sí |
|título|cadena|Sí |
|assignee_id|entero|No |
|completado|valor booleano|No |
|recurrence_type|cadena|No |
|recurrence_count|entero|No |
|fecha de vencimiento|cadena|No |
|o de estrella|valor booleano|No |



### <a name="newlist"></a>NewList


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|título|cadena|Sí |



### <a name="newsubtask"></a>NewSubtask


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|list_id|entero|Sí |
|TASK_ID|entero|Sí |
|título|cadena|Sí |
|completado|valor booleano|No |



### <a name="newnote"></a>NewNote


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|list_id|entero|Sí |
|TASK_ID|entero|Sí |
|contenido|cadena|Sí |



### <a name="newcomment"></a>NuevoComentario


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|list_id|entero|Sí |
|TASK_ID|entero|Sí |
|texto|cadena|Sí |



### <a name="newreminder"></a>NewReminder


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|list_id|entero|Sí |
|TASK_ID|entero|Sí |
|fecha|cadena|Sí |



### <a name="updatetask"></a>UpdateTask


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|revisión|entero|No |
|título|cadena|No |
|assignee_id|entero|No |
|completado|valor booleano|No |
|recurrence_type|cadena|No |
|recurrence_count|entero|No |
|fecha de vencimiento|cadena|No |
|o de estrella|valor booleano|No |



### <a name="updatelist"></a>UpdateList


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|revisión|entero|No |
|título|cadena|No |



### <a name="updatesubtask"></a>UpdateSubtask


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|revisión|entero|No |
|título|cadena|No |
|completado|valor booleano|No |



### <a name="updatenote"></a>ActualizaciónNota


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|revisión|entero|No |
|contenido|cadena|No |



### <a name="updatereminder"></a>UpdateReminder


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|fecha|cadena|No |
|revisión|entero|No |


## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)