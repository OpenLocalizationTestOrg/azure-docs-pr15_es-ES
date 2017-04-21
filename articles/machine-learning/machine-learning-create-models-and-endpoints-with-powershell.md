<properties
pageTitle="Crear varios modelos de una ensayo | Microsoft Azure"
description="Usar PowerShell para crear varios modelos de aprendizaje y web extremos de servicio con el mismo algoritmo pero formación diferentes conjuntos de datos."
services="machine-learning"
documentationCenter=""
authors="hning86"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="machine-learning"
ms.workload="data-services"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="10/03/2016"
ms.author="garye;haining"/>

# <a name="create-many-machine-learning-models-and-web-service-endpoints-from-one-experiment-using-powershell"></a>Crear muchos modelos de aprendizaje y web extremos de servicio de un ensayo con PowerShell

Aquí es un problema común de aprendizaje de máquina: que desee crear muchos modelos que tienen el mismo flujo de trabajo de formación y utilizan el mismo algoritmo, pero formación diferentes conjuntos de datos como entrada. Este artículo muestra cómo hacer esto a escala en Azure máquina aprendizaje Studio con solo un ensayo único.

Por ejemplo, supongamos que usted es propietario de un negocio de franquicia alquiler de bicicleta global. Desea crear un modelo de regresión para predecir la demanda de alquiler basándose en datos históricos. Tiene 1.000 ubicaciones de alquiler en todo el mundo y ha recopilado un conjunto de datos para cada ubicación que incluye características importantes como fecha, hora, meteorología y tráfico que son específicas de cada ubicación.

Puede entrenar el modelo de una vez con una versión combinada de todos los conjuntos de datos en todas las ubicaciones. Pero porque cada una de las ubicaciones tiene un entorno único, sería mejor entrenar el modelo de regresión por separado con el conjunto de datos para cada ubicación. De este modo, cada modelo capacitado podría tener en cuenta el almacén de diferentes tamaños, volumen, geografía, población, entorno de tráfico fáciles de bicicleta, *etc.*.

Puede que sea el mejor método, pero no desea crear 1.000 experimentación formación en Azure el aprendizaje con cada uno de los que representa una única ubicación. Además de ser una tarea muy dura, también es parece bastante eficaz, ya que cada ensayo deberá los mismos componentes excepto el conjunto de datos de aprendizaje.

Afortunadamente, podemos conseguir esto con la [API de formación de aprendizaje del equipo de Azure](machine-learning-retrain-models-programmatically.md) y automatizar la tarea con [PowerShell de aprendizaje del equipo de Azure](machine-learning-powershell-module.md).

> [AZURE.NOTE] Para hacer que nuestro ejemplo se ejecuta más rápidamente, se reducirá el número de ubicaciones de 1.000 a 10. Pero los mismos principios y procedimientos que se aplican a 1.000 ubicaciones. La única diferencia es que si desea entrenar de conjuntos de datos de 1.000 probablemente va a pensar de la ejecución de las siguientes secuencias de comandos de PowerShell en paralelo. Cómo hacer está fuera del ámbito de este artículo, pero puede encontrar algunos ejemplos de PowerShell multiproceso en Internet.  

## <a name="set-up-the-training-experiment"></a>Configurar el ensayo de formación

Vamos a usar un ejemplo [experimentar formación](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1) que ya hemos creado en la [Galería de inteligencia de Cortana](http://gallery.cortanaintelligence.com). Abra este ensayo en el área de trabajo de [Azure máquina aprendizaje Studio](https://studio.azureml.net) .

>[AZURE.NOTE] Para seguir este ejemplo, desea utilizar un área de trabajo estándar, en lugar de un área de trabajo gratuita. Se creará un extremo de cada cliente - para un total de puntos finales de 10 - y que requieren un área de trabajo estándar desde un área de trabajo libre se limita a los extremos de 3. Si solo tiene un área de trabajo libre, simplemente modificar las secuencias de comandos siguientes para permitir 3 ubicaciones.

El ensayo usa un módulo de **Importar datos** para importar el conjunto de datos de formación *customer001.csv* desde una cuenta de almacenamiento de Azure. Supongamos que hemos recopilado todas las ubicaciones de alquiler de bicicleta conjuntos de datos de aprendizaje y almacenados en la misma ubicación de almacenamiento de blobs con nombres de archivo comprendido entre *rentalloc001.csv* y *rentalloc10.csv*.

![imagen](./media/machine-learning-create-models-and-endpoints-with-powershell/reader-module.png)

Tenga en cuenta que se ha agregado un módulo de **Salida del servicio Web** en el módulo de **Modelo de tren** .
Cuando este ensayo se implementa como un servicio web, el extremo asociado que salida devolverá el modelo capacitado en el formato de un archivo de .ilearner.

Tenga en cuenta que hemos configurado un parámetro del servicio web para la dirección URL que utiliza el módulo de **Importar datos** . Esto nos permite usar el parámetro para especificar conjuntos de datos de entrenamiento individual para entrenar el modelo para cada ubicación.
Hay otras maneras que podríamos haber hecho esto, por ejemplo, mediante una consulta SQL con un parámetro del servicio web para obtener datos de una base de datos de SQL Azure o simplemente mediante un módulo de **Entrada del servicio Web** para pasar de un conjunto de datos al servicio web.

![imagen](./media/machine-learning-create-models-and-endpoints-with-powershell/web-service-output.png)

Ahora, vamos a ejecutar este ensayo formación utilizando el valor de predeterminado *rental001.csv* como el conjunto de datos de aprendizaje. Si se ve el resultado del módulo **Evaluate** (haga clic en el resultado y seleccione **visualizar**), puede ver obtenemos un rendimiento aceptable de *AUC* = 0.91. En este momento, estamos listos implementar un servicio web de ensayo de este curso.

## <a name="deploy-the-training-and-scoring-web-services"></a>Implementar la formación y la puntuación de servicios web

Para implementar el servicio web de aprendizaje, haga clic en el botón **Configurar servicio Web** debajo del lienzo de ensayo y seleccione **Implementar el servicio de Web**. Llamar a este servicio web "" formación de alquiler de bicicleta".

Ahora es necesario implementar el servicio web de resultados.
Para ello, podemos haga clic en **Configurar servicio Web** debajo del lienzo y seleccione **Predictiva servicio Web**. Esto crea un ensayo puntuación.
Debemos realizar algunos ajustes secundarias para que funcione como un servicio web, como la eliminación de la columna de la etiqueta "cnt" de los datos de entrada y limitar los resultados para el identificador de la instancia y el correspondiente pronosticados valor.

Para ahorrar que funcionan, simplemente puede abrir el [ensayo predictiva](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1) en la galería que ya se ha preparado.

Para implementar el servicio web, ejecute el ensayo predictiva, a continuación, haga clic en el botón de **Servicio Web de implementar** debajo del lienzo. Nombre del servicio web puntuación "Bicicletas alquiler puntuación" ".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Crear extremos de servicios web idénticos 10 con PowerShell

Este servicio web incluye un extremo predeterminado. Pero no estamos más interesados en el extremo predeterminado ya que no se pueden actualizar. Lo que necesitamos es crear 10 extremos adicionales, uno para cada ubicación. Haremos esto con PowerShell.

En primer lugar, nos configurar nuestro entorno de PowerShell:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

A continuación, ejecute el siguiente comando PowerShell:

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

Ahora que hemos creado 10 extremos y contienen el mismo modelo capacitado capacitado en *customer001.csv*. Puede verlos en el Portal de administración de Azure.

![imagen](./media/machine-learning-create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Actualizar los extremos para usar conjuntos de datos de formación independiente con PowerShell

El siguiente paso es actualizar los extremos con los modelos de datos individuales de cada cliente forma exclusiva capacitados. Pero primero necesitamos generar estos modelos del servicio web de **Aprendizaje de alquiler de bicicletas** . Volvamos al servicio web de **Aprendizaje de alquiler de bicicletas** . Es necesario llamar a su extremo BES 10 veces con 10 conjuntos de datos de formación diferentes para generar 10 modelos diferentes. Usaremos el cmdlet de PowerShell **InovkeAmlWebServiceBESEndpoint** para realizar esta acción.

También debe proporcionar las credenciales de su cuenta de almacenamiento de blobs en `$configContent`, es decir, los campos `AccountName`, `AccountKey` y `RelativeLocation`. La `AccountName` puede ser uno de los nombres de cuenta, como se muestra en el **Portal de administración de Azure clásica** (pestaña*almacenamiento* ). Una vez que haga clic en una cuenta de almacenamiento, su `AccountKey` pueden encontrarse pulsando el botón **Administrar las teclas de acceso** en la parte inferior y copia de la *Clave principal de Access*. La `RelativeLocation` es la ruta de acceso en relación con el almacenamiento donde se almacena un nuevo modelo. Por ejemplo, la ruta de acceso `hai/retrain/bike_rental/` en el script de puntos a un contenedor denominado `hai`, y `/retrain/bike_rental/` son subcarpetas. Actualmente, no puede crear subcarpetas a través del portal de interfaz de usuario, pero hay [Varios exploradores de almacenamiento de Azure](../storage/storage-explorers.md) que permiten hacerlo. Se recomienda que cree un nuevo contenedor en el almacenamiento para almacenar los nuevos modelos capacitados (archivos .ilearner) como sigue: en la página de almacenamiento, haga clic en el botón **Agregar** en la parte inferior y asígnele el nombre `retrain`. En resumen, los cambios necesarios para la siguiente secuencia de comandos pertenecen a `AccountName`, `AccountKey` y `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

    # Invoke the retraining API 10 times
    # This is the default (and the only) endpoint on the training web service
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

>[AZURE.NOTE] El extremo BES es el único modo admitido para esta operación. RR no se pueden usar para generar modelos capacitados.

Como puede ver, en lugar de crear 10 diferentes BES configuración json archivos de trabajo, creamos dinámicamente la cadena de configuración en su lugar y fuente al parámetro *jobConfigString* del cmdlet **InvokeAmlWebServceBESEndpoint** , ya que no es realmente es necesario mantener una copia en el disco.

Si todo va bien, después de un tiempo debería ver 10 archivos .ilearner, de *model001.ilearner* a *model010.ilearner*, en la cuenta de almacenamiento de Azure. Ahora podemos actualizar nuestros 10 extremos de servicios web puntuación con estos modelos mediante el cmdlet de PowerShell **AmlWebServiceEndpoint de revisión** . Nuevamente, recuerde que nos podemos revisión sólo los extremos no predeterminada que se creó mediante programación anteriormente.

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '<my_blob_sas_token>'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

Esto se debe ejecutar con rapidez. Cuando finalice la ejecución, se habrá creado correctamente 10 extremos de servicios web predictiva, que contiene un modelo capacitado capacitado forma exclusiva en el conjunto de datos específico en una ubicación de alquiler, desde un ensayo formación único. Para comprobarlo, puede intentar llamar a estos extremos mediante el cmdlet **InvokeAmlWebServiceRRSEndpoint** , asígneles los mismos datos de entrada y, a continuación, debería ver los resultados de predicción diferente desde los modelos de formación con conjuntos de formación diferentes.

## <a name="full-powershell-script"></a>Secuencia de comandos de PowerShell completa

Esta es la lista del código fuente completo:

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and properly set to point to the valid workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

    # Create 10 endpoints on the scoring web service
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpontName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

    # Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '?test'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }
