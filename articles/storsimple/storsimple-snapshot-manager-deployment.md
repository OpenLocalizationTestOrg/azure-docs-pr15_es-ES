<properties 
   pageTitle="Implementar el Administrador de instantáneas StorSimple | Microsoft Azure"
   description="Aprenda a descargar e instalar al administrador de instantáneas StorSimple, un complemento MMC para administrar las características de copia de seguridad y protección de datos de StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Implementar el complemento de MMC Administrador de instantáneas StorSimple

## <a name="overview"></a>Información general

El Administrador de instantáneas StorSimple es un complemento de consola de administración de Microsoft (MMC) que simplifica la protección de datos y administración de copia de seguridad en un entorno de Microsoft Azure StorSimple. Con el Administrador de instantáneas StorSimple, puede administrar Microsoft Azure StorSimple local y almacenamiento de nube como si fuese un sistema de almacenamiento completamente integrado, por tanto, simplificar los procesos de copia de seguridad y restauración y reducir los costos. 

Este tutorial describe los requisitos de configuración, así como procedimientos para instalar, quitar y actualizar StorSimple instantánea Manager.

>[AZURE.NOTE] 
>
>- No puede usar el Administrador de instantáneas StorSimple para administrar Microsoft Azure StorSimple Virtual matrices (también conocido como StorSimple local dispositivos virtuales).
>
>- Si planea instalar StorSimple 2 en el dispositivo StorSimple, asegúrese de descargar la versión más reciente del Administrador de instantánea StorSimple e instálelo **antes de instalar la actualización StorSimple 2**. La versión más reciente del Administrador de instantánea StorSimple es compatible con versiones anteriores y funciona con todas las versiones publicadas de Microsoft Azure StorSimple. Si está utilizando la versión anterior del Administrador de instantánea StorSimple, deberá actualizarlo (no es necesario desinstalar la versión anterior antes de instalar la versión nueva).

## <a name="storsimple-snapshot-manager-installation"></a>Instalación de administrador de instantáneas StorSimple

Administrador de instantáneas StorSimple se puede instalar en equipos que ejecutan Windows Server 2008 R2 SP1, Windows Server 2012 y sistema operativo Windows Server 2012 R2. En servidores que ejecutan Windows 2008 R2, también debe instalar el Service Pack 1 de Windows Server 2008 y Windows Management Framework 3.0. 

Antes de instalar o actualizar el complemento de administrador de instantáneas StorSimple para la consola de administración de Microsoft (MMC), asegúrese de que el servidor de Microsoft Azure StorSimple dispositivo y host están configurados correctamente. 

## <a name="configure-prerequisites"></a>Configurar los requisitos previos

Los pasos siguientes proporcionan una descripción general de las tareas de configuración que debe completar antes de instalar al administrador de instantáneas StorSimple. Para completar la configuración de Microsoft Azure StorSimple y la información de configuración, incluidos los requisitos del sistema y las instrucciones paso a paso, vea [implementar el dispositivo de StorSimple local](storsimple-deployment-walkthrough.md).

>[AZURE.IMPORTANT]Antes de comenzar, revise la [lista de comprobación de configuración de implementación](storsimple-deployment-walkthrough.md#deployment-configuration-checklist) y y [los requisitos previos de implementación](storsimple-deployment-walkthrough.md#deployment-prerequisites) de [implementar el dispositivo de StorSimple local](storsimple-deployment-walkthrough.md).
> <br>
 
### <a name="before-you-install-storsimple-snapshot-manager"></a>Antes de instalar el Administrador de instantáneas StorSimple

1. Descomprimir, montaje y conecte el dispositivo de Microsoft Azure StorSimple como se describe en [instalar el dispositivo 8100 StorSimple](storsimple-8100-hardware-installation.md) o [el dispositivo 8600 StorSimple](storsimple-8600-hardware-installation.md).

2. Asegúrese de que el equipo host está ejecutando uno de los siguientes sistemas operativos:

    - Windows Server 2008 R2 (en servidores ejecutando Windows 2008 R2, también debe instalar el Service Pack 1 de Windows Server 2008 y Windows Management Framework 3.0)
    - Windows Server 2012
    - Windows Server 2012 R2
 
    Para un dispositivo virtual StorSimple, el host debe ser una máquina Virtual de Azure de Microsoft. 

3. Asegúrese de que cumple los requisitos de configuración de Microsoft Azure StorSimple. Para obtener más información, vaya a [requisitos previos de implementación](storsimple-deployment-walkthrough.md#deployment-prerequisites).

4. Conecte el dispositivo al host y realizar la configuración inicial. Para obtener más información, vaya a [los pasos de implementación](storsimple-deployment-walkthrough.md#deployment-steps).

5. Crear volúmenes en el dispositivo, les asigna el host y compruebe que el host puede montaje y usarlos. Administrador de instantáneas StorSimple es compatible con los siguientes tipos de volúmenes: 

    - Volúmenes básicos
    - Volúmenes simples
    - Volúmenes dinámicos
    - Volúmenes dinámicos reflejados (RAID 1)
    - Volúmenes compartidos de clúster
 
    Para obtener información sobre la creación de volúmenes en el dispositivo de StorSimple o un dispositivo virtual de StorSimple, vaya a [paso 6: crear un volumen](storsimple-deployment-walkthrough.md#step-6-create-a-volume), en [el dispositivo de StorSimple local de implementación](storsimple-deployment-walkthrough.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Instalar a un nuevo administrador de instantánea StorSimple

Antes de instalar el Administrador de instantáneas StorSimple, asegúrese de que los volúmenes creado en el dispositivo de StorSimple o StorSimple dispositivo virtual montaje, inicializado y con formato como se describe en [configurar los requisitos previos](#configure-prerequisites).

>[AZURE.IMPORTANT]
>
>- Para un dispositivo virtual StorSimple, el host debe ser una máquina Virtual de Azure de Microsoft. 
>
>- Debe ejecutar el host de Windows 2008 R2, Windows Server 2012 o Windows Server 2012 R2. Si el servidor está ejecutando Windows Server 2008 R2, también debe instalar el Service Pack 1 de Windows Server 2008 y Windows Management Framework 3.0.
>
>- Debe configurar una conexión iSCSI desde el host al dispositivo StorSimple antes de que puede conectar el dispositivo para el Administrador de instantáneas StorSimple.

Siga estos pasos para completar una instalación limpia de administrador de instantáneas StorSimple. Si va a instalar una actualización, vaya a [actualizar o volver a instalar Administrador de instantáneas StorSimple](#upgrade-or-reinstall-storsimple-snapshot-manager).

- Paso 1: Instalar el Administrador de instantáneas StorSimple 
- Paso 2: Conecte StorSimple instantánea administrador a un dispositivo 
- Paso 3: Comprobar la conexión con el dispositivo 

###<a name="step-1-install-storsimple-snapshot-manager"></a>Paso 1: Instalar el Administrador de instantáneas StorSimple

Use los siguientes pasos para instalar el Administrador de instantáneas StorSimple.

#### <a name="to-install-storsimple-snapshot-manager"></a>Para instalar el Administrador de instantáneas StorSimple

1. Descargar el software de administrador de instantáneas StorSimple (vaya a [Administrador de instantáneas StorSimple](https://www.microsoft.com/download/details.aspx?id=44220) en Microsoft Download Center) y guardarlo localmente en el host.

2. En el Explorador de archivos, haga clic en la carpeta comprimida y, a continuación, haga clic en **extraer todos**.

3. En la ventana de **carpetas extraer comprimido (zip)** , en el cuadro **Seleccionar un destino y extraer archivos** , escriba o busque la ruta de acceso que desea que en el archivo se extrae. 

       >[AZURE.IMPORTANT] Debe instalar el Administrador de instantáneas StorSimple en la unidad C:.
 
4. Seleccione la casilla de verificación **Mostrar archivos extraídos cuando haya terminado** y, a continuación, haga clic en **extraer**.

    ![Extraer el cuadro de diálogo de archivos](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 

4. Cuando finalice la extracción, se abre la carpeta de destino. Haga doble clic en el icono de configuración de aplicación que aparece en la carpeta de destino.

5. Cuando aparezca el mensaje de **Éxito de instalación** , haga clic en **Cerrar**. Debería ver el icono del Administrador de instantánea StorSimple en el escritorio.

    ![icono de escritorio](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Paso 2: Conecte StorSimple instantánea administrador a un dispositivo

Use los siguientes pasos para conectar StorSimple instantánea administrador a un dispositivo de StorSimple.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Para conectar el Administrador de instantáneas StorSimple a un dispositivo

1. Haga clic en el icono del Administrador de instantánea StorSimple en el escritorio. Aparece la ventana Administrador de instantáneas StorSimple. La ventana contiene un panel de **ámbito** , un panel de **resultados** y un panel de **acciones** . 

    ![Interfaz de usuario de administrador de instantáneas StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png) 

    - El panel de **ámbito** (el panel izquierdo) contiene una lista de nodos organizados en una estructura de árbol. Puede expandir algunos nodos para seleccionar una vista o datos específicos relacionados con ese nodo. Haga clic en el icono de flecha para expandir o contraer un nodo. Haga clic en un elemento en el panel de **ámbito** para ver una lista de acciones disponibles para ese elemento. 

    - El panel de **resultados** (el panel central) contiene información de estado detallada sobre el nodo, vista o datos que haya seleccionado en el panel de **ámbito** .

    - El panel de **acciones** muestra las operaciones que puede realizar en el nodo, vista o datos que haya seleccionado en el panel de **ámbito** .

    Para obtener una descripción completa de la interfaz de usuario de administrador de instantáneas StorSimple, vea la [interfaz de usuario de administrador de instantáneas StorSimple](storsimple-use-snapshot-manager.md).

2. En el panel de **ámbito** , haga clic en el nodo de **dispositivos** y, a continuación, haga clic en **configurar un dispositivo**. Aparece el cuadro de diálogo **configurar un dispositivo** .

    ![Configurar un dispositivo](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 

3. En el cuadro de lista del **dispositivo** , seleccione la dirección IP del dispositivo de Microsoft Azure StorSimple o dispositivo virtual. En el cuadro de texto **contraseña** , escriba la contraseña de administrador de instantáneas StorSimple que ha creado para el dispositivo en el portal de clásico de Azure. Haga clic en **Aceptar**.

4. Administrador de instantáneas StorSimple busca en el dispositivo que ha identificado. Si el dispositivo está disponible, el Administrador de instantáneas StorSimple agrega una conexión. Puede [comprobar la conexión con el dispositivo](#to-verify-the-connection) para confirmar que la conexión se ha agregado correctamente.

    Si el dispositivo no está disponible por cualquier motivo, el Administrador de instantáneas StorSimple devuelve un mensaje de error. Haga clic en **Aceptar** para cerrar el mensaje de error y, a continuación, haga clic en **Cancelar** para cerrar el cuadro de diálogo **configurar un dispositivo** .

5. Cuando se conecta a un dispositivo, StorSimple instantánea administrador importa cada grupo de volumen configurado para ese dispositivo, siempre que el grupo de volumen asociada copias de seguridad. Grupos de volumen que no tienen las copias de seguridad asociadas no se importan. Además, no se importan las directivas de copia de seguridad que se crearon para un grupo de volumen. Para ver los grupos importados, haga clic en el nodo de **Grupos de volumen** de nivel superior en el panel de **ámbito** y haga clic en **botón de alternancia importado grupos**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Paso 3: Comprobar la conexión con el dispositivo

Utilice los siguientes pasos para comprobar que StorSimple instantánea administrador está conectado al dispositivo StorSimple.

#### <a name="to-verify-the-connection"></a>Para comprobar la conexión

1. En el panel de **ámbito** , haga clic en el nodo **dispositivos** .

    ![Estado de dispositivo de administrador de instantáneas StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 

2. Compruebe el panel de **resultados** : 

   - Si aparece un indicador de color verde en el icono de dispositivo y **disponibles** aparece en la columna **estado** , el dispositivo está conectado. 

   - Si aparece un indicador rojo en el icono del dispositivo y no disponible aparece en la columna **estado** , el dispositivo no está conectado. 

   - Si **la actualización** aparece en la columna **estado** , Administrador de instantáneas StorSimple está recuperando grupos de volumen y asociados copias de seguridad de un dispositivo conectado.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Actualizar o volver a instalar Administrador de instantáneas StorSimple

Administrador de instantáneas StorSimple debe desinstalar completamente antes de actualizar o volver a instalar el software. 

Antes de reinstalar StorSimple instantánea Manager, realizar una copia de la base de datos de administrador de instantáneas de StorSimple existente en el equipo host. Esto guarda la información de configuración y directivas de copia de seguridad para que pueda restaurar fácilmente estos datos de copia de seguridad.

Si está actualizando o reinstalar a StorSimple instantánea administrador, siga estos pasos:

- Paso 1: Desinstalar Administrador de instantáneas StorSimple 
- Paso 2: Realizar una copia de la base de datos de administrador de instantáneas StorSimple 
- Paso 3: Reinstalar StorSimple instantánea Manager y restaurar la base de datos 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Paso 1: Desinstalar Administrador de instantáneas StorSimple

Siga los pasos siguientes para desinstalar el Administrador de instantáneas StorSimple.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Para desinstalar el Administrador de instantáneas StorSimple

1. En el equipo host, abra el **Panel de Control**, haga clic en **programas**y, a continuación, haga clic en **programas y características**.

2. En el panel izquierdo, haga clic en **Desinstalar o cambiar un programa**.

3. Haga clic en **Administrador de instantáneas StorSimple**y, a continuación, haga clic en **desinstalar**.

4. Esto inicia el programa de instalación de administrador de instantáneas StorSimple. Haga clic en **Modificar configuración**y, a continuación, haga clic en **desinstalar**.

    >[AZURE.NOTE] Si hay algún proceso MMC ejecutando en segundo plano, como administrador de instantáneas StorSimple o administración de disco, se producirá un error la desinstalación y recibirá un mensaje que cierre todas las instancias de MMC antes de intentar desinstalar el programa. Seleccione **Cerrar automáticamente las aplicaciones e intente reiniciar ellos una vez completada la configuración**y, a continuación, haga clic en **Aceptar**.
 
5. Cuando termine el proceso de desinstalación, aparece un mensaje de **Configuración correcta** . Haga clic en **Cerrar**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Paso 2: Realizar una copia de la base de datos de administrador de instantáneas StorSimple

Realice los pasos siguientes para crear y guardar una copia de la base de datos de administrador de instantáneas StorSimple.

#### <a name="to-back-up-the-database"></a>Para realizar copias de seguridad de la base de datos

1. Detenga el servicio de administración de Microsoft StorSimple:

   1. Inicie el administrador del servidor.

   2. En el panel de administrador del servidor, en el menú **Herramientas** , seleccione **Servicios**.

   3. En la página **Servicios** , seleccione **El servicio de administración de Microsoft StorSimple**.

   4. En el panel derecho, en el **Servicio de administración de StorSimple de Microsoft**, haga clic en **Detener el servicio**.

        ![Detener el servicio Administrador de StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)

2. Vaya a C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    >[AZURE.NOTE] ProgramData es una carpeta oculta.

3. Busque el archivo XML de catálogo, copie el archivo y almacenar la copia en una ubicación segura o en la nube.

    ![Archivo de copia de seguridad del catálogo de StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)

4. Reinicie el servicio de administración de Microsoft StorSimple: 

    1. En el panel de administrador del servidor, en el menú **Herramientas** , seleccione **Servicios**.

    2. En la página **Servicios** , seleccione lo **Servicios de administración de Microsoft StorSimple**e.

    3. En el panel derecho, en el **Servicio de administración de StorSimple de Microsoft**, haga clic en **reiniciar el servicio**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Paso 3: Reinstalar StorSimple instantánea Manager y restaurar la base de datos

Para volver a instalar Administrador de StorSimple instantáneas, siga los pasos de [instalar un nuevo administrador de instantánea StorSimple](#install-a-new-storsimple-snapshot-manager). A continuación, use el siguiente procedimiento para restaurar la base de datos de administrador de instantáneas StorSimple.

#### <a name="to-restore-the-database"></a>Para restaurar la base de datos

1. Detenga el servicio de administración de Microsoft StorSimple:

    1. Inicie el administrador del servidor.

    2. En el panel de administrador del servidor, en el menú **Herramientas** , seleccione **Servicios**.

    3. En la página **Servicios** , seleccione **El servicio de administración de Microsoft StorSimple**.

    4. En el panel derecho, en el **Servicio de administración de StorSimple de Microsoft**, haga clic en **Detener el servicio**.

2. Vaya a C:\ProgramData\Microsoft\StorSimple\BACatalog. 

     >[AZURE.NOTE] ProgramData es una carpeta oculta.

3. Elimine el archivo XML de catálogo y reemplazar con la versión guardada anteriormente.

4. Reinicie el servicio de administración de Microsoft StorSimple: 

    1. En el panel de administrador del servidor, en el menú **Herramientas** , seleccione **Servicios**.

    2. En la página **Servicios** , seleccione **El servicio de administración de Microsoft StorSimple**.

    3. En el panel derecho, en el **Servicio de administración de StorSimple de Microsoft**, haga clic en **reiniciar el servicio**.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre el Administrador de instantáneas StorSimple, vaya a [¿Qué es el Administrador de instantáneas StorSimple?](storsimple-what-is-snapshot-manager.md).

- Para obtener más información acerca de la interfaz de usuario de administrador de instantáneas StorSimple, vaya a la [interfaz de usuario de administrador de instantáneas StorSimple](storsimple-use-snapshot-manager.md).

- Para obtener más información sobre cómo usar el Administrador de instantáneas StorSimple, vaya a [Usar el Administrador de instantánea StorSimple para administrar la solución StorSimple](storsimple-snapshot-manager-admin.md).
