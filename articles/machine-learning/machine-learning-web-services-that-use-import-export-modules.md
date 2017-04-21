<properties
    pageTitle="Implementar servicios web de Azure v que utilizan módulos de importación y exportación de datos | Microsoft Azure"
    description="Obtenga información sobre cómo usar los módulos datos importar y exportar datos para enviar y recibir datos de un servicio web."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondlaghaeian"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="v-donglo"/>



# <a name="deploying-azure-ml-web-services-that-use-data-import-and-data-export-modules"></a>Implementar servicios web de Azure v que utilizan módulos de importación y exportación de datos 

Cuando se crea un ensayo predictivo, normalmente agregar una entrada de servicio web y los resultados. Cuando se implementa el ensayo, los consumidores pueden enviar y recibir datos del servicio web a través de las entradas y salidas. Para algunas aplicaciones, datos del consumidor pueden estar disponible desde una fuente de datos o ya residen en un origen de datos externo, como el almacenamiento de blobs de Windows Azure. En estos casos, no necesitan leer y escribir datos con los resultados y entradas de servicio web. En su lugar, puede usar el servicio de ejecución de lote (BES) para leer los datos de origen de datos mediante un módulo de importar datos y escribir los resultados de la puntuación en una ubicación de datos diferente mediante un módulo de exportación de datos.

La importación de datos y módulos de exportación de datos, puede leer y escribir en un número de datos que se proporcionan ubicaciones como una dirección URL de Web a través de HTTP, una consulta subárbol, una base de datos de SQL Azure, almacenamiento de tablas de Azure, almacenamiento de blobs de Windows Azure, una fuente de datos o una base de datos SQL local.

Este tema se usa el "ejemplo 5: tren, prueba, Evaluate para clasificación binario: conjunto de datos para adultos" de ejemplo y se asume el conjunto de datos ya se ha cargado en una tabla de SQL Azure denominada censusdata.

## <a name="create-the-training-experiment"></a>Crear el ensayo de formación 
 
Cuando se abre el "ejemplo 5: tren, prueba, Evaluate para clasificación binario: conjunto de datos para adultos" muestra utiliza el conjunto de datos de ejemplo para adultos censo ingresos binario clasificación. Y el ensayo en el lienzo tendrá un aspecto similar a la siguiente imagen.

![Configuración inicial del ensayo.](./media/machine-learning-web-services-that-use-import-export-modules/initial-look-of-experiment.png)
  

Para leer los datos de la tabla de SQL Azure:

1.  Eliminar el módulo de conjunto de datos.
2.  En el cuadro de búsqueda de componentes, escriba importar.
3.  En la lista de resultados, agregar un módulo de *Importar datos* en el lienzo de ensayo.
4.  Conecte salida del módulo de *Importar datos de* la entrada del módulo *Limpiar datos que faltan* .
5.  En el panel de propiedades, seleccione la **Base de datos de SQL Azure** en la lista desplegable **Origen de datos** .
6.  En el **nombre del servidor de base de datos**, **nombre de la base de datos**, **nombre de usuario**y campos de **contraseña** , escriba la información adecuada para la base de datos.
7.  En el campo de consulta de base de datos, escriba la siguiente consulta.

        select [age],
           [workclass],
           [fnlwgt],
           [education],
           [education-num],
           [marital-status],
           [occupation],
           [relationship],
           [race],
           [sex],
           [capital-gain],
           [capital-loss],
           [hours-per-week],
           [native-country],
           [income]
        from dbo.censusdata;

8.  En la parte inferior de la ensayo lienzo, haga clic en **Ejecutar**.

## <a name="create-the-predictive-experiment"></a>Crear el ensayo predictiva

A continuación, configurar el el ensayo predictiva se va a implementar el servicio web.

1.  En la parte inferior del lienzo de ensayo, haga clic en **Configurar servicio Web** y seleccione **El servicio de Web predictiva (recomendado)**.
2.  Quitar la *Entrada del servicio Web* y *módulos de salida del servicio Web* desde el ensayo predictiva. 
3.  En el cuadro de búsqueda de componentes, escriba exportar.
4.  En la lista de resultados, agregar un módulo de *Exportar datos* al lienzo de ensayo.
5.  Conecte salida del *Modelo de puntuación* módulo la entrada del módulo de *Exportación de datos* . 
6.  En el panel de propiedades, seleccione la **Base de datos de SQL Azure** en la lista desplegable de destino de datos.
7.  En el **nombre de servidor de base de datos**, el **nombre de la base de datos**, **nombre de cuenta de usuario de servidor**y campos de **contraseña de cuenta de usuario de servidor** , escriba la información adecuada para la base de datos.
8.  En el campo **lista de columnas que se guardarán separados por comas** , escriba etiquetas de puntuación.
9.  En el **campo nombre de la tabla de datos**, escriba dbo. ScoredLabels. Si la tabla no existe, se crea cuando se ejecuta el ensayo o se llama al servicio web.
10. En el campo **lista de columnas de tabla de datos separados por comas** , escriba ScoredLabels.

Cuando escribe una aplicación que llama al servicio web final, desea especificar una consulta de entrada diferente o tabla de destino en tiempo de ejecución. Para configurar estas entradas y los resultados, puede usar la característica de parámetros de servicio Web para establecer la propiedad de *origen de datos* del módulo de *Importar datos* y la propiedad de destino de datos de modo de *Exportar los datos* .  Para obtener más información sobre parámetros de servicio Web, vea la [entrada de parámetros de servicio Web AzureML](https://blogs.technet.microsoft.com/machinelearning/2014/11/25/azureml-web-service-parameters/) en la inteligencia de Cortana y el Blog de aprendizaje del equipo.

Para configurar los parámetros del servicio Web para la consulta de importación y la tabla de destino:

1.  En el panel de propiedades para el módulo de *Importar datos* , haga clic en el icono en la parte superior derecha del campo de **consulta de base de datos** y seleccione **establecer como parámetro del servicio web**.
2.  En el panel de propiedades para el módulo de *Exportación de datos* , haga clic en el icono en la parte superior derecha del campo de **nombre de tabla de datos** y seleccione **establecer como parámetro del servicio web**.
3.  En la parte inferior del panel de propiedades de módulo de *Exportación de datos* , en la sección de **Parámetros de servicio Web** , haga clic en consulta de base de datos y cambie su consulta.
4.  Haga clic en el **nombre de la tabla de datos** y cambie su **tabla**.

Cuando haya terminado, el ensayo debe ser similar a la siguiente imagen.
 
![Aspecto final de ensayo.](./media/machine-learning-web-services-that-use-import-export-modules/experiment-with-import-data-added.png)

Ahora puede implementar el ensayo como un servicio web.

## <a name="deploy-the-web-service"></a>Implementar el servicio web 
Puede implementar a estándar o de nuevo servicio web.

### <a name="deploy-a-classic-web-service"></a>Implementar un servicio Web clásico

Implementar como un servicio Web clásico y crear una aplicación para utilizarla:

1.  En la parte inferior del lienzo de ensayo, haga clic en ejecutar.
2.  Cuando haya finalizado la ejecución, haga clic en **Servicio Web de implementar** y seleccione **Implementar servicios Web [clásica]**.
3.  En el panel de servicios web, busque la clave API. Copiar y guardarla para usarla más tarde.
4.  En la tabla de **Extremo predeterminado** , haga clic en el vínculo de la **Ejecución del lote** para abrir la página de Ayuda de API.
5.  En Visual Studio, cree una aplicación de consola de C#.
6.  En la página de Ayuda de API, busque la sección de **Código de ejemplo** en la parte inferior de la página.
7.  Copie y pegue el código de ejemplo de C# en el archivo Program.cs y quite todas las referencias con el almacenamiento de blobs.
8.  Actualizar el valor de la variable *apiKey* con la clave de API guardada anteriormente.
9.  Busque la declaración de la solicitud y actualizar los valores de los parámetros de servicio Web que se pasan a los módulos de *Datos importar* y *Exportar datos* . En este caso, se utiliza la consulta original, pero define un nuevo nombre de tabla.

        var request = new BatchExecutionRequest() 
        {   
            GlobalParameters = new Dictionary<string, string>() {
            { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
            { "Table", "dbo.ScoredTable2" },
            }
        };

10. Ejecute la aplicación. 

Al finalizar la ejecución, se agrega una nueva tabla a la base de datos que contiene los resultados de la puntuación.

### <a name="deploy-a-new-web-service"></a>Implementar un nuevo servicio Web

Implementar como un nuevo servicio Web y crear una aplicación para utilizarla:

1.  En la parte inferior de la ensayo lienzo, haga clic en **Ejecutar**.
2.  Cuando haya finalizado la ejecución, haga clic en **Servicio Web de implementar** y seleccione **Implementar [New] servicio Web**.
3.  En la página implementar ensayo, escriba un nombre para el servicio web y haga clic en **implementar**un plan de precio.
4.  En la página **Tutorial rápido** , haga clic en **Consume**.
5.  En la sección de **Código de ejemplo** , haga clic en **lote**.
6.  En Visual Studio, cree una aplicación de consola de C#.
7.  Copie y pegue el código de ejemplo de C# en el archivo Program.cs.
8.  Actualizar el valor de la variable *apiKey* con la **Clave principal** situado en la sección **información de consumo básico** .
9.  Busque la declaración de *scoreRequest* y actualizar los valores de los parámetros de servicio Web que se pasan a los módulos de *Datos importar* y *Exportar datos* . En este caso, se utiliza la consulta original, pero define un nuevo nombre de tabla.

        var scoreRequest = new
        {
            Inputs = new Dictionary<string, StringTable>()
            {
            },
            GlobalParameters = new Dictionary<string, string>() {
                 { "Query", @"select [age], [workclass], [fnlwgt], [education], [education-num], [marital-status], [occupation], [relationship], [race], [sex], [capital-gain], [capital-loss], [hours-per-week], [native-country], [income] from dbo.censusdata" },
                { "Table", "dbo.ScoredTable3" },
            }
        };

10. Ejecute la aplicación. 
 

