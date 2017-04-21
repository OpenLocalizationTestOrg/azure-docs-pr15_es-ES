<properties 
   pageTitle="PowerShell para la administración de dispositivos StorSimple | Microsoft Azure"
   description="Aprenda a usar Windows PowerShell para StorSimple para administrar el dispositivo de StorSimple."
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
   ms.date="08/18/2016"
   ms.author="alkohli@microsoft.com" />

# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Usar Windows PowerShell para StorSimple para administrar el dispositivo

## <a name="overview"></a>Información general

Windows PowerShell para StorSimple ofrece una interfaz de línea de comandos que puede usar para administrar el dispositivo de Microsoft Azure StorSimple. Como el nombre sugiere, es una interfaz de línea de comandos basado en Windows PowerShell, que está integrada en un espacio de ejecución restringida. Desde la perspectiva del usuario en la línea de comandos, un espacio de ejecución restringida aparece como una versión de Windows PowerShell restringida. Mientras mantiene algunas de las características básicas de Windows PowerShell, esta interfaz tiene cmdlets dedicado adicionales que están dirigidos a administrar el dispositivo de Microsoft Azure StorSimple. 

Este artículo describe la Windows PowerShell para características de StorSimple, incluido cómo puede conectarse a esta interfaz y contiene vínculos a procedimientos paso a paso o flujos de trabajo que puede realizar con esta interfaz. Los flujos de trabajo incluyen cómo registrar su dispositivo, configure la interfaz de red en el dispositivo, instalar las actualizaciones que requieren el dispositivo esté en modo de mantenimiento, cambiar el estado del dispositivo y solucionar problemas que puede experimentar.

Después de leer este artículo, podrá:

- Conectarse a su dispositivo StorSimple con Windows PowerShell para StorSimple.

- Administrar el dispositivo StorSimple con Windows PowerShell para StorSimple.

- Obtén ayuda de Windows PowerShell para StorSimple.

>[AZURE.NOTE]   

>- Windows PowerShell para los cmdlets StorSimple le permiten administrar el dispositivo de StorSimple desde una consola serie o remota a través de Windows PowerShell remoto. Para obtener más información acerca de cada uno de los cmdlets individuales que puede usarse en esta interfaz, vaya a [referencia de cmdlet de Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

>- Los cmdlets de Azure PowerShell StorSimple son un conjunto diferente de los cmdlets que permiten automatizar StorSimple nivel de servicio y las tareas de migración de la línea de comandos. Para obtener más información acerca de los cmdlets de PowerShell de Azure para StorSimple, vaya a la [referencia de los cmdlets StorSimple de Azure](https://msdn.microsoft.com/library/azure/dn920427.aspx).

Puede tener acceso a Windows PowerShell para StorSimple usando uno de los métodos siguientes:

- [Conectarse a la consola de serie de dispositivo StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
- [Conectarse remotamente a StorSimple con Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)
    

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Conectar con Windows PowerShell para StorSimple a través de la consola de serie de dispositivo

Puede [Descargar PuTTY](http://www.putty.org/) o terminal similar software de emulación para conectar con Windows PowerShell para StorSimple. Necesita configurar PuTTY específicamente para tener acceso al dispositivo de Microsoft Azure StorSimple. Los temas siguientes contienen los pasos detallados acerca de cómo configurar PuTTy y conectar el dispositivo. También se explican las diversas opciones de menú de la consola de serie.

### <a name="putty-settings"></a>Configuración masilla

Asegúrese de que usa la siguiente configuración masilla para conectarse a la interfaz de Windows PowerShell desde la consola de serie.

#### <a name="to-configure-putty"></a>Para configurar PuTTY

1. En el cuadro de diálogo masilla **nueva configuración** , en el panel **categorías** , seleccione **teclado**.

2. Asegúrese de que están seleccionadas las opciones siguientes (estos son la configuración predeterminada al iniciar una nueva sesión). 

  	|Elemento de teclado|Seleccione|
  	|---|---|
  	|Tecla retroceso|¿Control-? (127)|
  	|Teclas de inicio y finalización|Estándar|
  	|Teclado y teclas de función|ESC [n ~|
  	|Estado inicial de las teclas de cursor|Normal|
  	|Estado inicial del teclado numérico|Normal|
  	|Habilitar las características de teclado adicionales|Control Alt es diferente de AltGr|

    ![Configuración de masilla compatibles](./media/storsimple-windows-powershell-administration/IC740877.png)

3. Haga clic en **Aplicar**.

4. En el panel **categorías** , seleccione **traducción**.

5. En el cuadro de lista **juego de caracteres remota** , seleccione **UTF-8**.

6. En el **tratamiento de caracteres de dibujo de línea**, seleccione **usar dibujo de línea de puntos de código**. La ilustración siguiente muestra las selecciones masilla correctas.

    ![UTF Putty configuración](./media/storsimple-windows-powershell-administration/IC740878.png)

7. Haga clic en **Aplicar**.


Ahora puede usar PuTTY para conectarse a la consola de serie de dispositivo realizando los siguientes pasos.

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]


### <a name="about-the-serial-console"></a>Acerca de la consola de serie

Cuando se tiene acceso a la interfaz del dispositivo StorSimple a través de la consola de serie, un mensaje de pancarta se presenta de Windows PowerShell, seguido de las opciones de menú. 

El mensaje pancarta contiene información básica de dispositivo de StorSimple como modelo, el nombre, la versión del software instalado y el estado del controlador que se tiene acceso. La imagen siguiente muestra un ejemplo de un mensaje de titular.

![Mensaje de pancarta serie](./media/storsimple-windows-powershell-administration/IC741098.png)

>[AZURE.IMPORTANT] Puede usar el mensaje pancarta para identificar si el controlador a que está conectado está activo o pasivo.

La imagen siguiente muestra las distintas opciones de espacio que están disponibles en el menú de consola serie.

![Registrar el dispositivo 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Puede elegir entre las siguientes opciones:

1. **Inicie sesión con acceso completo** Esta opción le permite conectarse (con las credenciales correctas) para el espacio de ejecución de **SSAdminConsole** en el controlador local. (El controlador local es el controlador que tiene actualmente acceso a través de la consola de serie de su dispositivo StorSimple). Esta opción también puede utilizarse para permitir que Microsoft Support tener acceso sin restricciones espacio (una sesión de soporte técnico) para solucionar problemas de dispositivo posibles. Después de usar la opción 1 para iniciar sesión, puede permitir que el técnico de Microsoft Support tener acceso sin restricciones espacio ejecutando un cmdlet específico. Para obtener más información, consulte para [iniciar una sesión de soporte técnico](storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).

2. **Inicie sesión en el controlador de punto con acceso completo** Esta opción es idéntica a la opción 1, excepto en que puede conectarse (con las credenciales correctas) para el espacio de ejecución de **SSAdminConsole** en el controlador del mismo nivel. Dado que el dispositivo de StorSimple es un dispositivo de alta disponibilidad con dos controladores en una configuración de activo y pasivo, punto hace referencia a otro controlador en el dispositivo que tiene acceso a través de la consola serie).
Similar a la opción 1, esta opción también sirve para permitir que Microsoft Support tener acceso sin restricciones de espacio en un controlador de punto.

3. **Conectar con acceso limitado** Esta opción se utiliza para acceder a la interfaz de Windows PowerShell en modo limitado. No se le solicita las credenciales de acceso. Esta opción se conecta a un espacio de ejecución más restringido en comparación con las opciones 1 y 2.  Algunas de las tareas que están disponibles a través de la opción 1 que **no se* puede realizar en este espacio son:

    - Restablecer la configuración de fábrica
    - Cambiar la contraseña
    - Habilitar o deshabilitar el acceso de soporte
    - Aplicar actualizaciones
    - Instalar revisiones 
                                                

    >[AZURE.NOTE] **Esta es la opción preferida si ha olvidado la contraseña de administrador del dispositivo y no se puede conectar a través de la opción 1 o 2.**

4. **Cambiar de idioma** Esta opción permite cambiar el idioma de visualización en la interfaz de Windows PowerShell. Los idiomas compatibles son inglés, japonés, ruso, francés, coreano sur, español, italiano, alemán, chino y portugués brasileño.


## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Conectarse remotamente a StorSimple con Windows PowerShell para StorSimple

Puede usar Windows PowerShell remoto para conectarse a su dispositivo StorSimple. Cuando se conecte de esta manera, no verá un menú. (Verá un menú únicamente si usa la consola de serie en el dispositivo para conectarse. Conexión de forma remota le lleva directamente en el equivalente de "opción 1: acceso completo" en la consola serie.) Con Windows PowerShell remoto, debe conectarse a un espacio de ejecución específica. También puede especificar el idioma para mostrar. 

El idioma de visualización es independiente del idioma que ha configurado con la opción de **Cambiar de idioma** en el menú de consola serie. PowerShell remoto seleccionará automáticamente la configuración regional del dispositivo que se conecta desde si no se especifica.

>[AZURE.NOTE] Si está trabajando con hosts virtuales de Microsoft Azure y StorSimple virtuales dispositivos, puede usar Windows PowerShell remoto y del host virtual para conectar con el dispositivo virtual. Si ha configurado una ubicación compartida en el host en el que desea guardar la información de la sesión de Windows PowerShell, debe tener en cuenta que el principal todos incluye solo los usuarios autenticados. Por lo tanto, si ha configurado el recurso compartido para permitir el acceso a todos los usuarios y conectar sin especificar credenciales, se usará el principal no autenticado anónimo y se muestra un error. Para solucionar este problema, en la opción compartir host se debe habilitar la cuenta de invitado y asígnele al invitado acceso completo a la cuenta para el recurso compartido o debe especificar credenciales válidas junto con el cmdlet de Windows PowerShell.

Puede usar HTTP o HTTPS para conectarse a través de Windows PowerShell remoto. Utilice las instrucciones en los siguientes tutoriales:

- [Conectar utilizando HTTP de forma remota](storsimple-remote-connect.md#connect-through-http)
- [Conectar utilizando HTTPS de forma remota](storsimple-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Consideraciones de seguridad de conexión

Cuando decida cómo conectarse a Windows PowerShell para StorSimple, considere lo siguiente:

- Conectar directamente a la consola de serie de dispositivo es seguro, pero no conectarse a la consola serie a través de los modificadores de red está. Tenga cuidado de los riesgos de seguridad al conectarse al dispositivo serie a través de modificadores de la red.

- Conectar a través de una sesión HTTP puede ofrecer más seguridad que conectarse a través de la consola serie a través de la red. Aunque no es el método más seguro, es aceptable en redes de confianza.

- Conectar a través de una sesión HTTPS es más segura y la opción recomendada.


## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administrar el dispositivo StorSimple con Windows PowerShell para StorSimple
La siguiente tabla muestra un resumen de todas las tareas comunes de administración y flujos de trabajo complejos que pueden realizarse dentro de la interfaz de Windows PowerShell del dispositivo StorSimple. Para obtener más información acerca de cada flujo de trabajo, haga clic en la entrada correspondiente en la tabla.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell para flujos de trabajo de StorSimple

|Si desea hacer esto...|Use este procedimiento.|
|---|---|
|Registrar su dispositivo|[Configurar y registrar el dispositivo con Windows PowerShell para StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
|Configurar a proxy web</br>Ver la configuración de proxy de web|[Configurar a proxy de web de su dispositivo StorSimple](storsimple-configure-web-proxy.md)|
|Modificar la configuración de interfaz de red 0 de datos en el dispositivo|[Modificar la interfaz de red 0 de datos de su dispositivo StorSimple](storsimple-modify-data-0.md)|
|Dejar un controlador </br> Reiniciar o apagar un controlador </br> Cerrar un dispositivo</br>Restablecer el dispositivo a la configuración predeterminada de fábrica|[Administrar los controladores de dispositivo](storsimple-manage-device-controller.md)|
|Instalar revisiones y actualizaciones de modo de mantenimiento|[Actualizar el dispositivo](storsimple-update-device.md)|
|Especificar el modo de mantenimiento </br>Salir del modo de mantenimiento|[Modos del dispositivo StorSimple](storsimple-device-modes.md)|
|Crear un paquete de compatibilidad</br>Descifrar y modificar un paquete de compatibilidad|[Crear y administrar un paquete de compatibilidad](storsimple-create-manage-support-package.md)|
|Iniciar una sesión de soporte técnico</br>|[Iniciar una sesión de soporte técnico de Windows PowerShell para StorSimple](/storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)
 

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Obtener ayuda en Windows PowerShell para StorSimple

En Windows PowerShell para StorSimple, cmdlet ayuda está disponible. Una versión en línea, actualizada de esta Ayuda también está disponible, que puede usar para actualizar la Ayuda en el sistema.

Obtener ayuda en esta interfaz es similar a la de Windows PowerShell y funcionará la mayoría de los cmdlets relacionados con la Ayuda. Puede encontrar ayuda para Windows PowerShell en línea en la biblioteca de TechNet: [Scripting con Windows PowerShell](http://go.microsoft.com/fwlink/?LinkID=108518).

A continuación se muestra una breve descripción de los tipos de ayuda para esta interfaz de Windows PowerShell, incluido cómo actualizar la Ayuda.

#### <a name="to-get-help-for-a-cmdlet"></a>Para obtener ayuda sobre un cmdlet

- Para obtener ayuda para cualquier cmdlet o una función, utilice el siguiente comando:`Get-Help <cmdlet-name>`

- Para obtener ayuda en línea para cualquier cmdlet, use el cmdlet anterior con el `-Online` parámetro:`Get-Help <cmdlet-name> -Online`

- Para obtener ayuda completa, puede usar el `–Full` parámetro y para obtener ejemplos, use la `–Examples` parámetro.

#### <a name="to-update-help"></a>Para actualizar la Ayuda

Puede actualizar fácilmente la Ayuda de la interfaz de Windows PowerShell. Siga estos pasos para actualizar la Ayuda en el sistema.

#### <a name="to-update-cmdlet-help"></a>Para actualizar el cmdlet ayuda

1. Inicie Windows PowerShell con la opción **Ejecutar como administrador** .

1. En el símbolo del sistema, escriba: `Update-Help`

1. Se instalará los archivos de Ayuda actualizados.

1. Una vez instalados los archivos de ayuda, escriba: `Get-Help Get-Command`. Esta opción mostrará una lista de los cmdlets que la Ayuda está disponible.


>[AZURE.NOTE] Para obtener una lista de todos los cmdlets disponibles en un espacio, inicie sesión en la opción de menú correspondiente y ejecutar la `Get-Command` cmdlet.

## <a name="next-steps"></a>Pasos siguientes
Si experimenta problemas con el dispositivo StorSimple al realizar uno de los flujos de trabajo anteriores, consulte [Herramientas para solucionar problemas de implementaciones StorSimple](storsimple-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

