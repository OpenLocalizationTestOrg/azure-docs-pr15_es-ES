<properties 
   pageTitle="Solucionar problemas de implementación de StorSimple | Microsoft Azure"
   description="Describe cómo diagnosticar y corregir los errores que se producen cuando se implementa en primer lugar StorSimple."
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
   ms.author="alkohli" />

# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Solucionar problemas de implementación de dispositivo StorSimple

## <a name="overview"></a>Información general

Este artículo proporcionan pautas de solución de problemas útil para la implementación de Microsoft Azure StorSimple. Describe los problemas comunes, las posibles causas y pasos recomendados para ayudar a solucionar los problemas que surjan al configurar StorSimple. Esta información se aplica a dispositivo físico local StorSimple y el dispositivo virtual StorSimple.

> [AZURE.NOTE] Pueden ocurrir problemas relacionados con la configuración de dispositivos que pueden producirse cuando se implementa el dispositivo por primera vez o puede producirse más adelante, cuando el dispositivo está operativo. En este artículo se centra en solución de problemas de implementación por primera vez. Para solucionar un dispositivo operativo, vaya a [solucionar problemas de funcionamiento del dispositivo](storsimple-troubleshoot-operational-device.md).

También en este artículo se describe las herramientas de solución de problemas de implementaciones StorSimple y proporciona un ejemplo paso a paso de solución de problemas.

## <a name="first-time-deployment-issues"></a>Problemas de implementación por primera vez

Si tiene un problema al implementar el dispositivo por primera vez, considere lo siguiente:

- Si está solucionando problemas de un dispositivo físico, asegúrese de que el hardware está instalado y configurado como se describe en [instalar el dispositivo 8100 StorSimple](storsimple-8100-hardware-installation.md) o [el dispositivo 8600 StorSimple](storsimple-8600-hardware-installation.md).
- Compruebe los requisitos previos para su implementación. Asegúrese de que tiene toda la información que se describe de la [lista de comprobación de configuración de implementación](storsimple-deployment-walkthrough.md#deployment-configuration-checklist).
- Revise las notas de la versión de StorSimple para ver si se describe el problema. Las notas incluyen soluciones para los problemas de instalación conocidos. 

Durante la implementación de dispositivo, más comunes los problemas a que los usuarios pueden producirse nominal cuando ejecuta el Asistente de configuración y cuando registre el dispositivo a través de Windows PowerShell para StorSimple. (Utilizar Windows PowerShell para StorSimple para registrar y configurar el dispositivo de StorSimple. Para obtener más información sobre el registro de dispositivo, consulte [paso 3: configurar y registrar el dispositivo a través de Windows PowerShell para StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

Las secciones siguientes pueden ayudar a resolver los problemas que aparecen al configurar el dispositivo StorSimple por primera vez.

## <a name="first-time-setup-wizard-process"></a>Proceso de Asistente de configuración de la primera vez

Los pasos siguientes resumen el proceso del Asistente de configuración. Para obtener información detallada del programa de instalación, vea [implementar el dispositivo de StorSimple local](storsimple-deployment-walkthrough.md).

1. Ejecute el cmdlet [Invocar HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) para iniciar al Asistente de configuración que le guiará en los pasos restantes. 
2. Configurar la red: el Asistente de configuración le permite configurar la red de la interfaz de red 0 de datos en el dispositivo StorSimple. Estas opciones incluyen las siguientes:
  - Virtual IP (VIP), máscara de subred y puerta de enlace, el cmdlet [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) se ejecuta en segundo plano. Configura la dirección IP, máscara de subred y la puerta de enlace para la interfaz de red 0 de datos en el dispositivo StorSimple.
  - Servidor DNS principal: el cmdlet [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) se ejecuta en segundo plano. Establece la configuración de DNS para la solución StorSimple.
  - Servidor NTP: el cmdlet [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) se ejecuta en segundo plano. Configura la configuración del servidor NTP para la solución StorSimple.
  - El proxy web opcional el cmdlet [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) se ejecuta en segundo plano. Establece y habilita la configuración de proxy de web para la solución StorSimple.
3. Configurar las contraseñas: el siguiente paso es configurar el administrador del dispositivo y las contraseñas de administrador de instantáneas StorSimple. Si se están ejecutando Update 1, a continuación, no deberá configurar la contraseña de administrador de instantáneas StorSimple.
  - La contraseña de administrador del dispositivo se usa para iniciar sesión en el dispositivo. La contraseña del dispositivo predeterminado es **Contraseña1**.
  - Al configurar un dispositivo para usar el Administrador de instantánea StorSimple, se requiere la contraseña de administrador de instantáneas StorSimple. Debe establecer primero la contraseña en el Asistente de configuración y, a continuación, puede establecer y cambiar desde el servicio Administrador de StorSimple. Esta contraseña autentica el dispositivo con el Administrador de instantáneas StorSimple.
 
    > [AZURE.IMPORTANT] Las contraseñas se recopilada antes del registro, pero se aplican solo después de registrar correctamente el dispositivo. Si hay un error al aplicar una contraseña, se le pedirá que proporcione la contraseña de nuevo hasta que se recopilan las contraseñas necesarias (que cumplen los requisitos de complejidad).

4. Registrar el dispositivo: el último paso consiste en registrar el dispositivo con el servicio de administrador de StorSimple que se ejecuta en Microsoft Azure. El registro requiere para [obtener la clave de registro de servicio](storsimple-manage-service.md#get-the-service-registration-key) desde el portal de clásico Azure y proporcione en el Asistente de configuración. Después de que el dispositivo se registra correctamente, se proporciona una clave de cifrado de datos de servicio. Asegúrese de mantener esta clave de cifrado en una ubicación segura porque será necesario para registrar todos los dispositivos posteriores con el servicio.

## <a name="common-errors-during-device-deployment"></a>Errores comunes durante la implementación de dispositivo

Las tablas siguientes enumeran los errores comunes que se pueden encontrar al:

- Establecer la configuración de red necesarios.
- Establecer la configuración de proxy de web opcional.
- Configurar el administrador del dispositivo y las contraseñas de administrador de instantáneas StorSimple. 
- Registrar el dispositivo. 

## <a name="errors-during-the-required-network-settings"></a>Errores durante la configuración de red

| No.| Mensaje de error | Causas posibles | Acción recomendada |
| ---| ------------- | --------------- | ------------------ |
| 1  | Invocar-HcsSetupWizard: Este comando sólo se puede ejecutar en el controlador de activo. | Configuración que se realizó en el controlador de pasivo.| Ejecutar este comando desde el controlador activo. Para obtener más información, consulte [identificar un controlador active en el dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).|
| 2 | Invocar-HcsSetupWizard: Dispositivo no está listo. | Existen problemas con la conectividad de red en datos 0.| Compruebe la conectividad de red física de los datos 0.|
| 3 | Invocar-HcsSetupWizard: Hay un conflicto de dirección IP con otro sistema de la red (excepción de HRESULT: 0x80070263). | La dirección IP suministrada para datos 0 ya estaba en uso por otro sistema. | Proporcionar una dirección IP nueva que no está en uso.|
| 4 | Invocar-HcsSetupWizard: Error de un recurso de clúster. (Excepción de HRESULT: 0x800713AE). | Duplicar a VIP. La dirección IP proporcionada ya está en uso.| Proporcionar una dirección IP nueva que no está en uso.|
| 5 | Invocar-HcsSetupWizard: La dirección IPv4 no válidos. | La dirección IP se proporciona en un formato incorrecto.| Compruebe el formato y proporcione su dirección IP de nuevo. Para obtener más información, consulte [Direcciones Ipv4][1]. |
| 6 | Invocar-HcsSetupWizard: La dirección IPv6 no válida. | La dirección IP se proporciona en un formato incorrecto.| Compruebe el formato y proporcione su dirección IP de nuevo. Para obtener más información, consulte [Direcciones Ipv6][2].|
| 7 | Invocar-HcsSetupWizard: Hay no hay más extremos disponibles desde el asignador. (Excepción de HRESULT: 0x800706D9) | La funcionalidad de clúster no funciona. | [Contacto de soporte de Microsoft](storsimple-contact-microsoft-support.md) para los pasos siguientes.

## <a name="errors-during-the-optional-web-proxy-settings"></a>Errores durante la configuración de proxy de web opcional

| No.| Mensaje de error | Causas posibles | Acción recomendada |
| ---| ------------- | --------------- | ------------------ |
| 1  | HcsSetupWizard invocar: Parámetro no válido (excepción de HRESULT: 0 x 80070057) | Uno de los parámetros proporcionados para la configuración de proxy no es válido.| La URI no está disponible en el formato correcto. Utilice el siguiente formato: http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 | Invocar-HcsSetupWizard: El servidor RPC no está disponible (excepción de HRESULT: 0x800706ba) | La causa es una de las siguientes acciones:<ol><li>El clúster no está activo.</li><li>El controlador de pasivo no puede comunicarse con el controlador de active y se ejecuta el comando de controlador pasivo.</li></ol> | Dependiendo de la causa principal:<ol><li>[Contacto de soporte de Microsoft](storsimple-contact-microsoft-support.md) para asegurarse de que el clúster está en marcha.</li><li>Ejecutar el comando desde el controlador activo. Si desea ejecutar el comando desde el controlador pasivo, debe asegurarse de que el controlador de pasivo puede comunicarse con el controlador de activo. Deberá ponerse [en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) si esta conectividad ha quedado rota.</li></ol> |
| 3 | Invocar-HcsSetupWizard: Error de llamada RPC (excepción de HRESULT: 0x800706be) | Clúster es hacia abajo. | [Contacto de soporte de Microsoft](storsimple-contact-microsoft-support.md) para asegurarse de que el clúster está en marcha.|
| 4 | Invocar-HcsSetupWizard: No se encuentra el recurso de clúster (excepción de HRESULT: 0x8007138f) | No se encuentra el recurso. Esto puede ocurrir cuando la instalación no es correcta. | Debe restablecer el dispositivo a la configuración predeterminada de fábrica. [Contacto de soporte de Microsoft](storsimple-contact-microsoft-support.md) para crear un recurso de clúster.|
| 5 | Invocar-HcsSetupWizard: Sin conexión el recurso de clúster (excepción de HRESULT: 0x8007138c)| Recursos de clúster no están en línea. | [Contacto de soporte de Microsoft](storsimple-contact-microsoft-support.md) para los pasos siguientes.|

## <a name="errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords"></a>Errores relacionados con el administrador del dispositivo y las contraseñas de administrador de instantáneas StorSimple

La contraseña de administrador del dispositivo predeterminado es **Contraseña1**. Esta contraseña caduca después del primer inicio de sesión; por lo tanto, debe usar al Asistente de configuración para cambiarlo. Debe proporcionar una nueva contraseña de administrador de dispositivos cuando se registre el dispositivo por primera vez. 

Si utiliza el software de administrador de instantáneas StorSimple que se ejecuta en el host del servidor de Windows para administrar el dispositivo, también debe proporcionar una contraseña de administrador de instantáneas StorSimple durante el registro de la primera vez. 

Asegúrese de que las contraseñas cumplan los requisitos siguientes:

- La contraseña de administrador del dispositivo debe estar entre 8 y 15 caracteres.
- La contraseña de administrador de instantáneas StorSimple debería 14 o 15 caracteres de longitud.
- Las contraseñas deben contener 3 de los siguientes tipos de 4 caracteres: minúsculas, mayúsculas, numéricos y especiales. 
- La contraseña no puede ser la misma que la última 24 contraseñas.

Además, tenga en cuenta que las contraseñas expiren cada año y se pueden cambiar después de registrar correctamente el dispositivo. Si se produce un error en el registro por cualquier motivo, las contraseñas no cambiará. Para obtener más información sobre el administrador del dispositivo y contraseñas StorSimple instantánea Manager, vaya a [usar el servicio de administrador de StorSimple para cambiar las contraseñas de StorSimple](storsimple-change-passwords.md).

Puede encontrar uno o varios de los siguientes errores al configurar el administrador del dispositivo y las contraseñas de administrador de instantáneas StorSimple.

| No.| Mensaje de error | Acción recomendada |
| ---| ------------- | ------------------ | 
| 1  | La contraseña supera la longitud máxima. |Usar una contraseña que cumple los requisitos siguientes:<ul><li>La contraseña de administrador del dispositivo debe estar entre 8 y 15 caracteres.</li><li>La contraseña de administrador de instantáneas StorSimple debe tener 14 o 15 caracteres de longitud.</li></ul> | 
| 2 | La contraseña no cumple la longitud necesaria. | Usar una contraseña que cumple los requisitos siguientes:<ul><li>La contraseña de administrador del dispositivo debe estar entre 8 y 15 caracteres.</li><li>La contraseña de administrador de instantáneas StorSimple debe tener 14 o 15 caracteres de longitud.</lu></ul> |
| 3 | La contraseña debe contener caracteres en minúsculas. | Las contraseñas deben contener 3 de los siguientes tipos de 4 caracteres: minúsculas, mayúsculas, numéricos y especiales. Asegúrese de que la contraseña cumple estos requisitos. |
| 4 | La contraseña debe contener caracteres numéricos. | Las contraseñas deben contener 3 de los siguientes tipos de 4 caracteres: minúsculas, mayúsculas, numéricos y especiales. Asegúrese de que la contraseña cumple estos requisitos. |
| 5 | La contraseña debe contener caracteres especiales. | Las contraseñas deben contener 3 de los siguientes tipos de 4 caracteres: minúsculas, mayúsculas, numéricos y especiales. Asegúrese de que la contraseña cumple estos requisitos. |
| 6 | La contraseña debe contener 3 de los siguientes tipos de 4 caracteres: mayúsculas, minúsculas, numéricos y especiales. | La contraseña no contiene los tipos de caracteres necesarios. Asegúrese de que la contraseña cumple estos requisitos. |
| 7 | Parámetro no coincide con la confirmación. | Asegúrese de que la contraseña cumple todos los requisitos y que ha introducido correctamente. |
| 8 | La contraseña no coincide con el valor predeterminado. | La contraseña predeterminada es *Contraseña1*. Debe cambiar esta contraseña después de iniciar sesión por primera vez. |
| 9 | La contraseña que ha escrito no coincide con el dispositivo. Vuelva a escribir la contraseña. | Compruebe la contraseña y vuelva a escribir. |

Las contraseñas se recopilan antes de que el dispositivo está registrado, pero se aplican solo después de registro correcta. El flujo de trabajo de recuperación de contraseña requiere el dispositivo para registrarse. 

> [AZURE.IMPORTANT] En general, si se produce un error en un intento de aplicar una contraseña, a continuación, el software varias veces intenta recopilar la contraseña hasta que es correcta. En algunas ocasiones, no se puede aplicar la contraseña. En esta situación, puede registrar el dispositivo y continuar, pero no se cambiará las contraseñas. No recibirá indicación qué no se ha cambiado la contraseña: la contraseña de administrador del dispositivo o la contraseña de administrador de instantáneas StorSimple. Si se produce esta situación, se recomienda que cambie ambas contraseñas.

Puede restablecer las contraseñas en el portal de clásica Azure a través del servicio de administrador de StorSimple. Para obtener más información, consulte: 

- [Cambiar la contraseña de administrador de dispositivos](storsimple-change-passwords.md#change-the-device-administrator-password).
- [Cambiar la contraseña de administrador de instantáneas StorSimple](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

## <a name="errors-during-device-registration"></a>Errores durante el registro del dispositivo

Usar el servicio de administrador de StorSimple que se ejecuta en Microsoft Azure registrar el dispositivo. Puede encontrar uno o varios de los siguientes problemas durante el registro del dispositivo.

| No.| Mensaje de error | Causas posibles | Acción recomendada |
| ---| ------------- | --------------- | ------------------ |
| 1  | Error 350027: No se pudo registrar el dispositivo con el Administrador de StorSimple. |  | Espere unos minutos y, a continuación, vuelva a intentarlo. Si el problema continúa, [póngase en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md).|
| 2  | Error 350013: Se ha producido un error registrar el dispositivo. Esto puede deberse a que la clave de registro de servicio incorrecto. | | Registrar el dispositivo nuevo con la clave de registro de servicio correcto. Para obtener más información, consulte [obtener la clave de registro de servicio.](storsimple-manage-service.md#get-the-service-registration-key) |
| 3 | Error 350063: Error de autenticación de servicio de administrador de StorSimple pasa pero el registro. Vuelva a intentar la operación más tarde. | Este error indica que ha pasado la autenticación con ACS pero no se pudo realizar la llamada de registro realizada en el servicio. Esto podría ser el resultado de un problema de red se. | Si el problema persiste, ponte [en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md). |
| 4 | Error 350049: No se pudo alcanzar el servicio durante el registro. | Cuando se realiza la llamada al servicio, se recibe una excepción web. En algunos casos, esto puede obtener fijo con reintentar la operación más adelante. | Compruebe la dirección IP y el nombre DNS y, a continuación, vuelva a intentar la operación. Si el problema persiste, [póngase en contacto con Microsoft Support.](storsimple-contact-microsoft-support.md) | 
| 5 | Error 350031: El dispositivo ya se ha registrado. | | No es necesaria ninguna acción. |
| 6 | Error 350016: Error de registro de dispositivo. | |Asegúrese de que la clave del registro es correcta. |
| 7 | Invocar-HcsSetupWizard: Ha ocurrido un error al registrar su dispositivo; Esto puede deberse a dirección IP incorrecta o el nombre DNS. Compruebe la configuración de red y vuelva a intentarlo. Si el problema continúa, [póngase en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md). (Error 350050) | Asegúrese de que el dispositivo puede hacer ping a la red externa. Si no tiene conectividad de red externa, el registro puede fallar con este error. Este error puede ser una combinación de uno o varios de estos procedimientos:<ul><li>IP incorrecta</li><li>Subred incorrecta</li><li>Puerta de enlace incorrecta</li><li>Configuración de DNS incorrecta</li></ul> | Consulte los pasos en el [ejemplo paso a paso de solución de problemas](#step-by-step-storsimple-troubleshooting-example). |
| 8 | Invocar-HcsSetupWizard: Error en la operación actual debido a un error de servicio interno [0x1FBE2]. Vuelva a intentar la operación después de unos minutos. Si el problema continúa, póngase en contacto con Microsoft Support. | Se trata de un error genérico iniciado para todos los errores de usuario invisibles de agente o servicio. Puede ser la razón más común que ha fallado la autenticación de ACS. Una posible causa del error es que hay problemas con la configuración del servidor NTP y hora del dispositivo no está configurado correctamente. | Corrija la hora (si hay problemas) y, a continuación, vuelva a intentar la operación de registro. Si usa el comando conjunto HcsSystem - zona horaria para ajustar la zona horaria, poner en mayúsculas cada palabra en la zona horaria (por ejemplo "hora estándar del Pacífico").  Si este problema persiste, [contacto de soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para los pasos a continuación. |
| 9 | Advertencia: No se pudo activar el dispositivo. El administrador del dispositivo y contraseñas StorSimple instantánea Manager no han cambiado. | Si se produce un error en el registro, el administrador del dispositivo y contraseñas StorSimple instantánea Manager no han cambiado. |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Herramientas para solucionar problemas de implementaciones StorSimple

StorSimple incluye varias herramientas que puede utilizar para solucionar problemas de la solución StorSimple. Se incluyen:

- Paquetes de soporte técnico y los registros de dispositivo 
- Cmdlets de diseñados específicamente para la solución de problemas 

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Soporte técnico de paquetes y los registros de dispositivo disponibles para solucionar problemas

Un paquete de compatibilidad contiene todos los registros relevantes que pueden ayudar al equipo de Microsoft Support para solucionar problemas de dispositivo. Puede usar Windows PowerShell para StorSimple para generar un paquete de compatibilidad cifrado que puede compartir con el personal de soporte técnico.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Para ver los registros o el contenido del paquete de compatibilidad

1. Usar Windows PowerShell para StorSimple para generar un paquete de compatibilidad, como se describe en [crear y administrar un paquete de compatibilidad con](storsimple-create-manage-support-package.md).

2. Descargar el [script de descifrado](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente en el equipo cliente.

3. Use este [procedimiento paso a paso](storsimple-create-manage-support-package.md#edit-a-support-package) para abrir y descifrar el paquete de compatibilidad.

4. Los registros de paquete de compatibilidad descifrado están en formato de etw/etvx. Puede realizar los siguientes pasos para ver estos archivos en el Visor de eventos de Windows:
  1. Ejecute el comando **eventvwr** en el cliente de Windows. Se iniciará el Visor de eventos.
  2. En el panel de **acciones** , haga clic en **Abrir registro guardado** y seleccione los archivos de registro en formato de etvx/etw (el paquete de compatibilidad). Ahora puede ver el archivo. Después de abrir el archivo, puede secundario y guarde el archivo como texto.
   
    > [AZURE.IMPORTANT] También puede usar el cmdlet **Get-WinEvent** abrir estos archivos en Windows PowerShell. Para obtener más información, consulte [Obtener WinEvent](https://technet.microsoft.com/library/hh849682.aspx) en la documentación de referencia de cmdlet de Windows PowerShell.

5. Cuando los registros se abre en el Visor de sucesos, busque los siguientes registros que contienen los problemas relacionados con la configuración del dispositivo:

  - Registro de hcs_pfconfig/operativos
  - hcs_pfconfig/Config

6. En los archivos de registro, buscar cadenas relacionadas con los cmdlets llamados por el Asistente de configuración. Para obtener una lista de estos cmdlets, vea [proceso de Asistente de configuración de primera vez](#first-time-setup-wizard-process) . 

7. Si no puede averiguar la causa del problema, puede [ponerse en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para los pasos siguientes. Realice los pasos de [crear una solicitud de soporte](storsimple-contact-microsoft-support.md#create-a-support-request) cuando póngase en contacto con Microsoft Support para obtener ayuda.

## <a name="cmdlets-available-for-troubleshooting"></a>Cmdlets disponibles para la solución de problemas

Use los siguientes cmdlets de Windows PowerShell para detectar errores de conectividad.

- `Get-NetAdapter`: Utilice este cmdlet para detectar el estado de interfaces de red. 

- `Test-Connection`: Utilice este cmdlet para comprobar la conectividad de red dentro y fuera de la red.

- `Test-HcsmConnection`: Use este cmdlet para comprobar la conectividad de un dispositivo registrado correctamente.

Si se están ejecutando Update 1 en su dispositivo StorSimple, también están disponibles los siguientes cmdlets de diagnósticos.

- `Sync-HcsTime`: Use este cmdlet para mostrar la hora del dispositivo y forzar una sincronización de tiempo con el servidor NTP.

- `Enable-HcsPing`y `Disable-HcsPing`: Utilice estos cmdlets para permitir que los hosts hacer ping a las interfaces de red en el dispositivo StorSimple. De forma predeterminada, las interfaces de red StorSimple no responde a las solicitudes de ping.

- `Trace-HcsRoute`: Use este cmdlet como una herramienta de seguimiento de la ruta. Envía paquetes a cada enrutador hacia un destino final durante un período de tiempo y, a continuación, calcula los resultados basándose en los paquetes devueltos por cada salto. Puesto que `Trace-HcsRoute` muestra el grado de pérdida de paquetes en un determinado enrutador o un vínculo, puede determinar qué enrutadores o vínculos podrían estar causando problemas de red. 

- `Get-HcsRoutingTable`: Use este cmdlet para mostrar la tabla de enrutamiento IP local.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Solucionar problemas con el cmdlet Get-AdaptadorRed

Al configurar interfaces de red para una implementación de dispositivo de la primera vez, el estado de hardware no está disponible en el servicio de administrador de StorSimple IU porque el dispositivo no está todavía registrado con el servicio. Además, la página de estado de Hardware puede no siempre refleja correctamente el estado del dispositivo, especialmente si hay problemas que afectan a la sincronización del servicio. En estos casos, puede usar el `Get-NetAdapter` cmdlet para determinar el estado y el estado de las interfaces de red.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Para ver una lista de todos los adaptadores de red en el dispositivo

1. Inicio de Windows PowerShell para StorSimple y, a continuación, escriba `Get-NetAdapter`. 

2. Utilizar los resultados de la `Get-NetAdapter` cmdlet y las siguientes pautas para conocer el estado de la interfaz de red.
  - Si la interfaz está habilitado y correcto, se muestra el estado de **ifIndex** como **hacia arriba**.
  - Si la interfaz es correcto, pero no está conectada física (mediante un cable de red), se muestra la **ifIndex** como **deshabilitado**.
  - Si la interfaz es correcto pero no está habilitado, se muestra el estado de **ifIndex** como **no se encuentra**.
  - Si no existe la interfaz, no aparecen en esta lista. El servicio de administrador de StorSimple IU seguirá mostrando esta interfaz en un estado de error.

Para obtener más información sobre cómo usar este cmdlet, vaya a [GetNetAdapter](https://technet.microsoft.com/library/jj130867.aspx) en la referencia de cmdlet de Windows PowerShell. 

Las siguientes secciones muestran ejemplos de resultados de la `Get-NetAdapter` cmdlet. 

 En estos ejemplos, controlador 0 era el controlador de pasivo y se ha configurado como sigue:

- DATOS 0, 1 de datos, DATA 2 y 3 de datos de red interfaces existían en el dispositivo.
- DATOS 4 y 5 de datos de tarjetas de red no estaban presentes; por lo tanto, no se muestran en el resultado.
- Se ha habilitado datos 0.

Controlador 1 era el controlador de activo y se ha configurado como sigue:

- DATOS 0, datos 1, 2 de datos, datos 3, 4 de datos y red de datos 5 interfaces existían en el dispositivo.
- Se ha habilitado datos 0.

**Resultados de ejemplo: controlador 0**

El siguiente es el resultado de controlador de 0 (el controlador pasivo). DATOS 1, dato 2 y 3 de datos no están conectado. DATOS 4 y 5 de datos no se muestran porque no están presentes en el dispositivo. 

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Resultados de ejemplo: controlador 1**

A continuación se muestra el resultado de controlador de 1 (el controlador activo). Solo los datos 0 se configura la interfaz de red en el dispositivo y el trabajo.

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent

 
## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Solucionar problemas con el cmdlet de conexión de prueba

Puede usar el `Test-Connection` cmdlet para determinar si el dispositivo de StorSimple puede conectarse a la red externa. Si todos los parámetros de red, incluido el DNS están configurados correctamente en el Asistente de configuración, puede usar el `Test-Connection` cmdlet para hacer ping a una dirección conocida fuera de la red, como outlook.com. 

Debe habilitar ping solucionar problemas de conectividad con este cmdlet si se deshabilita el ping.

Vea los ejemplos siguientes del resultado de la `Test-Connection` cmdlet. 

> [AZURE.NOTE] En el primer ejemplo, el dispositivo está configurado con un DNS incorrecto. En el segundo ejemplo, el DNS es correcto.
 
**Resultados de ejemplo: DNS incorrecta**

En el ejemplo siguiente, no hay ningún resultado para las direcciones IPV4 e IPV6, lo que indica que no se resuelve el DNS. Esto significa que no hay ninguna conectividad a la red externa y debe proporcionarse un DNS correcto. 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Resultados de ejemplo: DNS correcto**

En el ejemplo siguiente, el DNS devuelve la dirección IPV4, que indica que el DNS está configurado correctamente. Esto confirma que hay conectividad con la red externa. 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Solucionar problemas con el cmdlet HcsmConnection de prueba

Usar el `Test-HcsmConnection` cmdlet para un dispositivo que ya está conectado a y registrado con el servicio de administrador de StorSimple. Este cmdlet le ayuda a comprobar la conectividad entre un dispositivo registrado y el servicio de administrador de StorSimple correspondiente. Puede ejecutar este comando en Windows PowerShell para StorSimple. 

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Para ejecutar el cmdlet HcsmConnection de prueba

1. Asegúrese de que el dispositivo está registrado.

2. Comprobar el estado del dispositivo. Si el dispositivo está desactivado, en modo de mantenimiento o sin conexión, es posible que vea los siguientes errores: 

   - ErrorCode.CiSDeviceDecommissioned: indica que el dispositivo está desactivado.
   - ErrorCode.DeviceNotReady: indica que el dispositivo está en modo de mantenimiento.
   - ErrorCode.DeviceNotReady: indica que el dispositivo no está en línea.

3. Compruebe que está ejecutando el servicio Administrador de StorSimple (use el cmdlet [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) ). Si no se está ejecutando el servicio, es posible que vea los siguientes errores:

   - ErrorCode.CiSApplianceAgentNotOnline
   - ErrorCode.CisPowershellScriptHcsError: indica que se ha producido una excepción cuando ejecutó Get-ClusterResource.

4. Comprobar el token de servicio de Control de acceso (ACS). Si se produce una excepción de web, sería el resultado de un problema de puerta de enlace, falta la autenticación de proxy, un DNS incorrecta o un error de autenticación. Es posible que vea los siguientes errores:

   - ErrorCode.CiSApplianceGateway: indica una excepción de HttpStatusCode.BadGateway: el servicio de resolución de nombre no pudo resolver el nombre de host. 
   - ErrorCode.CiSApplianceProxy: indica una excepción de HttpStatusCode.ProxyAuthenticationRequired (código de estado HTTP 407): el cliente no pudo autenticar con el servidor proxy. 
   - ErrorCode.CiSApplianceDNSError: indica una excepción de WebExceptionStatus.NameResolutionFailure: el servicio de resolución de nombre no pudo resolver el nombre de host.
   - ErrorCode.CiSApplianceACSError: indica que el servicio devuelve un error de autenticación, pero hay conectividad.
   
    Si no se produce una excepción de web, busque ErrorCode.CiSApplianceFailure. Esto indica que el dispositivo no.

5. Compruebe la conectividad de servicio de nube. Si el servicio inicia una excepción de web, es posible que vea los siguientes errores:

  - ErrorCode.CiSApplianceGateway: indica una excepción de HttpStatusCode.BadGateway: un servidor proxy intermedio ha recibido una solicitud incorrecta de otro proxy o del servidor original.
  - ErrorCode.CiSApplianceProxy: indica una excepción de HttpStatusCode.ProxyAuthenticationRequired (código de estado HTTP 407): el cliente no pudo autenticar con el servidor proxy. 
  - ErrorCode.CiSApplianceDNSError: indica una excepción de WebExceptionStatus.NameResolutionFailure: el servicio de resolución de nombre no pudo resolver el nombre de host.
  - ErrorCode.CiSApplianceACSError: indica que el servicio devuelve un error de autenticación, pero hay conectividad.
  
    Si no se produce una excepción de web, busque ErrorCode.CiSApplianceSaasServiceError. Esto indica un problema con el servicio Administrador de StorSimple.
 
6. Compruebe la conectividad de Bus de servicio de Azure. ErrorCode.CiSApplianceServiceBusError indica que el dispositivo no puede conectarse al Bus de servicio.
 
Los archivos de registro CiSCommandletLog0Curr.errlog y CiSAgentsvc0Curr.errlog tendrá más información, como los detalles de la excepción. 

Documentación de referencia para obtener más información sobre cómo usar el cmdlet y, a continuación, vaya a la [Prueba HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) en Windows PowerShell.

> [AZURE.IMPORTANT] Puede ejecutar este cmdlet para la activa y el controlador de pasivo. 
 
Vea los ejemplos siguientes del resultado de la `Test-HcsmConnection` cmdlet. 

**Resultado: dispositivo correctamente registrado ejecuta StorSimple versión de ejemplo (julio de 2014)**

El primer ejemplo es de un dispositivo que está registrado correctamente con el servicio Administrador de StorSimple y no tiene problemas de conectividad. 

     Controller1>Test-HcsmConnection -verbose
     Checking device state  ... Success.
     Device registered successfully
     Checking device authentication.  ... This operation will take few minutes to complete....
     Checking device authentication.  ... Success.
     Checking connectivity from device to StorSimple Manager service.  ... This operation will take few minutes to complete....
     Checking connectivity from device to StorSimple Manager service.  ... Success.
     Checking connectivity from StorSimple Manager service to StorSimple device. .... Success.
     Controller1>

**Resultados de ejemplo: dispositivo correctamente registrado ejecutando StorSimple Update 1**

Si se están ejecutando Update 1 en su dispositivo StorSimple, no necesitará ejecutar con el modificador detallado.

      Controller1>Test-HcsmConnection
       
      Checking device registration state  ... Success
      Device registered successfully
       
      Checking primary NTP server [time.windows.com] ... Success
       
      Checking web proxy  ... NOT SET
       
      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET
       
      Checking device online  ... Success
 
      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success
       
      Checking connectivity from device to service  ... This will take a few minutes.
       
      Checking connectivity from device to service  ... Success
       
      Checking connectivity from service to device  ... Success
       
      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**Resultado: dispositivo sin conexión ejecuta StorSimple versión de ejemplo (julio de 2014)**

Este ejemplo es de un dispositivo que tiene un estado **sin conexión** en el portal de clásico de Azure.

     Checking device state: Success 
     Device is registered successfully 
     Checking connectivity from device to SaaS.. Failure

El dispositivo no se pudo conectar con la configuración de proxy de web actual. Esto podría ser un problema con la configuración de proxy de web o un problema de conectividad de red. En este caso, debe asegurarse de que la configuración de proxy de web es correcta y los servidores proxy de web están en línea y accesibles. 

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Solucionar problemas con el cmdlet de sincronización HcsTime

Use este cmdlet para mostrar la hora del dispositivo. Si la hora del dispositivo tiene un desplazamiento con el servidor NTP, a continuación, puede usar este cmdlet para la hora de forzar sincronizar con el servidor NTP. Si el desplazamiento entre el dispositivo y el servidor NTP es mayor de 5 minutos, se muestra una advertencia. Si el desplazamiento supera 15 minutos, el dispositivo pasará sin conexión. Aún puede usar este cmdlet para forzar una sincronización de tiempo. Sin embargo, si el desplazamiento supera 15 horas, a continuación, no podrá a se muestran la hora y un mensaje de error de sincronización de fuerza.

**Resultados de ejemplo: sincronización de hora forzado con sincronización HcsTime**
 
     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.
 
     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Solucionar problemas con los cmdlets HcsPing habilitar y deshabilitar HcsPing

Utilice estos cmdlets para asegurarse de que las interfaces de red en el dispositivo responden a solicitudes de ping ICMP. De forma predeterminada las interfaces de red StorSimple no responde a las solicitudes de ping. Este cmdlet es la manera más sencilla de saber si el dispositivo está en línea y accesibles.  

**Resultados de ejemplo: HcsPing habilitar y deshabilitar HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Solucionar problemas con el cmdlet HcsRoute de seguimiento

Use este cmdlet como una herramienta de seguimiento de la ruta. Envía paquetes a cada enrutador hacia un destino final durante un período de tiempo y, a continuación, calcula los resultados basándose en los paquetes devueltos por cada salto. Puesto que el cmdlet muestra el grado de pérdida de paquetes en un determinado vínculo o enrutador, puede determinar qué enrutadores o vínculos podrían estar causando problemas de red.

**Resultados de ejemplo que muestra cómo realizar un seguimiento de la ruta de un paquete con seguimiento HcsRoute**

     Controller0>Trace-HcsRoute -Target 10.126.174.25
     
     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]
      
     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]
      
     Trace complete.

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Solucionar problemas con el cmdlet Get-HcsRoutingTable

Use este cmdlet para ver la tabla de enrutamiento de su dispositivo StorSimple. Una tabla de enrutamiento es un conjunto de reglas que pueden ayudar a determinar donde se dirigirá los paquetes de datos de viaje en una red de protocolo de Internet (IP). 

La tabla de enrutamiento muestra las interfaces y la puerta de enlace que distribuya los datos a las redes especificadas. También le ofrece la métrica de enrutamiento que es la toma de decisiones para la ruta de acceso para alcanzar un destino concreto. La parte inferior la métrica de enrutamiento, mayor será la preferencia. 

Por ejemplo, si tiene 2 interfaces de red, datos 2 y 3 de datos, conectado a Internet. Si la métrica de enrutamiento de datos 2 y 3 de datos es 15 y 261, respectivamente, 2 de datos con la métrica de enrutamiento inferior es la interfaz preferida que se usa para conectarse a Internet.

Si se están ejecutando Update 1 en su dispositivo StorSimple, la interfaz de red 0 de datos tiene mayor preferencia para el tráfico de la nube. Esto significa que incluso si hay otras interfaces habilitadas para la nube, la nube ¿enrutar el tráfico a través de datos 0. 

Si ejecuta la `Get-HcsRoutingTable` cmdlet sin especificar parámetros (como se muestra en el siguiente ejemplo), el cmdlet generará tablas de enrutamiento de IPv4 e IPv6. Como alternativa, puede especificar `Get-HcsRoutingTable -IPv4` o `Get-HcsRoutingTable -IPv6` para obtener una tabla de enrutamiento relevante.

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================
       
      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================
       
      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None
       
      Controller0>
 
## <a name="step-by-step-storsimple-troubleshooting-example"></a>Ejemplo paso a paso de solución de problemas de StorSimple

En el ejemplo siguiente se muestra paso a paso de solución de problemas de una implementación de StorSimple. En la situación de ejemplo, registro de dispositivo se produce un mensaje de error que indica que la configuración de la red o el nombre DNS es incorrecto.

El mensaje de error devuelto es:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

El error podría estar causado por cualquiera de las siguientes acciones:

- Instalación de hardware incorrecto
- Interfaces de red defectuoso
- Dirección IP incorrecta, máscara de subred, puerta de enlace, servidor DNS principal o proxy web
- Clave de registro incorrecto
- Configuración del firewall incorrecto

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Para buscar y solucionar el problema de registro de dispositivo

1. Comprobar la configuración del dispositivo: en el controlador activo, ejecute `Invoke-HcsSetupWizard`.

     > [AZURE.NOTE] En el controlador de activo, debe ejecutar el Asistente de configuración. Para comprobar que está conectado al controlador activo, examine la pancarta realizada en la consola serie. La cabecera indica si está conectado a controlador de 0 o 1 y, si el controlador está activo o pasivo. Para obtener más información, vaya a [identificar un controlador active en el dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).
 
2. Asegúrese de que el dispositivo está conectado correctamente: Compruebe la red cableado en el dispositivo posterior. El cableado es específico en el modelo de dispositivo. Para obtener más información, vaya a [instalar el dispositivo 8100 StorSimple](storsimple-8100-hardware-installation.md) o [el dispositivo 8600 StorSimple](storsimple-8600-hardware-installation.md).

     > [AZURE.NOTE] Si está utilizando puertos de red de 10 GbE, debe usar los cables SFP y proporcionados adaptadores QSFP SFP. Para obtener más información, consulte la [lista de cables, modificadores y transceptores recomendados por el proveedor OEM para Mellanox puertos](http://www.mellanox.com/page/cables?mtag=cable_overview).
 
3. Comprobar el estado de la interfaz de red:

   - Use el cmdlet Get-AdaptadorRed para detectar el estado de las interfaces de red para datos 0. 
   - Si el vínculo no funciona, el estado de **ifindex** indica que la interfaz es hacia abajo. Deberá comprobar la conexión de red del puerto para el dispositivo y cambiar. También necesitará descartar cables incorrectos. 
   - Si sospecha que los datos 0 puerto en el controlador activo tiene un error, puede confirmar si se conecta a los datos puerto 0 en el controlador de 1. Para confirmarlo, desconecte el cable de red de la parte posterior del dispositivo de controlador de 0, conecte el cable al controlador 1 y, a continuación, ejecute el cmdlet Get-AdaptadorRed de nuevo. 
   Si los datos 0 puerto en un controlador falla, [póngase en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para los pasos siguientes. Debe reemplazar el controlador del sistema.
 
4. Compruebe la conectividad con el modificador:
   - Asegúrese de que son interfaces de red 0 de datos en el controlador 0 y 1 en su alojamiento principal en la misma subred. 
   - Compruebe el concentrador o enrutador. Normalmente, debe conectar ambos controladores al mismo concentrador o enrutador. 
   - Asegúrese de que los modificadores que usa para la conexión tienen datos 0 para ambos controladores en la misma vLAN.
   
5. Eliminar los errores de usuario:

  - Ejecutar al Asistente de configuración nuevamente (ejecución **Invocar HcsSetupWizard**) y escriba los valores para asegurarse de que no hay ningún error. 
  - Comprobar el registro de la clave que se utiliza. La misma clave de registro puede usarse para conectar varios dispositivos a un servicio de administrador de StorSimple. Use el procedimiento de [obtener la clave de registro de servicio](storsimple-manage-service.md#get-the-service-registration-key) para asegurarse de que está utilizando la clave de registro correcto.

    > [AZURE.IMPORTANT] Si tiene varios servicios que se ejecutan, debe asegurarse de que la clave de registro para el servicio adecuado se utiliza para registrar el dispositivo. Si ha registrado un dispositivo con el servicio de administrador de StorSimple un problema, deberá [ponerse en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para los pasos siguientes. Debe realizar un restablecimiento de fábrica del dispositivo (lo que podría provocar la pérdida de datos), a continuación, conectarse al servicio previsto.

6. Use el cmdlet de Probar conexión para comprobar que tiene conectividad con la red externa. Para obtener más información, vaya a [solucionar problemas con el cmdlet de conexión de prueba](#troubleshoot-with-the-test-connection-cmdlet).

7. Verificación de interferencias de firewall. Si ha comprobado la configuración de IP (VIP), subred, puerta de enlace y DNS virtual es correcta y todavía experimenta problemas de conectividad, es posible que el firewall está bloqueando la comunicación entre el dispositivo y la red externa. Debe asegurarse de que los puertos 80 y 443 están disponibles en el dispositivo StorSimple para las comunicaciones salientes. Para obtener más información, consulte [requisitos de su dispositivo StorSimple de red](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).

8. Busque los registros. Vaya a [registros de dispositivo disponibles para solucionar problemas y paquetes de soporte técnico](#support-packages-and-device-logs-available-for-troubleshooting).

9. Si los pasos anteriores no soluciona el problema, [póngase en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para obtener ayuda.

## <a name="next-steps"></a>Pasos siguientes
[Obtenga información sobre cómo solucionar problemas de un dispositivo de operaciones](storsimple-troubleshoot-operational-device.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
