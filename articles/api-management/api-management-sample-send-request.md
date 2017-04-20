<properties
    pageTitle="Mediante el servicio de administración de API para generar solicitudes HTTP"
    description="Aprenda a usar directivas de convocatoria y respuesta en administración de la API para llamar a los servicios externos de la API"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="darrmi"/>


# <a name="using-external-services-from-the-azure-api-management-service"></a>Usar servicios externos desde el servicio de administración de la API de Azure

Las directivas disponibles en el servicio de administración de la API de Azure pueden hacer una amplia gama de trabajo útil puramente según la solicitud entrante, la respuesta saliente y la información de configuración básica. Sin embargo, para poder interactuar con los servicios externos de la API de administración de directivas abre muchas más posibilidades.

Hemos visto anteriormente cómo podemos interactuar con el [servicio de Azure evento concentrador para registro, supervisión y análisis](api-management-log-to-eventhub-sample.md). En este artículo le mostramos servicio basado en directivas que le permiten interactuar con cualquier HTTP externo. Estas directivas pueden utilizarse para desencadenar eventos remotos o para recuperar información que se va a utilizar para manipular la solicitud y la respuesta de manera original.

## <a name="send-one-way-request"></a>Enviar una manera de solicitud
Posiblemente la interacción externa más sencilla es el estilo fire y olvidarse de solicitud que permite a un servicio externo recibir una notificación de algún tipo de evento importante. Podemos utilizar la directiva de flujo de control `choose` para detectar cualquier tipo de condición que le interesa y, a continuación, si la condición se cumple, nos podemos realizar una solicitud HTTP externa con la directiva de [Enviar una manera de solicitud](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) . Esto puede deberse a una solicitud de un sistema de mensajería como Hipchat o demora o un correo API como SendGrid o MailChimp o para incidencias de soporte técnico crítica haga algo parecido a PagerDuty. Todos estos sistemas de mensajería tienen simple HTTP APIs que podemos llamar fácilmente.

### <a name="alerting-with-slack"></a>Alerta de margen de demora
En el ejemplo siguiente se muestra cómo enviar un mensaje a un salón de chat demora si el código de estado de respuesta HTTP es mayor o igual a 500. Un error de 500 intervalo indica un problema con nuestro back-end API que no puede resolver el cliente de nuestra API de sí mismos. Normalmente se requiere algún tipo de intervención de nuestra parte.  

    <choose>
        <when condition="@(context.Response.StatusCode >= 500)">
          <send-one-way-request mode="new">
            <set-url>https://hooks.slack.com/services/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</set-url>
            <set-method>POST</set-method>
            <set-body>@{
                    return new JObject(
                            new JProperty("username","APIM Alert"),
                            new JProperty("icon_emoji", ":ghost:"),
                            new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                    context.Request.Method,
                                                    context.Request.Url.Path + context.Request.Url.QueryString,
                                                    context.Request.Url.Host,
                                                    context.Response.StatusCode,
                                                    context.Response.StatusReason,
                                                    context.User.Email
                                                    ))
                            ).ToString();
                }</set-body>
          </send-one-way-request>
        </when>
    </choose>

Margen de demora tiene el concepto de enlaces web entrantes. Al configurar un enlace de web entrantes, margen de demora genera una dirección URL especial que permite hacer una solicitud POST simple y pasar de un mensaje en el canal de margen de demora. El cuerpo JSON que creamos se basa en un formato definido por el margen de demora.

![Enlace de margen de demora Web](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>¿Es fire y olvide lo suficientemente buena?
Hay ciertos compensaciones al usar un estilo fire y olvidarse de solicitud. Si por algún motivo, se produce un error en la solicitud y luego el error no se mostrarán. En esta situación particular, no garantiza la complejidad de tener un error secundario informes de sistema y el costo de rendimiento adicional de espera de la respuesta. A continuación, en escenarios donde es fundamental para comprobar la respuesta, la directiva de [solicitud de envío](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) es una opción mejor.

## <a name="send-request"></a>Enviar solicitud
La `send-request` directiva habilita el uso de un servicio externo para realizar las funciones de procesamiento complejo y devolver datos a la administración de la API del servicio que puede usarse para el procesamiento de directiva adicional.

### <a name="authorizing-reference-tokens"></a>Autorizar tokens de referencia
Función principal de administración de la API protege los recursos de back-end. Si el servidor de autorización usado por la API crea los [tokens JWT](http://jwt.io/) como parte de su flujo de OAuth2 a como sucede en [Azure Active Directory](../active-directory/active-directory-aadconnect.md) , puede utilizar el `validate-jwt` directiva para comprobar la validez del token. Sin embargo, algunos servidores de autorización crean lo que se denomina [referencia tokens](http://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) que no se puede comprobar sin realizar una llamada al servidor de autorización.

### <a name="standardized-introspection"></a>Introspección normalizado
En el pasado no ha habido ninguna manera estándar de comprobar un símbolo de referencia con un servidor de autorización. Sin embargo una propuesta recientemente estándar [RFC 7662](https://tools.ietf.org/html/rfc7662) publicó IETF que define cómo un servidor de recursos puede comprobar la validez de un token.

### <a name="extracting-the-token"></a>Extraer el token
El primer paso es extraer el token en el encabezado de autorización. El valor de encabezado debe tener el formato con el `Bearer` esquema de autorización, un espacio y, a continuación, el token de autorización según [RFC 6750](http://tools.ietf.org/html/rfc6750#section-2.1). Desgraciadamente, hay casos donde se omite el esquema de autorización. Para la cuenta al analizar, estamos dividir el valor de encabezado en un espacio y seleccione la última cadena en la matriz devuelta de cadenas. Proporciona una solución para los encabezados de autorización con formato incorrecto.

    <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

### <a name="making-the-validation-request"></a>Realizar la solicitud de validación
Una vez que tenemos el token de autorización, podemos hacer que la solicitud para validar el token. RFC 7662 llama introspección de este proceso y requiere que se `POST` un formulario HTML para el recurso introspección. El formulario HTML debe contener al menos un par de clave/valor con la clave `token`. También se debe autenticar esta solicitud en el servidor de autorización para asegurarse de que no se vaya podrá clientes malintencionados para tokens válidas.

    <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
      <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
      <set-method>POST</set-method>
      <set-header name="Authorization" exists-action="override">
        <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
      </set-header>
      <set-header name="Content-Type" exists-action="override">
        <value>application/x-www-form-urlencoded</value>
      </set-header>
      <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
    </send-request>

### <a name="checking-the-response"></a>Comprobación de la respuesta
La `response-variable-name` atributo se utiliza para conceder acceso a la respuesta devuelta. El nombre definido en esta propiedad se puede utilizar como una clave en el `context.Variables` diccionario para tener acceso a la `IResponse` objeto.

Podemos recuperar el cuerpo del objeto de respuesta y RFC 7622 nos indica que la respuesta debe ser un objeto JSON y debe contener al menos una propiedad denominada `active` es un valor boolean. Cuando `active` es true, a continuación, el token se considera válido.

### <a name="reporting-failure"></a>Error de generación de informes
Usamos una `<choose>` directiva para detectar si el token es válido y si es así, devolver una respuesta 401.

    <choose>
      <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
        <return-response response-variable-name="existing response variable">
          <set-status code="401" reason="Unauthorized" />
          <set-header name="WWW-Authenticate" exists-action="override">
            <value>Bearer error="invalid_token"</value>
          </set-header>
        </return-response>
      </when>
    </choose>

Según [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) que describe cómo `bearer` se deben usar tokens, también devolver un `WWW-Authenticate` encabezado con la respuesta 401. La autenticación WWW está pensado para indicar un cliente acerca de cómo crear una solicitud correctamente autorizada. Debido a la gran variedad de enfoques posibles con el marco de trabajo OAuth2, es difícil comunicarse toda la información necesaria. Afortunadamente hay esfuerzos sencillo para ayudar a [los clientes descubrir cómo correctamente autorizar solicitudes a un servidor de recursos](http://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Solución final
Unión de todas las partes, obtenemos las siguientes directivas:

    <inbound>
      <!-- Extract Token from Authorization header parameter -->
      <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

      <!-- Send request to Token Server to validate token (see RFC 7662) -->
      <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
        <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
        <set-method>POST</set-method>
        <set-header name="Authorization" exists-action="override">
          <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
        </set-header>
        <set-header name="Content-Type" exists-action="override">
          <value>application/x-www-form-urlencoded</value>
        </set-header>
        <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
      </send-request>

      <choose>
            <!-- Check active property in response -->
            <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
                <!-- Return 401 Unauthorized with http-problem payload -->
                <return-response response-variable-name="existing response variable">
                    <set-status code="401" reason="Unauthorized" />
                    <set-header name="WWW-Authenticate" exists-action="override">
                        <value>Bearer error="invalid_token"</value>
                    </set-header>
                </return-response>
            </when>
        </choose>
      <base />
    </inbound>

Esto es solo uno de varios ejemplos de cómo el `send-request` directiva puede usarse para integrar útiles servicios externos en el proceso de las convocatorias y respuestas que fluye a través del servicio de administración de la API.

## <a name="response-composition"></a>Composición de respuesta
La `send-request` directiva se puede utilizar para mejorar una solicitud principal a un sistema back-end, como hemos visto en el ejemplo anterior, o se puede utilizar como un reemplazo completo para de la llamada de back-end. Usar esta técnica podemos crear fácilmente compuestos recursos que se agregan de varios sistemas diferentes.

### <a name="building-a-dashboard"></a>Creación de un panel   
A veces desea poder para exponer información que existe en varios sistemas de back-end, por ejemplo, para dirigir un panel. Los KPI proceden de todos los diferentes back-end, pero no desea proporcionar acceso directo a ellos y estaría bien si se puede recuperar toda la información en una sola solicitud. Tal vez algunos de los datos de back-end necesita algunas divisiones y fragmentar y saneamiento un poco en primer lugar! Posibilidad de caché ese recurso compuesto sería útil reducir la carga de back-end como saber los usuarios tienen costumbre de hammering la tecla F5 para ver si se pueden cambiar sus métricas underperforming.    

### <a name="faking-the-resource"></a>Falsificación del recurso
El primer paso para crear nuestro recurso paneles es configurar una tarea nueva en el portal de administración de la API de publisher. Se trata de una operación de marcador de posición que se usa para configurar nuestra directiva de composición para generar nuestro recurso dinámico.

![Operación de paneles](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Hacer que las solicitudes
Una vez el `dashboard` se ha creado la operación podemos configurar una directiva específicamente para esa operación. 

![Operación de paneles](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

El primer paso es para extraer los parámetros de consulta de la solicitud entrante, por lo que nos podemos reenviar a nuestra back-end. En este ejemplo nuestros paneles muestran información basada en un período de tiempo una, por tanto, tiene una `fromDate` y `toDate` parámetro. Podemos utilizar la `set-variable` directiva para extraer la información de la dirección URL de la solicitud.

    <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
    <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

Una vez que tenemos esta información que podemos hacer solicitudes en todos los sistemas de back-end. Cada solicitud crea una nueva dirección URL con la información de parámetros y llamadas de su servidor respectivo y almacena la respuesta en una variable de contexto.

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

Estas solicitudes se ejecutarán en secuencia, que no es ideal. En una versión próxima se presentan una nueva política denominada `wait` que le permitirá todas estas solicitudes para ejecutar en paralelo.

### <a name="responding"></a>Responder

Para crear la respuesta compuesta podemos usar la directiva de [respuesta de retorno](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) . La `set-body` elemento puede usar una expresión para crear un nuevo `JObject` con todas las representaciones de componente incrustadas como propiedades.

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>

La directiva completa tiene el siguiente aspecto:

    <policies>
        <inbound>

      <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
      <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

        <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
          <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
          <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <return-response response-variable-name="existing response variable">
          <set-status code="200" reason="OK" />
          <set-header name="Content-Type" exists-action="override">
            <value>application/json</value>
          </set-header>
          <set-body>
            @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                          new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                          new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                          new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                          ).ToString())
          </set-body>
        </return-response>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
    </policies>

En la configuración del marcador de posición operación que podemos configurar el recurso de panel en caché para al menos una hora porque comprendemos la naturaleza de los datos significa que incluso si es un valor de hora no está actualizado, seguirá estando suficientemente eficaz transmitir información valiosa para los usuarios.

## <a name="summary"></a>Resumen
Servicio de administración de la API de Azure proporciona directivas flexibles que se pueden aplicar de forma selectiva para el tráfico HTTP y permite composición de los servicios de back-end. Si desea mejorar la puerta de enlace API con las alertas de funciones, comprobación, capacidades de validación o crear nuevos recursos compuestos basados en varios servicios de back-end, el `send-request` y directivas relacionadas abrir un mundo de posibilidades.

## <a name="watch-a-video-overview-of-these-policies"></a>Ver un vídeo de introducción de estas directivas
Para obtener más información sobre el [envío una manera de solicitud](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest), [Enviar solicitud](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest)y directivas de [respuesta de retorno](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) de este artículo, por favor, vea el siguiente vídeo.

> [AZURE.VIDEO send-request-and-return-response-policies]
