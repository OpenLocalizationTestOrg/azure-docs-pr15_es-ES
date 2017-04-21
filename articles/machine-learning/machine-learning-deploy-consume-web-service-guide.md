<properties
    pageTitle="Servicios Web de Azure máquina aprendizaje: Implementación y consumo | Microsoft Azure"
    description="Recursos para implementar y consumir servicios web."
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
    ms.date="10/12/2016"
    ms.author="v-donglo"/>

# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Servicios Web de Azure máquina aprendizaje: Implementación y consumo

Puede usar el aprendizaje del equipo de Azure para implementar modelos como servicios web y los flujos de trabajo de aprendizaje del equipo. Estos servicios web, se usa para llamar a los modelos de aprendizaje de máquina desde aplicaciones a través de Internet para hacer predicciones en tiempo real o en modo por lotes. Dado que los servicios web son RESTful, puede llamar desde varios idiomas y plataformas, como .NET y Java, programación y aplicaciones, como Excel.

Las siguientes secciones proporcionan vínculos a tutoriales, código y documentación para ayudar a empezar.

## <a name="deploy-a-web-service"></a>Implementar un servicio web

### <a name="with-azure-machine-learning-studio"></a>Con el aprendizaje Azure Studio

Studio de aprendizaje de equipo y el portal de servicios Web de Microsoft Azure máquina aprendizaje ayudarle a implementar y administrar un servicio web sin escribir código.

Los siguientes vínculos proporcionan información general sobre cómo implementar un servicio web nuevo:

* Para obtener información general sobre cómo implementar un nuevo servicio web que se basa en el Administrador de recursos de Azure, vea [implementar un nuevo servicio web](machine-learning-webservice-deploy-a-web-service.md).
* Para obtener un tutorial acerca de cómo implementar un servicio web, vea [implementar un servicio web de aprendizaje del equipo de Azure](machine-learning-publish-a-machine-learning-web-service.md).
* Para obtener un tutorial acerca de cómo crear e implementar un servicio web completo, vea [tutorial paso 1: crear un área de trabajo de aprendizaje de máquina](machine-learning-walkthrough-1-create-ml-workspace.md).
* Para obtener ejemplos específicos que implementación un servicio web, consulte:

    * [Tutorial paso 5: Implementar el servicio web de aprendizaje del equipo de Azure](machine-learning-walkthrough-5-publish-web-service.md)
    * [Cómo implementar un servicio web en varias regiones](machine-learning-how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Con el proveedor de recursos de servicios de web API (API de administrador de recursos de Azure)

El proveedor de recursos de aprendizaje del equipo de Azure para los servicios web permite implementación y administración de servicios web mediante llamadas a la API de REST. Para obtener más información, vea la referencia de [Servicio de Web de aprendizaje de máquina (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) en MSDN.

### <a name="with-powershell-cmdlets"></a>Con los cmdlets de PowerShell

Azure proveedor de recursos de aprendizaje para los servicios web permite implementación y administración de servicios web mediante el uso de cmdlets de PowerShell.

Para usar los cmdlets, primero debe iniciar una sesión su cuenta de Azure desde el entorno de PowerShell mediante el cmdlet [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) . Si no está familiarizado con el procedimiento para llamar a los comandos de PowerShell que se basan en el Administrador de recursos, vea [con Azure con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md#login-to-your-azure-account).

Para exportar el ensayo predictiva, use [este código de ejemplo](https://github.com/ritwik20/AzureML-WebServices). Después de crear el archivo .exe desde el código, puede escribir:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Ejecutar la aplicación, crea una plantilla JSON de servicio web. Para usar la plantilla para implementar un servicio web, debe agregar la siguiente información:

* Clave y el nombre de la cuenta de almacenamiento

    Puede obtener el nombre de la cuenta de almacenamiento y la clave desde el [portal de Azure](https://portal.azure.com/) o en el [portal de clásico de Azure](http://manage.windowsazure.com/).
* Id. de plan de compromiso

    Puede obtener el identificador de plan desde el portal de [Servicios Web de Azure máquina aprendizaje](https://services.azureml.net) iniciar sesión y haciendo clic en un nombre de plan.

Agréguelos a la plantilla JSON como secundarios del nodo de *Propiedades* en el mismo nivel que el nodo *MachineLearningWorkspace* .

Aquí tenemos un ejemplo:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Consulte los siguientes artículos y el código de ejemplo para obtener más información:

* Referencia de [Cmdlets de aprendizaje del equipo de Azure]( https://msdn.microsoft.com/library/azure/mt767952.aspx) en MSDN
* Ejemplo de [tutorial](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) en GitHub

## <a name="consume-the-web-services"></a>Consumir los servicios web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Desde los servicios Web de aprendizaje de Azure máquina (pruebas) de la interfaz de usuario

Puede probar el servicio web desde el portal de servicios Web de Azure máquina aprendizaje. Esto incluye probar el servicio de respuesta de la solicitud (RR) y las interfaces de servicio de ejecución por lotes (BES).

* [Implementar un nuevo servicio web](machine-learning-webservice-deploy-a-web-service.md)
* [Implementar un servicio web de aprendizaje del equipo de Azure](machine-learning-publish-a-machine-learning-web-service.md)
* [Tutorial paso 5: Implementar el servicio web de aprendizaje del equipo de Azure](machine-learning-walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>Desde Excel

Puede descargar una plantilla de Excel que utiliza el servicio web:

* [Consumo de un servicio web de Azure el aprendizaje de Excel](machine-learning-consuming-from-excel.md)
* [Complemento de Excel para los servicios Web de Azure máquina aprendizaje](machine-learning-excel-add-in-for-web-services.md)


### <a name="from-a-rest-based-client"></a>Desde un cliente de REST

Servicios Web de Azure máquina aprendizaje son las API de REST. Puede usar estas API de distintas plataformas, como .NET, Python, R, Java, etcetera. La página **Consume** para el servicio web en el [portal de servicios Web de Microsoft Azure máquina aprendizaje](https://services.azureml.net) tiene código de ejemplo que puede ayudarle a empezar. Para obtener más información, vea [cómo utilizar un servicio web de Azure aprendizaje del equipo que se ha implementado desde un equipo de ensayo de aprendizaje](machine-learning-consume-web-services.md).

