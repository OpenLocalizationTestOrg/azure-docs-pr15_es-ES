<properties
    pageTitle="Capturar una VM Linux para usarlo como una plantilla | Microsoft Azure"
    description="Obtenga información sobre cómo capturar y generalizar una imagen de una basados en Linux Azure máquina virtual (VM) creada con el modelo de implementación de administrador de recursos de Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="iainfou"/>


# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>Capturar una máquina virtual de Linux ejecuta en Azure

Siga los pasos de este artículo para generalizar y capturar el Azure Linux máquina virtual () en el modelo de implementación de administrador de recursos. Cuando se generaliza la máquina virtual, quite la información de cuenta personal y preparar la máquina virtual que se utilizará como una imagen. Capturar una imagen de disco duro virtual generalized (disco duro virtual) para el sistema operativo, VHD para discos de datos adjuntos y una [plantilla de administrador de recursos](../azure-resource-manager/resource-group-overview.md) para nuevas implementaciones de máquina virtual. 

Para crear máquinas virtuales con la imagen, configurar recursos de red para cada nueva máquina virtual y usar la plantilla (un archivo de notación de objetos JavaScript o JSON,) para implementar desde las imágenes capturadas del disco duro virtual. De esta manera, puede replicar una máquina virtual con la configuración actual de software, similar a la forma de que usar imágenes en Azure Marketplace.

>[AZURE.TIP]Si desea crear una copia de la VM Linux existente con su estado especializada para copia de seguridad o de depuración, consulte [crear una copia de una máquina virtual de Linux ejecuta en Azure](virtual-machines-linux-copy-vm.md). Y si desea cargar un VHD Linux desde una máquina virtual de local, consulte [cargar y crear una VM Linux de la imagen de disco personalizada](virtual-machines-linux-upload-vhd.md).  

## <a name="before-you-begin"></a>Antes de empezar

Asegúrese de que cumple los siguientes requisitos previos:

* **VM de azure creados en el modelo de implementación de administrador de recursos** : si todavía no lo ha creado una VM Linux, puede usar el [portal](virtual-machines-linux-quick-create-portal.md), [Azure CLI](virtual-machines-linux-quick-create-cli.md)o [plantillas de administrador de recursos](virtual-machines-linux-cli-deploy-templates.md). 

    Configurar la máquina virtual según sea necesario. Por ejemplo, [Agregar discos de datos](virtual-machines-linux-add-disk.md), aplicar actualizaciones e instalar aplicaciones. 
* **Azure CLI** - instalar [CLI de Azure](../xplat-cli-install.md) en un equipo local.

## <a name="step-1-remove-the-azure-linux-agent"></a>Paso 1: Quitar al agente de Linux de Azure

Primero, ejecute el comando **waagent** con el parámetro **deprovision** en la VM Linux. Este comando elimina los archivos y los datos para que la máquina virtual esté listo para generalización. Para obtener información detallada, consulte la [Guía de usuario de agente de Azure Linux](virtual-machines-linux-agent-user-guide.md).

1. Conectarse a su VM Linux mediante un cliente SSH.

2. En la ventana SSH, escriba el siguiente comando:

    ```
    sudo waagent -deprovision+user
    ```
    >[AZURE.NOTE] Sólo puede ejecutar este comando en una máquina virtual que desea capturar como una imagen. No garantiza que la imagen está desactivada de toda la información confidencial o es adecuada para su redistribución.

3. Escriba **y** para continuar. Puede agregar la **-Forzar** parámetro para evitar este paso de confirmación.

4. Cuando finalice el comando, escriba **Salir**. Este paso cierra al cliente SSH.

    
## <a name="step-2-capture-the-vm"></a>Paso 2: Capturar la máquina virtual

Use la CLI de Azure para generalizar y capturar la máquina virtual. En los ejemplos siguientes, reemplace los nombres de parámetro de ejemplo con sus propios valores. Nombres de parámetro de ejemplo incluyen **myResourceGroup**, **myVnet**y **myVM**.

5. Desde el equipo local, abra la CLI de Azure e [iniciar sesión en su suscripción de Azure](../xplat-cli-connect.md). 

6. Asegúrese de que está en modo de administrador de recursos.

    ```
    azure config mode arm
    ```

7. Apagar la máquina virtual que usted ya aprovisionamiento anulado mediante el comando siguiente:

    ```
    azure vm deallocate -g MyResourceGroup -n myVM
    ```

8. Generalizar la máquina virtual con el siguiente comando:

    ```
    azure vm generalize -g MyResourceGroup -n myVM
    ```

9. Ahora, ejecute el comando de **azure vm capturar** , que captura la máquina virtual. En el ejemplo siguiente, se capturan los archivos de imagen VHD cuyos nombres empiecen por **MyVHDNamePrefix**y la opción **-t** especifica una ruta de acceso a la plantilla **MyTemplate.json**. 

    ```
    azure vm capture MyResourceGroup MyResourceGroup MyVHDNamePrefix -t MyTemplate.json
    ```

    >[AZURE.IMPORTANT]Los archivos de imagen de disco duro virtual se crean de forma predeterminada en la misma cuenta de almacenamiento que usa la máquina virtual original. Use la *misma cuenta de almacenamiento* para almacenar los archivos VHD de cualquier nuevas máquinas virtuales crear a partir de la imagen. 

6. Para buscar la ubicación de una imagen capturada, abra la plantilla JSON en un editor de texto. En la **storageProfile**, busque el **uri** de la **imagen** que se encuentra en el contenedor del **sistema** . Por ejemplo, el URI de la imagen de disco del sistema operativo es similar a`https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Paso 3: Crear una máquina virtual de la imagen capturada
Usar ahora la imagen con una plantilla para crear una VM Linux. Estos pasos le muestran cómo utilizar la CLI de Azure y la plantilla de archivo JSON que genera para crear la máquina virtual en una red virtual nueva.

### <a name="create-network-resources"></a>Crear recursos de red

Para usar la plantilla, debe configurar una red virtual y NIC para la nueva máquina virtual. Se recomienda que crear un grupo de recursos para los recursos en la ubicación donde se almacena la imagen de la máquina virtual. Ejecutar comandos similares a los siguientes, sustitución de nombres de los recursos y una ubicación de Azure adecuada ("centralus" en estos comandos):

    azure group create MyResourceGroup1 -l "centralus"

    azure network vnet create MyResourceGroup1 myVnet -l "centralus"

    azure network vnet subnet create MyResourceGroup1 myVnet mySubnet

    azure network public-ip create MyResourceGroup1 myIP -l "centralus"

    azure network nic create MyResourceGroup1 myNIC -k mySubnet -m myVnet -p myIP -l "centralus"

### <a name="get-the-id-of-the-nic"></a>Obtener el identificador de la NIC

Para implementar una máquina virtual de la imagen mediante el JSON guardado durante la captura, necesita el identificador de la NIC. Obtener ejecutando el siguiente comando:

    azure network nic show MyResourceGroup1 myNIC

El **identificador** en el resultado es similar a`/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`



### <a name="create-a-vm"></a>Crear una máquina virtual
Ahora, ejecute el comando siguiente para crear la máquina virtual de la imagen capturada de máquina virtual. Use el parámetro **-f** para especificar la ruta de acceso al archivo de JSON de plantilla que ha guardado.

    azure group deployment create MyResourceGroup1 MyDeployment -f MyTemplate.json

En el comando, se le pide que proporcione un nuevo nombre de la máquina virtual, el nombre de usuario de administrador y contraseña y el identificador de la NIC que creó anteriormente.

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    vmName: myNewVM
    adminUserName: myAdminuser
    adminPassword: ********
    networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic

En el ejemplo siguiente se muestra lo que ve para una implementación correcta:

    + Inicialización de configuraciones de la plantilla y parámetros
    + Crear una información de la implementación: creado xxxxxxx de implementación de plantilla
    + Esperando implementación completar los datos: DeploymentName: MyDeployment datos: ResourceGroupName: MyResourceGroup1 datos: ProvisioningState: datos se realizó correctamente: marca de tiempo: xxxxxxx datos: modo: datos incrementales: nombre del tipo de valor


    data:    ------------------  ------------  -------------------------------------

    datos: vmName cadena myNewVM


    datos: vmSize Standard_D1 de cadena


    datos: adminUserName cadena myAdminuser


    datos: adminPassword SecureString no definida


    datos: networkInterfaceId información de cadena /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic: implementación de grupo Crear comando Aceptar

### <a name="verify-the-deployment"></a>Comprobar la implementación

Ahora SSH a la máquina virtual que creó para comprobar la implementación y empezar a usar la nueva máquina virtual. Para conectarse a través de SSH, busque la dirección IP de la máquina virtual que ha creado, ejecute el siguiente comando:

    azure network public-ip show MyResourceGroup1 myIP

La dirección IP pública aparece en el comando. De forma predeterminada se conecta a la VM Linux por SSH en puerto 22.

## <a name="create-additional-vms"></a>Crear máquinas virtuales adicionales
Usar la imagen capturada y la plantilla para implementar máquinas virtuales adicionales siguiendo los pasos de la sección anterior. Otras opciones para crear máquinas virtuales de la imagen incluyen usando una plantilla de tutorial rápido o ejecutar el comando **crear azure vm** .

### <a name="use-the-captured-template"></a>Usar la plantilla capturada

Para usar la imagen capturada y la plantilla, siga estos pasos (que se detallan en la sección anterior):

* Asegúrese de que la imagen de la máquina virtual se encuentra en la misma cuenta de almacenamiento que hospeda disco duro virtual del VM.
* Copie el archivo de plantilla JSON y especifique un nombre único para el disco de sistema operativo del disco duro virtual de la nueva máquina virtual (o VHD). Por ejemplo, en la **storageProfile**, en el **disco duro virtual**en **uri**, especifique un nombre único para el disco duro virtual, similar a **osDisk**`https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`
* Crear una NIC en la misma u otra red virtual.
* Usa el archivo de plantilla modificada JSON, cree una implementación en el grupo de recursos en el que configurar la red virtual.

### <a name="use-a-quickstart-template"></a>Usar una plantilla de tutorial rápido

Si desea que la red configurar automáticamente cuando se crea una máquina virtual de la imagen, puede especificar los recursos en una plantilla. Por ejemplo, vea la [imagen de vm de usuario de 101 plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) GitHub. Esta plantilla crea una máquina virtual de la imagen personalizada y la red virtual necesaria, dirección IP pública y recursos NIC. Para obtener un tutorial de uso de la plantilla en el portal de Azure, consulte [cómo crear una máquina virtual de una imagen personalizada con una plantilla de administrador de recursos](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/).

### <a name="use-the-azure-vm-create-command"></a>Utilice la máquina virtual de azure comando create

Normalmente es más fácil de usar una plantilla de administrador de recursos para crear una máquina virtual de la imagen. Sin embargo, puede crear la VM _imperativamente_ mediante el comando **crear azure vm** con el **-Q** (**--imagen urn**) parámetro. Si usa este método, también pasar el parámetro **-d** (**disco--os disco duro virtual**) para especificar la ubicación del archivo .vhd OS para la nueva máquina virtual. Este archivo debe estar en el contenedor de VHD de la cuenta de almacenamiento donde se encuentra almacenado el archivo de imagen de disco duro virtual. El comando copia el disco duro virtual para la nueva máquina virtual automáticamente al contenedor **VHD** .

Antes de ejecutar **crear azure vm** con la imagen, realice los pasos siguientes:

1.  Crear un grupo de recursos o identificar un grupo de recursos existente para la implementación.

2.  Crear un recurso de dirección IP público y NIC para la nueva máquina virtual. Para que conocer los pasos crear una red virtual, dirección IP pública y NIC mediante CLI, consulte más adelante en este artículo. (**crear azure vm** también puede crear una NIC, pero debe pasar parámetros adicionales para una red virtual y subred).


A continuación, ejecutar un comando que pasa a URI para el nuevo archivo de disco duro virtual de sistema operativo y la imagen existente. En este ejemplo, se crea un tamaño de VM Standard_A1 en la región oriental US.

    azure vm create MyResourceGroup1 myNewVM eastus Linux -d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" -Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd" -z Standard_A1 -u myAdminname -p myPassword -f myNIC

Opciones de comandos adicionales, ejecute `azure help vm create`.

## <a name="next-steps"></a>Pasos siguientes

Para administrar su máquinas virtuales con CLI, consulte las tareas de [implementar y administrar máquinas virtuales mediante plantillas de administrador de recursos de Azure y Azure CLI](virtual-machines-linux-cli-deploy-templates.md).
