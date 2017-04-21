<properties 
   pageTitle="Implementar el servicio de administrador de StorSimple para StorSimple virtual matriz | Microsoft Azure"
   description="Se explica cómo crear y eliminar el servicio de administrador de StorSimple en el portal de clásico Azure y describe cómo administrar la clave de registro de servicio."
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
   ms.date="05/19/2016"
   ms.author="alkohli" />

# <a name="deploy-the-storsimple-manager-service-for-storsimple-virtual-array"></a>Implementar el servicio de administrador de StorSimple para StorSimple Virtual matriz

## <a name="overview"></a>Información general

El servicio Administrador de StorSimple se ejecuta en Microsoft Azure y se conecta a varios dispositivos de StorSimple. Después de crear el servicio, puede usarlo para administrar los dispositivos desde el portal de Microsoft Azure clásico ejecutando en un explorador. Esto le permite supervisar todos los dispositivos que están conectados al servicio de administrador de StorSimple desde una única ubicación central, reduciendo carga administrativa.

La página de inicio del Administrador de StorSimple enumera todos los servicios de StorSimple administrador que puede usar para administrar los dispositivos de almacenamiento de StorSimple. Para cada servicio de administrador de StorSimple, se presenta la información siguiente en la página Administrador de StorSimple:

- **Nombre** : el nombre que se ha asignado a su servicio de administrador de StorSimple cuando se creó. No se puede cambiar el nombre de servicio después de crea el servicio.

- **Estado** : el estado del servicio, que puede ser **activa**, **crear**o **en línea**.

- **Ubicación** : la ubicación geográfica en la que se van a implementar el dispositivo StorSimple.

- **Suscripción** a la suscripción de facturación asociado con el servicio.

Las tareas comunes que se pueden realizar a través de la página del Administrador de StorSimple son:

- Crear un servicio
- Eliminar un servicio
- Obtener la clave de registro de servicio
- Regenerar la clave de registro de servicio

Este tutorial describe cómo llevar a cabo cada una de estas tareas. La información contenida en este artículo es aplicable únicamente a las matrices Virtual StorSimple. Para obtener más información acerca de la serie 8000 StorSimple, vaya a [implementar un servicio de administrador de StorSimple](storsimple-manage-service.md).

## <a name="create-a-service"></a>Crear un servicio

Use la opción **Crear rápido** para crear un servicio de administrador de StorSimple si desea distribuir el dispositivo StorSimple. Para crear un servicio, debe tener:

- Una suscripción con un contrato Enterprise
- Una cuenta de almacenamiento de Microsoft Azure active
- La información de facturación que se usa para la administración de acceso

También puede generar una cuenta de almacenamiento predeterminada al crear el servicio.

Un único servicio puede administrar varios dispositivos. Sin embargo, un dispositivo no puede abarcar varios servicios. Una gran empresa puede tener varias instancias del servicio para trabajar con varias suscripciones, organizaciones o incluso ubicaciones de implementación.  

> [AZURE.NOTE] Debe separadas instancias del servicio de StorSimple Manager para administrar dispositivos de la serie 8000 StorSimple y StorSimple Virtual matrices.

Realice los pasos siguientes para crear un servicio.

[AZURE.INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

## <a name="delete-a-service"></a>Eliminar un servicio

Antes de eliminar un servicio, asegúrese de que no hay dispositivos conectados están utilizando. Si el servicio está en uso, desactive los dispositivos conectados. La operación de desactivar la conexión entre el dispositivo y el servicio de servidor pero, conservar los datos de dispositivo en la nube. 

> [AZURE.IMPORTANT] Después de elimina un servicio, no se puede deshacer la operación. 

Realice los pasos siguientes para eliminar un servicio.

### <a name="to-delete-a-service"></a>Para eliminar un servicio

1. En la página **Administrador de StorSimple servicio** , seleccione el servicio que desea eliminar.

1. Haga clic en **Eliminar** en la parte inferior de la página.

1. En la notificación de confirmación, haga clic en **Sí** . Puede tardar unos minutos para el servicio que desea eliminar.

## <a name="get-the-service-registration-key"></a>Obtener la clave de registro de servicio

Después de haber creado correctamente un servicio, deberá registrar su dispositivo StorSimple con el servicio. Para registrar el primer dispositivo StorSimple, necesitará la clave de registro de servicio. Para registrar dispositivos adicionales con un servicio de StorSimple existente, necesitará la clave de registro y la clave de cifrado de datos de servicio (que se genera en el primer dispositivo durante el registro). Para obtener más información acerca de la clave de cifrado de datos de servicio, vea [obtener la clave de cifrado de datos de servicio de la interfaz de usuario de web local](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key). 

Realice los pasos siguientes para obtener la clave de registro de servicio.

[AZURE.INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

Mantener la clave de registro de servicio en una ubicación segura. Necesitará esta clave, así como la clave de cifrado de datos de servicio, registrar dispositivos adicionales con este servicio. Después de obtener la clave de registro de servicio, debe configurar el dispositivo a través de Windows PowerShell para la interfaz de StorSimple.

## <a name="regenerate-the-service-registration-key"></a>Regenerar la clave de registro de servicio

Debe volver a crear una clave de registro de servicio si es necesario para realizar la rotación de clave o si ha cambiado la lista de administradores de servicio. Al regenerar la clave, la clave nueva se usa solamente para registrar los siguientes dispositivos. Los dispositivos que ya se han registrado se ven afectados por este proceso.

Realice los pasos siguientes para volver a generar una clave de registro de servicio.

### <a name="to-regenerate-the-service-registration-key"></a>Para regenerar la clave de registro de servicio

1. En la página **servicio StorSimple administrador** , haga clic en **Clave de registro**.

1. En el cuadro de diálogo **Clave de registro de servicio** , haga clic en **regenerar**.

1. Verá un mensaje de confirmación. Haga clic en **Aceptar** para continuar con la regeneración.

1. Aparecerá una nueva clave de registro de servicio.

1. Copie esta clave y guardarla para registrar nuevos dispositivos con este servicio.

1. Haga clic en el icono de verificación ![Icono de comprobación](./media/storsimple-ova-manage-service/image7.png) para cerrar este cuadro de diálogo.


## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [empezar](storsimple-ova-deploy1-portal-prep.md) con una matriz virtual de StorSimple.
    
- Obtenga información sobre cómo [administrar el dispositivo de StorSimple](storsimple-ova-web-ui-admin.md).

 
