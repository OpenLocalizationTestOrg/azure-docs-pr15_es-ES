<properties
   pageTitle="Crear y cargar una imagen de VM FreeBSD | Microsoft Azure"
   description="Aprenda a crear y cargar un disco duro virtual (VHD) que contiene el sistema operativo FreeBSD para crear una máquina virtual de Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/29/2016"
   ms.author="kyliel"/>

# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>Crear y cargar un VHD FreeBSD en Azure

Este artículo le muestra cómo crear y cargar un disco duro virtual (VHD) que contiene el sistema operativo FreeBSD. Después de cargarlo, puede utilizar como su propia imagen para crear una máquina virtual (VM) en Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que tiene los siguientes elementos:

- **Suscripción de un Azure**--si no tiene una cuenta, puede crear uno de dos minutos. Si tiene una suscripción a MSDN, consulte [crédito Azure mensuales para los suscriptores de Visual Studio.](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). En caso contrario, obtenga información sobre cómo [crear una cuenta de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).  

- **Herramientas de azure PowerShell**--PowerShell de Azure el módulo debe estar instalado y configurado para usar su suscripción de Azure. Para descargar el módulo, vea [descargas de Azure](https://azure.microsoft.com/downloads/). Un tutorial que describe cómo instalar y configurar el módulo está disponible aquí. Usar el cmdlet [Azure descargas](https://azure.microsoft.com/downloads/) para cargar el disco duro virtual.

- **Sistema operativo de FreeBSD instalado en un archivo .vhd**--un compatibles FreeBSD sistema operativo debe estar instalado en un disco duro virtual. Existen varias herramientas para crear archivos .vhd. Por ejemplo, puede usar una solución de virtualización como Hyper-V para crear el archivo .vhd e instalar el sistema operativo. Para obtener instrucciones sobre cómo instalar y usar Hyper-V, vea [instalar Hyper-V y crear una máquina virtual](http://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] El formato más reciente de VHDX no es compatible con Azure. Puede convertir el disco en formato de disco duro virtual mediante el Administrador de Hyper-V o el cmdlet [disco duro virtual de convertir](https://technet.microsoft.com/library/hh848454.aspx). Además, hay un [tutorial en MSDN sobre cómo usar FreeBSD con Hyper-V](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).

Esta tarea incluye los cinco pasos siguientes.

## <a name="step-1-prepare-the-image-for-upload"></a>Paso 1: Preparar la imagen para cargar

En la máquina virtual donde ha instalado el sistema operativo FreeBSD, complete los siguientes procedimientos:

1. Habilitar DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart

2. Activar el SSH.

    SSH está activado de forma predeterminada después de la instalación desde disco. Si aún no está habilitado por algún motivo, o si usa FreeBSD VHD directamente, escriba lo siguiente:

        # echo 'sshd_enable="YES"' >> /etc/rc.conf
        # ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
        # ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
        # service sshd restart

3. Configurar una consola de serie.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf

4. Instale sudo.

    La cuenta raíz está deshabilitada en Azure. Esto significa que necesita para utilizar sudo de un usuario para que se ejecute comandos con privilegios elevados.

        # pkg install sudo
;
5. Requisitos previos para agente de Azure.

        # pkg install python27  
        # pkg install Py27-setuptools27   
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git

6. Instalar a agente de Azure.

    La versión más reciente del agente de Azure siempre se encuentra en [github](https://github.com/Azure/WALinuxAgent/releases). La versión 2.0.10 + oficialmente es compatible con FreeBSD 10 y 10.1 y la versión 2.1.4 admite oficialmente FreeBSD 10.2 y versiones posteriores.

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    2.0, usaremos 2.0.16 como ejemplo:

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    2.1, usaremos 2.1.4 como ejemplo:

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

    >[AZURE.IMPORTANT] Después de instalar al agente de Azure, es una buena idea comprobar que se está ejecutando:

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # service –e | grep waagent
        /etc/rc.d/waagent
        # cat /var/log/waagent.log

7. Activar el sistema.

    Activar el sistema para limpiar y sea adecuado para su configuración. El comando siguiente también elimina la última cuenta de usuario de preparación y los datos asociados:

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    Ahora puede apagar la máquina virtual.

## <a name="step-2-create-a-storage-account-in-azure"></a>Paso 2: Crear una cuenta de almacenamiento en Azure ##

Se necesita una cuenta de almacenamiento en Azure para cargar un archivo .vhd, por lo que puede utilizar para crear una máquina virtual. Puede usar el portal clásico Azure para crear una cuenta de almacenamiento.

1. Inicie sesión en el [portal de clásico de Azure](https://manage.windowsazure.com).

2. En la barra de comandos, seleccione **nuevo**.

3. Seleccione los **Servicios de datos** > **almacenamiento** > **crear rápido**.

    ![Crear una cuenta de almacenamiento de rápido](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-quick-create.png)

4. Rellene los campos como sigue:

    - En el campo **URL** , escriba un nombre de subdominio a usar en la URL de la cuenta de almacenamiento. La entrada puede contener de 3-24 números y letras en minúscula. Este nombre se convierte en el nombre de host dentro de la dirección URL que se utiliza para dirigir el almacenamiento de blobs de Windows Azure, almacenamiento Azure cola o recursos de almacenamiento de tablas de Azure para la suscripción.

    - En el menú desplegable de **Afinidad de ubicación o grupo** , seleccione la **ubicación o el grupo de afinidad** para la cuenta de almacenamiento. Un grupo de afinidad le permite poner los servicios de nube y el almacenamiento en el mismo centro de datos.

    - En el campo de **replicación** decidir si usar replicación **Geo redundantes** de la cuenta de almacenamiento. Replicación geo está activada de forma predeterminada. Esta opción aplica los datos a una ubicación secundaria, sin costo, para que el almacenamiento por error a esa ubicación si se produce un error importante en la ubicación primaria. La ubicación secundaria se asigna automáticamente y no se puede cambiar. Si necesita tener más control sobre la ubicación del almacenamiento en la nube debido a requisitos legales o directivas de su organización, puede desactivar la replicación geo. Sin embargo, tenga en cuenta que si decide activar más tarde geo replicación, se cargará una cuota de transferencia de datos de una sola vez para replicar los datos existentes en la ubicación secundaria. Servicios de almacenamiento sin replicación geo se ofrecen con un descuento. Pueden encontrar más detalles acerca de cómo administrar geo replicación de cuentas de almacenamiento aquí: [crear, administrar, o eliminar una cuenta de almacenamiento](../storage-create-storage-account/#replication-options).

    ![Escriba los detalles de la cuenta de almacenamiento](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-create-account.png)


5. Seleccione **crear cuenta de almacenamiento**. La cuenta aparece ahora en el **almacenamiento**.

    ![Cuenta de almacenamiento creado correctamente](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storagenewaccount.png)

6. A continuación, cree un contenedor para los archivos cargados .vhd. Seleccione el nombre de la cuenta de almacenamiento y, a continuación, seleccione **contenedores**.

    ![Detalles de la cuenta de almacenamiento](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_detail.png)

7. Seleccione **crear un contenedor**.

    ![Detalles de la cuenta de almacenamiento](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_container.png)

8. En el campo **nombre** , escriba un nombre para el contenedor. A continuación, en el menú desplegable de **acceso** , seleccione el tipo de directiva de acceso que desea.

    ![Nombre del contenedor](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_containervalues.png)

    > [AZURE.NOTE] De forma predeterminada, el contenedor es privado y solo se puede acceder al propietario de la cuenta. Para permitir el acceso de lectura público a la BLOB en el contenedor, pero no a las propiedades de contenedor y metadatos, use la opción de **Blobs público** . Para permitir el acceso de lectura público completa para el contenedor y BLOB, use la opción de **Contenedor público** .

## <a name="step-3-prepare-the-connection-to-azure"></a>Paso 3: Preparar la conexión de Azure

Antes de cargar un archivo .vhd, debe establecer una conexión segura entre el equipo y su suscripción de Azure. Puede usar el método de Azure Active Directory (AD Azure) o el certificado para realizar esta acción.

### <a name="use-the-azure-ad-method-to-upload-a-vhd-file"></a>Use el método de Azure AD para cargar un archivo .vhd

1. Abra la consola de PowerShell de Azure.

2. Escriba el siguiente comando:  
    `Add-AzureAccount`

    Este comando abre una ventana de inicio de sesión donde pueden iniciar sesión con su cuenta profesional o educativa.

    ![Ventana de PowerShell](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/add_azureaccount.png)

3. Azure autentica y guarda la información de credenciales. A continuación, cierra la ventana.

### <a name="use-the-certificate-method-to-upload-a-vhd-file"></a>Use el método de certificado para cargar un archivo .vhd

1. Abra la consola de PowerShell de Azure.

2. Tipo:  `Get-AzurePublishSettingsFile`.

3. Una ventana del explorador se abrirá y le pide que descargue un archivo de .publishsettings. Este archivo contiene información y un certificado para la suscripción de Azure.

    ![Página de descarga del explorador](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)

3. Guarde el archivo .publishsettings.

4. Tipo:  `Import-AzurePublishSettingsFile <PathToFile>`, donde `<PathToFile>` es la ruta completa al archivo .publishsettings.

   Para obtener más información, vea [Introducción a los cmdlets de Azure](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx).

   Para obtener más información sobre la instalación y configuración de PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

## <a name="step-4-upload-the-vhd-file"></a>Paso 4: Cargue el archivo .vhd

Al cargar el archivo .vhd, se puede colocar en cualquier lugar en el almacenamiento de blobs de Windows. Los siguientes son algunos de los términos que usará al cargar el archivo:
-  **BlobStorageURL** es la dirección URL de la cuenta de almacenamiento que ha creado en el paso 2.
-  **YourImagesFolder** es el contenedor de almacenamiento de blobs de Windows donde desea almacenar las imágenes.
- **VHDName** es la etiqueta que aparece en el portal de Azure clásico para identificar el disco duro virtual.
- **PathToVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd.


Desde la ventana de PowerShell de Azure que utilizó en el paso anterior, escriba:

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-5-create-a-vm-with-the-uploaded-vhd-file"></a>Paso 5: Crear una máquina virtual con el archivo .vhd cargados
Después de cargar el archivo .vhd, puede agregar una imagen a la lista de imágenes personalizadas que están asociadas a su suscripción y crear una máquina virtual con esta imagen personalizada.

1. Desde la ventana de PowerShell de Azure que utilizó en el paso anterior, escriba:

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

    > [AZURE.NOTE]Usar Linux como el tipo de sistema operativo. La versión actual de PowerShell de Azure acepta "solo Linux" o "Windows" como parámetro.

2. Después de completar los pasos anteriores, la nueva imagen aparece al elegir la pestaña **imágenes** en el portal de clásico de Azure.  

    ![Elegir una imagen](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/addfreebsdimage.png)

3. Crear una máquina virtual de la galería. Esta nueva imagen está ahora disponible en **Mis imágenes**.
4. Seleccione la nueva imagen. A continuación, vaya a través de las indicaciones para configurar un nombre de host, la contraseña, SSH clave y así sucesivamente.

    ![Imagen personalizada](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/createfreebsdimageinazure.png)

4. Después de completar el proceso de aprovisionamiento, verá su VM FreeBSD ejecuta en Azure.

    ![Imagen de FreeBSD en azure](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/freebsdimageinazure.png)
