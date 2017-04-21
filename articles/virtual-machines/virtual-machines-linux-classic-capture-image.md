<properties
    pageTitle="Capturar una imagen de una VM Linux | Microsoft Azure"
    description="Aprenda a capturar una imagen de una basados en Linux Azure máquina virtual (VM) creada con el modelo de implementación clásico."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="iainfou"/>


# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Cómo capturar una máquina virtual de Linux clásica como una imagen

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Obtenga información sobre cómo llevar a [cabo estos pasos con el modelo de administrador de recursos](virtual-machines-linux-capture-image.md).

En este artículo se muestra cómo capturar una clásica máquina virtual Azure con Linux como una imagen para crear otros equipos virtuales. Esta imagen incluye el disco de sistema operativo y los discos de datos adjuntados a la máquina virtual. No incluye configuración de red, por lo que necesitará configurar al crear las demás máquinas virtuales de la imagen.

Azure almacena la imagen en **las imágenes**, junto con las imágenes que haya cargado. Para obtener más información acerca de las imágenes, consulte [Acerca de imágenes de máquina Virtual en Azure] [].

## <a name="before-you-begin"></a>Antes de empezar

El procedimiento siguiente asume que ya ha creado una máquina virtual Azure utiliza el modelo de implementación estándar y ha configurado el sistema operativo, incluidos asociar los discos de datos. Si necesita crear una máquina virtual, lea [cómo crear una máquina Virtual Linux] [].


## <a name="capture-the-virtual-machine"></a>Capturar la máquina virtual

1. [Conectarse a la máquina virtual](virtual-machines-linux-mac-create-ssh-keys.md) mediante un cliente SSH de su elección.

2. En la ventana SSH, escriba el siguiente comando. El resultado de `waagent` puede variar ligeramente dependiendo de la versión de esta herramienta:

    `sudo waagent -deprovision+user`

    Este comando intenta eliminar del sistema y sea adecuado para reaprovisionamiento. Esta operación realiza las siguientes tareas:

    - Quita las claves de host SSH (si Provisioning.RegenerateSshHostKeyPair es 'y' en el archivo de configuración)
    - Borra la configuración del servidor de nombres en /etc/resolv.conf
    - Quita el `root` contraseña de usuario de/etcetera/sombra (si Provisioning.DeleteRootPassword es 'y' en el archivo de configuración)
    - Quita caché concesiones de cliente
    - Nombre de host se restablecerán localhost.localdomain
    - Elimina el último usuario aprovisiona cuenta (que se obtienen de /var/lib/waagent) **y datos asociados**.

    >[AZURE.NOTE] Baja elimina los archivos y datos "generalizar" la imagen. Sólo puede ejecutar este comando en una máquina virtual que desea capturar como una nueva plantilla de imagen. No garantiza que la imagen está desactivada de toda la información confidencial o es adecuada para su redistribución a terceros.


3. Escriba **y** para continuar. Puede agregar la `-force` parámetro para evitar este paso de confirmación.

4. Escriba **Salir** para cerrar al cliente SSH.

    >[AZURE.NOTE] Los pasos siguientes se suponen que ya ha [instalado la CLI de Azure](../xplat-cli-install.md) en el equipo cliente. Los siguientes pasos también pueden realizarse en el [portal de clásico de Azure] [].

5. Desde el equipo cliente, abra Azure CLI e iniciar sesión en su suscripción de Azure. Para obtener más información, lea [conectarse a una suscripción de Azure desde la CLI de Azure](../xplat-cli-connect.md).

6. Asegúrese de que está en modo de administración de servicios:

    `azure config mode asm`

7. Apagar la máquina virtual que ya se desactivan en los pasos anteriores con:

    `azure vm shutdown <your-virtual-machine-name>`

    >[AZURE.NOTE] Puede averiguar todas las máquinas virtuales creadas en su suscripción con`azure vm list`

8. Cuando se detiene la máquina virtual, capturar la imagen con el comando:

    `azure vm capture -t <your-virtual-machine-name> <new-image-name>`

    Escriba el nombre de la imagen que desee en lugar de _nuevo nombre de imagen_. Este comando crea una imagen de sistema operativo generalizada. La `-t` subcomando elimina la máquina virtual original.

9.  La nueva imagen ahora está disponible en la lista de imágenes que se pueden usar para configurar las nuevas máquinas virtuales. Puede verla con el comando:

    `azure vm image list`

    En el [portal de clásica Azure] [], aparece en la lista de **imágenes** .

    ![Captura de la imagen correcta](./media/virtual-machines-linux-classic-capture-image/VMCapturedImageAvailable.png)


## <a name="next-steps"></a>Pasos siguientes
La imagen está lista para usarse para crear máquinas virtuales. Puede usar el comando de Azure CLI `azure vm create` y proporcionar el nombre de la imagen que creó. Para obtener más información acerca del comando, consulte [mediante la CLI de Azure con modelo de implementación de clásico](../virtual-machines-command-line-tools.md) . Como alternativa, use el [portal clásica Azure] [] para crear una máquina virtual personalizada utilizando el método **De la Galería** y seleccionando la imagen que ha creado. Para obtener más detalles, consulte [cómo crear una máquina Virtual de personalizado] [] .

**Vea también:** [Guía de usuario de Azure agente Linux](virtual-machines-linux-agent-user-guide.md)

[Portal de clásico de Azure]: http://manage.windowsazure.com
[Acerca de las imágenes de máquina Virtual de Azure]: virtual-machines-linux-classic-about-images.md
[Cómo crear una máquina Virtual personalizada]: virtual-machines-linux-classic-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]: virtual-machines-windows-classic-attach-disk.md
[Cómo crear una máquina Virtual Linux]: virtual-machines-linux-classic-create-custom.md
