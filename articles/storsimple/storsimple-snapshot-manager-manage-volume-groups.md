<properties 
   pageTitle="Grupos de volumen de administrador de instantáneas StorSimple | Microsoft Azure"
   description="Describe cómo usar el complemento de MMC Administrador de instantáneas StorSimple para crear y administrar grupos de volumen."
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

# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Use el Administrador de instantáneas de StorSimple para crear y administrar grupos de volumen

## <a name="overview"></a>Información general

Puede usar el nodo **Grupos de volumen** en el panel de **ámbito** para asignar volúmenes a grupos de volumen, ver información sobre un grupo de volumen, programar las copias de seguridad y editar grupos de volumen. 

Los grupos de volumen son grupos de volúmenes relacionados utilizados para asegurarse de que las copias de seguridad son coherentes para la aplicación. Para obtener más información, consulte [volúmenes y grupos de volumen](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) y la [integración con el servicio de instantáneas de volumen de Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

>[AZURE.IMPORTANT] 
>
> * Todos los volúmenes en un grupo de volumen deben proceder de un proveedor de servicios de nube solo.
> 
> * Al configurar grupos de volumen, no mezcle volúmenes compartidos de clúster (CSVs) y no CSVs en el mismo grupo de volumen. Administrador de instantáneas StorSimple no admite una mezcla de CSVs y no CSVs en la misma instantánea.
 
![Nodo de grupos de volumen](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Figura 1: Nodo de grupos de volumen del Administrador de StorSimple instantánea** 

En este tutorial se explica cómo puede usar el Administrador de instantáneas StorSimple para:

- Ver información sobre los grupos de volumen 
- Crear un grupo de volumen
- Realizar copias de seguridad de un grupo de volumen
- Editar un grupo de volumen
- Eliminar un grupo de volumen

Todas estas acciones también están disponibles en el panel de **acciones** .
 
## <a name="view-volume-groups"></a>Ver grupos de volumen

Si hace clic en el nodo **Grupos de volumen** , el panel de **resultados** muestra la siguiente información acerca de cada grupo de volumen, según las selecciones de la columna que realice. (Las columnas en el panel de **resultados** son configurables. Haga clic en el nodo **volúmenes** , seleccione **la vista**y, a continuación, seleccione **Agregar o quitar columnas**.)

Columna de resultados | Descripción 
:--------------|:------------ 
Nombre           | La columna **nombre** contiene el nombre del grupo de volumen.
Aplicación    | La columna de **aplicaciones** muestra el número de autores de VSS actualmente instalado y en ejecución en el host de Windows.
Seleccionado       | La columna **seleccionado** muestra el número de volúmenes que se encuentran en el grupo de volumen. Cero (0) indica que no hay ninguna aplicación está asociada con los volúmenes en el grupo de volumen.
Importar       | La columna **importada** muestra el número de volúmenes importados. Cuando se establece en **True**, esta columna indica que un grupo de volumen se ha importado desde el portal de clásico Azure y no se creó en el Administrador de instantáneas StorSimple.
 
>[AZURE.NOTE] Grupos de volumen de StorSimple Administrador de instantáneas también se muestran en la ficha de **Directivas de copia de seguridad** en el portal de clásico de Azure.
 
## <a name="create-a-volume-group"></a>Crear un grupo de volumen

Use el procedimiento siguiente para crear un grupo de volumen.

#### <a name="to-create-a-volume-group"></a>Para crear un grupo de volumen

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple. 

2. En el panel de **ámbito** , haga clic en **Grupos de volumen**y, a continuación, haga clic en **Crear grupo de volumen**. 

    ![Crear grupo de volumen](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
 
    Aparece el cuadro de diálogo **crear un grupo de volumen** . 

    ![Crear un cuadro de diálogo del grupo de volumen](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png) 

3.  Escriba la información siguiente: 

    1. En el cuadro **nombre** , escriba un nombre único para el nuevo grupo de volumen. 

    2. En el cuadro de **aplicaciones** , seleccione aplicaciones asociadas con los volúmenes que va a agregar al grupo de volumen. 

        El cuadro **aplicaciones** enumera sólo las aplicaciones que utilizan StorSimple volúmenes y tienen los creadores VSS habilitadas para ellos. El escritor VSS está habilitado solo si todos los volúmenes que conoce el escritor se StorSimple volúmenes. Si el cuadro de aplicaciones está vacío, no se instalan aplicaciones que utilizan volúmenes de Azure StorSimple y han admitido creadores VSS. (Actualmente, Azure StorSimple admite Microsoft Exchange y SQL Server). Para obtener más información acerca de los autores de VSS, consulte [integración con el servicio de instantáneas de volumen de Windows](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

        Si selecciona una aplicación, se seleccionan automáticamente todos los volúmenes asociados. Por el contrario, si selecciona volúmenes asociados a una aplicación específica, la aplicación se selecciona automáticamente en el cuadro de **aplicaciones** . 

    3. En el cuadro **volúmenes** , seleccione volúmenes StorSimple para agregar al grupo de volumen. 

      - Puede incluir volúmenes con único o múltiples particiones. (Varios volúmenes de partición pueden ser discos dinámicos o discos básicos con varias particiones.) Un volumen que contiene varias particiones se trata como una sola unidad. Por lo tanto, si agrega solo una de las particiones a un grupo de volumen, todas las particiones se agregan automáticamente a ese grupo de volumen al mismo tiempo. Después de agregar una partición de volumen varias a un grupo de volumen, el volumen de partición varios continúa tratará como una sola unidad.

      - Puede crear grupos de volumen vacía asignándoles no cualquier volumen. 

      - No mezcle volúmenes compartidos de clúster (CSVs) y no CSVs en el mismo grupo de volumen. Administrador de instantáneas StorSimple no admite una mezcla de volúmenes de CSV y no CSV en la misma instantánea. 

4. Haga clic en **Aceptar** para guardar el grupo de volumen.

## <a name="back-up-a-volume-group"></a>Realizar copias de seguridad de un grupo de volumen

Use el siguiente procedimiento para realizar copias de seguridad de un grupo de volumen.

#### <a name="to-back-up-a-volume-group"></a>Para realizar copias de seguridad de un grupo de volumen

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple.

2. En el panel de **ámbito** , expanda el nodo **Grupos de volumen** , haga clic en el nombre de un grupo de volumen y, a continuación, haga clic en **Realizar copia de seguridad**. 

    ![Copia de seguridad grupo de volumen inmediatamente](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)

3. En el cuadro de diálogo **Realizar copia de seguridad** , seleccione **Instantánea Local** o una **Instantánea de la nube**y, a continuación, haga clic en **crear**. 

    ![Tomar el cuadro de diálogo copia de seguridad](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png) 

4. Para confirmar que se está ejecutando la copia de seguridad, expanda el nodo **trabajos** y, a continuación, haga clic en **Ejecutar**. Debe aparecer la copia de seguridad.

5. Para ver la instantánea completada, expanda el nodo de **Catálogo de copia de seguridad** , expanda el nombre del grupo de volumen y, a continuación, haga clic en **Instantánea Local** o una **Instantánea de la nube**. Si ha finalizado correctamente, se mostrará la copia de seguridad. 

## <a name="edit-a-volume-group"></a>Editar un grupo de volumen

Use el siguiente procedimiento para editar un grupo de volumen.

#### <a name="to-edit-a-volume-group"></a>Para editar un grupo de volumen

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple.

2. En el panel de **ámbito** , expanda el nodo **Grupos de volumen** , haga clic en el nombre de un grupo de volumen y, a continuación, haga clic en **Editar**. 

3. Aparece el cuadro de diálogo **crear un grupo de volumen **. Puede cambiar las entradas de **nombre**, **aplicaciones**y **volúmenes** . 

4. Haga clic en **Aceptar** para guardar los cambios.

## <a name="delete-a-volume-group"></a>Eliminar un grupo de volumen

Use el procedimiento siguiente para eliminar un grupo de volumen. 

>[AZURE.WARNING] También se eliminan todas las copias de seguridad asociados con el grupo de volumen.

#### <a name="to-delete-a-volume-group"></a>Para eliminar un grupo de volumen

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple. 

2. En el panel de **ámbito** , expanda el nodo **Grupos de volumen** , haga clic en el nombre de un grupo de volumen y, a continuación, haga clic en **Eliminar**. 

3. Aparece el cuadro de diálogo **Eliminar grupo de volumen** . Escriba **Confirm** en el cuadro de texto y, a continuación, haga clic en **Aceptar**. 

    El grupo de volumen eliminado desaparecerá de la lista en el panel de **resultados** y se eliminan todas las copias de seguridad que están asociadas a ese grupo de volumen desde el catálogo de copia de seguridad.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [usar StorSimple instantánea Manager para administrar la solución StorSimple](storsimple-snapshot-manager-admin.md).
- Obtenga información sobre cómo [usar el Administrador de instantáneas StorSimple para crear y administrar las directivas de copia de seguridad](storsimple-snapshot-manager-manage-backup-policies.md).
