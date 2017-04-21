<properties 
   pageTitle="Directivas de copia de seguridad de administrador de instantáneas StorSimple | Microsoft Azure"
   description="Describe cómo usar el complemento de MMC Administrador de instantáneas StorSimple para crear y administrar las directivas de copia de seguridad que controlan copias de seguridad programadas."
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
   ms.date="05/12/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Usar el Administrador de instantánea StorSimple para crear y administrar las directivas de copia de seguridad

## <a name="overview"></a>Información general

Una directiva de copia de seguridad crea una programación de copia de los datos de volumen localmente o en la nube. Cuando se crea una directiva de copia de seguridad, también puede especificar una directiva de retención. (Puede conservar un máximo de 64 instantáneas). Para obtener más información acerca de las directivas de copia de seguridad, vea [tipos de copia de seguridad](storsimple-what-is-snapshot-manager.md#backup-type) en [serie 8000 StorSimple: una solución de nube híbrida](storsimple-overview.md).

En este tutorial se explica cómo:

- Crear una directiva de copia de seguridad 
- Editar una directiva de copia de seguridad 
- Eliminar una directiva de copia de seguridad 

## <a name="create-a-backup-policy"></a>Crear una directiva de copia de seguridad

Use el procedimiento siguiente para crear una nueva directiva de copia de seguridad.

#### <a name="to-create-a-backup-policy"></a>Para crear una directiva de copia de seguridad

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple.

2. En el panel de **ámbito** , haga clic en **Directivas de copia de seguridad**y haga clic en **Crear directiva de copia de seguridad**.

    ![Crear una directiva de copia de seguridad](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Aparece el cuadro de diálogo **crear una directiva** . 

    ![Crear una directiva - pestaña General](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)

3. En la pestaña **General** , rellene la información siguiente:

   1. En el cuadro de texto **nombre** , escriba un nombre para la directiva.

   2. En el cuadro de texto **grupo de volumen** , escriba el nombre del grupo de volumen asociado con la directiva.

   3. Seleccione **instantánea Local** o **instantánea de la nube**.

   4. Seleccione el número de instantáneas que desea conservar. Si selecciona **todos**, 64 instantáneas será conservan (el máximo). 

4. Haga clic en la pestaña **programación** .

    ![Crear una directiva - pestaña programación](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)

5. En la pestaña **programación** , rellene la información siguiente: 

   1. Haga clic en la casilla de verificación **Habilitar** para programar la siguiente copia de seguridad.

   2. En **configuración**, seleccione **una vez**, **diaria**, **semanal**o **mensual**. 

   3. En el cuadro de texto **Inicio** , haga clic en el icono de calendario y seleccione una fecha de inicio.

   4. En **Configuración avanzada**, puede establecer programaciones de repetición opcionales y una fecha de finalización.

   5. Haga clic en **Aceptar**.

Después de crear una directiva de copia de seguridad, la siguiente información aparece en el panel de **resultados** :

- **Nombre** : el nombre de la directiva de copia de seguridad.

- **Tipo** : instantánea local o una instantánea de la nube.

- **Grupo de volumen** : el grupo de volumen asociado con la directiva.

- **Retención** , el número de instantáneas que se conservan; el máximo es 64.

- **Creado** : la fecha en que se creó la directiva.

- **Habilitado** : si la directiva está actualmente en vigor: **True** indica que está en vigor; **False** indica que no es en efecto. 

## <a name="edit-a-backup-policy"></a>Editar una directiva de copia de seguridad

Use el siguiente procedimiento para editar una directiva de copia de seguridad existente.

#### <a name="to-edit-a-backup-policy"></a>Para editar una directiva de copia de seguridad

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple. 

2. En el panel de **ámbito** , haga clic en el nodo de **Directivas de copia de seguridad** . Todas las directivas de copia de seguridad aparecen en el panel de **resultados** . 

3. Haga clic en la directiva que desea editar y, a continuación, haga clic en **Editar**. 

    ![Editar una directiva de copia de seguridad](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png) 

4. Cuando aparezca la ventana **crear una directiva** , escriba los cambios y, a continuación, haga clic en **Aceptar**. 

## <a name="delete-a-backup-policy"></a>Eliminar una directiva de copia de seguridad

Use el procedimiento siguiente para eliminar una directiva de copia de seguridad.

#### <a name="to-delete-a-backup-policy"></a>Para eliminar una directiva de copia de seguridad

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple. 

2. En el panel de **ámbito** , haga clic en el nodo de **Directivas de copia de seguridad** . Todas las directivas de copia de seguridad aparecen en el panel de **resultados** . 

3. Haga clic en la directiva de copia de seguridad que desea eliminar y, a continuación, haga clic en **Eliminar**.

4. Cuando aparezca el mensaje de confirmación, haga clic en **Sí**.

    ![Eliminar confirmación de directiva de copia de seguridad](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [usar StorSimple instantánea Manager para administrar la solución StorSimple](storsimple-snapshot-manager-admin.md).
- Obtenga información sobre cómo [usar el Administrador de instantáneas StorSimple para ver y administrar trabajos de copia de seguridad](storsimple-snapshot-manager-manage-backup-jobs.md).
