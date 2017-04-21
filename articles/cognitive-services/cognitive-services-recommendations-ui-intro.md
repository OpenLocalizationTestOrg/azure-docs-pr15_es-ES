<properties
    pageTitle="Crear un modelo con la UI Recommnendations | Microsoft Azure"
    description="Azure recomendaciones de aprendizaje: creación de un modelo de la interfaz de usuario de recomendaciones"
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
    ms.date="10/11/2016"
    ms.author="luisca"/>

# <a name="building-a-model-with-the-recommendations-ui"></a>Crear un modelo con la interfaz de usuario de recomendaciones

Este documento es una guía paso a paso. Nuestro objetivo es que le guíe durante los pasos necesarios para formar un modelo mediante la [Interfaz de usuario de recomendaciones](https://recommendations-portal.azurewebsites.net/).
Seguir el ejercicio le permite comprender el proceso para crear un modelo antes de hacerlo mediante programación. También ayuda a familiarizarse con la interfaz de usuario, que es útil cuando empiece a usar el servicio.

Este ejercicio tardará unos 30 minutos.

<a name="Step1"></a>
## <a name="step-1---sign-in-to-the-recommendations-ui"></a>Paso 1: inicio de sesión en la interfaz de usuario de recomendaciones ##

1. Si aún no lo ha hecho, debe [suscripción](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) para una nueva suscripción [API de recomendaciones](https://www.microsoft.com/cognitive-services/en-us/recommendations-api) . Puede registrarse para el servicio en **Azure** en [http://portal.azure.com/](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) e inicie sesión con su cuenta de Azure. Se proporcionan instrucciones detalladas sobre el proceso de registro en *1 de la tarea* de la [Guía de introducción](cognitive-services-recommendations-quick-start.md) 

1. Una vez que haya obtenido una **clave** para la suscripción de la API de recomendaciones, vaya a la [Interfaz de usuario de recomendaciones](https://recommendations-portal.azurewebsites.net/). 

1. Inicie sesión en el portal de escribir la clave en el campo de **Clave de cuenta** y, a continuación, haga clic en el botón de **Inicio de sesión** .

    ![Recomendaciones de interfaz de usuario: Inicio de sesión de cuadro de diálogo.][reco_signin]


<a name="Step2"></a>
## <a name="step-2---lets-gather-some-training-data"></a>Paso 2: recopilar vamos a algunos datos de formación ##

Antes de poder crear una compilación, el motor necesita dos partes de la información: un archivo de catálogo y un conjunto de archivos de uso. 

El archivo de catálogo contiene información acerca de los elementos que ofrece al cliente. Un archivo de uso contiene información acerca de cómo se usan los elementos o las transacciones de su empresa.

Normalmente haría consultas a la base de datos de la tienda para estos elementos de información. En la actualidad, ofrecemos algunos datos de ejemplo para usted que puede aprender a usar la API de recomendaciones.

Puede descargar los datos de [http://aka.ms/RecoSampleData](http://aka.ms/RecoSampleData). Copiar y descomprimir el archivo **Books.Zip** a una carpeta en el equipo local. Por ejemplo, **c:\data**.

Encontrará información detallada sobre el esquema de los archivos de catálogo y el uso en el artículo de [recopilación de datos para entrenar el modelo](cognitive-services-recommendations-collecting-data.md) .
 
Para este ejercicio, vamos a trabajar con un pequeño archivo para que no tiene que esperar demasiado para formación muy. Si desea probar con un archivo más realista, hemos **MsStoreData.zip** que contiene las transacciones de ejemplo de Microsoft Store en la [misma ubicación](http://aka.ms/RecoSampleData).

<a name="Step3"></a>
## <a name="step-3---create-a-project-and-upload-catalog-and-usage-data"></a>Paso 3: crear un proyecto y cargar los datos del catálogo y el uso ##

Al iniciar sesión en la [Interfaz de usuario de recomendaciones](https://recommendations-portal.azurewebsites.net/), consulte la página de proyectos. Si ha creado previamente todos los proyectos, debería verlos aquí.
Un proyecto (también conocido como *un modelo* en la referencia de la API) es un contenedor de los datos de catálogo y el uso. Puede crear varios *crea* dentro del proyecto. Le guiaremos a través del proceso en los siguientes pasos.

1. Para crear un nuevo proyecto, escriba el nombre en el cuadro de texto (algo como funcionaría "MyFirstModel") y haga clic en **Agregar proyecto**.
 
    ![Recomendaciones de interfaz de usuario: Página de proyectos.][reco_projects]

1. Una vez que se crea el proyecto, haga clic en el botón **Buscar archivo** en la sección **Agregar un archivo de catálogo** . Cargue el catálogo que obtuvo en el paso 2. Si guardó en *c:\data*, debe ir a la carpeta.

    ![Recomendaciones de interfaz de usuario: Agregar datos a un proyecto.][reco_firstmodel]

1. Después de carga el catálogo, haga clic en el botón **Buscar archivo** en la sección **Agregar archivos de uso** . Agregue el archivo usage_large.txt.

> **¿Qué sucede si tengo un archivo de datos de uso grande?**
>
> Se puede cargar sólo de los archivos de uso menos de 200 MB. Dicho esto, el sistema puede alojar hasta por valor de 2 GB de datos de transacciones para cargar más de un archivo si es necesario.
> Es posible que no tenga tantos datos para generar un buen modelo, no es el tamaño de los datos que se importa, pero la calidad de los datos. Es común para ver datos de uso, donde la mayoría de las transacciones son solo en unos cuantos elementos populares y hay "algo de señal" para otros elementos.

<a name="Step4"></a>
## <a name="step-4---lets-do-some-training"></a>Paso 4: vamos a hacer algunos cursos! ##

Ahora que ha cargado el catálogo y los datos de uso, estamos listos para entrenar el motor de modo que pueden obtener tramas de nuestros datos.

1.  Haga clic en el botón **Generar nueva** .

1.  Seleccione **recomendaciones** como el tipo de generación. Observe que admitimos la clasificación generar y un FBT (con frecuencia adquirido juntos) crear tipos también.

    ![Recomendaciones de interfaz de usuario: Crear el cuadro de diálogo.][reco_build_dialog.png]


    Una generación FBT le permite identificar patrones para los productos que están normalmente comprado/consumida en la misma transacción.
    Una compilación de clasificación se usa para identificar las características de interés. 
    No vamos muy profundizar en FBT o clasificación se basa en este talleres, pero si está interesado debe desproteger los [tipos de generación y página de documentación de calidad de modelo](cognitive-services-recommendations-buildtypes.md).

1. Haga clic en el botón **Generar** . El proceso de generación tarda unos cinco minutos, si está utilizando los datos de libros. Se tarda más en conjuntos de datos más grande.

<a name="Step5"></a>
## <a name="step-5---lets-find-out-what-the-machine-learned"></a>Paso 5: vamos a averiguar lo aprendido la máquina! ##

Una vez completada la compilación, verá una nueva compilación en la lista de versiones. Esta versión de compilación se puede consultar para obtener recomendaciones de usuario y el elemento.

1. Una vez completada la compilación, haga clic en **puntuación**. Esto le permite jugar con el modelo y vea qué elementos se recomiendan.

    ![Recomendaciones de interfaz de usuario: Botón de calificación][reco_score_button]

1. Seleccione un elemento para ver qué elementos se devuelven como recomendaciones para ese elemento. Tenga en cuenta que si no hay suficientes transacciones de predecir una recomendación para un elemento en particular, el sistema no devolverá ninguna recomendación para ese elemento.  Si tiene un elemento que no devuelve ninguna recomendación por algún motivo, intente puntuación otros elementos.

<a name="Step6"></a>
## <a name="step-6---next-steps"></a>Paso 6: pasos siguientes ##
¡Felicidades! tiene capacitado un modelo y, a continuación, ¿tiene recomendaciones de ese modelo.  La interfaz de usuario de recomendaciones es una herramienta útil que le permite ver el estado de los proyectos y genera. 

Ahora que ya tiene un modelo, desea obtener información sobre cómo realizar los pasos anteriores mediante programación. Para aprender a llamar a la API mediante programación, nos invitar a comprobar la [Referencia de la API de recomendaciones](http://go.microsoft.com/fwlink/?LinkId=759348) y descargue la [Aplicación de ejemplo de recomendaciones](http://go.microsoft.com/fwlink/?LinkID=759344).


[reco_signin]:../media/cognitive-services/reco_signin.PNG
[reco_projects]:../media/cognitive-services/reco_projects.PNG
[reco_firstmodel]:../media/cognitive-services/reco_firstmodel.png
[reco_build_dialog.png]:../media/cognitive-services/reco_build_dialog.png
[reco_score_button]:../media/cognitive-services/reco_score_button.png
