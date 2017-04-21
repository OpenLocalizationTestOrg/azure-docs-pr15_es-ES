<properties
    pageTitle="Un nuevo servicio web mediante los cmdlets de PowerShell de administración de aprendizaje de máquina de reciclaje | Microsoft Azure"
    description="Aprenda a un modelo de reciclaje y actualizar el servicio web para usar el modelo recién capacitado en aprendizaje de máquina Azure con los cmdlets de PowerShell de administración de aprendizaje de equipo mediante programación."
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
    ms.date="09/27/2016"
    ms.author="v-donglo"/>

# <a name="retrain-a-new-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>Un nuevo servicio web mediante los cmdlets de PowerShell de administración de aprendizaje de máquina de reciclaje

Cuando reciclaje un nuevo servicio web, actualice la definición del servicio web predictiva para hacer referencia al nuevo modelo capacitado.  

## <a name="prerequisites"></a>Requisitos previos

Debe ha configurado un ensayo de aprendizaje y un ensayo predictivo tal como se muestra en [aprendizaje de reciclaje modelos mediante programación](machine-learning-retrain-models-programmatically.md). 

>[AZURE.IMPORTANT] El ensayo predictiva debe distribuirse como un equipo de administrador de recursos de Azure (nueva) en función de aprendizaje servicio web. 
 
Para obtener información adicional sobre implementación de servicios web, vea [implementar un servicio web de aprendizaje del equipo de Azure](machine-learning-publish-a-machine-learning-web-service.md).

Este proceso requiere que ha instalado los Cmdlets de aprendizaje del equipo de Azure. Para obtener información de instalación de los cmdlets de aprendizaje, vea la referencia de [Cmdlets de aprendizaje del equipo de Azure](https://msdn.microsoft.com/library/azure/mt767952.aspx) en MSDN.

Copiar la siguiente información de los resultados de la reconversión:

* BaseLocation
* RelativeLocation

Se muestran los pasos que necesarios:

1.  Inicie sesión en su cuenta de administrador de recursos de Azure.
2.  Obtener la definición del servicio web
3.  Exportar la definición del servicio Web como JSON
4.  Actualizar la referencia al objeto ilearner binario en el JSON.
5.  Importar el JSON en una definición de servicio Web
6.  Actualizar el servicio web con la nueva definición del servicio Web

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Inicie sesión en su cuenta de administrador de recursos de Azure

En primer lugar debe iniciar sesión en su cuenta de Azure desde el entorno de PowerShell mediante el cmdlet [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) .

## <a name="get-the-web-service-definition"></a>Obtener la definición del servicio Web

A continuación, obtener el servicio Web llamando el cmdlet [Get-AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx) . La definición del servicio Web es una representación interna del modelo cualificado del servicio web y no es modificable directamente. Asegúrese de que está recuperando la definición del servicio Web para su prueba predictiva y no el ensayo de aprendizaje.

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

Para determinar el nombre del grupo de recursos de un servicio web existente, ejecute el cmdlet Get-AzureRmMlWebService sin parámetros para mostrar los servicios web de su suscripción. Busque el servicio web y, a continuación, mire su identificador de servicio web. El nombre del grupo de recursos es el cuarto elemento ID, justo después del elemento *resourceGroups* . En el ejemplo siguiente, el nombre del grupo de recursos es SouthCentralUS de MachineLearning de forma predeterminada.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

O bien, para determinar el nombre del grupo de recursos de un servicio web existente, inicie sesión en el portal de servicios Web de Microsoft Azure máquina aprendizaje. Seleccione el servicio web. El nombre del grupo de recursos es el quinto elemento de la dirección URL del servicio web, justo después del elemento *resourceGroups* . En el ejemplo siguiente, el nombre del grupo de recursos es SouthCentralUS de MachineLearning de forma predeterminada.

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>Exportar la definición del servicio Web como JSON

Para modificar la definición en el modelo de formación usar recién capacitado modelo, primero debe utilizar el cmdlet [AzureRmMlWebService de exportación](https://msdn.microsoft.com/library/azure/mt767935.aspx) para exportar a un archivo con formato JSON.

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>Actualizar la referencia al objeto ilearner binario en el JSON.

En los activos, busque [modelo capacitado], actualice el valor de *uri* en el nodo *locationInfo* con el URI del blob ilearner. La URI se genera combinando los *BaseLocation* y la *RelativeLocation* de los resultados de BES formación llamada.

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
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

## <a name="import-the-json-into-a-web-service-definition"></a>Importar el JSON en una definición de servicio Web

Debe usar el cmdlet [Importar AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx) para convertir el archivo modificado de JSON en una definición de servicio Web que puede usar para actualizar el ensayo Predicative.

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>Actualizar el servicio web con la nueva definición del servicio Web

Por último, use cmdlet [AzureRmMlWebService de actualización](https://msdn.microsoft.com/library/azure/mt767922.aspx) para actualizar el ensayo predictiva.

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>Resumen

Usar los cmdlets de administración de PowerShell de aprendizaje de equipo, puede actualizar el modelo de formación de un servicio Web predictiva permite escenarios como:

* Modelo periódico formación con los nuevos datos.
* Distribución de un modelo de los clientes con el objetivo de hacerles reciclaje el modelo con sus propios datos.
