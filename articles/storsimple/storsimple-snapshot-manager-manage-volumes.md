<properties 
   pageTitle="Administrador de StorSimple instantáneas y volúmenes | Microsoft Azure"
   description="Describe cómo usar el complemento de MMC StorSimple instantánea administrador para ver y administrar volúmenes y configurar copias de seguridad."
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
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Usar el Administrador de instantánea StorSimple para ver y administrar volúmenes

## <a name="overview"></a>Información general

Puede usar el nodo Administrador de instantáneas StorSimple **volúmenes** (en el panel de **ámbito** ) para seleccionar volúmenes y ver información sobre ellos. Los volúmenes se presentan como unidades que corresponden a los volúmenes montadas por el host. El nodo de **volúmenes** muestra volúmenes locales y tipos de volumen que son compatibles con StorSimple, incluidos los volúmenes encontrados mediante el uso de iSCSI y un dispositivo. 

Para obtener más información acerca de volúmenes compatibles, vaya a la [compatibilidad con varios tipos de volumen](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Lista de volumen en el panel de resultados](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

El nodo **volúmenes** también le permite examinar o eliminar volúmenes después StorSimple instantánea Manager descubre ellos. 

Este tutorial explica cómo puede montaje, inicializar y dar formato a volúmenes y, a continuación, utilizar el Administrador de instantáneas de StorSimple para:

- Ver información sobre volúmenes 
- Eliminar volúmenes
- Volver a examinar volúmenes 
- Configurar un volumen básico y realice copias de seguridad
- Configurar un volumen reflejado dinámico y realice copias de seguridad

>[AZURE.NOTE] Todas las acciones de nodo de **volumen** también están disponibles en el panel de **acciones** .
 
## <a name="mount-volumes"></a>Montaje volúmenes

Use el procedimiento siguiente para montaje, inicializar y dar formato a volúmenes de StorSimple. Este procedimiento utiliza la administración de disco, una utilidad del sistema de administración de disco duro y los volúmenes o particiones correspondiente. Para obtener más información sobre la administración de disco, vaya a [Administración de disco](https://technet.microsoft.com/library/cc770943.aspx) en el sitio Web de Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Montaje de volúmenes

1. En el equipo host, inicie el iniciador de iSCSI de Microsoft.

2. Proporcionar una de las direcciones IP de interfaz como el portal de destino o dirección IP de detección y conectar el dispositivo. Después de que el dispositivo está conectado, los volúmenes podrá tener acceso a su sistema de Windows. Para obtener más información sobre cómo usar el iniciador de iSCSI de Microsoft, vaya a la sección "Conectarse a un dispositivo de destino iSCSI" en el [iniciador de instalar y configurar Microsoft iSCSI][1].

3. Use cualquiera de las siguientes opciones para iniciar la administración de disco:

    - En el cuadro **Ejecutar** , escriba Diskmgmt.msc.

    - Inicie el administrador del servidor, expanda el nodo de **almacenamiento** y, a continuación, seleccione **Administración de discos**.

    - Iniciar **Herramientas administrativas**, expanda el nodo de **Administración de equipos** y, a continuación, seleccione **Administración de disco**. 

    >[AZURE.NOTE] Debe usar privilegios de administrador para ejecutar la administración de disco.
 
4. Llevar los volúmenes en línea:

   1. En administración de discos, haga clic en cualquier volumen marcado **sin conexión**.

   2. Haga clic en **Reactivar disco**. El disco debe marcarse **Online** después de que se vuelva a activar el disco.

5. Inicializar los volúmenes:

   1. Haga clic en los volúmenes descubiertos.

   2. En el menú, seleccione **Inicializar disco**.

   3. En el cuadro de diálogo **Inicializar disco** , seleccione los discos que desee iniciar y, a continuación, haga clic en **Aceptar**.

6. Dar formato a volúmenes simples:

   1. Haga clic en un volumen que desea dar formato.

   2. En el menú, seleccione **Nuevo volumen Simple**.

   3. Usar al Asistente para nuevo volumen Simple para formatear el volumen:

      - Especifique el tamaño del volumen.
      - Proporcionar una letra de unidad.
      - Seleccione el sistema de archivos NTFS.
      - Especifique un tamaño de unidad de asignación de 64 KB.
      - Realizar un formato rápido.

7. Dar formato a volúmenes con varias particiones. Para obtener instrucciones, vaya a la sección, "Particiones y volúmenes" en [Implementar la administración de disco](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Ver información sobre los volúmenes

Utilice el procedimiento siguiente para ver información sobre volúmenes de Azure StorSimple y local.

#### <a name="to-view-volume-information"></a>Para ver la información de volumen

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple. 

2. En el panel de **ámbito** , haga clic en el nodo de **volúmenes** . Una lista de volúmenes locales y montadas, incluidos todos los volúmenes de Azure StorSimple, aparece en el panel de **resultados** . Las columnas en el panel de **resultados** son configurables. (Haga clic en el nodo de **volúmenes** , seleccione **la vista**y, a continuación, seleccione **Agregar o quitar columnas**).

    ![Configurar las columnas](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)

    Columna de resultados | Descripción 
    :--------------|:-------------
    Nombre           | La columna **nombre** contiene la letra de unidad asignada a cada volumen descubierto.
    Dispositivo         | La columna **dispositivo** contiene la dirección IP del dispositivo conectado al equipo host.
    Nombre de volumen de dispositivo | La columna **Nombre de volumen de dispositivo** contiene el nombre del volumen de dispositivo al que pertenece el volumen seleccionado. Este es el nombre de volumen definido en el portal de Azure clásico de dicho volumen específico.
    Rutas de acceso   | La columna de **Las rutas de acceso** muestra la ruta de acceso para el volumen. Esta es la unidad letra o un punto en que el volumen es accesible en el equipo host.
 
## <a name="delete-a-volume"></a>Eliminar un volumen

Use el procedimiento siguiente para eliminar un volumen desde el Administrador de instantáneas StorSimple.

>[AZURE.NOTE] No puede eliminar un volumen si forma parte de ningún grupo de volumen. (La opción Eliminar no está disponible para volúmenes de miembros de un grupo de volumen). Debe eliminar el grupo de volumen completo para eliminar el volumen.


#### <a name="to-delete-a-volume"></a>Para eliminar un volumen

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple.

2. En el panel de **ámbito** , haga clic en el nodo de **volúmenes** . 

3. En el panel de **resultados** , haga clic en el volumen que desea eliminar.

4. En el menú, haga clic en **Eliminar**. 

    ![Eliminar un volumen](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 

5. Aparece el cuadro de diálogo **Eliminar volumen** . Escriba **Confirm** en el cuadro de texto y, a continuación, haga clic en **Aceptar**.

6. De forma predeterminada, el Administrador de instantáneas StorSimple copia un volumen antes de eliminarlo. Esta precaución puede protegerle de pérdida de datos si la eliminación fue no intencionada. Administrador de instantáneas StorSimple muestra un mensaje de progreso de **Instantánea automática** mientras se copia el volumen. 

    ![Mensaje de instantánea automática](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Volver a examinar volúmenes

Use el procedimiento siguiente para volver a examinar los volúmenes conectados a StorSimple instantánea Manager.

#### <a name="to-rescan-the-volumes"></a>Para volver a los volúmenes

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple.

2. En el panel de **ámbito** contextual **volúmenes**y, a continuación, haga clic en **volver a examinar volúmenes**.

    ![Volver a examinar volúmenes](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
 
    Este procedimiento sincroniza la lista de volumen con el Administrador de instantáneas StorSimple. Los cambios, como nuevos volúmenes o volúmenes eliminados, se reflejarán en los resultados.

## <a name="configure-and-back-up-a-basic-volume"></a>Configurar y realizar copias de seguridad de un volumen básico

Use el procedimiento siguiente para configurar una copia de seguridad de un volumen básico y, a continuación, iniciar una copia de seguridad inmediatamente o crear una directiva para copias de seguridad programadas.

### <a name="prerequisites"></a>Requisitos previos

Antes de comenzar:

- Asegúrese de que el equipo de StorSimple dispositivo y host están configurados correctamente. Para obtener más información, vaya a [implementar el dispositivo de StorSimple local](storsimple-deployment-walkthrough-u2.md).

- Instalar y configurar el Administrador de instantáneas StorSimple. Para obtener más información, vaya a [Implementar el Administrador de instantáneas de StorSimple](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Para configurar la copia de seguridad de un volumen básico

1. Crear un volumen básico en el dispositivo StorSimple.

2. Montaje, inicializar y dar formato al volumen como se describe en [montaje volúmenes](#mount-volumes). 

3. Haga clic en el icono del Administrador de instantánea StorSimple en el escritorio. Aparece la ventana Administrador de instantáneas StorSimple. 

4. En el panel de **ámbito** , haga clic en el nodo de **volúmenes** y, a continuación, seleccione **volver a examinar volúmenes**. Cuando haya finalizado la búsqueda, una lista de volúmenes debería aparecer en el panel de **resultados** . 

5. En el panel de **resultados** , haga clic en el volumen y, a continuación, seleccione **Crear grupo de volumen**. 

    ![Crear grupo de volumen](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 

6. En el cuadro de diálogo **Crear grupo de volumen** , escriba un nombre para el grupo de volumen, asignar volúmenes a él y, a continuación, haga clic en **Aceptar**.

7. En el panel de **ámbito** , expanda el nodo de **Grupos de volumen** . El nuevo grupo de volumen debería aparecer en el nodo **Grupos de volumen** . 

8. Haga clic en el nombre del grupo de volumen.

    - Para iniciar un trabajo de copia de seguridad (a petición) interactivo, haga clic en **Realizar copia de seguridad**. 

    - Para programar una copia de seguridad automática, haga clic en **Crear directiva de copia de seguridad**. En la página **General** , seleccione un grupo de volumen de la lista. En la página **programación** , escriba los detalles de la programación. Cuando haya terminado, haga clic en **Aceptar**. 

9. Para confirmar que ha iniciado el trabajo de copia de seguridad, expanda el nodo **trabajos** en el panel de **ámbito** y, a continuación, haga clic en el nodo de **ejecución** . La lista de trabajos en ejecución aparece en el panel de **resultados** . 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Configurar y realizar copias de seguridad de un volumen reflejado dinámico

Complete los siguientes pasos para configurar la copia de seguridad de un volumen reflejado dinámico:

- Paso 1: Usar la administración de discos para crear un volumen reflejado dinámico. 

- Paso 2: Usar StorSimple instantánea el administrador configurar copia de seguridad.

### <a name="prerequisites"></a>Requisitos previos

Antes de comenzar:

- Asegúrese de que el equipo de StorSimple dispositivo y host están configurados correctamente. Para obtener más información, vaya a [implementar el dispositivo de StorSimple local](storsimple-deployment-walkthrough-u2.md).

- Instalar y configurar el Administrador de instantáneas StorSimple. Para obtener más información, vaya a [Implementar el Administrador de instantáneas de StorSimple](storsimple-snapshot-manager-deployment.md).

- Configurar dos volúmenes en el dispositivo StorSimple. (En los ejemplos, los volúmenes disponibles son **disco 1** y **2 de disco**). 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Paso 1: Usar la administración de disco para crear un volumen reflejado dinámico

Administración de discos es una utilidad del sistema de administración de disco duro y los volúmenes o particiones que contienen. Para obtener más información sobre la administración de disco, vaya a [Administración de disco](https://technet.microsoft.com/library/cc770943.aspx) en el sitio Web de Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Para crear un volumen reflejado dinámico

1. Use cualquiera de las siguientes opciones para iniciar la administración de disco: 

   - Abrir el cuadro **Ejecutar** , escriba **Diskmgmt.msc**y presione ENTRAR.

   - Inicie el administrador del servidor, expanda el nodo de **almacenamiento** y, a continuación, seleccione **Administración de discos**. 

   - Iniciar **Herramientas administrativas**, expanda el nodo de **Administración de equipos** y, a continuación, seleccione **Administración de disco**. 

2. Asegúrese de que tiene dos volúmenes disponibles en el dispositivo StorSimple. (En el ejemplo, los volúmenes disponibles son **disco 1** y **2 de disco**). 

3. En la ventana de administración de disco, en la columna derecha de la parte inferior, haga clic en **el disco 1** y seleccione **Nuevo volumen reflejado**. 

    ![Nuevo volumen reflejado](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 

4. En la página del Asistente para **Nuevo volumen reflejado** , haga clic en **siguiente**.

5. En la página **Seleccionar discos** , seleccione **disco 2** en el panel **seleccionado** , haga clic en **Agregar**y, a continuación, haga clic en **siguiente**. 

6. En la página **Asignar letra de unidad o la ruta de acceso** , acepte los valores predeterminados y, a continuación, haga clic en **siguiente**. 

7. En la página **Formatear volumen** , en el cuadro **Tamaño de la unidad de asignación** , seleccione **64 KB**. Active la casilla de verificación **realizar un formato rápido** y, a continuación, haga clic en **siguiente**. 

8. En la página **completar el nuevo volumen reflejado** , revise la configuración y, a continuación, haga clic en **Finalizar**. 

9. Aparece un mensaje para indicar que el disco básico se convertirá en un disco dinámico. Haga clic en **Sí**.

    ![Mensaje de conversión de disco dinámico](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 

10. En administración de discos, compruebe que disco 1 y 2 de disco se muestran como volúmenes reflejados dinámicos. (**Dinámica** debería aparecer en la columna Estado, y debe cambiar el color de la barra de capacidad a rojo, que indica un volumen reflejado). 

    ![Disco administración reflejada los discos dinámicos](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 
 
### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Paso 2: Usar el Administrador de instantáneas de StorSimple Configurar copia de seguridad

Use el procedimiento siguiente para configurar un volumen reflejado dinámico y comenzar una copia de seguridad inmediatamente o crear una directiva para copias de seguridad programadas.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Para configurar la copia de seguridad de un volumen reflejado dinámico

1. Haga clic en el icono del Administrador de instantánea StorSimple en el escritorio. Aparece la ventana Administrador de instantáneas StorSimple. 

2. En el panel de **ámbito** , haga clic en el nodo de **volúmenes** y seleccione **volver a examinar volúmenes**. Cuando haya finalizado la búsqueda, una lista de volúmenes debería aparecer en el panel de **resultados** . El volumen reflejado dinámico se muestra como un único volumen. 

3. En el panel de **resultados** , haga clic en el volumen reflejado dinámico y, a continuación, haga clic en **Crear grupo de volumen**. 

4. En el cuadro de diálogo **Crear grupo de volumen** , escriba un nombre para el grupo de volumen, asigne el volumen reflejado dinámico a este grupo y, a continuación, haga clic en **Aceptar**. 

5. En el panel de **ámbito** , expanda el nodo de **Grupos de volumen** . El nuevo grupo de volumen debería aparecer en el nodo **Grupos de volumen** . 

6. Haga clic en el nombre del grupo de volumen. 

    - Para iniciar un trabajo de copia de seguridad (a petición) interactivo, haga clic en **Realizar copia de seguridad**. 

    - Para programar una copia de seguridad automática, haga clic en **Crear directiva de copia de seguridad**. En la página **General** , seleccione el grupo de volumen de la lista. En la página **programación** , escriba los detalles de la programación. Cuando haya terminado, haga clic en **Aceptar**. 

7. Puede supervisar el trabajo de copia de seguridad mientras se ejecuta. En el panel de **ámbito** , expanda el nodo **trabajos** y, a continuación, haga clic en **Ejecutar**, los detalles del trabajo aparecen en el panel de **resultados** . Cuando haya finalizado el trabajo de copia de seguridad, los detalles se transfieren a la lista de trabajos de **últimas 24** horas. 

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [usar StorSimple instantánea Manager para administrar la solución StorSimple](storsimple-snapshot-manager-admin.md).
- Obtenga información sobre cómo [usar el Administrador de instantáneas StorSimple para crear y administrar grupos de volumen](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
