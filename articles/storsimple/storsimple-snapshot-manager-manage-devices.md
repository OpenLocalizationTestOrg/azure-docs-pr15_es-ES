<properties 
   pageTitle="Administrar dispositivos con el Administrador de instantáneas StorSimple | Microsoft Azure"
   description="Describe cómo usar el complemento de MMC Administrador de instantáneas StorSimple para conectar y administrar dispositivos StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Usar el Administrador de instantánea para conectarse y administrar dispositivos de StorSimple StorSimple

## <a name="overview"></a>Información general

Puede usar los nodos en el panel de administrador de instantáneas StorSimple **ámbito** comprobar los datos importados de dispositivo de StorSimple y actualizar los dispositivos de almacenamiento conectados. Además, al hacer clic en el nodo de **dispositivos** , puede ver una lista de dispositivos conectados y la información de estado correspondiente en el panel de **resultados** .

![Dispositivos conectados](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Figura 1: Administrador de instantáneas StorSimple conectado dispositivo** 

Según las selecciones de **vista** , el panel de **resultados** muestra la siguiente información sobre cada dispositivo. (Para obtener más información acerca de cómo configurar una vista, vaya al [menú Ver](storsimple-use-snapshot-manager.md#view-menu).


| Columna de resultados  |Descripción          |
|:----------------|:--------------------| 
| Nombre            | El nombre del dispositivo tal como está configurado en el portal de clásico de Azure|
| Modelo           | El número de modelo del dispositivo|
| Versión         | La versión del software instalado en el dispositivo |
| Estado          | Si el dispositivo está disponible |
| Sincronizó por última vez     | Fecha y hora cuando se sincronizó por última vez el dispositivo |
| Nº serie      | El número de serie para el dispositivo |
 
Si haga el nodo de **dispositivos** en el panel de **alcance** , puede seleccionar entre las acciones siguientes:

- Agregar o reemplazar un dispositivo 
- Conecte un dispositivo y comprobar la importación 
- Actualizar los dispositivos conectados 

Si hace clic en el nodo **dispositivos** y, a continuación, haga clic en un nombre de dispositivo en el panel de **resultados** , puede seleccionar una de las siguientes acciones:

- Autenticar un dispositivo 
- Ver detalles del dispositivo 
- Actualizar un dispositivo 
- Eliminar una configuración de dispositivo 
- Cambiar una contraseña de dispositivo

>[AZURE.NOTE] Todas estas acciones también están disponibles en el panel de **acciones** .
 
Este tutorial explica cómo usar el Administrador de instantáneas StorSimple para conectarse y administrar dispositivos y realizar las siguientes tareas:

- Agregar o reemplazar un dispositivo 
- Conecte un dispositivo y comprobar la importación 
- Actualizar los dispositivos conectados 
- Autenticar un dispositivo 
- Ver detalles del dispositivo 
- Actualizar un dispositivo individual 
- Eliminar una configuración de dispositivo 
- Cambiar una contraseña de dispositivo expirada
- Reemplazar un dispositivo incorrecto

>[AZURE.NOTE] Para obtener información general sobre el uso de la interfaz de administrador de instantáneas StorSimple, vaya a la [interfaz de usuario de administrador de instantáneas StorSimple](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Agregar o reemplazar un dispositivo

Use el siguiente procedimiento para agregar o reemplazar un dispositivo StorSimple.

#### <a name="to-add-or-replace-a-device"></a>Para agregar o reemplazar un dispositivo

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple.

2. En el panel de **ámbito** , haga clic en el nodo de **dispositivos** y, a continuación, haga clic en **configurar un dispositivo**. Aparece el cuadro de diálogo **configurar un dispositivo** .

    ![Configurar un dispositivo StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 

3. En el cuadro de lista desplegable del **dispositivo** , seleccione la dirección IP del dispositivo o dispositivo virtual. 

4. En el cuadro de texto **contraseña** , escriba la contraseña de administrador de instantáneas StorSimple que ha creado para el dispositivo en el portal de clásico de Azure. Haga clic en **Aceptar**. Administrador de instantáneas StorSimple busca en el dispositivo que ha identificado. 

    - Si el dispositivo está disponible, el Administrador de instantáneas StorSimple agrega una conexión. 

    - Si el dispositivo no está disponible por cualquier motivo, el Administrador de instantáneas StorSimple devuelve un mensaje de error. Haga clic en **Aceptar** para cerrar el mensaje de error y, a continuación, haga clic en **Cancelar** para cerrar el cuadro de diálogo **configurar un dispositivo** .

## <a name="connect-a-device-and-verify-imports"></a>Conecte un dispositivo y comprobar la importación

Use el procedimiento siguiente para conectar un dispositivo StorSimple y compruebe que se importan los grupos existentes de volumen que tienen asociadas las copias de seguridad.

#### <a name="to-connect-a-device-and-verify-imports"></a>Conecte un dispositivo y comprobar la importación

1. Conectar un dispositivo con el Administrador de instantáneas de StorSimple, siga las instrucciones en Agregar o reemplazar un dispositivo. Cuando se conecta a un dispositivo, el Administrador de instantáneas StorSimple responde como sigue:

    - Si el dispositivo no está disponible por cualquier motivo, el Administrador de instantáneas StorSimple devuelve un mensaje de error. 

   - Si el dispositivo está disponible, el Administrador de instantáneas StorSimple agrega una conexión. Cuando seleccione el dispositivo, aparece en el panel de **resultados** y el campo estado indica el dispositivo está **disponible**. Administrador de instantáneas StorSimple importa los grupos de volumen configurados para el dispositivo, siempre que los grupos de volumen tienen asociadas las copias de seguridad. No se importan las directivas de copia de seguridad. Grupos de volumen que no tienen las copias de seguridad asociadas no se importan.

2. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple.

3. Secundario al nodo superior en el panel de **ámbito** y, a continuación, haga clic en **Mostrar importaciones de alternancia**.

    ![Seleccione Alternar importaciones presentación](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 

4. Aparece el cuadro de diálogo **Mostrar importaciones de alternancia** , que muestra el estado de los grupos de volumen importado y realizar copias de seguridad. Haga clic en **Aceptar**. 

Una vez importados correctamente las copias de seguridad y grupos de volumen, puede usar Administrador de instantáneas StorSimple para administrarlos, como ¿administrar grupos de volumen y las copias de seguridad que ha creado y se ha configurado con el Administrador de instantáneas StorSimple. 

## <a name="refresh-connected-devices"></a>Actualizar los dispositivos conectados

Use el siguiente procedimiento para sincronizar los dispositivos conectados de StorSimple con el Administrador de instantáneas StorSimple.

####<a name="to-refresh-connected-devices"></a>Para actualizar los dispositivos conectados

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple.

2. En el panel de **ámbito** , haga clic en **dispositivos**y, a continuación, haga clic en **Actualizar dispositivos**. Esto sincroniza los dispositivos conectados con el Administrador de instantáneas de StorSimple para que pueda ver los grupos de volumen y las copias de seguridad, como las adiciones recientes. 

    ![Actualizar los dispositivos StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)
 
La acción **Actualizar dispositivos** recupera los nuevos grupos de volumen y las copias de seguridad asociados de dispositivos conectados. A diferencia de la acción **Examinar volúmenes** disponible para el nodo de **volúmenes** , **Actualizar dispositivos** no restaurar el registro de copia de seguridad.

## <a name="authenticate-a-device"></a>Autenticar un dispositivo

Utilice el procedimiento siguiente para autenticar un dispositivo StorSimple con el Administrador de instantáneas StorSimple.

#### <a name="to-authenticate-a-device"></a>Para autenticar un dispositivo

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple.

2. En el panel de **ámbito** , haga clic en **dispositivos**.

3. En el panel de **resultados** , haga clic en el nombre del dispositivo y, a continuación, haga clic en **autenticar**.

4. Aparece el cuadro de diálogo de **autenticación** . Escriba la contraseña del dispositivo y, a continuación, haga clic en **Aceptar**.

    ![Cuadro de diálogo de autenticar](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 
 
## <a name="view-device-details"></a>Ver detalles del dispositivo

Use el procedimiento siguiente para ver los detalles de un dispositivo StorSimple y, si es necesario, volver a sincronizar el dispositivo con el Administrador de instantáneas StorSimple.

#### <a name="to-view-and-resynchronize-device-details"></a>Para ver y volver a sincronizar los detalles del dispositivo

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple.

2. En el panel de **ámbito** , haga clic en **dispositivos**.

3. En el panel de **resultados** , haga clic en el nombre del dispositivo y, a continuación, haga clic en **Detalles**. 

4 el cuadro de diálogo de **Detalles del dispositivo** aparece. Este cuadro muestra el nombre, modelo, versión, número de serie, estado, destino iSCSI nombre completo (IQN) última sincronización y fecha. 

   - Haga clic en **Resync** para sincronizar el dispositivo.

   - Haga clic en **Aceptar** o en **Cancelar** para cerrar el cuadro de diálogo.

    ![Detalles del dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 
 
## <a name="refresh-an-individual-device"></a>Actualizar un dispositivo individual

Use el procedimiento siguiente para volver a sincronizar un dispositivo StorSimple individual con el Administrador de instantáneas StorSimple.

#### <a name="to-refresh-a-device"></a>Para actualizar un dispositivo

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple. 

2. En el panel de **ámbito** , haga clic en **dispositivos**. 

3. En el panel de **resultados** , haga clic en el nombre del dispositivo y, a continuación, haga clic en **Actualizar dispositivo**. Esto sincroniza el dispositivo con el Administrador de instantáneas StorSimple. 

## <a name="delete-a-device-configuration"></a>Eliminar una configuración de dispositivo

Use el procedimiento siguiente para eliminar una determinada configuración de dispositivo de StorSimple desde el Administrador de instantáneas StorSimple.

#### <a name="to-delete-a-device-configuration"></a>Para eliminar una configuración de dispositivo

1. Haga clic en el icono del escritorio para iniciar el Administrador de instantánea StorSimple. 

2. En el panel de **ámbito** , haga clic en **dispositivos**. 

3. En el panel de **resultados** , haga clic en el nombre del dispositivo y, a continuación, haga clic en **Eliminar**. 

4. Aparece el siguiente mensaje. Haga clic en **Sí** para eliminar la configuración o haga clic en **No** para cancelar la eliminación.

    ![Eliminar la configuración de dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Cambiar una contraseña de dispositivo expirada

Debe escribir una contraseña para autenticar un dispositivo StorSimple con el Administrador de instantáneas StorSimple. Configurar esta contraseña cuando utiliza la interfaz de Windows PowerShell para configurar el dispositivo. Sin embargo, puede caducar la contraseña. Si esto sucede, puede usar el portal clásico Azure para cambiar la contraseña. A continuación, porque el dispositivo se configuró en el Administrador de instantáneas de StorSimple antes de que la contraseña ha caducado, deben autenticarse de nuevo el dispositivo en el Administrador de instantáneas StorSimple. 

#### <a name="to-change-the-expired-password"></a>Para cambiar la contraseña caducada

1. En el portal de clásico Azure, inicie el servicio de administrador de StorSimple.

2. Haga clic en **dispositivos** > **Configurar** el dispositivo.

3. Desplácese hacia abajo hasta la sección Administrador de instantáneas StorSimple. Escriba una contraseña que sea 14 y 15 caracteres. Asegúrese de que la contraseña contiene una mezcla de mayúsculas, minúsculas, numéricos y caracteres especiales.

4. Vuelva a escribir la contraseña para confirmarla.

5. Haga clic en **Guardar** en la parte inferior de la página.

#### <a name="to-re-authenticate-the-device"></a>Para volver a autenticar el dispositivo

1. Inicie el Administrador de instantánea StorSimple.

2. En el panel de **ámbito** , haga clic en **dispositivos**. Aparece una lista de los dispositivos configurados en el panel de **resultados** . 

3. Seleccione el dispositivo, con el botón secundario y, a continuación, haga clic en **autenticar**.

4. En la ventana **autenticar** , escriba la nueva contraseña. 

5. Seleccione el dispositivo, con el botón secundario y seleccione **el dispositivo de actualización**. Esto sincroniza el dispositivo con el Administrador de instantáneas StorSimple. 

## <a name="replace-a-failed-device"></a>Reemplazar un dispositivo incorrecto

Si un dispositivo StorSimple falla y se reemplaza por un dispositivo de suspensión (migración tras error), siga estos pasos para conectar con el nuevo dispositivo y ver las copias de seguridad asociados.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Para conectarse a un nuevo dispositivo después de la migración

1. Volver a configurar la conexión iSCSI al nuevo dispositivo. Para obtener instrucciones, vaya a "paso 7: montaje, inicialización y formato un volumen" en [el dispositivo de StorSimple local de implementación](storsimple-deployment-walkthrough-u2.md). 

>[AZURE.NOTE] Si el nuevo dispositivo StorSimple tiene la misma dirección IP que el antiguo, es posible que pueda conectarse a la configuración anterior. 

2. Detenga el servicio de administración de Microsoft StorSimple:

    1. Inicie el administrador del servidor.

    2. En el panel de administrador del servidor, en el menú **Herramientas** , seleccione **Servicios**. 

    3. En la ventana **Servicios** , seleccione el **Servicio de administración de Microsoft StorSimple**. 

    4. En el panel derecho, en el **Servicio de administración de StorSimple de Microsoft**, haga clic en **Detener el servicio**. 

3. Quitar la información de configuración relacionada con el dispositivo antiguo: 

    1. En el Explorador de archivos, vaya a C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    2. Elimine los archivos en la carpeta BACatalog. 

4. Reinicie el servicio de administración de Microsoft StorSimple: 

    1. En el panel de administrador del servidor, en el menú **Herramientas** , seleccione **Servicios**. 

    2. En la ventana **Servicios** , seleccione el **Servicio de administración de Microsoft StorSimple**. 

    3. En el panel derecho, en el **Servicio de administración de StorSimple de Microsoft**, haga clic en **reiniciar el servicio**. 

5. Inicie el Administrador de instantánea StorSimple. 

6. Para configurar el nuevo dispositivo StorSimple, complete los pasos en el paso 2: conecte un dispositivo StorSimple en el [Administrador de instantánea StorSimple implementar](storsimple-snapshot-manager-deployment.md). 

7. Haga clic en el nodo de nivel superior en el panel de **ámbito** (StorSimple instantánea administrador en el ejemplo) y, a continuación, haga clic en **Mostrar importaciones de alternancia**. 

8. Aparecerá un mensaje cuando las copias de seguridad y grupos de volumen importado están visibles en el Administrador de instantáneas StorSimple. Haga clic en **Aceptar**. 

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [usar StorSimple instantánea Manager para administrar la solución StorSimple](storsimple-snapshot-manager-admin.md).
- Obtenga información sobre cómo [usar el Administrador de instantáneas StorSimple para ver y administrar volúmenes](storsimple-snapshot-manager-manage-volumes.md).

