<properties 
    pageTitle="Guía de inicio rápido: API de recomendaciones de aprendizaje de equipo | Microsoft Azure" 
    description="Máquina Azure aprendizaje recomendaciones - Guía de inicio rápido" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/>

# <a name="quick-start-guide-for-the-machine-learning-recommendations-api"></a>Guía de inicio rápido de la API de recomendaciones de aprendizaje de equipo

>[AZURE.NOTE]Debe comenzar a usar el servicio cognitivas API de recomendaciones en lugar de esta versión. El servicio de recomendaciones cognitivas se van a reemplazar este servicio y las nuevas características se desarrollará allí. Tiene nuevas capacidades como lotes de soporte técnico, una mejor API Explorer, una experiencia de suscripción y facturación de superficie, más coherente API más limpio, etcetera.
> Más información acerca de la [migración al nuevo servicio cognitivas](http://aka.ms/recomigrate)


Este documento se describe cómo incorporados a su servicio o la aplicación para utilizar las recomendaciones de aprendizaje de Microsoft Azure máquina. Puede encontrar más detalles acerca de la API de recomendaciones en la [Galería](http://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="general-overview"></a>Descripción general

Para usar recomendaciones de aprendizaje del equipo de Azure, debe realizar los pasos siguientes:

* Crear un modelo: un modelo es un contenedor de los datos de uso, catálogo de datos y el modelo de recomendación.
* Importar datos de catálogo - catálogo contienen información de metadatos de los elementos. 
* Importar datos de uso - datos de uso se pueden cargar en una de las maneras de 2 (o ambos):
    * Cargar un archivo que contiene los datos de uso.
    * Mediante el envío de eventos de adquisición de datos.
    Normalmente carga un archivo de uso para poder crear un modelo de recomendación inicial (inicio) y usarlo hasta que el sistema recopila suficientes datos utilizando el formato de adquisición de datos.
* Cree un modelo de recomendación: se trata de una operación asincrónica en la que el sistema de recomendación toma todos los datos de uso y crea un modelo de recomendación. Esta operación puede tardar varios minutos o varias horas, según el tamaño de los datos y los parámetros de configuración de compilación. Al desencadenar la compilación, obtendrá un identificador de compilación. Usarlo para comprobar si ha finalizado el proceso de generación antes de empezar a utilizar recomendaciones.
* Consumir recomendaciones - obtener recomendaciones para una lista de elementos o un elemento específico.

Todos los pasos anteriores se realizan a través de la API de recomendaciones de aprendizaje de máquina de Azure.  Puede descargar una aplicación de ejemplo que implementa cada uno de estos pasos de la [Galería así.](http://1drv.ms/1xeO2F3)

##<a name="limitations"></a>Limitaciones

* El número máximo de modelos por suscripción es 10.
* El número máximo de elementos que puede contener un catálogo es 100,000.
* El número máximo de puntos de uso que se conservan es ~ 5.000.000. El más antiguo se eliminarán si se cargó o notificadas nuevos.
* El tamaño máximo de datos que se pueden enviar en publicación (por ejemplo, importar datos de catálogo, importar datos de uso) es 200MB.
* Es el número de transacciones por segundo para una compilación de modelo de recomendación que no esté activa ~ 2TPS. Una compilación de modelo de recomendación está activa puede alojar hasta 20TPS.

##<a name="integration"></a>Integración

###<a name="authentication"></a>Autenticación
Microsoft Azure Marketplace es compatible con la básica o OAuth método de autenticación. Puede encontrar fácilmente las teclas cuenta desplazándose hasta las teclas en el catálogo de soluciones en [la configuración de cuenta](https://datamarket.azure.com/account/keys). 
####<a name="basic-authentication"></a>Autenticación básica
Agregue el encabezado de autorización:

    Authorization: Basic <creds>
               
    Where <creds> = ConvertToBase64("AccountKey:" + yourAccountKey);  
    
Convertir a base 64 (C#)

    var bytes = Encoding.UTF8.GetBytes("AccountKey:" + yourAccountKey);
    var creds = Convert.ToBase64String(bytes);
    
Convertir a base 64 (JavaScript)

    var creds = window.btoa("AccountKey" + ":" + yourAccountKey);
    



###<a name="service-uri"></a>URI del servicio
Es la raíz de servicio URI para las API de recomendaciones de aprendizaje de Azure máquina [aquí.](https://api.datamarket.azure.com/amla/recommendations/v2/)

El URI del servicio completo se expresa utilizando los elementos de la especificación de OData.

###<a name="api-version"></a>Versión API
Al final, cada llamada a la API tendrá un parámetro de consulta llamado apiVersion que debe establecerse a "1.0".

###<a name="ids-are-case-sensitive"></a>Identificadores distinguen mayúsculas de minúsculas
Identificadores, devueltas por cualquiera de las API distinguen mayúsculas de minúsculas y se deben utilizar como tales cuando se pasa como parámetros en llamadas API posteriores. Por ejemplo, los identificadores de modelo y catálogo distinguen mayúsculas de minúsculas.

###<a name="create-a-model"></a>Crear un modelo
Crear una solicitud de "Crear modelo":

| Método HTTP | URI |
|:--------|:--------|
|Exponer     |`<rootURI>/CreateModel?modelName=%27<model_name>%27&apiVersion=%271.0%27`<br><br>Ejemplo:<br>`<rootURI>/CreateModel?modelName=%27MyFirstModel%27&apiVersion=%271.0%27`|

|   Nombre de parámetro  |   Valores válidos                        |
|:--------          |:--------                              |
|   modelName   |   Sólo letras (de a Z, a z), números (0-9), guiones (-) y subrayado (_) permitidos.<br>La longitud máxima: 20 |
|   apiVersion      | 1.0 |
|||
| Cuerpo de la solicitud | NINGUNO |


**Respuesta**:

Código de estado HTTP: 200

- `feed/entry/content/properties/id`-Contiene el identificador del modelo.
**Nota**: el identificador de modelo distingue mayúsculas de minúsculas.

XML de OData

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Create A New Model</subtitle>
      <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T06:35:21Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
      </entry>
    </feed>


###<a name="import-catalog-data"></a>Importar datos de catálogo

Si cargar varios archivos de catálogo al mismo modelo con varias llamadas, se insertará los nuevos elementos de catálogo. Los elementos existentes permanecerán con los valores originales.

| Método HTTP | URI |
|:--------|:--------|
|Exponer     |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Ejemplo:<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|   Nombre de parámetro  |   Valores válidos                        |
|:--------          |:--------                              |
|   modelId |   Identificador único del modelo (distingue entre mayúsculas y minúsculas)  |
| nombre de archivo | Identificador textual del catálogo.<br>Sólo letras (de a Z, a z), números (0-9), guiones (-) y subrayado (_) permitidos.<br>La longitud máxima: 50 |
|   apiVersion      | 1.0 |
|||
| Cuerpo de la solicitud | Catálogo de datos. Formato:<br>`<Item Id>,<Item Name>,<Item Category>[,<description>]`<br><br><table><tr><th>Nombre</th><th>Obligatorio</th><th>Tipo</th><th>Descripción</th></tr><tr><td>Id. de elemento</td><td>Sí</td><td>Longitud alfanumérico, max 50</td><td>Identificador único de un elemento</td></tr><tr><td>Nombre del elemento</td><td>Sí</td><td>255 de longitud alfanumérico, max</td><td>Nombre del elemento</td></tr><tr><td>Categoría de producto</td><td>Sí</td><td>255 de longitud alfanumérico, max</td><td>Categoría a la que pertenece (por ejemplo, cocina libretas de series...) este elemento</td></tr><tr><td>Descripción</td><td>No</td><td>Longitud alfanumérico, max 4000</td><td>Descripción de este elemento</td></tr></table><br>Tamaño de archivo máximo es 200MB.<br><br>Ejemplo:<br><pre>2406e770-c 769-4189-89de-1c9283f93a96, Clara Callan, libro<br>21bf8088-b6c0-4509-870C-e1c7ac78304a, la sala olvidar: una ficción (Byzantium libro), de libro<br>Libreta de 3bb5cb44-d143-4bdd-a55c-443964bf4b23, Spadework,<br>552a1940-21e4-4399-82bb-594b46d7ed54, retención de Animals, libro</pre> |


**Respuesta**:

Código de estado HTTP: 200

- `Feed\entry\content\properties\LineCount`-Aceptado número de líneas.
- `Feed\entry\content\properties\ErrorCount`-Número de líneas que no se insertaron debido a un error.

XML de OData

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
        <subtitle type="text">Import catalog file</subtitle>
        <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">ImportCatalogFileEntity2</title>
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">4</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
      </m:properties>
    </content>
     </entry>
    </feed>


###<a name="import-usage-data"></a>Importar datos de uso

####<a name="uploading-a-file"></a>Cargar un archivo
Esta sección muestra cómo cargar datos de uso mediante un archivo. Puede llamar a esta API varias veces con datos de uso. Todos los datos de uso se guardará para todas las llamadas.

| Método HTTP | URI |
|:--------|:--------|
|Exponer     |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>Ejemplo:<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|   Nombre de parámetro  |   Valores válidos                        |
|:--------          |:--------                              |
|   modelId |   Identificador único del modelo (distingue entre mayúsculas y minúsculas) |
| nombre de archivo | Identificador textual del catálogo.<br>Sólo letras (de a Z, a z), números (0-9), guiones (-) y subrayado (_) permitidos.<br>La longitud máxima: 50 |
|   apiVersion      | 1.0 |
|||
| Cuerpo de la solicitud | Datos de uso. Formato:<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>Nombre</th><th>Obligatorio</th><th>Tipo</th><th>Descripción</th></tr><tr><td>Identificador de usuario</td><td>Sí</td><td>Alfanumérico</td><td>Identificador único de un usuario</td></tr><tr><td>Id. de elemento</td><td>Sí</td><td>Longitud alfanumérico, max 50</td><td>Identificador único de un elemento</td></tr><tr><td>Hora</td><td>No</td><td>Fecha en formato: aaaa/MM/ddTHH (por ejemplo, 2013/06/20T10:00:00)</td><td>Tiempo de datos</td></tr><tr><td>Evento</td><td>No, si proporcionada, a continuación, también debe colocar fecha</td><td>Uno de estos procedimientos:<br>• Haga clic en<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Compra</td><td></td></tr></table><br>Tamaño de archivo máximo es 200MB.<br><br>Ejemplo:<br><pre>149452, 1b3d95e2 84e4 414c bb38 be9cf461c347<br>6360, 1b3d95e2 84e4 414c bb38 be9cf461c347<br>50321, 1b3d95e2 84e4 414c bb38 be9cf461c347<br>71285, 1b3d95e2 84e4 414c bb38 be9cf461c347<br>224450, 1b3d95e2 84e4 414c bb38 be9cf461c347<br>236645, 1b3d95e2 84e4 414c bb38 be9cf461c347<br>107951, 1b3d95e2 84e4 414c bb38 be9cf461c347</pre> |

**Respuesta**:

Código de estado HTTP: 200

- `Feed\entry\content\properties\LineCount`-Aceptado número de líneas.
- `Feed\entry\content\properties\ErrorCount`-Número de líneas que no se insertaron debido a un error.
- `Feed\entry\content\properties\FileId`-Identificador de archivo.


XML de OData

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Add bulk usage data (usage file)</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
    </entry>
    </feed>


####<a name="using-data-acquisition"></a>Uso de adquisición de datos
Esta sección muestra cómo enviar eventos en tiempo real recomendaciones de aprendizaje del equipo de Azure, normalmente desde el sitio Web.

| Método HTTP | URI |
|:--------|:--------|
|Exponer     |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|

|   Nombre de parámetro  |   Valores válidos                        |
|:--------          |:--------                              |
|   apiVersion      | 1.0 |
|||
|Cuerpo de la solicitud| Entrada de datos de evento para cada evento que desea enviar. Debe enviar para la misma sesión de usuario o explorador el mismo identificador en el campo Id. (Consulte ejemplo de cuerpo de evento siguiente).|


- Ejemplo de evento 'Haga clic':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>Click</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Ejemplo de evento 'RecommendationClick':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>RecommendationClick</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Ejemplo de evento 'AddShopCart':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>AddShopCart</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Ejemplo de evento 'RemoveShopCart':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>RemoveShopCart</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        </EventData>
        </EventData>
        </Event>

- Ejemplo de evento 'Compra':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
            <Name>Purchase</Name> 
            <PurchaseItems>
            <PurchaseItems>
                <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
                <Count>3</Count>
            </PurchaseItems>
        </PurchaseItems>
        </EventData>
        </EventData>
        </Event>

- Ejemplo enviando 2 eventos, 'Haga clic en' y 'AddShopCart':

        <Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
        <SessionId>11112222</SessionId>
        <EventData>
        <EventData>
        <Name>Click</Name>
        <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
        <ItemName>itemName</ItemName>
        <ItemDescription>item description</ItemDescription>
        <ItemCategory>category</ItemCategory>
        </EventData>
        <EventData>
        <Name>AddShopCart</Name>
        <ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
        </EventData>
        </EventData>
        </Event>

**Respuesta**: código de estado HTTP: 200

###<a name="build-a-recommendation-model"></a>Cree un modelo de recomendación

| Método HTTP | URI |
|:--------|:--------|
|Exponer     |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>Ejemplo:<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|

|   Nombre de parámetro  |   Valores válidos                        |
|:--------          |:--------                              |
| modelId | Identificador único del modelo (distingue entre mayúsculas y minúsculas)  |
| userDescription | Identificador textual del catálogo. Tenga en cuenta que si usa espacios se debe codificar con % 20 en su lugar. Consulte el ejemplo anterior.<br>La longitud máxima: 50 |
| apiVersion | 1.0 |
|||
| Cuerpo de la solicitud | NINGUNO |

**Respuesta**:

Código de estado HTTP: 200

Esta es una API asincrónica. Obtendrá un identificador de compilación como respuesta. Para saber cuándo ha finalizado la generación, debe llamar a la API "Obtener crea estado de un modelo de" y busque este ID de compilación en la respuesta. Tenga en cuenta que puede tomar una compilación de los minutos horas dependiendo del tamaño de los datos.

No puede utilizar recomendaciones hasta la compilación finaliza.

Estado de compilación válida:

- Crear: se creó el modelo.
- En la cola: activó la compilación de modelo y está en la cola.
- Edificio: modelo se está generando.
- Éxito: generar finalizó correctamente.
- Error: generar finaliza con un error.
- Cancelado: se canceló la compilación.
- Cancelar: generar se cancela.


Tenga en cuenta que se puede encontrar el identificador de compilación en la siguiente ruta de acceso:`Feed\entry\content\properties\Id`

XML de OData

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Build a Model with RequestBody</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
    </entry>
    </feed>

###<a name="get-build-status-of-a-model"></a>Obtener el estado de generación de un modelo

| Método HTTP | URI |
|:--------|:--------|
|Obtener     |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>Ejemplo:<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|



|   Nombre de parámetro  |   Valores válidos                        |
|:--------          |:--------                              |
|   modelId         |   Identificador único del modelo (distingue entre mayúsculas y minúsculas)    |
|   onlyLastBuild   |   Indica si se devolverá todo el historial de compilación del modelo o solo el estado de la versión más reciente. |
|   apiVersion      |   1.0                                 |


**Respuesta**:

Código de estado HTTP: 200

La respuesta incluye una entrada por compilación. Cada entrada tiene los siguientes datos:

- `feed/entry/content/properties/UserName`: Nombre del usuario.
- `feed/entry/content/properties/ModelName`: Nombre del modelo.
- `feed/entry/content/properties/ModelId`– Identificador único del modelo.
- `feed/entry/content/properties/IsDeployed`: Si la compilación se implementa (también conocido como compilación activa).
- `feed/entry/content/properties/BuildId`: Crear un identificador único.
- `feed/entry/content/properties/BuildType`-Tipo de la compilación.
- `feed/entry/content/properties/Status`: Estado de la generación. Puede ser una de las siguientes acciones: Error, creación, en cola, Cancelling, cancelada, éxito
- `feed/entry/content/properties/StatusMessage`: Mensaje de estado detallada (se aplica solo a los estados concretos).
- `feed/entry/content/properties/Progress`– (%) De progreso de la compilación.
- `feed/entry/content/properties/StartTime`: Hora de inicio compilación.
- `feed/entry/content/properties/EndTime`: Crear la hora de finalización.
- `feed/entry/content/properties/ExecutionTime`-Duración de la compilación.
- `feed/entry/content/properties/ProgressStep`– Detalles acerca de la fase actual que tiene una generación en curso.

Estado de compilación válida:
- Creado: se creó la entrada de solicitud de compilación.
- En la cola: activó la solicitud de compilación y está en la cola.
- Edificio: compilación está en curso.
- Éxito: generar finalizó correctamente.
- Error: generar finaliza con un error.
- Cancelado: se canceló la compilación.
- Cancelar: generar se cancela.

Valores válidos para el tipo de generación:
- Jerarquía de rango, compilación. (Para clasificar crear detalles, consulte el documento "Documentación de recomendación de aprendizaje de máquina API".)
- Recomendación: generación de recomendación.
- FBT - adquirido conjuntamente con frecuencia compilación.

XML de OData

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get builds status of a model</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetBuildsStatusEntity</title>
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
        <d:ModelName m:type="Edm.String">ModelName</d:ModelName>
        <d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
        <d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
        <d:BuildId m:type="Edm.String">1000272</d:BuildId>
        <d:BuildType m:type="Edm.String">Recommendation</d:BuildType>
        <d:Status m:type="Edm.String">Success</d:Status>
        <d:StatusMessage m:type="Edm.String"></d:StatusMessage>
        <d:Progress m:type="Edm.String">0</d:Progress>
        <d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
        <d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
        <d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
        <d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
        <d:ProgressStep m:type="Edm.String"></d:ProgressStep>
      </m:properties>
     </content>
    </entry>
    </feed>


###<a name="get-recommendations"></a>Obtener recomendaciones

| Método HTTP | URI |
|:--------|:--------|
|Obtener     |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>Ejemplo:<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|



|   Nombre de parámetro  |   Valores válidos                        |
|:--------          |:--------                              |
| modelId | Identificador único del modelo (distingue entre mayúsculas y minúsculas) |
| ItemId | Lista de valores separados por comas de los elementos recomendar para.<br>La longitud máxima: 1024 |
| numberOfResults | Número de resultados requeridos |
| includeMetatadata | Usos futuros, siempre es false |
| apiVersion | 1.0 |

**Respuesta:**

Código de estado HTTP: 200

La respuesta incluye una entrada por elemento recomendado. Cada entrada tiene los siguientes datos:

- `Feed\entry\content\properties\Id`-Id. de elemento recomendado
- `Feed\entry\content\properties\Name`-Nombre del elemento.
- `Feed\entry\content\properties\Rating`-Calificación de la recomendación; un número mayor significa confianza superior.
- `Feed\entry\content\properties\Reasoning`-Recomendación razonamiento (por ejemplo, explicaciones de recomendación).

XML de OData

La respuesta de ejemplo siguiente incluye 10 elementos recomendados:

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
     <subtitle type="text">Get Recommendation</subtitle>
     <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
    </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
    </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
    </feed>

###<a name="update-model"></a>Modelo de actualización
Puede actualizar la descripción del modelo o el identificador de compilación activa.
*Identificador de compilación activa* - cada compilación para cada modelo tiene un identificador de compilación. El identificador de compilación activa es la primera generación correcta de cada nuevo modelo. Una vez que tiene un identificador de compilación activa y lo crea adicionales para el mismo modelo, necesita explícitamente establecer como el identificador de compilación de forma predeterminada si desea. Cuando consumir recomendaciones, si no especifica el identificador de compilación que desea usar, se utilizará automáticamente el predeterminado.

Este mecanismo permite - una vez que tenga un modelo de recomendación en producción: para crear nuevos modelos y probarlas antes de ascender a producción.

| Método HTTP | URI |
|:--------|:--------|
|COLOCAR     |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br><br>Ejemplo:<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|


|   Nombre de parámetro  |   Valores válidos                        |
|:--------          |:--------                              |
| Id. | Identificador único del modelo (distingue entre mayúsculas y minúsculas) |
| apiVersion | 1.0 |
|||
| Cuerpo de la solicitud | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`   <Description>New Description</Description>`<br>`          <ActiveBuildId>-1</ActiveBuildId>`<br>`</ModelUpdateParams>`<br><br>Tenga en cuenta que las etiquetas XML, descripción y ActiveBuildId son opcionales. Si no desea establecer la descripción o ActiveBuildId, quite la etiqueta completa. |

**Respuesta**:

Código de estado HTTP: 200

XML de OData

    <feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Update an Existing Model</subtitle>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'</id>
    <rights type="text" />
     <updated>2014-10-05T10:27:17Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v2/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;apiVersion='1.0'" />
    </feed>

##<a name="legal"></a>Legal
Este documento se proporciona "como-es". Información y vistas expresadas en este documento, incluidas las direcciones URL y otras referencias de sitio Web de Internet, pueden cambiar sin previo aviso. Algunos ejemplos de este documento se proporcionan únicamente con fines ilustrativos y son ficticios. Ninguna asociación ni conexión real pretende o reales. Este documento no proporciona ningún derecho legal sobre la propiedad intelectual en cualquier producto de Microsoft. Puede copiar y usar este documento internos referencia. © Microsoft 2014. Reservados todos los derechos. 
 
