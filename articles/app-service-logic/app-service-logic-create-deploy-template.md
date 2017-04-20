<properties
   pageTitle="Crear una plantilla de implementación de la aplicación de lógica | Microsoft Azure"
   description="Obtenga información sobre cómo crear una plantilla de implementación de la aplicación de lógica y usarlo para la administración de versiones"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="create-a-logic-app-deployment-template"></a>Crear una plantilla de implementación de la aplicación de lógica

Después de crear una aplicación de lógica, desea crear como una plantilla de administrador de recursos de Azure. De este modo, puede implementar fácilmente la aplicación lógica a cualquier entorno o grupo de recursos, donde es posible que tenga. Para obtener una introducción a las plantillas de administrador de recursos, asegúrese de consultar los artículos en [Administrador de recursos de Azure habilitados](../resource-group-authoring-templates.md) e [implementar recursos con las plantillas de administrador de recursos de Azure](../resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Plantilla de implementación de aplicación de lógica

Una aplicación lógica tiene tres componentes básicos:

* **Recursos de la aplicación de lógica**. Este recurso contiene información acerca de cosas como precios de plan, la ubicación y la definición del flujo de trabajo.
* **Definición de flujo de trabajo**. Esto es lo que se ve en la vista código. Incluye la definición de los pasos del flujo y la forma en que debe ejecutar el motor. Esta es la `definition` propiedad del recurso de aplicación lógica.
* **Conexiones**. Estos son los recursos independientes que almacenan metadatos acerca de todas las conexiones de conector, como una cadena de conexión y un token de acceso de forma segura. Hace referencia a estos en una aplicación de la lógica de la `parameters` sección del recurso de aplicación lógica.

Puede ver estos componentes para aplicaciones de lógica existente mediante una herramienta como el [Explorador de recursos de Azure](http://resources.azure.com).

Para crear una plantilla para una aplicación de lógica usar con las implementaciones de grupo de recursos, debe definir los recursos y agregar parámetros según sea necesario. Por ejemplo, si implementa un desarrollo, la prueba y el entorno de producción, es probable que desee usar cadenas de conexión diferentes a una base de datos SQL en cada entorno. O bien, es posible que desee implementar en varias suscripciones o grupos de recursos.  

## <a name="create-a-logic-app-deployment-template"></a>Crear una plantilla de implementación de la aplicación de lógica

La manera más sencilla de tener una plantilla de implementación de la aplicación de lógica válida es usar la [Visual Studio Tools para aplicaciones de lógica](./app-service-logic-deploy-from-vs.md).  Las herramientas de Visual Studio generan una plantilla de implementación válido que puede utilizarse en cualquier suscripción o ubicación.

Otras herramientas pueden ayudarle a crear una plantilla de implementación de la aplicación de lógica. Puede crear manualmente, es decir, mediante el uso de los recursos que ya ha comentado aquí para crear los parámetros según sea necesario. Otra opción es utilizar un módulo de PowerShell [creador de plantilla de aplicación de lógica](https://github.com/jeffhollan/LogicAppTemplateCreator) . Este módulo de código abierto evalúa primero la aplicación de la lógica y las conexiones que está utilizando y, a continuación, genera recursos de plantilla con los parámetros necesarios para su implementación. Por ejemplo, si tiene una aplicación de lógica que recibe un mensaje de una cola de Bus de servicio de Azure y agrega datos a una base de datos de SQL Azure, la herramienta conservar toda la lógica de orquestación y agregar parámetros a las cadenas de conexión de SQL y Bus de servicio para que se pueden establecer en la implementación.

>[AZURE.NOTE] Conexiones deben estar en el mismo grupo de recursos que la aplicación de la lógica.

### <a name="install-the-logic-app-template-powershell-module"></a>Instalar el módulo de PowerShell de plantilla de aplicación de lógica

Para instalar el módulo, lo más sencillo es a través de la [Galería de PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), mediante el comando `Install-Module -Name LogicAppTemplate`.  

También puede instalar el módulo de PowerShell manualmente:

1. Descargar la versión más reciente del [creador de plantilla de aplicación de lógica](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
1. Extraer la carpeta en la carpeta del módulo de PowerShell (normalmente `%UserProfile%\Documents\WindowsPowerShell\Modules`).

Para el módulo trabajar con cualquier acceso inquilino y suscripción token, recomendamos usarlo con la herramienta de línea de comandos [ARMClient](https://github.com/projectkudu/ARMClient) .  Esta [entrada de blog](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) , ARMClient se explica con más detalle.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Generar una plantilla de aplicación de la lógica con PowerShell

Después de instala PowerShell, puede generar una plantilla con el siguiente comando:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId`es el identificador de suscripción de Azure. Esta línea primero obtiene un acceso token a través de ARMClient, a continuación, canalizaciones mediante la secuencia de comandos de PowerShell y, a continuación, crea la plantilla de un archivo JSON.

## <a name="add-parameters-to-a-logic-app-template"></a>Agregar parámetros a una plantilla de aplicación de lógica

Después de crear la plantilla de aplicación lógica, puede seguir agregar o modificar parámetros que es posible que necesite. Por ejemplo, si la definición incluye un identificador de recursos para una función de Azure o anidada flujo de trabajo que tiene previsto implementar en una sola implementación, puede agregar más recursos a la plantilla y agregar parámetros identificadores según sea necesario. El mismo aplica a todas las referencias a las API o Swagger personalizado extremos que espera implementar con cada grupo de recursos.

## <a name="deploy-a-logic-app-template"></a>Implementar una plantilla de aplicación de lógica

Puede implementar la plantilla mediante cualquier número de herramientas, incluyendo PowerShell, API de REST, administración de lanzamiento de Visual Studio y la implementación de plantilla de Portal de Azure. Consulte este artículo sobre cómo [implementar recursos mediante el Administrador de recursos de Azure plantillas](../resource-group-template-deploy.md) para obtener información adicional. Además, se recomienda que cree un [archivo de parámetros](../resource-group-template-deploy.md#parameter-file) para almacenar valores para el parámetro.

### <a name="authorize-oauth-connections"></a>Autorizar conexiones de OAuth

Después de la implementación, la aplicación de lógica funciona llevar a cabo con parámetros válidos. Sin embargo, las conexiones de OAuth aún tendrán que estar autorizado para generar un token de acceso válido. Para ello, puede abrir la aplicación de la lógica en el diseñador y, a continuación, autorizar conexiones. O bien, si desea automatizar, puede usar una secuencia de comandos para consentimiento a cada conexión de OAuth. Hay una secuencia de comandos de ejemplo en GitHub en el proyecto [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) .

## <a name="visual-studio-release-management"></a>Administración de la versión de Visual Studio

Un escenario común para implementar y administrar un entorno es usar una herramienta como la administración de lanzamiento de Visual Studio, con una plantilla de implementación de la aplicación de lógica. Visual Studio Team Services incluye una tarea de [Grupo de recursos de Azure implementar](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) que puede agregar a cualquier compilación o liberar canalización. Debe tener un [servicio principal](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) para autorización implementar y, a continuación, puede generar la definición de lanzamiento.

1. En administración de versiones, para crear una nueva definición de, seleccione **vacía** para empezar con una definición vacía.

    ![Crear una definición de nueva y vacía][1]   

1. Elija los recursos que necesita para esto. Es probable que se trata de la plantilla de aplicación lógica generada manualmente o como parte del proceso de generación.
1. Agregar una tarea de **Implementación de grupo de recursos de Azure** .
1. Configurar con un [servicio principal](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)y hacer referencia a los archivos de plantilla y parámetros de plantilla.
1. Continuar generar los pasos del proceso de lanzamiento de cualquier entorno, prueba automatizada o los aprobadores según sea necesario.

<!-- Image References -->
[1]: ./media/app-service-logic-create-deploy-template/emptyReleaseDefinition.PNG
