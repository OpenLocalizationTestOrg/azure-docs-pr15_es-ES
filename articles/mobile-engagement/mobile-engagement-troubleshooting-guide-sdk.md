<properties 
   pageTitle="Azure compromiso móvil guía - SDK de solución de problemas" 
   description="Solución de problemas de integración de SDK en Azure Mobile compromiso" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-sdk-integration-issues"></a>Guía de solución de problemas para los problemas de integración de SDK

Los siguientes son posibles problemas que puede encontrarse con cómo se integra compromiso de móvil de Azure en la aplicación.

## <a name="sdk-issues-discovered-by-a-failure-in-another-area-of-your-application"></a>Problemas SDK descubiertas un error en otra área de la aplicación

### <a name="issue"></a>Problema
- Error de colección de datos de interfaz de usuario (en análisis, supervisión, segmentación o paneles).
- Errores de inserción (Push no funciona en la aplicación, fuera de la aplicación, o ambos).
- Avanzadas fallos de función (seguimiento, ubicación geográfica o plataforma que no funciona Push específico).
- Errores de la API (API éxito a menudo silenciosamente sin mensajes de error).
- Errores de servicio (ninguno de Azure Mobile compromiso funciona para su aplicación).

### <a name="causes"></a>Causas

- Mayoría de los problemas que se deben resolver en el SDK de compromiso de Azure Mobile se detectarán por un error en la aplicación (por ejemplo, un error de conjunto de datos de interfaz de usuario, error de inserción, fallos de las funciones avanzadas, error de API, aplicación se bloquea o interrupción del servicio aparente).  
- Si una característica determinada de Azure Mobile compromiso no ha trabajado nunca en la aplicación antes, debe completar la integración. 
- Si una característica determinada de Azure Mobile compromiso funcionaba y detenido, debe actualizar a la última versión en el SDK de compromiso de Azure Mobile. Recuerde que hay una versión diferente de Azure Mobile compromiso SDK para cada plataforma compatible con Azure Mobile compromiso (Android, iOS, Windows y Windows Phone).

#### <a name="sdk-integration"></a>Integración de SDK

- Compromiso de Azure móvil integrado no correctamente en SDK (análisis).
- Póngase en contacto integrado no correctamente en SDK (en la aplicación y fuera de aplicación inserta).
- Certificado expirado o es incorrecta ord frente desarrollo (solo en iOS).
- GCM o ADM integrado no correctamente en SDK (Android sólo - servicio específico inserta).
- Seguimiento no correctamente integrada en SDK (almacén de la instalación de seguimiento).
- Diferida ubicación o GPS no correctamente integrada en SDK (destinatarios por ubicación geográfica).


**Vea también:**

- [Documentación de SDK - guías de integración][Link 5] 
- [Solución de problemas - Guía de inserción][Link 23]

#### <a name="sdk-upgrade"></a>Actualización SDK

- Es necesario actualizar SDK para resolver los problemas con las versiones anteriores de SDK (a menudo relacionadas con versiones más recientes del dispositivo OS).
- Desinstalar todas las versiones anteriores de la aplicación en el dispositivo y vuelva a instalar la versión más reciente de la aplicación, volver a registrar el identificador de dispositivo de la interfaz de usuario de Azure Mobile compromiso para confirmar que el dispositivo está utilizando la versión más reciente de la aplicación.

**Vea también:**

- [Documentación de SDK - notas](http://go.microsoft.com/fwlink/?LinkId= 525554) 
- [Documentación de SDK - guías de actualización](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### <a name="sdk-other"></a>SDK otro

- Errores de aplicación manifiestos "AndroidManifest.xml" pueden provocar Azure Mobile compromiso no trabajar (solo Android).
- Un problema común con integración de SDK y el uso de la API es confundir las teclas SDK y las API.

**Vea también:**

- [Conceptos - glosario][Link 6]

## <a name="advanced-coding-issues"></a>Problemas de codificación de avanzada

### <a name="issue"></a>Problema
-  Código específico de plataforma no directamente relacionada con Azure Mobile compromiso puede provocar problemas en iOS, Android y Windows Phone.

### <a name="causes"></a>Causas

- Muchas avanzada problemas de codificación con Azure Mobile compromiso son causados por no directamente relacionada con Azure Mobile compromiso de código específico de plataforma escrito incorrectamente. Debe consultar la documentación específica de la plataforma de que desarrollar para además de la documentación de Azure Mobile compromiso (Android, iOS, Web, Windows y Windows Phone).
- Configurar no correctamente "categorías", se evita que vincular desde una notificación a otra ubicación dentro o fuera de la aplicación (solo Android). 
- No establecer "UIKit.framework" "opcionales" en el código de iOS, se muestra un símbolo de"no encontrado error" o se bloquea en dispositivos iOS antiguos (solo en iOS).
- Certificados expirados o no correctamente con la versión de desarrollo u Ord de certificado de causas inserción problemas (sólo iOS).
- Existen algunas limitaciones inherente a una plataforma que no se puede controlar el compromiso de Azure Mobile (como por ejemplo funcionamiento para el centro de sistema de la aplicación se inserta en Android y iOS).
- Compromiso de Azure móvil se publica una lista completa de los paquetes internos utilizada Azure Mobile compromiso para iOS y Android para referencia. Tenga en cuenta que algunas características de contratación de Azure Mobile son específicos de la plataforma (Android, iOS, Web, Windows y Windows Phone).

### <a name="see-also"></a>Vea también

 - [Solución de problemas - Guía de inserción][Link 23] 
 - [Documentación de SDK - notas][Link 5]
 - [Documentación de SDK - guías de actualización][Link 5]

## <a name="application-crashes"></a>Errores de aplicación

### <a name="issue"></a>Problema
- La aplicación se bloquea en el dispositivo de los usuarios finales.

### <a name="causes"></a>Causas

- Información de bloqueo puede verse en la *Interfaz de usuario de análisis* o la *API de análisis*
- Puede encontrar el identificador de dispositivo de su dispositivo de prueba y realice la misma acción que provocó su aplicación bloqueo para un usuario final para ayudarle a identificar la causa de su bloqueo.
- A veces se resuelven los problemas conocidos con el SDK de compromiso de Azure Mobile que producen aplicaciones que se bloquea al actualizar a la última versión del SDK. Asegúrese de comprobar las notas acerca de la plataforma al investigar se bloquea.

### <a name="see-also"></a>Vea también

- [Documentación de SDK - notas][Link 5]
- [Documentación de SDK - guías de actualización][Link 5]

## <a name="app-store-upload-failures"></a>Errores de carga de App store

### <a name="issue"></a>Problema
- Errores relacionados con la carga de la última versión de la aplicación de Apple, Google o la tienda de aplicaciones de Windows.

### <a name="causes"></a>Causas

- Aplicación a veces almacena las aplicaciones de bloque con determinadas características habilitadas (por ejemplo, Apple Store evita el uso de IDFV en aplicaciones de la tienda y la tienda GooglePlay evita el uso compartido de información entre las aplicaciones de la aplicación). 
- Asegúrese de que las notas acerca de la plataforma y la versión actual del SDK si tiene dificultades para cargar una aplicación a la tienda.

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/en-us/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/en-us/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/en-us/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 
