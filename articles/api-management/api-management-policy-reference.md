<properties 
    pageTitle="Referencia de directiva de administración de API de Azure" 
    description="Obtenga información sobre las directivas disponibles para configurar la administración de la API." 
    services="api-management" 
    documentationCenter="" 
    authors="vladvino" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="apimpm"/>

# <a name="azure-api-management-policy-reference"></a>Referencia de directiva de administración de API de Azure

Esta sección proporciona un índice para las directivas de la [referencia de la directiva de administración de la API][]. Para obtener información sobre cómo agregar y configurar las directivas, consulte [directivas de administración de la API][].

Las expresiones de directiva se pueden utilizar como valores de atributo o valores de texto en cualquiera de las directivas de administración de la API, a menos que la directiva especifica lo contrario. Algunas directivas, como las directivas de [flujo de Control][] y [establezca variable][] se basan en las expresiones de directiva. Para obtener más información, vea [expresiones de directiva][] y [Directivas avanzadas][]

## <a name="policy-reference-index"></a>Índice de referencia de directiva

-   [Directivas de restricción de acceso][]
    -   [Encabezado HTTP Active][] - exige la existencia o el valor de un encabezado HTTP.
    -   [Tasa de llamada límite por suscripción][] : uso de la API impide picos limitando la tasa de llamada, por la suscripción.
    -   [Tasa de llamada límite clave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) : uso de la API impide picos limitando la tasa de llamada, en una base por clave.
    -   [Restringir llamador direcciones IP][] - filtros (permite o rechaza) llamadas de direcciones IP específicas o intervalos de direcciones.
    -   [Establecer la cuota de uso de suscripción][] - le permite exigir una ampliar ni duración llamada volumen y ancho de banda de cuota, por la suscripción.
    -   [Establecer la cuota de uso de clave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) : le permite exigir una ampliar ni duración llamada volumen y ancho de banda de cuota, en una base de clave por.
    -   [Validar JWT][] - aplica la existencia y validez de una JWT extraído de un encabezado HTTP especificado o un parámetro de consulta especificado.
-   [Directivas avanzadas][]
    -   [Flujo de control][] - condicionalmente se aplica a las instrucciones de directiva basadas en los resultados de la evaluación de las [expresiones][]booleanas.
    -   [Envíe la solicitud][] - reenvía la solicitud al servicio de back-end.
    -   [Registro de eventos concentrador][] - envía mensajes en el formato especificado a un mensaje de destino definido por una entidad de [registrador](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger) .
    -   [Vuelva a intentar](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry) - reintentos de ejecución de las instrucciones de directiva adjunto, si y hasta que se cumpla la condición. Ejecución se repita en los intervalos de tiempo especificado y número de reintentos hasta especificado.
    -   [Devolver respuesta](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) - cancelaciones de ejecución de canalización y devuelve la respuesta especificada directamente a la persona que llama.
    -   [Enviar solicitud de una forma](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) - envía una solicitud a la dirección URL especificada sin tener que esperar una respuesta.
    -   [Enviar solicitud](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) - envía una solicitud a la dirección URL especificada.
    -   [Método de solicitud de set](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) - le permite cambiar el método HTTP para una solicitud.
    -   [Establecer estado](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) - cambia el código de estado HTTP en el valor especificado.
    -   [Establecer variable][] - conservar un valor en una variable con nombre [contexto][] para el acceso posterior.
    -   [Seguimiento](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace) - agrega una cadena en el resultado de la [API Inspector](../api-management/api-management-howto-api-inspector.md) .
    -   [Espere](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) - espera adjunto enviar solicitud, obtener el valor de caché, o directivas de flujo de Control para completar antes de continuar.
-   [Directivas de autenticación][]
    -   [Autenticar con Basic][] - autenticar con un servicio de back-end mediante la autenticación básica.
    -   [Autenticar con certificado de cliente][] - autenticar con un servicio de back-end con los certificados de cliente.
-   [Directivas de almacenamiento en caché][] 
    -   [Obtener de caché][] - realizar caché buscar y devolver una respuesta válida en caché cuando esté disponible.
    -   [Almacenamiento en caché][] , respuesta de la caché según la configuración de control de caché especificada.
    -   [Obtener el valor de caché](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) - recuperar un elemento en caché por clave.
    -   [Valor de almacenamiento en caché](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) : almacén de un elemento de la caché por clave.
    -   [Quitar el valor de caché](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey) - quitar un elemento de la caché por clave.
-   [Entre las directivas de dominio][] 
    -   [Permitir llamadas de dominios][] - hace que la API accesible desde clientes basados en explorador Adobe Flash y Microsoft Silverlight.
    -   [CORS][] - agrega entre origen de recursos compartidos de soporte técnico (CORS) a una operación o una API para permitir las llamadas entre dominios desde clientes basados en explorador.
    -   [JSONP][] - agrega JSON con relleno (JSONP) al soporte técnico para una operación o una API para permitir las llamadas entre dominios de clientes basada en Explorador de JavaScript.
-   [Directivas de transformación][] 
    -   [Convertir JSON a XML][] - convierte solicitud o respuesta del cuerpo de JSON a XML.
    -   [Convertir XML a JSON][] - convierte solicitud o respuesta del cuerpo XML a JSON.
    -   [Buscar y reemplazar la cadena en el cuerpo][] : encuentra una subcadena solicitud o respuesta y lo sustituye por una subcadena diferente.
    -   [Direcciones URL de máscara contenido][] - vínculos volver a escribe (máscaras) en la respuesta del cuerpo para que señalen al vínculo equivalente a través de la puerta de enlace.
    -   [Configurar el servicio de back-end][] - cambia el servicio de back-end para una solicitud entrante.
    -   [Establecer cuerpo][] - establece el cuerpo del mensaje para las solicitudes entrantes y salientes.
    -   [Establecer encabezado][] - asigna un valor a una respuesta existente o un encabezado de la solicitud o agrega un encabezado de respuesta o solicitud de nuevo.
    -   [Establecer el parámetro de cadena de consulta][] - agrega valor reemplaza o elimina solicitar el parámetro de cadena de consulta.
    -   [Reescritura de URL][] - convierte una dirección URL de la solicitud de su formulario público al formulario esperado por el servicio web.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre las expresiones de directiva, vea el siguiente vídeo.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Directivas de restricción de acceso]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Comprobar el encabezado HTTP]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Tasa de llamada límite por suscripción]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Restringir el llamador direcciones IP]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Establecer la cuota de uso por suscripción]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Validar JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Directivas avanzadas]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Flujo de control]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Establecer variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[expresiones]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[contexto]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Envíe la solicitud]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Registro de concentrador de evento]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Directivas de autenticación]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Autenticar con Basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Autenticar con certificado de cliente]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Directivas de almacenamiento en caché]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Obtener de caché]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Almacenar en caché]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Entre las directivas de dominio]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Permitir llamadas entre dominios]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Directivas de transformación]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Convertir JSON en XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Convertir XML en JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Buscar y reemplazar cadena en cuerpo]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Direcciones URL de la máscara de contenido]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Servicio de back-end de conjunto]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Conjunto de cuerpo]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Definir el encabezado HTTP]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Parámetro de cadena de consulta de conjunto]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[Reescritura de URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Directivas de administración de la API de]: api-management-howto-policies.md
[Referencia de la directiva de administración de la API]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Expresiones de directiva]: https://msdn.microsoft.com/library/azure/dn910913.aspx

 
