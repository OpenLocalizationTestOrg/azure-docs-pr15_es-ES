<properties
pageTitle="ProjectOnline | Microsoft Azure"
description="Crear aplicaciones de lógica con el servicio de aplicación de Azure. Project Online es una solución en línea flexible para la administración de carteras de proyectos (PPM) y el trabajo diario de Microsoft. Entregar mediante Office 365, Project Online permite a las organizaciones comenzar rápidamente con capacidades de administración de proyectos eficaces para planear, priorizar y administrar proyectos y las inversiones en carteras de proyectos, desde prácticamente cualquier lugar en prácticamente cualquier dispositivo."
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

# <a name="get-started-with-the-projectonline-connector"></a>Empezar a trabajar con el conector de ProjectOnline

Project Online es una solución en línea flexible para la administración de carteras de proyectos (PPM) y el trabajo diario de Microsoft. Entregar mediante Office 365, Project Online permite a las organizaciones comenzar rápidamente con capacidades de administración de proyectos eficaces para planear, priorizar y administrar proyectos y las inversiones en carteras de proyectos, desde prácticamente cualquier lugar en prácticamente cualquier dispositivo.

>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2015-08-01-vista previa de aplicaciones de lógica. 

Puede empezar creando una aplicación de lógica ahora, vea [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones

El conector de ProjectOnline puede utilizarse como una acción; tiene desencadenadores. Todos los conectores admiten datos en formatos XML y JSON. 

 El conector de ProjectOnline tiene las siguientes acciones o desencadenadores disponibles:

### <a name="projectonline-actions"></a>Acciones de ProjectOnline
Puede realizar estas acciones:

|Acción|Descripción|
|--- | ---|
|[ListProjects](connectors-create-api-projectonline.md#listprojects)|Muestra los proyectos de su sitio de project online|
|[CreateProject](connectors-create-api-projectonline.md#createproject)|Crea un nuevo proyecto en su sitio de project online|
|[CreateTask](connectors-create-api-projectonline.md#createtask)|Crea una nueva tarea de proyecto|
|[CreateResource](connectors-create-api-projectonline.md#createresource)|Crea los recursos de empresa en su sitio de project online|
|[ListTasks](connectors-create-api-projectonline.md#listtasks)|Enumera las tareas publicadas en un proyecto|
|[CheckoutProject](connectors-create-api-projectonline.md#checkoutproject)|Desprotege un proyecto en su sitio|
|[PublishProject](connectors-create-api-projectonline.md#publishproject)|Proteger y publicar y proyecto en su sitio existente|
### <a name="projectonline-triggers"></a>Desencadenadores ProjectOnline
Puede escuchar para estos eventos:

|Desencadenador | Descripción|
|--- | ---|
|Cuando se crea un nuevo proyecto|Un flujo se activa siempre que se crea un nuevo proyecto|
|Cuando se crea un nuevo recurso|Activa un flujo de nuevo cuando se crea un nuevo recurso|
|Cuando se crea una nueva tarea|Active un flujo cuando se crea una nueva tarea|


## <a name="create-a-connection-to-projectonline"></a>Crear una conexión a ProjectOnline
Para crear aplicaciones de lógica con ProjectOnline, primero debe crear una **conexión** y proporcionar los detalles de las siguientes propiedades: 

|(Propiedad)| Obligatorio|Descripción|
| ---|---|---|
|Token|Sí|Proporcionar las credenciales de ProjectOnline|

>[AZURE.INCLUDE [Steps to create a connection to ProjectOnline](../../includes/connectors-create-api-projectonline.md)]

>[AZURE.TIP] Puede usar esta conexión en otras aplicaciones de lógica.

## <a name="reference-for-projectonline"></a>Referencia de ProjectOnline
Se aplica a la versión: 1.0

## <a name="onnewproject"></a>OnNewProject
Cuando se crea un nuevo proyecto: activa un flujo siempre que se crea un nuevo proyecto 

```GET: /trigger/_api/ProjectData/Projects``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|Sí|consulta|Ninguno|Raíz de la dirección url del sitio de su sitio del proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="onnewresource"></a>OnNewResource
Cuando se crea un nuevo recurso: Active un flujo de nuevo cuando se crea un nuevo recurso 

```GET: /trigger/_api/ProjectData/Resources``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|Sí|consulta|Ninguno|Raíz de la dirección url del sitio de su sitio del proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="onnewtask"></a>OnNewTask
Cuando se crea una tarea nueva: Active un flujo cuando se crea una nueva tarea 

```GET: /trigger/_api/ProjectData/Tasks``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|Sí|consulta|Ninguno|Raíz de la dirección url del sitio de su sitio del proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="listprojects"></a>ListProjects
Lista de proyectos: muestra los proyectos de su sitio de project online 

```GET: /_api/ProjectServer/Projects``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|Sí|consulta|Ninguno|Raíz de la dirección url del sitio de su sitio del proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="createproject"></a>CreateProject
Crea el nuevo proyecto: crea un nuevo proyecto en su sitio de project online 

```POST: /_api/ProjectServer/Projects``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|Sí|consulta|Ninguno|Raíz de la dirección url del sitio de su sitio del proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|proyecto| |Sí|cuerpo|Ninguno|Nuevo proyecto para crear|

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


## <a name="createtask"></a>CreateTask
Crea una tarea nueva: crea una nueva tarea de proyecto 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|Sí|consulta|Ninguno|Raíz de la dirección url del sitio de su sitio del proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|cadena|Sí|ruta de acceso|Ninguno|Identificador exclusivo del proyecto para agregar la tarea|
|tarea| |Sí|cuerpo|Ninguno|Nueva tarea para agregar al proyecto|

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


## <a name="createresource"></a>CreateResource
Crear nuevo recurso: crea los recursos de empresa en su sitio de project online 

```POST: /_api/ProjectServer/EnterpriseResources``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|Sí|consulta|Ninguno|Raíz de la dirección url del sitio de su sitio del proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|recursos| |Sí|cuerpo|Ninguno|Nuevo recurso de empresa para agregar al proyecto|

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


## <a name="listtasks"></a>ListTasks
Listas de tareas: muestra las tareas de un proyecto publicadas 

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|Sí|consulta|Ninguno|Raíz de la dirección url del sitio de su sitio del proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|cadena|Sí|ruta de acceso|Ninguno|Identificador único del proyecto para capturar tareas|

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


## <a name="checkoutproject"></a>CheckoutProject
Desproteger un proyecto: desprotege un proyecto en su sitio 

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|Sí|consulta|Ninguno|Raíz de la dirección url del sitio de su sitio del proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|cadena|Sí|ruta de acceso|Ninguno|Identificador exclusivo del proyecto para agregar la tarea|

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


## <a name="publishproject"></a>PublishProject
Protección y publicar proyecto: proteger y publicar y proyecto en su sitio existente 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|siteUrl|cadena|Sí|consulta|Ninguno|Raíz de la dirección url del sitio de su sitio del proyecto (ejemplo: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|cadena|Sí|ruta de acceso|Ninguno|Identificador único del proyecto para registrar|

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

### <a name="triggerprojectswrapper"></a>TriggerProjectsWrapper


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|valor|matriz|No |



### <a name="triggerproject"></a>TriggerProject


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|ProjectStartDate|cadena|No |
|ProjectFinishDate|cadena|No |
|ProjectCreatedDate|cadena|No |
|ProjectId|cadena|No |
|ProjectModifiedDate|cadena|No |
|ProjectType|entero|No |
|Nombre de proyecto|cadena|No |



### <a name="triggerresourceswrapper"></a>TriggerResourcesWrapper


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|valor|matriz|No |



### <a name="triggerresource"></a>TriggerResource


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|ResourceId|cadena|No |
|ResourceBaseCalendar|cadena|No |
|ResourceBookingType|entero|No |
|ResourceCanLevel|valor booleano|No |
|ResourceCostPerUse|número|No |
|ResourceCreatedDate|cadena|No |
|ResourceEarliestAvailableFrom|cadena|No |
|ResourceEmail|cadena|No |
|ResourceInitials|cadena|No |
|ResourceIsActive|valor booleano|No |
|ResourceIsGeneric|valor booleano|No |
|ResourceLatestAvailableTo|cadena|No |
|ResourceModifiedDate|cadena|No |
|ResourceName|cadena|No |
|ResourceStatsuName|cadena|No |
|Tipo de recurso|entero|No |
|Tipo de CampoDescripción|cadena|No |
|TypeName|cadena|No |



### <a name="triggertaskswrapper"></a>TriggerTasksWrapper


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|valor|matriz|No |



### <a name="triggertask"></a>TriggerTask


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|ProjectId|cadena|No |
|TaskId|cadena|No |
|Nombre de proyecto|cadena|No |
|Nombre de tarea|cadena|No |
|TaskCreatedDate|cadena|No |
|TaskModifieddate|cadena|No |
|TaskStartDate|cadena|No |
|TaskFinishDate|cadena|No |
|TaskPriority|entero|No |
|TaskIsActive|valor booleano|No |



### <a name="newproject"></a>Nuevo proyecto


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Nombre|cadena|Sí |
|Descripción|cadena|No |
|Inicio|cadena|No |



### <a name="newreource"></a>NewReource


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Nombre|cadena|Sí |
|IsBudget|valor booleano|No |
|IsGeneric|valor booleano|No |
|IsInactive|valor booleano|No |



### <a name="project"></a>Proyecto


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|ApprovedStart|cadena|No |
|ApprovedEnd|cadena|No |
|CheckedOutDate|cadena|No |
|CheckOutDescription|cadena|No |
|CheckOutId|cadena|No |
|CreatedDate|cadena|No |
|Id.|cadena|No |
|IsCheckedOut|valor booleano|No |
|LastPublishedDate|cadena|No |
|LastSavedDate|cadena|No |
|OptimizerDecision|entero|No |
|PlannerDecision|entero|No |
|ProjectType|entero|No |
|Nombre|cadena|No |
|WinprojVersion|cadena|No |



### <a name="projectswrapper"></a>ProjectsWrapper


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|valor|matriz|No |



### <a name="newtask"></a>Nueva tarea


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|parámetros|no definido|Sí |



### <a name="taskparameters"></a>TaskParameters


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Nombre|cadena|Sí |
|Notas|cadena|No |
|Inicio|cadena|No |
|Duración|cadena|No |



### <a name="enterpriseresource"></a>EnterpriseResource


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|CanLevel|valor booleano|No |
|Código|cadena|No |
|CostAccrual|entero|No |
|CostCenter|cadena|No |
|Creado|cadena|No |
|DefaultBookingType|entero|No |
|Correo electrónico|cadena|No |
|ExternalID no|cadena|No |
|Grupo|cadena|No |
|HireDate|cadena|No |
|Id.|cadena|No |
|Iniciales|cadena|No |
|IsActive|valor booleano|No |
|IsBudget|valor booleano|No |
|IsCheckedOut|valor booleano|No |
|IsGeneric|valor booleano|No |
|IsTeam|valor booleano|No |
|MaterialLabel|cadena|No |
|Modificado|cadena|No |
|Nombre|cadena|No |
|Fonética|cadena|No |
|Tipo de recurso|entero|No |
|TerminationDate|cadena|No |



### <a name="taskswrapper"></a>TasksWrapper


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|valor|matriz|No |



### <a name="task"></a>Tarea


| Nombre de propiedad | Tipo de datos | Obligatorio |
|---|---|---|
|Creado|cadena|No |
|Modificado|cadena|No |
|Inicio|cadena|No |
|Finalizar|cadena|No |
|Nombre|cadena|No |
|Id.|cadena|No |
|Prioridad|entero|No |
|PercentComplete|entero|No |
|Notas|cadena|No |
|Contacto|cadena|No |


## <a name="next-steps"></a>Pasos siguientes
[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)