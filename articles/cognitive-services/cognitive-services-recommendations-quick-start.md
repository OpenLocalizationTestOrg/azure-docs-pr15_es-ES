<properties
    pageTitle="Guía de inicio rápido: API de recomendaciones de aprendizaje de equipo | Microsoft Azure"
    description="Máquina Azure aprendizaje recomendaciones - Guía de inicio rápido"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="luisca"/>

# <a name="quick-start-guide-for-the-cognitive-services-recommendations-api"></a>Guía de inicio rápido de la API de recomendaciones cognitivas de servicios

Este documento se describe cómo incorporados a su servicio o la aplicación para que utilice la [API de recomendaciones](http://go.microsoft.com/fwlink/?LinkId=759710).
Puede encontrar más detalles sobre la API de recomendaciones y otros servicios cognitivas [aquí](http://go.microsoft.com/fwlink/?LinkId=759709). En esta guía, también puede encontrar la [Referencia de la API de recomendaciones](http://go.microsoft.com/fwlink/?LinkId=759348) útiles.


<a name="Overview"></a>
## <a name="general-overview"></a>Descripción general

Este documento es una guía paso a paso. Nuestro objetivo es que le guíe durante los pasos necesarios para formar un modelo y a recursos que le permitirá utilizar el modelo de su entorno de producción.

Este ejercicio tardará unos 30 minutos.

Para usar la [API de recomendaciones](http://go.microsoft.com/fwlink/?LinkId=759710), debe realizar los pasos siguientes:

1. Crear un modelo: un modelo es un contenedor de los datos de uso, catálogo de datos y el modelo de recomendación.
1. Importar datos de catálogo - catálogo contienen información de metadatos acerca de los elementos.
1. Importar datos de uso - datos de uso se pueden cargar en una de las maneras de 2 (o ambos):
  -  Cargar un archivo que contiene los datos de uso.
  -  Envío de eventos de adquisición de datos.
  Normalmente carga un archivo de uso para poder crear un modelo de recomendación inicial (inicio) y usarlo hasta que el sistema recopila suficientes datos utilizando el formato de adquisición de datos.
1. Cree un modelo de recomendación: se trata de una operación asincrónica en la que el sistema de recomendación toma todos los datos de uso y crea un modelo de recomendación. Esta operación puede tardar varios minutos o varias horas, según el tamaño de los datos y los parámetros de configuración de compilación. Al desencadenar la compilación, obtendrá un identificador de compilación. Usarlo para comprobar si ha finalizado el proceso de generación antes de empezar a utilizar recomendaciones.
1. Consumir recomendaciones - obtener recomendaciones para una lista de elementos o un elemento específico.

<a name="GetStarted"></a>
### <a name="lets-get-started"></a>Comencemos.

Para empezar la creación de un modelo de recomendaciones. A continuación, se le guiará sobre cómo usar los resultados generados por el modelo de recomendaciones de energía en un sitio de comercio electrónico.

<a name="Ex1Task1"></a>
#### <a name="task-1---signing-up-for-the-recommendations-api"></a>Tarea 1: firma para la API de recomendaciones ####

En esta tarea, deberá iniciar sesión en el servicio de recomendaciones API y crear un modelo de recomendaciones.

1. Vaya al **Portal de Azure** en [http://portal.azure.com/](http://portal.azure.com/) e inicie sesión con su cuenta de Azure.

1.  Haga clic en **+ nuevo**.

1. Seleccione la opción de **inteligencia** .

1. Seleccione el producto **Cognitivas API de los servicios** .
Este producto permiten iniciar una suscripción para cualquiera de los servicios cognitivos API (cara, el análisis de texto, visión de equipo, etcetera). Hoy nos centraremos en la API de recomendaciones.

1. En la página de inicio de la API de servicios cognitivas, escriba el **nombre de la cuenta** para la suscripción de recomendaciones. (Para instancia: "MyRecommendations"). Este nombre no debe tener espacios en él.

1. En **tipo de API**, seleccione **recomendaciones**.

1. En el **nivel de precios**, puede seleccionar un plan. Puede seleccionar la capa de **libre** de 10.000 transacciones y mes. Se trata de un plan libre, por lo que es una buena manera de empezar a probar el sistema. Una vez que va a producción, le recomendamos que tenga en cuenta el volumen de la solicitud y cambia el tipo de plan en consecuencia. (Nota: puede tener solo una suscripción gratuita de nivel en un momento)

1. Seleccione un **Grupo de recursos**o cree uno nuevo si aún no tiene una.

1. Puede cambiar otros elementos en el cuadro de diálogo Crear. Debemos señalar que el proveedor de recursos hoy solo es compatible de centros de datos de Estados Unidos.

1. Una vez que haya terminado con todas las selecciones, haga clic en **crear**.

1. Espere unos minutos para el recurso para su implementación.
Una vez que se ha implementado, puede ir a la sección de **teclas** en el módulo de **configuración** donde se le proporcionará una clave principal y secundaria para utilizar la API.  Copiar la clave principal, deberá al crear el primer modelo.

<a name="Ex1Task2"></a>
#### <a name="task-2---did-you-bring-your-data"></a>Tarea 2: ¿poner los datos? ####

La API de recomendaciones aprenderá desde el catálogo y las transacciones para proporcionar recomendaciones buenos productos. Esto significa que necesita fuente con datos sobre sus productos (llamamos a esto un archivo de **catálogo** ) y un conjunto de transacciones lo suficientemente grandes como para que pueda buscar modelos interesantes de consumo (llamamos este **uso**).

1. Normalmente haría consultas a la base de datos de transacciones para estos elementos de información.
En el caso de no tiene muy útil, ofrecemos algunos datos de ejemplo en función de los datos de transacciones de Microsoft Store.

 Puede descargar los datos desde [aquí](http://aka.ms/RecoSampleData). Copiar y descomprimir el MsStoreData.Zip a una carpeta en el equipo local.

 > **Nota:** El código de ejemplo que va a descargar y ejecutar en la tarea 3 tiene los datos de ejemplo ya incrustados dentro de ella, para que esta tarea es opcional.  Dicho esto, esta tarea, podrá descargar más realistas conjuntos de datos y le permiten comprender las entradas en la API de recomendaciones mejor.

1.  Ahora veamos el archivo de catálogo. Navegue hasta la ubicación donde copió los datos.
 Abra el archivo de catálogo en **el Bloc de notas**.

 Observe que el archivo de catálogo es muy sencillo. Tiene el siguiente formato`<itemid>,<item name>,<product category>`

 >  AAA-04294, DwnLd de OfficeLangPack 2013 de 32 o 64 E34 en línea, Office <br>
 > AAA-04303, DwnLd de OfficeLangPack 2013 de 32 o 64 y en línea, Office  <br>
 > C9F 00168, KRUSELL Kiruna voltear portada para Nokia Lumia 635 - Camel, Accesorios

 Debemos señalar que un archivo de catálogo puede ser mucho más rico, por ejemplo puede agregar metadatos acerca de los productos (llamamos estas *características del elemento*). Verá la sección [formato de catálogo](http://go.microsoft.com/fwlink/?LinkID=760716) en la referencia de la API para obtener más detalles sobre el formato de catálogo.

1. Vamos a hacer lo mismo con los datos de uso. Verá que la fecha de uso tiene el formato `<User Id>,<Item Id>,<Time Stamp>,<Event>`.

  > 00037FFEA61FCA16, 288186200, 2015/08/04T11:02:52, 0003BFFDD4C2148C de compra, 297833400, 08/2015/04T11:02:50, 0003BFFDD4C2118D de compra, 297833300, 08/2015/04T11:02:40, 00030000D16C4237 de compra, 297833300, 08/2015/04T11:02:37, 0003BFFDD4C20B63 de compra, 297833400, 08/2015/04T11:02:12, 00037FFEC8567FB8 de compra, 297833400, 08/2015/04T11:02:04, compra

Observe que los tres primeros elementos son obligatorios. El tipo de evento es opcional. Puede comprobar el [formato de uso](http://go.microsoft.com/fwlink/?LinkID=760712) para obtener más información sobre este tema.

 > **¿Cuántos datos necesita?**
 <p>
Bien, realmente depende de los datos de uso. El sistema aprende cuando los usuarios comprar distintos elementos. Algunas versiones como FBT, es importante saber qué elementos se adquieren en las transacciones de la misma. (Llamamos esta *con repeticiones*). Una buena regla general es que la mayoría de elementos en las transacciones de 20 o más, por lo que si tiene 10.000 elementos del catálogo, recomendamos que tiene al menos 20 horas ese número de transacciones o aproximadamente 200.000.
Una vez más, se trata de una regla general. Debe experimentar con sus datos.
</p>

<a name="Ex1Task3"></a>
####Tarea 3: creación de un modelo de recomendaciones####

Ahora que tiene una cuenta y tiene datos, vamos a crear su primer modelo.

En esta tarea, usará la aplicación de ejemplo para crear su primer modelo.

1. En primer lugar debe tener en cuenta de la [Referencia de la API de recomendaciones](http://go.microsoft.com/fwlink/?LinkId=759348).

1. Descargue la [Aplicación de ejemplo](http://go.microsoft.com/fwlink/?LinkID=759344) en una carpeta local.

1. En Visual Studio, abra la solución **RecommendationsSample.sln** ubicada en la carpeta **C#** .

1. Abra el archivo **SampleApp.cs** . Nota que estos pasos en el archivo:
 + Crear un modelo
 + Agregar un archivo de catálogo
 + Agregar un archivo de uso
 + Desencadenar una compilación del modelo
 + Obtener una recomendación basándose en un par de elementos
<p></p>

1. Reemplace el valor del campo **AccountKey** con la clave de la tarea 1.

1. Paso a través de la solución, y se muestra cómo se crea un modelo.

1. Pruebe a reemplazar los archivos de catálogo y el uso que acaba de descargar para crear un nuevo modelo de Microsoft Store o de recomendaciones de libro. Debe cambiar el nombre del modelo y los elementos cuya solicitar recomendaciones.

1. Cuando se crea el modelo, tome nota del **identificador de modelo** como tendrán al solicitar recomendaciones en su entorno de producción.

>  Obtener más información sobre tipos de generación y cómo se evalúa la calidad de versiones [aquí](cognitive-services-recommendations-buildtypes.md).

<a name="Ex1Task4"></a>
### <a name="putting-your-model-in-production"></a>¡Colocación de un modelo en producción! ###

Ahora que sabe cómo crear un modelo y consumir recomendaciones, el siguiente paso es poner en producción en su sitio Web, aplicaciones móviles o integrar en su sistema CRM o ERP.
Evidentemente, cada una de estas implementaciones sería diferente. Puesto que la API de recomendaciones se solicitan como un servicio web, debería poder llamarlo desde cualquiera de estos diferentes entornos fácilmente.

**Importante:**  Si desea mostrar recomendaciones desde un cliente público (por ejemplo, su sitio de portal de negocio), debe crear un servidor proxy para proporcionar las recomendaciones. Esto es importante para que la clave de API no se expone a entidades (no sea de confianza) externas.

Estas son algunas ideas de ubicaciones donde puede usar recomendaciones:

### <a name="product-page"></a>Página del producto

**Recomendaciones de elemento**
<p>Si se ha preparado el modelo de datos de compra permitirá a cliente para *detectar los productos que están probable que sean de interés para las personas que han comprado el elemento de origen*.</p>
<p>Si el modelo se ha preparado en, haga clic en datos, permitirá su cliente para *detectar los productos que puedan ser visitado por personas que han visitado el elemento de origen*. Este tipo de modelo puede devolver elementos similares.</p>

**Con frecuencia juntos adquirido recomendaciones**
<p>Podría formar A preguntas juntos adquirido generar, para poder acceder conjuntos de elementos están probable que se puede comprar junto con este elemento.</p>

### <a name="check-out-page"></a>Desproteger página

**Recomendaciones de elemento**
<p>Recomendaciones modelo podría tardar como entrada de una lista de elementos. Por lo que podría pasar todos los elementos en la cesta como entrada para obtener recomendaciones.
En este caso, el modelo le proporcionará recomendaciones que sean de interés dado todos los elementos en la cesta.
</p>

### <a name="landing-page"></a>Página de inicio

**Recomendaciones de usuario**
<p>
Recomendaciones puede tomar el modelo como entrada el identificador de usuario.  El historial de las transacciones por ese usuario usará para proporcionar recomendaciones personalizadas para el usuario especificado.
</p>

Consulte la [Documentación de recomendaciones de elemento de obtener](http://go.microsoft.com/fwlink/?LinkID=760719).

<a name="Ex1Task6"></a>
### <a name="whats-next"></a>¿Qué es el siguiente?
Enhorabuena si ha llegado este extremo! Para obtener que más puede visitar la [Referencia de la API de recomendaciones](http://go.microsoft.com/fwlink/?LinkId=759348) de completa si tienes alguna pregunta, no dude en ponerse en contacto con nosotros enmlapi@microsoft.com
