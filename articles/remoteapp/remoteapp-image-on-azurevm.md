<properties
    pageTitle="Crear una imagen de Azure RemoteApp basándose en una máquina virtual de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo crear una imagen para Azure RemoteApp a partir de una máquina virtual Azure."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="create-a-azure-remoteapp-image-based-on-an-azure-virtual-machine"></a>Crear una imagen de Azure RemoteApp basada en una máquina virtual de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Puede crear imágenes de Azure RemoteApp (que mantenga las aplicaciones que compartir en la colección) desde una máquina virtual Azure. Se puede usar una imagen de máquina virtual que hemos agregado a la Galería de imágenes de Azure VM que cumpla los requisitos de imagen de Azure RemoteApp: puede usar dicha imagen VM como punto de partida para su propia VM, si lo desea. Busque la imagen de "Windows Server escritorio remoto" en la biblioteca.

Hay dos pasos para crear su propia imagen basada en una máquina virtual de Azure: crear la imagen y luego cárguelo desde la biblioteca de Azure VM en Azure RemoteApp.

## <a name="create-a-custom-image-based-on-an-azure-vm"></a>Crear una imagen personalizada basada en una máquina virtual de Azure

Siga estos pasos para crear una imagen basada en una máquina virtual de Azure.

1. Crear una máquina virtual Azure. Puede utilizar el "Windows Server escritorio remoto" o la imagen "Windows Server Remote Desktop sesión Host con Microsoft Office 365 ProPlus" de la Galería de imágenes de Azure máquina virtual. Esta imagen cumple todos los requisitos de imagen de plantilla de RemoteApp de Azure.

    Para obtener información detallada, vea [crear una máquina virtual con Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md).

2. Conectarse a la máquina virtual e instalar y configurar las aplicaciones que desea compartir a través de RemoteApp. Asegúrese de realizar las configuraciones de Windows adicionales necesarios para que las aplicaciones.

    Para obtener más información, vea [cómo iniciar sesión en una máquina Virtual que ejecuta Windows Server](../virtual-machines/virtual-machines-windows-classic-connect-logon.md).

3. Si está utilizando una de las imágenes de Host de sesión de escritorio remoto de Windows Server, hay una secuencia de comandos de validación incluyen que garantizará que su VM cumpla la RemoteApp pre-reqs. Para ejecutar la secuencia de comandos, haga doble clic en **ValidateRemoteAppImage** en el escritorio. Asegúrese de que todos los errores notificados por la secuencia de comandos se corrigen antes de continuar con el paso siguiente.

4. SYSPREP generalizar y capturar la imagen. Vea [cómo capturar una máquina Virtual de Windows para usar como una plantilla](../virtual-machines/virtual-machines-windows-classic-capture-image.md) para obtener instrucciones.



## <a name="import-the-image-into-the-azure-remoteapp-image-library"></a>Importar la imagen en la biblioteca de imágenes de RemoteApp de Azure

Siga estos pasos para importar la nueva imagen RemoteApp de Azure:

1. En la pestaña **Imágenes de plantilla** :
    - Si no tiene ninguna imagen existente, haga clic en **cargar o importar una imagen de la plantilla**.
    - Si ya tiene al menos una imagen, haga clic en **+** para agregar una nueva imagen.

2. Seleccione **importar una imagen desde su máquinas virtuales** biblioteca y, a continuación, haga clic en **siguiente**.

3. En la siguiente página, seleccione la imagen personalizada de la lista y compruebe que ha seguido los pasos que aparecen al crear la imagen. Haga clic en **siguiente**.
4. Escriba un nombre para la nueva imagen RemoteApp y seleccione la ubicación y luego haga clic en la marca de verificación para iniciar el proceso de importación.

> [AZURE.NOTE] Puede importar imágenes desde cualquier lugar de Azure compatible con Azure máquinas virtuales de Windows en cualquier ubicación de Azure compatible con RemoteApp de Azure. Dependiendo de las ubicaciones de la importación puede tardar hasta 25 minutos.

Ahora está listo para crear la nueva colección, ya sea una colección de [nube](remoteapp-create-cloud-deployment.md) o [híbrido](remoteapp-create-hybrid-deployment.md), según sus necesidades.
