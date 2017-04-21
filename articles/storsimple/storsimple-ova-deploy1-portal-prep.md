<properties
   pageTitle="Implementar StorSimple Virtual matriz 1: preparación del Portal"
   description="Primer tutorial para implementar StorSimple matriz virtual implica la preparación del portal"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/24/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---prepare-the-portal"></a>Implementar StorSimple Virtual matriz: preparar el portal

![](./media/storsimple-ova-deploy1-portal-prep/getstarted4.png)

## <a name="overview"></a>Información general

En este artículo se aplica a la versión de Microsoft Azure StorSimple matriz Virtual (también conocido como el dispositivo virtual de StorSimple local o dispositivo virtual de StorSimple) ejecución marzo de 2016 disponibilidad general (GA). Este es el primer artículo de la serie de tutoriales de implementación necesarias para implementar completamente la matriz virtual como un archivo o un servidor de iSCSI. En este artículo se describe la preparación necesaria para crear y configurar el servicio de administrador de StorSimple antes de hacer el aprovisionamiento de una matriz virtual. En este artículo también vínculos fuera a una lista de comprobación de configuración de implementación, así como la configuración de los requisitos previos.

Necesitará privilegios de administrador para completar el proceso de instalación y configuración. Le recomendamos que revise la lista de comprobación de configuración de implementación antes de empezar. La preparación del portal tendrá menos de 10 minutos.

La información publicada en este artículo se aplica a la implementación de StorSimple Virtual matrices en Azure portal clásica, así como la nube de Microsoft Azure administración pública.

### <a name="get-started"></a>Introducción

El flujo de trabajo de implementación consta de preparar el portal, el aprovisionamiento de una matriz virtual en su entorno virtualizado y completar la instalación. Para empezar con la implementación de matriz Virtual de StorSimple como un archivo o un servidor iSCSI, debe hacer referencia a los siguientes recursos tabulados (artículos y vídeos).

#### <a name="deployment-articles"></a>Artículos de implementación

Consulte los artículos siguientes en la secuencia indicada para implementar la matriz Virtual de StorSimple.

| **#** | **En este paso**                          | **Va a hacer esto...**                                                         | **Use estos documentos.**|
|------|-------------------------------------------|--------------------------------------------------------------------------------|------------------------|
|1.   | **Configurar el portal de clásico de Azure**       | Crear y configurar el servicio de administrador de StorSimple antes de hacer el aprovisionamiento de un dispositivo virtual de StorSimple.  |[Preparar el portal](storsimple-ova-deploy1-portal-prep.md)|
|2.   | **Aprovisionar la matriz Virtual**           | Hyper-v, aprovisionar y conectarse a un dispositivo virtual StorSimple en un sistema de host ejecuta Hyper-V en Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2. <br></br> <br></br> Para VMware, aprovisionar y conectarse a StorSimple local dispositivo virtual en un sistema de host ejecuta VMware ESXi 5.5 y versiones posteriores.<br></br>| [Aprovisionar una matriz virtual en Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) <br></br> <br></br> [Aprovisionar una matriz virtual en VMware](storsimple-ova-deploy2-provision-vmware.md)|
|3.    | **Configurar la matriz Virtual**              | El servidor de archivos, realizar la configuración inicial, registrar su servidor de archivos de StorSimple y completar la configuración de dispositivo. A continuación, puede aprovisionar recursos compartidos de SMB. <br></br> <br></br> Para su servidor iSCSI, realizar la configuración inicial, registrar su servidor de iSCSI StorSimple y completar la configuración del dispositivo. A continuación, puede aprovisionar volúmenes iSCSI.| [Configurar matriz virtual como servidor de archivos](storsimple-ova-deploy3-fs-setup.md)<br></br> <br></br>[Configurar matriz virtual como servidor iSCSI](storsimple-ova-deploy3-iscsi-setup.md)|

#### <a name="deployment-videos"></a>Vídeos de implementación

| **Para realizar este paso...** |  **Vea este vídeo.**|
|----------------|-------------|
| Instrucciones paso a paso para empezar con la matriz Virtual StorSimple. | [Introducción a la matriz Virtual StorSimple](https://azure.microsoft.com/documentation/videos/get-started-with-the-storsimple-virtual-array/)|
| Instrucciones paso a paso para aprovisionar una matriz Virtual StorSimple en Hyper-V.|[Crear una matriz de StorSimple Virtual.](https://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-array/) |
|Instrucciones paso a paso para configurar y registrar una matriz Virtual de StorSimple|[Configurar una matriz de StorSimple Virtual.](https://azure.microsoft.com/documentation/videos/configure-a-storsimple-virtual-array/)|
|Instrucciones paso a paso para crear recursos compartidos, realizar copias de seguridad de recursos compartidos y restaurar los datos en una matriz Virtual StorSimple está configurado como un servidor de archivos|[Utilice la matriz Virtual StorSimple](https://azure.microsoft.com/documentation/videos/use-the-storsimple-virtual-array/)|
|Instrucciones paso a paso para la migración tras error y recuperación de desastres de una matriz de StorSimple Virtual.|[StorSimple de recuperación de matriz Virtual](https://azure.microsoft.com/documentation/videos/storsimple-virtual-array-disaster-recovery/)

Ahora puede empezar a configurar el portal clásico Azure.

## <a name="configuration-checklist"></a>Lista de comprobación de configuración

La lista de comprobación de configuración describe la información que debe recopilar antes de configurar el software en el dispositivo StorSimple. Preparar esta información con antelación le ayudará a simplificar el proceso de implementación del dispositivo de StorSimple en su entorno. Dependiendo de si el dispositivo virtual de StorSimple se implementará como un archivo o un servidor de iSCSI, necesitará una de las siguientes listas de comprobación.

-   Descargar la [lista de comprobación de la configuración de servidor de StorSimple matriz Virtual archivo](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).

-   Descargue la [matriz Virtual de StorSimple iSCSI lista de comprobación de configuración de servidor](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Requisitos previos

Aquí encontrará los requisitos previos de configuración del servicio de administrador de StorSimple, el dispositivo virtual de StorSimple y la red del centro de datos.

### <a name="for-the-storsimple-manager-service"></a>Para el servicio Administrador de StorSimple

Antes de empezar, asegúrese de:

-   Tiene su cuenta de Microsoft con las credenciales de acceso.

-   Tiene su cuenta de almacenamiento de Microsoft Azure con credenciales de acceso.

-   Su suscripción de Microsoft Azure debe estar habilitada para servicio Administrador de StorSimple.

### <a name="for-the-storsimple-virtual-device"></a>Para el dispositivo virtual de StorSimple

Antes de implementar un dispositivo virtual, asegúrese de:

-   Tiene acceso a un sistema de host ejecuta Hyper-V en Windows Server 2008 R2 o posterior o sus (ESXi 5,5 o posterior) que puede ser utilizado para una disposición en un dispositivo.

-   El sistema host es capaz de que se dedicará a los siguientes recursos para configurar el dispositivo virtual:

    -   Un mínimo de 4 núcleos.

    -   Al menos 8 GB de RAM.

    -   Una interfaz de red.

    -   Un disco virtual de 500 GB para los datos del sistema.

### <a name="for-the-datacenter-network"></a>Para la red de centro de datos

Antes de empezar, asegúrese de:

-   La red de su centro de datos está configurada según los requisitos de red de su dispositivo StorSimple. Para obtener más información, consulte los [Requisitos del sistema de matriz Virtual de StorSimple](storsimple-ova-system-requirements.md).

-   El dispositivo virtual de StorSimple tiene un ancho de banda de Internet Mbps 5 dedicado (o más) disponible en todo momento. Este ancho de banda no debe compartirse con otras aplicaciones.

## <a name="step-by-step-preparation"></a>Preparación del paso a paso

Utilice las siguientes instrucciones paso a paso para preparar su portal para el servicio Administrador de StorSimple.

## <a name="step-1-create-a-new-service"></a>Paso 1: Crear un nuevo servicio

Una sola instancia del servicio de StorSimple administrador puede administrar varios dispositivos StorSimple 1200. Realice los pasos siguientes para crear una nueva instancia del servicio de administrador de StorSimple. Si tiene un servicio de administrador de StorSimple existente para administrar los dispositivos de 1200, omita este paso y vaya a [paso 2: obtener la clave de registro del servicio](#step-2-get-the-service-registration-key).

[AZURE.INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

> [AZURE.IMPORTANT]
>
> Si no habilitó la creación automática de una cuenta de almacenamiento con el servicio, debe crear al menos una cuenta de almacenamiento después de haber creado correctamente un servicio.
>

> - Si no ha creado una cuenta de almacenamiento automáticamente, vaya a [Configurar una nueva cuenta de almacenamiento para el servicio](#optional-step-configure-a-new-storage-account-for-the-service) para obtener instrucciones detalladas.
>

> - Si habilita la creación automática de una cuenta de almacenamiento, vaya a [paso 2: obtener la clave de registro del servicio](#step-2-get-the-service-registration-key).


## <a name="step-2-get-the-service-registration-key"></a>Paso 2: Obtener la clave de registro de servicio


Después de configurar y ejecutar el servicio Administrador de StorSimple, debe obtener la clave de registro de servicio. Esta clave se usa para registrar y conectar el dispositivo de StorSimple con el servicio.

Realice los pasos siguientes en el [portal de clásico de Azure](https://manage.windowsazure.com/).


[AZURE.INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

> [AZURE.NOTE]
>
> La clave de registro de servicio se utiliza para registrar todos los dispositivos de StorSimple Manager que necesitan registrar con el servicio de administrador de StorSimple.

## <a name="step-3-download-the-virtual-device-image"></a>Paso 3: Descargar la imagen de dispositivo virtual

Después de que tiene la clave de registro de servicio, debe descargar la imagen de dispositivo virtual apropiado para proporcionar un dispositivo virtual en su sistema de host. Las imágenes de dispositivo virtual son específicos del sistema operativo y pueden descargarse desde la página de inicio rápido en el portal de clásico de Azure.

> [AZURE.IMPORTANT] El software que se ejecuta en la matriz Virtual StorSimple sólo puede utilizarse junto con el servicio Administrador de Storsimple.


Realice los pasos siguientes en el [portal de clásico de Azure](https://manage.windowsazure.com/).

#### <a name="to-get-the-virtual-device-image"></a>Para obtener la imagen de dispositivo virtual

1.  En la página **servicio StorSimple administrador** , haga clic en el servicio que ha creado. Esto le llevará a la página de **Inicio rápido** . (Puede hacer clic en el icono de inicio rápido ![](./media/storsimple-ova-deploy1-portal-prep/image8.png) acceso a la página de **Inicio rápido** en cualquier momento.)

1.  Haga clic en el vínculo correspondiente a la imagen que desea descargar desde Microsoft Download Center. Los archivos de imagen son aproximadamente 4,8 GB.

    -   VHDX Hyper-v en Windows Server 2012 y versiones posteriores

    -   Disco duro virtual para Hyper-V en Windows Server 2008 R2 y versiones posteriores

    -   VMDK VMWare ESXi 5.5 y versiones posteriores

2.  Descargue y descomprima el archivo en una unidad local, hacer que una nota de donde se encuentra el archivo descomprimido.

![icono de vídeo](./media/storsimple-ova-deploy1-portal-prep/video_icon.png) **vídeo disponible**

Vea el vídeo para obtener instrucciones paso a paso empezar con la matriz Virtual StorSimple.

> [AZURE.VIDEO get-started-with-the-storsimple-virtual-array]



## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Como paso opcional: configurar una nueva cuenta de almacenamiento para el servicio

Se trata de un paso opcional que debe realizarse únicamente si no habilitó la creación automática de una cuenta de almacenamiento con el servicio.

Si necesita crear una cuenta de almacenamiento de Azure en una región diferente, consulte [cómo crear una cuenta de almacenamiento](storage-create-storage-account.md#create-a-storage-account) para obtener instrucciones paso a paso.

Realice los pasos siguientes en el [portal clásica Azure](https://manage.windowsazure.com/) en la página de servicio del Administrador de StorSimple para agregar una cuenta de almacenamiento de Microsoft Azure existente.

#### <a name="to-add-a-storage-account"></a>Para agregar una cuenta de almacenamiento

1.  En el servicio de administrador de StorSimple página de inicio, seleccione el servicio y haga doble clic en él. Esto le llevará a la página de **Inicio rápido** . Seleccione la página de **configuración** .

2.  Haga clic en **Agregar/Editar cuenta de almacenamiento**. En el cuadro de diálogo **Agregar/Editar cuenta de almacenamiento** , haga lo siguiente:

    1.  Haga clic en **Agregar nuevo**.

    1.  Proporcione un nombre para su cuenta de almacenamiento.

    1.  Proporcionar la **Tecla de acceso** principal para su cuenta de Microsoft Azure almacenamiento.

    1.  Seleccione **el modo de habilitar SSL** para crear un canal seguro para la comunicación de red entre el dispositivo y la nube. Desactive la casilla de verificación **Habilitar SSL modo** solo si trabaja dentro de una nube privada.

    1.  Haga clic en el icono de verificación ![](./media/storsimple-ova-deploy1-portal-prep/image7.png). Se le notificará cuando se crea correctamente la cuenta de almacenamiento.

        ![](./media/storsimple-ova-deploy1-portal-prep/image11.png)

1.  La cuenta de almacenamiento recién creado se mostrará en la página **Configurar** en **cuentas de almacenamiento**. Haga clic en **Guardar** para guardar la cuenta de almacenamiento recién creado. Haga clic en **Aceptar** cuando se le solicite confirmación.


## <a name="next-step"></a>Siguiente paso

El siguiente paso es aprovisionar una máquina virtual para el dispositivo virtual de StorSimple. Según el sistema operativo host, consulte las instrucciones detalladas en:

-   [Aprovisionar una matriz Virtual StorSimple en Hyper-V](storsimple-ova-deploy2-provision-hyperv.md)

-   [Aprovisionar una matriz Virtual StorSimple en VMware](storsimple-ova-deploy2-provision-vmware.md)
