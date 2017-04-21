<properties
    pageTitle="Compatibilidad de uso compartido (CORS) de recursos entre origen | Microsoft Azure"
    description="Obtenga información sobre cómo habilitar el soporte de CORS para los servicios de almacenamiento de Microsoft Azure."
    services="storage"
    documentationCenter=".net"
    authors="cbrooks"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="cbrooks"/>

# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Recursos de origen entre uso compartido de soporte técnico (CORS) para los servicios de almacenamiento de Azure

A partir de la versión 2013-08-15, los servicios de almacenamiento de Azure admiten el uso compartido de recursos entre origen (CORS) para los servicios de blobs, tabla, cola y archivo. CORS es una característica HTTP que permite a una aplicación de web que se ejecuta en un dominio tener acceso a recursos de otro dominio. Exploradores Web implementan una restricción de seguridad que se conoce como [Directiva de mismo origen](http://www.w3.org/Security/wiki/Same_Origin_Policy) que impide que una página web desde las API de llamada en un dominio diferente; CORS ofrece una manera segura para permitir que un dominio (el dominio de origen) llamar a las API de otro dominio. Vea la [especificación de CORS](http://www.w3.org/TR/cors/) para obtener más información sobre CORS.

Puede establecer reglas CORS individualmente para cada uno de los servicios de almacenamiento, llamando al [Establecer propiedades de servicio de blobs](https://msdn.microsoft.com/library/hh452235.aspx), [Establecer propiedades del servicio de cola](https://msdn.microsoft.com/library/hh452232.aspx)y [Establecer propiedades de servicio de la tabla](https://msdn.microsoft.com/library/hh452240.aspx). Cuando la configuración de las reglas CORS para el servicio se evaluará para determinar si se permite según las reglas que haya especificado una solicitud autenticada correctamente con el servicio de un dominio diferente.

>[AZURE.NOTE] Tenga en cuenta que CORS no es un mecanismo de autenticación. Cualquier solicitud realizada en un recurso de almacenamiento cuando se habilita CORS debe tener una firma de autenticación apropiado o debe realizarse en un recurso público.

## <a name="understanding-cors-requests"></a>Descripción de las solicitudes CORS

Una solicitud de CORS de un dominio de origen puede constar de dos solicitudes distintas:

- Una solicitud de comprobaciones, que consulta las restricciones CORS impuestas por el servicio. A menos que el método de solicitud es un [método sencillo](http://www.w3.org/TR/cors/), es decir, GET, cabeza o publicación, se requiere la solicitud de comprobaciones.

- La solicitud real realizada con el recurso que desee.

### <a name="preflight-request"></a>Solicitud de comprobaciones

Las consultas de solicitud de comprobaciones las restricciones de CORS que se han establecido por el propietario de la cuenta del servicio de almacenamiento. El explorador web (u otro agente de usuario) envía una solicitud de opciones que incluye los encabezados de la solicitud, dominio método y origen. El servicio de almacenamiento evalúa la operación basándose en un conjunto de reglas CORS que especifique qué dominios de origen, métodos de solicitud y los encabezados de solicitud pueden especificarse en una solicitud real en un recurso de almacenamiento configurado previamente.

Si CORS está habilitada para el servicio y hay una regla CORS que coincida con la solicitud de comprobaciones, el servicio responde con código de estado 200 (Aceptar) e incluye los encabezados de Control de acceso necesarios en la respuesta.

Si CORS no está habilitado para el servicio o ninguna regla CORS coincide con la solicitud de comprobaciones, el servicio responde con código de estado 403 (prohibido).

Si la solicitud de opciones no contiene los encabezados CORS necesarios (los encabezados de origen y Access Control solicitud método), el servicio responde con código de estado 400 (solicitud incorrecta).

Tenga en cuenta que se evalúa una solicitud de comprobaciones con el servicio (Blob, colas y tabla) y no con el recurso solicitado. El propietario de la cuenta debe habilitar CORS como parte de las propiedades del servicio cuenta en orden de la solicitud correctamente.

### <a name="actual-request"></a>Solicitud real

Una vez que se acepte la solicitud de comprobaciones y devuelve la respuesta, el explorador enviará la solicitud real con el recurso de almacenamiento. El explorador denegar real solicitar inmediatamente si se rechaza la solicitud de comprobación.

La solicitud se trata como solicitud normal con el servicio de almacenamiento. La presencia del encabezado de origen indica que la solicitud es una solicitud CORS y el servicio deberá comprobar las reglas CORS coincidentes. Si se encuentra una coincidencia, los encabezados de Control de acceso se agregará a la respuesta y enviados al cliente. Si no encuentra una coincidencia, no se devuelven los encabezados de Control de acceso CORS.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Habilitar CORS para los servicios de almacenamiento de Azure

Reglas CORS se establecen en el nivel de servicio, por lo que necesita habilitar o deshabilitar CORS para cada servicio (Blob, cola y tabla) por separado. CORS está deshabilitado de forma predeterminada, para cada servicio. Para habilitar CORS, debe establecer las propiedades de servicio adecuado con la versión 2013-08-15 o versiones posteriores y agregar reglas CORS a las propiedades del servicio. Para obtener más información sobre cómo habilitar o deshabilitar CORS de un servicio y cómo definir reglas CORS, consulte [Configurar propiedades del servicio de blobs](https://msdn.microsoft.com/library/hh452235.aspx), [Establecer propiedades del servicio de cola](https://msdn.microsoft.com/library/hh452232.aspx)y [Establecer propiedades de servicio de la tabla](https://msdn.microsoft.com/library/hh452240.aspx).

Aquí es un ejemplo de una sola regla CORS especificado a través de una operación de establecer propiedades de servicio:

    <Cors>    
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
            <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
            <MaxAgeInSeconds>200</MaxAgeInSeconds>
        </CorsRule>
    <Cors>

Cada elemento que se incluyen en la regla CORS se describe a continuación:

- **AllowedOrigins**: los dominios de origen que tienen permiso para realizar una solicitud con el servicio de almacenamiento a través de CORS. El dominio de origen es el dominio del que se origina la solicitud. Observe que el origen debe ser una coincidencia exacta entre mayúsculas y minúsculas con el origen de la edad de usuario se envía al servicio. También puede usar el carácter comodín ' *' para permitir que todos los dominios de origen realizar solicitudes a través de CORS. En el ejemplo anterior, los dominios [http://www.contoso.com](http://www.contoso.com) y [http://www.fabrikam.com](http://www.fabrikam.com) pueden realizar solicitudes con el servicio de CORS.

- **AllowedMethods**: los métodos (verbos de solicitud HTTP) que puede utilizar el dominio de origen para una solicitud CORS. En el ejemplo anterior, se permiten solo las solicitudes de obtener y colocar.

- **AllowedHeaders**: los encabezados de solicitud que puede especificar el dominio de origen en la solicitud CORS. En el ejemplo anterior, se admiten todos los encabezados de metadatos, comenzando con x-ms-metadatos, x ms meta de destino y x ms meta abc. Tenga en cuenta que el carácter comodín ' *' indica que se permite cualquier encabezado que empiece por el prefijo especificado.

- **ExposedHeaders**: los encabezados de respuesta que se envían en la respuesta a la solicitud CORS y expuestos por el explorador para el emisor de la solicitud. En el ejemplo anterior, se indica el explorador para exponer cualquier encabezado que empiece por meta de ms x.

- **MaxAgeInSeconds**: solicitar el tiempo máximo que un explorador debe caché estas opciones.

Los servicios de almacenamiento de Azure admiten especificar encabezados con prefijo de elementos de la **AllowedHeaders** y la **ExposedHeaders** . Para permitir que una categoría de encabezados, puede especificar un prefijo común a esa categoría. Por ejemplo, especificando *meta de ms x** como un encabezado con prefijo establece una regla que coincidan con todos los encabezados que comienzan con meta de ms x.

Las limitaciones siguientes se aplican a las reglas CORS:

- Puede especificar hasta cinco reglas CORS por cada servicio de almacenamiento (Blob, tabla y cola).
- El tamaño máximo de todos los CORS configuración de las reglas de la solicitud, excluyendo etiquetas XML, no debe superar 2 KB.
- La longitud del encabezado permitido, encabezado expuesto o permitido origen no debe superar 256 caracteres.
- Encabezados permitidos y encabezados expuestos pueden ser:
  - Encabezados literales, donde se proporciona el nombre del encabezado exacta, como **procesa una meta de ms x**. Puede especificarse un máximo de 64 literales encabezados de la solicitud.
  - Encabezados, donde se proporciona un prefijo del encabezado, por ejemplo, **x-ms-metadatos**el prefijo *. Especificar un prefijo de esta manera se permite o expone cualquier encabezado que comienza con el prefijo dado. Un máximo de dos encabezados con prefijo se puede especificar en la solicitud.
- Los métodos (o verbos HTTP) especificado en el elemento **AllowedMethods** deben cumplir los métodos compatibles con la API del servicio de almacenamiento de Azure. Métodos admitidos son eliminar, GET, cabeza, combinar, publicar, opciones y SUPERPONER.

## <a name="understanding-cors-rule-evaluation-logic"></a>Descripción de la lógica de evaluación de regla CORS

Cuando un servicio de almacenamiento recibe una solicitud de comprobaciones o real, evalúa solicitud según las reglas CORS que ha establecido para el servicio a través de la operación de establecer propiedades del servicio correspondiente. Reglas CORS se evalúan en el orden en que se establecieron en el cuerpo de la solicitud de la operación de establecer propiedades del servicio.

Reglas CORS se evalúan como sigue:

1. En primer lugar, el dominio de origen de la solicitud se compara con los dominios que aparecen para el elemento **AllowedOrigins** . Si el dominio de origen se incluye en la lista o todos los dominios permitidos con el carácter comodín ' *', a continuación, reglas de recaudación de evaluación. Si el dominio de origen no está incluido, falla la solicitud.

2. A continuación, el método (o verbo HTTP) de la solicitud se compara con los métodos enumerados en el elemento **AllowedMethods** . Si el método se incluye en la lista, se realiza la evaluación de reglas; Si no es así, se produce un error en la solicitud.

3. Si la solicitud coincide con una regla en su dominio de origen y su método, esa regla está seleccionada para la solicitud y no hay más reglas se evalúan de proceso. Antes de la solicitud puede tener éxito, sin embargo, los encabezados especificados en la solicitud se comparan con los encabezados que aparece en el elemento **AllowedHeaders** . Si los encabezados enviados no coinciden con los encabezados permitidos, se produce un error en la solicitud.

Dado que las reglas se procesan en el orden en que están presentes en el cuerpo de la solicitud, se recomienda que especifique las reglas más restrictiva con respecto a orígenes primero en la lista, para que estos se evalúan primero. Especifique las reglas que son menos restrictivas: por ejemplo, una regla para permitir que todos los orígenes: al final de la lista.

### <a name="example--cors-rules-evaluation"></a>Ejemplo: CORS reglas de evaluación

En el ejemplo siguiente se muestra un cuerpo de la solicitud parcial para una operación establecer reglas CORS para los servicios de almacenamiento. Para obtener más información sobre la creación de la solicitud, vea [Establecer propiedades de servicio de blobs](https://msdn.microsoft.com/library/hh452235.aspx), [Establecer propiedades del servicio de cola](https://msdn.microsoft.com/library/hh452232.aspx)y [Establecer propiedades de servicio de la tabla](https://msdn.microsoft.com/library/hh452240.aspx) .

    <Cors>
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
            <AllowedMethods>PUT,HEAD</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
        </CorsRule>
        <CorsRule>
            <AllowedOrigins>*</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
        </CorsRule>
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
            <AllowedMethods>GET</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
        </CorsRule>
    </Cors>

A continuación, tenga en cuenta las siguientes solicitudes de CORS:

Solicitar||| Respuesta||
---|---|---|---|---
**Método** |**Origen** |**Encabezados de solicitud** |**Coincidencia de regla** |**Resultado**
**COLOCAR** | http://www.contoso.com |x-ms-blob-tipo de contenido | Primera regla |Éxito
**Obtener** | http://www.contoso.com| x-ms-blob-tipo de contenido | Segunda regla |Éxito
**Obtener** | http://www.contoso.com| x-ms-blob-tipo de contenido | Segunda regla | Error

La primera solicitud coincide con la primera regla: el dominio de origen coincide con los orígenes permitidos, el método coincide con los métodos permitidos, y el encabezado coincide con los encabezados de permitidos y por tanto se realiza correctamente.

La segunda solicitud no coincide con la primera regla porque el método no coincide con los métodos permitidos. Sin embargo,, coinciden la segunda regla, por lo que se realiza correctamente.

La tercera solicitud coincide con la segunda regla en su dominio de origen y el método, por lo que no hay más reglas se evalúan. Sin embargo, el *encabezado de x ms cliente solicitud identificador* no está permitido por la segunda regla, por lo que se produce un error en la solicitud, a pesar de que la semántica de la tercera regla habría permitido se realice correctamente.

>[AZURE.NOTE] Aunque este ejemplo muestra una regla menos restrictiva antes uno más restrictivo, en general lo mejor es la primera lista las reglas más restrictivas.

## <a name="understanding-how-the-vary-header-is-set"></a>Comprender cómo se establece el encabezado de variar

El encabezado de *variar* es un encabezado HTTP/1.1 estándar, que consta de un conjunto de campos de encabezado de solicitud que indiquen al agente de usuario o explorador acerca de los criterios que se han seleccionado por el servidor para procesar la solicitud. El encabezado de *variar* se utiliza principalmente para el almacenamiento en caché, servidores proxy, los exploradores y CDN, que utiliza para determinar cómo se debe almacenar en caché la respuesta. Para obtener más información, vea la especificación para el [encabezado de variar](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Cuando el explorador o en otro agente de usuario almacena en caché la respuesta de una solicitud CORS, se almacena en caché en el dominio de origen como el origen permitido. Cuando un segundo dominio problemas la misma solicitud para un recurso de almacenamiento mientras esté activa la memoria caché, el agente de usuario recupera el dominio de origen en caché. El segundo dominio no coincide con el dominio en caché, para que la solicitud falla cuando lo haría correctamente en caso contrario. En algunos casos, el almacenamiento de Azure establece el encabezado de variar en **origen** para indicar el agente de usuario para enviar la solicitud CORS posteriores al servicio cuando el dominio que solicita difiere el origen de la caché.

Almacenamiento de Azure establece el encabezado de *variar* en **origen** real solicitudes GET por cabeza en los casos siguientes:

- Cuando el origen de la solicitud coincide exactamente con el origen permitido definido por una regla CORS. Para que sea una coincidencia exacta, la regla CORS no puede incluir un carácter comodín ' * ' carácter.

- No hay ninguna regla que coincide con el origen de la solicitud, pero CORS está habilitada para el servicio de almacenamiento.

En el caso de que una solicitud GET/cabeza coincide con una regla CORS que permite a todos los orígenes de la respuesta indica que se permiten todos los orígenes y la caché de agente de usuario permitirá las solicitudes subsiguientes de cualquier dominio de origen mientras esté activa la memoria caché.

Tenga en cuenta que para las solicitudes utilizando métodos distintos GET/cabeza, los servicios de almacenamiento no establece el encabezado variar, ya que no se almacenan en caché las respuestas a estos métodos agentes de usuario.

La tabla siguiente indica cómo Azure almacenamiento se responder a solicitudes GET/cabeza basadas en los casos mencionados anteriormente:

Solicitar|Configuración de cuenta y el resultado de la evaluación de la regla|||Respuesta|||
---|---|---|---|---|---|---|---|---
**Encabezado de origen presente en la solicitud** | **Reglas CORS especificadas para este servicio** | **Regla coincidente existe que permite a todos los orígenes (*)** | **Regla coincidente existe coincidencia exacta de origen** | **Respuesta incluye el encabezado de variar establecido en origen** | **Respuesta incluye Access Control-permitido-origen: "*"** | **Respuesta incluye Access Control-expuestas-encabezados**
No|No|No|No|No|No|No
No|Sí|No|No|Sí|No|No
No|Sí|Sí|No|No|Sí|Sí
Sí|No|No|No|No|No|No
Sí|Sí|No|Sí|Sí|No|Sí
Sí|Sí|No|No|Sí|No|No
Sí|Sí|Sí|No|No|Sí|Sí


## <a name="billing-for-cors-requests"></a>Facturación para las solicitudes CORS

Solicitudes de comprobaciones correctas se cargarán si habilitó CORS por cualquiera de los servicios de almacenamiento de la cuenta (llamando al [Establecer propiedades de servicio de blobs](https://msdn.microsoft.com/library/hh452235.aspx), [Establecer propiedades del servicio cola](https://msdn.microsoft.com/library/hh452232.aspx)o [Establecer propiedades de servicio de tabla](https://msdn.microsoft.com/library/hh452240.aspx)). Para minimizar los cargos, considere la posibilidad de establecer el elemento de **MaxAgeInSeconds** en las reglas CORS en un valor grande para que el agente de usuario almacena la solicitud.

No se cargará solicitudes comprobaciones incorrectas.

## <a name="next-steps"></a>Pasos siguientes

[Establecer las propiedades de servicio de blobs](https://msdn.microsoft.com/library/hh452235.aspx)

[Establecer propiedades de servicio de cola](https://msdn.microsoft.com/library/hh452232.aspx)

[Establecer las propiedades de servicio de tabla](https://msdn.microsoft.com/library/hh452240.aspx)

[Especificación de uso compartido de W3C Origin entre recursos](http://www.w3.org/TR/cors/)
