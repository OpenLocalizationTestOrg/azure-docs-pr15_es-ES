<properties
    pageTitle="Administrar servidores mediante el modelo de implementación clásico de Azure y depósitos de copia de seguridad de Azure | Microsoft Azure"
    description="Use este tutorial para aprender a administrar depósitos de copia de seguridad de Azure y servidores."
    services="backup"
    documentationCenter=""
    authors="markgalioto"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="jimpark;markgal"/>


# <a name="manage-azure-backup-vaults-and-servers-using-the-classic-deployment-model"></a>Administrar depósitos de copia de seguridad de Azure y servidores mediante el modelo de implementación clásica

> [AZURE.SELECTOR]
- [Administrador de recursos](backup-azure-manage-windows-server.md)
- [Clásico](backup-azure-manage-windows-server-classic.md)

En este artículo encontrará una descripción general de las tareas de administración de copia de seguridad disponibles a través del portal clásico Azure y el agente de copia de seguridad de Microsoft Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo de implementación de administrador de recursos.

## <a name="management-portal-tasks"></a>Tareas de administración de portal
1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).

2. Haga clic en **Servicios de recuperación**y luego haga clic en el nombre del depósito de copia de seguridad para ver la página de inicio rápido.

    ![Servicios de recuperación](./media/backup-azure-manage-windows-server-classic/rs-left-nav.png)

Seleccionando las opciones en la parte superior de la página de inicio rápido, puede ver las tareas de administración disponibles.

![Administrar las pestañas](./media/backup-azure-manage-windows-server-classic/qs-page.png)

### <a name="dashboard"></a>Panel
Seleccione el **panel** para ver un resumen de uso para el servidor. La **información general de uso** incluye:

- El número de servidores de Windows registrado en la nube
- El número de Azure máquinas virtuales de Windows protegido en nube
- El almacenamiento total consumido en Azure
- El estado de los trabajos recientes

En la parte inferior del panel puede realizar las siguientes tareas:

- **Certificado de administrar** : si se ha usado un certificado para registrar el servidor, a continuación, use esta opción para actualizar el certificado. Si está utilizando las credenciales de la cámara, no utilice **Administrar certificado**.
- **Eliminar** : elimina el depósito de copia de seguridad actual. Si ya no se utiliza un depósito de copia de seguridad, puede eliminarlo para liberar espacio de almacenamiento. **Eliminar** sólo está habilitado después de que se hayan eliminado todos los servidores registrados desde la cámara.

![Tareas de copia de seguridad de paneles](./media/backup-azure-manage-windows-server-classic/dashboard-tasks.png)

## <a name="registered-items"></a>Elementos registrados
Seleccionar **Elementos registrados** para ver los nombres de los servidores que están registrados en este depósito.

![Elementos registrados](./media/backup-azure-manage-windows-server-classic/registered-items.png)

El filtro de **tipo** de forma predeterminada en Máquina Virtual de Azure. Para ver los nombres de los servidores que están registrados en este depósito, seleccione **Windows server** desde el menú desplegable.

Desde aquí puede realizar las siguientes tareas:

- **Permitir que el nuevo registro** : cuando se selecciona esta opción para un servidor puede utilizar el **Asistente de registro** en el agente de copia de seguridad de Microsoft Azure local para registrar el servidor con la copia de seguridad depósito una segunda vez. Debe volver a registrar debido a un error en el certificado o si tenía un servidor que volver a crearse.
- **Eliminar** - elimina un servidor de la cámara de copia de seguridad. Se elimina todos los datos almacenados asociados con el servidor inmediatamente.

    ![Tareas de artículos registrados](./media/backup-azure-manage-windows-server-classic/registered-items-tasks.png)

## <a name="protected-items"></a>Elementos protegidos
Seleccionar **Elementos protegidos** para ver los elementos que han se ha realizado una copia de los servidores.

![Elementos protegidos](./media/backup-azure-manage-windows-server-classic/protected-items.png)

## <a name="configure"></a>Configurar

En la pestaña **Configurar** puede seleccionar la opción de redundancia de almacenamiento apropiado. La mejor hora para seleccionar la opción de redundancia de almacenamiento es correcta después de crear un depósito y antes de que todos los equipos están registrados en él.

>[AZURE.WARNING] Una vez que se ha registrado un elemento a la cámara, la opción de redundancia de almacenamiento está bloqueada y no se puede modificar.

![Configurar](./media/backup-azure-manage-windows-server-classic/configure.png)

Consulte este artículo para obtener más información acerca de [redundancia de almacenamiento](../storage/storage-redundancy.md).

## <a name="microsoft-azure-backup-agent-tasks"></a>Tareas de agente de copia de seguridad de Microsoft Azure

### <a name="console"></a>Consola

Abra el **agente de copia de seguridad de Microsoft Azure** (puede encontrarlo buscando en el equipo de *Copia de seguridad de Microsoft Azure*).

![Agente de copia de seguridad](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)

Puede realizar las siguientes tareas de administración de las **acciones** disponibles a la derecha de la consola de agente de copia de seguridad:

- Registrar servidor
- Copia de seguridad de programación
- Realizar una copia de seguridad ahora
- Cambiar las propiedades

![Acciones de consola de agente](./media/backup-azure-manage-windows-server-classic/console-actions.png)

>[AZURE.NOTE] Para **Recuperar datos**, consulte [Restaurar archivos en un servidor de Windows o el equipo cliente de Windows](backup-azure-restore-windows-server.md).

### <a name="modify-an-existing-backup"></a>Modificar una copia de seguridad existente

1. Haga clic en **Programar copia de seguridad**en el agente de copia de seguridad de Microsoft Azure.

    ![Programar una copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)

2. En el **Asistente para la programación de copia de seguridad** , deje seleccionada la opción **realizar cambios en los elementos de copia de seguridad u horas** y haga clic en **siguiente**.

    ![Modificar una copia de seguridad programada](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)

3. Si desea agregar o modificar elementos, haga clic en **Agregar elementos**en la pantalla **Seleccionar elementos de la copia de seguridad** .

    También puede establecer la **Configuración de exclusión** de esta página del asistente. Si desea excluir archivos o tipos de archivo lea el procedimiento para agregar una [configuración de exclusiones](#exclusion-settings).

4. Seleccione los archivos y carpetas que desea hacer copia de seguridad y haga clic en **Aceptar**.

    ![Agregar elementos](./media/backup-azure-manage-windows-server-classic/add-items-modify.png)

5. Especifique la **programación de copia de seguridad** y haga clic en **siguiente**.

    Puede programar diaria (con un máximo de 3 veces al día) o copias de seguridad semanales.

    ![Especifique la programación de copia de seguridad](./media/backup-azure-manage-windows-server-classic/specify-backup-schedule-modify-close.png)

    >[AZURE.NOTE] Especificar la programación de copia de seguridad se explica detalladamente en este [artículo](backup-azure-backup-cloud-as-tape.md).

6. Seleccione la **Directiva de retención** de la copia de seguridad y haga clic en **siguiente**.

    ![Seleccione la directiva de retención](./media/backup-azure-manage-windows-server-classic/select-retention-policy-modify.png)

7. En la pantalla de **confirmación** , revise la información y haga clic en **Finalizar**.

8. Una vez que el asistente termine de crear la **programación de copia de seguridad**, haga clic en **Cerrar**.

    Después de modificar la protección, puede confirmar que las copias de seguridad activan correctamente, vaya a la pestaña **tareas** y confirmar que los cambios se reflejarán en los trabajos de copia de seguridad.

### <a name="enable-network-throttling"></a>Habilitar el límite de red  
El agente de copia de seguridad de Azure proporciona una ficha de regulación que le permite controlar cómo se usa el ancho de banda durante la transferencia de datos. Este control puede ser útil si necesita hacer una copia de los datos durante horas de trabajo pero no desea que el proceso de copia de seguridad para interferir con otro tráfico de internet. Limitación de datos se aplica transferencia para realizar copias de seguridad y restaurar las actividades.  

Habilitar el límite:

1. En el **agente de copia de seguridad**, haga clic en **Cambiar las propiedades**.

2. Seleccione la casilla de verificación **Habilitar límite para operaciones de copia de seguridad de uso de ancho de banda de internet** .

    ![Límite de red](./media/backup-azure-manage-windows-server-classic/throttling-dialog.png)

3. Una vez habilitada la limitación, especifique el ancho de banda permitido para transferir datos de copia de seguridad durante el **horario de trabajo** y las **horas de trabajo no**.

    Los valores de ancho de banda comienzan en 512 kilobytes por segundo y pueden ir hasta 1023 megabytes por segundo (Mbps). También puede designar el inicio y finalización para las **horas de trabajo**y los días de la semana se consideran trabajo días. Se considera el tiempo fuera de las horas de trabajo designados no jornada laboral.

4. Haga clic en **Aceptar**.

## <a name="exclusion-settings"></a>Configuración de exclusiones

1. Abra el **agente de copia de seguridad de Microsoft Azure** (puede encontrarlo buscando en el equipo de *Copia de seguridad de Microsoft Azure*).

    ![Abrir agente de copia de seguridad](./media/backup-azure-manage-windows-server-classic/snap-in-search.png)

2. Haga clic en **Programar copia de seguridad**en el agente de copia de seguridad de Microsoft Azure.

    ![Programar una copia de seguridad de Windows Server](./media/backup-azure-manage-windows-server-classic/schedule-backup.png)

3. En el Asistente de copia de seguridad de programación deje seleccionada la opción **realizar cambios en los elementos de copia de seguridad u horas** y haga clic en **siguiente**.

    ![Modificar una programación](./media/backup-azure-manage-windows-server-classic/modify-or-stop-a-scheduled-backup.png)

4. Haga clic en **configuración de exclusiones**.

    ![Seleccione los elementos que se excluyen](./media/backup-azure-manage-windows-server-classic/exclusion-settings.png)

5. Haga clic en **Agregar exclusión**.

    ![Agregar exclusiones](./media/backup-azure-manage-windows-server-classic/add-exclusion.png)

6. Seleccione la ubicación y, a continuación, haga clic en **Aceptar**.

    ![Seleccione una ubicación de exclusión](./media/backup-azure-manage-windows-server-classic/exclusion-location.png)

7. Agregar la extensión de archivo en el campo **Tipo de archivo** .

    ![Excluir por tipo de archivo](./media/backup-azure-manage-windows-server-classic/exclude-file-type.png)

    Agregar una extensión. mp3

    ![Ejemplo de tipo de archivo](./media/backup-azure-manage-windows-server-classic/exclude-mp3.png)

    Para agregar otra extensión, haga clic en **Agregar exclusión** y escriba otra extensión de tipo de archivo (agregando una extensión .jpeg).

    ![Otro ejemplo de tipo de archivo](./media/backup-azure-manage-windows-server-classic/exclude-jpg.png)

8. Cuando haya agregado todas las extensiones, haga clic en **Aceptar**.

9. Continúe con el Asistente de copia de seguridad de programación haciendo clic en **siguiente** hasta la **página de confirmación**, haga clic en **Finalizar**.

    ![Confirmación de exclusiones](./media/backup-azure-manage-windows-server-classic/finish-exclusions.png)

## <a name="next-steps"></a>Pasos siguientes
- [Restaurar Windows Server o cliente de Windows de Azure](backup-azure-restore-windows-server.md)
- Para obtener más información acerca de la copia de seguridad de Azure, consulte [Información general de copia de seguridad de Azure](backup-introduction-to-azure-backup.md)
- Visite el [foro de copia de seguridad de Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933)
