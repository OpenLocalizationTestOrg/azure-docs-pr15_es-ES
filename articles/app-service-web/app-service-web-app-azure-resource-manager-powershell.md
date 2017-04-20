<properties
    pageTitle="Comandos de PowerShell basada en el Administrador de recursos de Azure aplicación Web de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo usar los comandos de PowerShell en función de administrador de recursos de Azure nuevos para administrar las aplicaciones Web de Azure."
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

# <a name="using-azure-resource-manager-based-powershell-to-manage-azure-web-apps"></a>Usar PowerShell basada en el Administrador de recursos de Azure para administrar aplicaciones Web de Azure#

> [AZURE.SELECTOR]
- [CLI de Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
- [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)

Con Microsoft Azure PowerShell versión 1.0.0 se han agregado nuevos comandos, que le proporcionan al usuario la capacidad de usar los comandos de PowerShell en función de administrador de recursos de Azure para administrar aplicaciones Web.

Para obtener información sobre la administración de grupos de recursos, vea [con Azure con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md). 

Para obtener información sobre la lista completa de parámetros y opciones para los cmdlets de PowerShell, vea la [Referencia completa de los cmdlets basada en el Administrador de recursos de Azure de aplicación Web de Cmdlets de PowerShell](https://msdn.microsoft.com/library/mt619237.aspx)

## <a name="managing-app-service-plans"></a>Aplicación de servicio de administración de los planes ##

### <a name="create-an-app-service-plan"></a>Crear un Plan de servicio de aplicación ###
Para crear un plan de servicio de aplicación, use el cmdlet **AzureRmAppServicePlan de nuevo** .

A continuación se muestran las descripciones de los distintos parámetros:

-   **Nombre**: nombre del plan de servicio de aplicación.
-   **Ubicación**: ubicación del plan de servicio.
-   **ResourceGroupName**: grupo de recursos que incluye el plan de servicios de aplicación recién creado.
-   **Nivel**: el nivel de precio que desee (predeterminado es gratuita, otras opciones son compartidos, Basic, estándar y Premium).
-   **WorkerSize**: el tamaño de los trabajadores (el valor predeterminado es pequeña si se especificó el parámetro de nivel como básico, estándar o Premium. Otras opciones son mediana y grande.)
-   **NumberofWorkers**: el número de los trabajadores de la aplicación de servicio plan (el valor predeterminado es 1). 

Ejemplo para usar este cmdlet:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### <a name="create-an-app-service-plan-in-an-app-service-environment"></a>Crear un Plan de servicio de aplicaciones en un entorno de servicio de aplicaciones ###
Para crear un plan de servicio de la aplicación en un entorno de servicio de aplicación, use el mismo comando **AzureRmAppServicePlan de nuevo** con parámetros adicionales para especificar la nom de y del recurso grupo Nom.

Ejemplo para usar este cmdlet:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -AseName constosoASE -AseResourceGroupName contosoASERG -Tier Premium -WorkerSize Large -NumberofWorkers 10

Para obtener más información sobre el entorno de servicios de aplicación, consulte [Introducción a la aplicación de servicio de entorno](app-service-app-service-environment-intro.md)

### <a name="list-existing-app-service-plans"></a>Lista de planes de servicio de aplicación existente ###

Para obtener una lista de los planes de servicio de aplicación existente, use el cmdlet **Get-AzureRmAppServicePlan** .

Para mostrar todos los planes de servicio de aplicación en la suscripción, utilice: 

    Get-AzureRmAppServicePlan

Para mostrar todos los planes de servicio de aplicación en un grupo de recursos específico, use:

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

Para obtener un plan de servicios de aplicación concreta, use:

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### <a name="configure-an-existing-app-service-plan"></a>Configurar un Plan de servicio de aplicación existente ###

Para cambiar la configuración de un plan de servicio de aplicación existente, use el cmdlet **Set-AzureRmAppServicePlan** . Puede cambiar el nivel, tamaño de trabajo y el número de trabajadores 

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### <a name="scaling-an-app-service-plan"></a>Ajuste de escala de un Plan de servicio de aplicación ####

Para cambiar la escala de un Plan de servicio de aplicación existente, use:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### <a name="changing-the-worker-size-of-an-app-service-plan"></a>Cambiar el tamaño de trabajo de un Plan de servicio de aplicación ####

Para cambiar el tamaño de los trabajadores de un Plan de servicio de aplicación existente, use:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### <a name="changing-the-tier-of-an-app-service-plan"></a>Cambiar el nivel de un Plan de servicio de aplicaciones ####

Para cambiar el nivel de un Plan de servicio de aplicación existente, use:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### <a name="delete-an-existing-app-service-plan"></a>Eliminar un Plan de servicio de aplicación existente ###

Para eliminar un plan de servicio de aplicación existente, todas las aplicaciones web asignadas necesitan mover o eliminar en primer lugar. A continuación, usando el cmdlet **Quitar AzureRmAppServicePlan** puede eliminar el plan de servicios de aplicación.

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## <a name="managing-app-service-web-apps"></a>Administrar aplicaciones de servicio de aplicación Web ##

### <a name="create-a-web-app"></a>Crear una aplicación Web ###

Para crear una aplicación web, use el cmdlet **AzureRmWebApp de nuevo** .

A continuación se muestran las descripciones de los distintos parámetros:

- **Nombre**: nombre para la aplicación web.
- **AppServicePlan**: nombre para el plan de servicio se usa para hospedar la aplicación web.
- **ResourceGroupName**: grupo de recursos que hospeda el plan de servicios de aplicación.
- **Ubicación**: la ubicación de la aplicación web.

Ejemplo para usar este cmdlet:

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### <a name="create-a-web-app-in-an-app-service-environment"></a>Crear una aplicación Web en un entorno de servicio de aplicaciones ###

Para crear una aplicación web en un entorno de aplicación de servicio (ASE). Use el mismo comando **AzureRmWebApp de nuevo** con parámetros adicionales para especificar la Nom y el nombre del grupo de recursos que pertenece el ASE.

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

Para obtener más información sobre el entorno de servicios de aplicación, consulte [Introducción a la aplicación de servicio de entorno](app-service-app-service-environment-intro.md)

### <a name="delete-an-existing-web-app"></a>Eliminar una aplicación Web existente ###

Para eliminar una aplicación web existente puede usar el cmdlet **Quitar AzureRmWebApp** , debe especificar el nombre de la aplicación web y el nombre del grupo de recursos.

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### <a name="list-existing-web-apps"></a>Lista de aplicaciones Web existente ###

Para obtener una lista de las aplicaciones web existente, use el cmdlet **Get-AzureRmWebApp** .

Para obtener una lista de todas las aplicaciones web en su suscripción, use:

    Get-AzureRmWebApp

Para obtener una lista de todas las aplicaciones web en un grupo de recursos específico, use:

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

Para obtener una aplicación web específica, use:

    Get-AzureRmWebApp -Name ContosoWebApp

### <a name="configure-an-existing-web-app"></a>Configurar una aplicación Web existente ###

Para cambiar las opciones y configuraciones para una aplicación web existente, use el cmdlet **Set-AzureRmWebApp** . Para obtener una lista completa de parámetros, compruebe el [vínculo de referencia de Cmdlet](https://msdn.microsoft.com/library/mt652487.aspx)

Ejemplo (1): Utilice este cmdlet para cambiar las cadenas de conexión

    $connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

Ejemplo (2): agregar o cambiar la configuración de la aplicación

    $appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


Ejemplo (3): configurar la aplicación web para ejecutar en modo de 64 bits

    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### <a name="change-the-state-of-an-existing-web-app"></a>Cambiar el estado de una aplicación Web existente ###

#### <a name="restart-a-web-app"></a>Reiniciar una aplicación web ####

Para reiniciar una aplicación web, debe especificar el grupo de recursos y el nombre de la aplicación web.

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Detener una aplicación web ####

Para detener una aplicación web, debe especificar el grupo de recursos y el nombre de la aplicación web.

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Iniciar una aplicación web ####

Para iniciar una aplicación web, debe especificar el grupo de recursos y el nombre de la aplicación web.

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Administrar perfiles de publicación de Web App ###

Cada aplicación web tiene un perfil de publicación que se puede usar para publicar sus aplicaciones, se pueden ejecutar varias operaciones en los perfiles de publicación.

#### <a name="get-publishing-profile"></a>Obtener el perfil de publicación ####

Para obtener el perfil de publicación para una aplicación web, use:

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

Este comando repite el perfil de publicación a la línea de comandos como salida también el perfil de publicación a un archivo de texto.

#### <a name="reset-publishing-profile"></a>Restablecer el perfil de publicación ####

Para ambas restablecer la contraseña de publicación de web y FTP implementar una aplicación web, use:

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-certificates"></a>Administrar los certificados de aplicación Web ###

Para obtener información acerca de cómo administrar los certificados de aplicación web, consulte el [enlace de certificados SSL con PowerShell](app-service-web-app-powershell-ssl-binding.md)


### <a name="next-steps"></a>Pasos siguientes ###
- Para obtener información sobre la compatibilidad con PowerShell del Administrador de recursos de Azure, consulte [usando PowerShell de Azure con el Administrador de recursos de Azure.](../powershell-azure-resource-manager.md)
- Para obtener información sobre los entornos de aplicación de servicio, consulte [Introducción al entorno de aplicación de servicio.](app-service-app-service-environment-intro.md)
- Para obtener información sobre la administración de certificados SSL de servicio de aplicación con PowerShell, vea [enlace de certificados SSL con PowerShell.](app-service-web-app-powershell-ssl-binding.md)
- Para obtener información sobre la lista completa de los cmdlets de PowerShell en función de administrador de recursos de Azure para aplicaciones Web de Azure, consulte [Azure referencia de los cmdlets de PowerShell Cmdlets de administrador de recursos de Web Apps Azure.](https://msdn.microsoft.com/library/mt619237.aspx)
- - Para obtener información acerca de cómo administrar el servicio de aplicación mediante CLI, consulte [Using Azure Resource Manager-Based XPlat CLI para Azure Web App.](app-service-web-app-azure-resource-manager-xplat-cli.md)
