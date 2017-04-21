<properties 
   pageTitle="Póngase en contacto con soporte técnico de Microsoft | Microsoft Azure"
   description="Obtenga información sobre cómo crear una solicitud de soporte técnico e iniciar una sesión de asistencia en el dispositivo StorSimple."
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="contact-microsoft-support"></a>Póngase en contacto con soporte técnico de Microsoft

Si tiene problemas con la solución de Microsoft Azure StorSimple, puede crear una solicitud de servicio de soporte técnico. En una sesión en línea con el soporte técnico de ingeniería, también tendrá que iniciar una sesión de asistencia en el dispositivo StorSimple. En este artículo le guiará a través de:

- Cómo crear una solicitud de soporte.
- Cómo iniciar una sesión de soporte técnico de la interfaz de Windows PowerShell del dispositivo StorSimple.

Revise el [SLA de soporte técnico de StorSimple 8000 Series e información](https://msdn.microsoft.com/library/mt433077.aspx) antes de crear una solicitud de soporte.

## <a name="create-a-support-request"></a>Crear una solicitud de soporte técnico

Realice los pasos siguientes para crear una solicitud de soporte:

#### <a name="to-create-a-support-request"></a>Para crear una solicitud de soporte técnico

1. En el [portal de clásica Azure](https://manage.windowsazure.com/), en la esquina superior derecha, haga clic en el nombre de la cuenta y, a continuación, haga clic en **Soporte técnico de Microsoft del contacto**.

    ![Soporte de MS contacto a través de ManagementPortal](./media/storsimple-contact-microsoft-support/Ibiza1.png)

2. Se le redirigirá al nuevo portal de Azure (portal.azure.com). Haga clic en el mosaico de **solicitud de soporte técnico de nuevo** .

    ![Soporte de MS de contacto a través del portal de nuevo](./media/storsimple-contact-microsoft-support/Ibiza2.png)

    En el lado derecho de la pantalla, aparece el panel de **solicitud de soporte técnico de nuevo** . 

    ![Nuevo panel de solicitud de soporte técnico](./media/storsimple-contact-microsoft-support/Ibiza3a.png)

3. En el cuadro de diálogo **conceptos básicos** , haga lo siguiente:                                
    1. En la lista desplegable **tipo de problema** , seleccione **técnicos**.
    2. Seleccione una **suscripción** de la lista desplegable.
    3. En la lista desplegable de **servicio** , seleccione **StorSimple**. 
    4. Seleccione un **plan de soporte técnico** de la lista desplegable. Necesita un plan de soporte técnico de pago para habilitar el soporte técnico.

4. Haga clic en **siguiente**. Aparece el cuadro de diálogo de **problema** .

    ![Nuevo panel de solicitud de soporte técnico](./media/storsimple-contact-microsoft-support/Ibiza5a.png) 

5. En el cuadro de diálogo del **problema** , haga lo siguiente:

    1.  Seleccione un nivel de **gravedad** de la lista desplegable.
    2.  Seleccione un **tipo de problema** de la lista desplegable.
    3.  Seleccione una **categoría** de la lista desplegable. 
    4.  En el cuadro **Detalles** , describa brevemente el problema.
    5.  En el cuadro **intervalo de tiempo** , indique la fecha, hora y zona horaria que corresponde a la instancia más reciente de su problema.
    6.  En la **carga de archivos**, haga clic en el icono de carpeta para buscar el paquete de compatibilidad.
    7.  Active la casilla de verificación de **compartir información de diagnóstico** .

6. Haga clic en **siguiente**. Aparece el cuadro de diálogo **información de contacto** .

    ![Nuevo panel de solicitud de soporte técnico](./media/storsimple-contact-microsoft-support/Ibiza6a.png) 

7. Escriba la información de contacto y seleccione un método de contacto (teléfono o correo electrónico). 

8. Active la casilla de verificación **Guardar los cambios de contacto para las solicitudes de soporte futuro** .

9. Haga clic en **crear**.

Después de que ha enviado la solicitud, un técnico de soporte técnico contactará tan pronto como sea posible para continuar con su solicitud.

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Iniciar una sesión de soporte técnico de Windows PowerShell para StorSimple

Para solucionar los problemas que surjan con el dispositivo StorSimple, debe comunicarse con el equipo de Microsoft Support. Microsoft Support que necesite utilizar una sesión de soporte técnico para iniciar sesión en el dispositivo. 

Siga estos pasos para iniciar una sesión de soporte técnico:

#### <a name="to-start-a-support-session"></a>Para iniciar una sesión de soporte técnico

1. El dispositivo de acceso directamente mediante la consola serie o a través de una sesión de telnet desde un equipo remoto. Para ello, siga los pasos de [PuTTY usar para conectarse a la consola de serie de dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. En la sesión que se abre, presione la tecla **ENTRAR** para obtener un símbolo del sistema.

3. En el menú de consola serie, seleccione la opción 1, **inicie sesión con acceso completo**.

4. Cuando se le solicite, escriba la contraseña siguiente: 

    `Password1`

5. Cuando se le solicite, escriba el siguiente comando:

    `Enable-HcsSupportAccess`

6. Se presentará una cadena cifrada a usted. Copie esta cadena en un editor de texto como el Bloc de notas.

7. Guardar esta cadena y enviar un mensaje de correo electrónico a Microsoft Support. 

> [AZURE.IMPORTANT] Puede deshabilitar el acceso de soporte ejecutando `Disable-HcsSupportAccess`. El dispositivo StorSimple también intentar deshabilitar el acceso de soporte 8 horas después de la sesión iniciada. Es recomendable cambiar sus credenciales de dispositivo StorSimple después de iniciar una sesión de asistencia.
