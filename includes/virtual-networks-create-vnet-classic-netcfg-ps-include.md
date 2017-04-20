## <a name="how-to-create-a-vnet-using-a-network-config-file-from-powershell"></a>Cómo crear un VNet con un archivo de configuración de red de PowerShell

Azure utiliza un archivo xml para definir todas las VNets disponibles para una suscripción. Puede descargar el archivo y editarlo para modificar o eliminar VNets existentes y crear nuevos. En este documento, se obtenga información sobre cómo descargar este archivo, que se conoce como archivo de configuración (o netcgf) de la red y editarlo para crear un nuevo VNet. Compruebe el [esquema de configuración de red virtual Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) para obtener más información sobre el archivo de configuración de red.

Para crear un VNet con un archivo de netcfg con PowerShell, siga los pasos siguientes.

1. Si nunca ha usado Azure PowerShell, vea [cómo instalar y configurar Azure PowerShell](../articles/powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccione la suscripción.
2. Desde la consola de PowerShell de Azure, use el cmdlet **Get-AzureVnetConfig** para descargar el archivo de configuración de red, ejecute el siguiente comando. 

        Get-AzureVNetConfig -ExportToFile c:\NetworkConfig.xml

    Resultados esperados:

        XMLConfiguration                                                                                                     
        ----------------                                                                                                     
        <?xml version="1.0" encoding="utf-8"?>...  

3. Abra el archivo que guardó en el paso 2 con cualquier aplicación de edición de texto o XML y busque el **<VirtualNetworkSites>** elemento. Si tiene cualquier redes ya ha creado, cada red se mostrará como su propia **<VirtualNetworkSite>** elemento.
4. Para crear la red virtual descritas en este escenario, agregue el siguiente código XML justo por debajo del **<VirtualNetworkSites>** elemento:

        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>

9.  Guarde el archivo de configuración de red.
10. Desde la consola de PowerShell de Azure, use el cmdlet **Set-AzureVnetConfig** para cargar el archivo de configuración de red, ejecute el siguiente comando. Observe el resultado en el comando, debería ver **correcta** en **OperationStatus**. Si es que no es el caso, compruebe el archivo xml de errores.

        Set-AzureVNetConfig -ConfigurationPath c:\NetworkConfig.xml

    Éste es el resultado esperado para el comando anterior:

        OperationDescription OperationId                          OperationStatus
        -------------------- -----------                          ---------------
        Set-AzureVNetConfig  49579cb9-3f49-07c3-ada2-7abd0e28c4e4 Succeeded 
    
11. Desde la consola de PowerShell de Azure, use el cmdlet **Get-AzureVnetSite** para comprobar que se agregó la nueva red, ejecute el siguiente comando. 

        Get-AzureVNetSite -VNetName TestVNet

    Éste es el resultado esperado para el comando anterior:

        AddressSpacePrefixes : {192.168.0.0/16}
        Location             : Central US
        AffinityGroup        : 
        DnsServers           : {}
        GatewayProfile       : 
        GatewaySites         : 
        Id                   : b953f47b-fad9-4075-8cfe-73ff9c98278f
        InUse                : False
        Label                : 
        Name                 : TestVNet
        State                : Created
        Subnets              : {FrontEnd, BackEnd}
        OperationDescription : Get-AzureVNetSite
        OperationId          : 3f35d533-1f38-09c0-b286-3d07cd0904d8
        OperationStatus      : Succeeded