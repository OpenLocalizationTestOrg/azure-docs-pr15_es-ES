<properties
    pageTitle="Caché personalizado en administración de la API de Azure"
    description="Obtenga información sobre cómo almacenar en caché elementos clave en la administración de la API de Azure"
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

# <a name="custom-caching-in-azure-api-management"></a>Caché personalizado en administración de la API de Azure
Servicio de administración de la API de Azure tiene compatibilidad integrada para [el almacenamiento en caché de la respuesta HTTP](api-management-howto-cache.md) utilizando la URL como clave. Puede modificar la clave con los encabezados de solicitud la `vary-by` propiedades. Esto es útil para el almacenamiento en caché de respuestas HTTP completas (también conocido como representaciones), pero a veces es útil en caché solo una parte de una representación. Las directivas de [caché valor de búsqueda](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) y el [valor de la tienda de caché](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) nuevas proporcionan la capacidad de almacenar y recuperar arbitrarios segmentos de datos desde dentro de definiciones de directiva. Esta capacidad también agrega valor a la directiva de [solicitud de envío](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) de previamente introducidos porque ahora puede almacenar en caché las respuestas de servicios externos.

## <a name="architecture"></a>Arquitectura  
Servicio de administración de la API utiliza una caché de datos compartido por inquilino que, como escala hasta varias unidades aún obtendrá acceso a los mismos datos en caché. Sin embargo, cuando se trabaja con una implementación de múltiples región hay caché independientes dentro de cada una de las regiones. Debido a esto, es importante no considerar la memoria caché como un almacén de datos sea la única fuente de algún dato de la información. Si hizo y decide más adelante aprovechar las ventajas de la implementación de múltiples región, los clientes con los usuarios que viajan pueden perder el acceso a los datos almacenados en caché.

## <a name="fragment-caching"></a>Almacenamiento en caché
Hay algunos casos en los que las respuestas que se devuelven contienen una parte de datos que se consume determinar y aún permanece actualizado durante un período de tiempo razonable. Por ejemplo, considere la posibilidad de un servicio creado por una línea aérea que proporciona información relacionada reservas de vuelos, estado de vuelo, etcetera. Si el usuario es miembro del programa de puntos de líneas aéreas, también tendría información relativa a su estado actual y kilometraje acumulado. Esta información relacionada con el usuario se podría almacenar en un sistema diferente, pero puede ser conveniente para incluir en las respuestas devueltas sobre reservas y estado de vuelo. Esto puede hacerse mediante un proceso denominado almacenamiento en caché. Puede devolver la representación principal desde el servidor de origen con algún tipo de token para indicar dónde está la información relacionada con el usuario que desea insertar. 

Tenga en cuenta la siguiente respuesta JSON desde una API de back-end.


    {
      "airline" : "Air Canada",
      "flightno" : "871",
      "status" : "ontime",
      "gate" : "B40",
      "terminal" : "2A",
      "userprofile" : "$userprofile$"
    }  

Y recursos secundarios en `/userprofile/{userid}` que parece,

     { "username" : "Bob Smith", "Status" : "Gold" }

Para determinar la información de usuario apropiadas para incluir, debemos identificar quién es el usuario final. Este mecanismo es depende de la implementación. Por ejemplo, estoy usando la `Subject` Reclamación de un `JWT` token. 

    <set-variable
      name="enduserid"
      value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

Nos almacenar esta `enduserid` valor de una variable de contexto para su uso posterior. El siguiente paso es determinar si una solicitud anterior ya recuperen la información de usuario y almacenados en caché. Para ello, usamos la `cache-lookup-value` directiva.

      <cache-lookup-value
      key="@("userprofile-" + context.Variables["enduserid"])"
      variable-name="userprofile" />

Si no hay ninguna entrada en la caché que corresponde al valor de clave, entonces no `userprofile` variable de contexto se creará. Se comprueba el éxito de la búsqueda utilizando la `choose` directiva de flujo de control.

    <choose>
        <when condition="@(!context.Variables.ContainsKey("userprofile"))">
            <!— If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
        </when>
    </choose>


Si la `userprofile` no existe la variable de contexto, a continuación, vamos a tener que realizar una solicitud HTTP para recuperarla.

    <send-request
      mode="new"
      response-variable-name="userprofileresponse"
      timeout="10"
      ignore-error="true">

      <!-- Build a URL that points to the profile for the current end-user -->
      <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
          (string)context.Variables["enduserid"]).AbsoluteUri)
      </set-url>
      <set-method>GET</set-method>
    </send-request>

Usamos el `enduserid` para crear la dirección URL para el recurso de perfil de usuario. Una vez que tenemos la respuesta, podemos extraer el texto del cuerpo de la respuesta y almacenar en una variable de contexto.

    <set-variable
        name="userprofile"
        value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

Para evitar nos tiene que realizar esta solicitud HTTP nuevo, cuando el mismo usuario realiza otra solicitud, podemos almacenar el perfil de usuario en la memoria caché.

    <cache-store-value
        key="@("userprofile-" + context.Variables["enduserid"])"
        value="@((string)context.Variables["userprofile"])" duration="100000" />

El valor se almacena en la caché con la misma clave que intentamos originalmente para recuperarla con. La duración que se elija para almacenar el valor depende de cómo los usuarios cómo tolerancia a errores y los cambios de la información suelen información al día. 

Es importante tener en cuenta que recuperar de la caché sigue siendo un fuera de proceso, la solicitud de red y potencialmente puede agregar decenas de milisegundos a la solicitud. Las ventajas proceden al determinar que la información de perfil de usuario tarda mucho más que debido a la necesidad de base de datos de consultas o agregar información de fondo varios.

El último paso del proceso es actualizar la respuesta devuelta con nuestra información de perfil de usuario.

    <!—Update response body with user profile-->
    <find-and-replace
        from='"$userprofile$"'
        to="@((string)context.Variables["userprofile"])" />

Elegí incluir las comillas como parte del token de modo que incluso cuando no se produce el reemplazar, la respuesta fue JSON siendo válido. Esto era principalmente facilitar la depuración.

Una vez combinar juntos todos estos pasos, el resultado final es una directiva que tiene un aspecto como el siguiente.

     <policies>
        <inbound>
            <!-- How you determine user identity is application dependent -->
            <set-variable
              name="enduserid"
              value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

            <!--Look for userprofile for this user in the cache -->
            <cache-lookup-value
              key="@("userprofile-" + context.Variables["enduserid"])"
              variable-name="userprofile" />

            <!-- If we don’t find it in the cache, make a request for it and store it -->
            <choose>
                <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                    <!—Make HTTP request to get user profile -->
                    <send-request
                      mode="new"
                      response-variable-name="userprofileresponse"
                      timeout="10"
                      ignore-error="true">

                       <!-- Build a URL that points to the profile for the current end-user -->
                        <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                        <set-method>GET</set-method>
                    </send-request>

                    <!—Store response body in context variable -->
                    <set-variable
                      name="userprofile"
                      value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                    <!—Store result in cache -->
                    <cache-store-value
                      key="@("userprofile-" + context.Variables["enduserid"])"
                      value="@((string)context.Variables["userprofile"])"
                      duration="100000" />
                </when>
            </choose>
            <base />
        </inbound>
        <outbound>
            <!—Update response body with user profile-->
            <find-and-replace
                  from='"$userprofile$"'
                  to="@((string)context.Variables["userprofile"])" />
            <base />
        </outbound>
     </policies>

Este método de almacenamiento en caché se utiliza principalmente en los sitios web donde se compone HTML en el servidor para que se puede representar como una sola página. Sin embargo, también puede ser útil en el lado de API donde los clientes no es cliente de almacenamiento en caché HTTP o es conveniente no colocar esa responsabilidad del cliente.

También se puede realizar en este mismo tipo de almacenamiento en caché de los servidores web de back-end con un servidor de caché Redis, sin embargo, usando el servicio de administración de la API para realizar esta tarea es útil cuando se proceden de los fragmentos de caché de fondo diferente que las respuestas principales.

## <a name="transparent-versioning"></a>Control de versiones transparente
Es muy común para varias versiones de implementación diferente de una API que sea compatible con en cualquier momento. Esta es tal vez para admitir diferentes entornos, como desarrollo, prueba, producción, etcetera, o puede resultar compatibilidad con versiones anteriores de la API para dar tiempo para los consumidores de API migrar a las versiones más recientes. 

Un enfoque de control en lugar de requerir que los programadores de cliente cambiar las direcciones URL de `/v1/customers` a `/v2/customers` es almacenar en los datos de perfil del consumidor qué versión de la API que actualmente desean utilizar y la dirección URL de back-end adecuados de llamadas. Para determinar la URL correcta back-end de llamadas para un cliente en particular, es necesario para algunos datos de configuración de la consulta. Almacenamiento en caché estos datos de configuración, podemos minimizar la reducción del rendimiento de realizar esta búsqueda.

El primer paso es determinar el identificador que se usa para configurar la versión deseada. En este ejemplo, que elegí asociar la versión de la clave del producto suscripción. 

        <set-variable name="clientid" value="@(context.Subscription.Key)" />

A continuación, se realice una búsqueda en la caché para ver si ya hemos recuperado la versión de cliente que desee.

        <cache-lookup-value
        key="@("clientversion-" + context.Variables["clientid"])"
        variable-name="clientversion" />

A continuación, se comprueba si no se encuentra, en la memoria caché.

    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">

Si no la encontramos, a continuación, vamos y recuperarla.

    <send-request
        mode="new"
        response-variable-name="clientconfiguresponse"
        timeout="10"
        ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
    </send-request>

Extrae el texto de cuerpo de respuesta de la respuesta.

    <set-variable
          name="clientversion"
          value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />

Guardarla en la memoria caché para usos futuros.

    <cache-store-value
          key="@("clientversion-" + context.Variables["clientid"])"
          value="@((string)context.Variables["clientversion"])"
          duration="100000" />

Y finalmente actualizar la dirección URL de back-end para seleccionar la versión del servicio deseado por el cliente.

    <set-backend-service
          base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />

La directiva completamente es la siguiente.

     <inbound>
        <base />
        <set-variable name="clientid" value="@(context.Subscription.Key)" />
        <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

        <!-- If we don’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("clientversion"))">
                <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                    <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>
                <!-- Store response body in context variable -->
                <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
                <!-- Store result in cache -->
                <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
            </when>
        </choose>
        <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
    </inbound>


Lo que permite a los consumidores de la API transparente controlar qué versión de back-end está teniendo acceso a los clientes sin tener que actualizar y volver a los clientes es una solución elegante que resuelve muchos problemas de control de versiones de API.

## <a name="tenant-isolation"></a>Aislamiento de inquilinos

En implementaciones de múltiples arrendatarios, mayores algunas compañías crear distintos grupos de inquilinos de distintas implementaciones de hardware de back-end. Esto minimiza el número de clientes que se ven afectados por un problema de hardware en el servidor. También permite nuevas versiones de software para realizarse en fases. Lo ideal es que esta arquitectura de back-end debería ser transparente a los consumidores de la API. Esto se puede conseguir de forma similar al control de versiones transparente porque se basa en la misma técnica de manipulación de la dirección URL de back-end mediante el estado de la configuración de claves API por.  

En lugar de devolver una versión preferida de la API para cada clave de la suscripción, debe devolver un identificador que hace referencia a un inquilino al grupo de hardware asignado. Ese identificador puede utilizarse para crear la dirección URL de back-end adecuados.

## <a name="summary"></a>Resumen
Libertad para utilizar la caché de administración de la API de Azure para almacenar cualquier tipo de datos habilita un acceso eficaz a los datos de configuración que pueden afectar al modo en que se procesa una solicitud entrante. También puede usarse para almacenar fragmentos de datos que pueden aumentar las respuestas, devueltos por un API de back-end.

## <a name="next-steps"></a>Pasos siguientes
Por favor, envíenos sus comentarios en el subproceso Disqus para este tema si existen otros escenarios que se han habilitado estas directivas para usted, o si hay situaciones que le gustaría lograr, pero no sentir no existen actualmente.
