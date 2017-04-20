<properties
    pageTitle="Aplicación Web de herramientas de la línea de comandos de varias plataformas de basada en el Administrador de recursos Azure para Azure | Microsoft Azure"
    description="Obtenga información sobre cómo usar las nuevas herramientas de línea de comandos de varias plataformas basadas en el Administrador de recursos de Azure y administrar las aplicaciones Web de Azure."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="aelnably"/>

# <a name="using-azure-resource-manager-based-xplat-cli-for-azure-web-app"></a>Uso de CLI XPlat basado en el Administrador de recursos de Azure aplicación Web de Azure#

> [AZURE.SELECTOR]
- [CLI de Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
- [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)

Con el lanzamiento de la versión de herramientas de línea de comandos de Microsoft Azure entre plataformas 0.10.5, se han agregado nuevos comandos. Estos comandos dar al usuario la capacidad de usar los comandos de PowerShell en función de administrador de recursos de Azure para administrar aplicaciones Web.

Para obtener información sobre la administración de grupos de recursos, vea [utilizar CLI Azure para administrar Azure y grupos de recursos](../xplat-cli-azure-resource-manager.md). 


## <a name="managing-app-service-plans"></a>Aplicación de servicio de administración de los planes ##

### <a name="create-an-app-service-plan"></a>Crear un Plan de servicio de aplicación ###
Para crear un plan de servicio de aplicación, utilice el comando **crear appserviceplan azure** .

A continuación se muestran las descripciones de los distintos parámetros:

-   **: grupo de recursos**: grupo de recursos que incluye el plan de servicios de aplicación recién creado.
-   **--nombre**: nombre del plan de servicio de aplicación.
-   **--ubicación**: ubicación del plan de servicio de aplicación.
-   **--nivel**: el sku de precio que quiera (las opciones son: F1 (gratis). D1 (compartido). B1 (pequeña básico), B2 (medio básica) y B3 (Basic grande). S1 (pequeña estándar), S2 (medio estándar) y S3 (estándar grande). P1 (pequeña Premium), P2 (medio Premium) y P3 (Premium grande).)
-   **--instancias**: el número de los trabajadores de la aplicación de servicio plan (el valor predeterminado es 1).

Ejemplo para usar este cmdlet:

    azure appserviceplan create --name ContosoAppServicePlan --location "South Central US" --resource-group ContosoAzureResourceGroup --sku P1 --instances 10

### <a name="list-existing-app-service-plans"></a>Lista de planes de servicio de aplicación existente ###

Para mostrar los planes de servicio de aplicación existente, utilice el comando **lista appserviceplan azure** .

Para mostrar todos los planes de servicio de aplicación en un grupo de recursos específico, use:

    azure appserviceplan list --resource-group ContosoAzureResourceGroup

Para obtener un plan de servicios de aplicación concreta, use el comando **Mostrar appserviceplan azure** :

    azure appserviceplan show --name ContosoAppServicePlan --resource-group southeastasia

### <a name="configure-an-existing-app-service-plan"></a>Configurar un Plan de servicio de aplicación existente ###

Para cambiar la configuración de un plan de servicio de aplicación existente, use el comando **appserviceplan azure config** . Puede cambiar el sku y el número de trabajadores 

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1 --instances 9

#### <a name="scaling-an-app-service-plan"></a>Ajuste de escala de un Plan de servicio de aplicación ####

Para cambiar la escala de un Plan de servicio de aplicación existente, use:

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --instances 9

#### <a name="changing-the-sku-of-an-app-service-plan"></a>Cambiar el SKU de un Plan de servicio de aplicaciones ####

Para cambiar el sku de un Plan de servicio de aplicación existente, use:

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1


### <a name="delete-an-existing-app-service-plan"></a>Eliminar un Plan de servicio de aplicación existente ###

Para eliminar un plan de servicio de aplicación existente, todas las aplicaciones web asignadas necesitan mover o eliminar en primer lugar. A continuación, usando el comando **Eliminar Web App azure** que puede eliminar el plan de servicios de aplicación.

    azure appserviceplan delete --name ContosoAppServicePlan --resource-group southeastasia

## <a name="managing-app-service-web-apps"></a>Administrar aplicaciones de servicio de aplicación Web ##

### <a name="create-a-web-app"></a>Crear una aplicación Web ###

Para crear una aplicación web, utilice el comando **crear Web App azure** .

A continuación se muestran las descripciones de los distintos parámetros:

- **--nombre**: nombre para la aplicación web.
- **--plan**: nombre para el plan de servicio se usa para hospedar la aplicación web.
- **: grupo de recursos**: grupo de recursos que hospeda el plan de servicios de aplicación.
- **--ubicación**: la ubicación de la aplicación web.

Ejemplo para usar este cmdlet:

    azure webapp create --name ContosoWebApp --resource-group ContosoAzureResourceGroup --plan ContosoAppServicePlan --location "South Central US"

### <a name="delete-an-existing-web-app"></a>Eliminar una aplicación Web existente ###

Para eliminar una aplicación web existente puede usar el comando **Eliminar Web App azure** , debe especificar el nombre de la aplicación web y el nombre del grupo de recursos.

    azure webapp delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="list-existing-web-apps"></a>Lista de aplicaciones Web existente ###

Para mostrar las aplicaciones web existente, utilice el comando de la **lista de la aplicación Web de azure** .

Para obtener una lista de todas las aplicaciones web en un grupo de recursos específico, use:

    azure webapp list --resource-group ContosoAzureResourceGroup

Para obtener una aplicación web específica, use el comando **Mostrar de la aplicación Web de azure** .

    azure webapp show --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="configure-an-existing-web-app"></a>Configurar una aplicación Web existente ###

Para cambiar la configuración y las configuraciones de una aplicación web existente, utilice el comando **establece la configuración de la aplicación Web de azure** .

Ejemplo (1): cambiar la versión de php de una aplicación web 

    azure webapp config set --name ContosoWebApp --resource-group ContosoAzureResourceGroup --phpversion 5.6

Ejemplo (2): agregar o cambiar la configuración de la aplicación

    webapp config appsettings set --name ContosoWebApp --resource-group ContosoAzureResourceGroup appsetting1=appsetting1value,appsetting2=appsetting2value

Para saber qué otra configuración puede ser cambiado, utilice el comando **configuración de la aplicación Web de azure set -h** .

### <a name="change-the-state-of-an-existing-web-app"></a>Cambiar el estado de una aplicación Web existente ###

#### <a name="restart-a-web-app"></a>Reiniciar una aplicación web ####

Para reiniciar una aplicación web, debe especificar el grupo de recursos y el nombre de la aplicación web.

    azure webapp restart --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Detener una aplicación web ####

Para detener una aplicación web, debe especificar el grupo de recursos y el nombre de la aplicación web.

    azure webapp stop --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Iniciar una aplicación web ####

Para iniciar una aplicación web, debe especificar el grupo de recursos y el nombre de la aplicación web.

    azure webapp start --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Administrar perfiles de publicación de Web App ###

Cada aplicación web tiene un perfil de publicación que se pueden usar para publicar las aplicaciones.

#### <a name="get-publishing-profile"></a>Obtener el perfil de publicación ####

Para obtener el perfil de publicación para una aplicación web, use:

    azure webapp publishingprofile --name ContosoWebApp --resource-group ContosoAzureResourceGroup

Este comando repite la publicación de perfil de usuario y contraseña para la línea de comandos.

### <a name="manage-web-app-hostnames"></a>Administrar nombres de host de Web App ###

Para administrar los enlaces de nombre de host de la aplicación web, utilice el comando de **nombres de host de configuración de aplicación Web de azure**  

#### <a name="list-hostname-bindings"></a>Lista hostname enlaces ####

Para obtener los enlaces hostname actual para una aplicación web, use:

    azure webapp config hostnames list --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="add-hostname-bindings"></a>Agregar enlaces de nombre de host ####

Para agregar enlaces de nombre de host para una aplicación web, use:

    azure webapp config hostnames add --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

#### <a name="delete-hostname-bindings"></a>Eliminar enlaces de nombre de host ####

Para eliminar los enlaces hostname, use:

    azure webapp config hostnames delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

### <a name="next-steps"></a>Pasos siguientes ###
- Para obtener información sobre compatibilidad de Azure Administrador de recursos CLI, consulte [utilizar CLI Azure para administrar grupos de recursos y recursos de Azure.](../xplat-cli-azure-resource-manager.md)
- Para obtener información acerca de cómo administrar el servicio de la aplicación con PowerShell, vea [Using Azure Resource Manager-Based PowerShell para administrar aplicaciones Web de Azure.](app-service-web-app-azure-resource-manager-powershell.md)
