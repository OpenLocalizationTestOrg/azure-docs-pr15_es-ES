<properties
   pageTitle="Implementar StorSimple Virtual matriz: disposición en Hyper-V"
   description="Este tutorial de implementación de matriz Virtual de StorSimple segunda implica el aprovisionamiento de un dispositivo virtual en Hyper-V."
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
   ms.date="10/11/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---provision-a-virtual-array-in-hyper-v"></a>Implementar StorSimple Virtual Array - aprovisionar una matriz Virtual de Hyper-V

![](./media/storsimple-ova-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Información general

Este tutorial aprovisionamiento se aplica a la versión de Microsoft Azure StorSimple Virtual matrices (también conocido como dispositivos virtuales de StorSimple local o dispositivos virtuales de StorSimple) ejecución marzo de 2016 disponibilidad general (GA). Este tutorial describe cómo aprovisionar una matriz Virtual StorSimple en un sistema de host ejecuta Hyper-V en Windows Server 2012 R2, en Windows Server 2008 R2 o Windows Server 2012. En este artículo se aplica a la implementación de StorSimple Virtual matrices en Azure portal clásica, así como la nube de Microsoft Azure administración pública.

Necesitará privilegios de administrador para aprovisionar y configurar un dispositivo virtual. La configuración de aprovisionamiento e inicial puede tardar unos 10 minutos en completarse.


## <a name="provisioning-prerequisites"></a>Requisitos previos de aprovisionamiento

Aquí encontrará los requisitos previos para proporcionar un dispositivo virtual en un sistema de host ejecuta Hyper-V en Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2.

### <a name="for-the-storsimple-manager-service"></a>Para el servicio Administrador de StorSimple

Antes de empezar, asegúrese de:

-   Ha completado todos los pasos [Preparar el portal de la matriz Virtual de StorSimple](storsimple-ova-deploy1-portal-prep.md).

-   La imagen de dispositivo virtual que ha descargado para Hyper-V desde el portal de Azure. Para obtener más información, vea [paso 3: Descargar la imagen del dispositivo virtual](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

    > [AZURE.IMPORTANT] El software que se ejecuta en la matriz Virtual StorSimple sólo puede utilizarse junto con el servicio Administrador de Storsimple.

### <a name="for-the-storsimple-virtual-device"></a>Para el dispositivo virtual de StorSimple

Antes de implementar un dispositivo virtual, asegúrese de:

-   Tiene acceso a un sistema de host ejecuta Hyper-V en Windows Server 2008 R2 o posterior que puede ser utilizado para una disposición en un dispositivo.

-   El sistema host es capaz de que se dedicará a los siguientes recursos para configurar el dispositivo virtual:

    -   Un mínimo de 4 núcleos.

    -   Al menos 8 GB de RAM.

    -   Una interfaz de red.

    -   Un disco virtual de 500 GB para los datos del sistema.

### <a name="for-the-network-in-the-datacenter"></a>Para la red en el centro de datos

Antes de comenzar, revise los requisitos de red para implementar un dispositivo virtual de StorSimple y configurar correctamente la red de centro de datos. Para obtener más información, consulte [requisitos de red de la matriz Virtual de StorSimple](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Paso a paso de aprovisionamiento

Para aprovisionar y conectarse a un dispositivo virtual, debe realizar los siguientes pasos:

1.  Asegúrese de que el sistema host tiene suficientes recursos para cumplir con los requisitos mínimos dispositivo virtual.

2.  Aprovisionar a un dispositivo virtual en el hipervisor.

3.  Inicie el dispositivo virtual y obtener la dirección IP.

En las siguientes secciones se explica cada uno de estos pasos.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements"></a>Paso 1: Asegúrese de que el sistema host cumple los requisitos mínimos dispositivo virtual

Para crear un dispositivo virtual, necesitará:

-   El rol de Hyper-V instalado en Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 SP1.

-   Administrador de Hyper-V de Microsoft en un cliente de Microsoft Windows conectado al host.

Asegúrese de que el hardware subyacente (sistema de host) en la que va a crear el dispositivo virtual es capaz de que se dedicará a los siguientes recursos en el dispositivo virtual:

- Un mínimo de 4 núcleos.
- Al menos 8 GB de RAM.
- Una interfaz de red.
- Un disco virtual de 500 GB para los datos del sistema.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Paso 2: Aprovisionar un dispositivo virtual de hipervisor

Siga estos pasos para configurar un dispositivo en el hipervisor.

#### <a name="to-provision-a-virtual-device"></a>Para proporcionar un dispositivo virtual

1.  En el host del servidor de Windows, copie la imagen de dispositivo virtual en una unidad local. Esta es la imagen (disco duro virtual o VHDX) que ha descargado a través del portal de Azure. Anote la ubicación donde haya copiado la imagen que va a utilizar esto más adelante en el procedimiento.

2.  Abra el **Administrador del servidor**. En la esquina superior derecha, haga clic en **Herramientas** y seleccione **Administrador de Hyper-V**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image1.png)

    Si está ejecutando Windows Server 2008 R2, abra el Administrador de Hyper-V. En el administrador del servidor, haga clic en **funciones > Hyper-V > Administrador de Hyper-V**.

1.  En el **Administrador de Hyper-V**, en el panel de ámbito, haga clic en el nodo del sistema para abrir el menú contextual y, a continuación, haga clic en **nuevo** > **Máquina Virtual**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image2.png)

1.  En la página del Asistente para nueva máquina Virtual **antes de empezar** , haga clic en **siguiente**.

1.  En la página **ubicación y especifique el nombre** , proporcione un **nombre** para el dispositivo virtual. Haga clic en **siguiente**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image4.png)

1.  En la página **especificar generación** , elija el tipo de imagen de dispositivo y, a continuación, haga clic en **siguiente**. Esta página no aparece si está usando Windows Server 2008 R2.

    * Elija **generación 2** si ha descargado una imagen .vhdx para Windows Server 2012 o posterior.
    * Elija **generación 1** si ha descargado una imagen .vhd para Windows Server 2008 R2 o posterior.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image5.png)

1.  En la página **asignar memoria** , especifique una **memoria de inicio** de al menos **MB 8192**, no habilitar memoria dinámica y, a continuación, haga clic en **siguiente**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image6.png)

1.  En la página **configurar redes** , especifique el modificador virtual que está conectado a Internet y, a continuación, haga clic en **siguiente**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image7.png)

1.  En la página **en el disco duro virtual conectar** , elija **utilizar un disco duro virtual existente**, especifique la ubicación de la imagen de dispositivo virtual (.vhdx o .vhd) y, a continuación, haga clic en **siguiente**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image8m.png)

1.  Revise el **Resumen** y, a continuación, haga clic en **Finalizar** para crear la máquina virtual. Pero no avanzar todavía, necesitará agregar algunos núcleos de CPU y una segunda unidad. 

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image9.png)

1.  Para satisfacer los requisitos mínimos, deberá 4 núcleos. Para agregar procesadores virtuales, con el sistema de host seleccionado en la ventana **Administrador de Hyper-V** , en el panel derecho debajo de la lista de **máquinas virtuales de Windows**, busque la máquina virtual que acaba de crear. Seleccione y haga clic en el nombre del equipo y seleccione **configuración**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image10.png)

1.  En la página de **configuración** , en el panel izquierdo, haga clic en **procesador**. En el panel derecho, establezca el **número de procesadores virtuales** a 4 (o más). Haga clic en **Aplicar**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image11.png)

1.  Para satisfacer los requisitos mínimos, también debe agregar un disco de datos virtual de 500 GB. En la página de **configuración** :

    1.  En el panel izquierdo, seleccione el **Controlador SCSI**.
    2.  En el panel derecho, seleccione la **Unidad** y haga clic en **Agregar**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image12.png)

1.  En la página de la **unidad de disco duro** , seleccione la opción **en el disco duro Virtual** y haga clic en **nuevo**. Se iniciará el **Asistente para nuevo disco duro de Virtual**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image13.png)

1.  En la página del asistente en el disco duro Virtual nuevo **antes de empezar** , haga clic en **siguiente**.

1.  En la **página Elegir formato de disco**, acepte la opción predeterminada de formato **VHDX** . Haga clic en **siguiente**. Si está ejecutando Windows Server 2012 R2 o Windows Server 2008 R2, no verá esta pantalla.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image15.png)

1.  En la **página Elegir tipo de disco**, establecer el tipo de disco duro virtual como **expansión dinámica** (recomendado). Si elige disco de **tamaño fijo** , también funciona, pero es podrán que deba esperar mucho tiempo. Se recomienda no usar la opción **Differencing** . Haga clic en **siguiente**. Observe que la **expansión dinámica** es el valor predeterminado en Windows Server 2012 R2 y Windows Server 2012. En Windows Server 2008 R2, el valor predeterminado es de **tamaño fijo**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image16.png)

1.  En la página **especificar el nombre y ubicación** , proporcione un **nombre** , así como la **ubicación** (que puede seleccionar uno) para el disco de datos. Haga clic en **siguiente**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image17.png)

1.  En la página **Configurar disco** , seleccione la opción **crear un disco duro virtual en blanco nuevo** y especifique el tamaño como **500 GB** (o más). Mientras 500 GB es el requisito mínimo, siempre puede aprovisionar un disco mayor. Tenga en cuenta que no se puede expandir o reducir el disco que una vez se aprovisione. Para obtener más información sobre el tamaño del disco aprovisionar, revise la sección de tamaño en el documento de [prácticas recomendadas](storsimple-ova-best-practices.md#configuration-best-practices) . Haga clic en **siguiente**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image18.png)

1.  En la página **Resumen** , revise los detalles del disco virtual de datos y si se cumple, haga clic en **Finalizar** para crear el disco. El asistente se cerrará y un disco duro virtual se agregará a su equipo.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image19.png)

2.  Volverá a la página de **configuración** . Haga clic en **Aceptar** para cerrar la página de **configuración** y volver a la ventana Administrador de Hyper-V.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Paso 3: Iniciar el dispositivo virtual y obtener la dirección IP

Siga estos pasos para iniciar el dispositivo virtual y conectarse a él.

#### <a name="to-start-the-virtual-device"></a>Para iniciar el dispositivo virtual

1.  Inicie el dispositivo virtual.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image21.png)

1.  Después de que el dispositivo esté ejecutando, seleccione el dispositivo, haga clic en y seleccione **Conectar**.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image22.png)

1.  Que tenga que esperar 5 a 10 minutos para el dispositivo esté listo. Se muestra un mensaje de estado en la consola para indicar el progreso. Después de que el dispositivo está listo, vaya a la **acción**. Presione `Ctrl + Alt + Delete` para iniciar sesión en el dispositivo virtual. El usuario predeterminado es *StorSimpleAdmin* y la contraseña predeterminada es *Contraseña1*.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image23.png)

1.  Por motivos de seguridad, la contraseña de administrador del dispositivo caduca en el primer registro. Se le pedirá que cambie la contraseña.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image24.png)

    Escriba una contraseña que contiene al menos 8 caracteres. La contraseña debe cumplir al menos 3 fuera de los siguientes 4 requisitos: mayúsculas, minúsculas, numéricos y caracteres especiales. Vuelva a escribir la contraseña para confirmarla. Se le notificará que ha cambiado la contraseña.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image25.png)

1.  Una vez que la contraseña se ha cambiado correctamente, puede reiniciar el dispositivo virtual. Espere a que el dispositivo de inicio.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image26.png)

    La consola de Windows PowerShell del dispositivo se mostrará junto con una barra de progreso.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image27.png)

1.  Los pasos 6-8 solo se aplican durante el inicio en un entorno DHCP no. Si se encuentra en un entorno DHCP, omita estos pasos y vaya al paso 9. Si inicia su dispositivo en el entorno no DHCP, verá la siguiente pantalla.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image28m.png)

    Ahora debe configurar la red.

1.  Usar el `Get-HcsIpAddress` comando para enumerar las interfaces de red habilitadas en el dispositivo virtual. Si su dispositivo dispone de una única interfaz de red habilitada, el nombre predeterminado asignado a esta interfaz es `Ethernet`.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image29m.png)

1.  Usar el `Set-HcsIpAddress` cmdlet para configurar la red. A continuación se muestra un ejemplo:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image30.png)

1.  Una vez completada la configuración inicial y el dispositivo se ha inicializado, verá el texto de titular de dispositivo. Anote la dirección IP y la dirección URL que aparece en el texto del titular para administrar el dispositivo. Esta dirección IP se usará para conectarse a la interfaz de usuario de su dispositivo virtual de web y completar la configuración local y el registro.

    ![](./media/storsimple-ova-deploy2-provision-hyperv/image31m.png)



1. (Opcional) Realizar este paso solo si va a implementar el dispositivo en la nube de gobierno. Ahora se habilita el modo de Estados Unidos información procesamiento estándar Federal (FIPS) en el dispositivo. El estándar FIPS 140 define los algoritmos de cifrado aprobados para su uso por Federal nos informático de administración pública para la protección de datos confidenciales.
    1. Para habilitar el modo FIPS, ejecute el siguiente cmdlet:

        `Enter-HcsFIPSMode`

    2. Reinicie el dispositivo después de habilitar el modo FIPS para que las validaciones cryptographic surtan efecto.

        > [AZURE.NOTE] Puede habilitar o deshabilitar el modo FIPS en su dispositivo. Alternar el dispositivo entre el modo FIPS y no FIPS no es compatible.

Si el dispositivo no cumple los requisitos mínimos de configuración, verá un error en el texto del titular (que se muestra a continuación). Debe modificar la configuración del dispositivo para que tenga los recursos adecuados para cumplir los requisitos mínimos. A continuación, puede reiniciar y conectar el dispositivo. Consulte los requisitos mínimos de configuración de [paso 1: asegúrese de que el sistema host cumple los requisitos mínimos dispositivo virtual](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-hyperv/image32.png)

Si se enfrenta a cualquier otro error durante la configuración inicial mediante la interfaz de usuario de web local, consulte los siguientes flujos de trabajo en [administrar su matriz Virtual de StorSimple mediante la interfaz de usuario de web local](storsimple-ova-web-ui-admin.md).

-   Ejecutar pruebas de diagnóstico para [solucionar problemas de instalación de la interfaz de usuario de web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Paquete de registro de generar y ver los archivos de registro](storsimple-ova-web-ui-admin.md#generate-a-log-package).

![icono de vídeo](./media/storsimple-ova-deploy2-provision-hyperv/video_icon.png)  **vídeo disponible**

Vea el vídeo para ver cómo puede proporcionar una matriz Virtual StorSimple en Hyper-V.

> [AZURE.VIDEO create-a-storsimple-virtual-array]

## <a name="next-steps"></a>Pasos siguientes

-   [Configurar la matriz Virtual de StorSimple como un servidor de archivos](storsimple-ova-deploy3-fs-setup.md)

-   [Configurar la matriz Virtual de StorSimple como un servidor iSCSI](storsimple-ova-deploy3-iscsi-setup.md)
