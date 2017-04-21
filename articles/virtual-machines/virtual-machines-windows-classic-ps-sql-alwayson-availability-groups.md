<properties
    pageTitle="Configurar siempre en el grupo de disponibilidad en Azure VM con PowerShell"
    description="Este tutorial usa recursos creados con el modelo de implementación clásica y usa PowerShell para crear una siempre en grupo disponibilidad en Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="mikeray" />

# <a name="configure-always-on-availability-group-in-azure-vm-with-powershell"></a>Configurar siempre en el grupo de disponibilidad en Azure VM con PowerShell

> [AZURE.SELECTOR]
- [Administrador de recursos: plantilla](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Administrador de recursos: Manual](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Clásica: interfaz de usuario](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Clásica: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Azure máquinas virtuales de Windows (VM) puede ayudar a los administradores de base de datos para implementar cuanto menor sea el costo de un sistema de SQL Server de alta disponibilidad. En este tutorial se muestra cómo implementar un grupo de disponibilidad con SQL Server siempre en llevar a cabo dentro de un entorno de Azure. Al final del curso, su solución de SQL Server siempre en Azure tendrá los siguientes elementos:

- Una red virtual que contenga varias subredes, incluidos un front-end y una subred de back-end

- Un controlador de dominio con un dominio de Active Directory (AD)

- Dos SQL Server las máquinas virtuales implementado a la subred back-end y unido al dominio de Active Directory

- Un clúster de nodo 3 WSFC con el modelo de quórum de mayoría de nodo

- Un grupo de disponibilidad con dos réplicas sincrónico confirmación de una base de datos de disponibilidad

Este escenario se elige para su sencillo, no para su rentabilidad u otros factores en Azure. Por ejemplo, puede minimizar el número de máquinas virtuales de un grupo de réplica de dos disponibilidad para guardar en cálculo horas en Azure mediante el controlador de dominio como el auxiliar de compartir archivo quórum en un clúster de 2 nodos WSFC. Este método reduce el recuento VM en uno de la configuración anterior.

Este tutorial está pensado para mostrar los pasos necesarios para configurar la solución descrita anteriormente sin tener que describe los detalles de cada paso. Por lo tanto, en lugar de mostrar los pasos de configuración de la interfaz gráfica, usa PowerShell scripting rápidamente le lleva a través de cada paso. Presupone lo siguiente:

- Ya tiene una cuenta de Azure con la suscripción de máquina virtual.

- Ha instalado los [cmdlets de PowerShell de Azure](../powershell-install-configure.md).

- Ya tiene una sólida comprensión de siempre en grupos de disponibilidad para soluciones de local. Para obtener más información, consulte [Siempre en disponibilidad de grupos (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Conectarse a su suscripción de Azure y crear la red Virtual

1. En una ventana de PowerShell en el equipo local, importar el módulo Azure, descargue un archivo de configuración de publicación en el equipo y conectar su sesión de PowerShell a su suscripción de Azure importando la configuración de publicación descargada.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    El comando **Get-AzurePublishgSettingsFile** genera automáticamente una administración certificado con Azure descarga en su equipo. Un explorador se abrirá automáticamente y se le pide que escriba las credenciales de cuenta de Microsoft para la suscripción de Azure. El archivo descargado **.publishsettings** contiene toda la información que necesita para administrar su suscripción de Azure. Después de guardar este archivo en un directorio local, importar mediante el comando **Importar AzurePublishSettingsFile** .

    >[AZURE.NOTE] El archivo publishsettings contiene sus credenciales (sin codificar) que se utilizan para administrar sus suscripciones Azure y servicios. La práctica recomendada para este archivo es guardarlo temporalmente fuera de los directorios de origen (por ejemplo, en la carpeta Libraries\Documents) y a continuación, eliminarlo una vez completada la importación. Un usuario malintencionado de obtener acceso al archivo publishsettings puede editar, crear y eliminar los servicios de Azure.

1. Definir una serie de variables que va a utilizar para crear su infraestructura de TI de la nube.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    Preste atención a las siguientes acciones para asegurarse de que sus comandos se realizará correctamente más adelante:

    - Las variables **$storageAccountName** y **$dcServiceName** deben ser únicos porque se usan para identificar el servidor de almacenamiento de la nube cuenta y la nube, respectivamente, de internet.

    - Nombres especificados para las variables **$affinityGroupName** y **$virtualNetworkName** están configurados en el documento de configuración de red virtual que usará más adelante.

    - **$sqlImageName** especifica el nombre de la imagen de máquina virtual que contiene SQL Server 2012 Service Pack 1 Enterprise Edition actualizado.

    - Para simplificar, **Contoso! 000** es la misma contraseña que utiliza en todo el tutorial.

1. Crear un grupo de afinidad.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

1. Crear una red virtual importando un archivo de configuración.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    El archivo de configuración contiene el siguiente documento XML. En resumen, especifica una red virtual denominada **ContosoNET** en el grupo de afinidad denominado **ContosoAG**y tiene el espacio de dirección **10.10.0.0/16** y tiene dos subredes, **10.10.1.0/24** y **10.10.2.0/24**, que son la subred frontal y hacer copias subred, respectivamente. La subred frontal es donde puede colocar las aplicaciones cliente, como Microsoft SharePoint y la subred atrás es donde colocará las máquinas virtuales de SQL Server. Si cambia las variables **$affinityGroupName** y **$virtualNetworkName** anteriormente, también debe cambiar los nombres correspondientes a continuación.

        <NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

1. Crear una cuenta de almacenamiento que está asociada con el grupo de afinidad había creado y establecerlo como la cuenta de almacenamiento actual en la suscripción.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

1. Crear el servidor de controlador de dominio en el nuevo conjunto de disponibilidad y servicio de nube.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    Esta serie de comandos canalizadas hacer lo siguiente:

    - **Nuevo AzureVMConfig** crea una configuración de máquina virtual.

    - **Agregar AzureProvisioningConfig** proporciona los parámetros de configuración de un servidor de Windows independiente.

    - **Agregar AzureDataDisk** agrega el disco de datos que se usa para almacenar los datos de Active Directory, con la opción establecida en ninguna.

    - **Nuevo AzureVM** crea un nuevo servicio de nube y la nueva máquina virtual de Azure en el nuevo servicio de nube.

1. Espere a que la nueva máquina virtual se configurarán por completo y descargar el archivo de escritorio remoto en el directorio de trabajo. Puesto que la nueva máquina virtual de Azure tarda mucho tiempo en abastecerse, el bucle while continúa para sondear la nueva máquina virtual hasta que esté listo para su uso.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

El servidor DC ahora está configurado correctamente. A continuación, va a configurar el dominio de Active Directory en este servidor DC. Deje la ventana de PowerShell abierta en el equipo local. Podrá utilizarla más tarde para crear dos máquinas virtuales de servidor de SQL.

## <a name="configure-the-domain-controller"></a>Configurar el controlador de dominio

1. Conectarse al servidor de controlador de dominio, ejecute el archivo de escritorio remoto. Usar el Administrador de la máquina AzureAdmin de nombre de usuario y contraseña **Contoso! 000**, que se haya especificado al crear la nueva máquina virtual.

1. Abra una ventana de PowerShell en modo de administrador.

1. Ejecute el siguiente **DCPROMO. EXE** comando para configurar el dominio **corp.contoso.com** , con los directorios de datos en la unidad M.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    Una vez completado el comando, la máquina virtual se reinicia automáticamente.

1. Vuelva a conectarse al servidor DC, ejecute el archivo de escritorio remoto. En este momento, inicie sesión como **CORP\Administrator**.

1. Abra una ventana de PowerShell en modo de administrador e importar el módulo de Active Directory PowerShell mediante el siguiente comando:

        Import-Module ActiveDirectory

1. Ejecute los siguientes comandos para agregar tres usuarios al dominio.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** se utiliza para configurar todos los elementos relacionados con las instancias del servicio de SQL Server, el clúster WSFC y el grupo de disponibilidad. **CORP\SQLSvc1** y **CORP\SQLSvc2** se usan como las cuentas de servicio de SQL Server para los dos las máquinas virtuales de SQL Server.

1. A continuación, ejecute los comandos siguientes para dar a **CORP\Install** los permisos para crear objetos de equipo en el dominio.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    El GUID especificado anteriormente es del tipo de objeto de equipo. La cuenta de **CORP\Install** necesita el permiso **Crear objetos de equipo** y **Leer todas las propiedades** para crear los objetos Active directo para el clúster WSFC. El permiso de **Lectura de todas las propiedades** ya se expresa a CORP\Install de forma predeterminada, por lo que es necesario conceder de forma explícita. Para obtener más información sobre los permisos necesarios para crear el clúster WSFC, consulte [Guía paso a paso de clúster de migración tras error: configuración de cuentas de Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Ahora que ha terminado de configurar Active Directory y los objetos de usuario, creará dos máquinas virtuales de servidor SQL y unirse a ellas para este dominio.

## <a name="create-the-sql-server-vms"></a>Crear las máquinas virtuales SQL Server

1. Seguir usando la ventana de PowerShell que está abierta en el equipo local. Definir las variables adicionales siguientes:

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    De dirección IP **10.10.0.4** normalmente se asigna a la primera VM se crea en la subred **10.10.0.0/16** de su red virtual Azure. Debe comprobar que esta es la dirección de su servidor DC ejecutando **IPCONFIG**.

1. Ejecute los comandos siguientes canalizados para crear la primera VM en el clúster WSFC, denominado **ContosoQuorum**:

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    Tenga en cuenta las siguientes afirmaciones sobre el comando anterior:

    - **Nuevo AzureVMConfig** crea una configuración de máquina virtual con el nombre del conjunto de disponibilidad deseado. Se crearán las VM subsiguientes con el mismo nombre del conjunto de disponibilidad para que forman parte del mismo conjunto de disponibilidad.

    - **Agregar AzureProvisioningConfig** combina la máquina virtual para el dominio de Active Directory que creó.

    - **Establecer AzureSubnet** coloca la máquina virtual en la subred atrás.

    - **Nuevo AzureVM** crea un nuevo servicio de nube y la nueva máquina virtual de Azure en el nuevo servicio de nube. El parámetro **DnsSettings** especifica que el servidor DNS para los servidores en el nuevo servicio de nube tiene la de dirección IP **10.10.0.4**, que es la dirección IP del servidor DC. Este parámetro es necesario para habilitar máquinas virtuales de nuevas en el servicio de nube para unirse al dominio de Active Directory correctamente. Sin este parámetro, debe establecer manualmente la configuración de IPv4 en la máquina virtual para usar el servidor DC como el servidor DNS principal después de la máquina virtual se aprovisiona y, a continuación, unirse a la máquina virtual para el dominio de Active Directory.

1. Ejecute los comandos siguientes canalizados para crear la VM de servidor SQL, denominados **ContosoSQL1** y **ContosoSQL2**.

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    Tenga en cuenta las siguientes afirmaciones sobre los comandos de arriba:

    - **Nuevo AzureVMConfig** utiliza el mismo nombre de conjunto de disponibilidad como el servidor DC y utiliza la imagen de SQL Server 2012 Service Pack 1 Enterprise Edition en la Galería de máquina virtual. También establece el disco de sistema operativo en sólo lectura de caché (sin caché de escritura). Se recomienda que migre los archivos de base de datos en un disco de datos independiente adjuntar a la máquina virtual y configurarla con sin leer o la caché de escritura. Sin embargo, lo más próximo es quitar de caché de escritura en el disco de sistema operativo, ya que no se pueden quitar leer caché en el disco de sistema operativo.

    - **Agregar AzureProvisioningConfig** combina la máquina virtual para el dominio de Active Directory que creó.

    - **Establecer AzureSubnet** coloca la máquina virtual en la subred atrás.

    - **Agregar AzureEndpoint** agrega extremos de access para que las aplicaciones cliente pueden tener acceso a estas instancias de servicios de SQL Server en internet. Puertos diferentes reciben a ContosoSQL1 y ContosoSQL2.

    - **Nuevo AzureVM** crea la nueva máquina virtual de servidor de SQL en el mismo servicio de nube como ContosoQuorum. Debe colocar las máquinas virtuales en el mismo servicio de nube si desea que aparezcan en el mismo conjunto de disponibilidad.

1. Espere a que cada VM se configurarán por completo y descargar su archivo de escritorio remoto en el directorio de trabajo. El bucle recorre las tres nuevas máquinas virtuales y ejecuta los comandos dentro de las llaves de nivel superior para cada uno de ellos.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    Las máquinas virtuales de SQL Server ahora aprovisionadas y ejecución, pero se instalan con SQL Server con las opciones predeterminadas.

## <a name="initialize-the-wsfc-cluster-vms"></a>Inicializar las VM de clúster WSFC

En esta sección, debe modificar los tres servidores que se usa en el clúster WSFC y la instalación de SQL Server. Específicamente:

- (Todos los servidores) Debe instalar la característica **Clúster de migración tras error** .

- (Todos los servidores) Debe agregar **CORP\Install** como el **Administrador**del equipo.

- (ContosoSQL1 y ContosoSQL2 sólo) Debe agregar **CORP\Install** como una función **sysadmin** en la base de datos de forma predeterminada.

- (ContosoSQL1 y ContosoSQL2 sólo) Debe agregar **NT AUTHORITY\System** como un inicio de sesión con los permisos siguientes:

    - Modificar cualquier grupo de disponibilidad

    - Conectar SQL

    - Estado de servidor de vista

- (ContosoSQL1 y ContosoSQL2 sólo) El protocolo **TCP** ya está habilitado en la VM de SQL Server. Sin embargo, todavía necesita abrir el firewall para el acceso remoto de SQL Server.

Ahora está listo para empezar. A partir de **ContosoQuorum**, siga los pasos siguientes:

1. Conectarse a **ContosoQuorum** iniciando los archivos del escritorio remotos. Usar el Administrador de la máquina **AzureAdmin** de nombre de usuario y contraseña **Contoso! 000**, que se haya especificado al crear las máquinas virtuales.

1. Compruebe que los equipos se han unido a **corp.contoso.com**.

1. Espere a que la instalación de SQL Server finalizar la ejecución de las tareas de inicialización automatizado antes de continuar.

1. Abra una ventana de PowerShell en modo de administrador.

1. Instalar la característica clúster de migración tras error de Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering

1. Agregar **CORP\Install** como administrador local.

        net localgroup administrators "CORP\Install" /Add

1. Inicie sesión en ContosoQuorum. Haya terminado con este servidor ahora.

        logoff.exe

A continuación, inicialice **ContosoSQL1** y **ContosoSQL2**. Siga los pasos siguientes, que son idénticos tanto de las máquinas virtuales de SQL Server.

1. Iniciar los archivos del escritorio remotos para conectarse a dos máquinas virtuales de servidor de SQL. Usar el Administrador de la máquina **AzureAdmin** de nombre de usuario y contraseña **Contoso! 000**, que se haya especificado al crear las máquinas virtuales.

1. Compruebe que los equipos se han unido a **corp.contoso.com**.

1. Espere a que la instalación de SQL Server finalizar la ejecución de las tareas de inicialización automatizado antes de continuar.

1. Abra una ventana de PowerShell en modo de administrador.

1. Instalar la característica clúster de migración tras error de Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering

1. Agregar **CORP\Install** como administrador local

        net localgroup administrators "CORP\Install" /Add

1. Importar el proveedor de PowerShell SQL Server.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking

1. Agregar **CORP\Install** como la función sysadmin para la instancia de SQL Server predeterminada.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."

1. Agregar **NT AUTHORITY\System** como un inicio de sesión con los tres permisos describen anteriormente.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."

1. Abra el firewall para el acceso remoto de SQL Server.

        netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP

1. Inicie sesión en ambos máquinas virtuales.

        logoff.exe

Por último, está listo para configurar el grupo de disponibilidad. El proveedor de PowerShell de SQL Server que usa para realizar el trabajo en **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Configurar el grupo de disponibilidad

1. Vuelva a conectarse a **ContosoSQL1** iniciando los archivos del escritorio remotos. En lugar de iniciar sesión con la cuenta de equipo, inicie sesión con **CORP\Install**.

1. Abra una ventana de PowerShell en modo de administrador.

1. Definir las variables siguientes:

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"

1. Importar el proveedor de PowerShell SQL Server.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking

1. Cambiar la cuenta de servicio de SQL Server para ContosoSQL1 a CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Cambiar la cuenta de servicio de SQL Server para ContosoSQL2 a CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Descargar **CreateAzureFailoverCluster.ps1** de [Crear el clúster de WSFC para siempre en disponibilidad de los grupos en la máquina virtual de Azure](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) en el directorio local de trabajo. Esta secuencia de comandos que usa para crear un clúster WSFC funcional. Para obtener información importante sobre cómo WSFC interactúa con la red de Azure, consulte [alta disponibilidad y recuperación para SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-high-availability-dr.md).

1. Cambie al directorio de trabajo y cree el clúster WSFC con la secuencia de comandos descargado.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"

1. Habilitar siempre en grupos de disponibilidad de las instancias predeterminadas de SQL Server en **ContosoSQL1** y **ContosoSQL2**.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)

1. Crear un directorio de copia de seguridad y conceder permisos para las cuentas de servicio de SQL Server. Preparar la base de datos de disponibilidad en la segunda réplica usará este directorio.

        $backup = "C:\backup"
        New-Item $backup -ItemType directory
        net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
        icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")

1. Crear una base de datos en **ContosoSQL1** denominado **MyDB1**, realizar una copia completa y una copia de seguridad del registro y restaurar en **ContosoSQL2** con la opción **WITH NORECOVERY** .

        Invoke-SqlCmd -Query "CREATE database $db"
        Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
        Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
        Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
        Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery

1. Crear la disponibilidad de los extremos de grupo en las máquinas virtuales de SQL Server y establecer los permisos adecuados en los extremos.

        $endpoint =
            New-SqlHadrEndpoint MyMirroringEndpoint `
            -Port 5022 `
            -Path "SQLSERVER:\SQL\$server1\Default"
        Set-SqlHadrEndpoint `
            -InputObject $endpoint `
            -State "Started"
        $endpoint =
            New-SqlHadrEndpoint MyMirroringEndpoint `
            -Port 5022 `
            -Path "SQLSERVER:\SQL\$server2\Default"
        Set-SqlHadrEndpoint `
            -InputObject $endpoint `
            -State "Started"

        Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
        Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
        Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
        Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2

1. Crear réplicas de disponibilidad.

        $primaryReplica =
            New-SqlAvailabilityReplica `
            -Name $server1 `
            -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
            -AvailabilityMode "SynchronousCommit" `
            -FailoverMode "Automatic" `
            -Version 11 `
            -AsTemplate
        $secondaryReplica =
            New-SqlAvailabilityReplica `
            -Name $server2 `
            -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
            -AvailabilityMode "SynchronousCommit" `
            -FailoverMode "Automatic" `
            -Version 11 `
            -AsTemplate

1. Por último, cree el grupo de disponibilidad y unirse a la réplica secundaria al grupo de disponibilidad.

        New-SqlAvailabilityGroup `
            -Name $ag `
            -Path "SQLSERVER:\SQL\$server1\Default" `
            -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
            -Database $db
        Join-SqlAvailabilityGroup `
            -Path "SQLSERVER:\SQL\$server2\Default" `
            -Name $ag
        Add-SqlAvailabilityDatabase `
            -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
            -Database $db

## <a name="next-steps"></a>Pasos siguientes
Ha ahora implementado correctamente SQL Server siempre en mediante la creación de un grupo de disponibilidad en Azure. Para configurar una escucha para este grupo de disponibilidad, consulte [configurar un detector de ILB para siempre en disponibilidad de los grupos de Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

Para más información sobre el uso de SQL Server en Azure, vea [SQL Server en Azure máquinas virtuales de Windows](virtual-machines-windows-sql-server-iaas-overview.md).
