<properties 
   pageTitle="Web administración de la interfaz de usuario de matriz Virtual de StorSimple | Microsoft Azure"
   description="Describe cómo realizar tareas de administración de dispositivos básicos a través de la interfaz de usuario de web StorSimple Virtual matriz."
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
   ms.date="04/07/2016"
   ms.author="alkohli" />

# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Usar la interfaz de usuario Web para administrar la matriz Virtual de StorSimple

![flujo del proceso de instalación](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Información general

Los tutoriales de este artículo se aplican a la Microsoft Azure StorSimple Virtual matriz (también conocido como el StorSimple local dispositivo virtual) ejecuta la versión de 2016 marzo disponibilidad general (GA). En este artículo se describe algunos de los flujos de trabajo complejos y tareas de administración que pueden realizarse en la matriz Virtual StorSimple. Puede administrar la matriz Virtual StorSimple con el Administrador de StorSimple del servicio (denominados el portal de interfaz de usuario) de la interfaz de usuario y la interfaz de usuario de web local para el dispositivo. En este artículo se centra en las tareas que puede realizar con la interfaz de usuario de web.

En este artículo incluye los siguientes tutoriales:

- Obtener la clave de cifrado de datos de servicio
- Solucionar errores de instalación de interfaz de usuario de web
- Generar un paquete de registro
- Apagar o reiniciar el dispositivo

## <a name="get-the-service-data-encryption-key"></a>Obtener la clave de cifrado de datos de servicio

Una clave de cifrado de datos de servicio se genera cuando se registre el primer dispositivo con el servicio Administrador de StorSimple. Esta clave es necesaria con la clave de registro de servicio para registrar dispositivos adicionales con el servicio Administrador de StorSimple.

Si ha perdido la clave de cifrado de datos de servicio y la necesidad de recuperarlo, realice los siguientes pasos de la interfaz de usuario del dispositivo de web local registrados con el servicio.

#### <a name="to-get-the-service-data-encryption-key"></a>Para obtener la clave de cifrado de datos de servicio

1. Conectarse a la interfaz de usuario de web local. Vaya a **configuración** > **configuración de la nube**.
  

2. En la parte inferior de la página, haga clic en **obtener la clave de cifrado de datos de servicio**. Aparecerá una clave. Copie y guarde esta clave.
    
    ![obtener la clave de cifrado de datos de servicio 1](./media/storsimple-ova-web-ui-admin/image27.png)
   


## <a name="troubleshoot-web-ui-setup-errors"></a>Solucionar errores de instalación de interfaz de usuario de web

En algunos casos al configurar el dispositivo a través de la interfaz de usuario de web local que puede surgir errores. Para diagnosticar y solucionar estos errores, puede ejecutar las pruebas de diagnóstico.

#### <a name="to-run-the-diagnostic-tests"></a>Para ejecutar las pruebas de diagnóstico

1. En la interfaz de usuario de web local, vaya a la **solución de problemas** > **pruebas de diagnóstico**.

    ![ejecute los diagnósticos de 1](./media/storsimple-ova-web-ui-admin/image29.png)

2. En la parte inferior de la página, haga clic en **Ejecutar pruebas de diagnóstico**. Se iniciará pruebas para diagnosticar posibles problemas con la red, el dispositivo, el proxy web, hora o configuración de la nube. Se le notificará que el dispositivo esté ejecutando pruebas.

3. Cuando han terminado las pruebas, se mostrarán los resultados. En el ejemplo siguiente se muestra los resultados de pruebas de diagnóstico. Observe que la configuración de proxy web no se ha configurado en este dispositivo y, por lo tanto, no se ejecutó la prueba de proxy de web. Todas las pruebas de configuración de red, servidor DNS y configuración de hora estaban correctamente.

    ![ejecute los diagnósticos de 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Generar un paquete de registro

Un paquete de registro se compone de todos los registros relevantes que pueden ayudar a Microsoft Support para solucionar los problemas de dispositivo. En esta versión, puede generar un paquete de registro a través de la interfaz de usuario de web local.

#### <a name="to-generate-the-log-package"></a>Para generar el paquete de registro

1. En la interfaz de usuario de web local, vaya a la **solución de problemas** > **registros del sistema**.

    ![Generar registro paquete 1](./media/storsimple-ova-web-ui-admin/image31.png)

2. En la parte inferior de la página, haga clic en **Crear paquete de registro**. Se creará un paquete de los registros del sistema. Esto puede tardar un par de minutos.

    ![generar el paquete de registro 2](./media/storsimple-ova-web-ui-admin/image32.png)

    Se le notificará una vez creado correctamente el paquete y, a continuación, la página se actualizará para indicar la hora y fecha cuando se creó el paquete.

    ![Generar registro paquete 3](./media/storsimple-ova-web-ui-admin/image33.png)

3. Haga clic en **descargar el paquete de registro**. Un paquete zip se descargará en su sistema.

    ![Generar registro paquete 4](./media/storsimple-ova-web-ui-admin/image34.png)

4. Puede descomprimir el paquete de registro descargado y ver los archivos de registro del sistema.

## <a name="shut-down-and-restart-your-device"></a>Cierre y reinicie el dispositivo

Puede apagar o reiniciar el dispositivo virtual mediante la interfaz de usuario de web local. Le recomendamos que antes de reiniciar, tomar los volúmenes o recursos compartidos sin conexión en el host y, a continuación, en el dispositivo. Esto minimizará la posibilidad de daños en los datos. 

#### <a name="to-shut-down-your-virtual-device"></a>Para cerrar el dispositivo virtual

1. En la interfaz de usuario de web local, vaya a **Mantenimiento** > **configuración de energía**.

2. En la parte inferior de la página, haga clic en **Cerrar**.

    ![Cierre de dispositivo 1](./media/storsimple-ova-web-ui-admin/image36.png)

3. Aparecerá una advertencia que indica que un apagado del dispositivo interrumpe cualquier IO que estaban en curso, de un tiempo de inactividad. Haga clic en el icono de verificación ![icono de comprobación](./media/storsimple-ova-web-ui-admin/image3.png).

    ![Advertencia de cierre de dispositivo](./media/storsimple-ova-web-ui-admin/image37.png)

    Se le notificará que se ha iniciado el cierre.

    ![Cierre de dispositivo iniciado](./media/storsimple-ova-web-ui-admin/image38.png)

    El dispositivo se cerrará. Si desea iniciar el dispositivo, debe hacerlo a través del Administrador de Hyper-V.

#### <a name="to-restart-your-virtual-device"></a>Para reiniciar el dispositivo virtual

1. En la interfaz de usuario de web local, vaya a **Mantenimiento** > **configuración de energía**.

2. En la parte inferior de la página, haga clic en **reiniciar**.

    ![reinicio de dispositivo](./media/storsimple-ova-web-ui-admin/image36.png)

3. Aparecerá una advertencia que indica que reiniciar el dispositivo se interrumpen cualquier IOs que estaban en curso, de un tiempo de inactividad. Haga clic en el icono de verificación ![icono de comprobación](./media/storsimple-ova-web-ui-admin/image3.png).

    ![Reinicie advertencia](./media/storsimple-ova-web-ui-admin/image37.png)

    Se le notificará que se ha iniciado el reinicio.

    ![reiniciar iniciado](./media/storsimple-ova-web-ui-admin/image39.png)

    Mientras el reinicio está en curso, se pierde la conexión a la interfaz de usuario. Puede supervisar el reinicio actualizándolos periódicamente la interfaz de usuario. Como alternativa, puede supervisar el estado de reinicio del dispositivo mediante el Administrador de Hyper-V.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo](storsimple-manager-service-administration.md).
