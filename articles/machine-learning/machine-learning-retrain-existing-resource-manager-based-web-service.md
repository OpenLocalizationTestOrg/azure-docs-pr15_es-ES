<properties
    pageTitle="Un servicio web predictiva existente de reciclaje | Microsoft Azure"
    description="Aprenda a un modelo de reciclaje y actualizar el servicio web para usar el modelo recién capacitado en Azure el aprendizaje."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondl"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="v-donglo"/>


# <a name="retrain-an-existing-predictive-web-service"></a>Un servicio web predictiva existente de reciclaje

Este documento describe el proceso de reconversión para el siguiente escenario:

* Tiene una prueba de aprendizaje y un ensayo predictivo que ha implementado en un servicio web operationalized.
* Tiene nuevos datos que desea que el servicio web predictiva a usar para realizar su puntuación.

Comenzando con el servicio web existente y experimentación, debe seguir estos pasos:

1. Actualizar el modelo.
    1. Modificar el ensayo de formación para permitir salidas y entradas de servicio web.
    2. Implementar ensayo de aprendizaje como un servicio web reconversión.
    3. Usar el servicio de ejecución de lote (BES de ensayo de formación) para el modelo de reciclaje.
2. Use los cmdlets de PowerShell de aprendizaje del equipo de Azure para actualizar el ensayo predictiva.
    1.  Inicie sesión en su cuenta de administrador de recursos de Azure.
    2.  Obtener la definición del servicio web.
    3.  Exportar la definición del servicio web como JSON.
    4.  Actualizar la referencia al objeto ilearner binario en el JSON.
    5.  Importar el JSON en una definición de servicio web.
    6.  Actualizar el servicio web con una nueva definición del servicio web.

## <a name="deploy-the-training-experiment"></a>Implementar ensayo de formación

Para implementar el ensayo formación como un servicio web reconversión, debe agregar entradas de servicio web y los resultados en el modelo. Mediante una conexión de un módulo de *Salida del servicio Web* para el ensayo * [Tren modelo] [ train-model] * módulo, habilita el ensayo de formación generar un nuevo modelo cualificado que puede usar en el ensayo predictiva. Si tiene un módulo de *Evaluar modelo* , también puede adjuntar el resultado del servicio web para obtener los resultados de la evaluación como resultado.

Para actualizar el ensayo de aprendizaje:

1. Conecte un módulo de *Entrada de servicio Web* para la entrada de datos (por ejemplo, un módulo de *Limpiar datos que faltan* ). Normalmente, desea asegurarse de que los datos de entrada se procesan en la misma manera que los datos originales de aprendizaje.
2. Conecte un módulo de *Salida del servicio Web* a los resultados del módulo *Modelo tren* .
3. Si tiene un módulo de *Evaluar modelo* y desea que los resultados de la evaluación, conecte un módulo de *Salida del servicio Web* en el resultado de *Evaluar modelo* módulo.

Ejecutar el ensayo.

A continuación, debe implementar el ensayo formación como un servicio web que genera un modelo de formación y los resultados de la evaluación de modelo.  

En la parte inferior del lienzo de ensayo, haga clic en **Configurar servicio Web**y, a continuación, seleccione **Implementar [New] servicio Web**. El portal de servicios Web de Azure máquina aprendizaje se abrirá la página de **Servicio Web de implementar** . Escriba un nombre para el servicio web, elija un plan de pago y, a continuación, haga clic en **implementar**. Solo puede usar el método de ejecución por lotes para crear modelos capacitados.


## <a name="retrain-the-model-with-new-data-by-using-bes"></a>El modelo de datos nuevos de reciclaje utilizando BES

En este ejemplo, usaremos C# para crear la aplicación reconversión. También puede usar el código de ejemplo de Python o R para llevar a cabo esta tarea.

Llamar a las API de reconversión:

1. Crear una aplicación de consola de C# en Visual Studio (**nuevo** > **proyecto** > **Windows Desktop** > **Aplicación de consola**).
2.  Inicie sesión en el portal de servicios Web de aprendizaje de equipo.
3.  Haga clic en el servicio web que está trabajando.
2.  Haga clic en **consumir**.
3.  En la parte inferior de la página **Consume** , en la sección de **Código de ejemplo** , haga clic en **lote**.
5.  Copie el código C# de ejemplo para la ejecución del lote y pegarlo en el archivo Program.cs. Asegúrese de que el espacio de nombres permanece intacta.

Agregue el paquete de NuGet Microsoft.AspNet.WebApi.Client, como se especifica en los comentarios. Para agregar la referencia a Microsoft.WindowsAzure.Storage.dll, primero debe instalar la [biblioteca de cliente para servicios de almacenamiento de Azure](https://www.nuget.org/packages/WindowsAzure.Storage).

La siguiente captura de pantalla muestra la página de **Consume** en el portal de servicios Web de Azure máquina aprendizaje.

![Consumir página][1]

### <a name="update-the-apikey-declaration"></a>Actualizar la declaración de apikey

Busque la declaración de **apikey** :

    const string apiKey = "abc123"; // Replace this with the API key for the web service

En la sección **información de consumo básico** de la página **Consume** , busque la clave principal y copiar a la declaración de **apikey** .

### <a name="update-the-azure-storage-information"></a>Actualizar la información de almacenamiento de Azure

El código de ejemplo BES carga un archivo de una unidad local (por ejemplo, "C:\temp\CensusIpnput.csv") para el almacenamiento de Azure, procesa y vuelve a escribir los resultados en el almacenamiento de Azure.  

Para actualizar la información de almacenamiento de Azure, debe recuperar el nombre de la cuenta de almacenamiento, la clave y la información de contenedor para su cuenta de almacenamiento desde el portal de clásico Azure y, a continuación, actualizar la correspondi después de ejecutar el ensayo, el flujo de trabajo resultante debe ser similar al siguiente:

![Flujo de trabajo resultante después de ejecutar][4]valores NG en el código.

1. Inicie sesión en el portal de clásico de Azure.
1. En la columna de navegación izquierdo, haga clic en **almacenamiento**.
1. En la lista de cuentas de almacenamiento, seleccione uno para almacenar el modelo retrained.
1. En la parte inferior de la página, haga clic en **Administrar las teclas de acceso**.
1. Copie y guarde la **Clave principal de Access** y cierre el cuadro de diálogo.
1. En la parte superior de la página, haga clic en **contenedores**.
1. Seleccione un contenedor existente, o cree uno nuevo y guarde el nombre.

Busque la *StorageAccountName*, *StorageAccountKey*y *StorageContainerName* declaraciones y actualizar los valores que guardó en el portal de clásico.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

También debe asegurarse de que el archivo de entrada está disponible en la ubicación que especifique en el código.

### <a name="specify-the-output-location"></a>Especifique la ubicación de salida

Al especificar la ubicación de salida en la solicitud, se debe especificar la extensión del archivo que se especifica en *RelativeLocation* como `ilearner`. Consulte el ejemplo siguiente:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

El siguiente es un ejemplo de salida reconversión: ![formación de salida][6]

## <a name="evaluate-the-retraining-results"></a>Evaluar los resultados de reconversión

Cuando se ejecuta la aplicación, el resultado incluye la dirección URL y el token de acceso compartido de firmas que son necesarios para tener acceso a los resultados de la evaluación.

Puede ver los resultados de rendimiento del modelo retrained combinando los *BaseLocation*, *RelativeLocation*y *SasBlobToken* desde los resultados de *output2* (como se muestra en la imagen de salida reconversión anterior) y pegar la dirección URL completa en la barra de direcciones del explorador.  

Examine los resultados para determinar si el modelo recién capacitado funciona bien para reemplazar el existente.

Copie la *BaseLocation*, *RelativeLocation*y *SasBlobToken* de los resultados.

## <a name="retrain-the-web-service"></a>El servicio web de reciclaje

Cuando reciclaje un nuevo servicio web, actualice la definición del servicio web predictiva para hacer referencia al nuevo modelo capacitado. La definición del servicio web es una representación interna del modelo cualificado del servicio web y no es modificable directamente. Asegúrese de que está recuperando la definición del servicio web para su prueba predictiva y no el ensayo de aprendizaje.

## <a name="sign-in-to-azure-resource-manager"></a>Iniciar sesión para el Administrador de recursos Azure

Primero debe iniciar una sesión su cuenta de Azure desde el entorno de PowerShell mediante el cmdlet [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) .

## <a name="get-the-web-service-definition-object"></a>Obtener el objeto de la definición del servicio Web

A continuación, obtenga el objeto de la definición del servicio Web llamando el cmdlet [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) .

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Para determinar el nombre del grupo de recursos de un servicio web existente, ejecute el cmdlet Get-AzureRmMlWebService sin parámetros para mostrar los servicios web de su suscripción. Busque el servicio web y, a continuación, mire su identificador de servicio web. El nombre del grupo de recursos es el cuarto elemento ID, justo después del elemento *resourceGroups* . En el ejemplo siguiente, el nombre del grupo de recursos es SouthCentralUS de MachineLearning de forma predeterminada.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

O bien, para determinar el nombre del grupo de recursos de un servicio web existente, inicie sesión en el portal de servicios Web de Azure máquina aprendizaje. Seleccione el servicio web. El nombre del grupo de recursos es el quinto elemento de la dirección URL del servicio web, justo después del elemento *resourceGroups* . En el ejemplo siguiente, el nombre del grupo de recursos es SouthCentralUS de MachineLearning de forma predeterminada.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>Exportar el objeto de la definición del servicio Web como JSON

Para modificar la definición del modelo cualificado para usar el modelo recién capacitado, primero debe utilizar el cmdlet [AzureRmMlWebService de exportación](https://msdn.microsoft.com/library/azure/mt767935.aspx) para exportar a un archivo de formato JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>Actualizar la referencia al objeto ilearner binario

En los activos, busque [modelo capacitado], actualice el valor de *uri* en el nodo *locationInfo* con el URI del blob ilearner. La URI se genera combinando los *BaseLocation* y la *RelativeLocation* de los resultados de BES formación llamada.

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition-object"></a>Importar el JSON en un objeto de la definición del servicio Web

Debe usar el cmdlet [Importar AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) para convertir el archivo modificado de JSON en un objeto de la definición del servicio Web que puede usar para actualizar el ensayo predicative.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>Actualizar el servicio web

Por último, puede usar el cmdlet de [AzureRmMlWebService actualizar](https://msdn.microsoft.com/library/azure/mt767922.aspx) para actualizar el ensayo predictiva.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -

[1]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
