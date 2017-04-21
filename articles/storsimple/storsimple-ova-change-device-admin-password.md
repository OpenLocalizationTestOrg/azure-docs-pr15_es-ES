<properties 
   pageTitle="Cambiar la contraseña de administrador del dispositivo virtual StorSimple | Microsoft Azure"
   description="Describe cómo usar el portal de clásico Azure o la interfaz de usuario de web StorSimple Virtual matriz para cambiar la contraseña de administrador de dispositivos."
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
   ms.date="06/17/2016"
   ms.author="alkohli" />

# <a name="change-the-storsimple-virtual-array-device-administrator-password"></a>Cambiar la contraseña de administrador del dispositivo StorSimple Virtual matriz

## <a name="overview"></a>Información general

Cuando utiliza la interfaz de Windows PowerShell para tener acceso al dispositivo virtual de StorSimple, debe escribir una contraseña de administrador de dispositivos. Cuando el dispositivo StorSimple primero es aprovisionado e iniciado, la contraseña predeterminada es *Contraseña1*. Para la seguridad de los datos, la contraseña predeterminada expira la primera vez que inicie sesión y se le pide que cambie esta contraseña.

También puede usar la interfaz de usuario de web local o en el portal de clásico Azure para cambiar la contraseña de administrador de dispositivos en cualquier momento después de que el dispositivo esté implementado en su entorno de producción. Cada uno de estos procedimientos se describe en este artículo.

## <a name="use-the-azure-classic-portal-to-change-the-password"></a>Usar el portal de clásico Azure para cambiar la contraseña

Realice los pasos siguientes para cambiar la contraseña de administrador de dispositivos a través del portal clásica Azure.

#### <a name="to-change-the-device-administrator-password-via-the-azure-classic-portal"></a>Para cambiar la contraseña de administrador de dispositivos a través del portal de clásico de Azure

1. En el portal, haga clic en **dispositivos** > **configuración** de su dispositivo.

2. Desplácese hacia abajo hasta la sección de la **Contraseña de administrador del dispositivo** . Proporcione una contraseña de administrador que contiene de 8 a 15 caracteres. La contraseña debe ser una combinación de caracteres en mayúsculas, minúsculas, numéricos y especiales.

3. Confirme la contraseña.

4. Haga clic en **Guardar** en la parte inferior de la página.

Ahora debe actualizarse la contraseña de administrador de dispositivos. Puede usar esta contraseña modificada para tener acceso al dispositivo localmente.

## <a name="use-the-storsimple-virtual-array-web-ui-to-change-the-password"></a>Usar la interfaz de usuario de web StorSimple Virtual matriz para cambiar la contraseña

Realice los pasos siguientes para cambiar la contraseña de administrador de dispositivos a través de la interfaz de usuario de web local.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Para cambiar la contraseña de administrador de dispositivos a través de la interfaz de usuario de web local

1. En la interfaz de usuario de web local, haga clic en **Mantenimiento** > **cambiar la contraseña** de su dispositivo.

    ![cambiar Contraseña1](./media/storsimple-ova-change-device-admin-password/image40.png)

2. Escriba la **contraseña actual**.

3. Proporcione una **contraseña nueva**. La contraseña debe tener al menos 8 caracteres. Debe contener 3 de 4 de las siguientes acciones: mayúsculas, minúsculas, numéricos y caracteres especiales.

    Tenga en cuenta que la contraseña no puede ser la misma que la última 24 contraseñas.

3. Vuelva a escribir la contraseña para confirmarla.

    ![cambiar Contraseña2](./media/storsimple-ova-change-device-admin-password/image41.png)

4. En la parte inferior de la página, haga clic en **Aplicar**. A continuación, se aplicará la nueva contraseña. Si el cambio de contraseña no es correcto, verá el siguiente error.

    ![error de contraseña](./media/storsimple-ova-change-device-admin-password/image42.png)

    Después de que la contraseña se actualiza correctamente, se le notificará. A continuación, puede usar esta contraseña modificada acceder localmente al dispositivo.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre la [administración de la matriz Virtual de StorSimple](storsimple-ova-web-ui-admin.md).
