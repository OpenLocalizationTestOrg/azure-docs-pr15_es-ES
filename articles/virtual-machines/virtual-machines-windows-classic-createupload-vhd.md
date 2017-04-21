<properties
    pageTitle="Crear y cargar una imagen de máquina virtual con Powershell | Microsoft Azure"
    description="Aprenda a crear y cargar una imagen de Windows Server generalized (disco duro virtual) mediante el modelo de implementación clásica y Powershell de Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/21/2016"
    ms.author="cynthn"/>

# <a name="create-and-upload-a-windows-server-vhd-to-azure"></a>Crear y cargar un disco duro virtual de Windows Server en Azure

Este artículo le muestra cómo cargar su propia imagen VM generalizado como un disco duro virtual (VHD), por lo que puede usar para crear máquinas virtuales de Windows. Para obtener más detalles sobre discos y VHD en Microsoft Azure, consulte [acerca de los discos y VHD para máquinas virtuales](virtual-machines-linux-about-disks-vhds.md).


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]. También puede [cargar](virtual-machines-windows-upload-image.md) una máquina virtual mediante el modelo de administrador de recursos. 

## <a name="prerequisites"></a>Requisitos previos

En este artículo se supone que:

- **Un Azure suscripción** : si no tiene una, puede [Abrir una cuenta de Azure de forma gratuita](/pricing/free-trial/?WT.mc_id=A261C142F).

- **[Microsoft Azure PowerShell](../powershell-install-configure.md)** - tiene el módulo de PowerShell de Microsoft Azure instalado y configurado para usar su suscripción. 

- **A . Archivo de disco duro virtual** - compatibles con almacenados en un archivo .vhd y conectado a una máquina virtual de sistema operativo Windows. Compruebe si las funciones de servidor que se ejecuta en el disco duro virtual son compatibles con Sysprep. Para obtener más información, vea [Compatibilidad con Sysprep Roles del servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [AZURE.IMPORTANT] El formato VHDX no es compatible con Microsoft Azure. Puede convertir el disco en formato de disco duro virtual con el Administrador de Hyper-V o el [disco duro virtual de convertir cmdlet](http://technet.microsoft.com/library/hh848454.aspx). Para obtener información detallada, consulte este [blogpost](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## <a name="step-1-prep-the-vhd"></a>Paso 1: Preparar el disco duro virtual 

Antes de cargar el disco duro virtual en Azure, debe ser generalizado mediante la herramienta Sysprep. Esto prepara el disco duro virtual que se utilizará como una imagen. Para obtener más información acerca de Sysprep, vea [cómo usar Sysprep: Introducción](http://technet.microsoft.com/library/bb457073.aspx). Copia de la máquina virtual antes de ejecutar Sysprep.

Desde el equipo virtual que se instaló el sistema operativo, siga el procedimiento siguiente:

1. Inicie sesión en el sistema operativo.

2. Abra una ventana de símbolo del sistema como administrador. Cambie el directorio a **%windir%\system32\sysprep**y luego ejecute `sysprep.exe`.

    ![Abra una ventana del símbolo del sistema](./media/virtual-machines-windows-classic-createupload-vhd/sysprep_commandprompt.png)

3.  Aparece el cuadro de diálogo de la **Herramienta de preparación del sistema** .

    ![Iniciar Sysprep](./media/virtual-machines-windows-classic-createupload-vhd/sysprepgeneral.png)

4.  En la **Herramienta de preparación del sistema**, seleccione **Escriba sistema fuera de cuadro experiencia (configuración rápida)** y asegúrese de que está activada **Generalize** .

5.  En **Opciones de cierre**, seleccione **Cerrar**.

6.  Haga clic en **Aceptar**.

## <a name="step-2-create-a-storage-account-and-a-container"></a>Paso 2: Crear una cuenta de almacenamiento y un contenedor

Se necesita una cuenta de almacenamiento en Azure para que tenga un lugar para cargar el archivo .vhd. Este paso muestra cómo crear una cuenta, u obtenga la información que necesita desde una cuenta existente. Reemplace las variables de &lsaquo; corchetes &rsaquo; con su propia información.

1. Inicio de sesión

        Add-AzureAccount

1. Configurar la suscripción de Azure.

        Select-AzureSubscription -SubscriptionName <SubscriptionName> 

2. Crear una nueva cuenta de almacenamiento. El nombre de la cuenta de almacenamiento debe ser único, 3-24 caracteres. El nombre puede ser cualquier combinación de letras y números. También debe especificar una ubicación como "Oriente nosotros"
        
        New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>

3. Establecer la nueva cuenta de almacenamiento como el valor predeterminado.
        
        Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>

4. Crear un nuevo contenedor.

        New-AzureStorageContainer -Name <ContainerName> -Permission Off

 

## <a name="step-3-upload-the-vhd-file"></a>Paso 3: Cargar el archivo .vhd

Usar el [Complemento AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) para cargar el disco duro virtual.

En la ventana de PowerShell de Azure que usó en el paso anterior, escriba el siguiente comando y reemplace las variables de &lsaquo; corchetes &rsaquo; con su propia información.

        Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>


## <a name="step-4-add-the-image-to-your-list-of-custom-images"></a>Paso 4: Agregar la imagen a la lista de imágenes personalizadas

Usar el cmdlet [AzureVMImage agregar](https://msdn.microsoft.com/library/mt589167.aspx) para agregar la imagen a la lista de sus imágenes personalizadas.

        Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"


## <a name="next-steps"></a>Pasos siguientes

Puede [crear una máquina virtual personalizada](virtual-machines-windows-classic-createportal.md) con la imagen que ha cargado.

