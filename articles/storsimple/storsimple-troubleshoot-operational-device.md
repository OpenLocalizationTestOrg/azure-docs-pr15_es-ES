<properties 
   pageTitle="Solucionar problemas de un dispositivo StorSimple implementado | Microsoft Azure"
   description="Describe cómo diagnosticar y corregir los errores que se producen en un dispositivo de StorSimple que se encuentra actualmente implementado y en funcionamiento."
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
   ms.date="05/16/2016"
   ms.author="v-sharos" />

# <a name="troubleshoot-an-operational-storsimple-device"></a>Solucionar problemas de un dispositivo StorSimple operativo

## <a name="overview"></a>Información general

En este artículo se proporciona pautas de solución de problemas útil para resolver problemas de configuración que se pueden encontrar después de que el dispositivo StorSimple implementada y en funcionamiento. Describe los problemas comunes, las posibles causas y pasos recomendados para ayudar a solucionar los problemas que surjan al ejecutar Microsoft Azure StorSimple. Esta información se aplica a dispositivo físico local StorSimple y el dispositivo virtual StorSimple.

Al final de este artículo que encontrará una lista de códigos de error que pueden surgir durante la operación de Microsoft Azure StorSimple, así como los pasos que puede realizar para resolver los errores. 

## <a name="setup-wizard-process-for-operational-devices"></a>Proceso de configuración de Asistente para dispositivos operativos

Usar el Asistente de configuración ([Invocar HcsSetupWizard][1]) para comprobar la configuración del dispositivo y tomar medidas correctivas si es necesario.

Cuando se inicia el Asistente de configuración en un dispositivo previamente configurado y funciona, el flujo del proceso es diferente. Puede cambiar solo las entradas siguientes:

- Dirección IP, la máscara de subred y la puerta de enlace
- Servidor DNS principal
- Servidor NTP primario
- Configuración de proxy de web opcional

El Asistente de configuración no realiza las operaciones relacionadas con el registro de recopilación y dispositivo de contraseña.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Errores que se producen durante las posteriores ejecuciones del Asistente de configuración

La siguiente tabla describe los errores que pueden surgir al ejecutar el Asistente de configuración en un dispositivo operativo, las posibles causas de los errores y las medidas recomendadas para resolverlos. 

| No. | Mensaje de error o condición | Causas posibles | Acción recomendada |
|:--- |:-------------------------- |:--------------- |:------------------ |
|  1  | Error 350032: Ya se ha desactivado este dispositivo. | Verá este error si se inicia al Asistente de configuración en un dispositivo que está desactivado. | [Contacto de soporte de Microsoft](storsimple-contact-microsoft-support.md) para los pasos siguientes. No se puede poner un dispositivo desactivado en el servicio. Restablecimiento de fábrica puede ser necesario antes de que el dispositivo se puede activar de nuevo. |
|  2  | Invocar-HcsSetupWizard: ERROR_INVALID_FUNCTION (excepción de HRESULT: 0x80070001) | Falla la actualización del servidor DNS. Configuración de DNS es configuraciones globales y se aplica en todas las interfaces de red habilitado. | Habilitar la interfaz y vuelva a aplicar la configuración de DNS. Esto puede interrumpir la red para otras interfaces habilitados porque esta configuración es global. |
|  3  | El dispositivo aparece a estar en línea en el portal de servicio de administrador de StorSimple, pero cuando intenta completar la configuración mínima y guardar la configuración, se produce un error en la operación. | Durante la instalación inicial, no se configuró el proxy web, incluso si se ha producido un servidor proxy real en su lugar. | Usar el [cmdlet de prueba HcsmConnection] [ 2] para localizar el error. [Contacto de soporte de Microsoft](storsimple-contact-microsoft-support.md) si no puede corregir el problema. |
|  4  | Invocar-HcsSetupWizard: Valor no está dentro del intervalo esperado. | Una máscara de subred incorrecta produce este error. Causas posibles son: <ul><li> La máscara de subred falta o está vacío.</li><li>El formato de prefijo Ipv6 es incorrecto.</li><li>La interfaz está habilitada para la nube, pero la puerta de enlace es que faltan o son incorrectos.</li></ul>Tenga en cuenta que datos 0 automáticamente nube habilitada si ha configurado mediante el Asistente de configuración. | Para determinar el problema, use subred 0.0.0.0 o 256.256.256.256 y, a continuación, mire el resultado. Escriba los valores correctos para la máscara de subred, la puerta de enlace y el prefijo Ipv6, según sea necesario. |
 
## <a name="error-codes"></a>Códigos de error

Se muestran en orden numérico.

|Número de error|Texto de error o la descripción|Acción recomendada de usuario|
|:---|:---|:---|
|10502|Un error al acceder a su cuenta de almacenamiento.|Espere unos minutos y vuelva a intentarlo. Si el error persiste, ponte contacto soporte técnico de Microsoft para los pasos siguientes.|
|40017|Error en la operación de copia de seguridad como un volumen especificado en la directiva de copia de seguridad no se ha encontrado en el dispositivo.|Vuelva a intentar la copia de seguridad de la operación, si el error persiste, póngase en contacto con Microsoft Support. para los pasos siguientes.|
|40018|Error en la operación de copia de seguridad como ninguno de los volúmenes especificados en la directiva de copia de seguridad se han encontrado en el dispositivo. |Vuelva a intentar la copia de seguridad de la operación, si el error persiste, póngase en contacto con Microsoft Support. para los pasos siguientes.|
|390061|El sistema está ocupado o no está disponible.|Espere unos minutos y vuelva a intentarlo. Si el error persiste, ponte contacto soporte técnico de Microsoft para los pasos siguientes.|
|390143|Ha ocurrido un error con código de error 390143. (Error desconocido).|Si el error persiste, póngase en contacto con Microsoft Support para los pasos siguientes.|

## <a name="next-steps"></a>Pasos siguientes

Si no puede resolver el problema, [póngase en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para obtener ayuda. 


[1]: https://technet.microsoft.com/en-us/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/en-us/%5Clibrary/Dn715782(v=WPS.630).aspx
