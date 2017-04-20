<properties
    pageTitle="Conectar la aplicación a su red virtual con PowerShell"
    description="Instrucciones sobre cómo conectar y trabajar con redes virtuales con PowerShell"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="wpickett"
    editor="cephalin"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="ccompy"/>

# <a name="connect-your-app-to-your-virtual-network-by-using-powershell"></a>Conectar la aplicación a su red virtual con PowerShell #

## <a name="overview"></a>Información general ##

En el servicio de aplicación de Azure, puede conectar su aplicación (web, móvil o API) a una red virtual Azure (VNet) en la suscripción. Esta característica se denomina VNet integración. La característica de integración de VNet no debe confundirse con la característica entorno de servicio de aplicación, que le permite ejecutar una instancia de servicio de la aplicación de Azure en la red virtual.

La característica de integración de VNet tiene una interfaz de usuario (UI) en el nuevo portal que puede usar para integrar con redes virtuales que se implementan mediante el modelo de implementación clásico o el modelo de implementación de administrador de recursos de Azure. Si desea obtener más información sobre la característica, consulte [integrar la aplicación con una red virtual Azure](web-sites-integrate-with-vnet.md).

En este artículo es no sobre cómo usar la interfaz de usuario, pero en su lugar acerca de cómo habilitar la integración con PowerShell. Como los comandos para cada modelo de implementación son diferentes, este artículo contiene una sección para cada modelo de implementación.  

Antes de continuar con este artículo, asegúrese de que tiene:

- El último SDK de PowerShell de Azure instalado. Esto se puede instalar con el instalador de plataforma Web.
- Una aplicación de servicio de aplicación de Azure ejecuta en Standard o Premium SKU.

## <a name="classic-virtual-networks"></a>Redes virtuales clásicas ##

Esta sección explica tres tareas para redes virtuales que utilizan el modelo de implementación clásica:

1. Conectar la aplicación a una red virtual preexistente que tiene una puerta de enlace y está configurada para el sitio de punto de conexión.
1. Actualizar la información de integración de una red virtual para la aplicación.
1. Desconectar la aplicación de la red virtual.

### <a name="connect-an-app-to-a-classic-vnet"></a>Conectar una aplicación a un VNet clásico ###

Para conectar una aplicación a una red virtual, siga estos tres pasos:

1. Declarar a la aplicación web que van a unir una red virtual determinada. La aplicación, generará un certificado que se asignará a la red virtual para el sitio de punto de conectividad.
1. Cargar el certificado de la aplicación web en la red virtual y, a continuación, recuperar el paquete de la VPN de sitio a punto URI.
1. Actualice la conexión de red virtual de la aplicación web con el paquete de sitio de punto de URI.

Los pasos de la primeros y la terceros son totalmente secuencias de comandos, pero el segundo paso requiere una acción única manual a través del portal o access para realizar acciones de **colocar** o **revisión** en el extremo del Administrador de recursos de Azure de red virtual. Póngase en contacto con soporte técnico de Azure que estén habilitadas. Antes de empezar, asegúrese de que tiene una red virtual clásica que tiene conectividad de sitio de punto ya habilitado y una puerta de enlace implementada. Para crear la puerta de enlace y habilitar la conectividad de punto a sitios, debe usar el portal como se describe en la [creación de una puerta de enlace VPN][createvpngateway].

Debe estar en la misma suscripción como su plan de servicios de aplicación que contiene la aplicación que se integra con la red virtual clásica.

##### <a name="set-up-azure-powershell-sdk"></a>Configurar el SDK de PowerShell de Azure #####

Abra una ventana de PowerShell y configurar su cuenta de Azure y de suscripción mediante:

    Login-AzureRmAccount

Este comando abrirá un símbolo del sistema para obtener las credenciales de Azure. Después de iniciar sesión, use uno de los siguientes comandos para seleccionar la suscripción que desea usar. Asegúrese de que está usando la suscripción que en su red virtual y un plan de servicios de aplicación.

    Select-AzureRmSubscription –SubscriptionName [WebAppSubscriptionName]

o

    Select-AzureRmSubscription –SubscriptionId [WebAppSubscriptionId]

##### <a name="variables-used-in-this-article"></a>Variables usadas en este artículo #####

Para simplificar los comandos, se establecerá una variable de PowerShell **$Configuration** con la configuración específica.

Establecer variable de PowerShell con los parámetros siguientes:

    $Configuration = @{}
    $Configuration.WebAppResourceGroup = "[Your web app resource group]"
    $Configuration.WebAppName = "[Your web app name]"
    $Configuration.VnetSubscriptionId = "[Your vnet subscription id]"
    $Configuration.VnetResourceGroup = "[Your vnet resource group]"
    $Configuration.VnetName = "[Your vnet name]"

La ubicación de la aplicación debería la ubicación sin espacios. Por ejemplo, US oeste es westus.

    $Configuration.WebAppLocation = "[Your web app Location]"

El siguiente elemento es donde se debe escribir el certificado. Debe ser una ruta de acceso de escritura en el equipo local. Asegúrese de incluir .cer al final.

    $Configuration.GeneratedCertificatePath = "[C:\Path\To\Certificate.cer]"

Para ver qué configuración, escriba **$Configuration**.

    > $Configuration

    Name                           Value
    ----                           -----
    GeneratedCertificatePath       C:\vnetcert.cer
    VnetSubscriptionId             efc239a4-88f9-2c5e-a9a1-3034c21ad496
    WebAppResourceGroup            vnetdemo-rg
    VnetResourceGroup              testase1-rg
    VnetName                       TestNetwork
    WebAppName                     vnetintdemoapp
    WebAppLocation                 centralus

El resto de esta sección se supone que tiene una variable creada según se ha descrito.

##### <a name="declare-the-virtual-network-to-the-app"></a>Declarar una red virtual a la aplicación #####

Use el comando siguiente para indicar a la aplicación que va a utilizar esta red virtual determinada. Esto hará que la aplicación generar certificados necesarios:

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -PropertyObject @{"VnetResourceId" = "/subscriptions/$($Configuration.VnetSubscriptionId)/resourceGroups/$($Configuration.VnetResourceGroup)/providers/Microsoft.ClassicNetwork/virtualNetworks/$($Configuration.VnetName)"} -Location $Configuration.WebAppLocation -ApiVersion 2015-07-01

Si este comando se realiza correctamente, **$vnet** debe tener una variable **Propiedades** en él. Huella digital de un certificado y los datos del certificado, debe contener la variable de **Propiedades** .

##### <a name="upload-the-web-app-certificate-to-the-virtual-network"></a>Cargar el certificado de la aplicación web en la red virtual #####

Un manual paso puntuales se requiere para cada combinación de red virtual y la suscripción. Es decir, si se están conectando aplicaciones de suscripción A una red Virtual, debe realizar este paso solo una vez independientemente de cuántos aplicaciones que configure. Si va a agregar una nueva aplicación a otra red virtual, deberá volver a hacerlo. El motivo es que se genera un conjunto de certificados en un nivel de suscripción de servicio de la aplicación de Azure, y el conjunto se genera una vez para cada red virtual que se conectarán las aplicaciones.

Los certificados ya habrá establecidos si sigue estos pasos o si integrado con la misma red virtual a través del portal.

El primer paso es generar el archivo .cer. El segundo paso es cargar el archivo .cer en su red virtual. Para generar el archivo .cer de la llamada a la API en el paso anterior, ejecute los comandos siguientes.

    $certBytes = [System.Convert]::FromBase64String($vnet.Properties.certBlob)
    [System.IO.File]::WriteAllBytes("$($Configuration.GeneratedCertificatePath)", $certBytes)

El certificado se encuentra en la ubicación que **$Configuration.GeneratedCertificatePath** especifica.

Para cargar el certificado de forma manual, usar el [portal de Azure] [ azureportal] y **Examinar una red Virtual (clásico)** > **conexiones VPN** > punto al sitio de**** > **Administrar certificados**. A continuación, cargar el certificado.

##### <a name="get-the-point-to-site-package"></a>Obtener el paquete de sitio de punto #####

Es el siguiente paso para configurar una conexión de red virtual en una aplicación web obtener el paquete de punto a sitios y proporcionar a su aplicación web.

Guardar la plantilla siguiente en un archivo llamado GetNetworkPackageUri.json en algún lugar en el equipo, por ejemplo, C:\Azure\Templates\GetNetworkPackageUri.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "certData": {
                "type": "string"
            },
            "certThumbprint": {
                "type": "string"
            },
            "networkName": {
                "type": "string"
            }
        },
        "variables": {
            "legacyVnetName": "[concat('Group ', resourceGroup().name, ' ', parameters('networkName'))]"
            },
            "resources": [
            ],
        "outputs" : {
            "PackageUri" :
            {
            "value" : "[listPackage(resourceId('Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates', parameters('networkName'), 'primary', parameters('certThumbprint')), '2014-06-01').packageUri]", "type" : "string"
            }
        }
    }


Establecer parámetros de entrada:

    $parameters = @{"certData" = $vnet.Properties.certBlob ;
    certThumbprint = $vnet.Properties.certThumbprint ;
    "networkName" = $Configuration.VnetName }

Llamar a la secuencia de comandos:

    $output = New-AzureRmResourceGroupDeployment -Name unused -ResourceGroupName $Configuration.VnetResourceGroup -TemplateParameterObject $parameters -TemplateFile C:\PATH\TO\GetNetworkPackageUri.json


La variable **$output. Outputs.packageUri** contendrá el paquete URI a la aplicación web.

##### <a name="upload-the-point-to-site-package-to-your-app"></a>Cargar el paquete de sitio de punto en la aplicación #####

El último paso es proporcionar la aplicación con este paquete. Simplemente ejecute el siguiente comando:

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)/primary" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-07-01 -PropertyObject @{"VnetName" = $Configuration.VnetName ; "VpnPackageUri" = $($output.Outputs.packageUri).Value } -Location $Configuration.WebAppLocation

Si un mensaje le pide que confirme la sobrescritura de un recurso existente, asegúrese de habilitarlo.

Cuando se complete correctamente este comando, la aplicación ahora debe estar conectada a la red virtual. Para confirmar la correcta, vaya a la consola de aplicación y escriba lo siguiente:

    SET WEBSITE_

Si hay una variable de entorno denominada WEBSITE_VNETNAME que tiene un valor que coincida con el nombre de la red virtual de destino, todas las configuraciones se realizó correctamente.

### <a name="update-classic-vnet-integration-information"></a>Actualizar la información de integración de VNet clásico ###

Para actualizar o resync su información, simplemente Repita los pasos que siguió cuando creó la integración en primer lugar. Los pasos son:

1. Definir la información de configuración.
1. Declarar una red virtual a la aplicación.
1. Obtener el paquete de punto al sitio.
1. Cargar el paquete de sitio de punto en la aplicación.

### <a name="disconnect-your-app-from-a-classic-vnet"></a>Desconectar la aplicación de un VNet clásico ###

Para desconectarse de la aplicación, necesitará la información de configuración que se estableció durante la integración de red virtual. Uso de esa información, hay a continuación, un comando para desconectarse de la aplicación de su red virtual.

    $vnet = Remove-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-07-01

## <a name="resource-manager-virtual-networks"></a>Administrador de recursos de redes virtuales ##

Redes virtuales del Administrador de recursos tienen API del Administrador de recursos de Azure, que simplificar algunos procesos cuando se compara con las redes virtuales clásicas. Tenemos una secuencia de comandos que le ayudarán a completar las siguientes tareas:

- Crear una red virtual del Administrador de recursos e Intégrelo la aplicación.
- Crear una puerta de enlace, configurar la conectividad de sitio de punto en una red virtual preexistentes de administrador de recursos y, a continuación, integrar la aplicación con él.
- Integrar su aplicación con una red virtual Administrador de recursos preexistentes que tiene una puerta de enlace y conectividad habilitado punto al sitio.
- Desconectar la aplicación de la red virtual.

### <a name="resource-manager-vnet-app-service-integration-script"></a>Script de integración de administrador de recursos VNet App Service ###

Copie la siguiente secuencia de comandos y guardarlo en un archivo. Si no desea usar la secuencia de comandos, no dude en aprender de para ver cómo configurar cosas con una red virtual del Administrador de recursos.


    function ReadHostWithDefault($message, $default)
    {
        $result = Read-Host "$message [$default]"
        if($result -eq "")
        {
            $result = $default
        }
            return $result
        }

    function PromptCustom($title, $optionValues, $optionDescriptions)
    {
        Write-Host $title
        Write-Host
        $a = @()
        for($i = 0; $i -lt $optionValues.Length; $i++){
            Write-Host "$($i+1))" $optionDescriptions[$i]
        }
        Write-Host

        while($true)
        {
            Write-Host "Choose an option: "
            $option = Read-Host
            $option = $option -as [int]

            if($option -ge 1 -and $option -le $optionValues.Length)
            {
                return $optionValues[$option-1]
            }
        }
    }

    function PromptYesNo($title, $message, $default = 0)
    {
        $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", ""
        $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", ""
        $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)
        $result = $host.ui.PromptForChoice($title, $message, $options, $default)
        return $result
    }

    function CreateVnet($resourceGroupName, $vnetName, $vnetAddressSpace, $vnetGatewayAddressSpace, $location)
    {
        Write-Host "Creating a new VNET"
        $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
        New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix $vnetAddressSpace -Subnet $gatewaySubnet
    }

    function CreateVnetGateway($resourceGroupName, $vnetName, $vnetIpName, $location, $vnetIpConfigName, $vnetGatewayName, $certificateData, $vnetPointToSiteAddressSpace)
    {
        $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName
        $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

        Write-Host "Creating a public IP address for this VNET"
        $pip = New-AzureRmPublicIpAddress -Name $vnetIpName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $vnetIpConfigName -Subnet $subnet -PublicIpAddress $pip

        Write-Host "Adding a root certificate to this VNET"
        $root = New-AzureRmVpnClientRootCertificate -Name "AppServiceCertificate.cer" -PublicCertData $certificateData

        Write-Host "Creating Azure VNET Gateway. This may take up to an hour."
        New-AzureRmVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $resourceGroupName -Location $location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Basic -VpnClientAddressPool $vnetPointToSiteAddressSpace -VpnClientRootCertificates $root
    }

    function AddNewVnet($subscriptionId, $webAppResourceGroup, $webAppName)
    {
        Write-Host "Adding a new Vnet"
        Write-Host
        $vnetName = Read-Host "Specify a name for this Virtual Network"

        $vnetGatewayName="$($vnetName)-gateway"
        $vnetIpName="$($vnetName)-ip"
        $vnetIpConfigName="$($vnetName)-ipconf"

        # Virtual Network settings
        $vnetAddressSpace="10.0.0.0/8"
        $vnetGatewayAddressSpace="10.5.0.0/16"
        $vnetPointToSiteAddressSpace="172.16.0.0/16"

        $changeRequested = 0
        $resourceGroupName = $webAppResourceGroup

        while($changeRequested -eq 0)
        {
            Write-Host
            Write-Host "Currently, I will create a VNET with the following settings:"
            Write-Host
            Write-Host "Virtual Network Name: $vnetName"
            Write-Host "Resource Group Name:  $resourceGroupName"
            Write-Host "Gateway Name: $vnetGatewayName"
            Write-Host "Vnet IP name: $vnetIpName"
            Write-Host "Vnet IP config name:  $vnetIpConfigName"
            Write-Host "Address Space:$vnetAddressSpace"
            Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
            Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
            Write-Host
            $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

            if($changeRequested -eq 0)
            {
                $vnetName = ReadHostWithDefault "Virtual Network Name" $vnetName
                $resourceGroupName = ReadHostWithDefault "Resource Group Name" $resourceGroupName
                $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
                $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
                $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
                $vnetAddressSpace = ReadHostWithDefault "Vnet Address Space" $vnetAddressSpace
                $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
                $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
            }
        }

        $ErrorActionPreference = "Stop";

        # We create the virtual network and add it here. The way this works is:
        # 1) Add the VNET association to the App. This allows the App to generate certificates, etc. for the VNET.
        # 2) Create the VNET and VNET gateway, add the certificates, create the public IP, etc., required for the gateway
        # 3) Get the VPN package from the gateway and pass it back to the App.

        $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup
        $location = $webApp.Location

        Write-Host "Creating App association to VNET"
        $propertiesObject = @{
         "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
        }
        $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

        CreateVnet $resourceGroupName $vnetName $vnetAddressSpace $vnetGatewayAddressSpace $location

        CreateVnetGateway $resourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

        Write-Host "Retrieving VPN Package and supplying to App"
        $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName -VirtualNetworkGatewayName $vnetGatewayName -ProcessorArchitecture Amd64

        # Put the VPN client configuration package onto the App
        $PropertiesObject = @{
        "vnetName" = $VirtualNetworkName; "vpnPackageUri" = $packageUri
        }

        New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

        Write-Host "Finished!"
    }

    function AddExistingVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
        $ErrorActionPreference = "Stop";

        # At this point, the gateway should be able to be joined to an App, but may require some minor tweaking. We will declare to the App now to use this VNET
        Write-Host "Getting App information"
        $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $location = $webApp.Location

        $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $currentVnet = $webAppConfig.Properties.VnetName
        if($currentVnet -ne $null -and $currentVnet -ne "")
        {
            Write-Host "Currently connected to VNET $currentVnet"
        }

        # Display existing vnets
        $vnets = Get-AzureRmVirtualNetwork
        $vnetNames = @()
        foreach($vnet in $vnets)
        {
            $vnetNames += $vnet.Name
        }

        Write-Host
        $vnet = PromptCustom "Select a VNET to integrate with" $vnets $vnetNames

        # We need to check if this VNET is able to be joined to a App, based on following criteria
            # If there is no gateway, we can create one.
            # If there is a gateway:
                # It must be of type Vpn
                # It must be of VpnType RouteBased
                # If it doesn't have the right certificate, we will need to add it.
                # If it doesn't have a point-to-site range, we will need to add it.

        $gatewaySubnet = $vnet.Subnets | Where-Object { $_.Name -eq "GatewaySubnet" }

        if($gatewaySubnet -eq $null -or $gatewaySubnet.IpConfigurations -eq $null -or $gatewaySubnet.IpConfigurations.Count -eq 0)
        {
            $ErrorActionPreference = "Continue";
            # There is no gateway. We need to create one.
            Write-Host "This Virtual Network has no gateway. I will need to create one."

            $vnetName = $vnet.Name
            $vnetGatewayName="$($vnetName)-gateway"
            $vnetIpName="$($vnetName)-ip"
            $vnetIpConfigName="$($vnetName)-ipconf"

            # Virtual Network settings
            $vnetAddressSpace="10.0.0.0/8"
            $vnetGatewayAddressSpace="10.5.0.0/16"
            $vnetPointToSiteAddressSpace="172.16.0.0/16"

            $changeRequested = 0

            Write-Host "Your VNET is in the address space $($vnet.AddressSpace.AddressPrefixes), with the following Subnets:"
            foreach($subnet in $vnet.Subnets)
            {
                Write-Host "$($subnet.Name): $($subnet.AddressPrefix)"
            }

            $vnetGatewayAddressSpace = Read-Host "Please choose a GatewaySubnet address space"

            while($changeRequested -eq 0)
            {
                Write-Host
                Write-Host "Currently, I will create a VNET gateway with the following settings:"
                Write-Host
                Write-Host "Virtual Network Name: $vnetName"
                Write-Host "Resource Group Name:  $($vnet.ResourceGroupName)"
                Write-Host "Gateway Name: $vnetGatewayName"
                Write-Host "Vnet IP name: $vnetIpName"
                Write-Host "Vnet IP config name:  $vnetIpConfigName"
                Write-Host "Address Space:$($vnet.AddressSpace.AddressPrefixes)"
                Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
                Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
                Write-Host
                $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

                if($changeRequested -eq 0)
                {
                    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
                    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
                    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
                    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
                    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
                }
            }

            $ErrorActionPreference = "Stop";

            Write-Host "Creating App association to VNET"
            $propertiesObject = @{
             "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
            }

            $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

            # If there is no gateway subnet, we need to create one.
            if($gatewaySubnet -eq $null)
            {
                $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
                $vnet.Subnets.Add($gatewaySubnet);
                Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
            }

            CreateVnetGateway $vnet.ResourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

            $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $vnetGatewayName
        }
        else
        {
            $uriParts = $gatewaySubnet.IpConfigurations[0].Id.Split('/')
            $gatewayResourceGroup = $uriParts[4]
            $gatewayName = $uriParts[8]

            $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $gatewayName

            # validate gateway types, etc.
            if($gateway.GatewayType -ne "Vpn")
            {
                Write-Error "This gateway is not of the Vpn type. It cannot be joined to an App."
                return
            }

            if($gateway.VpnType -ne "RouteBased")
            {
                Write-Error "This gateways Vpn type is not RouteBased. It cannot be joined to an App."
                return
            }

            if($gateway.VpnClientConfiguration -eq $null -or $gateway.VpnClientConfiguration.VpnClientAddressPool -eq $null)
            {
                Write-Host "This gateway does not have a Point-to-site Address Range. Please specify one in CIDR notation, e.g. 10.0.0.0/8"
                $pointToSiteAddress = Read-Host "Point-To-Site Address Space"
                Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $gateway.Name -VpnClientAddressPool $pointToSiteAddress
            }

            Write-Host "Creating App association to VNET"
            $propertiesObject = @{
             "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnet.Name)"
            }

            $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

            # We need to check if the certificate here exists in the gateway.
            $certificates = $gateway.VpnClientConfiguration.VpnClientRootCertificates

            $certFound = $false
            foreach($certificate in $certificates)
            {
                if($certificate.PublicCertData -eq $virtualNetwork.Properties.CertBlob)
                {
                    $certFound = $true
                    break
                }
            }

            if(-not $certFound)
            {
                Write-Host "Adding certificate"
                Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName "AppServiceCertificate.cer" -PublicCertData $virtualNetwork.Properties.CertBlob -VirtualNetworkGatewayName $gateway.Name
            }
        }

        # Now finish joining by getting the VPN package and giving it to the App
        Write-Host "Retrieving VPN Package and supplying to App"
        $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $vnet.ResourceGroupName -VirtualNetworkGatewayName $gateway.Name -ProcessorArchitecture Amd64

        # Put the VPN client configuration package onto the App
        $PropertiesObject = @{
        "vnetName" = $vnet.Name; "vpnPackageUri" = $packageUri
        }

        New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

        Write-Host "Finished!"
    }

    function RemoveVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
        $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $currentVnet = $webAppConfig.Properties.VnetName
        if($currentVnet -ne $null -and $currentVnet -ne "")
        {
            Write-Host "Currently connected to VNET $currentVnet"

            Remove-AzureRmResource -ResourceName "$($webAppName)/$($currentVnet)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        }
          else
        {
          Write-Host "Not connected to a VNET."
        }
    }

    Write-Host "Please Login"
    Login-AzureRmAccount

    # Choose subscription. If there's only one we will choose automatically

    $subs = Get-AzureRmSubscription
    $subscriptionId = ""

    if($subs.Length -eq 0)
    {
        Write-Error "No subscriptions bound to this account."
        return
    }

    if($subs.Length -eq 1)
    {
        $subscriptionId = $subs[0].SubscriptionId
    }
    else
    {
        $subscriptionChoices = @()
        $subscriptionValues = @()

        foreach($subscription in $subs){
        $subscriptionChoices += "$($subscription.SubscriptionName) ($($subscription.SubscriptionId))";
        $subscriptionValues += ($subscription.SubscriptionId);
        }

        $subscriptionId = PromptCustom "Choose a subscription" $subscriptionValues $subscriptionChoices
    }

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $resourceGroup = Read-Host "Please enter the Resource Group of your App"

    $appName = Read-Host "Please enter the Name of your App"

    $options = @("Add a NEW Virtual Network to an App", "Add an EXISTING Virtual Network to an App", "Remove a Virtual Network from an App");
    $optionValues = @(0, 1, 2)
    $option = PromptCustom "What do you want to do?" $optionValues $options

    if($option -eq 0)
    {
        AddNewVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 1)
    {
        AddExistingVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 2)
    {
        RemoveVnet $subscriptionId $resourceGroup $appName
    }

Guardar una copia de la secuencia de comandos. En este artículo, se denomina V2VnetAllinOne.ps1, pero puede usar otro nombre. No hay ningún argumento para esta secuencia de comandos. Simplemente ejecútelo. Lo primero que va a hacer la secuencia de comandos es pedirá que inicie sesión en. Después de iniciar sesión, la secuencia de comandos obtiene detalles sobre su cuenta y devuelve una lista de suscripciones. Sin contar la solicitud de sus credenciales, la ejecución de secuencia de comandos inicial es similar a esta:

    PS C:\Users\ccompy\Documents\VNET> .\V2VnetAllInOne.ps1
    Please Login

    Environment           : AzureCloud
    Account               : ccompy@microsoft.com
    TenantId              : 722278f-fef1-499f-91ab-2323d011db47
    SubscriptionId        : af5358e1-acac-2c90-a9eb-722190abf47a
    CurrentStorageAccount :

    Choose a subscription

    1) Demostración de suscripción (af5358e1-acac-2c90-a9eb-722190abf47a)
    2) Prueba de MS (a5350f55-dd5a-41ec-2ddb-ff7b911bb2ef)
    3) Demostración púrpura suscripción (2d4c99a4-57f9-4d5e-a0a1-0034c52db59d)

    Elija una opción: 3

    Cuenta: ccompy@microsoft.com entorno: AzureCloud suscripción: 2d4c99a4-57f9-4d5e-a0a1-0034c52db59d inquilino: 722278f fef1 499f 91ab 2323d011db47

    Escriba el grupo de recursos de la aplicación: c hcdemo escriba el nombre de la aplicación: v2vnetpowershell ¿qué desea hacer?

    1) Agregar una nueva red Virtual a una aplicación
    2) Agregar una red Virtual de existente a una aplicación
    3) Quitar una red Virtual de una aplicación

El resto de esta sección explica cada una de las tres opciones.

### <a name="create-a-resource-manager-vnet-and-integrate-with-it"></a>Crear un administrador de recursos de VNet e intégrelo con él ###

Para crear una nueva red virtual que utiliza el modelo de implementación de administrador de recursos e integrarlo con la aplicación, seleccione **1) agregar una nueva red Virtual a una aplicación**. Le solicitará que el nombre de la red virtual. En mi caso, como puede ver en la siguiente configuración, utiliza el nombre, v2pshell.

La secuencia de comandos proporciona los detalles acerca de la red virtual que está creando. Si quiero, ¿puedo cambiar cualquiera de los valores. En la ejecución de este ejemplo, hemos creado una red virtual que tiene los siguientes valores:

    Virtual Network Name:         v2pshell
    Resource Group Name:          hcdemo-rg
    Gateway Name:                 v2pshell-gateway
    Vnet IP name:                 v2pshell-ip
    Vnet IP config name:          v2pshell-ipconf
    Address Space:                10.0.0.0/8
    Gateway Address Space:        10.5.0.0/16
    Point-To-Site Address Space:  172.16.0.0/12

    Do you wish to change these settings?
    [Y] Yes  [N] No  [?] Help (default is "N"):

Si desea cambiar alguno de los valores, escriba **Y** y realice los cambios. Cuando esté satisfecho con la configuración de red virtual, escriba **N** o simplemente presione ENTRAR cuando se le solicite acerca de cómo cambiar la configuración. Desde aquí hasta la finalización, el script le indicará algunas de las configuraciones que ' letras i haciendo hasta que se inicia crear la puerta de enlace de red virtual. Este paso puede tardar hasta una hora. No hay ningún indicador de progreso durante esta fase, pero la secuencia de comandos le informará cuando haya acabado de crear la puerta de enlace.

Cuando finalice el script, indicará **terminado**. En este momento, tendrá una red virtual de administrador de recursos que tiene el nombre y la configuración que haya seleccionado. Esta nueva red virtual también se integrará con la aplicación.

### <a name="integrate-your-app-with-a-preexisting-resource-manager-vnet"></a>Integrar su aplicación con una preexistentes VNet Administrador de recursos ###

Cuando está integración con una red virtual preexistente, si proporciona una red virtual del Administrador de recursos que no tiene una puerta de enlace o el sitio de punto de conexión, la secuencia de comandos configurará. Si el VNET ya tiene esas cosas configurar, la secuencia de comandos va directamente a la integración de la aplicación. Para iniciar este proceso, simplemente seleccione **2) agregar una red Virtual existente a una aplicación**.

Esta opción sólo funciona si tiene una red virtual Administrador de recursos preexistentes que está en la misma suscripción como la aplicación. Después de seleccionar la opción, aparecerá una lista de sus redes virtual del Administrador de recursos.   

    Select a VNET to integrate with

    1) v2demonetwork
    2) v2pshell
    3) v2vnetintdemo
    4) v2asenetwork
    5) v2pshell2

    Elija una opción: 5

Simplemente seleccione la red virtual que desea integrar con. Si ya tiene una puerta de enlace que tiene conectividad punto al sitio habilitado, la secuencia de comandos simplemente integrará su aplicación con su red virtual. Si no tiene una puerta de enlace, debe especificar la subred de puerta de enlace. La subred de puerta de enlace debe estar en el espacio de direcciones de red virtual y no puede ser de cualquier otra subred. Si tiene una red virtual sin una puerta de enlace y ejecutar este paso, cosas tiene este aspecto:

    This Virtual Network has no gateway. I will need to create one.
    Your VNET is in the address space 172.16.0.0/16, with the following Subnets:
    default: 172.16.0.0/24
    Please choose a GatewaySubnet address space: 172.16.1.0/26

En este ejemplo, hemos creado una puerta de enlace de red virtual que tiene los siguientes valores:

    Virtual Network Name:         v2pshell2
    Resource Group Name:          vnetdemo-rg
    Gateway Name:                 v2pshell2-gateway
    Vnet IP name:                 v2pshell2-ip
    Vnet IP config name:          v2pshell2-ipconf
    Address Space:                172.16.0.0/16
    Gateway Address Space:        172.16.1.0/26
    Point-To-Site Address Space:  172.16.0.0/12

    Do you wish to change these settings?
    [Y] Yes  [N] No  [?] Help (default is "N"):
    Creating App association to VNET

Si desea cambiar cualquiera de los valores, puede hacerlo. En caso contrario, presione ENTRAR y la secuencia de comandos creará la puerta de enlace y adjuntar la aplicación a su red virtual. La hora de creación de la puerta de enlace es una hora, sin embargo, asegúrese de que tenga en cuenta. Cuando finalice todo, la secuencia de comandos indicará **terminado**.

### <a name="disconnect-your-app-from-a-resource-manager-vnet"></a>Desconectar la aplicación de un VNet Administrador de recursos ###

Desconectarse de la aplicación de su red virtual no tomar hacia abajo de la puerta de enlace o deshabilitar el sitio de punto de conexión. Es posible que, después de todo, utilizar para algo más. También no desconecta ella desde otras aplicaciones de distinto al que ha proporcionado. Para realizar esta acción, seleccione **3) eliminar una red Virtual de una aplicación**. Cuando lo haga, verá algo parecido a esto:

    Currently connected to VNET v2pshell

    Confirm
    Are you sure you want to delete the following resource:
    /subscriptions/edcc99a4-b7f9-4b5e-a9a1-3034c51db496/resourceGroups/hcdemo-rg/providers/Microsoft.Web/sites/v2vnetpowers
    hell/virtualNetworkConnections/v2pshell
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Aunque la secuencia de comandos indica eliminar, no se elimina la red virtual. Solo está eliminando la integración. Después de confirmar que esto es lo que desea, el comando se procesa muy rápidamente y le indica que **True** cuando está listo.

<!--Links-->
[createvpngateway]: http://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/
[azureportal]: http://portal.azure.com
