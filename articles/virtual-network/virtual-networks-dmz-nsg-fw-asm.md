<properties
   pageTitle="Ejemplo de DMZ: crear una DMZ para proteger aplicaciones con un Firewall y NSGs | Microsoft Azure"
   description="Crear una DMZ con un Firewall y grupos de seguridad de red (NSG)"
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

# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>Ejemplo 2: crear una DMZ para proteger aplicaciones con un Firewall y NSGs

[Volver a la página de prácticas recomendada de seguridad límite][HOME]

En este ejemplo se creará una DMZ con un firewall, servidores de cuatro ventanas y grupos de seguridad de red. También le guiará a través de cada uno de los comandos pertinentes para proporcionar un conocimiento más profundo de cada paso. También hay una sección de escenario de tráfico para proporcionar una detallada paso a paso cómo continúa el tráfico a través de los niveles de defensa en la DMZ. Por último, en las referencias de sección es el código completo e instrucciones para crear este entorno para probar y experimentar con diferentes escenarios. 

![Entrada DMZ con NVA y GSN][1]

## <a name="environment-description"></a>Descripción del entorno
En este ejemplo hay una suscripción que contiene lo siguiente:

- Dos servicios de nube: "FrontEnd001" y "BackEnd001"
- Una red Virtual "CorpNetwork", con dos subredes: "Cliente" y "Back-end"
- Un solo grupo de seguridad de red que se aplica a ambas subredes
- Un dispositivo virtual de red, en este ejemplo, un NextGen Firewall Barracuda, conectado a la subred front-end
- Un servidor de Windows que representa un servidor de aplicaciones web ("IIS01")
- Los servidores de dos ventanas que representan aplicación volver fin servidores ("AppVM01", "AppVM02")
- Un servidor de Windows que representa un servidor DNS ("DNS01")

>[AZURE.NOTE] Aunque este ejemplo usa un NextGen Firewall Barracuda, muchos de los dispositivos de red Virtual diferente podrían utilizarse en este ejemplo.

En la sección referencias a continuación hay un script de PowerShell que generará la mayoría del entorno descrito anteriormente. Crear las máquinas virtuales y redes virtuales, aunque haya terminado la secuencia de comandos de ejemplo, no se describen en detalle en este documento.
 
Para crear el entorno:

  1.    Guardar el archivo de xml de configuración de red incluido en la sección referencias (actualizado con IP, ubicación y nombres de direcciones para que coincida con el escenario determinado)
  2.    Actualizar las variables de usuario en la secuencia de comandos para que coincida con el entorno que es la secuencia de comandos se ejecute en (suscripciones, nombres de servicio, etcetera)
  3.    Ejecutar la secuencia de comandos de PowerShell

**Nota**: la región indicada en la secuencia de comandos de PowerShell debe coincidir con la región indicada en el archivo xml de configuración de red.

Una vez que la secuencia de comandos se ejecute correctamente, podrán tomarse los siguientes pasos posteriores a la secuencia de comandos:

1.  Configurar las reglas de firewall, se trata en la sección siguiente titulada: reglas de Firewall.
2.  Opcionalmente, en la sección referencias son dos secuencias de comandos para configurar el servidor web y el servidor de la aplicación con una aplicación web simple para permitir pruebas con esta configuración DMZ.

La siguiente sección explica la mayoría de las instrucciones de secuencias de comandos en relación con los grupos de seguridad de red.

## <a name="network-security-groups-nsg"></a>Grupos de seguridad de red (NSG)
En este ejemplo, un grupo de GSN está integrado y, a continuación, cargar con seis reglas. 

>[AZURE.TIP] Por lo general, debe crear las reglas de "Permitir" específicas en primer lugar y, a continuación, las reglas de "Denegar" más genéricas última. El indica prioridad asignada que son las reglas evalúa primero. Una vez que se encuentra el tráfico a aplicar a una regla específica, no se evalúan otras reglas. Pueden aplicar reglas de GSN en cualquiera en la dirección de entrada o salida (desde la perspectiva de la subred).

Mediante declaración, que se crean las siguientes reglas para el tráfico entrante:

1.  Se permite el tráfico DNS interno (puerto 53)
2.  Se permite el tráfico RDP (puerto 3389) desde Internet a cualquier VM
3.  Se permite el tráfico HTTP (puerto 80) desde Internet a la NVA (firewall)
4.  Se permite el tráfico (todos los puertos) desde IIS01 a AppVM1
5.  Cualquier (todos los puertos) el tráfico de Internet a toda la denegado VNet (ambas subredes)
6.  Se ha denegado el tráfico (todos los puertos) de la subred front-end a la subred back-end

Con estas reglas enlazado cada subred, si una solicitud HTTP era entrante desde Internet en el servidor web, ambas reglas 3 (permitir) y 5 (denegar) se aplican, pero como regla 3 tiene prioridad solo ésta se aplicará y regla 5 no debería tener en cuenta. Por lo tanto la solicitud HTTP podrán el firewall. Si ese mismo tráfico estaba intentando alcanzar el servidor DNS01, regla 5 (denegar) sería el primero en aplicar y no se permitirá el tráfico para pasar al servidor. Regla 6 (denegar) bloquea la subred front-end de hablar a la subred back-end (excepto el tráfico permitido en las reglas 1 y 4), esto protege la red de back-end en caso de un intruso compromete la aplicación web en el front-end, el intruso habría acceso limitado a la red back-end "protegida" (solo a los recursos que se exponen en el servidor de AppVM01).

Hay una regla de salida predeterminado que permite tráfico saliente a internet. En este ejemplo, estamos permitir el tráfico saliente y no modificar las reglas de salida. Para bloquear el tráfico en ambas direcciones, definidos por el enrutamiento de usuario es necesario, esto se explora en un ejemplo diferente que puede encontrar en el [documento del límite de seguridad principal][HOME].

Las reglas de GSN explicadas anteriores son muy similares a las reglas de GSN de [ejemplo 1: crear una DMZ sencilla con NSGs][Example1]. Revise la descripción GSN en ese documento para una vista detallada de cada regla GSN y sus atributos.

## <a name="firewall-rules"></a>Reglas de Firewall
Un cliente de administración tendrán que esté instalado en un equipo para administrar el firewall y crear las configuraciones que sea necesarias. Vea el proveedor de la documentación de su firewall (u otro NVA) acerca de cómo administrar el dispositivo. El resto de esta sección describe la configuración del servidor de seguridad, a través del cliente de administración de proveedores (es decir, no el portal de Azure o PowerShell).

Aquí puede encontrar instrucciones para la descarga de cliente y conectarse a la Barracuda usado en este ejemplo: [Administración de Barracuda NG](https://techlib.barracuda.com/NG61/NGAdmin)

En el firewall, las reglas de reenvío tendrán que debe crearse. Puesto que este ejemplo solo enruta el tráfico de internet en enlazado al servidor de seguridad y, a continuación, en el servidor web, es necesario reenvío solo una regla NAT. En el NextGen Firewall Barracuda usados en este ejemplo la regla sería una regla NAT de destino ("horario de verano NAT") para pasar este tráfico.

Para crear la siguiente regla (o comprobar reglas predeterminados existentes), empezando desde el panel de administración de Barracuda NG de cliente, vaya a la pestaña Configuración, en la configuración de operaciones sección, haga clic en el conjunto de reglas. Una cuadrícula denominada, "Principales reglas" Mostrar las reglas activas y desactivadas existentes en el firewall. En la esquina superior derecha de la cuadrícula es un pequeño verde "+" del botón, haga clic en esta opción para crear una nueva regla (Nota: el firewall esté "bloqueado" para los cambios, si ve un botón marcada como "Bloquear" y no puede crear o editar las reglas, haga clic en este botón para "desbloquear" el conjunto de reglas y permitir la edición). Si desea editar una regla existente, seleccione esa regla, haga clic en y seleccione Editar regla.

Crear una nueva regla y proporcione un nombre, como "WebTraffic". 

El icono de la regla de destino NAT es similar a esta: ![Icono NAT de destino][2]

La propia regla tendría un aspecto similar al siguiente:

![Regla de Firewall][3]

Aquí cualquier entrada dirección que visita el Firewall intentando alcanzar HTTP (puerto 80 o 443 para HTTPS) se enviarán fuera de la interfaz de "IP Local DHCP1" del Firewall y se redirige al servidor Web con la dirección IP de 10.0.1.5. Puesto que el tráfico es provengan del puerto 80 y el servidor web en el puerto 80 no se necesita ningún cambio de puerto. Sin embargo, la lista de objetivo podría haber sido 10.0.1.5:8080 si el servidor Web escucha en el puerto 8080, por tanto, traducir el puerto de entrada 80 en el servidor de seguridad al puerto entrante 8080 en el servidor web.

Un método de conexión debe también se indicado, para la regla de destino de Internet, "Dinámico SNAT" es más apropiado. 

Aunque se ha creado una solo regla es importante que su prioridad esté configurada correctamente. Si en la cuadrícula de todas las reglas de firewall de que esta nueva regla está en la parte inferior (debajo de la regla "BLOCKALL") nunca deben tener en cuenta. Asegúrese de que la regla recién creada para el tráfico web está por encima de la regla BLOCKALL.

Una vez que se creó la regla, debe ser inserta el firewall y, a continuación, activa, si no lo hace la regla no surtirá efecto. El proceso de inserción y la activación se describe en la sección siguiente.

## <a name="rule-activation"></a>Activación de regla
Con el conjunto de reglas modificado para agregar esta regla, el conjunto de reglas se debe cargarse en el firewall y activado.

![Activación de la regla de Firewall][4]

En la esquina superior derecha del cliente de administración son un clúster de botones. Haga clic en el botón "Enviar cambios" para enviar las reglas modificadas el firewall y luego haga clic en el botón "Activar".

Con la activación del conjunto de reglas de firewall esta versión de compilación del entorno de ejemplo está completa. Opcionalmente, se pueden ejecutar las secuencias de comandos de compilación de entrada en la sección referencias para agregar una aplicación a este entorno para probar los escenarios de tráfico siguientes.

>[AZURE.IMPORTANT] Es fundamental tener en cuenta que no se alcanzado al servidor web directamente. Cuando un explorador solicita una página HTTP FrontEnd001.CloudApp.Net, el extremo HTTP (puerto 80) pasa este tráfico al cortafuegos no en el servidor web. El firewall, a continuación, según la regla de crear, NAT que solicitan al servidor Web.

## <a name="traffic-scenarios"></a>Escenarios de tráfico

#### <a name="allowed-web-to-web-server-through-firewall"></a>(Permitido) Servidor Web en Web a través del Firewall
1.  Página HTTP de solicitudes de usuario de Internet de FrontEnd001.CloudApp.Net (servicio de nube opuestas de Internet)
2.  Pasadas de tráfico a través de un extremo abierto en el puerto 80 a interfaz local de firewall 10.0.1.4:80 del servicio de nube
3.  Front-end subred comienza el procesamiento de la regla de entrada:
  1.    No aplicar, mover a la siguiente regla GSN regla 1 (DNS)
  2.    No aplicar, mover a la siguiente regla GSN regla 2 (RDP)
  3.    Aplicar GSN regla 3 (Internet Firewall), el tráfico es procesamiento de la regla permitidos, detener
4.  Tráfico llega a dirección IP interna del servidor de seguridad (10.0.1.4)
5.  Regla de reenvío de Firewall vea esto es el tráfico del puerto 80, se redirige al servidor web IIS01
6.  Iis01 escucha el tráfico de web, recibe esta solicitud y se inicia el procesamiento de la solicitud
7.  Iis01 solicita a SQL Server en AppVM01 información
8.  No hay reglas de salida en subred front-end, el tráfico está permitido
9.  La subred de back-end comienza el procesamiento de la regla de entrada:
  1.    No aplicar, mover a la siguiente regla GSN regla 1 (DNS)
  2.    No aplicar, mover a la siguiente regla GSN regla 2 (RDP)
  3.    No aplicar, mover a la siguiente regla GSN regla 3 (Internet Firewall)
  4.    GSN regla 4 aplicar (IIS01 a AppVM01), se permite el tráfico, detener el procesamiento de la regla
10. AppVM01 recibe la consulta SQL y responde
11. Puesto que no hay ninguna regla de salida de la subred back-end se permite la respuesta
12. Front-end subred comienza el procesamiento de la regla de entrada:
  1.    No hay ninguna regla GSN que se aplica a entrante tráfico de la subred back-end a la subred front-end, por lo que ninguna de la GSN aplican las reglas
  2.    La regla de sistema predeterminada permitir el tráfico entre subredes ¿permitir que este tráfico para que permita el tráfico.
13. El servidor IIS recibe la respuesta SQL y completa la respuesta HTTP y envía al solicitante
14. Puesto que se trata de una sesión NAT desde el firewall, el destino de respuesta (inicialmente) es para el Firewall
15. El firewall recibe la respuesta del servidor Web y reenvía al usuario de Internet
16. Porque hay no se permite reglas de salida de la subred front-end la respuesta y el usuario de Internet recibe la página web solicitado.

#### <a name="allowed-rdp-to-backend"></a>(Permitido) RDP a back-end
1.  Administrador de servidor en internet solicita sesión RDP AppVM01 en BackEnd001.CloudApp.Net:xxxxx donde xxxxx es el número de puerto asignado aleatoriamente para RDP a AppVM01 (el puerto asignado puede encontrar en el Portal de Azure o a través de PowerShell)
2.  Dado que el Firewall sólo está escuchando en la dirección FrontEnd001.CloudApp.Net, no participa con este flujo de tráfico
3.  Back-end subred comienza el procesamiento de la regla de entrada:
  1.    No aplicar, mover a la siguiente regla GSN regla 1 (DNS)
  2.    Aplicar GSN regla 2 (RDP), el tráfico es procesamiento de la regla permitidos, detener
4.  Con ninguna regla saliente, aplicarán reglas de forma predeterminada y se permite el tráfico de retorno
5.  Sesión RDP está habilitado
6.  AppVM01 solicita contraseña de nombre de usuario

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Permitido) Búsqueda de servidor DNS de Web en el servidor DNS
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

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(Permitido) Archivo de access de servidor Web en AppVM01
1.  Solicita un archivo en AppVM01 iis01
2.  No hay reglas de salida en subred front-end, el tráfico está permitido
3.  La subred de back-end comienza el procesamiento de la regla de entrada:
  1.    No aplicar, mover a la siguiente regla GSN regla 1 (DNS)
  2.    No aplicar, mover a la siguiente regla GSN regla 2 (RDP)
  3.    No aplicar, mover a la siguiente regla GSN regla 3 (Internet Firewall)
  4.    GSN regla 4 aplicar (IIS01 a AppVM01), se permite el tráfico, detener el procesamiento de la regla
4.  AppVM01 recibe la solicitud y responde con archivo (suponiendo que se permite el acceso)
5.  Puesto que no hay ninguna regla de salida de la subred back-end se permite la respuesta
6.  Front-end subred comienza el procesamiento de la regla de entrada:
  1.    No hay ninguna regla GSN que se aplica a entrante tráfico de la subred back-end a la subred front-end, por lo que ninguna de la GSN aplican las reglas
  2.    La regla de sistema predeterminada permitir el tráfico entre subredes ¿permitir que este tráfico para que permita el tráfico.
7.  El servidor IIS recibe el archivo

#### <a name="denied-web-direct-to-web-server"></a>(Denegado) Web directa a servidor Web
Dado que el servidor Web, IIS01 y el Firewall en el mismo servicio de nube comparten la misma dirección IP hacia pública. Por tanto, el tráfico HTTP se dirigirá al firewall. Aunque la solicitud podría representarse correctamente, no puede ir directamente al servidor Web, que pasa por diseño, a través del Firewall en primer lugar. Vea el primer escenario de esta sección para el flujo del tráfico.

#### <a name="denied-web-to-backend-server"></a>(Denegado) Web a servidor back-end
1.  Usuario de Internet intenta obtener acceso a un archivo en AppVM01 a través del servicio de BackEnd001.CloudApp.Net
2.  Puesto que no hay ningún extremo abierto para el recurso compartido de archivos, esto no podría pasar por el servicio de nube y no puede conectar con el servidor
3.  Si los extremos estuvieran abiertos por algún motivo, regla GSN 5 (Internet a VNet) ¿bloquear este tráfico

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(Denegado) Búsqueda DNS de Web en el servidor DNS
1.  Usuario de Internet intenta buscar un registro DNS interno en DNS01 a través del servicio de BackEnd001.CloudApp.Net
2.  Puesto que no hay ningún extremo abierto para DNS, esto no podría pasar por el servicio de nube y no puede conectar con el servidor
3.  Si los extremos estuvieran abiertos por algún motivo, regla GSN 5 (Internet a VNet) ¿bloquear este tráfico (Nota: regla 1 (DNS) no se aplican por dos razones, primero la dirección de origen es internet, esta regla solo se aplica a la VNet local como origen, también esta es una regla de permiso, así que nunca debería Denegar tráfico)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(Denegado) Web Access SQL a través del Firewall
1.  Usuario de Internet solicita datos SQL de FrontEnd001.CloudApp.Net (servicio de nube opuestas de Internet)
2.  Puesto que no hay ningún extremo abierto para SQL, esto podría no pasar por el servicio de nube y no alcance el firewall
3.  Si extremos estuvieran abiertos por algún motivo, la subred front-end comienza el procesamiento de la regla de entrada:
  1.    No aplicar, mover a la siguiente regla GSN regla 1 (DNS)
  2.    No aplicar, mover a la siguiente regla GSN regla 2 (RDP)
  3.    Aplicar GSN regla 2 (Internet Firewall), el tráfico es procesamiento de la regla permitidos, detener
4.  Tráfico llega a dirección IP interna del servidor de seguridad (10.0.1.4)
5.  Firewall no tiene ninguna regla de reenvío para SQL y descarta el tráfico

## <a name="conclusion"></a>Conclusión
Esta es una manera relativamente directa de protección de la aplicación con un firewall y aislar la subred back-end de tráfico entrante.

Más ejemplos y una descripción general de los límites de seguridad de red pueden encontrarse [aquí][HOME].

## <a name="references"></a>Referencias
### <a name="main-script-and-network-config"></a>Secuencia de comandos principal y configuración de red
Guardar la secuencia de comandos completa en un archivo de script de PowerShell. Guardar la configuración de red en un archivo denominado "NetworkConf2.xml".
Modifique las variables definidas por el usuario, según sea necesario. Ejecute la secuencia de comandos, a continuación, siga las instrucciones de configuración de regla de Firewall anteriores.

#### <a name="full-script"></a>Secuencia de comandos completa
Podrá esta secuencia de comandos, en función de las variables definidas por el usuario:

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
      Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
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
       myFirewall - 10.0.1.4
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
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
    
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 1
        #       - The AppVM1 Server must be VM 2
        #       - The DNS server must be VM 4
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.
    
        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"
    
        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"
    
        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 4 - The DNS Server
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
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
        
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
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
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
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
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Entrada DMZ con GSN"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Icono NAT de destino"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Regla de Firewall"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Activación de la regla de Firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
