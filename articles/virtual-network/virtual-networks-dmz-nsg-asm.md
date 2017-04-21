<properties
   pageTitle="Ejemplo de DMZ: crear una DMZ sencilla con NSGs | Microsoft Azure"
   description="Crear una DMZ con grupos de seguridad de red (NSG)"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# <a name="example-1--build-a-simple-dmz-with-nsgs"></a>Ejemplo 1: crear una DMZ sencilla con NSGs

[Volver a la página de prácticas recomendada de seguridad límite][HOME]

En este ejemplo se creará una DMZ simple con cuatro grupos de seguridad de red y servidores de windows. También le guiará a través de cada uno de los comandos pertinentes para proporcionar un conocimiento más profundo de cada paso. También hay una sección de escenario de tráfico para proporcionar una detallada paso a paso cómo continúa el tráfico a través de los niveles de defensa en la DMZ. Por último, en las referencias de sección es el código completo e instrucciones para crear este entorno para probar y experimentar con diferentes escenarios. 

![Entrada DMZ con GSN][1]

## <a name="environment-description"></a>Descripción del entorno
En este ejemplo hay una suscripción que contiene lo siguiente:

- Dos servicios de nube: "FrontEnd001" y "BackEnd001"
- Una red Virtual, "CorpNetwork", con dos subredes; "Cliente" y "Back-end"
- Un grupo de seguridad de red que se aplica a ambas subredes
- Un servidor de Windows que representa un servidor de aplicaciones web ("IIS01")
- Los servidores de dos ventanas que representan aplicación volver fin servidores ("AppVM01", "AppVM02")
- Un servidor de Windows que representa un servidor DNS ("DNS01")

En la sección referencias a continuación hay un script de PowerShell que generará la mayoría del entorno descrito anteriormente. Crear las máquinas virtuales y redes virtuales, aunque haya terminado la secuencia de comandos de ejemplo, no se describen en detalle en este documento. 

Para crear el entorno;

  1.    Guardar el archivo de xml de configuración de red incluido en la sección referencias (actualizado con IP, ubicación y nombres de direcciones para que coincida con el escenario determinado)
  2.    Actualizar las variables de usuario en la secuencia de comandos para que coincida con el entorno que es la secuencia de comandos se ejecute en (suscripciones, nombres de servicio, etcetera)
  3.    Ejecutar la secuencia de comandos de PowerShell

**Nota**: la región indicada en la secuencia de comandos de PowerShell debe coincidir con la región indicada en el archivo xml de configuración de red.

Una vez que la secuencia de comandos se ejecute correctamente adicional pasos opcionales se pueden tomar, en la sección referencias son dos secuencias de comandos para configurar el servidor web y el servidor de la aplicación con una aplicación web simple para permitir pruebas con esta configuración DMZ.

Las siguientes secciones proporcionan una descripción detallada de los grupos de seguridad de red y su funcionamiento en este ejemplo a través de líneas de clave de la secuencia de comandos de PowerShell.

## <a name="network-security-groups-nsg"></a>Grupos de seguridad de red (NSG)
En este ejemplo, un grupo de GSN está integrado y, a continuación, cargar con seis reglas. 

>[AZURE.TIP] Por lo general, debe crear las reglas de "Permitir" específicas en primer lugar y, a continuación, las reglas de "Denegar" más genéricas última. El indica prioridad asignada que son las reglas evalúa primero. Una vez que se encuentra el tráfico a aplicar a una regla específica, no se evalúan otras reglas. Pueden aplicar reglas de GSN en cualquiera en la dirección de entrada o salida (desde la perspectiva de la subred).

Mediante declaración, que se crean las siguientes reglas para el tráfico entrante:

1.  Se permite el tráfico DNS interno (puerto 53)
2.  Se permite el tráfico RDP (puerto 3389) desde Internet a cualquier VM
3.  Se permite el tráfico HTTP (puerto 80) de Internet en el servidor web (IIS01)
4.  Se permite el tráfico (todos los puertos) desde IIS01 a AppVM1
5.  Cualquier (todos los puertos) el tráfico de Internet a toda la denegado VNet (ambas subredes)
6.  Se ha denegado el tráfico (todos los puertos) de la subred front-end a la subred back-end

Con estas reglas enlazado cada subred, si una solicitud HTTP era entrante desde Internet en el servidor web, ambas reglas 3 (permitir) y 5 (denegar) se aplican, pero como regla 3 tiene prioridad solo ésta se aplicará y regla 5 no debería tener en cuenta. Por lo tanto la solicitud HTTP podrán el servidor web. Si ese mismo tráfico estaba intentando alcanzar el servidor DNS01, regla 5 (denegar) sería el primero en aplicar y no se permitirá el tráfico para pasar al servidor. Regla 6 (denegar) bloquea la subred front-end de hablar a la subred back-end (excepto el tráfico permitido en las reglas 1 y 4), esto protege la red de back-end en caso de un intruso compromete la aplicación web en el front-end, el intruso habría acceso limitado a la red back-end "protegida" (solo a los recursos que se exponen en el servidor de AppVM01).

Hay una regla de salida predeterminado que permite tráfico saliente a internet. En este ejemplo, estamos permitir el tráfico saliente y no modificar las reglas de salida. Para bloquear el tráfico en ambas direcciones, definidos por el enrutamiento de usuario es necesario, esto se explora en "Ejemplo 3" por debajo.

Cada regla se explica con más detalle como sigue (**Nota**: cualquier elemento de la debajo de lista en el que comienza con un signo de dólar (por ejemplo: $NSGName) es una variable definida por el usuario de la secuencia de comandos en la sección de referencia de este documento):

1. En primer lugar debe generarse un grupo de seguridad de la red para mantener las reglas:

        New-AzureNetworkSecurityGroup -Name $NSGName `
            -Location $DeploymentLocation `
            -Label "Security group for $VNetName subnets in $DeploymentLocation"
 
2.  En este ejemplo la primera regla permitirá tráfico DNS entre todas las redes internas al servidor DNS en la subred back-end. La regla tiene algunos parámetros importantes:
  - "Tipo" indica en qué dirección de tráfico de esta regla aplicará; Esta es desde la perspectiva de la máquina Virtual o subred (en función de donde está enlazado esta GSN). Por lo tanto si tipo es "Entrada" y tráfico está escribiendo la subred, se aplicará la regla y no ¿se verá afectado el tráfico de salir de la subred por esta regla.
  - "Prioridad" establece el orden en que se evaluará un flujo de tráfico. Cuanto menor sea el número mayor es la prioridad. Tan pronto como una regla se aplica a un flujo de tráfico específico, más reglas no se procesan. Por tanto, si una regla con prioridad 1 permite el tráfico y una regla de prioridad 2 rechaza tráfico y ambas reglas se aplican al tráfico, a continuación, ¿permita el tráfico de flujo (como regla 1 tenía una mayor prioridad entra en vigor y se aplica ninguna regla más).
  - "Acción" indica si el tráfico afectado por esta regla se bloqueados o permitido.

            Get-AzureNetworkSecurityGroup -Name $NSGName | `
                Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
                -Type Inbound -Priority 100 -Action Allow `
                -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
                -DestinationAddressPrefix $VMIP[4] `
                -DestinationPortRange '53' `
                -Protocol *

3.  Esta regla permitirá RDP tráfico fluya desde internet al puerto RDP en cualquier servidor en cualquier subred en la VNET. Esta regla usa dos tipos especiales de prefijos de dirección; "VIRTUAL_NETWORK" y "INTERNET". Esta es una forma sencilla de una categoría más grande de prefijos de direcciones de direcciones.

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
            -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK `
            -DestinationPortRange '3389' `
            -Protocol *

4.  Esta regla permite el tráfico de internet alcanzar al servidor web. Esto no cambia el comportamiento de enrutamiento solo permite tráfico destinado IIS01 pasar. Por tanto, si el tráfico de Internet tenía el servidor web como el destino esta regla haría permitirlo y detener el procesamiento de más reglas. (En la regla en prioridad 140 todos los demás tráfico de internet entrante se bloquea). Si solo está procesando tráfico HTTP, esta regla se podría restringir aún más para permitir únicamente el puerto de destino 80.

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
            -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] `
            -DestinationPortRange '*' `
            -Protocol *

5.  Esta regla permite tráfico pase desde el servidor de IIS01 en el servidor de AppVM01 una regla posterior se bloqueará todas las demás front-end para el tráfico de back-end. Para mejorar esta regla, si se conoce el puerto que se deben agregar. Por ejemplo, si el servidor IIS alcance sólo SQL Server en AppVM01, el rango de puerto de destino debe cambiar de "*" (si hay alguna) 1433 (el puerto SQL), lo que permite una superficie de ataque entrante más pequeña en AppVM01 debe la aplicación web quede comprometida.

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
            -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
 
6.  Esta regla impide que el tráfico de internet a los servidores de la red. En combinación con la regla en prioridad 110 y 120, permite sólo el tráfico de internet entrante para el firewall y puertos RDP a otros servidores y bloques todo lo demás. 

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule `
            -Name "Isolate the $VNetName VNet from the Internet" `
            -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK `
            -DestinationPortRange '*' `
            -Protocol *
 
7.  La regla final rechaza el tráfico de la subred front-end a la subred back-end. Puesto que se trata de una regla sola de entrada, se permite el tráfico inverso (desde el servidor para el cliente).

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule `
            -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" `
            -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix `
            -DestinationPortRange '*' `
            -Protocol * 

## <a name="traffic-scenarios"></a>Escenarios de tráfico
#### <a name="allowed-web-to-web-server"></a>(*Permitido*) Servidor Web para Web
1.  Página HTTP de solicitudes de usuario de Internet de FrontEnd001.CloudApp.Net (servicio de nube opuestas de Internet)
2.  Servicio pasadas tráfico a través de un extremo abierto en el puerto 80 hacia IIS01 en la nube (el servidor web)
3.  Front-end subred comienza el procesamiento de la regla de entrada:
  1.    No aplicar, mover a la siguiente regla GSN regla 1 (DNS)
  2.    No aplicar, mover a la siguiente regla GSN regla 2 (RDP)
  3.    Aplicar GSN regla 3 (Internet a IIS01), el tráfico es procesamiento de la regla permitidos, detener
4.  Tráfico visitas interno dirección IP del servidor web IIS01 (10.0.1.5)
5.  Iis01 escucha el tráfico de web, recibe esta solicitud y se inicia el procesamiento de la solicitud
6.  Iis01 solicita a SQL Server en AppVM01 información
7.  No hay reglas de salida en subred front-end, el tráfico está permitido
8.  La subred de back-end comienza el procesamiento de la regla de entrada:
  1.    No aplicar, mover a la siguiente regla GSN regla 1 (DNS)
  2.    No aplicar, mover a la siguiente regla GSN regla 2 (RDP)
  3.    No aplicar, mover a la siguiente regla GSN regla 3 (Internet Firewall)
  4.    GSN regla 4 aplicar (IIS01 a AppVM01), se permite el tráfico, detener el procesamiento de la regla
9.  AppVM01 recibe la consulta SQL y responde
10. Puesto que no hay ninguna regla de salida de la subred back-end se permite la respuesta
11. Front-end subred comienza el procesamiento de la regla de entrada:
  1.    No hay ninguna regla GSN que se aplica a entrante tráfico de la subred back-end a la subred front-end, por lo que ninguna de la GSN aplican las reglas
  2.    La regla de sistema predeterminada permitir el tráfico entre subredes ¿permitir que este tráfico para que permita el tráfico.
12. El servidor IIS recibe la respuesta SQL y completa la respuesta HTTP y envía al solicitante
13. Porque hay no se permite reglas de salida de la subred front-end la respuesta y el usuario de Internet recibe la página web solicitado.

#### <a name="allowed-rdp-to-backend"></a>(*Permitido*) RDP a back-end
1.  Administrador de servidor en internet solicita sesión RDP AppVM01 en BackEnd001.CloudApp.Net:xxxxx donde xxxxx es el número de puerto asignado aleatoriamente para RDP a AppVM01 (el puerto asignado puede encontrar en el Portal de Azure o a través de PowerShell)
2.  Back-end subred comienza el procesamiento de la regla de entrada:
  1.    No aplicar, mover a la siguiente regla GSN regla 1 (DNS)
  2.    Aplicar GSN regla 2 (RDP), el tráfico es procesamiento de la regla permitidos, detener
3.  Con ninguna regla saliente, aplicarán reglas de forma predeterminada y se permite el tráfico de retorno
4.  Sesión RDP está habilitado
5.  AppVM01 solicita contraseña de nombre de usuario

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(*Permitido*) Búsqueda de servidor DNS de Web en el servidor DNS
1.  Web Server, IIS01, necesidades de datos de una fuente en www.data.gov, pero debe resolver la dirección.
2.  La configuración de red para las listas de VNet DNS01 (10.0.2.4 en la subred back-end) como el servidor DNS principal, IIS01 envía la solicitud DNS a DNS01
3.  No hay reglas de salida en subred front-end, el tráfico está permitido
4.  Back-end subred comienza el procesamiento de la regla de entrada:
  1.     Aplicar GSN regla 1 (DNS), el tráfico es procesamiento de la regla permitidos, detener
5.  Servidor DNS recibe la solicitud
6.  Servidor DNS no tiene la dirección en caché y se le pide a un servidor DNS raíz en internet
7.  Sin reglas de salida en subred back-end, el tráfico está permitido
8.  Servidor DNS de Internet responde, ya que esta sesión iniciada internamente, se permite la respuesta
9.  Servidor DNS almacena en caché la respuesta y responde a la solicitud inicial a IIS01
10. Sin reglas de salida en subred back-end, el tráfico está permitido
11. Front-end subred comienza el procesamiento de la regla de entrada:
  1.    No hay ninguna regla GSN que se aplica a entrante tráfico de la subred back-end a la subred front-end, por lo que ninguna de la GSN aplican las reglas
  2.    La regla de sistema predeterminada permitir el tráfico entre subredes ¿permitir que este tráfico para que permita el tráfico
12. Iis01 recibe la respuesta DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Permitido*) Archivo de access de servidor Web en AppVM01
1.  Solicita un archivo en AppVM01 iis01
2.  No hay reglas de salida en subred front-end, el tráfico está permitido
3.  La subred de back-end comienza el procesamiento de la regla de entrada:
  1.    No aplicar, mover a la siguiente regla GSN regla 1 (DNS)
  2.    No aplicar, mover a la siguiente regla GSN regla 2 (RDP)
  3.    No aplicar, mover a la siguiente regla GSN regla 3 (Internet a IIS01)
  4.    GSN regla 4 aplicar (IIS01 a AppVM01), se permite el tráfico, detener el procesamiento de la regla
4.  AppVM01 recibe la solicitud y responde con archivo (suponiendo que se permite el acceso)
5.  Puesto que no hay ninguna regla de salida de la subred back-end se permite la respuesta
6.  Front-end subred comienza el procesamiento de la regla de entrada:
  1.    No hay ninguna regla GSN que se aplica a entrante tráfico de la subred back-end a la subred front-end, por lo que ninguna de la GSN aplican las reglas
  2.    La regla de sistema predeterminada permitir el tráfico entre subredes ¿permitir que este tráfico para que permita el tráfico.
7.  El servidor IIS recibe el archivo

#### <a name="denied-web-to-backend-server"></a>(*Denegado*) Web a servidor back-end
1.  Usuario de Internet intenta obtener acceso a un archivo en AppVM01 a través del servicio de BackEnd001.CloudApp.Net
2.  Puesto que no hay ningún extremo abierto para el recurso compartido de archivos, esto no podría pasar por el servicio de nube y no puede conectar con el servidor
3.  Si los extremos estuvieran abiertos por algún motivo, regla GSN 5 (Internet a VNet) ¿bloquear este tráfico

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(*Denegado*) Búsqueda DNS de Web en el servidor DNS
1.  Usuario de Internet intenta buscar un registro DNS interno en DNS01 a través del servicio de BackEnd001.CloudApp.Net
2.  Puesto que no hay ningún extremo abierto para DNS, esto no podría pasar por el servicio de nube y no puede conectar con el servidor
3.  Si los extremos estuvieran abiertos por algún motivo, regla GSN 5 (Internet a VNet) ¿bloquear este tráfico (Nota: regla 1 (DNS) no se aplican por dos razones, primero la dirección de origen es internet, esta regla solo se aplica a la VNet local como origen, también esta es una regla de permiso, así que nunca debería Denegar tráfico)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(*Denegado*) Web Access SQL a través del Firewall
1.  Usuario de Internet solicita datos SQL de FrontEnd001.CloudApp.Net (servicio de nube opuestas de Internet)
2.  Puesto que no hay ningún extremo abierto para SQL, esto podría no pasar por el servicio de nube y no alcance el firewall
3.  Si extremos estuvieran abiertos por algún motivo, la subred front-end comienza el procesamiento de la regla de entrada:
  1.    No aplicar, mover a la siguiente regla GSN regla 1 (DNS)
  2.    No aplicar, mover a la siguiente regla GSN regla 2 (RDP)
  3.    Aplicar GSN regla 3 (Internet a IIS01), el tráfico es procesamiento de la regla permitidos, detener
4.  Tráfico llega a dirección IP interna de la IIS01 (10.0.1.5)
5.  Iis01 no está escuchando en el puerto 1433, así que no hay respuesta a la solicitud

## <a name="conclusion"></a>Conclusión
Esta es una forma relativamente simple y directa de aislar la subred back-end de tráfico entrante.

Más ejemplos y una descripción general de los límites de seguridad de red pueden encontrarse [aquí][HOME].

## <a name="references"></a>Referencias
### <a name="main-script-and-network-config"></a>Secuencia de comandos principal y configuración de red
Guardar la secuencia de comandos completa en un archivo de script de PowerShell. Guardar la configuración de red en un archivo denominado "NetworkConf1.xml".
Modifique las variables definidas por el usuario, según sea necesario. Ejecute la secuencia de comandos, a continuación, siga las instrucciones de configuración de regla de Firewall contenida en la sección 1 del ejemplo anterior.

#### <a name="full-script"></a>Secuencia de comandos completa
Esta secuencia de comandos que se mostrarán, en función de las variables definidas por el usuario;

1.  Conectarse a una suscripción de Azure
2.  Crear una nueva cuenta de almacenamiento
3.  Crear un nuevo VNet y dos subredes según se define en el archivo de configuración de red
4.  Crear máquinas virtuales de servidor de 4 windows
5.  Configurar GSN incluidos:
  - Crear un GSN
  - Rellenar con reglas
  - Enlazar el GSN a las subredes adecuadas

Esta secuencia de comandos de PowerShell debe ejecutarse localmente en conectado a internet PC o servidor.

>[AZURE.IMPORTANT] Cuando se ejecuta esta secuencia de comandos, puede haber advertencias u otros mensajes informativos que pop en PowerShell. Sólo mensajes de error en rojo son la causa de problema.


    <# 
     .SYNOPSIS
      Example of Network Security Groups in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - Network Security Groups to allow/deny traffic patterns as declared
      
      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).
    
     .Notes
      Security requirements are different for each use case and can be addressed in a
      myriad of ways. Please be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that can be used, but should not be used for all scenarios. You
      are responsible to assess your security needs and the appropriate protections
      needed, and then effectively implement those protections.
    
      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.5
     
      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6
    
    #>
    
    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()
    
    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section
    
      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    
      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"
    
      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"
    
      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf1.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
      
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 0
        #       - The AppVM1 Server must be VM 1
        #       - The DNS server must be VM 3
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.
    
        # VM 0 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"
    
        # VM 1 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 2 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 3 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #   

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop
    
      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}
    
      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop
    
    # Validation
    $FatalError = $false
    
    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}
    
    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}
    
    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}
    
    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}
    
    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop
    
    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
    
    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                Remove-AzureEndpoint -Name "PowerShell" | `
                New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Note: A Remote Desktop endpoint is automatically created when each VM is created.
            $i++
        }
        # Add HTTP Endpoint for IIS01
        Get-AzureVM -ServiceName $ServiceName[0] -Name $VMName[0] | Add-AzureEndpoint -Name HTTP -Protocol tcp -LocalPort 80 -PublicPort 80 | Update-AzureVM
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
        
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[3] -DestinationPortRange '53' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[0]) to $($VMName[1])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[0] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[1] -DestinationPortRange '*' `
            -Protocol *
        
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *
    
        # Assign the NSG to the Subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
    
    # Optional Post-script Manual Configuration
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
      

#### <a name="network-config-file"></a>Archivo de configuración de red
Guardar este archivo xml con ubicación actualizada y agregar el vínculo a este archivo a la variable $NetworkConfigFile en la secuencia de comandos anterior.
    
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Secuencias de comandos de aplicación de ejemplo
Si desea instalar una aplicación de ejemplo para este y otros ejemplos DMZ, se haya proporcionado uno en el siguiente vínculo: [Secuencia de comandos de aplicación][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "Entrada DMZ con GSN"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

