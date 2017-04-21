<properties 
   pageTitle="Administrar su cuenta de almacenamiento StorSimple | Microsoft Azure"
   description="Se explica cómo puede usar la página Administrador de StorSimple configurar para agregar, editar, eliminar o girar las claves de seguridad de una cuenta de almacenamiento asociada con la matriz Virtual StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/29/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-storage-accounts-for-storsimple-virtual-array"></a>Usar el servicio Administrador de StorSimple para administrar cuentas de almacenamiento de matriz Virtual de StorSimple

## <a name="overview"></a>Información general

La página **Configurar** presenta los parámetros del servicio global que pueden crearse en el servicio Administrador de StorSimple. Estos parámetros se pueden aplicar a todos los dispositivos conectados al servicio y se incluyen:

- Cuentas de almacenamiento 
- Registros de control de acceso 

En este tutorial se explica cómo puede usar **la página** para agregar, editar o eliminar cuentas de almacenamiento de la matriz Virtual de StorSimple. La información en este tutorial se aplica solo a la matriz Virtual StorSimple ejecutando el software de la versión de marzo de 2016 GA.

 ![Configurar página](./media/storsimple-ova-manage-storage-accounts/configure_service_page.png)  

Cuentas de almacenamiento contienen las credenciales que el dispositivo se usa para acceder a su cuenta de almacenamiento a su proveedor de servicios de nube. Para las cuentas de Microsoft Azure almacenamiento, son credenciales como el nombre de cuenta y la tecla de acceso principal. 

En la página **Configurar** , se muestran todas las cuentas de almacenamiento que se han creado para la suscripción de facturación en un formato tabular que contiene la información siguiente:

- **Nombre** : el nombre exclusivo asignado a la cuenta cuando se creó.
- **SSL habilitado** : si el SSL está habilitado y comunicación de nube de dispositivo está sobre el canal seguro.

Las tareas más comunes relacionados con las cuentas de almacenamiento que se pueden realizar en la página **Configurar** son:

- Agregar una cuenta de almacenamiento 
- Editar una cuenta de almacenamiento 
- Eliminar una cuenta de almacenamiento 


## <a name="types-of-storage-accounts"></a>Tipos de cuentas de almacenamiento

Existen tres tipos de cuentas de almacenamiento que se pueden usar con el dispositivo de StorSimple.

- **Cuentas de almacenamiento generado automáticamente** , como el nombre sugiere, este tipo de cuenta de almacenamiento se genera automáticamente cuando se crea el servicio por primera vez. Para obtener más información acerca de cómo se crea esta cuenta de almacenamiento, consulte [crear un nuevo servicio](storsimple-ova-manage-service.md#create-a-service). 
- **Cuentas de almacenamiento en la suscripción de servicio** : estas son las cuentas de almacenamiento de Azure que están asociadas a la misma suscripción como del servicio. Para obtener más información sobre cómo estas almacenamiento se crean cuentas, consulte [Acerca de cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md). 
- **Cuentas de almacenamiento fuera de la suscripción de servicio** : estas son las cuentas de almacenamiento de Azure que no se han asociado con el servicio y es probable que existía antes de que se creó el servicio.

Cada matriz Virtual de StorSimple crea un contenedor (con un hcs prefijo) en la cuenta de almacenamiento asociado. Este contenedor tiene todos los datos de nube de su dispositivo. No elimine este contenedor mediante el acceso a través del servicio de almacenamiento de Azure que se producirá esta acción de pérdida de datos.

## <a name="add-a-storage-account"></a>Agregar una cuenta de almacenamiento

Puede agregar una cuenta de almacenamiento a su configuración de servicio del Administrador de StorSimple al proporcionar un nombre descriptivo único y las credenciales de acceso que estén vinculadas a la cuenta de almacenamiento. También tiene la opción de habilitar el modo de sockets seguros (SSL) de la capa para crear un canal seguro para la comunicación de red entre el dispositivo y la nube.

Puede crear varias cuentas para un proveedor de servicios de nube determinado. Mientras se guarda la cuenta de almacenamiento, el servicio intenta comunicarse con el proveedor de servicios de nube. Las credenciales y el material de access que proporcione se puede autenticar en este momento. Solo si la autenticación se realiza correctamente, se crea una cuenta de almacenamiento. Si se produce un error en la autenticación, se mostrará un mensaje de error correspondiente.

Cuentas de almacenamiento de administrador de recursos creadas en el portal de Azure también son compatibles con StorSimple. No se muestran las cuentas de almacenamiento de administrador de recursos en la lista desplegable de selección, solo el almacenamiento se mostrarán las cuentas creadas en el portal de clásico de Azure. Cuentas de almacenamiento de administrador de recursos tendrán que agregarse mediante el procedimiento para agregar una cuenta de almacenamiento, como se describe a continuación.

El procedimiento para agregar una cuenta de Azure almacenamiento clásica se detalla a continuación.

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-ova-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>Editar una cuenta de almacenamiento

Puede editar una cuenta de almacenamiento utilizada por el dispositivo. Si modifica una cuenta de almacenamiento que está en uso actualmente, los campos disponibles para modificar son la tecla de acceso y el modo SSL para la cuenta de almacenamiento. Puede proporcionar la nueva tecla de acceso de almacenamiento o modificar la selección del **modo de habilitar SSL** y guardar la configuración actualizada.

#### <a name="to-edit-a-storage-account"></a>Para editar una cuenta de almacenamiento

1. En la página de inicio de servicio, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en **Configurar**.

2. Haga clic en **Agregar o editar cuentas de almacenamiento**.

3. En el cuadro de diálogo **Agregar/editar cuentas de almacenamiento** :

  1. En la lista desplegable de **Cuentas de almacenamiento**, elija una cuenta existente que desea modificar. 
  2. Si es necesario, puede modificar la selección de **Activar el modo de SSL** .
  3. Puede volver a generar las claves de acceso de cuenta de almacenamiento. Para obtener más información, vea [volver a crear las claves de la cuenta de almacenamiento](storage-create-storage-account.md#manage-your-storage-access-keys). Proporcionar la nueva clave de cuenta de almacenamiento. Para una cuenta de almacenamiento de Azure, esta es la clave de acceso principal. 
  4. Haga clic en el icono de verificación ![Active icono](./media/storsimple-ova-manage-storage-accounts/checkicon.png) para guardar la configuración. La configuración se actualizará en la página **Configurar** . 
  5. En la parte inferior de la página, haga clic en **Guardar** para guardar la configuración recién actualizada. 

     ![Editar una cuenta de almacenamiento](./media/storsimple-ova-manage-storage-accounts/modifyexistingstorageaccount.png)
  
## <a name="delete-a-storage-account"></a>Eliminar una cuenta de almacenamiento

> [AZURE.IMPORTANT] Puede eliminar una cuenta de almacenamiento solo si no está en uso. Si está utilizando una cuenta de almacenamiento, se le notificará.

#### <a name="to-delete-a-storage-account"></a>Para eliminar una cuenta de almacenamiento

1. En la página de inicio del servicio de administrador de StorSimple, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en **Configurar**.

2. En la lista tabular de cuentas de almacenamiento, mantenga el mouse sobre la cuenta que desea eliminar.

3. Aparecerá un icono de eliminar (**x**) en la columna derecha extrema para esa cuenta de almacenamiento. Haga clic en el icono **x** para eliminar las credenciales.

4. Cuando se le solicite confirmación, haga clic en **Sí** para continuar con la eliminación. El listado de la tabla se actualizará para reflejar los cambios.

5. En la parte inferior de la página, haga clic en **Guardar** para guardar la configuración recién actualizada.


## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [Administrar la matriz Virtual de StorSimple](storsimple-ova-web-ui-admin.md).
