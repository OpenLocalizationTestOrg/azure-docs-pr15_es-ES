<properties 
   pageTitle="Administrar los registros de control de acceso de la matriz Virtual StorSimple | Microsoft Azure"
   description="Describe cómo administrar los registros de control de acceso (ACRs) para determinar qué hosts pueden conectarse a un volumen en la matriz Virtual StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/03/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-access-control-records-for-the-storsimple-virtual-array"></a>Usar el servicio de StorSimple Manager para administrar los registros de control de acceso de la matriz Virtual StorSimple 

## <a name="overview"></a>Información general

Registros de control de acceso (ACRs) le permiten especificar qué hosts pueden conectarse a un volumen en la matriz Virtual de StorSimple (también conocido como el StorSimple local dispositivo virtual). ACRs están en un volumen específico y contienen los nombres completos de iSCSI (IQNs) de los hosts. Cuando un host intenta conectarse a un volumen, el dispositivo comprueba la ACR asociado a ese volumen para el nombre IQN y, si hay una coincidencia, se establece la conexión. La sección **registros de control de acceso** en la página **Configurar** muestra todos los registros con los IQNs correspondientes de los hosts de control de acceso.

En este tutorial se explica las siguientes tareas relacionadas con el ACR comunes:

- Obtener el IQN
- Agregar un registro de control de acceso 
- Editar un registro de control de acceso 
- Eliminar un registro de control de acceso 

> [AZURE.IMPORTANT] 
> 
> - Cuando se asigna una ACR a un volumen, procure que el volumen está simultáneamente el acceso a más de un host no agrupado porque esto podría dañar el volumen. 
> - Al eliminar un ACR desde un volumen, asegúrese de que el host correspondiente no tiene acceso el volumen porque la eliminación podría provocar una interrupción de lectura y escritura.

## <a name="get-the-iqn"></a>Obtener el IQN

Realice los pasos siguientes para obtener el IQN de un host de Windows que se está ejecutando Windows Server 2012.

[AZURE.INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Agregar un ACR

Utilice la página de **configuración** del servicio de StorSimple administrador para agregar ACRs. Normalmente, asociará uno ACR con un volumen.

Para obtener información acerca de cómo asociar un ACR con un volumen, vaya a [Agregar un volumen](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).

>[AZURE.IMPORTANT] 
> 
>Cuando se asigna una ACR a un volumen, procure que el volumen está simultáneamente el acceso a más de un host no agrupado porque esto podría dañar el volumen.
 
Siga estos pasos para agregar un ACR.

#### <a name="to-add-an-acr"></a>Para agregar un ACR

1. En la página de inicio de servicio, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en la pestaña **configuración** .

    ![ficha Configuración](./media/storsimple-ova-manage-acrs/acr1.png)

2. En la lista tabular en **registros de control de acceso**, proporcione un **nombre** para el ACR.

3. En **nombre del iniciador iSCSI**, proporcione el nombre IQN de su host de Windows. 

4. Haga clic en **Guardar** en la parte inferior de la página para guardar el ACR recién creado. Verá el siguiente mensaje de confirmación.

    ![mensaje de confirmación](./media/storsimple-ova-manage-acrs/acr2.png)

5. Haga clic en el icono de verificación ![icono de comprobación](./media/storsimple-ova-manage-acrs/check-icon.png). El listado de la tabla se actualizará para reflejar esta adición.

## <a name="edit-an-acr"></a>Editar un ACR

Use la página de **configuración** en el portal de clásico Azure editar ACRs. 

> [AZURE.NOTE] Debe modificar solamente esos ACRs que no están actualmente en uso. Para editar un ACR asociada a un volumen que está en uso, debe primero tomar el volumen sin conexión.

Siga estos pasos para editar un ACR.

#### <a name="to-edit-an-acr"></a>Para editar un ACR

1. En la página de inicio de servicio, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en la pestaña **configuración** .

2. En la lista tabular de los registros de control de acceso, desplace el puntero sobre el ACR que desea modificar.

3. Proporcione un nombre nuevo o IQN de la ACR.

4. Haga clic en **Guardar** en la parte inferior de la página para guardar el ACR modificada. Verá un mensaje de confirmación. 

5. Haga clic en el icono de verificación ![icono de comprobación](./media/storsimple-ova-manage-acrs/check-icon.png). El listado de la tabla se actualizará para reflejar este cambio.

## <a name="delete-an-access-control-record"></a>Eliminar un registro de control de acceso

Use la página de **configuración** en el portal de clásico Azure para eliminar ACRs. 

> [AZURE.NOTE] 
> 
> - Debe eliminar solo esos ACRs que no están actualmente en uso. Para eliminar un ACR asociada a un volumen que está en uso, debe primero tomar el volumen sin conexión.
> - Al eliminar un ACR desde un volumen, asegúrese de que el host correspondiente no tiene acceso el volumen porque la eliminación podría provocar una interrupción de lectura y escritura.

Realice los pasos siguientes para eliminar un registro de control de acceso.

#### <a name="to-delete-an-access-control-record"></a>Para eliminar un registro de control de acceso

1. En la página de inicio de servicio, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en la pestaña **configuración** .

2. En la lista tabular de los registros de control de acceso (ACRs), desplace el puntero sobre el ACR que desea eliminar.

3. Un icono de eliminar (**x**) aparecerá en la columna derecha extrema para el ACR que seleccione. Haga clic en el icono **x** para eliminar el ACR. Verá el siguiente mensaje de confirmación.

    ![mensaje de confirmación](./media/storsimple-ova-manage-acrs/acr3.png)

5. Haga clic en el icono de verificación ![icono de comprobación](./media/storsimple-ova-manage-acrs/check-icon.png). El listado de la tabla se actualizará para reflejar la eliminación.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre cómo [Agregar volúmenes y configurar ACRs](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).
