<properties 
   pageTitle="Administrar su cuenta de almacenamiento StorSimple | Microsoft Azure"
   description="Se explica cómo puede usar la página Administrador de StorSimple configurar para agregar, editar, eliminar o girar las claves de seguridad para una cuenta de almacenamiento."
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
   ms.date="04/29/2016"
   ms.author="v-sharos" />

# <a name="use-the-storsimple-manager-service-to-manage-your-storage-account"></a>Usar el servicio Administrador de StorSimple para administrar su cuenta de almacenamiento

## <a name="overview"></a>Información general

La página **Configurar** presenta todos los parámetros de servicio global que pueden crearse en el servicio Administrador de StorSimple. Estos parámetros se pueden aplicar a todos los dispositivos conectados al servicio y se incluyen:

- Cuentas de almacenamiento 
- Plantillas de ancho de banda 
- Registros de control de acceso 

En este tutorial se explica cómo puede usar **la página** para agregar, editar o eliminar cuentas de almacenamiento o girar las claves de seguridad para una cuenta de almacenamiento.

 ![Configurar página](./media/storsimple-manage-storage-accounts/HCS_ConfigureService.png)  

Cuentas de almacenamiento contienen las credenciales que el dispositivo se usa para acceder a su cuenta de almacenamiento a su proveedor de servicios de nube. Para las cuentas de Microsoft Azure almacenamiento, son credenciales como el nombre de cuenta y la tecla de acceso principal. 

En la página **Configurar** , se muestran todas las cuentas de almacenamiento que se han creado para la suscripción de facturación en un formato tabular que contiene la información siguiente:

- **Nombre** : el nombre exclusivo asignado a la cuenta cuando se creó.
- **SSL habilitado** : si el SSL está habilitado y comunicación de nube de dispositivo está sobre el canal seguro.
- **Utilizado por** : el número de volúmenes con la cuenta de almacenamiento.

Las tareas más comunes relacionados con las cuentas de almacenamiento que se pueden realizar en la página **Configurar** son:

- Agregar una cuenta de almacenamiento 
- Editar una cuenta de almacenamiento 
- Eliminar una cuenta de almacenamiento 
- Rotación de clave de cuentas de almacenamiento 

## <a name="types-of-storage-accounts"></a>Tipos de cuentas de almacenamiento

Existen tres tipos de cuentas de almacenamiento que se pueden usar con el dispositivo de StorSimple.

- **Cuentas de almacenamiento generado automáticamente** , como el nombre sugiere, este tipo de cuenta de almacenamiento se genera automáticamente cuando se crea el servicio por primera vez. Para obtener más información acerca de cómo se crea esta cuenta de almacenamiento, consulte [paso 1: crear un nuevo servicio](storsimple-deployment-walkthrough-u1.md#step-1-create-a-new-service) en [implementar el dispositivo de StorSimple local](storsimple-deployment-walkthrough.md). 
- **Cuentas de almacenamiento en la suscripción de servicio** : estas son las cuentas de almacenamiento de Azure que están asociadas a la misma suscripción como del servicio. Para obtener más información sobre cómo estas almacenamiento se crean cuentas, consulte [Acerca de cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md). 
- **Cuentas de almacenamiento fuera de la suscripción de servicio** : estas son las cuentas de almacenamiento de Azure que no se han asociado con el servicio y es probable que existía antes de que se creó el servicio.

## <a name="add-a-storage-account"></a>Agregar una cuenta de almacenamiento

Puede agregar una cuenta de almacenamiento al proporcionar un nombre descriptivo único y las credenciales de acceso que estén vinculadas a la cuenta de almacenamiento (con el proveedor de servicios de nube especificada). También tiene la opción de habilitar el modo de sockets seguros (SSL) de la capa para crear un canal seguro para la comunicación de red entre el dispositivo y la nube.

Puede crear varias cuentas para un proveedor de servicios de nube determinado. Tenga en cuenta, sin embargo, después de crea una cuenta de almacenamiento, no puede cambiar el proveedor de servicios de nube.

Mientras se guarda la cuenta de almacenamiento, el servicio intenta comunicarse con el proveedor de servicios de nube. Las credenciales y el material de access que proporcione se puede autenticar en este momento. Solo si la autenticación se realiza correctamente, se crea una cuenta de almacenamiento. Si se produce un error en la autenticación, se mostrará un mensaje de error correspondiente.

Cuentas de almacenamiento de administrador de recursos creadas en el portal de Azure también son compatibles con StorSimple. Las cuentas de almacenamiento de administrador de recursos no aparecerá en la lista desplegable de selección cuando intenta crear un contenedor de volumen, solo las cuentas de almacenamiento creadas en el portal de clásico Azure se mostrarán. Cuentas de almacenamiento de administrador de recursos tendrán que agregarse mediante el procedimiento para agregar una cuenta de almacenamiento que se describen a continuación.

> [AZURE.NOTE] El procedimiento para agregar una cuenta de almacenamiento varía en función de la versión del software de StorSimple que está utilizando. Asegúrese de seguir el procedimiento correcto para su versión de StorSimple.


[AZURE.INCLUDE [add-a-storage-account-update1](../../includes/storsimple-configure-new-storage-account-u1.md)]

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>Editar una cuenta de almacenamiento

Puede editar una cuenta de almacenamiento que utiliza un contenedor de volumen. Si modifica una cuenta de almacenamiento que está en uso actualmente, el único campo disponible para modificar es la tecla de acceso para la cuenta de almacenamiento. Puede proporcionar la nueva tecla de acceso de almacenamiento y guardar la configuración actualizada.

#### <a name="to-edit-a-storage-account"></a>Para editar una cuenta de almacenamiento

1. En la página de inicio de servicio, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en **Configurar**.

2. Haga clic en **Agregar o editar cuentas de almacenamiento**.

3. En el cuadro de diálogo **Agregar/editar cuentas de almacenamiento** :

  1. En la lista desplegable de **Cuentas de almacenamiento**, elija una cuenta existente que desea modificar. Esto también puede incluir las cuentas de almacenamiento que se generaban automáticamente cuando se creó el servicio por primera vez.
  2. Si es necesario, puede modificar la selección de **Activar el modo de SSL** .
  3. Puede girar la teclas de acceso de la cuenta de almacenamiento. Consulte [rotación de clave de cuentas de almacenamiento](#key-rotation-of-storage-accounts) para obtener más información sobre cómo realizar la rotación de clave.
  4. Haga clic en el icono de verificación ![Active icono](./media/storsimple-manage-storage-accounts/HCS_CheckIcon.png) para guardar la configuración. La configuración se actualizará en la página **Configurar** . Haga clic en **Guardar** para guardar la configuración recién actualizada.

     ![Editar una cuenta de almacenamiento](./media/storsimple-manage-storage-accounts/HCs_AddEditStorageAccount.png)
  
## <a name="delete-a-storage-account"></a>Eliminar una cuenta de almacenamiento

> [AZURE.IMPORTANT] Puede eliminar una cuenta de almacenamiento solo si no se usa un contenedor de volumen. Si se utiliza una cuenta de almacenamiento por un contenedor de volumen, elimine primero el contenedor de volumen y, a continuación, elimine la cuenta de almacenamiento asociado.

#### <a name="to-delete-a-storage-account"></a>Para eliminar una cuenta de almacenamiento

1. En la página de inicio del servicio de administrador de StorSimple, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en **Configurar**.

2. En la lista tabular de cuentas de almacenamiento, mantenga el mouse sobre la cuenta que desea eliminar.

3. Aparecerá un icono de eliminar (**x**) en la columna derecha extrema para esa cuenta de almacenamiento. Haga clic en el icono **x** para eliminar las credenciales.

4. Cuando se le solicite confirmación, haga clic en **Sí** para continuar con la eliminación. El listado de la tabla se actualizará para reflejar los cambios.

## <a name="key-rotation-of-storage-accounts"></a>Rotación de clave de cuentas de almacenamiento

Por motivos de seguridad, la rotación de clave a menudo es necesario en los centros de datos. 

> [AZURE.NOTE] La siguiente información de rotación de clave y el procedimiento de rotación que se aplican a solo cuentas de Microsoft Azure almacenamiento. Si usa otro proveedor de servicios de nube, puede administrar las claves de la cuenta de almacenamiento a través de paneles de su proveedor.
 
Cada suscripción de Microsoft Azure puede tener una o varias cuentas de almacenamiento asociado. El acceso a estas cuentas se controla mediante las teclas de acceso y de suscripción para cada cuenta de almacenamiento. 

Cuando se crea una cuenta de almacenamiento, Microsoft Azure genera dos teclas de acceso de almacenamiento de 512 bits que se usan para la autenticación cuando se tiene acceso a la cuenta de almacenamiento. Tener dos teclas de acceso de almacenamiento le permite volver a generar las claves con ninguna interrupción en el servicio de almacenamiento o el acceso a dicho servicio. La clave que está en uso es la clave *principal* y la clave de copia de seguridad se conoce como la clave *secundaria* . Una de estas dos claves debe proporcionarse cuando el dispositivo de Microsoft Azure StorSimple tiene acceso a su proveedor de servicios de almacenamiento de nube.

## <a name="what-is-key-rotation"></a>¿Qué es la rotación de clave?

Normalmente, aplicaciones use solo una de las teclas de acceso a los datos. Después de un período de tiempo, puede hacer que las aplicaciones pasar el uso de la segunda clave. Después de que haya cambiado las aplicaciones a la clave secundaria, puede retirar la primera clave y, a continuación, generar una nueva clave. Usando las dos claves de este modo permite el acceso de aplicaciones a los datos sin que se produzca tiempo de inactividad.

Las teclas de la cuenta de almacenamiento siempre se almacenan en el servicio en forma cifrada. No obstante, estos pueden restablecer a través del servicio de administrador de StorSimple. El servicio puede obtener la clave principal y secundario para todas las cuentas de almacenamiento en la misma suscripción, incluidas cuentas creadas en el servicio de almacenamiento, así como el almacenamiento predeterminado crean cuentas que se genera cuando el servicio de administrador de StorSimple fue la primero. El servicio de administrador de StorSimple siempre obtendrá estas teclas desde el portal de clásico Azure y, a continuación, almacenarlos en forma cifrada.

## <a name="rotation-workflow"></a>Flujo de trabajo de giro

Un administrador de Microsoft Azure puede volver a crear o cambiar la clave principal o secundaria accediendo directamente a la cuenta de almacenamiento (mediante el servicio de Microsoft Azure almacenamiento). El servicio Administrador de StorSimple no ve este cambio automáticamente.

Para informar el servicio de administrador de StorSimple del cambio, necesitará tener acceso al servicio de administrador de StorSimple acceso a la cuenta de almacenamiento y, a continuación, sincronizar la clave principal o secundaria (dependiendo de cuál se cambió). El servicio obtiene la clave más reciente, cifra las teclas y envía la clave de cifrado al dispositivo.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service-azure-only"></a>Para sincronizar las claves para las cuentas de almacenamiento en la misma suscripción como el servicio (solo Azure)

1. En la página **Servicios** , haga clic en la ficha **Configurar** .

2. Haga clic en **Agregar o editar cuentas de almacenamiento**.

3. En el cuadro de diálogo, haga lo siguiente:

  1. Seleccione la cuenta de almacenamiento con la clave que desea sincronizar. Las teclas de la cuenta de almacenamiento están encriptadas cuando se muestran.
  2. En el servicio de administrador de StorSimple, deberá actualizar la clave que se ha modificado previamente en el servicio de almacenamiento de Microsoft Azure. Si la clave de acceso principal cambió (generado), haga clic en **sincronizar la clave principal**. Si ha cambiado la clave secundaria, haga clic en **sincronizar clave secundaria**.

    ![sincronizar las teclas](./media/storsimple-manage-storage-accounts/HCS_KeyRotationStorageAccountSameSubscriptionAsService.png)

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Para sincronizar las claves para las cuentas de almacenamiento fuera de la suscripción de servicio

1. En la página **Servicios** , haga clic en la ficha **Configurar** .

2. Haga clic en **Agregar o editar cuentas de almacenamiento**.

3. En el cuadro de diálogo, haga lo siguiente:

  1. Seleccione la cuenta de almacenamiento con la tecla de acceso que desea actualizar.
  2. Debe actualizar la tecla de acceso de almacenamiento en el servicio Administrador de StorSimple. En este caso, puede ver la tecla de acceso de almacenamiento. Escriba la nueva clave en el cuadro de y **Tecla de acceso de cuenta de almacenamiento**. 
  3. Guarde los cambios. Ahora debe actualizarse la tecla de acceso de la cuenta de almacenamiento.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [seguridad de StorSimple](storsimple-security.md).
- Más información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).
