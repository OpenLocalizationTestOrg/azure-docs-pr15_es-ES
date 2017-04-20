<properties
    pageTitle="Clonar de aplicación Web con PowerShell"
    description="Obtenga información sobre cómo clonar sus aplicaciones Web a nuevas aplicaciones de Web con PowerShell."
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
    ms.date="01/13/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-app-cloning-using-powershell"></a>Aplicación de servicio de aplicaciones de Azure clonar con PowerShell#

Con el lanzamiento de Microsoft Azure PowerShell versión 1.1.0 se ha agregado una nueva opción a AzureRMWebApp nuevo que desea dar al usuario la capacidad para clonar una aplicación Web existente a una aplicación recién creada en una región diferente o en la misma región. Esto le permitirá a los clientes implementar un número de aplicaciones a través de diferentes regiones rápida y fácilmente.

Aplicación clonar está actualmente solo es compatible con planes de servicio de aplicación de nivel premium. La nueva característica usa las mismas limitaciones como característica de copia de seguridad de aplicaciones Web, vea [realizar copias de seguridad de una aplicación web de servicio de aplicaciones de Azure](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

Para obtener información sobre el uso de administrador de recursos de Azure según Azure PowerShell cmdlets para administrar las aplicaciones Web comprobar el [Administrador de recursos de Azure en función de comandos de PowerShell para la aplicación Web de Azure](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="cloning-an-existing-app"></a>Clonar una aplicación existente ##

Escenario: Una aplicación web existente en la región sur Central de EE, el usuario desea clonar el contenido a una nueva aplicación web en la región Norte Central de EE. Esto se puede conseguir usando la versión de administrador de recursos de Azure del cmdlet de PowerShell para crear una nueva aplicación web con la opción - SourceWebApp.

Conocer el nombre del grupo de recursos que contiene la aplicación web de origen, podemos utilizar el siguiente comando PowerShell para obtener información de la aplicación web origen (en este caso se denomina aplicación Web de origen):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Para crear un nuevo Plan de servicio de aplicación, podemos utilizar AzureRmAppServicePlan nuevo comando como en el ejemplo siguiente

    New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium

Mediante el comando nuevo AzureRmWebApp, podemos crear la nueva aplicación web en la región Norte Central de EE y vincular a un nivel premium existente Plan de servicio de aplicación, además podemos utilizar el mismo grupo de recursos como la aplicación web de origen, o definir un nuevo grupo de recursos, a continuación muestra:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp

Clonar una aplicación web existente incluidos implementación todos sus ranuras, el usuario tendrá que usar el parámetro IncludeSourceWebAppSlots, el siguiente comando PowerShell muestra el uso de dicho parámetro con el comando nuevo AzureRmWebApp:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots $true

Para clonar una aplicación web existente en la misma región, el usuario necesita crear un nuevo grupo de recursos y un servicio de aplicación nuevo plan en la misma región y, a continuación, usar el siguiente comando PowerShell clonar la aplicación web

    $destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonar un entorno de aplicación de servicio de una aplicación existente ##

Escenario: Una aplicación web existente en la región sur Central de EE, el usuario desea clonar el contenido a una nueva aplicación web para un entorno de servicio de aplicación existente (ASE).

Conocer el nombre del grupo de recursos que contiene la aplicación web de origen, podemos utilizar el siguiente comando PowerShell para obtener información de la aplicación web origen (en este caso se denomina aplicación Web de origen):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Conocer la nom de y el nombre del grupo de recursos que pertenece el ASE, el usuario puede usar el comando nuevo AzureRmWebApp para crear la nueva aplicación web en la ASE existente, a continuación muestra:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp

El parámetro de ubicación es necesario debido a la razón heredado, pero en el caso de crear una aplicación en un ASE se omitirá. 

## <a name="cloning-an-existing-app-slot"></a>Clonar una franja de aplicación existente ##

Escenario: El usuario desea clonar una franja de aplicación Web existente a, ya sea una nueva aplicación Web o un nuevo espacio de Web App. En la misma región como la franja de Web App original o en una región diferente, puede ser la nueva aplicación Web.

Conocer el nombre del grupo de recursos que contiene la aplicación web de origen, podemos utilizar el siguiente comando PowerShell para obtener información de la zona de la aplicación de web origen (en este caso denominado origen webappslot) vinculado al origen de Web App-Web App:

    $srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot

La siguiente muestra cómo crear un duplicado de la aplicación web de origen a una nueva aplicación web:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot

## <a name="configuring-traffic-manager-while-cloning-a-app"></a>Configuración del Administrador de tráfico al clonar una aplicación ##

Crear aplicaciones web de región múltiples y configurar el Administrador de tráfico de Azure para enrutar el tráfico a todas estas aplicaciones web, es un escenario importante n para asegurarse de que las aplicaciones de los clientes están muy disponibles, al clonar una aplicación web existente tiene la opción conectar ambas aplicaciones web a un nuevo perfil de administrador de tráfico o una existente, tenga en cuenta que el Administrador de recursos Azure solo se admite la versión del Administrador de tráfico.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-a-app"></a>Crear un nuevo perfil de administrador de tráfico mientras clonar una aplicación ###

Escenario: El usuario desea clonar otra región de una aplicación web al configurar un perfil de administrador de administrador de recursos de Azure tráfico que incluyen las aplicaciones web. La siguiente muestra cómo crear un duplicado de la aplicación web de origen a una nueva aplicación web al configurar un nuevo perfil de administrador de tráfico:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Agregar un nuevo clona Web App a un perfil de administrador de tráfico existente ###

Escenario: El usuario ya tiene un perfil de administrador de administrador de recursos de Azure tráfico que le gustaría agregar ambas aplicaciones web como extremos. Para ello, primero debemos ensamblar el identificador del perfil de administrador de tráfico existente, tenemos el identificador de suscripción, el nombre del grupo de recursos y el nombre del perfil de administrador de tráfico existente.

    $TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"

Después de tener el identificador del Administrador de tráfico, el siguiente muestra cómo crear un duplicado de la aplicación web de origen a una nueva aplicación web mientras se agrega a un perfil de administrador de tráfico existente:

    $destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID

## <a name="current-restrictions"></a>Restricciones actuales ##

Esta característica está actualmente en la vista previa, estamos trabajando para agregar nuevas capacidades a lo largo del tiempo, la siguiente lista son las limitaciones conocidas de la versión actual de la aplicación clonar:

- No se clona configuración de escala automático
- No se clona configuración de programación de copia de seguridad
- No se clona configuración VNET
- Perspectivas de aplicación no se establecen automáticamente hacia arriba en la aplicación web de destino
- No se clona fácil configuración de autenticación
- No se clonar kudu extensión
- No se clonarán reglas de sugerencia
- No se clonar el contenido de la base de datos


### <a name="references"></a>Referencias ###
- [Administrador de recursos de Azure en función de comandos de PowerShell aplicación Web de Azure](app-service-web-app-azure-resource-manager-powershell.md)
- [Clonar de aplicación Web con el Portal de Azure](app-service-web-app-cloning-portal.md)
- [Realizar copias de seguridad de una aplicación web en la aplicación de servicio de Azure](web-sites-backup.md)
- [Compatibilidad de Azure del Administrador de recursos con vista previa del Administrador de tráfico de Azure](../../articles/traffic-manager/traffic-manager-powershell-arm.md)
- [Introducción al entorno de servicio de aplicaciones](app-service-app-service-environment-intro.md)
- [Usar PowerShell Azure con el Administrador de recursos Azure](../powershell-azure-resource-manager.md)
