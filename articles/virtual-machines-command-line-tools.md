<properties
    pageTitle="Los comandos CLI Azure en modo de administración de servicios | Microsoft Azure"
    description="Comandos de Azure comando línea interfaz en modo de administración del servicio para administrar implementaciones en el modelo de implementación clásica"
    services="virtual-machines-linux,virtual-machines-windows,mobile-services, cloud-services"
    documentationCenter=""
    authors="dlepow"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="danlep"/>

# <a name="azure-cli-commands-in-azure-service-management-asm-mode"></a>Comandos CLI Azure en modo de administración de servicios de Azure (asm)

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-classic-include.md)]También puede [Leer acerca de los comandos de modelo de administrador de recursos](virtual-machines/azure-cli-arm-commands.md)y utilizar la CLI para [migrar recursos](virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md) desde el clásico al modelo de administrador de recursos.

Este artículo proporciona la sintaxis y las opciones para los comandos de Azure CLI que normalmente se utiliza para crear y administrar recursos de Azure en el modelo de implementación clásico. Obtener acceso a estos comandos ejecuta la CLI en modo de administración de servicios de Azure (asm). No es una referencia completa y su versión CLI puede mostrar comandos ligeramente diferentes o parámetros. 

Para comenzar, primera [instalar CLI Azure](xplat-cli-install.md) y [conectarse a su suscripción de Azure](xplat-cli-connect.md).

La sintaxis de comando actual y opciones en la línea de comandos, escriba `azure help` o, para mostrar la ayuda para un comando específico, `azure help [command]`. También encontrará ejemplos CLI en la documentación para crear y administrar los servicios de Azure específicos.

Parámetros opcionales se muestran entre corchetes (por ejemplo, `[parameter]`). Todos los demás parámetros son necesarios.

Además de los parámetros opcionales específicos del comando documentados aquí, hay tres parámetros opcionales que se pueden usar para mostrar el resultado detallado como opciones de convocatoria y códigos de estado. La `-v` parámetro proporciona un resultado detallado y la `-vv` parámetro proporciona un resultado detallado incluso más detallado. La `--json` opción genera el resultado en formato json sin formato.

## <a name="setting-asm-mode"></a>Establecer el modo de asm

Use el comando siguiente para habilitar los comandos de modo de administración de servicios de Azure CLI.

    azure config mode asm

>[AZURE.NOTE] Modo de administrador de recursos de Azure y administración de servicios de Azure la CLI son mutuamente. Es decir, no se puede administrar recursos creados en un modo de otro modo.

## <a name="manage-your-account-information-and-publish-settings"></a>Administrar la información de cuenta y configuración de publicación
Una forma que CLI puede conectarse a su cuenta es mediante la información de su suscripción de Azure. (Consulte [conexión a una suscripción de Azure desde la CLI Azure](xplat-cli-connect.md) para ver otras opciones). Puede obtener desde el portal de Azure clásico en un archivo de configuración de publicación, como se describe a continuación. Puede importar el archivo de configuración de la publicación como persistente local configuración que CLI se usa para las siguientes operaciones. Solo debe importar la configuración de publicación de una vez.

**descarga de cuenta [opciones]**

Este comando inicia un explorador para descargar el archivo .publishsettings desde el portal de clásico de Azure.

    ~$ azure account download
    info:   Executing command account download
    info:   Launching browser to https://windows.azure.com/download/publishprofile.aspx
    help:   Save the downloaded file, then execute the command
    help:   account import <file>
    info:   account download command OK

**la cuenta de importación [opciones] &lt;archivo >**


Este comando importa un archivo de publishsettings o un certificado para que se puede usar la herramienta futuros.

    ~$ azure account import publishsettings.publishsettings
    info:   Importing publish settings file publishsettings.publishsettings
    info:   Found subscription: 3-Month Free Trial
    info:   Found subscription: Pay-As-You-Go
    info:   Setting default subscription to: 3-Month Free Trial
    warn:   The 'publishsettings.publishsettings' file contains sensitive information.
    warn:   Remember to delete it now that it has been imported.
    info:   Account publish settings imported successfully

> [AZURE.NOTE] El archivo publishsettings puede contener detalles (es decir, el nombre de suscripción y el identificador) sobre más de una suscripción. Al importar el archivo publishsettings, la primera suscripción se usa como la descripción predeterminada. Para usar una suscripción diferente, ejecute el siguiente comando:
<code>~$ azure config set subscription &lt;other-subscription-id&gt;</code>

**cuenta desactive [opciones]**

Este comando quita la publishsettings almacenado en el que se han importado. Utilice este comando si terminado con la herramienta en este equipo y desea asegurarse de que la herramienta no se pueden usar con su cuenta en futuras sesiones.

    ~$ azure account clear
    Clearing account info.
    info:   OK

**lista de cuentas [opciones]**

Lista de las suscripciones importadas

    ~$ azure account list
    info:    Executing command account list
    data:    Name                                    Id
           Current
    data:    --------------------------------------  -------------------------------
    -----  -------
    data:    Forums Subscription                     8679c8be-3b05-49d9-b8fb  true
    data:    Evangelism Team Subscription            9e672699-1055-41ae-9c36  false
    data:    MSOpenTech-Prod                         c13e6a92-706e-4cf5-94b6  false

**cuenta configurado [opciones] &lt;suscripción&gt;**

Establecer la suscripción actual

###<a name="commands-to-manage-your-affinity-groups"></a>Comandos para administrar los grupos de afinidad

**lista de grupo afinidad cuenta [opciones]**

Este comando enumera los grupos de afinidad Azure.

Puede establecer grupos de afinidad cuando un grupo de máquinas virtuales abarca varios equipos físicos. El grupo afinidad especifica que los equipos físicos deben ser cerca entre sí como sea posible, para reducir la latencia de red.

    ~$ azure account affinity-group list
    + Fetching affinity groups
    data:   Name                                  Label   Location
    data:   ------------------------------------  ------  --------
    data:   535EBAED-BF8B-4B18-A2E9-8755FB9D733F  opentec  West US
    info:   account affinity-group list command OK

**grupo de cuenta afinidad crear [opciones] &lt;nombre&gt;**

Este comando crea un grupo de afinidad

    ~$ azure account affinity-group create opentec -l "West US"
    info:    Executing command account affinity-group create
    + Creating affinity group
    info:    account affinity-group create command OK

**grupo de cuenta afinidad mostrar [opciones] &lt;nombre&gt;**

Este comando muestra los detalles del grupo afinidad

    ~$ azure account affinity-group show opentec
    info:    Executing command account affinity-group show
    + Getting affinity groups
    data:    $ xmlns "http://schemas.microsoft.com/windowsazure"
    data:    $ xmlns:i "http://www.w3.org/2001/XMLSchema-instance"
    data:    Name "opentec"
    data:    Label "b3BlbnRlYw=="
    data:    Description $ i:nil "true"
    data:    Location "West US"
    data:    HostedServices ""
    data:    StorageServices ""
    data:    Capabilities Capability 0 "PersistentVMRole"
    data:    Capabilities Capability 1 "HighMemory"
    info:    account affinity-group show command OK

**cuenta de grupo de afinidad eliminar [opciones] &lt;nombre&gt;**

Este comando elimina el grupo de afinidad especificado

    ~$ azure account affinity-group delete opentec
    info:    Executing command account affinity-group delete
    Delete affinity group opentec? [y/n] y
    + Deleting affinity group
    info:    account affinity-group delete command OK

###<a name="commands-to-manage-your-account-environment"></a>Comandos para administrar su entorno de cuenta

**lista de cuentas de ent [opciones]**

Lista de los entornos de cuenta

    C:\windows\system32>azure account env list
    info:    Executing command account env list
    data:    Name
    data:    ---------------
    data:    AzureCloud
    data:    AzureChinaCloud
    info:    account env list command OK

**cuenta ent mostrar [opciones] [entorno]**

Mostrar detalles del entorno de cuenta

    ~$ azure account env show
    info:    Executing command account env show
    Environment name: AzureCloud
    data:    Environment publishingProfile  http://go.microsoft.com/fwlink/?LinkId=2544
    data:    Environment portal  http://go.microsoft.com/fwlink/?LinkId=2544
    info:    account env show command OK

**cuenta ent agregar [opciones] [entorno]**

Este comando agrega un entorno a la cuenta

**cuenta ent establece [opciones] [entorno]**

Este comando establece el entorno de cuenta

**cuenta ent eliminar [opciones] [entorno]**

Este comando elimina el entorno especificado de la cuenta

## <a name="commands-to-manage-your-classic-virtual-machines"></a>Comandos para administrar su máquinas virtuales clásicas
El siguiente diagrama muestra cómo clásica Azure máquinas virtuales hospedadas en el entorno de producción de implementación de un servicio de nube de Azure.

![Diagrama técnica de Azure](./media/virtual-machines-command-line-tools/architecturediagram.jpg)

**Crear nuevo** crea la unidad de almacenamiento de blobs (es decir, e:\ en el diagrama); **adjuntar** adjunta un disco ya creado pero no conectado a una máquina virtual.

**VM crear [opciones] &lt;nombre dns > &lt;imagen > &lt;nombre de usuario > [contraseña]**

Este comando crea una máquina virtual Azure. De forma predeterminada, cada máquina virtual (vm) se crea en su propio servicio de nube. Puede especificar que una máquina virtual debe agregarse a un servicio de nube existente mediante el uso de la opción - c tal como se describe a continuación.

La máquina virtual de crear el comando, como el portal clásico Azure, solo crea máquinas virtuales en el entorno de implementación de producción. No hay ninguna opción para crear una máquina virtual en el entorno de implementación de ensayo de un servicio de nube. Si su suscripción no tiene una cuenta de almacenamiento de Azure existente, el comando crea uno.

Puede especificar una ubicación mediante el--parámetro de ubicación, o puede especificar un grupo de afinidad a través del--parámetro grupo afinidad. Si no se proporciona, se le pide que proporcione una de la lista de ubicaciones válidas.

La contraseña proporcionada debe tener 8 123 caracteres y cumple los requisitos de complejidad de la contraseña del sistema operativo que esté utilizando para este equipo virtual.

Si va a usar SSH para administrar un equipo virtual Linux de implementada (como suele ser el caso), debe habilitar SSH a través de la opción -e cuando se crea la máquina virtual. No es posible habilitar SSH después de que se ha creado la máquina virtual.

Máquinas virtuales de Windows puede habilitar RDP más adelante agregando el puerto 3389 como un extremo.

Se admiten los siguientes parámetros opcionales para este comando:

**- c,--conectar** crear la máquina virtual dentro de una implementación ya se ha creado en un servicio de hospedaje. Si no se utiliza - vmname con esta opción, se genera automáticamente el nombre de la nueva máquina virtual.<br />
**-n, nombre de la máquina virtual:** Especifique el nombre de la máquina virtual. Este parámetro toma el nombre de servicio de hospedaje de forma predeterminada. Si no se especifica - vmname, se genera el nombre de la nueva máquina virtual como &lt;nombre de servicio >&lt;id >, donde &lt;id > es el número de máquinas virtuales existentes en el servicio más 1. Por ejemplo, si utiliza este comando para agregar una máquina virtual a un servicio de hospedaje MyService que tiene una máquina virtual existente, la nueva máquina virtual se denomina MyService2.<br />
**-u,--url blob** Especifique la dirección URL almacenamiento de blobs de destino en el que se va a crear el disco de sistema de máquina virtual. <br />
**a la z, tamaño de la máquina virtual:** Especificar el tamaño de la máquina virtual. Los valores válidos son: "ExtraSmall", "Pequeña", "Medium", "Grandes", "ExtraLarge", "A5", "A6", "A7", "A8", "A9", "A10", "A11", "Basic_A0", "Basic_A1", "Basic_A2", "Basic_A3", "Basic_A4", "Standard_D1", "Standard_D2", "Standard_D3", "Standard_D4", "Standard_D11", "Standard_D12", "Standard_D13", "Standard_D14", "Standard_DS1", "Standard_DS2", "Standard_DS3", "Standard_DS4", "Standard_DS11", "Standard_DS12", "Standard_DS13", "Standard_DS14", "Standard_G1", "Standard_G2" , "Standard_G3", "Standard_G4", "Standard_G55". El valor predeterminado es "Pequeño". <br />
**-r** Conectividad RDP se agrega a una máquina virtual de Windows. <br />
**-e,--ssh** Conectividad SSH se agrega a una máquina virtual de Windows. <br />
**-t,--ssh certificado** Especifica el certificado SSH. <br />
**-s** La suscripción <br />
**-o,--Comunidad** La imagen especificada es una imagen de la Comunidad <br />
**-w** El nombre de red virtual <br/>
**-l,--ubicación** especifica la ubicación (por ejemplo, "Norte Central de EE"). <br />
**-a,--afinidad grupo** especifica el grupo de afinidad.<br />
**-w,--nombre de red virtual** Especifique la red virtual en la que desea agregar la nueva máquina virtual. Redes virtuales pueden configurar y se administra desde el portal de clásico de Azure.<br />
**-b,--nombres de subred** Especifica los nombres de subred para asignar la máquina virtual.

En este ejemplo, MSFT__Win2K8R2SP1-120514-1520-141205-01-en-us-30GB es una imagen proporcionada por la plataforma. Para obtener más información acerca de las imágenes de sistema operativo, consulte la lista de imágenes de máquina virtual.

    ~$ azure vm create my-vm-name MSFT__Windows-Server-2008-R2-SP1.11-29-2011 username --location "West US" -r
    info:   Executing command vm create
    Enter VM 'my-vm-name' password: ************
    info:   vm create command OK

**crear desde VM &lt;nombre dns > &lt;rol archivo >**

Este comando crea una máquina virtual Azure desde un archivo de rol JSON.

    ~$ azure vm create-from my-vm example.json
    info:   OK

**lista de VM [opciones]**

Este comando enumera máquinas virtuales de Windows Azure. La opción de json--especifica que los resultados se devuelven en formato JSON.

    ~$ azure vm list
    info:   Executing command vm list
    data:   DNS Name                          VM Name      Status
    data:   --------------------------------  -----------  ---------
    data:   my-vm-name.cloudapp-preview.net        my-vm        ReadyRole
    info:   vm list command OK

**lista de ubicación de la máquina virtual [opciones]**

Este comando enumera todas las ubicaciones de la cuenta de Azure disponibles.

    ~$ azure vm location list
    info:   Executing command vm location list
    data:   Name                   Display Name
    data:   ---------------------  ------------
    data:   Azure Preview  West US
    info:   account location list command OK

**VM mostrar [opciones] &lt;nombre >**

Este comando muestra detalles sobre una máquina virtual Azure. La opción de json--especifica que los resultados se devuelven en formato JSON.

    ~$ azure vm show my-vm
    info:   Executing command vm show
    data:   {
    data:       InstanceSize: 'Small',
    data:       InstanceStatus: 'ReadyRole',
    data:       DataDisks: [],
    data:       IPAddress: '10.26.192.206',
    data:       DNSName: 'my-vm.cloudapp.net',
    data:       InstanceStateDetails: {},
    data:       VMName: 'my-vm',
    data:       Network: {
    data:           Endpoints: [
    data:               {
    data:                   Protocol: 'tcp',
    data:                   Vip: '65.52.250.250',
    data:                   Port: '63238' ,
    data:                   LocalPort: '3389',
    data:                   Name: 'RemoteDesktop'
    data:               }
    data:           ]
    data:       },
    data:       Image: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
    data:       OSVersion: 'WA-GUEST-OS-1.18_201203-01'
    data:   }
    info:   vm show command OK

**VM eliminar [opciones] &lt;nombre >**

Este comando elimina una máquina virtual Azure. De forma predeterminada, este comando no elimina la blobs de Windows Azure desde la que se crean el disco de sistema operativo y el disco de datos. Para eliminar el objeto binario y la máquina virtual en que se basa, especifique la opción -b.

    ~$ azure vm delete my-vm
    info:   Executing command vm delete
    info:   vm delete command OK

**VM inicio [opciones] &lt;nombre >**

Este comando inicia una máquina virtual Azure.

    ~$ azure vm start my-vm
    info:   Executing command vm start
    info:   vm start command OK

**VM reinicie [opciones] &lt;nombre >**

Este comando reinicia una máquina virtual Azure.

    ~$ azure vm restart my-vm
    info:   Executing command vm restart
    info:   vm restart command OK

**Cierre VM [opciones] &lt;nombre >**

Este comando se cierra una máquina virtual Azure. Puede usar la opción -p para especificar que el recurso de cálculo no se publican en apagado.

```
~$ azure vm shutdown my-vm
info:   Executing command vm shutdown
info:   vm shutdown command OK  
```

**captura de VM &lt;nombre de vm > &lt;nombre de imagen de destino >**

Este comando captura una imagen de Azure máquina virtual.

Solo se puede capturar una imagen de máquina virtual si el estado de la máquina virtual es **su interrupción**. Apagar la máquina virtual antes de continuar.

    ~$ azure.cmd vm capture my-vm mycaptureimagename --delete
    info:   Executing command vm capture
    + Fetching VMs
    + Capturing VM
    info:   vm capture command OK

**exportación de VM [opciones] &lt;nombre de vm > &lt;ruta de acceso de archivo >**

Este comando exporta una imagen de Azure máquina virtual a un archivo

    ~$ azure vm export "myvm" "C:\"
    info:    Executing command vm export
    + Getting virtual machines
    + Exporting the VM
    info:   vm export command OK

##  <a name="commands-to-manage-your-azure-virtual-machine-endpoints"></a>Comandos para administrar los extremos de Azure máquina virtual
El siguiente diagrama muestra la arquitectura de una implementación típica de varias instancias de una máquina virtual clásica. En este ejemplo, el puerto 3389 está abierto en cada máquina virtual (para el acceso RDP). También es una dirección IP interna (por ejemplo, 168.55.11.1) en cada máquina virtual usado por el equilibrador de carga para enrutar el tráfico a la máquina virtual. Esta dirección IP interna también puede utilizarse para la comunicación entre máquinas virtuales de Windows.

![azurenetworkdiagram](./media/virtual-machines-command-line-tools/networkdiagram.jpg)

Las solicitudes externas a máquinas virtuales vayan a través de un equilibrador de carga. Por este motivo, las solicitudes no se pueden especificar en un equipo virtual específico en implementaciones con varios equipos virtuales. Para implementaciones con varios equipos virtuales, asignación de puertos debe estar configurado entre las máquinas virtuales (vm puerto) y el equilibrador de carga (puerto de libras).

**crear el extremo de VM &lt;nombre de vm > &lt;libras puerto > [vm puerto]**

Este comando crea un extremo de la máquina virtual. También puede usar -u o--habilitar directos servidor retorno para especificar si habilitar servidor directo devolver en este extremo, deshabilitada de forma predeterminada.

    ~$ azure vm endpoint create my-vm 8888 8888
    azure vm endpoint create my-vm 8888 8888
    info:   Executing command vm endpoint create
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint create command OK

**vm extremo crear múltiples [opciones] &lt;nombre de vm > &lt;libras puerto > [:&lt;puerto vm > [:&lt;protocolo > [:&lt;habilitar directos servidor retorno > [:&lt;nombre del conjunto de libras > [:&lt;protocolo de sondeo > [:&lt;puerto de sondeo > [:&lt;ruta de acceso de sondeo > [:&lt;nombre interno libras >]]] {1 -*}**

Crear múltiples extremos de máquina virtual.

**extremo de VM eliminar [opciones] &lt;nombre de vm > &lt;nombre del extremo >**

Este comando elimina un extremo de la máquina virtual.

    ~$ azure vm endpoint delete my-vm http
    azure vm endpoint delete my-vm http
    info:   Executing command vm endpoint delete
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint delete command OK

**lista de extremos de VM &lt;nombre de vm >**

Este comando enumera todos los extremos de la máquina virtual. La opción de json--especifica que los resultados se devuelven en formato JSON.

    ~$ azure vm endpoint list my-linux-vm
    data:   Name  External Port  Local Port
    data:   ----  -------------  ----------
    data:   ssh   22             22

**actualización de extremo de VM [opciones] &lt;nombre de vm > &lt;nombre del extremo >**

Este comando actualiza un extremo de vm a nuevos valores con estas opciones.

    -n, --endpoint-name <name>          the new endpoint name
    -lo, --lb-port <port>                the new load balancer port
    -t, --vm-port <port>                the new local port
    -o, --endpoint-protocol <protocol>  the new transport layer protocol for port (tcp or udp)

**extremo de VM mostrar [opciones] &lt;nombre de vm >**

Este comando muestra los detalles de los extremos en una máquina virtual

    ~$ azure vm endpoint show "mycouchvm"
    info:    Executing command vm endpoint show
    + Getting virtual machines
    data:    Network Endpoints 0 LoadBalancedEndpointSetName "CouchDB_EP-5984"
    data:    Network Endpoints 0 LocalPort "5984"
    data:    Network Endpoints 0 Name "CouchDB_EP"
    data:    Network Endpoints 0 Port "5984"
    data:    Network Endpoints 0 Protocol "tcp"
    data:    Network Endpoints 0 Vip "168.61.9.97"
    data:    Network Endpoints 1 LoadBalancedEndpointSetName "CouchEP_2-2020"
    data:    Network Endpoints 1 LocalPort "2020"
    data:    Network Endpoints 1 Name "CouchEP_2"
    data:    Network Endpoints 1 Port "2020"
    data:    Network Endpoints 1 Protocol "tcp"
    data:    Network Endpoints 1 Vip "168.61.9.97"
    data:    Network Endpoints 2 LocalPort "3389"
    data:    Network Endpoints 2 Name "RemoteDesktop"
    data:    Network Endpoints 2 Port "3389"
    data:    Network Endpoints 2 Protocol "tcp"
    data:    Network Endpoints 2 Vip "168.61.9.97"
    info:    vm endpoint show command OK

## <a name="commands-to-manage-your-azure-virtual-machine-images"></a>Comandos para administrar las imágenes de Azure máquina virtual

Imágenes de máquina virtual están capturas de ya configurados máquinas virtuales que se pueden replicar según sea necesario.

**lista de imágenes de máquina virtual [opciones]**

Este comando obtiene una lista de imágenes de máquina virtual. Existen tres tipos de imágenes: imágenes creadas por Microsoft, que tienen el prefijo con "MSFT", imágenes creadas por terceros, que tienen el prefijo con el nombre del proveedor y las imágenes cree. Para crear imágenes, puede capturar una máquina virtual existente o crear una imagen desde un .vhd personalizado cargado al almacenamiento de blobs de Windows. Para obtener más información sobre el uso de un .vhd personalizados, vea imagen vm crear.
La opción de json--especifica que los resultados se devuelven en formato JSON.

    ~$ azure vm image list
    data:   Name                                                                   Category   OS
    data:   ---------------------------------------------------------------------  ---------  -------
    data:   CANONICAL__Canonical-Ubuntu-12-04-20120519-2012-05-19-en-us-30GB.vhd   Canonical  Linux
    data:   MSFT__Windows-Server-2008-R2-SP1.11-29-2011                            Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1-with-SQL-Server-2012-Eval.11-29-2011  Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.en-us.30GB.2012-03-22                      Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.2-17-2012                                  Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1.en-us.30GB.2012-3-22                  Microsoft  Windows
    data:   OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd                 OpenLogic  Linux
    data:   SUSE__SUSE-Linux-Enterprise-Server-11SP2-20120521-en-us-30GB.vhd       SUSE       Linux
    data:   SUSE__OpenSUSE64121-03192012-en-us-15GB.vhd                            SUSE       Linux
    data:   WIN2K8-R2-WINRM                                                        User       Windows
    info:   vm image list command OK

**Mostrar imagen de máquina virtual [opciones] &lt;nombre >**

Este comando muestra los detalles de una imagen de máquina virtual.

    ~$ azure vm image show MSFT__Windows-Server-2008-R2-SP1.11-29-2011
    + Fetching VM image
    info:   Executing command vm image show
    data:   {
    data:       Label: 'Windows Server 2008 R2 SP1, Nov 2011',
    data:       Name: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
    data:       Description: 'Microsoft Windows Server 2008 R2 SP1',
    data:       @: { xmlns: 'http://schemas.microsoft.com/windowsazure', xmlns:i: 'http://www.w3.org/2001/XMLSchema-instance' },
    data:       Category: 'Microsoft',
    data:       OS: 'Windows',
    data:       Eula: 'http://www.microsoft.com',
    data:       LogicalSizeInGB: '30'
    data:   }
    info:   vm image show command OK

**eliminación de imágenes de máquina virtual [opciones] &lt;nombre >**

Este comando elimina una imagen de máquina virtual.

    ~$ azure vm image delete my-vm-image
    info:   Executing command vm image delete
    info:   VM image deleted: my-vm-image
    info:   vm image delete command OK

**Crear imagen VM &lt;nombre > [ruta de origen]**

Este comando crea una imagen de máquina virtual. Archivos .vhd personalizado se cargan almacenamiento de blobs y, a continuación, se crea la imagen de la máquina virtual desde allí. Utilizar esta imagen de máquina virtual para crear una máquina virtual. Los parámetros de ubicación y el sistema operativo son necesarios.

>[AZURE.NOTE]Este comando solo admite actualmente cargar archivos .vhd fijo. Para cargar un archivo .vhd dinámicos, use las [Utilidades de disco duro virtual de Azure para ir](https://github.com/Microsoft/azure-vhd-utils-for-go).

Algunos sistemas imponen límites de descriptor de archivo de cada proceso. Si supera este límite, la herramienta muestra un error de límite de descriptor de archivo. Puede ejecutar el comando nuevo con -p &lt;número > parámetro para reducir el número máximo de cargas paralelas. El número máximo predeterminado de cargas paralelas es 96.

    ~$ azure vm image create mytestimage ./Sample.vhd -o windows -l "West US"
    info:   Executing command vm image create
    + Retrieving storage accounts
    info:   VHD size : 13 MB
    info:   Uploading 13312.5 KB
    Requested:100.0% Completed:100.0% Running: 105 Time:    8s Speed:  1721 KB/s
    info:   http://myaccount.blob.core.azure.com/vm-images/Sample.vhd is uploaded successfully
    info:   vm image create command OK

## <a name="commands-to-manage-your-azure-virtual-machine-data-disks"></a>Comandos para administrar los discos de datos de Azure máquina virtual

Discos de datos son archivos .vhd en el almacenamiento de blobs de Windows que puede usar una máquina virtual. Para obtener más información acerca de cómo los discos de datos se implementan almacenamiento de blobs, vea el diagrama técnico Azure mostrado anteriormente.

Los comandos para adjuntar discos de datos (disco de azure vm adjuntar y azure vm disco adjuntar nuevos) asignar un número de unidad lógica (LUN) en el disco de datos adjuntos, según sea necesario por el protocolo SCSI. El primer disco de datos adjunto a una máquina virtual está asignado LUN 0, el siguiente está asignada LUN 1 y así sucesivamente.

Cuando se desconecta un disco de datos con la vm azure desasociar comando, use la &lt;lun&gt; parámetros para indicar qué disco desasociar.

>[AZURE.NOTE] Siempre debe desasociar discos de datos en orden inverso, comenzando con el LUN con el número mayor que se ha asignado. La capa de Linux SCSI no admite desasociar un LUN numerado inferior mientras mayor numeración de LUN aún está conectado. Por ejemplo, no debe desasociar LUN 0 si todavía está conectada LUN 1.

**presentación de disco VM [opciones] &lt;nombre >**

Este comando muestra detalles sobre un disco de Azure.

    ~$ azure vm disk show anucentos-anucentos-0-20120524070008
    info:   Executing command vm disk show
    data:   AttachedTo DeploymentName "mycentos"
    data:   AttachedTo HostedServiceName "myanucentos"
    data:   AttachedTo RoleName "myanucentos"
    data:   OS "Linux"
    data:   Location "Azure Preview"
    data:   LogicalDiskSizeInGB "30"
    data:   MediaLink "http://mystorageaccount.blob.core.azure-preview.com/vhd-store/mycentos-cb39b8223b01f95c.vhd"
    data:   Name "mycentos-mycentos-0-20120524070008"
    data:   SourceImageName "OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd"
    info:   vm disk show command OK

**lista de disco VM [opciones] [nombre de vm]**

Este comando listas discos Azure o discos conectados a una máquina virtual especificada. Si se ejecuta con un parámetro de nombre de la máquina virtual, devuelve todos los discos conectados a la máquina virtual. LUN 1 se crea con la máquina virtual y otros discos enumerados se adjuntan por separado.

    ~$ azure vm disk list mycentos
    info:   Executing command vm disk list
    data:   Lun  Size(GB)  Blob-Name
    data:   ---  --------  --------------------------------
    data:   1    30        mycentos-cb39b8223b01f95c.vhd
    data:   2    10        mycentos-e3f0d717950bb78d.vhd
    info:   vm disk list command OK

Ejecutar este comando sin un parámetro de nombre de la máquina virtual devuelve todos los discos.

    ~$ azure vm disk list
    data:   Name                                        OS
    data:   ------------------------------------------  -------
    data:   mycentos-mycentos-0-20120524070008          Linux
    data:   mycentos-mycentos-2-20120525055052
    data:   mywindows-winvm-20120522223119              Windows
    info:   vm disk list command OK

**eliminar de la máquina virtual disco [opciones] &lt;nombre >**

Este comando elimina un disco Azure un repositorio personal. El disco debe desasociar de la máquina virtual antes de que se elimine.

    ~$ azure vm disk delete mycentos-mycentos-2-20120525055052
    info:   Executing command vm disk delete
    info:   Disk deleted: mycentos-mycentos-2-20120525055052
    info:   vm disk delete command OK

**Crear disco de VM &lt;nombre > [ruta de origen]**

Este comando carga y registra un disco de Azure. --blob url,--ubicación, o--debe especificar afinidad del grupo. Si utiliza este comando con [ruta de origen], el archivo .vhd especificado se carga y se crea una imagen. A continuación, puede adjuntar esta imagen a una máquina virtual con disco vm adjuntar.

Algunos sistemas imponen límites de descriptor de archivo de cada proceso. Si supera este límite, la herramienta muestra un error de límite de descriptor de archivo. Puede ejecutar el comando nuevo con -p &lt;número > parámetro para reducir el número máximo de cargas paralelas. El número máximo predeterminado de cargas paralelas es 96.

    ~$ azure vm disk create my-data-disk ~/test.vhd --location "West US"
    info:   Executing command vm disk create
    info:   VHD size : 10 MB
    info:   Uploading 10240.5 KB
    Requested:100.0% Completed:100.0% Running:  81 Time:   11s Speed:   952 KB/s
    info:   http://account.blob.core.azure.com/disks/test.vhd is uploaded successfully
    info:   vm disk create command OK

**carga de disco VM [opciones] &lt;ruta de origen > &lt;blob url > &lt;clave de cuenta de almacenamiento >**

Este comando le permite cargar un disco de vm

    ~$ azure vm disk upload "http://sourcestorage.blob.core.windows.net/vhds/sample.vhd" "http://destinationstorage.blob.core.windows.net/vhds/sample.vhd" "DESTINATIONSTORAGEACCOUNTKEY"
    info:   Executing command vm disk upload
    info:   Uploading 12351.5 KB
    info:   vm disk upload command OK

**adjuntar VM disco &lt;nombre de vm > &lt;nombre de imagen de disco >**

Este comando asocia un disco existente en el almacenamiento de blobs de Windows a un equipo virtual existente implementado en un servicio de nube.

    ~$ azure vm disk attach my-vm my-vm-my-vm-2-201242418259
    info:   Executing command vm disk attach
    info:   vm disk attach command OK

**máquina virtual disco adjuntar nuevos &lt;nombre de vm > &lt;gb de tamaño > [blob url]**

Este comando asocia un disco de datos a una máquina virtual Azure. En este ejemplo, 20 es el tamaño del nuevo disco en gigabytes, que se adjuntará. Opcionalmente, puede usar una dirección URL de blob como último argumento especificar explícitamente el blob de destino para crear. Si no especifica una dirección URL de blobs, se genera automáticamente un objeto blob.

    ~$ azure vm disk attach-new nick-test36 20 http://nghinazz.blob.core.azure-preview.com/vhds/vmdisk1.vhd
    info:   Executing command vm disk attach-new
    info:   vm disk attach-new command OK  

**Desasociar de máquina virtual disco &lt;nombre de vm > &lt;lun >**

Este comando separa un disco de datos conectado a una máquina virtual Azure. &lt;LUN > identifica el disco para ser separada. Para obtener una lista de discos asociados con un disco antes de que lo desasocie, usar lista de disco vm &lt;nombre de vm >.

    ~$ azure vm disk detach my-vm 2
    info:   Executing command vm disk detach
    info:   vm disk detach command OK

## <a name="commands-to-manage-your-azure-cloud-services"></a>Comandos para administrar los servicios de nube de Azure

Servicios de nube de Azure son aplicaciones y servicios alojados en funciones de web y las funciones de trabajo. Los comandos siguientes pueden utilizarse para administrar los servicios de nube de Azure.

**servicio de crear [opciones] &lt;serviceName >**

Este comando crea un servicio de nube

    ~$ azure service create newservicemsopentech
    info:    Executing command service create
    + Getting locations
    help:    Location:
      1) East Asia
      2) Southeast Asia
      3) North Europe
      4) West Europe
      5) East US
      6) West US
      : 6
    + Creating cloud service
    data:    Cloud service name newservicemsopentech
    info:    service create command OK

**servicio mostrar [opciones] &lt;serviceName >**

Este comando muestra los detalles de un servicio de nube de Azure

    ~$ azure service show newservicemsopentech
    info:    Executing command service show
    + Getting cloud service
    data:    Name newservicemsopentech
    data:    Url https://management.core.windows.net/9e672699-1055-41ae-9c36-e85152f2e352/services/hostedservices/newservicemsopentech
    data:    Properties location West US
    data:    Properties label newservicemsopentech
    data:    Properties status Created
    data:    Properties dateCreated
    data:    Properties dateLastModified
    info:    service show command OK

**lista de servicios [opciones]**

Este comando enumera los servicios de nube de Azure.

    ~$ azure service list
    info:   Executing command service list
    data:   Name         Status
    data:   -----------  -------
    data:   service1     Created
    data:   service2     Created
    info:   service list command OK

**servicio de eliminar [opciones] &lt;nombre >**

Este comando elimina un servicio de nube de Azure.

    ~$ azure service delete myservice
    info:   Executing command service delete myservice
    info:   cloud-service delete command OK

Para forzar la eliminación, utilice la `-q` parámetro.


## <a name="commands-to-manage-your-azure-certificates"></a>Comandos para administrar sus certificados de Azure

Servicio de Azure certificados es SSL conectado a su cuenta de Azure. Para obtener más información acerca de los certificados de Azure, consulte [Administrar certificados](http://msdn.microsoft.com/library/azure/gg981929.aspx).

**lista de certificados de servicio [opciones]**

Este comando muestra los certificados de Azure.

    ~$ azure service cert list
    info:   Executing command service cert list
    + Fetching cloud services
    + Fetching certificates
    data:   Service   Thumbprint                                Algorithm
    data:   --------  ----------------------------------------  ---------
    data:   myservice  262DBF95B5E61375FA27F1E74AC7D9EAE842916C  sha1
    info:   service cert list command OK

**Crear certificado de servicio &lt;prefijo de dns > &lt;archivo > [contraseña]**

Este comando carga un certificado. Deje la solicitud de contraseña en blanco para los certificados que no están protegidos por contraseña.

    ~$ azure service cert create nghinazz ~/publishSet.pfx
    info:   Executing command service cert create
    Cert password:
    + Creating certificate
    info:   service cert create command OK

**eliminar el certificado servicio [opciones] &lt;huella digital >**

Este comando elimina un certificado.

    ~$ azure service cert delete 262DBF95B5E61375FA27F1E74AC7D9EAE842916C
    info:   Executing command service cert delete
    + Deleting certificate
    info:   nghinazz : cert deleted
    info:   service cert delete command OK

## <a name="commands-to-manage-your-web-apps"></a>Comandos para administrar las aplicaciones web

Una aplicación web de Azure es una configuración de web accesible por URI. Aplicaciones Web se hospedan en máquinas virtuales, pero no es necesario plantearse acerca de los detalles de la creación e implementación de la máquina virtual. Azure controla los detalles por usted.

**lista de sitios [opciones]**

Este comando enumera las aplicaciones web.

    ~$ azure site list
    info:   Executing command site list
    data:   Name            State    Host names
    data:   --------------  -------  --------------------------------------------------
    data:   mongosite       Running  mongosite.antdf0.antares.windows.net
    data:   myphpsite       Running  myphpsite.antdf0.antares.windows.net
    data:   mydrupalsite36  Running  mydrupalsite36.antdf0.antares.windows.net
    info:   site list command OK

**conjunto de sitio [opciones] [nombre]**

Este comando establece las opciones de configuración de la aplicación web de [nombre]

    ~$ azure site set
    info:    Executing command site set
    Web site name: mydemosite
    + Getting sites
    + Updating site config information
    info:    site set command OK

**sitio deploymentscript [opciones]**

Este comando genera un script de implementación personalizado

    ~$ azure site deploymentscript --node
    info:    Executing command site deploymentscript
    info:    Generating deployment script for node.js Web Site
    info:    Generated deployment script files
    info:    site deploymentscript command OK

**sitio crear [opciones] [nombre]**

Este comando crea una aplicación web y el directorio local.

    ~$ azure site create mysite
    info:   Executing command site create
    info:   Using location northeuropewebspace
    info:   Creating a new web site
    info:   Created web site at  mysite.antdf0.antares.windows.net
    info:   Initializing repository
    info:   Repository initialized
    info:   site create command OK

> [AZURE.NOTE] El nombre del sitio debe ser único. No puede crear un sitio con el mismo nombre DNS de un sitio existente.

**Examinar sitio [opciones] [nombre]**

Este comando abre la aplicación web en un explorador.

    ~$ azure site browse mysite
    info:   Executing command site browse
    info:   Launching browser to http://mysite.antdf0.antares-test.windows-int.net
    info:   site browse command OK

**Mostrar sitio [opciones] [nombre]**

Este comando muestra detalles sobre una aplicación web.

    ~$ azure site show mysite
    info:   Executing command site show
    info:   Showing details for site
    data:   Site AdminEnabled true
    data:   Site HostNames mysite.antdf0.antares-test.windows-int.net
    data:   Site Name mysite
    data:   Site Owner 00060000814EDDEE
    data:   Site RepositorySiteName mysite
    data:   Site SelfLink https://s1.api.antdf0.antares.windows.net:454/subscriptions/444e62ff-4c5f-4116-a695-5c803ed584a5/webspaces/northeuropewebspace/sites/mysite
    data:   Site State Running
    data:   Site UsageState Normal
    data:   Site WebSpace northeuropewebspace
    data:   Config AppSettings
    data:   Config ConnectionStrings
    data:   Config DefaultDocuments 0=Default.htm, 1=Default.asp, 2=index.htm, 3=index.html, 4=iisstart.htm, 5=default.aspx, 6=index.php, 7=hostingstart.aspx
    data:   Config DetailedErrorLoggingEnabled false
    data:   Config HttpLoggingEnabled false
    data:   Config Metadata
    data:   Config NetFrameworkVersion v4.0
    data:   Config NumberOfWorkers 1
    data:   Config PhpVersion 5.3
    data:   Config PublishingPassword rJ}[Er2v[Y]q16B6vTD]n$[C2z}Z.pvgLfRcLnAp%ax]xstiLny};o@vmMAote@d
    data:   Config RequestTracingEnabled false
    data:   Repository https://mysite.scm.antdf0.antares-test.windows-int.net/
    info:   site show command OK

**Eliminar sitio [opciones] [nombre]**

Este comando elimina una aplicación web.

    ~$ azure site delete mysite
    info:   Executing command site delete
    info:   Deleting site mysite
    info:   Site mysite has been deleted
    info:   site delete command OK

 **intercambio de sitio [opciones] [nombre]**

Este comando cambia dos ranuras de aplicación web.

Este comando admite la siguiente opción adicional:

**- q o **--silencioso **: solicita confirmación. Use esta opción en secuencias de comandos automatizadas.


**inicio del sitio [opciones] [nombre]**

Este comando inicia una aplicación web.

    ~$ azure site start mysite
    info:   Executing command site start
    info:   Starting site mysite
    info:   Site mysite has been started
    info:   site start command OK

**Detener sitio [opciones] [nombre]**

Este comando detiene una aplicación web.

    ~$ azure site stop mysite
    info:   Executing command site stop
    info:   Stopping site mysite
    info:   Site mysite has been stopped
    info:   site stop command OK

**reinicio de sitio [opciones] [nombre]**

Este comando se detiene y, a continuación, inicie una aplicación web especificado.

Este comando admite la siguiente opción adicional:

**--franja** &lt;franja >: el nombre de la casilla para reiniciar.


**lista de ubicaciones de sitio [opciones]**

Este comando enumera las ubicaciones de la aplicación web.

    ~$ azure site location list
    info:    Executing command site location list
    + Getting locations
    data:    Name
    data:    ----------------
    data:    West Europe
    data:    West US
    data:    North Central US
    data:    North Europe
    data:    East Asia
    data:    East US
    info:    site location list command OK

###<a name="commands-to-manage-your-web-app-application-settings"></a>Comandos para administrar la configuración de aplicación web app

**lista de sitios appsetting [opciones] [nombre]**

Este comando muestra la configuración de aplicación que se agrega a la aplicación web.

    ~$ azure site appsetting list
    info:    Executing command site appsetting list
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Name  Value
    data:    ----  -----
    data:    test  value
    info:    site appsetting list command OK

**sitio appsetting agregar [opciones] &lt;keyvaluepair > [nombre]**

Este comando agrega una configuración de aplicación a la aplicación web como un par de valor de clave.

    ~$ azure site appsetting add test=value
    info:    Executing command site appsetting add
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    + Updating site config information
    info:    site appsetting add command OK

**sitio appsetting eliminar [opciones] &lt;clave > [nombre]**

Este comando elimina la configuración de aplicación especificado desde la aplicación web.

    ~$ azure site appsetting delete test
    info:    Executing command site appsetting delete
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    Delete application setting test? [y/n] y
    + Updating site config information
    info:    site appsetting delete command OK

**sitio appsetting mostrar [opciones] &lt;clave > [nombre]**

Este comando muestra detalles de la configuración de aplicación especificado

    ~$ azure site appsetting show test
    info:    Executing command site appsetting show
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Value:  value
    info:    site appsetting show command OK

###<a name="commands-to-manage-your-web-app-certificates"></a>Comandos para administrar los certificados de aplicación web

**lista de certificados de sitio [opciones] [nombre]**

Este comando muestra una lista de los certificados de aplicación web.

    ~$ azure site cert list
    info:    Executing command site cert list
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    data:    Subject                       Expiration Date                    Thumbprint
    data:    ----------------------------  -----------------------------------------
    ----------------  ----------------------------------------
    data:    *.msopentech.com              Fri Nov 28 2014 09:49:57 GMT-0800 (Pacific Standard Time)  A40E82D3DC0286D1F58650E570ECF8224F69A148
    data:    msopentech.azurewebsites.net  Fri Jun 19 2015 11:57:32 GMT-0700 (Pacific Daylight Time)  CE1CD6538852BF7A5DC32001C2E26A29B541F0E8
    info:    site cert list command OK

**certificado de sitio agregar [opciones] &lt;ruta de certificado > [nombre]**

**Eliminar certificado de sitio [opciones] &lt;huella digital > [nombre]**

**Mostrar certificado [opciones] del sitio &lt;huella digital > [nombre]**

Este comando muestra los detalles del certificado

    ~$ azure site cert show CE1CD65852B38DC32001C2E0E8F7A526A29B541F
    info:    Executing command site cert show
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    data:    Certificate hostNames 0=msopentech.azurewebsites.net
    data:    Certificate expirationDate
    data:    Certificate friendlyName msopentech.azurewebsites.net
    data:    Certificate issueDate
    data:    Certificate issuer CN=MSIT Machine Auth CA 2, DC=redmond, DC=corp, DC=microsoft, DC=com
    data:    Certificate subjectName msopentech.azurewebsites.net
    data:    Certificate thumbprint CE1CD65852B38DC32001C2E0E8F7A526A29B541F
    info:    site cert show command OK

###<a name="commands-to-manage-your-web-app-connection-strings"></a>Comandos para administrar las cadenas de conexión de aplicación web

**lista de cadena de conexión de sitio [opciones] [nombre]**

**cadena de conexión de sitio agregar [opciones] &lt;connectionname > &lt;valor > &lt;tipo > [nombre]**

**cadena de conexión de sitio eliminar [opciones] &lt;connectionname > [nombre]**

**cadena de conexión de sitio mostrar [opciones] &lt;connectionname > [nombre]**

###<a name="commands-to-manage-your-web-app-default-documents"></a>Comandos para administrar los documentos de forma predeterminada de aplicación web

**lista de sitios defaultdocument [opciones] [nombre]**

**sitio defaultdocument agregar [opciones] &lt;documento > [nombre]**

**sitio defaultdocument eliminar [opciones] &lt;documento > [nombre]**

###<a name="commands-to-manage-your-web-app-deployments"></a>Comandos para administrar las instalaciones de la aplicación web

**lista de distribución de sitio [opciones] [nombre]**

**implementación del sitio mostrar [opciones] &lt;commitId > [nombre]**

**bola de implementación de sitio [opciones] &lt;commitId > [nombre]**

**implementación de sitio github [opciones] [nombre]**

**conjunto de usuarios de implementación de sitio [opciones] [username] [paso]**

###<a name="commands-to-manage-your-web-app-domains"></a>Comandos para administrar los dominios de la aplicación web

**lista de dominios de sitio [opciones] [nombre]**

**Agregar un dominio de sitio [opciones] &lt;dn > [nombre]**

**eliminar el dominio sitio [opciones] &lt;dn > [nombre]**

###<a name="commands-to-manage-your-web-app-handler-mappings"></a>Comandos para administrar las asignaciones de controlador de aplicación web

**lista de administradores de sitio [opciones] [nombre]**

**controlador de sitio agregar [opciones] &lt;extensión > &lt;procesador > [nombre]**

**Eliminar controlador de sitio [opciones] &lt;extensión > [nombre]**

###<a name="commands-to-manage-your-web-jobs"></a>Comandos para administrar los trabajos de Web

**lista de trabajos de sitio [opciones] [nombre]**

Este comando de la lista todos los trabajos de web en una aplicación web.

Este comando admite las siguientes opciones adicionales:

+ **: tipo de trabajo** &lt;tipo de trabajo >: opcional. El tipo de la webjob. Valor válido es "desencadenadas" o "continua". De forma predeterminada devuelven webjobs de todos los tipos.
+ **--franja** &lt;franja >: el nombre de la casilla para reiniciar.

**Mostrar trabajo [opciones] del sitio &lt;nombreDeTrabajo > &lt;jobType > [nombre]**

Este comando muestra los detalles de una tarea de web específica.

Este comando admite las siguientes opciones adicionales:

+ **: nombre del trabajo** &lt;nombre del trabajo >: obligatorio. El nombre de la webjob.
+ **: tipo de trabajo** &lt;tipo de trabajo >: obligatorio. El tipo de la webjob. Valor válido es "desencadenadas" o "continua".
+ **--franja** &lt;franja >: el nombre de la casilla para reiniciar.

**eliminar de trabajo de sitio [opciones] &lt;nombreDeTrabajo > &lt;jobType > [nombre]**

Este comando elimina el trabajo web especificado.

Este comando admite las siguientes opciones adicionales:

+ **: nombre del trabajo** &lt;nombre del trabajo > necesarios. El nombre de la webjob.
+ **: tipo de trabajo** &lt;tipo de trabajo > necesarios. El tipo de la webjob. Valor válido es "desencadenadas" o "continua".
+ **-q** o **--silencioso**: solicita confirmación. Use esta opción en secuencias de comandos automatizadas.
+ **--franja** &lt;franja >: el nombre de la casilla para reiniciar.

**carga de trabajo de sitio [opciones] &lt;nombreDeTrabajo > &lt;jobType > <jobFile> [nombre]**

Este comando elimina el trabajo web especificado.

Este comando admite las siguientes opciones adicionales:

+ **: nombre del trabajo** &lt;nombre del trabajo >: obligatorio. El nombre de la webjob.
+ **: tipo de trabajo** &lt;tipo de trabajo >: obligatorio. El tipo de la webjob. Valor válido es "desencadenadas" o "continua".
+ **--archivo de trabajo** &lt;archivo de trabajo >: obligatorio. El archivo de trabajo.
+ **--franja** &lt;franja >: el nombre de la casilla para reiniciar.

**sitio de inicio del trabajo [opciones] &lt;nombreDeTrabajo > &lt;jobType > [nombre]**

Este comando inicia el trabajo web especificado.

Este comando admite las siguientes opciones adicionales:

+ **: nombre del trabajo** &lt;nombre del trabajo >: obligatorio. El nombre de la webjob.
+ **: tipo de trabajo** &lt;tipo de trabajo >: obligatorio. El tipo de la webjob. Valor válido es "desencadenadas" o "continua".
+ **--franja** &lt;franja >: el nombre de la casilla para reiniciar.

**dejar de trabajo de sitio [opciones] &lt;nombreDeTrabajo > &lt;jobType > [nombre]**

Este comando detiene el trabajo del sitio web especificado. Solo los trabajos continuos pueden detenerla.

Este comando admite las siguientes opciones adicionales:

+ **: nombre del trabajo** &lt;nombre del trabajo >: obligatorio. El nombre de la webjob.
+ **--franja** &lt;franja >: el nombre de la casilla para reiniciar.

###<a name="commands-to-manage-your-web-jobs-history"></a>Comandos para administrar el historial de trabajos de Web

**lista de historial de trabajos de sitio [opciones] [nombreDeTrabajo] [nombre]**

Este comando muestra un historial de las ejecuciones del trabajo web especificado.

Este comando admite las siguientes opciones adicionales:

+ **: nombre del trabajo** &lt;nombre del trabajo >: obligatorio. El nombre de la webjob.
+ **--franja** &lt;franja >: el nombre de la casilla para reiniciar.

**historial de trabajos de sitio mostrar [opciones] [nombreDeTrabajo] [runId] [nombre]**

Este comando obtiene los detalles del trabajo del trabajo web especificado.

Este comando admite las siguientes opciones adicionales:

+ **: nombre del trabajo** &lt;nombre del trabajo >: obligatorio. El nombre de la webjob.
+ **--Ejecutar id** &lt;identificador de ejecutar >: opcional. El identificador del historial de ejecución. Si no se especifica, mostrar la última ejecución.
+ **--franja** &lt;franja >: el nombre de la casilla para reiniciar.

###<a name="commands-to-manage-your-web-app-diagnostics"></a>Comandos para administrar los diagnósticos de aplicación web

**registro de sitio descargar [opciones] [nombre]**

Descargar un archivo .zip que contiene los diagnósticos de la aplicación web.

    ~$ azure site log download
    info:    Executing command site log download
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    + Downloading diagnostic log to diagnostics.zip
    info:    site log download command OK

**cola de registro de sitio [opciones] [nombre]**

Este comando conecta a su terminal del servicio de transmisión de registro.

    ~$ azure site log tail
    info:    Executing command site log tail
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    2013-11-19T17:24:17  Welcome, you are now connected to log-streaming service.

**registro de sitio establece [opciones] [nombre]**

Este comando configura las opciones de diagnósticos para la aplicación web.

    ~$ azure site log set -a
    info:    Executing command site log set
    + Getting output options
    help:    Output:
      1) file
      2) storage
      : 1
    Web site name: mydemosite
    + Getting locations
    + Getting sites
    + Getting site information
    + Getting diagnostic settings
    + Updating diagnostic settings
    info:    site log set command OK

###<a name="commands-to-manage-your-web-app-repositories"></a>Comandos para administrar los repositorios de la aplicación web

**rama del repositorio de sitio [opciones] &lt;rama > [nombre]**

**eliminar del repositorio de sitio [opciones] [nombre]**

**sincronización del sitio de repositorio [opciones] [nombre]**

###<a name="commands-to-manage-your-web-app-scaling"></a>Comandos para administrar la escala de aplicación web

**modo de escala de sitio [opciones] &lt;modo > [nombre]**

**escala de sitio instancias [opciones] &lt;instancias > [nombre]**


## <a name="commands-to-manage-azure-mobile-services"></a>Comandos de administración de servicios móviles de Azure

Servicios de Azure Mobile reúne un conjunto de servicios de Azure que habilitan las capacidades de back-end para las aplicaciones. Comandos de servicios móviles se dividen en las siguientes categorías:

+ [Comandos para administrar las instancias del servicio móvil](#Mobile_Services)
+ [Comandos para administrar la configuración del servicio móvil](#Mobile_Configuration)
+ [Comandos para administrar las tablas de servicio móvil](#Mobile_Tables)
+ [Comandos para administrar las secuencias de comandos de servicio móvil](#Mobile_Scripts)
+ [Comandos para administrar tareas programadas](#Mobile_Jobs)
+ [Comandos para ajustar el tamaño de un servicio móvil](#Mobile_Scale)

Las opciones siguientes se aplican a la mayoría de los comandos de servicios móviles:

+ **-h** o **--Ayuda**: información sobre el uso de salida de la pantalla.
+ **-s `<id>` ** o **: suscripción `<id>` **: usar una suscripción específica, especificada como `<id>`.
+ **-v** o **--detallado**: agregue un informe detallado.
+ **--json**: escribir JSON salida.

### <a name="Mobile_Services"></a>Comandos para administrar las instancias del servicio móvil

**ubicaciones móviles [opciones]**

Este comando enumera ubicaciones geográficas compatibles con servicios móviles.

    ~$ azure mobile locations
    info:    Executing command mobile locations
    info:    East US (default)
    info:    West US
    info:    North Europe

**Mobile crear [opciones] [nombreDeServicio] [sqlAdminUsername] [sqlAdminPassword]**

Este comando crea un servicio móvil junto con una base de datos SQL y el servidor.

    ~$ azure mobile create todolist your_login_name Secure$Password
    info:    Executing command mobile create
    + Creating mobile service
    info:    Overall application state: Healthy
    info:    Mobile service (todolist) state: ProvisionConfigured
    info:    SQL database (todolist_db) state: Provisioned
    info:    SQL server (e96ean1c6v) state: ProvisionConfigured
    info:    mobile create command OK

Este comando admite las siguientes opciones adicionales:

+ **- r `<sqlServer>` ** o **: SQL Server `<sqlServer>` **: usar un servidor de base de datos SQL existente, especificado como `<sqlServer>`.
+ **-d `<sqlDb>` ** o **--sqlDb `<sqlDb>` **: usar la base de datos SQL existente, especificado como `<sqlDb>`.
+ **-l `<location>` ** o **: ubicación `<location>` **: crear el servicio en una ubicación específica, especificada como `<location>`. Ejecutar azure ubicaciones móviles para obtener las ubicaciones disponibles.
+ **--sqlLocation `<location>` **: crear SQL server en un determinado `<location>`; el valor predeterminado es la ubicación del servicio móvil.

**eliminar móvil [opciones] [nombreDeServicio]**

Este comando elimina un servicio móvil junto con su base de datos SQL y el servidor.

    ~$ azure mobile delete todolist -a -q
    info:    Executing command mobile delete
    data:    Mobile service todolist
    data:    SQL database todolistAwrhcL60azo1C401
    data:    SQL server fh1kvbc7la
    + Deleting mobile service
    info:    Deleted mobile service
    + Deleting SQL server
    info:    Deleted SQL server
    + Deleting mobile application
    info:    Deleted mobile application
    info:    mobile delete command OK

Este comando admite las siguientes opciones adicionales:

+ **-d** o **--deleteData**: eliminar todos los datos de este servicio móvil de la base de datos.
+ **-** o **--deleteAll**: eliminar de la base de datos SQL y el servidor.
+ **-q** o **--silencioso**: solicita confirmación. Use esta opción en secuencias de comandos automatizadas.

**lista móvil [opciones]**

Este comando enumera los servicios móviles.

    ~$ azure mobile list
    info:    Executing command mobile list
    data:    Name          State  URL
    data:    ------------  -----  --------------------------------------
    data:    todolist      Ready  https://todolist.azure-mobile.net/
    data:    mymobileapp   Ready  https://mymobileapp.azure-mobile.net/
    info:    mobile list command OK

**Mostrar móvil [opciones] [nombreDeServicio]**

Este comando muestra detalles acerca de un servicio móvil.

    ~$ azure mobile show todolist
    info:    Executing command mobile show
    + Getting information
    info:    Mobile application
    data:    status Healthy
    data:    Mobile service name todolist
    data:    Mobile service status ProvisionConfigured
    data:    SQL database name todolistAwrhcL60azo1C401
    data:    SQL database status Linked
    data:    SQL server name fh1kvbc7la
    data:    SQL server status Linked
    info:    Mobile service
    data:    name todolist
    data:    state Ready
    data:    applicationUrl https://todolist.azure-mobile.net/
    data:    applicationKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    data:    masterKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    data:    webspace WESTUSWEBSPACE
    data:    region West US
    data:    tables TodoItem
    info:    mobile show command OK

**reiniciar móvil [opciones] [nombreDeServicio]**

Este comando reinicia una instancia de servicios de telefonía móvil.

    ~$ azure mobile restart todolist
    info:    Executing command mobile restart
    + Restarting mobile service
    info:    Service was restarted.
    info:    mobile restart command OK

**registro móvil [opciones] [nombreDeServicio]**

Este comando devuelve los registros del servicio móvil, filtrado de todos los tipos de registro, pero `error`.

    ~$ azure mobile log todolist -t error
    info:    Executing command mobile log
    data:
    data:    timeCreated 2013-01-07T16:04:43.351Z
    data:    type error
    data:    source /scheduler/TestingLogs.js
    data:    message This is an error.
    data:
    info:    mobile log command OK

Este comando admite las siguientes opciones adicionales:

+ **- r `<query>` ** o **--consulta `<query>` **: ejecuta la consulta de registro especificado.
+ **-t `<type>` ** o **--tipo `<type>` **: filtrar los registros devueltos por entrada `<type>`, lo que puede `information`, `warning`, o `error`.
+ **-k `<skip>` ** o **--omitir `<skip>` **: omite el número de filas especificado por `<skip>`.
+ **-p `<top>` ** o **--principio `<top>` **: devuelve un número específico de filas, especificado por `<top>`.

> [AZURE.NOTE] La **--consulta** parámetro tiene prioridad sobre **--tipo**, **--Omitir**, y **--principio**.

**recuperar móvil [opciones] [unhealthyservicename] [healthyservicename]**

Este comando recupera un servicio móvil mal estado moviéndolo a un servicio móvil correcto en una región diferente.

Este comando admite la siguiente opción adicional:

**-q** o **--silencioso**: suprimir la pregunta de confirmación de recuperación.

**tecla móvil regenerar [opciones] [nombreDeServicio] [tipo]**

Este comando regenera la tecla de aplicación de servicios de telefonía móvil.

    ~$ azure mobile key regenerate todolist application
    info:    Executing command mobile key regenerate
    info:    New application key is SmLorAWVfslMcOKWSsuJvuzdJkfUpt40
    info:    mobile key regenerate command OK

Tipos de clave son `master` y `application`.

> [AZURE.NOTE] Al generar las claves, clientes que usan la clave antigua podrán que no puedos tener acceso al servicio móvil. Al regenerar la clave de aplicación, debe actualizar la aplicación con el nuevo valor de clave.

**conjunto de claves móvil [opciones] [nombreDeServicio] [tipo] [valor]**

Este comando establece la clave del servicio móvil a un valor específico.


### <a name="Mobile_Configuration"></a>Comandos para administrar la configuración del servicio móvil

**lista de configuración de dispositivos móviles [opciones] [nombreDeServicio]**

Este comando muestra las opciones de configuración de un servicio móvil.

    ~$ azure mobile config list todolist
    info:    Executing command mobile config list
    + Getting mobile service configuration
    data:    dynamicSchemaEnabled true
    data:    microsoftAccountClientSecret Not configured
    data:    microsoftAccountClientId Not configured
    data:    microsoftAccountPackageSID Not configured
    data:    facebookClientId Not configured
    data:    facebookClientSecret Not configured
    data:    twitterClientId Not configured
    data:    twitterClientSecret Not configured
    data:    googleClientId Not configured
    data:    googleClientSecret Not configured
    data:    apnsMode none
    data:    apnsPassword Not configured
    data:    apnsCertifcate Not configured
    info:    mobile config list command OK

**configuración móvil obtener [opciones] [nombreDeServicio] [clave]**

Este comando obtiene una opción de configuración específica de un servicio móvil, esquema en este caso dinámicas.

    ~$ azure mobile config get todolist dynamicSchemaEnabled
    info:    Executing command mobile config get
    data:    dynamicSchemaEnabled true
    info:    mobile config get command OK

**establecer configuración móvil [opciones] [nombreDeServicio] [clave] [valor]**

Este comando establece una opción de configuración específica de un servicio móvil, esquema en este caso dinámicas.

    ~$ azure mobile config set todolist dynamicSchemaEnabled false
    info:    Executing command mobile config set
    info:    mobile config set command OK


### <a name="Mobile_Tables"></a>Comandos para administrar las tablas de servicio móvil

**lista de la tabla móvil [opciones] [nombreDeServicio]**

Este comando enumera todas las tablas en el servicio móvil.

    ~$azure mobile table list todolist
    info:    Executing command mobile table list
    data:    Name      Indexes  Rows
    data:    --------  -------  ----
    data:    Channel   1        0
    data:    TodoItem  1        0
    info:    mobile table list command OK

**Mostrar tabla móvil [opciones] [nombreDeServicio] [nombre de tabla]**

Este comando muestra los detalles devuelve una tabla específica.

    ~$azure mobile table show todolist
    info:    Executing command mobile table show
    + Getting table information
    info:    Table statistics:
    data:    Number of records 5
    info:    Table operations:
    data:    Operation  Script       Permissions
    data:    ---------  -----------  -----------
    data:    insert     1900 bytes   user
    data:    read       Not defined  user
    data:    update     Not defined  user
    data:    delete     Not defined  user
    info:    Table columns:
    data:    Name  Type           Indexed
    data:    ----  -------------  -------
    data:    id    bigint(MSSQL)  Yes
    data:    text      string
    data:    complete  boolean
    info:    mobile table show command OK

**tabla móvil crear [opciones] [nombreDeServicio] [nombre de tabla]**

Este comando crea una tabla.

    ~$azure mobile table create todolist Channels
    info:    Executing command mobile table create
    + Creating table
    info:    mobile table create command OK

Este comando admite la siguiente opción adicional:

+ **-p `&lt;permissions>` ** o **--permisos `&lt;permissions>` **: lista delimitada por comas de `<operation>` = `<permission>` pares, donde `<operation>` es `insert`, `read`, `update`, o `delete` y `&lt;permissions>` es `public`, `application` (predeterminado), `user`, o `admin`.

**leer los datos móviles [opciones] [nombreDeServicio] [nombre de tabla] [consulta]**

Este comando lee datos de una tabla.

    ~$azure mobile data read todolist TodoItem
    info:    Executing command mobile data read
    data:    id  text     complete
    data:    --  -------  --------
    data:    1   item #1  false
    data:    2   item #2  true
    data:    3   item #3  false
    data:    4   item #4  true
    info:    mobile data read command OK

Este comando admite las siguientes opciones adicionales:

+ **-k `<skip>` ** o **--omitir `<skip>` **: omite el número de filas especificado por `<skip>`.
+ **-t `<top>` ** o **--principio `<top>` **: devuelve un número específico de filas, especificado por `<top>`.
+ **-l** o **--lista**: devuelve datos en un formato de lista.

**actualización de la tabla móvil [opciones] [nombreDeServicio] [nombre de tabla]**

Este comando cambia eliminar permisos en una tabla únicamente a los administradores.

    ~$azure mobile table update todolist Channels -p delete=admin
    info:    Executing command mobile table update
    + Updating permissions
    info:    Updated permissions
    info:    mobile table update command OK

Este comando admite las siguientes opciones adicionales:

+ **-p `&lt;permissions>` ** o **--permisos `&lt;permissions>` **: lista delimitada por comas de `<operation>` = `<permission>` pares, donde `<operation>` es `insert`, `read`, `update`, o `delete` y `&lt;permissions>` es `public`, `application` (predeterminado), `user`, o `admin`.
+ **--deleteColumn `<columns>` **: lista delimitada por comas de columnas para eliminar, como `<columns>`.
+ **-q** o **--silencioso**: elimina las columnas sin pedir confirmación.
+ **--addIndex `<columns>` **: lista delimitada por comas de las columnas para incluir en el índice.
+ **--deleteIndex `<columns>` **: delimitado por comas de la lista de columnas para excluir del índice.

**tabla móvil eliminar [opciones] [nombreDeServicio] [nombre de tabla]**

Este comando elimina una tabla.

    ~$azure mobile table delete todolist Channels
    info:    Executing command mobile table delete
    Do you really want to delete the table (yes/no): yes
    + Deleting table
    info:    mobile table delete command OK

Especifique el parámetro - q para eliminar la tabla sin confirmación. Hacer esto para evitar el bloqueo de secuencias de comandos de automatización.

**datos móviles truncan [opciones] [nombreDeServicio] [nombre de tabla]**

Este comando quita todas las filas de datos de la tabla.

    ~$azure mobile data truncate todolist TodoItem
    info:    Executing command mobile data truncate
    info:    There are 7 data rows in the table.
    Do you really want to delete all data from the table? (y/n): y
    info:    Deleted 7 rows.
    info:    mobile data truncate command OK


### <a name="Mobile_Scripts"></a>Comandos para administrar las secuencias de comandos

Comandos de esta sección se usan para administrar las secuencias de comandos de servidor que pertenecen a un servicio móvil. Para obtener más información, vea [trabajar con secuencias de comandos de servidor de servicios móviles](https://github.com/Azure/azure-mobile-services/blob/master/docs/mobile-services-how-to-use-server-scripts.md).

**lista de secuencia de comandos móvil [opciones] [nombreDeServicio]**

Este comando enumera las secuencias de comandos registrados, incluidas las secuencias de comandos de la tabla y programador.

    ~$azure mobile script list todolist
    info:    Executing command mobile script list
    + Getting script information
    info:    Table scripts
    data:    Name                   Size
    data:    ---------------------  ----
    data:    table/TodoItem.delete  256
    data:    table/Devices.insert   1660
    error:   Unable to get shared scripts
    info:    Scheduler scripts
    data:    Name                 Status     Interval   Last run   Next run
    data:    -------------------  ---------  ---------  ---------  ---------
    data:    scheduler/undefined  undefined  undefined  undefined  undefined
    data:    scheduler/undefined  undefined  undefined  undefined  undefined
    info:    mobile script list command OK

**descarga de script móvil [opciones] [nombreDeServicio] [NombreDeSecuenciaDeComandos]**

Este comando descarga la secuencia de comandos de insertar de la tabla de TodoItem a un archivo llamado `todoitem.insert.js` en la `table` subcarpeta.

    ~$azure mobile script download todolist table/todoitem.insert.js
    info:    Executing command mobile script download
    info:    Saved script to ./table/todoitem.insert.js
    info:    mobile script download command OK

Este comando admite las siguientes opciones adicionales:

+ **-p `<path>` ** o **--ruta `<path>` **: la ubicación del archivo para guardar la secuencia de comandos, donde el directorio de trabajo actual es el valor predeterminado.
+ **-f `<file>` ** o **--archivo `<file>` **: el nombre del archivo en el que desea guardar el script.
+ **-o** o **--Reemplazar**: sobrescribir un archivo existente.
+ **-c** o **--consola**: escribir la secuencia de comandos en la consola en lugar de a un archivo.

**carga de script móvil [opciones] [nombreDeServicio] [NombreDeSecuenciaDeComandos]**

Este comando carga un script denominado `todoitem.insert.js` desde el `table` subcarpeta.

    ~$azure mobile script upload todolist table/todoitem.insert.js
    info:    Executing command mobile script upload
    info:    mobile script upload command OK

El nombre del archivo debe estar compuesto de los nombres de tabla y operación. Que debe estar ubicado en la subcarpeta de la tabla relacionada con la ubicación donde se ejecuta el comando. También puede usar el **-f `<file>` ** o **--archivo `<file>` ** parámetro para especificar un nombre de archivo diferente y la ruta de acceso al archivo que contiene la secuencia de comandos para registrar.


**Eliminar script móvil [opciones] [nombreDeServicio] [NombreDeSecuenciaDeComandos]**

Este comando quita la secuencia de comandos de insertar existente de la tabla TodoItem.

    ~$azure mobile script delete todolist table/todoitem.insert.js
    info:    Executing command mobile script delete
    info:    mobile script delete command OK

### <a name="Mobile_Jobs"></a>Comandos para administrar tareas programadas

Comandos de esta sección se usan para administrar tareas programadas que pertenecen a un servicio móvil. Para obtener más información, consulte [programar trabajos](http://msdn.microsoft.com/library/windowsazure/jj860528.aspx).

**lista de trabajos móvil [opciones] [nombreDeServicio]**

Este comando enumera las tareas programadas.

    ~$azure mobile job list todolist
    info:    Executing command mobile job list
    info:    Scheduled jobs
    data:    Job name    Script name           Status    Interval     Last run              Next run
    data:    ----------  --------------------  --------  -----------  --------------------  --------------------
    data:    getUpdates  scheduler/getUpdates  enabled   15 [minute]  2013-01-14T16:15:00Z  2013-01-14T16:30:00Z
    info:    You can manipulate scheduled job scripts using the 'azure mobile script' command.
    info:    mobile job list command OK

**trabajo móvil crear [opciones] [nombreDeServicio] [nombreDeTrabajo]**

Este comando crea un trabajo denominado `getUpdates` que se ha programado para que se ejecute cada hora.

    ~$azure mobile job create -i 1 -u hour todolist getUpdates
    info:    Executing command mobile job create
    info:    Job was created in disabled state. You can enable the job using the 'azure mobile job update' command.
    info:    You can manipulate the scheduled job script using the 'azure mobile script' command.
    info:    mobile job create command OK

Este comando admite las siguientes opciones adicionales:

+ **-i `<number>` ** o **--intervalo `<number>` **: el intervalo de trabajo, como un número entero. El valor predeterminado es `15`.
+ **-u `<unit>` ** o **--intervalUnit `<unit>` **: la unidad de _intervalo_, que puede ser uno de los siguientes valores:
    + **minuto** (predeterminado)
    + **hora**
    + **día**
    + **mes**
    + **Ninguno** (a petición trabajos)
+ **-t `<time>`** **hora de inicio: `<time>` ** Hora de inicio de la primera ejecución de la secuencia de comandos, en formato ISO. El valor predeterminado es `now`.

> [AZURE.NOTE] Tareas nuevas se crean en un estado deshabilitado porque aún se debe cargar una secuencia de comandos. Use el comando **script móvil cargar** para cargar una secuencia de comandos y el comando **Actualizar trabajo móvil** para habilitar el trabajo.

**Actualizar trabajo móvil [opciones] [nombreDeServicio] [nombreDeTrabajo]**

El siguiente comando habilita la deshabilitado `getUpdates` trabajo.

    ~$azure mobile job update -a enabled todolist getUpdates
    info:    Executing command mobile job update
    info:    mobile job update command OK

Este comando admite las siguientes opciones adicionales:

+ **-i `<number>` ** o **--intervalo `<number>` **: el intervalo de trabajo, como un número entero. El valor predeterminado es `15`.
+ **-u `<unit>` ** o **--intervalUnit `<unit>` **: la unidad de _intervalo_, que puede ser uno de los siguientes valores:
    + **minuto** (predeterminado)
    + **hora**
    + **día**
    + **mes**
    + **Ninguno** (a petición trabajos)
+ **-t `<time>`** **hora de inicio: `<time>` ** Hora de inicio de la primera ejecución de la secuencia de comandos, en formato ISO. El valor predeterminado es `now`.
+ **- `<status>` ** o **: estado `<status>` **: el estado del trabajo, que puede ser `enabled` o `disabled`.

**Eliminar trabajo móvil [opciones] [nombreDeServicio] [nombreDeTrabajo]**

Este comando quita el trabajo programado de getUpdates desde el servidor TodoList.

    ~$azure mobile job delete todolist getUpdates
    info:    Executing command mobile job delete
    info:    mobile job delete command OK

> [AZURE.NOTE] Eliminación de una tarea, también elimina la secuencia de comandos cargado.

### <a name="Mobile_Scale"></a>Comandos para ajustar el tamaño de un servicio móvil

Comandos de esta sección se usan para ajustar el tamaño de un servicio móvil. Para obtener más información, consulte [un servicio móvil de ajuste de escala](http://msdn.microsoft.com/library/windowsazure/jj193178.aspx).

**escala móvil mostrar [opciones] [nombreDeServicio]**

Este comando muestra información de escala, incluido el modo de cálculo actual y número de instancias.

    ~$azure mobile scale show todolist
    info:    Executing command mobile scale show
    data:    webspace WESTUSWEBSPACE
    data:    computeMode Free
    data:    numberOfInstances 1
    info:    mobile scale show command OK

**escala móvil cambiar [opciones] [nombreDeServicio]**

Este comando cambia la escala del servicio móvil de gratuita al modo premium.

    ~$azure mobile scale change -c Reserved -i 1 todolist
    info:    Executing command mobile scale change
    + Rescaling the mobile service
    info:    mobile scale change command OK

Este comando admite las siguientes opciones adicionales:

+ **- c `<mode>` ** o **--computeMode `<mode>` **: el modo de cálculo debe ser `Free` o `Reserved`.
+ **-i `<count>` ** o **--numberOfInstances `<count>` **: el número de instancias usado cuando se ejecuta en modo reservado.

> [AZURE.NOTE] Cuando se establece el modo de cálculo en `Reserved`, todos los servicios móviles de la misma región ejecutarán en modo de premium.


###<a name="commands-to-enable-preview-features-for-your-mobile-service"></a>Comandos para habilitar las características de vista previa para el servicio móvil

**lista de vista previa móvil [opciones] [nombreDeServicio]**

Este comando muestra las características de vista previa en el servicio especificado y de si están habilitados.

    ~$ azure mobile preview list mysite
    info:    Executing command mobile preview list
    + Getting preview features
    data:    Preview feature  Enabled
    data:    ---------------  -------
    data:    SourceControl    No
    data:    Users            No
    info:    You can enable preview features using the 'azure mobile preview enable' command.
    info:    mobile preview list command OK

**vista previa móvil habilitar [opciones] [nombreDeServicio] [NombreCaracterística]**

Este comando habilita la característica de vista previa especificada para un servicio móvil. Una vez habilitada, las características de vista previa no se puede deshabilitar para un servicio móvil.

###<a name="commands-to-manage-your-mobile-service-apis"></a>Comandos para administrar la API del servicio móvil

**lista de api móvil [opciones] [nombreDeServicio]**

Este comando muestra una lista móvil API personalizadas que ha creado para el servicio móvil de servicio.

    ~$ azure mobile api list mysite
    info:    Executing command mobile api list
    + Retrieving list of APIs
    info:    APIs
    data:    Name                  Get          Put          Post         Patch        Delete
    data:    --------------------  -----------  -----------  -----------  -----------  -----------
    data:    myCustomRetrieveAPI   application  application  application  application  application
    info:    You can manipulate API scripts using the 'azure mobile script' command.
    info:    mobile api list command OK

**api móvil crear [opciones] [nombreDeServicio] [apiname]**

Crea una API del servicio móvil de personalizado

    ~$ azure mobile api create mysite myCustomRetrieveAPI
    info:    Executing command mobile api create
    + Creating custom API: 'myCustomRetrieveAPI'
    info:    API was created successfully. You can modify the API using the 'azure mobile script' command.
    info:    mobile api create command OK

Este comando admite la siguiente opción adicional:

**-p** o **--permisos** &lt;permisos >: una lista delimitada por comas de &lt;método > =&lt;permisos > pares.

**actualización de api móvil [opciones] [nombreDeServicio] [apiname]**

Este comando actualiza la API personalizado de servicio móvil especificado.

Este comando admite la siguiente opción adicional:

Este comando admite las siguientes opciones adicionales:

+ **-p** o **--permisos** &lt;permisos >: una lista delimitada por comas de &lt;método > =&lt;permisos > pares.
+ **-f** o **--Forzar**: reemplaza cualquier cambio personalizado para el archivo de metadatos de permisos.

**api móvil eliminar [opciones] [nombreDeServicio] [apiname]**

    ~$ azure mobile api delete mysite myCustomRetrieveAPI
    info:    Executing command mobile api delete
    + Deleting API: 'myCustomRetrieveAPI'
    info:    mobile api delete command OK

Este comando elimina la API personalizado de servicio móvil especificado.

###<a name="commands-to-manage-your-mobile-application-app-settings"></a>Comandos para administrar la configuración de aplicación de la aplicación móvil

**lista de appsetting móvil [opciones] [nombreDeServicio]**

Este comando muestra la configuración de aplicación de la aplicación móvil para el servicio especificado.

    ~$ azure mobile appsetting list mysite
    info:    Executing command mobile appsetting list
    + Retrieving app settings
    data:    Name               Value
    data:    -----------------  -----
    data:    enablebetacontent  true
    info:    mobile appsetting list command OK

**móvil appsetting agregar [opciones] [nombreDeServicio] [nombre] [valor]**

Este comando agrega una configuración de aplicación personalizada para el servicio móvil.

    ~$ azure mobile appsetting add mysite enablebetacontent true
    info:    Executing command mobile appsetting add
    + Retrieving app settings
    + Adding app setting
    info:    mobile appsetting add command OK

**eliminar appsetting móvil [opciones] [nombreDeServicio] [nombre]**

Este comando quita la configuración de la aplicación especificada para el servicio móvil.

    ~$ azure mobile appsetting delete mysite enablebetacontent
    info:    Executing command mobile appsetting delete
    + Retrieving app settings
    + Removing app setting 'enablebetacontent'
    info:    mobile appsetting delete command OK

**Mostrar appsetting móvil [opciones] [nombreDeServicio] [nombre]**

Este comando quita la configuración de la aplicación especificada para el servicio móvil.

    ~$ azure mobile appsetting show mysite enablebetacontent
    info:    Executing command mobile appsetting show
    + Retrieving app settings
    info:    enablebetacontent: true
    info:    mobile appsetting show command OK

## <a name="manage-tool-local-settings"></a>Administrar la herramienta de configuración local

Configuración local es su ID de suscripción y el nombre de la cuenta de almacenamiento predeterminada.

**lista de configuración [opciones]**

Este comando muestra los valores de configuración.

    ~$ azure config list
    info:   Displaying config settings
    data:   Setting                Value
    data:   ---------------------  ------------------------------------
    data:   subscription           32-digit-subscription-key
    data:   defaultStorageAccount  name

**conjunto de configuración [opciones] &lt;nombre&gt;,&lt;valor&gt;**

Este comando cambia una opción de configuración.

    ~$ azure config set defaultStorageAccount myname
    info:   Setting 'defaultStorageAccount' to value 'myname'
    info:   Changes saved.

## <a name="commands-to-manage-service-bus"></a>Comandos para administrar Bus de servicio

Utilice estos comandos para administrar su cuenta de Bus de servicio

**comprobación de espacio de nombres de SB [opciones] &lt;nombre >**

Compruebe que un espacio de nombres de bus de servicio es legal y disponibles.

**crear espacio de nombres de SB &lt;nombre > &lt;ubicación >**

Crea un espacio de nombres de Bus de servicio.

    ~$ azure sb namespace create mysbnamespacea-test "West US"
    info:    Executing command sb namespace create
    + Creating namespace mysbnamespacea-test in region West US
    data:    Name: mysbnamespacea-test
    data:    Region: West US
    data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
    data:    Status: Activating
    data:    CreatedAt: 2013-11-14T16:23:29.32Z
    data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
    data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

    data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
    net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
    WMoRpYy0Ynk=
    data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
    data:    Enabled: true
    data:    _: [object Object]
    info:    sb namespace create command OK


**eliminar el espacio de nombres de SB &lt;nombre >**

Quitar un espacio de nombres.

    ~$ azure sb namespace delete mysbnamespacea-test
    info:    Executing command sb namespace delete
    Delete namespace mysbnamespacea-test? [y/n] y
    + Deleting namespace mysbnamespacea-test
    info:    sb namespace delete command OK

**lista de nombres de SB**

Lista de todos los espacios de nombres que creó para la cuenta.

    ~$ azure sb namespace list
    info:    Executing command sb namespace list
    + Getting namespaces
    data:    Name                 Region   Status
    data:    -------------------  -------  ------
    data:    mysbnamespacea-test  West US  Active
    info:    sb namespace list command OK


**lista de ubicación de espacio de nombres de SB**

Mostrar una lista de todas las ubicaciones disponibles de espacio de nombres.

    ~$ azure sb namespace location list
    info:    Executing command sb namespace location list
    + Getting locations
    data:    Name              Code
    data:    ----------------  ----------------
    data:    East Asia         East Asia
    data:    West Europe       West Europe
    data:    North Europe      North Europe
    data:    East US           East US
    data:    Southeast Asia    Southeast Asia
    data:    North Central US  North Central US
    data:    West US           West US
    data:    South Central US  South Central US
    info:    sb namespace location list command OK

**Mostrar nombres de SB &lt;nombre >**

Mostrar detalles de un espacio de nombres específico.

    ~$ azure sb namespace show mysbnamespacea-test
    info:    Executing command sb namespace show
    + Getting namespace
    data:    Name: mysbnamespacea-test
    data:    Region: West US
    data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
    data:    Status: Active
    data:    CreatedAt: 2013-11-14T16:23:29.32Z
    data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
    data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

    data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
    net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
    WMoRpYy0Ynk=
    data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
    data:    Enabled: true
    data:    UpdatedAt: 2013-11-14T16:25:37.85Z
    info:    sb namespace show command OK

**comprobar el espacio de nombres de SB &lt;nombre >**

Comprobar si el espacio de nombres está disponible.

## <a name="commands-to-manage-your-storage-objects"></a>Comandos para administrar los objetos de almacenamiento

###<a name="commands-to-manage-your-storage-accounts"></a>Comandos para administrar las cuentas de almacenamiento

**lista de cuentas de almacenamiento [opciones]**

Este comando muestra las cuentas de almacenamiento de su suscripción.

    ~$ azure storage account list
    info:    Executing command storage account list
    + Getting storage accounts
    data:    Name             Label  Location
    data:    ---------------  -----  --------
    data:    mybasestorage           West US
    info:    storage account list command OK

**Mostrar cuenta de almacenamiento [opciones]<name>**

Este comando muestra información sobre la cuenta de almacenamiento especificado, incluidas las propiedades URI y cuenta.

**cuenta de almacenamiento crear [opciones]<name>**

Este comando crea una cuenta de almacenamiento basada en las opciones proporcionadas.

    ~$ azure storage account create mybasestorage --label PrimaryStorage --location "West US"
    info:    Executing command storage account create
    + Creating storage account
    info:    storage account create command OK

Este comando admite las siguientes opciones adicionales:

+ **-e** o **--etiqueta** &lt;etiqueta >: la etiqueta de la cuenta de almacenamiento.
+ **-d** o **: descripción** &lt;descripción >: la cuenta de almacenamiento de la descripción.
+ **-l** o **--ubicación** &lt;nombre >: región geográfica en la que se va a crear la cuenta de almacenamiento.
+ **-** o **--grupo afinidad** &lt;nombre >: el grupo de afinidad que desea asociar la cuenta de almacenamiento. 
+ **--tipo**: indica el tipo de cuenta que desea crear: cualquier almacenamiento estándar con la opción de redundancia (LRS, ZRS, GRS/RAGRS) o almacenamiento Premium (PLRS).

**cuenta de almacenamiento configurado [opciones]<name>**

Este comando actualiza la cuenta de almacenamiento especificado.

    ~$ azure storage account set mybasestorage --kind Storage --sku-name GRS
    info:    Executing command storage account set
    + Updating storage account
    info:    storage account set command OK

Este comando admite las siguientes opciones adicionales:

+ **-e** o **--etiqueta** &lt;etiqueta >: la etiqueta de la cuenta de almacenamiento.
+ **-d** o **: descripción** &lt;descripción >: la cuenta de almacenamiento de la descripción.
+ **-l** o **--ubicación** &lt;nombre >: región geográfica en la que se va a crear la cuenta de almacenamiento.
+ **--tipo**: indica el nuevo tipo de cuenta: cualquier almacenamiento estándar con la opción de redundancia (LRS, ZRS, GRS/RAGRS) o almacenamiento Premium (PLRS).

**Eliminar cuenta de almacenamiento [opciones]<name>**

Este comando elimina la cuenta de almacenamiento especificado.

Este comando admite la siguiente opción adicional:

**-q** o **--silencioso**: solicita confirmación. Use esta opción en secuencias de comandos automatizadas.

###<a name="commands-to-manage-your-storage-account-keys"></a>Comandos para administrar las claves de la cuenta de almacenamiento

**[opciones] de la lista de claves de la cuenta de almacenamiento<name>**

Este comando enumera las claves principales y secundarias para la cuenta de almacenamiento especificado.

**teclas de la cuenta de almacenamiento renovación [opciones]<name>**

###<a name="commands-to-manage-your-storage-container"></a>Comandos para administrar el contenedor de almacenamiento

**lista de contenedor de almacenamiento [opciones] [prefijo]**

Este comando muestra la lista de contenedor de almacenamiento para una cuenta de almacenamiento especificado. La cuenta de almacenamiento especificada por la cadena de conexión o la clave de nombre y la cuenta de la cuenta de almacenamiento.

Este comando admite las siguientes opciones adicionales:

+ **-p** o **-prefijo** &lt;prefijo >: el prefijo de nombre del contenedor de almacenamiento.
+ **-** o **--nombre de la cuenta** &lt;nombre de la cuenta >: el nombre de la cuenta de almacenamiento.
+ **-k** o **--clave de cuenta** &lt;accountKey >: la clave de cuenta de almacenamiento.
+ **-c** o **--cadena de conexión** &lt;cadena de conexión >: la cadena de conexión de almacenamiento.
+ **--Depurar**: ejecuta el comando de almacenamiento en modo de depuración.

**presentación de contenedor de almacenamiento [opciones] [contenedor]**
**contenedor de almacenamiento crear [opciones] [contenedor]**

Este comando crea un contenedor de almacenamiento para la cuenta de almacenamiento especificado. La cuenta de almacenamiento especificada por la cadena de conexión o la clave de nombre y la cuenta de la cuenta de almacenamiento.

Este comando admite las siguientes opciones adicionales:

+ **--contenedor** &lt;contenedor >: el nombre del contenedor de almacenamiento para crear.
+ **-p** o **-prefijo** &lt;prefijo >: el prefijo de nombre del contenedor de almacenamiento.
+ **-** o **--nombre de la cuenta** &lt;nombre de la cuenta >: el nombre de la cuenta de almacenamiento
+ **-k** o **--clave de cuenta** &lt;accountKey >: la clave de cuenta de almacenamiento
+ **-c** o **--cadena de conexión** &lt;cadena de conexión >: la cadena de conexión de almacenamiento
+ **--Depurar**: ejecuta el comando de almacenamiento en modo de depuración.

**eliminar del contenedor de almacenamiento [opciones] [contenedor]**

Este comando elimina el contenedor de almacenamiento especificado. La cuenta de almacenamiento especificada por la cadena de conexión o la clave de nombre y la cuenta de la cuenta de almacenamiento.

Este comando admite las siguientes opciones adicionales:

+ **--contenedor** &lt;contenedor >: el nombre del contenedor de almacenamiento para crear.
+ **-p** o **-prefijo** &lt;prefijo >: el prefijo de nombre del contenedor de almacenamiento.
+ **-** o **--nombre de la cuenta** &lt;nombre de la cuenta >: el nombre de la cuenta de almacenamiento.
+ **-k** o **--clave de cuenta** &lt;accountKey >: la clave de cuenta de almacenamiento.
+ **-c** o **--cadena de conexión** &lt;cadena de conexión >: la cadena de conexión de almacenamiento.
+ **--Depurar**: ejecuta el comando de almacenamiento en modo de depuración.

**contenedor de almacenamiento establece [opciones] [contenedor]**

Este comando establece la lista de control de acceso para el contenedor de almacenamiento. La cuenta de almacenamiento especificada por la cadena de conexión o la clave de nombre y la cuenta de la cuenta de almacenamiento.

Este comando admite las siguientes opciones adicionales:

+ **--contenedor** &lt;contenedor >: el nombre del contenedor de almacenamiento para crear.
+ **-p** o **-prefijo** &lt;prefijo >: el prefijo de nombre del contenedor de almacenamiento.
+ **-** o **--nombre de la cuenta** &lt;nombre de la cuenta >: el nombre de la cuenta de almacenamiento.
+ **-k** o **--clave de cuenta** &lt;accountKey >: la clave de cuenta de almacenamiento.
+ **-c** o **--cadena de conexión** &lt;cadena de conexión >: la cadena de conexión de almacenamiento.
+ **--Depurar**: ejecuta el comando de almacenamiento en modo de depuración.

###<a name="commands-to-manage-your-storage-blob"></a>Comandos para administrar el blob de almacenamiento

**lista almacenamiento de blobs [opciones] [contenedor] [prefijo]**

Este comando devuelve una lista de blobs de almacenamiento en el contenedor de almacenamiento especificado.

Este comando admite las siguientes opciones adicionales:

+ **--contenedor** &lt;contenedor >: el nombre del contenedor de almacenamiento para crear.
+ **-p** o **-prefijo** &lt;prefijo >: el prefijo de nombre del contenedor de almacenamiento.
+ **-** o **--nombre de la cuenta** &lt;nombre de la cuenta >: el nombre de la cuenta de almacenamiento.
+ **-k** o **--clave de cuenta** &lt;accountKey >: la clave de cuenta de almacenamiento.
+ **-c** o **--cadena de conexión** &lt;cadena de conexión >: la cadena de conexión de almacenamiento.
+ **--Depurar**: ejecuta el comando de almacenamiento en modo de depuración.

**mostrar de blobs de almacenamiento [opciones] [contenedor] [blob]**

Este comando muestra los detalles de blobs de almacenamiento especificado.

Este comando admite las siguientes opciones adicionales:

+ **--contenedor** &lt;contenedor >: el nombre del contenedor de almacenamiento para crear.
+ **-p** o **-prefijo** &lt;prefijo >: el prefijo de nombre del contenedor de almacenamiento.
+ **-** o **--nombre de la cuenta** &lt;nombre de la cuenta >: el nombre de la cuenta de almacenamiento.
+ **-k** o **--clave de cuenta** &lt;accountKey >: la clave de cuenta de almacenamiento.
+ **-c** o **--cadena de conexión** &lt;cadena de conexión >: la cadena de conexión de almacenamiento.
+ **--Depurar**: ejecuta el comando de almacenamiento de información de depuración.

**eliminar de blobs de almacenamiento [opciones] [contenedor] [blob]**

Este comando admite las siguientes opciones adicionales:

+ **--contenedor** &lt;contenedor >: el nombre del contenedor de almacenamiento para crear.
+ **-b** o **--blob** &lt;blobName >: el nombre del objeto binario de almacenamiento para eliminar.
+ **-q** o **--silencioso**: quitar el blob de almacenamiento especificado sin confirmación.
+ **-** o **--nombre de la cuenta** &lt;nombre de la cuenta >: el nombre de la cuenta de almacenamiento.
+ **-k** o **--clave de cuenta** &lt;accountKey >: la clave de cuenta de almacenamiento.
+ **-c** o **--cadena de conexión** &lt;cadena de conexión >: la cadena de conexión de almacenamiento.
+ **--Depurar**: ejecuta el comando de almacenamiento de información de depuración.

**carga de blobs de almacenamiento [opciones] [archivo] [contenedor] [blob]**

Este comando cargar el archivo especificado en el blob de almacenamiento de specified\.

Este comando admite las siguientes opciones adicionales:

+ **--contenedor** &lt;contenedor >: el nombre del contenedor de almacenamiento para crear.
+ **-b** o **--blob** &lt;blobName >: el nombre del objeto binario de almacenamiento para cargar.
+ **-t** o **--blobtype** &lt;blobtype >: el tipo de almacenamiento de blobs: bloque o página.
+ **-p** o **--Propiedades** &lt;Propiedades >: las propiedades de blobs de almacenamiento de archivo cargado. Propiedades son clave = s de par de valor y separados con semicolon(;). Las propiedades disponibles son contentType, contentEncoding, contentLanguage y cacheControl.
+ **-m** o **metadatos--** &lt;metadatos >: los metadatos de blobs de almacenamiento para el archivo cargado. Metadatos son clave = pares de valor una instrucción d separada con punto y coma (;).
+ **--concurrenttaskcount** &lt;concurrenttaskcount >: el número máximo de solicitudes simultáneas de carga.
+ **-q** o **--silencioso**: sobrescribir el blob de almacenamiento especificado sin confirmación.
+ **-** o **--nombre de la cuenta** &lt;nombre de la cuenta >: el nombre de la cuenta de almacenamiento.
+ **-k** o **--clave de cuenta** &lt;accountKey >: la clave de cuenta de almacenamiento.
+ **-c** o **--cadena de conexión** &lt;cadena de conexión >: la cadena de conexión de almacenamiento.
+ **--Depurar**: ejecuta el comando de almacenamiento de información de depuración.

**descarga de blobs de almacenamiento [opciones] [contenedor] [blob] [destino]**

Este comando descarga el blob de almacenamiento especificado.

Este comando admite las siguientes opciones adicionales:

+ **--contenedor** &lt;contenedor >: el nombre del contenedor de almacenamiento para crear.
+ **-b** o **--blob** &lt;blobName >: el nombre de blobs de almacenamiento.
+ **-d** o **--destino** [destino]: la ruta de archivo o directorio de destino de descarga.
+ **-m** o **--checkmd5**: la md5sum de comprobación para el archivo descargado.
+ **--concurrenttaskcount** &lt;concurrenttaskcount > el número máximo de solicitudes simultáneas de carga
+ **-q** o **--silencioso**: sobrescribir el archivo de destino sin confirmación.
+ **-** o **--nombre de la cuenta** &lt;nombre de la cuenta >: el nombre de la cuenta de almacenamiento.
+ **-k** o **--clave de cuenta** &lt;accountKey >: la clave de cuenta de almacenamiento.
+ **-c** o **--cadena de conexión** &lt;cadena de conexión >: la cadena de conexión de almacenamiento.
+ **--Depurar**: ejecuta el comando de almacenamiento de información de depuración.

## <a name="commands-to-manage-sql-databases"></a>Comandos para administrar bases de datos SQL

Utilice estos comandos para administrar las bases de datos de SQL Azure

###<a name="commands-to-manage-sql-servers"></a>Comandos para administrar servidores SQL Server.

Utilice estos comandos para administrar los servidores de SQL

**SQL server crear &lt;administratorLogin > &lt;administratorPassword > &lt;ubicación >**

Crear un servidor de base de datos

    ~$ azure sql server create test T3stte$t "West US"
    info:    Executing command sql server create
    + Creating SQL Server
    data:    Server Name i1qwc540ts
    info:    sql server create command OK

**presentación de SQL server &lt;nombre >**

Mostrar detalles del servidor.

    ~$ azure sql server show xclfgcndfg
    info:    Executing command sql server show
    + Getting SQL server
    data:    SQL Server Name xclfgcndfg
    data:    SQL Server AdministratorLogin msopentechforums
    data:    SQL Server Location West US
    data:    SQL Server FullyQualifiedDomainName xclfgcndfg.database.windows.net
    info:    sql server show command OK

**lista de SQL server**

Obtener la lista de servidores.

    ~$ azure sql server list
    info:    Executing command sql server list
    + Getting SQL server
    data:    Name        Location
    data:    ----------  --------
    data:    xclfgcndfg  West US
    info:    sql server list command OK

**eliminación de SQL server &lt;nombre >**

Elimina un servidor

    ~$ azure sql server delete i1qwc540ts
    info:    Executing command sql server delete
    Delete server i1qwc540ts? [y/n] y
    + Removing SQL Server
    info:    sql server delete command OK

###<a name="commands-to-manage-sql-databases"></a>Comandos para administrar bases de datos SQL

Utilice estos comandos para administrar las bases de datos de SQL.

**db de SQL crear [opciones] &lt;nombre_de_servidor > &lt;databaseName > &lt;administratorPassword >**

Crea una instancia de base de datos

    ~$ azure sql db create fr8aelne00 newdb test
    info:    Executing command sql db create
    Administrator password: ********
    + Creating SQL Server Database
    info:    sql db create command OK

**db de SQL mostrar [opciones] &lt;nombre_de_servidor > &lt;databaseName > &lt;administratorPassword >**

Mostrar detalles de la base de datos.

    C:\windows\system32>azure sql db show fr8aelne00 newdb test
    info:    Executing command sql db show
    Administrator password: ********
    + Getting SQL server databases
    data:    Database _ ContentRootElement=m:properties, id=https://fr8aelne00.datab
    ase.windows.net/v1/ManagementService.svc/Server2('fr8aelne00')/Databases(4), ter
    m=Microsoft.SqlServer.Management.Server.Domain.Database, scheme=http://schemas.m
    icrosoft.com/ado/2007/08/dataservices/scheme, link=[rel=edit, title=Database, hr
    ef=Databases(4), rel=http://schemas.microsoft.com/ado/2007/08/dataservices/relat
    ed/Server, type=application/atom+xml;type=entry, title=Server, href=Databases(4)
    /Server, rel=http://schemas.microsoft.com/ado/2007/08/dataservices/related/Servi
    ceObjective, type=application/atom+xml;type=entry, title=ServiceObjective, href=
    Databases(4)/ServiceObjective, rel=http://schemas.microsoft.com/ado/2007/08/data
    services/related/DatabaseMetrics, type=application/atom+xml;type=entry, title=Da
    tabaseMetrics, href=Databases(4)/DatabaseMetrics, rel=http://schemas.microsoft.c
    om/ado/2007/08/dataservices/related/DatabaseCopies, type=application/atom+xml;ty
    pe=feed, title=DatabaseCopies, href=Databases(4)/DatabaseCopies], title=, update
    d=2013-11-18T19:48:27Z, name=
    data:    Database Id 4
    data:    Database Name newdb
    data:    Database ServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
    data:    Database AssignedServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
    data:    Database ServiceObjectiveAssignmentState 1
    data:    Database ServiceObjectiveAssignmentStateDescription Complete
    data:    Database ServiceObjectiveAssignmentErrorCode
    data:    Database ServiceObjectiveAssignmentErrorDescription
    data:    Database ServiceObjectiveAssignmentSuccessDate
    data:    Database Edition Web
    data:    Database MaxSizeGB 1
    data:    Database MaxSizeBytes 1073741824
    data:    Database CollationName SQL_Latin1_General_CP1_CI_AS
    data:    Database CreationDate
    data:    Database RecoveryPeriodStartDate
    data:    Database IsSystemObject
    data:    Database Status 1
    data:    Database IsFederationRoot
    data:    Database SizeMB -1
    data:    Database IsRecursiveTriggersOn
    data:    Database IsReadOnly
    data:    Database IsFederationMember
    data:    Database IsQueryStoreOn
    data:    Database IsQueryStoreReadOnly
    data:    Database QueryStoreMaxSizeMB
    data:    Database QueryStoreFlushPeriodSeconds
    data:    Database QueryStoreIntervalLengthMinutes
    data:    Database QueryStoreClearAll
    data:    Database QueryStoreStaleQueryThresholdDays
    info:    sql db show command OK

**lista de base de datos de SQL [opciones] &lt;nombre_de_servidor > &lt;administratorPassword >**

Lista de las bases de datos.

    ~$ azure sql db list fr8aelne00 test
    info:    Executing command sql db list
    Administrator password: ********
    + Getting SQL server databases
    data:    Name    Edition  Collation                     MaxSizeInGB
    data:    ------  -------  ----------------------------  -----------
    data:    master  Web      SQL_Latin1_General_CP1_CI_AS  5
    info:    sql db list command OK

**db de SQL eliminar [opciones] &lt;nombre_de_servidor > &lt;databaseName > &lt;administratorPassword >**

Elimina una base de datos.

    ~$ azure sql db delete fr8aelne00 newdb test
    info:    Executing command sql db delete
    Administrator password: ********
    Delete database newdb? [y/n] y
    + Getting SQL server databases
    + Removing database
    info:    sql db delete command OK

###<a name="commands-to-manage-your-sql-server-firewall-rules"></a>Comandos para administrar las reglas de firewall de SQL Server

Utilice estos comandos para administrar las reglas de firewall de SQL Server

**SQL firewallrule crear [opciones] &lt;nombre_de_servidor > &lt;nombreregla > &lt;startIPAddress > &lt;endIPAddress >**

Crear una regla de firewall para SQL Server.

    ~$ azure sql firewallrule create fr8aelne00 allowed 131.107.0.0 131.107.255.255
    info:    Executing command sql firewallrule create
    + Creating Firewall Rule
    info:    sql firewallrule create command OK

**SQL firewallrule mostrar [opciones] &lt;nombre_de_servidor > &lt;nombreregla >**

Mostrar detalles de la regla de firewall.

    ~$ azure sql firewallrule show fr8aelne00 allowed
    info:    Executing command sql firewallrule show
    + Getting firewall rule
    data:    Firewall rule Name allowed
    data:    Firewall rule Type Microsoft.SqlAzure.FirewallRule
    data:    Firewall rule State Normal
    data:    Firewall rule SelfLink https://management.core.windows.net/9e672699-105
    5-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00/firewallrules/allowed
    data:    Firewall rule ParentLink https://management.core.windows.net/9e672699-1
    055-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00
    data:    Firewall rule StartIPAddress 131.107.0.0
    data:    Firewall rule EndIPAddress 131.107.255.255
    info:    sql firewallrule show command OK

**lista de firewallrule SQL [opciones] &lt;nombre_de_servidor >**

Lista de las reglas de firewall.

    ~$ azure sql firewallrule list fr8aelne00
    info:    Executing command sql firewallrule list
    \data:    Name     Start IP address  End IP address
    data:    -------  ----------------  ---------------
    data:    allowed  131.107.0.0       131.107.255.255
    +
    info:    sql firewallrule list command OK

**SQL firewallrule eliminar [opciones] &lt;nombre_de_servidor > &lt;nombreregla >**

Este comando elimina una regla de firewall.

    ~$ azure sql firewallrule delete fr8aelne00 allowed
    info:    Executing command sql firewallrule delete
    Delete rule allowed? [y/n] y
    + Removing firewall rule
    info:    sql firewallrule delete command OK

## <a name="commands-to-manage-your-virtual-networks"></a>Comandos para administrar sus redes Virtual

Utilice estos comandos para administrar sus redes Virtual

**red vnet crear [opciones] &lt;ubicación >**

Crear una red Virtual.

    ~$ azure network vnet create vnet1 --location "West US" -v
    info:    Executing command network vnet create
    info:    Using default address space start IP: 10.0.0.0
    info:    Using default address space cidr: 8
    info:    Using default subnet start IP: 10.0.0.0
    info:    Using default subnet cidr: 11
    verbose: Address Space [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/8 (16777216)
    verbose: Subnet [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/11 (2097152)
    verbose: Fetching Network Configuration
    verbose: Fetching or creating affinity group
    verbose: Fetching Affinity Groups
    verbose: Fetching Locations
    verbose: Creating new affinity group AG1
    info:    Using affinity group AG1
    verbose: Updating Network Configuration
    info:    network vnet create command OK

**Mostrar vnet de red &lt;nombre >**

Mostrar detalles de una red Virtual.

    ~$ azure network vnet show vnet1
    info:    Executing command network vnet show
    + Fetching Virtual Networks
    data:    Name "vnet1"
    data:    Id "25786fbe-08e8-4e7e-b1de-b98b7e586c7a"
    data:    AffinityGroup "AG1"
    data:    State "Created"
    data:    AddressSpace AddressPrefixes 0 "10.0.0.0/8"
    data:    Subnets 0 Name "subnet-1"
    data:    Subnets 0 AddressPrefix "10.0.0.0/11"
    info:    network vnet show command OK

**lista de vnet de red**

Lista de todas las redes Virtual existente.

    ~$ azure network vnet list
    info:    Executing command network vnet list
    + Fetching Virtual Networks
    data:    Name        Status   AffinityGroup
    data:    ----------  -------  -------------
    data:    vnet1      Created  AG1
    data:    vnet2      Created  AG1
    data:    vnet3      Created  AG1
    data:    vnet4      Created  AG1
    info:    network vnet list command OK


**eliminar vnet red &lt;nombre >**

Elimina la red Virtual especificada.

    ~$ azure network vnet delete opentechvn1
    info:    Executing command network vnet delete
    + Fetching Network Configuration
    Delete the virtual network opentechvn1 ?  (y/n) y
    + Deleting the virtual network opentechvn1
    info:    network vnet delete command OK

**exportación de red [ruta del archivo]**

Para configuración de red avanzada, puede exportar la configuración de la red local. La configuración de red exportado incluye la configuración del servidor DNS, configuración de red virtual, configuración del sitio de red local y otras opciones de configuración.

**importación de red [ruta del archivo]**

Importar la configuración de una red local.

**red servidorDNS registrar [opciones] &lt;dnsIP >**

Registrar un servidor DNS que va a utilizar para la resolución de nombres en la configuración de red.

    ~$ azure network dnsserver register 98.138.253.109 --dns-id FrontEndDnsServer
    info:    Executing command network dnsserver register
    + Fetching Network Configuration
    + Updating Network Configuration
    info:    network dnsserver register command OK

**lista de servidorDNS de red**

Lista de todos los servidores DNS registrados en la configuración de red.

    ~$ azure network dnsserver list
    info:    Executing command network dnsserver list
    + Fetching Network Configuration
    data:    DNS Server ID         DNS Server IP
    data:    --------------------  --------------
    data:    DNS-bb39b4ac34d66a86  44.55.22.11
    data:    FrontEndDnsServer     98.138.253.109
    info:    network dnsserver list command OK

**red servidorDNS anular [opciones] &lt;dnsIP >**

Quita una entrada de servidor DNS de la configuración de red.

    ~$ azure network dnsserver unregister 77.88.99.11
    info:    Executing command network dnsserver unregister
    + Fetching Network Configuration
    Delete the DNS server entry dns-4 ( 77.88.99.11 ) %s ? (y/n) y
    + Deleting the DNS server entry dns-4 ( 77.88.99.11 )
    info:    network dnsserver unregister command OK

