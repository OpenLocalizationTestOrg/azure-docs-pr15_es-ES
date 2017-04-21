<properties 
   pageTitle="Catálogo de copia de seguridad de administrador de instantáneas StorSimple | Microsoft Azure"
   description="Describe cómo usar el complemento de MMC StorSimple instantánea administrador para ver y administrar el catálogo de copia de seguridad."
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
   ms.date="04/26/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Usar el Administrador de instantánea StorSimple para administrar el catálogo de copia de seguridad

## <a name="overview"></a>Información general

La función principal del Administrador de instantánea StorSimple es que le permite crear copias de seguridad coherentes para la aplicación de volúmenes de StorSimple en el formulario de instantáneas. Instantáneas, a continuación, se muestran en un archivo XML denominado *catálogo de copia de seguridad*. El catálogo de copia de seguridad organiza instantáneas por grupo de volumen y luego por instantánea local o una instantánea de la nube. 

Este tutorial describe cómo puede usar el nodo **Catálogo de copia de seguridad** para completar las siguientes tareas:

- Restaurar un volumen 
- Clonar un volumen o un grupo de volumen 
- Eliminar una copia de seguridad 
- Recuperar un archivo
- Restaurar la base de datos de administrador de instantáneas Storsimple

Puede ver el catálogo de copia de seguridad, expanda el nodo de **Catálogo de copia de seguridad** en el panel de **ámbito** y, a continuación, expandir el grupo de volumen.

- Si hace clic en el nombre del grupo de volumen, el panel de **resultados** muestra el número de copias instantáneas locales e instantáneas de nube disponibles para el grupo de volumen. 

- Si hace clic en **Instantánea Local** o una **Instantánea de la nube**, el panel de **resultados** muestra la siguiente información sobre cada instantánea de copia de seguridad (según la configuración de **vista** ): 

    - **Nombre** : el tiempo que se realizó la instantánea. 

    - **Tipo** : si se trata de una instantánea local o una instantánea de la nube. 

    - **Propietario** : el propietario del contenido. 

    - **Disponible** : si la instantánea está disponible actualmente. **True** indica que la instantánea está disponible y pueden restaurarse; **False** indica que la instantánea ya no está disponible. 

    - **Importados** : si se ha importado la copia de seguridad. **True** indica que la copia de seguridad se importó desde el servicio Administrador de StorSimple en el momento en que el dispositivo se ha configurado en el Administrador de instantáneas StorSimple; **False** indica que no se ha importado, pero se ha creado por el Administrador de instantáneas StorSimple. (Puede identificar fácilmente un grupo de volumen importado porque se agrega un sufijo que identifica el dispositivo desde el que se ha importado el grupo de volumen.)

    ![Catálogo de copia de seguridad](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)

- Si expandir **Instantánea Local** o una **Instantánea de la nube**y, a continuación, haga clic en un nombre de instantánea individual, el panel de **resultados** muestra la siguiente información sobre la instantánea seleccionada:

    - **Nombre** : el volumen identificado por letra. 

    - **Nombre local** : el nombre local de la unidad (si está disponible). 

    - **Dispositivo** : el nombre del dispositivo en el que reside el volumen. 

    - **Disponible** : si la instantánea está disponible actualmente. **True** indica que la instantánea está disponible y pueden restaurarse; **False** indica que la instantánea ya no está disponible. 


## <a name="restore-a-volume"></a>Restaurar un volumen

Use el procedimiento siguiente para restaurar un volumen de copia de seguridad.

#### <a name="prerequisites"></a>Requisitos previos

Si no lo ha hecho ya, crear un grupo de volumen y el volumen y, a continuación, elimine el volumen. De forma predeterminada, el Administrador de instantáneas StorSimple copia un volumen antes de permitir que se va a eliminar. Esta precaución puede evitar la pérdida de datos si se elimina accidentalmente el volumen o si los datos deben recuperarse por cualquier motivo. 

Administrador de instantáneas StorSimple muestra el siguiente mensaje mientras crea la copia de seguridad de precaución.

![Mensaje de instantánea automática](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

>[AZURE.IMPORTANT]No puede eliminar un volumen que forma parte de un grupo de volumen. La opción Eliminar está disponible. <br>

#### <a name="to-restore-a-volume"></a>Para restaurar un volumen

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple. 

2. En el panel de **ámbito** , expanda el nodo de **Catálogo de copia de seguridad** , expandir un grupo de volumen y, a continuación, haga clic en **Instantáneas locales** o **Instantáneas de la nube**. Una lista de instantáneas de copia de seguridad aparece en el panel de **resultados** . 

3. Busque la copia de seguridad que desea restaurar, con el botón secundario y, a continuación, haga clic en **Restaurar**. 

    ![Restaurar el catálogo de copia de seguridad](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 

4. En la página de confirmación, revise los detalles, escriba **Confirmar**y, a continuación, haga clic en **Aceptar**. Administrador de instantáneas StorSimple utiliza la copia de seguridad para restaurar el volumen. 

    ![Restaurar el mensaje de confirmación](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 

5. Puede supervisar la acción Restaurar mientras se ejecuta. En el panel de **ámbito** , expanda el nodo **trabajos** y, a continuación, haga clic en **Ejecutar**. Los detalles del trabajo aparecen en el panel de **resultados** . Cuando haya finalizado el trabajo de restauración, los detalles del trabajo se transfieren a la lista de las **últimas 24 horas** .

## <a name="clone-a-volume-or-volume-group"></a>Clonar un volumen o un grupo de volumen

Use el procedimiento siguiente para crear un duplicado (clonar) de un grupo de volumen o de volumen.

#### <a name="to-clone-a-volume-or-volume-group"></a>Clonar un volumen o un grupo de volumen

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple.

2. En el panel de **ámbito** , expanda el nodo de **Catálogo de copia de seguridad** , expanda un grupo de volumen y, a continuación, haga clic en **Instantáneas de nube**. Aparece una lista de las copias de seguridad en el panel de **resultados** .

3. Busque el volumen o el grupo de volumen que desea duplicar, haga clic en el volumen o el nombre del grupo de volumen y haga clic en **clonar**. Aparece el cuadro de diálogo **Clonar nube instantánea** .

    ![Clonar una instantánea de la nube](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 

4. Complete el cuadro de diálogo de **Clonar nube instantánea** como sigue: 

    1. En el cuadro de texto **nombre** , escriba un nombre para el volumen duplicado. Este nombre aparecerá en el nodo de **volúmenes** . 

    2. (Opcional) seleccione **unidad**y, a continuación, seleccione una letra de unidad de la lista desplegable. 

    3. (Opcional) seleccione **Carpeta (NTFS)**, escriba una ruta de acceso de la carpeta o haga clic en Examinar y seleccione una ubicación para la carpeta. 

    4. Haga clic en **crear**.

5. Cuando termine el proceso de clonación, debe inicializarse el volumen duplicado. Inicie el administrador del servidor y a continuación, inicie administración de discos. Para obtener instrucciones detalladas, consulte [montaje volúmenes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Una vez inicializado, el volumen aparecerá en el nodo de **volúmenes** en el panel de **ámbito** . Si no ve el volumen mostrado, actualice la lista de volúmenes (haga clic en el nodo de **volúmenes** y, a continuación, haga clic en **Actualizar**).

## <a name="delete-a-backup"></a>Eliminar una copia de seguridad

Use el procedimiento siguiente para eliminar una instantánea del catálogo de copia de seguridad. 

>[AZURE.NOTE]Al eliminar una instantánea de los datos de copia de seguridad asociados a la instantánea. Sin embargo, el proceso de limpieza de datos de la nube puede tardar algún tiempo.<br>
 
#### <a name="to-delete-a-backup"></a>Para eliminar una copia de seguridad

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple.

2. En el panel de **ámbito** , expanda el nodo de **Catálogo de copia de seguridad** , expandir un grupo de volumen y, a continuación, haga clic en **Instantáneas locales** o **Instantáneas de la nube**. Una lista de instantáneas aparece en el panel de **resultados** . 

3. Haga clic en la instantánea que desea eliminar y, a continuación, haga clic en **Eliminar**.

4. Cuando aparezca el mensaje de confirmación, haga clic en **Aceptar**. 

## <a name="recover-a-file"></a>Recuperar un archivo

Si un archivo se elimina accidentalmente un volumen, puede recuperar el archivo de recuperación de una instantánea que las fechas anteriores a la eliminación, usando la instantánea para crear una copia del volumen y, a continuación, copie el archivo desde el volumen duplicado en el volumen original.

#### <a name="prerequisites"></a>Requisitos previos

Antes de empezar, asegúrese de que tiene una copia de seguridad del grupo de volumen. Eliminar un archivo almacenado en uno de los volúmenes de ese grupo de volumen. Por último, realice los pasos siguientes para restaurar el archivo eliminado de la copia de seguridad. 

#### <a name="to-recover-a-deleted-file"></a>Recuperar un archivo eliminado

1. Haga clic en el icono del Administrador de instantánea StorSimple en el escritorio. Aparece la ventana de la consola de administrador de instantáneas StorSimple. 

2. En el panel de **ámbito** , expanda el nodo de **Catálogo de copia de seguridad** y busque una instantánea que contiene el archivo eliminado. Normalmente, debe seleccionar una instantánea que se creó antes de la eliminación. 

3. Encontrar el volumen que desea duplicar, con el botón secundario y haga clic en **clonar**. Aparece el cuadro de diálogo **Clonar nube instantánea** .

    ![Clonar una instantánea de la nube](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 

4. Complete el cuadro de diálogo de **Clonar nube instantánea** como sigue: 

   1. En el cuadro de texto **nombre** , escriba un nombre para el volumen duplicado. Este nombre aparecerá en el nodo de **volúmenes** . 

   2. (Opcional) Seleccione la **unidad**y, a continuación, seleccione una letra de unidad de la lista desplegable. 

   3. (Opcional) Seleccione **Carpeta (NTFS)**, escriba una ruta de acceso de la carpeta o haga clic en **Examinar** y seleccione una ubicación para la carpeta. 

   4. Haga clic en **crear**. 

5. Cuando termine el proceso de clonación, debe inicializarse el volumen duplicado. Inicie el administrador del servidor y a continuación, inicie administración de discos. Para obtener instrucciones detalladas, consulte [montaje volúmenes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Una vez inicializado, el volumen aparecerá en el nodo de **volúmenes** en el panel de **ámbito** . 

    Si no ve el volumen mostrado, actualice la lista de volúmenes (haga clic en el nodo de **volúmenes** y, a continuación, haga clic en **Actualizar**).

6. Abra la carpeta NTFS que contiene el volumen duplicado, expanda el nodo de **volúmenes** y, a continuación, abra el volumen duplicado. Busque el archivo que desee recuperar y copiarlo en el volumen principal.

7. Después de restaurar el archivo, puede eliminar la carpeta NTFS que contiene el volumen duplicado.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Restaurar la base de datos de administrador de instantáneas StorSimple

Deben realizarse copias de la base de datos de administrador de instantáneas StorSimple en el equipo host. Si se produce un desastre o se produce un error en el equipo host por cualquier motivo, se puede restaurar desde la copia de seguridad. Creación de la copia de seguridad de base de datos es un proceso manual.

#### <a name="to-back-up-and-restore-the-database"></a>Para realizar copias de seguridad y restaurar la base de datos

1. Detenga el servicio de administración de Microsoft StorSimple:

    1. Inicie el administrador del servidor.

    2. En el panel de administrador del servidor, en el menú **Herramientas** , seleccione **Servicios**.

    3. En la ventana **Servicios** , seleccione el **Servicio de administración de Microsoft StorSimple**.

    4. En el panel derecho, en el **Servicio de administración de StorSimple de Microsoft**, haga clic en **Detener el servicio**.

2. En el equipo host, vaya a C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    >[AZURE.NOTE] ProgramData es una carpeta oculta.
 
3. Busque el archivo XML de catálogo, copie el archivo y almacenar la copia en una ubicación segura o en la nube. Si se produce un error en el host, puede usar este archivo de copia de seguridad para ayudar a recuperar las directivas de copia de seguridad que creó en el Administrador de instantáneas StorSimple.

    ![Archivo de copia de seguridad del catálogo de Azure StorSimple](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)

4. Reinicie el servicio de administración de Microsoft StorSimple: 

    1. En el panel de administrador del servidor, en el menú **Herramientas** , seleccione **Servicios**.
    
    2. En la ventana **Servicios** , seleccione el **Servicio de administración de Microsoft StorSimple**.

    3. En el panel derecho, en el **Servicio de administración de StorSimple de Microsoft**, haga clic en **reiniciar el servicio**.

5. En el equipo host, vaya a C:\ProgramData\Microsoft\StorSimple\BACatalog. 

6. Elimine el archivo XML de catálogo y reemplazar con la versión de copia de seguridad que ha creado. 

7. Haga clic en el icono del Administrador de instantáneas StorSimple escritorio para iniciar el Administrador de instantánea StorSimple. 

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el [uso de administrador de instantáneas StorSimple para administrar la solución StorSimple](storsimple-snapshot-manager-admin.md).
- Más información sobre [los flujos de trabajo y las tareas de administrador de instantáneas StorSimple](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).
