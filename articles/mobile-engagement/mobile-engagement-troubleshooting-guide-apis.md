<properties 
   pageTitle="Azure compromiso móvil guía - API de solución de problemas" 
   description="Guías de Azure contratación móvil: las API de solución de problemas" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="10/04/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-api-issues"></a>Guía para solucionar problemas de la API

Los siguientes son posibles problemas que puede encontrarse con cómo interactúan los administradores con Azure Mobile compromiso a través de la API.

## <a name="syntax-issues"></a>Problemas de sintaxis

### <a name="issue"></a>Problema
- Errores de sintaxis con la API (o el comportamiento inesperado).

### <a name="causes"></a>Causas

- Problemas de sintaxis:
    - Asegúrese de comprobar la sintaxis de la API específica que se va a usar para confirmar que la opción está disponible.
    - Es un problema común de uso de la API confundir la API alcance y la API de inserción (la mayoría de las tareas debe realizarse con la API alcance en lugar de la API de inserción). 
    - Otro problema común con integración de SDK y el uso de la API es confundir las teclas SDK y las API.
    - Las secuencias de comandos que se conectan a la API debe enviar datos al menos cada 10 minutos o la conexión tendrá tiempo de espera (especialmente común en las secuencias de comandos de la API Monitor escucha de datos). Para evitar que los tiempos de espera, tiene la secuencia de comandos enviar un ping XMPP cada 10 minutos para mantener la sesión activa con el servidor.

### <a name="see-also"></a>Vea también
 
- [Documentación de la API][Link 4]
- [Información de protocolo XMPP]( http://xmpp.org/extensions/xep-0199.html)
 
## <a name="unable-to-use-the-api-to-perform-the-same-action-available-in-the-azure-mobile-engagement-ui"></a>No se puede utilizar la API para realizar la misma acción disponible en la interfaz de usuario de compromiso de móvil de Azure

### <a name="issue"></a>Problema
- Una acción adecuada de la interfaz de usuario de compromiso de Azure Mobile no funciona de la API de compromiso de Mobile Azure relacionados.

### <a name="causes"></a>Causas

- Confirme que puede realizar la misma acción de la interfaz de usuario de compromiso de Azure Mobile muestra que han integrado correctamente esta característica de Azure Mobile compromiso con el SDK.

### <a name="see-also"></a>Vea también
 
- [Documentación de la interfaz de usuario][Link 1]
 
## <a name="error-messages"></a>Mensajes de error

### <a name="issue"></a>Problema
- Códigos de error con la API mostrada en tiempo de ejecución o en los registros.

### <a name="causes"></a>Causas

- Aquí tiene una lista compuesta de números de códigos de estado de API comunes para referencia y solución de problemas preliminar:

        200        Success.
        200        Account updated: device registered, associated, updated, or removed from the current account.
        200        Returns a list of projects as a JSON object or an authentication token generated and returned in the response’s body.
        201        Account created.
        400        Invalid parameter or validation exception (check payload for details). The parameters provided to the API or service are invalid. In this case, the HTTP response will embed more details. Make sure to test for the MIME type of the response as the payload can either be plain text or a JSON object.
        401        Authentication error. No user is currently authenticated or connected (check the AppID and SDK key).
        402        Billing lock. The application is either off its quotas or is currently in a bad billing state.
        403        The application is not enabled or the specific API is disabled for this application.
        403        Unauthorized access to the project or application, invalid access key (the key must match the one provided when created).
        403        Campaign specific errors: campaign must be finished (or has already been activated), the suspend action can only be performed on an scheduled campaign, cannot finish a campaign that is not currently “in progress”, campaign must be “in progress” and the campaign’s property named, manual Push must be set to true.
        403        The email address is already associated to another account (a super user for instance). No authentication token will be generated.
        404        Application, device, campaign, or project identifier not found.
        404        Query parameter is invalid JSON or has a field with an unexpected value.
        404        The email address is not associated with an account. Please create or update the account first.
        405        Invalid HTTP method (GET, POST, etc.) or trying to edit a read only segment (i.e. add or update or delete a criterion). A segment becomes read only after it has been computed for the first time.
        409        Name already associated to a different device ID or campaign.
        413        Too many device identifiers (current limit is 1,000), POST URL encoded entity is over 2MB, or the period is too large to be displayed (the server didn’t retrieve the analytics because the user request is for a period that is too large).
        503        Analytics not available yet (the requested information is not computed yet for an application).
        504        The server was not able to handle your request in a reasonable time (if you make multiple calls to an API very quickly, try to make one call at a time and spread the calls out over time).

### <a name="see-also"></a>Vea también

- [Documentación de la API - errores detallados de cada API específica][Link 4]
 
## <a name="silent-failures"></a>Errores sin

### <a name="issue"></a>Problema
- Se produce un error en la acción API con ningún mensaje de error que se muestra en tiempo de ejecución o en los registros.

### <a name="causes"></a>Causas

- Muchos elementos se deshabilitarán en la interfaz de usuario de Azure Mobile compromiso si no se integra en correctamente, pero se un error de la API, así que no olvide probar la misma funcionalidad de la interfaz de usuario para ver si funciona.
- Compromiso de Azure Mobile y muchas características avanzadas de Azure Mobile contratación está intentando usar, necesitan individualmente se integren en la aplicación con el SDK como pasos independientes antes de que puede usarlos.

### <a name="see-also"></a>Vea también

- [Solución de problemas - Guía de SDK][Link 25]
 
<!--Link references-->
[Link 1]: mobile-engagement-user-interface-home.md
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
 
