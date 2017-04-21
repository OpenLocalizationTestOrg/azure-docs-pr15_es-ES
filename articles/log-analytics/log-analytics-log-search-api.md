<properties
    pageTitle="API de REST de búsqueda de registros de análisis de registro | Microsoft Azure"
    description="Esta guía proporciona un tutorial básico que describe cómo puede usar la API de REST de búsqueda de análisis de registro en el conjunto de administración de operaciones (OMS) y proporciona ejemplos que muestran cómo usar los comandos."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="log-analytics-log-search-rest-api"></a>API de REST de búsqueda de registros de análisis de registro

Esta guía proporciona un tutorial básico que describe cómo puede usar la API de REST de búsqueda de análisis de registro en el conjunto de administración de operaciones (OMS) y proporciona ejemplos que muestran cómo usar los comandos. Algunos de los ejemplos de este artículo de referencia perspectivas operativas, que es el nombre de la versión anterior de análisis de registro.

## <a name="overview-of-the-log-search-rest-api"></a>Información general sobre la API de REST de búsqueda de registros

La API de REST de búsqueda de análisis de registro es RESTful y se puede acceder a través de la API del Administrador de recursos de Azure. En este documento encontrará ejemplos donde se tiene acceso a la API a través de la [ARMClient](https://github.com/projectkudu/ARMClient), una herramienta de línea de comandos de Abrir origen que simplifica invocar la API del Administrador de recursos de Azure. El uso de ARMClient y PowerShell es una de muchas opciones para tener acceso a la API de búsqueda de análisis de registro. Otra opción es utilizar el módulo de PowerShell de Azure para OperationalInsights que incluye cmdlets para tener acceso a la búsqueda. Con estas herramientas puede utilizar la API de administrador de recursos de Azure REST para realizar llamadas a áreas de trabajo OMS y realizar los comandos de búsqueda dentro de ellas. La API mostrará resultados de búsqueda para usted en formato JSON, para que pueda utilizar los resultados de búsqueda de diferentes maneras mediante programación.

Puede usar el Administrador de recursos de Azure a través de una [biblioteca para .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) , así como una [API de REST](https://msdn.microsoft.com/library/azure/mt163658.aspx). Revise las páginas web vinculadas para obtener más información.

## <a name="basic-log-analytics-search-rest-api-tutorial"></a>Tutorial básico de API de REST de búsqueda de análisis de registro

### <a name="to-use-the-arm-client"></a>Para utilizar al cliente de ARM

1. Instalar [Chocolatey](https://chocolatey.org/), que es un administrador de paquetes de origen abierto para Windows. Abra una ventana del símbolo del sistema como administrador y, a continuación, ejecute el siguiente comando:

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```

2. Instalar el ARMClient ejecutando el siguiente comando:

    ```
    choco install armclient
    ```

### <a name="to-perform-a-simple-search-using-the-armclient"></a>Para realizar una búsqueda simple en el ARMClient

1. Inicie sesión en su cuenta de Microsoft o OrgID:

    ```
    armclient login
    ```

    Iniciar sesión correctamente enumera todas las suscripciones vinculadas a la cuenta determinada:

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```

2. Obtener las áreas de trabajo de la serie de administración de operaciones:

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    Una llamada Get correcta sería salida todas las áreas de trabajo que están vinculadas a la suscripción:

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. Crear la variable de búsqueda:

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. Buscar con la nueva variable de búsqueda:

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## <a name="log-analytics-search-rest-api-reference-examples"></a>Ejemplos de referencia de la API de REST de búsqueda de análisis de registro
Los siguientes ejemplos muestra cómo puede usar la API de búsqueda.

### <a name="search---actionread"></a>Búsqueda: acción de lectura

**Dirección Url de ejemplo:**

```
    /subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**Solicitar:**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
La siguiente tabla describe las propiedades que están disponibles.

|**(Propiedad)**|**Descripción**|
|---|---|
|Arriba|El número máximo de resultados para devolver.|
|Resaltar|Contiene parámetros pre y post, utilizados normalmente para resaltar campos coincidentes|
|Pre|Prefijos de la cadena especificada a los campos coincidentes.|
|Exponer|Anexa la cadena especificada a los campos coincidentes.|
|consulta|La consulta de búsqueda que se utiliza para recopilar y devolver resultados.|
|Inicio|Principio de la ventana de tiempo que se encuentra de resultados.|
|final|Final de la ventana de tiempo que se encuentra de resultados.|


**Respuesta:**

```
    {
      "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "__metadata" : {
        "resultType" : "raw",
        "total" : 1455,
        "top" : 150,
        "StartTime" : "2015-02-11T21:09:07.0345815Z",
        "Status" : "Successful",
        "LastUpdated" : "2015-02-11T21:09:07.331463Z",
        "CoreResponses" : [],
        "sort" : [{
          "name" : "TimeGenerated",
          "order" : "desc"
        }],
        "requestTime" : 450
      },
      "value": [{
        "SourceSystem" : "OpsManager",
        "TimeGenerated" : "2015-02-07T14:07:33Z",
        "Source" : "SideBySide",
        "EventLog" : "Application",
        "Computer" : "BAMBAM",
        "EventCategory" : 0,
        "EventLevel" : 1,
        "EventLevelName" : "Error",
        "UserName" : "N/A",
        "EventID" : 78,
        "MG" : "00000000-0000-0000-0000-000000000001",
        "TimeCollected" : "2015-02-07T14:10:04.69Z",
        "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
        "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "Type" : "Event",
        "__metadata" : {
          "Type" : "Event",
          "TimeGenerated" : "2015-02-07T14:07:33Z",
          "highlighting" : {
          "EventLevelName" : ["{[hl]}Error{[/hl]}"]
        }
      }]
    ],
            "start" : "2015-02-04T21:03:29.231Z",
            "end" : "2015-02-11T21:03:29.231Z"
          }
        }
      }]
    }
```

### <a name="searchid---actionread"></a>Buscar / {identificador} - acción o leer

**Solicitar el contenido de una búsqueda guardada:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

>[AZURE.NOTE] Si la búsqueda devuelve con el estado 'Pendiente', a continuación, los resultados actualizados de sondeo puede hacerse mediante esta API. Después de 6 min, el resultado de la búsqueda se quitarán de la caché y se devolverá el pasado HTTP. Si la solicitud de búsqueda inicial devuelve un estado 'De éxito' inmediatamente, no se agregará a la caché causando este API devolver HTTP pasado si consulta. El contenido de un resultado de HTTP 200 estarán en el mismo formato que la solicitud de búsqueda inicial solo con valores actualizados.

### <a name="saved-searches---rest-only"></a>Búsquedas - resto solo

**Solicitar la lista de búsquedas guardadas:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

Métodos compatibles: colocar eliminar GET

Métodos de conjunto compatibles: obtener

La siguiente tabla describe las propiedades que están disponibles.

|(Propiedad)|Descripción|
|---|---|
|Id.|El identificador único.|
|ETag|**Necesarios para la revisión**. Actualizado por servidor en cada escritura. Valor debe ser igual al valor almacenado actual o ' *' actualizar. 409 devuelve valores antiguos o no es válido.|
|Properties.Query|**Requerido**. La consulta de búsqueda.|
|properties.displayName|**Requerido**. El nombre de presentación definidos de usuario de la consulta. Si modelar como un recurso de Azure, sería una etiqueta.|
|Properties.Category|**Requerido**. Categoría de la consulta definida por el usuario. Si modelar como un recurso de Azure sería una etiqueta.|

>[AZURE.NOTE] La API de búsqueda de análisis de registro devuelve actualmente creadas por el usuario búsquedas de sondeo de búsquedas guardadas en un área de trabajo. La API no devolverá búsquedas guardadas proporcionadas por soluciones en este momento. Esta funcionalidad se agregará en una fecha posterior.

### <a name="create-saved-searches"></a>Crear búsquedas guardadas

**Solicitar:**

```
    $savedSearchParametersJson = "{'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="delete-saved-searches"></a>Eliminar guardado búsquedas

**Solicitar:**

```
    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### <a name="update-saved-searches"></a>Búsquedas de actualización guardado

 **Solicitar:**

```
    $savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="metadata---json-only"></a>Metadatos - JSON solo

Aquí es una manera de ver los campos para todos los tipos de registro para los datos recopilados en el área de trabajo. Por ejemplo, si desea que saber si el tipo de evento tiene un campo denominado equipo, esto es una forma de buscar y confirme.

**Solicitud de campos:**

```
    armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**Respuesta:**

```
    {
      "__metadata" : {
        "schema" : {
          "name" : "Example Name",
          "version" : 2
        },
        "resultType" : "schema",
        "requestTime" : 35
      },
      "value" : [{
          "name" : "MG",
          "displayName" : "MG",
          "type" : "Guid",
          "facetable" : true,
          "display" : false,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
        }, {
          "name" : "ManagementGroupName",
          "displayName" : "ManagementGroupName",
          "type" : "String",
          "facetable" : true,
          "display" : true,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
        }
      ]
    }
```

La siguiente tabla describe las propiedades que están disponibles.

|**(Propiedad)**|**Descripción**|
|---|---|
|nombre|Nombre de campo.|
|displayName|El nombre para mostrar del campo.|
|tipo|El tipo del valor del campo.|
|facetable|Combinación de actual 'indizada', ' almacenado ' y 'faceta' propiedades.|
|Mostrar|Propiedad actual 'mostrar'. True si el campo está visible en la búsqueda.|
|ownerType|Reducir a solo los tipos que pertenecen a onboarded IP.|


## <a name="optional-parameters"></a>Parámetros opcionales
La información siguiente describe parámetros opcionales disponibles.

### <a name="highlighting"></a>Resaltado

El parámetro de "Resaltar" es un parámetro opcional que se puede utilizar para solicitar el subsistema de búsqueda incluyen un conjunto de marcadores en su respuesta.

Estos marcadores indican el inicio y fin texto resaltado que coincida con los términos de la consulta de búsqueda.
Puede especificar los marcadores de inicio y finalización que se usan para ajustar el término resaltado por la búsqueda.

**Ejemplo de consulta de búsqueda**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**Resultados de ejemplo:**

```
    {
        "TimeGenerated":
        "2015-05-18T23:55:59Z", "Source":
        "EventLog": "Application",
        "Computer": "smokedturkey.net",
        "EventCategory": 0,
        "EventLevel":1,
        "EventLevelName":
        "Error"
        "Manager ", "__metadata":
        {
            "Type": "Event",
            "TimeGenerated": "2015-05-18T23:55:59Z",
            "highlighting": {
                "EventLevelName":
                ["{[hl]}Error{[/hl]}"]
            }
        }
    }
```

Observe que el resultado anterior contiene un mensaje de error que ha sido el prefijo y anexa.

## <a name="computer-groups"></a>Grupos de equipos

Grupos de equipos son especiales búsquedas guardadas que devuelven un conjunto de equipos.  Puede usar un grupo de equipos en otras consultas para limitar los resultados a los equipos en el grupo.  Un grupo de equipos se implementa como una búsqueda guardada con una etiqueta de grupo con un valor del equipo.

A continuación se muestra una respuesta de ejemplo para un grupo de equipos.

    "etag": "W/\"datetime'2016-04-01T13%3A38%3A04.7763203Z'\"",
    "properties": {
        "Category": "My Computer Groups",
        "DisplayName": "My Computer Group",
        "Query": "srv* | Distinct Computer",
        "Tags": [{
            "Name": "Group",
            "Value": "Computer"
        }],
    "Version": 1
    }

### <a name="retrieving-computer-groups"></a>Recuperar grupos de equipos

Use el método Get con el identificador del grupo para recuperar un grupo de equipos.

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### <a name="creating-or-updating-a-computer-group"></a>Crear o actualizar un grupo de equipos

Use el método superponer con un único guarda el identificador de búsqueda para crear un nuevo grupo de equipo. Si utiliza un ID de grupo de equipo existente, se modificará que uno. Al crear un grupo de equipos en la consola OMS, se crea el identificador del grupo y name.

La consulta usada para la definición de grupo debe devolver un conjunto de equipos para el grupo para que funcione correctamente.  Se recomienda que finalice la consulta con *| Equipos distintos* para asegurarse de que se devuelven los datos correctos.

La definición de la búsqueda guardada debe incluir una etiqueta con el nombre de grupo con un valor de equipo para la búsqueda se clasifican como un grupo de equipos.

    $etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
    $groupName="My Computer Group"
    $groupQuery = "Computer=srv* | Distinct Computer"
    $groupCategory="My Computer Groups"
    $groupID = "My Computer Groups | My Computer Group"

    $groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson

### <a name="deleting-computer-groups"></a>Eliminar grupos de equipos

Para eliminar un grupo de equipos, use el método de eliminación con el identificador del grupo.

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de las [búsquedas de registro](log-analytics-log-searches.md) crear consultas con campos personalizados de criterios.
