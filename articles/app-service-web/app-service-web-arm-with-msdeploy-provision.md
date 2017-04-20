<properties
    pageTitle="Implementar una aplicación web mediante MSDeploy con certificado ssl y de nombre de host"
    description="Usar una plantilla de administrador de recursos de Azure para implementar una aplicación web usa MSDeploy y configurar el nombre de host personalizado y un certificado SSL"
    services="app-service\web"
    manager="erikre"
    documentationCenter=""
    authors="jodehavi"
    />

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="john.dehavilland"/>

# <a name="deploy-a-web-app-with-msdeploy-custom-hostname-and-ssl-certificate"></a>Implementar una aplicación web con MSDeploy, nombre de host personalizado y certificado SSL

Esta guía le guía a través de la creación de una implementación de llevar a cabo para una aplicación Web de Azure, aprovechar MSDeploy, así como para agregar un nombre de host personalizado y un certificado SSL a la plantilla ARM.

Para obtener más información sobre la creación de plantillas, vea [Crear plantillas de administrador de recursos de Azure](../resource-group-authoring-templates.md).

###<a name="create-sample-application"></a>Crear la aplicación de ejemplo

Va a implementar una aplicación web ASP.NET. El primer paso es crear una aplicación web simple (o se puede usar una existente: en este caso, puede omitir este paso).

Abra 2015 de Visual Studio y elija Archivo > Nuevo proyecto. En el cuadro de diálogo que aparece, elija Web > aplicación Web de ASP.NET. En plantillas elija Web y elija la plantilla MVC. Seleccione _cambiar el tipo de autenticación_ _No_autenticación. Esto es solo para hacer que la aplicación de ejemplo tan sencillo como sea posible.

En este momento, tendrá una aplicación web básica ASP.Net lista para usarlo como parte del proceso de implementación.

###<a name="create-msdeploy-package"></a>Crear paquete MSDeploy

Siguiente paso es crear el paquete para implementar la aplicación web en Azure. Para ello, guarde el proyecto y, a continuación, ejecute lo siguiente desde la línea de comandos:

    msbuild yourwebapp.csproj /t:Package /p:PackageLocation="path\to\package.zip"

Esta opción creará un paquete zip en la carpeta PackageLocation. La aplicación ya está lista para su implementación, que ahora puede crear una plantilla de administrador de recursos de Azure hacerlo.

###<a name="create-arm-template"></a>Crear la plantilla ARM
En primer lugar, vamos a empezar con una plantilla ARM básica que creará una aplicación web y un plan de hospedaje (tenga en cuenta que no se muestran las variables y parámetros por razones de brevedad).

    {
        "name": "[parameters('appServicePlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "apiVersion": "2014-06-01",
        "dependsOn": [ ],
        "tags": {
            "displayName": "appServicePlan"
        },
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "sku": "[parameters('appServicePlanSKU')]",
            "workerSize": "[parameters('appServicePlanWorkerSize')]",
            "numberOfWorkers": 1
        }
    },
    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        }
    }

A continuación, debe modificar el recurso de la aplicación web para poner un recurso MSDeploy anidado. Esto le permitirá referencia el paquete creada anteriormente y Administrador de recursos de Azure usar MSDeploy para implementar el paquete de la aplicación Web de Azure. A continuación muestra el recurso Microsoft.Web/sites con recurso MSDeploy anidado:

    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        },
        "resources": [
            {
                "name": "MSDeploy",
                "type": "extensions",
                "location": "[resourceGroup().location]",
                "apiVersion": "2015-08-01",
                "dependsOn": [
                    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
                ],
                "tags": {
                    "displayName": "webDeploy"
                },
                "properties": {
                    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                        "IIS Web Application Name": "[variables('webAppName')]"
                    }
                }
            }
        ]
    }

Ahora verá que el recurso de MSDeploy tarda una propiedad **packageUri** que se define como sigue:

    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]"

Este **packageUri** toma el uri de cuenta de almacenamiento que la cuenta de almacenamiento de destino donde va a cargar el código postal de paquete para. El Administrador de recursos de Azure se aproveche [Compartido firmas de Access](../storage/storage-dotnet-shared-access-signature-part-1.md) para extraer el paquete hacia abajo en local de la cuenta de almacenamiento cuando se implementa la plantilla. Este proceso se pueden automatizar a través de un PowerShell script que va a cargar el paquete y llamar a la API de administración de Azure para crear las claves obligatorio y pasar en la plantilla como parámetros (*_artifactsLocation* y *_artifactsLocationSasToken*). Debe definir parámetros para la carpeta y se ha cargado en nombre de archivo del paquete en el contenedor de almacenamiento.

A continuación, debe agregar en otro recurso anidada para configurar los enlaces de nombre de host para aprovechar un dominio personalizado. En primer lugar debe asegurarse de que posee el nombre de host y configurarlo para comprobar Azure que usted es el propietario - vea [configurar un nombre de dominio personalizado en la aplicación de servicio de Azure](web-sites-custom-domain-name.md). Una vez que haya terminado puede agregar lo siguiente a la plantilla en la sección de recursos de Microsoft.Web/sites:

    {
        "apiVersion": "2015-08-01",
        "type": "hostNameBindings",
        "name": "www.yourcustomdomain.com",
        "dependsOn": [
            "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
        ],
        "properties": {
            "domainId": null,
            "hostNameType": "Verified",
            "siteName": "variables('webAppName')"
        }
    }

Por último, debe agregar otro nivel superior recurso, Microsoft.Web/certificates. Este recurso contiene el certificado SSL y se existe en el mismo nivel que su aplicación web y el plan de hospedaje.

    {
        "name": "[parameters('certificateName')]",
        "apiVersion": "2014-04-01",
        "type": "Microsoft.Web/certificates",
        "location": "[resourceGroup().location]",
        "properties": {
            "pfxBlob": "pfx base64 blob",
            "password": "some pass"
        }
    }

Debe tener un certificado SSL válido para configurar este recurso. Una vez que tenga ese certificado válido debe extraer los bytes pfx como una cadena de base 64. Una opción para extraer esto es usar el siguiente comando PowerShell:

    $fileContentBytes = get-content 'C:\path\to\cert.pfx' -Encoding Byte

    [System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-bytes.txt'

A continuación, podría pasar esto como un parámetro a la plantilla de implementación de ARM.

En este momento la plantilla ARM está lista.

###<a name="deploy-template"></a>Implementar plantilla

Los últimos pasos son juntar esto todo en una implementación end-to-end. Para facilitar la implementación que puede aprovechar la secuencia de comandos de PowerShell de **Implementar AzureResourceGroup.ps1** se agrega al crear un proyecto del grupo de recursos de Azure en Visual Studio para ayudar a cargar los artefactos necesarios en la plantilla. Requiere que haya creado una cuenta de almacenamiento que desea usar con anterioridad. En este ejemplo, he creado una cuenta de almacenamiento compartido para el package.zip cargarse. La secuencia de comandos Aproveche AzCopy para cargar el paquete en la cuenta de almacenamiento. Pasar de la ubicación de la carpeta de muestra y la secuencia de comandos cargará automáticamente todos los archivos dentro del directorio en el contenedor de almacenamiento con nombre. Después de llamar a implementar AzureResourceGroup.ps1 debe actualizar los enlaces de SSL para asignar el nombre de host personalizado con el certificado SSL.

El siguiente PowerShell muestra la implementación completa llamar a la implementación-AzureResourceGroup.ps1:

    #Set resource group name
    $rgName = "Name-of-resource-group"

    #call deploy-azureresourcegroup script to deploy web app

    .\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation "East US" `
                                    -ResourceGroupName $rgName `
                                    -UploadArtifacts `
                                    -StorageAccountName "name-of-storage-acct-for-package" `
                                    -StorageAccountResourceGroupName "resource-group-name-storage-acct" `
                                    -TemplateFile "web-app-deploy.json" `
                                    -TemplateParametersFile "web-app-deploy-parameters.json" `
                                    -ArtifactStagingDirectory "C:\path\to\packagefolder\"

    #update web app to bind ssl certificate to hostname. This has to be done after creation above.

    $cert = Get-PfxCertificate -FilePath C:\path\to\certificate.pfx

    $ar = Get-AzureRmResource -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -ApiVersion 2014-11-01

    $props = $ar.Properties

    $props.HostNameSslStates[2].'SslState' = 1
    $props.HostNameSslStates[2].'thumbprint' = $cert.Thumbprint
    $props.hostNameSslStates[2].'toUpdate' = $true

    Set-AzureRmResource -ApiVersion 2014-11-01 -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -PropertyObject $props

En este momento la aplicación debería se han implementado y debería poder examinar a través de https://www.yourcustomdomain.com
