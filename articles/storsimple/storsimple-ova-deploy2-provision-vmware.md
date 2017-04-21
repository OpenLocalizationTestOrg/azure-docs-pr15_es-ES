<properties
   pageTitle="Implementar StorSimple Virtual matriz: disposición en VMware"
   description="Este tutorial segunda serie de implementación de matriz Virtual de StorSimple implica el aprovisionamiento de un dispositivo virtual de VMware."
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
   ms.date="04/12/2016"
   ms.author="alkohli"/>


# <a name="deploy-storsimple-virtual-array---provision-a-virtual-array-in-vmware"></a>Implementar StorSimple Virtual Array - aprovisionar una matriz Virtual en VMware

![](./media/storsimple-ova-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Información general 
Este tutorial aprovisionamiento se aplica a la versión de disponibilidad general (GA) de marzo de 2016 ejecución StorSimple Virtual matrices (también conocido como dispositivos virtuales de StorSimple local o StorSimple virtuales dispositivos). Este tutorial describe cómo aprovisionar y conectarse a una matriz Virtual de StorSimple en un sistema de host ejecuta VMware ESXi 5.5 y versiones posteriores. En este artículo se aplica a la implementación de StorSimple Virtual matrices en Azure portal clásica, así como la nube de Microsoft Azure administración pública.

Necesitará privilegios de administrador para aprovisionar y conectarse a un dispositivo virtual. La configuración de aprovisionamiento e inicial puede tardar unos 10 minutos en completarse.


## <a name="provisioning-prerequisites"></a>Requisitos previos de aprovisionamiento

Aquí encontrará los requisitos previos para proporcionar un dispositivo virtual en un sistema de host ejecuta VMware ESXi 5.5 y versiones posteriores.

### <a name="for-the-storsimple-manager-service"></a>Para el servicio Administrador de StorSimple

Antes de empezar, asegúrese de:

-   Ha completado todos los pasos [Preparar el portal de la matriz Virtual de StorSimple](storsimple-ova-deploy1-portal-prep.md).

-   Ha descargado la imagen de dispositivo virtual para VMware desde el portal de Azure. Para obtener más información, vea [paso 3: Descargar la imagen del dispositivo virtual](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).

### <a name="for-the-storsimple-virtual-device"></a>Para el dispositivo virtual de StorSimple 

Antes de implementar un dispositivo virtual, asegúrese de:

-   Tiene acceso a un sistema de host ejecuta Hyper-V (2008 R2 o posterior) que puede ser utilizado para una disposición en un dispositivo.

-   El sistema host es capaz de que se dedicará a los siguientes recursos para configurar el dispositivo virtual:

    -   Un mínimo de 4 núcleos.

    -   Al menos 8 GB de RAM.

    -   Una interfaz de red.

    -   Un disco virtual de 500 GB para los datos del sistema.

### <a name="for-the-network-in-datacenter"></a>Para la red en el centro de datos 

Antes de empezar, asegúrese de:

-   Revisar los requisitos de red para implementar un dispositivo virtual de StorSimple y configurar la red del centro de datos según los requisitos. Para obtener más información, consulte [requisitos del sistema de matriz Virtual de StorSimple](storsimple-ova-system-requirements.md).

## <a name="step-by-step-provisioning"></a>Paso a paso de aprovisionamiento 

Para aprovisionar y conectarse a un dispositivo virtual, debe realizar los siguientes pasos:

1.  Asegúrese de que el sistema host tiene suficientes recursos para cumplir con los requisitos mínimos dispositivo virtual.

2.  Aprovisionar a un dispositivo virtual en el hipervisor.

3.  Inicie el dispositivo virtual y obtener la dirección IP.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Paso 1: Asegúrese de sistema host cumple los requisitos de dispositivo virtual mínimo

Para crear un dispositivo virtual, necesitará:

-   Acceso a un sistema de host ejecuta VMware ESXi Server 5.5 y superior.

-   VMware vSphere cliente en el sistema para administrar el host ESXi.

    -   Un mínimo de 4 núcleos.

    -   Al menos 8 GB de RAM.

    -   Una interfaz de red conectada a la red pueda enrutar el tráfico a Internet. El ancho de banda de Internet mínimo debería 5 Mbps para permitir el trabajo óptimo del dispositivo.

    -   Un disco virtual de 500 GB de datos.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Paso 2: Aprovisionar un dispositivo virtual de hipervisor

Siga estos pasos para configurar un dispositivo virtual en el hipervisor.

1.  Copie la imagen de dispositivo virtual en el sistema. Esta es la imagen que ha descargado a través del portal clásico Azure. 
    1.  Asegúrese de que este es el archivo de imagen más reciente que ha descargado. Si ha descargado anteriormente la imagen, descárguelo nuevamente para asegurarse de que la imagen más reciente. La imagen más reciente tiene dos archivos (en lugar de uno).
    2.  Anote la ubicación donde haya copiado la imagen que va a utilizar esto más adelante en el procedimiento.

2.  Inicie sesión en el servidor de ESXi mediante el cliente de vSphere. Debe tener privilegios de administrador para crear una máquina virtual.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image1.png)

1.  En el cliente vSphere, en la sección de inventario en el panel izquierdo, seleccione el servidor ESXi.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image2.png)

1.  En primer lugar, cargará el VMDK al servidor ESXi. Vaya a la pestaña **configuración** en el panel derecho. En **Hardware**, seleccione **almacenamiento**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image3.png)

1.  En el panel derecho, en **almacenes de datos**, seleccione el almacén de datos donde desea cargar el VMDK. El almacén de datos debe tener espacio suficiente para los discos de sistema operativo y los datos.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image4.png)

1.  Haga clic y seleccione **Examinar el almacén de datos**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image5.png)

1.  Aparecerá una ventana del **Explorador de almacén de datos** .

    ![](./media/storsimple-ova-deploy2-provision-vmware/image6.png)

1.  En la barra de herramientas, haga clic en ![](./media/storsimple-ova-deploy2-provision-vmware/image7.png) icono para crear una nueva carpeta. Especifique el nombre de la carpeta y tome nota del mismo. El nombre de la carpeta que usa más adelante al crear una máquina virtual (se recomienda recomendado). Haga clic en **Aceptar**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image8.png)

1.  La nueva carpeta aparecerá en el panel izquierdo del **Explorador de almacén de datos**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image9.png)

1.  Haga clic en el icono de carga ![](./media/storsimple-ova-deploy2-provision-vmware/image10.png) y seleccione **Cargar archivo**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image11.png)

1.  Ahora debe examinar y seleccione los archivos VMDK que ha descargado. Habrá dos archivos. Seleccione un archivo para cargarlo.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image12m.png)

1.  Haga clic en **Abrir**. Ahora se iniciará la carga del archivo VMDK del almacén de datos especificada. Puede tardar varios minutos cargar el archivo.


1.  Una vez completada la carga, se muestra el archivo en el almacén de datos en la carpeta que creó. 

    ![](./media/storsimple-ova-deploy2-provision-vmware/image14.png)

    Ahora debe cargar el archivo VMDK segundo en el mismo almacén de datos.

1.  Volver a la ventana de cliente vSphere. Con el servidor de ESXi seleccionado, haga clic y seleccione **nueva máquina Virtual**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image15.png)

1.  Aparecerá una ventana **Crear nueva máquina Virtual** . En la página **configuración** , seleccione la opción **personalizada** . Haga clic en **siguiente**.
    ![](./media/storsimple-ova-deploy2-provision-vmware/image16.png)

2.  En la página **nombre y ubicación** , especifique el nombre de la máquina virtual. Este nombre debe coincidir con el nombre de carpeta (práctica recomendada) especificado en el paso 8.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image17.png)

1.  En la página de **almacenamiento** , seleccione un almacén de datos que desea usar para aprovisionar la máquina virtual.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image18.png)

1.  En la página de la **Versión de la máquina Virtual** , seleccione **versión de máquina Virtual: 8**. Tenga en cuenta que se se admiten versiones 8 a 11.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image19.png)

1.  En la página del **sistema operativo invitado** , seleccione el **sistema operativo invitado** como **Windows**. **Versión**, en la lista desplegable, seleccione **Microsoft Windows Server 2012 (64 bits)**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image20.png)

1.  En la página de la **CPU** , ajuste el **número de sockets virtuales** y el **número de núcleos por socket virtual** para que el **Número Total de núcleos** es 4 (o más). Haga clic en **siguiente**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image21.png)

1.  En la página de la **memoria** , especifique 8 GB (o más) de RAM. Haga clic en **siguiente**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image22.png)

1.  En la página de **red** , especifique el número de las interfaces de red. El requisito mínimo es una interfaz de red.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image23.png)

1.  En la página de **Controlador SCSI** , acepte el predeterminado **controlador LSI lógica SA**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image24.png)

1.  En la página **Seleccionar un disco** , seleccione **usar un disco virtual existente**. Haga clic en **siguiente**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image25.png)

1.  En la página **Seleccionar disco existente** , bajo la **Ruta de acceso de archivo de disco**, haga clic en **Examinar**. Se abrirá un cuadro de diálogo **Examinar almacenes de datos** . Desplácese hasta la ubicación donde se ha cargado el VMDK. Ahora verá un solo archivo en el almacén de datos que se han combinado los dos archivos que ha cargado inicialmente. Seleccione el archivo y haga clic en **Aceptar**. Haga clic en **siguiente**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image26.png)

1.  En la página de **Opciones avanzadas** , acepte la opción predeterminada y haga clic en **siguiente**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image27.png)

1.  En la página **listo para completar** , revise toda la configuración asociada a la nueva máquina virtual. Consulte **Editar la configuración de la máquina virtual antes de completarse**. Haga clic en **continuar**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image28.png)

1.  En la página **Propiedades de máquinas virtuales de Windows** , en la ficha **Hardware** , busque el hardware del dispositivo. Seleccione **nuevo en el disco duro**. Haga clic en **Agregar**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image29.png)

1.  Aparece la ventana **Agregar Hardware** . En la página **Tipo de dispositivo** , en **Elegir el tipo de dispositivo que desea agregar**, seleccione el **disco duro** y haga clic en **siguiente**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image30.png)

1.  En la página **Seleccionar un disco** , seleccione **crear un nuevo disco virtual**. Haga clic en **siguiente**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image31.png)

1.  En la página **crear un disco** , cambiar el **Tamaño del disco** a 500 GB (o más). En **El aprovisionamiento de disco**, seleccione **Aprovisionamiento fina**. Haga clic en **siguiente**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image32.png)

1.  En la página de **Opciones avanzadas** , acepte el valor predeterminado.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image33.png)

1.  En la página **listo para completar** , revise las opciones de disco. Haga clic en **Finalizar**.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image34.png)

1.  Ahora volverá a la página de propiedades de la máquina Virtual. Un disco duro nuevo se agrega a su máquina virtual. Haga clic en **Finalizar**.
  
    ![](./media/storsimple-ova-deploy2-provision-vmware/image35.png)

2.  Con la máquina virtual seleccionada en el panel derecho, vaya a la pestaña **Resumen** . Revise la configuración de la máquina virtual.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image36.png)

Ahora está configurada su máquina virtual. El siguiente paso es de energía en este equipo y obtener la dirección IP.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Paso 3: Iniciar el dispositivo virtual y obtener la dirección IP

Siga estos pasos para iniciar el dispositivo virtual y conectarse a él.

#### <a name="to-start-the-virtual-device"></a>Para iniciar el dispositivo virtual

1.  Inicie el dispositivo virtual. En el Administrador de configuración, vSphere en el panel izquierdo, seleccione el dispositivo y haga clic en para mostrar el menú contextual. Seleccione **Power** y, a continuación, seleccione **el encendido**. Esto se debe encender la máquina virtual. Puede ver el estado en el panel de **Tareas recientes** de la parte inferior del cliente vSphere.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image37.png)

1.  Las tareas de configuración tardará unos minutos en completarse. Una vez que el dispositivo esté ejecutando, vaya a la pestaña de la **consola** . Enviar Ctrl + Alt + Supr para iniciar sesión en el dispositivo. Como alternativa, puede seleccionar el cursor en la ventana de consola y presione Ctrl + Alt + insertar. El usuario predeterminado es *StorSimpleAdmin* y la contraseña predeterminada es *Contraseña1*.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image38.png)

1.  Por motivos de seguridad, la contraseña de administrador del dispositivo caduca en el primer registro. Se le pedirá que cambie la contraseña.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image39.png)

1.  Escriba una contraseña que contiene al menos 8 caracteres. La contraseña debe contener 3 fuera de 4 de estos requisitos: mayúsculas, minúsculas, numéricos y caracteres especiales. Vuelva a escribir la contraseña para confirmarla. Se le notificará que ha cambiado la contraseña.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image40.png)

1.  Después de que la contraseña se ha cambiado correctamente, puede reiniciar el dispositivo virtual. Espere a que el reinicio completar. La consola de Windows PowerShell del dispositivo se mostrará junto con una barra de progreso.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image41.png)

1.  Los pasos 6-8 solo se aplican durante el inicio en un entorno DHCP no. Si se encuentra en un entorno DHCP, omita estos pasos y vaya al paso 9. Si inicia su dispositivo en el entorno no DHCP, verá la siguiente pantalla. 

    ![](./media/storsimple-ova-deploy2-provision-vmware/image42m.png)

    Ahora debe configurar la red.

1.  Usar el `Get-HcsIpAddress` comando para enumerar las interfaces de red habilitadas en el dispositivo virtual. Si su dispositivo dispone de una única interfaz de red habilitada, el nombre predeterminado asignado a esta interfaz es `Ethernet`.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image43m.png)

1.  Usar el `Set-HcsIpAddress` cmdlet para configurar la red. A continuación se muestra un ejemplo:


    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-ova-deploy2-provision-vmware/image44.png)

1.  Una vez completada la configuración inicial y el dispositivo se ha inicializado, verá el texto de titular de dispositivo. Anote la dirección IP y la dirección URL que aparece en el texto del titular para administrar el dispositivo. Esta dirección IP se usará para conectarse a la interfaz de usuario de su dispositivo virtual de web y completar la configuración local y el registro.

    ![](./media/storsimple-ova-deploy2-provision-vmware/image45.png)


1. (Opcional) Realizar este paso solo si va a implementar el dispositivo en la nube de gobierno. Ahora se habilita el modo de Estados Unidos información procesamiento estándar Federal (FIPS) en el dispositivo. El estándar FIPS 140 define los algoritmos de cifrado aprobados para su uso por Federal nos informático de administración pública para la protección de datos confidenciales.
    1. Para habilitar el modo FIPS, ejecute el siguiente cmdlet:
        
        `Enter-HcsFIPSMode`

    2. Reinicie el dispositivo después de habilitar el modo FIPS para que las validaciones cryptographic surtan efecto.

        > [AZURE.NOTE] Puede habilitar o deshabilitar el modo FIPS en su dispositivo. Alternar el dispositivo entre el modo FIPS y no FIPS no es compatible.


Si el dispositivo no cumple los requisitos mínimos de configuración, verá un error en el texto del titular (que se muestra a continuación). Debe modificar la configuración del dispositivo para que tenga los recursos adecuados para cumplir los requisitos mínimos. A continuación, puede reiniciar y conectar el dispositivo. Consulte los requisitos mínimos de configuración de [paso 1: asegúrese de que el sistema host cumple los requisitos mínimos dispositivo virtual](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-ova-deploy2-provision-vmware/image46.png)

Si se enfrenta a cualquier otro error durante la configuración inicial mediante la interfaz de usuario de web local, consulte los siguientes flujos de trabajo en [administrar su matriz Virtual de StorSimple mediante la interfaz de usuario de web local](storsimple-ova-web-ui-admin.md).

-   Ejecutar pruebas de diagnóstico para [solucionar problemas de instalación de la interfaz de usuario de web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Paquete de registro de generar y ver los archivos de registro](storsimple-ova-web-ui-admin.md#generate-a-log-package)...

## <a name="next-steps"></a>Pasos siguientes

-   [Configurar la matriz Virtual de StorSimple como un servidor de archivos](storsimple-ova-deploy3-fs-setup.md)

-   [Configurar la matriz Virtual de StorSimple como un servidor iSCSI](storsimple-ova-deploy3-iscsi-setup.md)

