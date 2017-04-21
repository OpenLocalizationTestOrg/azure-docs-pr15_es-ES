<properties
   pageTitle="Guía de diseño de API | Microsoft Azure"
   description="Guía de cómo crear una bien diseñada API."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/13/2016"
   ms.author="masashin"/>

# <a name="api-design-guidance"></a>Guía de diseño de API

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

## <a name="overview"></a>Información general

Muchas soluciones moderna basada en web realizar el uso de servicios web, alojados en servidores web, para proporcionar funcionalidad de cliente remoto de aplicaciones. Las operaciones que expone un servicio web constituyen un web API. Debe tener como objetivo un web bien diseñado API para admitir:

- **Independencia de la plataforma**. Aplicaciones cliente debería poder utilizar la API que proporciona el servicio web sin necesidad de cómo los datos o las operaciones que expone API implementación física. Esto requiere que la API se rige por los estándares comunes que permiten que un servicio web y de aplicaciones de cliente acordar los formatos de datos, el uso y la estructura de los datos que intercambian entre aplicaciones cliente y el servicio web.

- **Evolución de servicio**. El servicio web debería poder evolucionar y agregar (o quitar) funcionalidad de forma independiente de las aplicaciones cliente. Aplicaciones cliente existentes debería poder siga funcionando sin modificar como las características proporcionadas por el cambio de servicio web. También deben ser reconocible, todas las funciones para que las aplicaciones cliente de plena pueden utilizar.

La finalidad de esta guía es describir los problemas que debe tener en cuenta al diseñar un sitio web API.

## <a name="introduction-to-representational-state-transfer-rest"></a>Introducción a la transferencia de estado de representación (REST)

En su disertación en 2000, Roy Fielding propone una arquitectura alternativa para estructurar las operaciones expuestas por los servicios web; RESTO. REST es un estilo de arquitectura de creación de sistemas distribuidos según hipermedia. Una ventaja principal del modelo resto es que se basa en estándares abiertos y no enlaza la implementación de modelo o las aplicaciones cliente que tener acceso a cualquier implementación específica. Por ejemplo, podría implementar un servicio web REST mediante la API de Web de Microsoft ASP.NET y se podrían desarrollar aplicaciones cliente con cualquier idioma y un conjunto de herramientas que puede generar solicitudes HTTP y analizar las respuestas HTTP.

> [AZURE.NOTE] RESTO es realmente independiente de cualquier protocolo subyacente y no es necesariamente están vinculado HTTP. Sin embargo, las implementaciones más comunes de los sistemas que se basan en el resto de utilizan HTTP como protocolo de aplicación para enviar y recibir solicitudes. Este documento se centra en asignación principios resto a sistemas diseñados para funcionar con HTTP.

El modelo de resto utiliza una combinación de navegación para representar los objetos y servicios en una red (denominada _recursos_). Muchos sistemas que implementan resto normalmente utilizan el protocolo HTTP para transmitir solicitudes de acceso a estos recursos. En estos sistemas, una aplicación cliente envía una solicitud en forma de un URI que identifica un recurso y un método HTTP (el más comunes que se está GET, POST, SUPERPONER o eliminar) que indica la operación que debe realizarse en ese recurso.  El cuerpo de la solicitud HTTP contiene los datos necesarios para realizar la operación. El punto importante comprender es que el resto define un modelo de solicitud de estado. Las solicitudes HTTP deben ser independientes y pueden ocurrir en cualquier orden, por lo que no es factible intenta conservar la información de estado transitorias entre solicitudes.  Es el único lugar donde se almacena información en los propios recursos y cada solicitud debe ser una operación atómica. De hecho, un modelo de resto implementa una máquina de estado finita donde una solicitud de transiciones de un recurso de un estado no transitorios bien definido.

> [AZURE.NOTE] La naturaleza independiente de solicitudes individuales en el modelo de resto permite un sistema de construcción siguiendo estos principios para ser muy scalable. No es necesario para conservar la afinidad entre una aplicación de cliente realizar una serie de solicitudes y los servidores web específico esas solicitudes.

Otro punto fundamental para implementar un resto del modelo eficaz es comprender las relaciones entre los distintos recursos a la que el modelo proporciona acceso. Estos recursos normalmente se organizan como colecciones y relaciones. Por ejemplo, suponga que un análisis rápido de un sistema de comercio electrónico muestra que hay dos colecciones de cliente que están probable que haya interesado aplicaciones: pedidos y clientes. Cada pedido y el cliente deben tener su propia clave para fines de identificación único. La URI para tener acceso a la colección de pedidos podría ser algo tan simple como _/orders_, y de forma similar el URI para recuperar a todos los _clientes/Customers_. Emitir una solicitud HTTP GET a _/orders_ URI debe devolver una lista de todos los pedidos de la colección codificadas como una respuesta HTTP:

```HTTP
GET http://adventure-works.com/orders HTTP/1.1
...
```

La respuesta que se muestra a continuación codifica los pedidos como una estructura de la lista JSON:

```HTTP
HTTP/1.1 200 OK
...
Date: Fri, 22 Aug 2014 08:49:02 GMT
Content-Length: ...
[{"orderId":1,"orderValue":99.90,"productId":1,"quantity":1},{"orderId":2,"orderValue":10.00,"productId":4,"quantity":2},{"orderId":3,"orderValue":16.60,"productId":2,"quantity":4},{"orderId":4,"orderValue":25.90,"productId":3,"quantity":1},{"orderId":5,"orderValue":99.90,"productId":1,"quantity":1}]
```
Para obtener un pedido individual requiere que especifica el identificador del pedido desde el recurso de _pedidos_ , como _/orders/2_:

```HTTP
GET http://adventure-works.com/orders/2 HTTP/1.1
...
```

```HTTP
HTTP/1.1 200 OK
...
Date: Fri, 22 Aug 2014 08:49:02 GMT
Content-Length: ...
{"orderId":2,"orderValue":10.00,"productId":4,"quantity":2}
```

> [AZURE.NOTE] Para simplificar, estos ejemplos muestran la información en las respuestas que se devuelven como datos de texto JSON. Sin embargo, no hay ninguna razón por qué recursos no deben contener cualquier otro tipo de datos admitidos por HTTP, como la información de cifrado o binario; el tipo de contenido en la respuesta HTTP debe especificar el tipo. Además, un modelo de resto puede devolver los mismos datos en diferentes formatos, como XML o JSON. En este caso, el servicio web debería poder llevar a cabo negociación de contenido con el cliente que hace la solicitud. La solicitud puede incluir un encabezado de _aceptación_ que especifica el formato preferido que le gustaría recibir el cliente y el servicio web debe intentar respetan este formato si es posible.

Observe que la respuesta de una solicitud de resto emplea el estándar HTTP códigos de estado. Por ejemplo, una solicitud que devuelve datos válidos debe incluir el código de respuesta HTTP 200 (OK), mientras que una solicitud que no se puede encontrar o eliminar un recurso especificado debe devolver una respuesta que incluya el código de estado HTTP 404 (no encontrado).

## <a name="design-and-structure-of-a-restful-web-api"></a>Diseño y la estructura de un web API de REST

Las claves para diseñar una API web correctas son simplificar y coherencia. Una API de Web que tiene estos dos factores facilita generar aplicaciones cliente que necesitan para consumir la API.

Un sitio web REST API se centra en exponer un conjunto de recursos conectados y proporcionar las operaciones básicas que permiten a una aplicación manipular estos recursos y desplazarse fácilmente entre ellas. Por este motivo, los URI que constituyen un típico web REST API deben ser orientados a los datos que expone y usar las funciones proporcionadas por HTTP con estos datos. Este enfoque requiere una máxima diferente de la empleada normalmente al diseñar un conjunto de clases en una API orientada a objetos, que suele ser más motivado por el comportamiento de objetos y clases. Además, un web de REST API debe tener estado y no dependen de las operaciones que se invoca en una secuencia determinada. Las secciones siguientes resumen los puntos que debe tener en cuenta al diseñar un sitio web de REST API.

### <a name="organizing-the-web-api-around-resources"></a>Organizar la web API alrededor de recursos

> [AZURE.TIP] La URI expuestas por un servicio web REST deben estar basadas en nombres (los datos a la que la API de web proporciona acceso) y no los verbos (lo una aplicación puede hacer con los datos).

Centrarse en las entidades empresariales que expone la API de web. Por ejemplo, en un sitio web API diseñado para admitir el sistema de comercio electrónico descrito anteriormente, las entidades principales son los clientes y pedidos. Procesos como la ley de un pedido pueden conseguirse proporcionando una operación de HTTP POST que tiene la información del pedido y lo agrega a la lista de pedidos para el cliente. Internamente, esta operación de entrada puede realizar tareas como comprobación de existencias y facturación al cliente. La respuesta HTTP puede indicar si el pedido correctamente o no. Tenga en cuenta que no tiene un recurso se base en un elemento de datos físico único. Por ejemplo, un recurso de orden podría implementa internamente con información agregado de muchas filas distribuyan a través de varias tablas en una base de datos relacional, pero presentan al cliente como una entidad única.

> [AZURE.TIP] Evite el diseño de una interfaz REST que refleja o depende de la estructura interna de los datos que se exponen. RESTO es sobre la implementación de más de operaciones CRUD (crear, recuperar, actualizar, eliminar) simples en tablas separadas en una base de datos relacional. El propósito de descanso es asignar entidades empresariales y las operaciones que puede llevar a cabo una aplicación estas entidades a la implementación física de estas entidades, pero no se debería exponer un cliente a estos detalles físicos.

Entidades empresariales individuales no suelen ser aislamiento (aunque pueden existir algunos objetos singleton), pero en su lugar tiende a agrupar en colecciones. Resto términos, cada entidad y cada colección son recursos. En web REST API cada colección tiene su propio URI en el servicio web y realizar una solicitud HTTP GET sobre un URI para una colección de recupera una lista de elementos de la colección. Cada producto también tiene su propio URI y, a continuación, una aplicación puede enviar otra solicitud HTTP GET con ese URI para recuperar los detalles de ese elemento. Debe organizar a los URI para colecciones y elementos de forma jerárquica. En el sistema de comercio electrónico, el _URI/Customers_ denota colección del cliente y _/customers/5_ recupera los detalles del cliente único con el 5 de identificador de esta colección. Este enfoque ayuda a mantener la web API intuitiva.

> [AZURE.TIP] Adoptar una convención de nomenclatura coherente en URI; en general, le ayuda a utilizar sustantivos plurales de URI que colecciones de referencia.

También debe tener en cuenta las relaciones entre los distintos tipos de recursos y cómo puede exponer estas asociaciones. Por ejemplo, los clientes pueden realizar pedidos de cero o más. Una manera natural para representar esta relación sería a través de un URI como _/customers/5/orders_ encontrar todos los pedidos de cliente 5. También puede tener en cuenta que representa la asociación de un pedido a un cliente específico a través de un URI como _/orders/99/customer_ para buscar al cliente de orden 99, pero extender este modelo demasiado puede convertirse en difícil de implementar. Una solución mejor es proporcionar vínculos de navegación a los recursos, como el cliente, en el cuerpo del mensaje de respuesta HTTP devolverán cuando se consulta el orden asociados. Este mecanismo se describe con más detalle en la sección con el enfoque de HATEOAS para habilitar la navegación para recursos relacionados más adelante en esta guía.

Sistemas más complejos puede haber muchos más tipos de entidad y puede ser tentador proporcionar URI que permiten una aplicación de cliente para desplazarse a través de varios niveles de relaciones, como _/customers/1/orders/99/products_ para obtener la lista de productos en orden 99 realizados por el cliente 1. Sin embargo, este nivel de complejidad puede ser difícil de mantener y inflexible si las relaciones entre recursos cambian en el futuro. En su lugar, se debe buscar mantener a URI relativamente simple. Tenga en cuenta que una vez que una aplicación tiene una referencia a un recurso, debe ser posible utilizar esta referencia para buscar elementos relacionados con ese recurso. La consulta anterior se puede reemplazar con la URI _/customers/1/orders_ para buscar todos los pedidos de cliente 1 y, a continuación, consultar la URI _/orders/99/products_ para buscar los productos en este orden (suponiendo que 99 pedido por cliente 1).

> [AZURE.TIP] Evite que requieren recursos URI más compleja que _colección/elemento/colección_.

Otro punto para tener en cuenta es que todas las solicitudes de web imponen una carga en el servidor web y, cuanto mayor sea el número de solicitudes cuanto mayor sea la carga. Debe intentar definir los recursos para evitar "conversadoras" web API que exponen una gran cantidad de recursos pequeñas. API de este tipo puede obligarle a una aplicación de cliente para enviar varias solicitudes para encontrar todos los datos que necesita. Puede ser útil reducir la normalización de datos y combinar la información relacionada en recursos más grandes que se puede recuperar al emitir una solicitud. Sin embargo, debe equilibrar este enfoque contra la sobrecarga de obtención de datos que no pueden necesitar frecuentemente por el cliente. Recuperar objetos grandes puede aumentar la latencia de una solicitud e incurre en costos de ancho de banda adicional para ventaja pequeño si los datos adicionales no se usan con frecuencia.

Evitar la introducción de dependencias entre la API de web a la estructura, el tipo o la ubicación de los orígenes de datos subyacentes. Por ejemplo, si los datos se encuentran en una base de datos relacional, la API de web no es necesario exponer cada tabla como una colección de recursos. Piense en la web API como una extracción de la base de datos y, si es necesario introducir una capa de asignación entre la base de datos y la API de web. De esta forma, si cambia el diseño o la implementación de la base de datos (por ejemplo, mover una base de datos relacional que contiene una colección de tablas normalizados a un sistema de almacenamiento de NoSQL sin normalizar como una base de datos de documento) están aisladas aplicaciones cliente de estos cambios.
> [AZURE.TIP] El origen de los datos que sustenta la API de web no tiene que ser un almacén de datos; es posible que otro servicio o aplicación de línea de negocio o incluso una aplicación heredada ejecutando local dentro de una organización.

Por último, es no posible asignar cada operación de implementada por un sitio web API a un recurso específico. Puede controlar estas situaciones _no son recursos_ a través de solicitudes HTTP GET que llamar a una parte de la funcionalidad y devolverán los resultados como un mensaje de respuesta HTTP. Un sitio web API que implementa operaciones de estilo de calculadora simple como sumar y restar puede proporcionar a URI que exponen estas operaciones como recursos ficticio y utilizar la cadena de consulta para especificar los parámetros necesarios. Por ejemplo, una solicitud GET al URI _/add?operand1 = 99 & operand2 = 1_ podría devolver un mensaje de respuesta con el texto que contiene el valor 100 y solicitud al URI _/subtract?operand1 = 50 & operand2 = 20_ podría devolver un mensaje de respuesta con el texto que contiene el valor 30. Sin embargo, solo use estas formas de URI con moderación.

### <a name="defining-operations-in-terms-of-http-methods"></a>Definición de operaciones en términos de métodos HTTP

El protocolo HTTP define un número de métodos que asignar significado semántico a una solicitud. Los métodos HTTP comunes que se usan las API de REST más web son:

- **Obtener**, para recuperar una copia del recurso en el URI especificado. El cuerpo del mensaje de respuesta contiene los detalles del recurso solicitado.

- **Publicación**, para crear un nuevo recurso en el URI especificado. El cuerpo del mensaje de solicitud proporciona los detalles del nuevo recurso. Tenga en cuenta que publicar también puede utilizarse para desencadenar operaciones que realmente no crear recursos.

- **Colocar**, reemplazar o actualizar el recurso en el URI especificado. El cuerpo del mensaje de solicitud especifica el recurso modificarse y los valores que se aplique.

- **Eliminar**, quitar el recurso en el URI especificado.

> [AZURE.NOTE] El protocolo HTTP define también otros métodos menos utilizados como revisión que se utiliza para solicitar actualizaciones selectivas a un recurso, sede que se utiliza para solicitar una descripción de un recurso, opciones que permite a un cliente de información obtener información acerca de las opciones de comunicación compatible con el servidor y que permite a un cliente para solicitar información que puede usar para fines de pruebas y diagnósticos de seguimiento.

El efecto de una solicitud específica debe dependen de si el recurso al que se aplica es una colección o un elemento individual. La siguiente tabla resume las convenciones comunes adoptadas por implementaciones más REST usando el ejemplo de comercio electrónico. Tenga en cuenta que no todas estas solicitudes podrían implementa; depende del escenario en cuestión.

| **Recursos** | **Exponer** | **Obtener** | **COLOCAR** | **ELIMINAR** |
|--------------|----------|---------|---------|------------|
| / Customers | Crear un nuevo cliente | Recuperar todos los clientes | Actualización masiva de clientes (_Si ha implementado_) | Quitar todos los clientes |
| / clientes/1 | Error | Recuperar los detalles de cliente 1 | Actualizar los detalles de cliente 1 si existe, retorno de lo contrario, un error | Quitar al cliente 1 |
| /Customers/1/Orders | Crear un nuevo pedido para el cliente 1 | Recuperar todos los pedidos para el cliente 1 | Actualización de pedidos de cliente 1 (_Si implementa_) en masa | Quitar todos los pedidos para el cliente 1 (_Si ha implementado_) |

El propósito de solicitudes GET y eliminar son relativamente sencillos, pero hay confusión sobre la finalidad y los efectos de entrada y SUPERPONER las solicitudes en el ámbito.

Una solicitud POST debe crear un nuevo recurso con datos proporcionados en el cuerpo de la solicitud. En el modelo REST, con frecuencia se aplican solicitudes POST a recursos que son colecciones; el nuevo recurso se agrega a la colección.

> [AZURE.NOTE] También puede definir las solicitudes de publicación que desencadenar algunas funciones (y que no necesariamente devolver datos), que pueden aplicarse a las colecciones de estos tipos de solicitud. Por ejemplo podría usar una solicitud de publicación pasar un parte de horas a un servicio de procesamiento de nóminas y volver los impuestos calculados como respuesta.

Una solicitud de SUPERPONER está pensada para modificar un recurso existente. Si el recurso especificado no existe, la solicitud de SUPERPONER podría devolver un error (en algunos casos, podría realmente crear el recurso). COLOCAR solicitudes con más frecuencia se aplican a recursos de elementos individuales (por ejemplo, un cliente específico u orden), aunque se pueden aplicar a las colecciones, aunque se implementa con menos frecuencia. Tenga en cuenta que SUPERPONER solicita son idempotentes mientras que las solicitudes de publicación están; Si una aplicación envía la misma poner solicitud varias veces los resultados siempre debe coincidir (el mismo recurso se modificará con los mismos valores), pero si una aplicación repite la misma solicitud de publicación, el resultado será la creación de varios recursos.

> [AZURE.NOTE] Estrictamente, una solicitud de HTTP poner reemplaza un recurso existente con el recurso especificado en el cuerpo de la solicitud. Si su intención es para modificar una selección de propiedades de un recurso y dejar otras propiedades sin cambios, se debe implementar mediante una solicitud HTTP de revisiones. Sin embargo, muchas implementaciones REST relajan esta regla y usar SUPERPONER para ambos casos.

### <a name="processing-http-requests"></a>Procesar solicitudes HTTP
Los datos que incluye una aplicación de cliente en muchas solicitudes HTTP y los mensajes de respuesta correspondiente desde el servidor web, podrían presentarse en una variedad de formatos (o tipos de medios). Por ejemplo, se podrían proporcionar los datos que especifican los detalles de un cliente o un pedido como XML, JSON o algún otro formato de codificación y comprimido. Un web de REST API debe admitir distintos tipos de medios como solicitado por la aplicación cliente que envía una solicitud.

Cuando una aplicación cliente envía una petición que devuelve los datos en el cuerpo de un mensaje, puede especificar los tipos de medios que puede controlar en el encabezado de aceptación de la solicitud. El código siguiente muestra una solicitud HTTP GET que recupera los detalles del cliente 1 y solicita el resultado que se devolverá como JSON (el cliente todavía debe examinar el tipo de medio de los datos de la respuesta para comprobar el formato de los datos devueltos):

```HTTP
GET http://adventure-works.com/orders/2 HTTP/1.1
...
Accept: application/json
...
```

Si el servidor web compatible con este tipo de medio, puede responder con una respuesta que incluye el encabezado de tipo de contenido que especifica el formato de los datos en el cuerpo del mensaje:

> [AZURE.NOTE] Para una mayor interoperabilidad, deben ser tipos de referencia en los encabezados de Aceptar y tipo de contenido multimedia reconocen tipos MIME en lugar de escribe algunas multimedia personalizado.

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
{"orderID":2,"productID":4,"quantity":2,"orderValue":10.00}
```

Si el servidor web no admite el tipo de medio solicitado, puede enviar los datos en un formato diferente. EN todos los casos, debe especificar el tipo de medio (por ejemplo, la _aplicación o json_) en el encabezado de tipo de contenido. Es la responsabilidad de la aplicación cliente para analizar el mensaje de respuesta e interpretar los resultados en el cuerpo del mensaje correctamente.

Tenga en cuenta que en este ejemplo, el servidor web correctamente recupera los datos solicitados e indica éxito pasando un código de estado de 200 atrás en el encabezado de respuesta. Si no se encuentran datos coincidentes, en su lugar, se debe devolver un código de estado de 404 (no encontrado) y el cuerpo del mensaje de respuesta puede contener información adicional. Especifica el formato de esta información en el encabezado de tipo de contenido, como se muestra en el ejemplo siguiente:

```HTTP
GET http://adventure-works.com/orders/222 HTTP/1.1
...
Accept: application/json
...
```

Orden 222 no existe, por lo que el mensaje de respuesta es similar a esta:

```HTTP
HTTP/1.1 404 Not Found
...
Content-Type: application/json; charset=utf-8
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
{"message":"No such order"}
```

Cuando una aplicación envía una solicitud HTTP poner para actualizar un recurso, se especifica el URI del recurso y proporcionan los datos que se pueden modificar en el cuerpo del mensaje de solicitud. También debe especificar el formato de datos mediante el encabezado de tipo de contenido. Un formato común usado para obtener información basada en el texto es _application/x-www-form-urlencoded_, que consta de un conjunto de pares de nombre y valor separados por el carácter &. En el ejemplo siguiente se muestra una solicitud de HTTP poner que modifica la información en orden 1:

```HTTP
PUT http://adventure-works.com/orders/1 HTTP/1.1
...
Content-Type: application/x-www-form-urlencoded
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
ProductID=3&Quantity=5&OrderValue=250
```

Si la modificación es correcta, lo ideal es que debería responder con un código de estado HTTP 204, que indica que el proceso se ha controlado correctamente, pero que el cuerpo de la respuesta no contiene más información. El encabezado de la ubicación en la respuesta contiene el URI del recurso recién actualizado:

```HTTP
HTTP/1.1 204 No Content
...
Location: http://adventure-works.com/orders/1
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
```

> [AZURE.TIP] Si los datos en un mensaje de solicitud HTTP poner incluyen información de fecha y hora, asegúrese de que el servicio web acepta fechas y horas con formato siguiendo la norma ISO 8601.

Si el recurso que se actualicen no existe, el servidor web puede responder con una respuesta no se encuentra como se describió anteriormente. Como alternativa, si el servidor realmente crea el propio objeto podría devolver el estado códigos HTTP 200 (OK) o HTTP 201 (creado) y el cuerpo de la respuesta podrían contener los datos para el nuevo recurso. Si el encabezado de tipo de contenido de la solicitud especifica un formato de datos que no puede controlar el servidor web, debe responder con código de estado HTTP 415 (tipo de medio no compatible).

> [AZURE.TIP] Considere la implementación de operaciones de HTTP SUPERPONER masa que pueden por lotes actualizaciones de varios recursos en una colección. La solicitud de SUPERPONER debe especificar el URI de la colección y el cuerpo de la solicitud debe especificar los detalles de los recursos para modificarse. Este enfoque puede ayudar a reducir el intercambio de mensajes y mejorar el rendimiento.

El formato de una petición de HTTP POST que crear nuevos recursos son similares a los de solicitudes de SUPERPONER; el cuerpo del mensaje contiene los detalles del nuevo recurso que se va a agregar. Sin embargo, el URI normalmente especifica la colección a la que desea agregar el recurso. En el ejemplo siguiente se crea un nuevo pedido y lo agrega a la colección de pedidos:

```HTTP
POST http://adventure-works.com/orders HTTP/1.1
...
Content-Type: application/x-www-form-urlencoded
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
productID=5&quantity=15&orderValue=400
```

Si la solicitud se realiza correctamente, el servidor web debe responder con un código de mensaje con código de estado HTTP 201 (creado). El encabezado de la ubicación debe contener el URI del recurso recién creado, y el cuerpo de la respuesta debe contener una copia del nuevo recurso; el encabezado de tipo de contenido especifica el formato de datos:

```HTTP
HTTP/1.1 201 Created
...
Content-Type: application/json; charset=utf-8
Location: http://adventure-works.com/orders/99
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
Content-Length: ...
{"orderID":99,"productID":5,"quantity":15,"orderValue":400}
```

> [AZURE.TIP] Si los datos proporcionados por una solicitud SUPERPONER o POST no están válidos, el servidor web debe responder con un mensaje con código de estado HTTP 400 (solicitud incorrecta). El cuerpo del mensaje puede contener información adicional sobre el problema con la solicitud y los formatos de lo esperados, o puede contener un vínculo a una dirección URL que proporciona más detalles.

Para quitar un recurso, una solicitud de HTTP DELETE simplemente proporciona el URI del recurso que desea eliminar. En el ejemplo siguiente se intenta quitar orden 99:

```HTTP
DELETE http://adventure-works.com/orders/99 HTTP/1.1
...
```

Si la operación de eliminación se realiza correctamente, el servidor web debe responder con código de estado HTTP 204, que indica que el proceso se ha controlado correctamente, pero que el cuerpo de la respuesta no contiene más información (esta es la misma respuesta devuelta por una operación de SUPERPONER correcta, pero sin el encabezado de una ubicación como el recurso ya no existe.) También es posible que una solicitud de eliminación devolver el código de estado HTTP 200 (OK) o 202 (aceptado) si la eliminación se realiza de forma asincrónica.

```HTTP
HTTP/1.1 204 No Content
...
Date: Fri, 22 Aug 2014 09:18:37 GMT
```

Si no se encuentra el recurso, el servidor web debe devolver un mensaje de 404 (no encontrado) en su lugar.

> [AZURE.TIP] Si todos los recursos en una colección deben eliminarse, habilitar una solicitud HTTP DELETE especificar el URI de la colección en lugar de tener una aplicación para quitar cada recurso por separado de la colección.

### <a name="filtering-and-paginating-data"></a>Filtrado y la paginación de datos

Debe procurar mantener a los URI sencillo e intuitivo. Exponer una colección de recursos a través de un solo URI ayuda a este respecto, pero puede llevar a aplicaciones obteniendo grandes cantidades de datos cuando se requiere únicamente un subconjunto de la información. Generar un gran volumen de tráfico afecta no sólo el rendimiento y escalabilidad del servidor web, pero también afectar a la capacidad de respuesta de las aplicaciones cliente solicitando los datos.

Por ejemplo, si pedidos contienen el precio pagado por el orden, una aplicación de cliente que necesita para recuperar todos los pedidos que tienen un costo por un valor específico podría necesitar recuperar todos los pedidos de _/orders_ URI y, a continuación, filtrar estos pedidos localmente. Claramente este proceso es muy ineficaz; residuos de energía de procesamiento y ancho de banda de red en el servidor web API de hospedaje.

Puede ser una solución proporcionar un esquema URI como _/orders/ordervalue_greater_than_n_ donde _n_ es el precio del pedido, pero para las pero un número limitado de precios de este enfoque es práctico. Además, si necesita órdenes de consulta basadas en otros criterios, puede acabar que se enfrenta a proporcionar con una lista larga de URI con nombres posiblemente no intuitiva.

Es mejor para filtrar los datos proporcionar los criterios de filtro en la cadena de consulta que se pasa a la web API, como por ejemplo _/orders?ordervaluethreshold = n_. En este ejemplo, la operación correspondiente en la web API es responsable de análisis y control de la `ordervaluethreshold` parámetro en la cadena de consulta y devolver los resultados filtrados en la respuesta HTTP.

Algunas solicitudes de HTTP GET simples sobre los recursos de la colección potencialmente podrían devolver un gran número de elementos. Para combatir la posibilidad de que esto suceda, debe diseñar la API para limitar la cantidad de datos devueltos por cualquier solicitud de web. Puede lograr esto mediante el soporte de cadenas de consulta que permiten al usuario especificar el número máximo de elementos que se recuperarán (que podría ser sujeto a un límite de límite_superior para ayudar a evitar ataques de rechazo de servicio) y un desplazamiento inicial en la colección. Por ejemplo, la cadena de consulta en el URI _/orders?limit = 25 & desplazamiento = 50_ debe recuperar 25 pedidos comenzando con el orden de 50 que se encuentra en la colección de pedidos. Como de filtrado de datos, la operación que implementa la solicitud GET en la web API es responsable de análisis y control de la `limit` y `offset` parámetros en la cadena de consulta. Para ayudar a las aplicaciones cliente, recibe solicitudes que devuelven datos paginación también deben incluir algún tipo de metadatos que indican el número total de recursos disponibles en la colección. También puede tener en cuenta otras estrategias de paginación inteligente; Para obtener más información, vea [API Design Notes: paginación inteligente](http://bizcoder.com/api-design-notes-smart-paging)

Puede seguir una estrategia similar para ordenar los datos se recopila; puede proporcionar un parámetro de ordenación que toma un nombre de campo como el valor, como por ejemplo _/orders?sort = ProductID_. Sin embargo, tenga en cuenta que este enfoque puede tener un efecto perjudicial en caché (parte del identificador de recursos utilizado muchas implementaciones de caché como la clave para datos almacenados en caché de formulario de parámetros de cadena de consulta).

Puede extender este enfoque para limitar (proyecto) los campos devuelto si un elemento de recurso solo contiene una gran cantidad de datos. Por ejemplo, podría usar un parámetro de cadena de consulta que acepta una lista delimitada por comas de campos, como por ejemplo _/orders?fields = IdProducto, cantidad_.

> [AZURE.TIP] Dar a todos los parámetros opcionales en cadenas de consulta predeterminados significativos. Por ejemplo, establezca la `limit` parámetro a 10 y la `offset` parámetro 0 si implementa la paginación, establezca el parámetro de ordenación en la clave del recurso si implementa la ordenación y establezca la `fields` parámetro para todos los campos de recurso si admiten proyecciones.

### <a name="handling-large-binary-resources"></a>Administración de recursos binario grandes

Un recurso solo puede contener campos binarios grandes, como archivos o imágenes. Superar la transmisión problemas producidos por conexiones confiables e intermitentes y para mejorar los tiempos de respuesta, considere la posibilidad de que proporcionan operaciones que permiten estos recursos que se recuperarán en fragmentos por la aplicación cliente. Para ello, la API de web debe admitir el encabezado para aceptar intervalos para las solicitudes GET para recursos de gran tamaño e implementar lo ideal es que peticiones de HTTP cabeza para los recursos. El encabezado para aceptar intervalos indica que la operación GET admite resultados parciales y que una aplicación de cliente puede enviar solicitudes GET que devuelven un subconjunto de un recurso especificado como un intervalo de bytes. Una solicitud de cabeza es similar a una solicitud GET, excepto que sólo devuelve un encabezado que describe el recurso y el cuerpo de un mensaje vacío. Una aplicación de cliente puede emitir una solicitud de cabeza para determinar si se va a recuperar un recurso mediante las solicitudes GET parciales. En el ejemplo siguiente se muestra una solicitud de cabeza que obtiene información sobre una imagen de producto:

```HTTP
HEAD http://adventure-works.com/products/10?fields=productImage HTTP/1.1
...
```

El mensaje de respuesta contiene un encabezado que incluye el tamaño del recurso (4580 bytes) y el encabezado para aceptar intervalos que la operación de obtener correspondiente admite resultados parciales:

```HTTP
HTTP/1.1 200 OK
...
Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 4580
...
```

La aplicación cliente puede usar esta información para construir una serie de operaciones GET para recuperar la imagen en fragmentos más pequeños. La primera solicitud recupera los primeros bytes 2500 mediante el encabezado de intervalo:

```HTTP
GET http://adventure-works.com/products/10?fields=productImage HTTP/1.1
Range: bytes=0-2499
...
```

El mensaje de respuesta indica que se trata de una respuesta parcial volviendo código de estado HTTP 206. El encabezado de la longitud del contenido especifica el número real de bytes devuelto en el cuerpo del mensaje (no el tamaño del recurso) y el encabezado de intervalo de contenido indica qué parte del recurso es (0 2499 fuera de 4580 bytes):

```HTTP
HTTP/1.1 206 Partial Content
...
Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 2500
Content-Range: bytes 0-2499/4580
...
_{binary data not shown}_
```

Una solicitud posterior de la aplicación cliente puede recuperar el resto de los recursos mediante un encabezado de intervalo adecuado:

```HTTP
GET http://adventure-works.com/products/10?fields=productImage HTTP/1.1
Range: bytes=2500-
...
```

El mensaje de resultado correspondiente debe tener este aspecto:

```HTTP
HTTP/1.1 206 Partial Content
...
Accept-Ranges: bytes
Content-Type: image/jpeg
Content-Length: 2080
Content-Range: bytes 2500-4580/4580
...
```

## <a name="using-the-hateoas-approach-to-enable-navigation-to-related-resources"></a>Usar el enfoque HATEOAS para habilitar la navegación a los recursos relacionados

Uno de los propósitos principales de resto es que debe ser posible para desplazarse por el conjunto completo de recursos sin necesidad de conocer el esquema de URI. Cada solicitud HTTP GET debe devolver la información necesaria buscar los recursos relacionados directamente con el objeto solicitado por hipervínculos que se incluyen en la respuesta y, también debe proporcionarse con información que describe las operaciones disponibles en cada uno de estos recursos. Este principio se conoce como HATEOAS o hipertexto como motor de estado de la aplicación. El sistema de forma eficaz es una máquina de estado finita y la respuesta a cada solicitud contiene la información necesaria para pasar de un estado a otro; ninguna otra información debería ser necesario.

> [AZURE.NOTE] Actualmente no hay ningún normas o especificaciones que definen cómo el principio HATEOAS del modelo. Los ejemplos que se muestra en esta sección muestra una posible solución.

Por ejemplo, para controlar la relación entre los clientes y pedidos, los datos devueltos en la respuesta para un pedido específico deben contener a URI en forma de un hipervínculo identificar el cliente que hizo el pedido y las operaciones que pueden realizar en dicho cliente.

```HTTP
GET http://adventure-works.com/orders/3 HTTP/1.1
Accept: application/json
...
```

El cuerpo del mensaje de respuesta contiene un `links` matriz (resaltado en el ejemplo de código) que especifica la naturaleza de la relación (_cliente_), el URI de cliente (_http://adventure-works.com/customers/3_), cómo recuperar los detalles de este cliente (_obtener_) y los tipos MIME que admite el servidor web para recuperar esta información (_texto o xml_ y _json o aplicación_). Se trata de toda la información que necesita una aplicación de cliente para poder obtener los detalles del cliente. Además, la matriz de vínculos también incluye vínculos para las demás operaciones que se pueden realizar, como poner (para modificar al cliente, junto con el formato que el servidor web espera que el cliente para proporcionar) y a continuación, eliminan.

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"orderID":3,"productID":2,"quantity":4,"orderValue":16.60,"links":[(some links omitted){"rel":"customer","href":" http://adventure-works.com/customers/3", "action":"GET","types":["text/xml","application/json"]},{"rel":"
customer","href":" http://adventure-works.com /customers/3", "action":"PUT","types":["application/x-www-form-urlencoded"]},{"rel":"customer","href":" http://adventure-works.com /customers/3","action":"DELETE","types":[]}]}
```

Por integridad, la matriz de vínculos también debe incluir información de referencia a sí misma correspondiente al recurso que se ha recuperado. Estos vínculos se han omitido del ejemplo anterior, pero se resaltan en el código siguiente. Tenga en cuenta que en estos vínculos, se ha utilizado la relación _self_ para indicar que se trata de una referencia al recurso devuelto por la operación:

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"orderID":3,"productID":2,"quantity":4,"orderValue":16.60,"links":[{"rel":"self","href":" http://adventure-works.com/orders/3", "action":"GET","types":["text/xml","application/json"]},{"rel":" self","href":" http://adventure-works.com /orders/3", "action":"PUT","types":["application/x-www-form-urlencoded"]},{"rel":"self","href":" http://adventure-works.com /orders/3", "action":"DELETE","types":[]},{"rel":"customer",
"href":" http://adventure-works.com /customers/3", "action":"GET","types":["text/xml","application/json"]},{"rel":" customer" (customer links omitted)}]}
```

Para que este enfoque sea efectiva, aplicaciones cliente deben estar preparadas para recuperar y analizar esta información adicional.

## <a name="versioning-a-restful-web-api"></a>Control de versiones de una API de REST de web

No es muy probable que en todas las pero más sencillo de situaciones que un sitio web API permanecerá estático. Como requisitos empresariales cambian nuevas colecciones de recursos se pueden agregar, pueden cambiar las relaciones entre recursos y la estructura de los datos de recursos puede modificarse. Aunque actualizar un web API para controlar los requisitos de nuevos o diferentes es un proceso relativamente sencillo, debe tener en cuenta los efectos que tengan dichos cambios en aplicaciones cliente de consumo de la API de web. El problema es que aunque el desarrollador diseñar e implementar un sitio web API tiene control total sobre dicha API, el desarrollador no tiene el mismo grado de control sobre las aplicaciones cliente que puede generarse por las organizaciones de terceros funcionan de forma remota. Es fundamental principal habilitar las aplicaciones cliente existentes continuar funcionando sin cambios permitiendo nuevo cliente aplicaciones aprovechar las nuevas características y recursos.

Control de versiones permite un web API para indicar las características y recursos que expone y una aplicación de cliente puede enviar solicitudes que se dirigen a una versión específica de una característica o un recurso. Las secciones siguientes describen varios enfoques diferentes, cada una de las cuales tiene sus propias ventajas y desventajas.

### <a name="no-versioning"></a>Sin control de versiones

Este es el enfoque más sencillo y puede ser aceptable para algunas API interno. Cambios grandes pueden representarse como nuevos recursos o vínculos nuevo.  Agregar contenido a los recursos existentes no puede presentar un cambio importante como las aplicaciones cliente que no espera para ver que simplemente pasará por alto este contenido.

Por ejemplo, una solicitud para la URI _http://adventure-works.com/customers/3_ debe devolver los detalles de un cliente que contiene `id`, `name`, y `address` campos esperados por la aplicación cliente:

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

> [AZURE.NOTE] Los fines de claridad y simplificar las respuestas de ejemplo que se muestran en esta sección no incluye vínculos HATEOAS.

Si el `DateCreated` campo se agrega el esquema de los recursos de cliente, a continuación, la respuesta tendría un aspecto similar a este:

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":"1 Microsoft Way Redmond WA 98053"}
```

Aplicaciones cliente existentes pueden seguir funciona correctamente si son capaces de omitir campos no reconocidos, mientras nuevas aplicaciones cliente pueden estar diseñadas para controlar este nuevo campo. Sin embargo, si se producen cambios más radicales en el esquema de recursos (por ejemplo, quitar o cambiar el nombre de los campos) o cambian las relaciones entre recursos estos pueden constituir cambios importantes que impiden que aplicaciones cliente existente funciona correctamente. En estos casos debe considerar uno de los siguientes enfoques.

### <a name="uri-versioning"></a>Control de versiones URI

Cada vez que se modifica la web API o cambiar el esquema de recursos, agregue un número de versión al URI para cada recurso. Los URI previamente existentes deben seguir funcionando como antes, devolver los recursos que se ajusten a su esquema original.

Ampliar el ejemplo anterior, si la `address` campo es reestructurar en campos subcarpetas que contiene cada componente de la dirección (como `streetAddress`, `city`, `state`, y `zipCode`), esta versión del recurso se podría expone a través de un URI que contiene un número de versión, como http://adventure-works.com/v2/customers/3:

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":{"streetAddress":"1 Microsoft Way","city":"Redmond","state":"WA","zipCode":98053}}
```

Este mecanismo de control de versiones es muy sencillo, pero depende del servidor de enrutamiento de la solicitud al extremo correspondiente. Sin embargo, puede resultar difícil como web que API venza a través de varias iteraciones y el servidor tiene a un número de versiones diferentes de soporte técnico. También, desde el punto de vista de una purista en todos los casos las aplicaciones cliente obtienen los mismos datos (cliente 3), por lo que el URI no debe ser realmente diferente dependiendo de la versión. Este esquema también dificulta la implementación de HATEOAS todos los vínculos se necesite incluir el número de versión en sus identificadores URI.

### <a name="query-string-versioning"></a>Control de versiones de cadena de consulta

En lugar de proporcionar a varios URI, puede especificar la versión del recurso mediante un parámetro dentro de la cadena de consulta que se anexa a la solicitud HTTP, como _http://adventure-works.com/customers/3?version=2_. El parámetro de versión, de forma predeterminada un significativo valor como 1 si se omite aplicaciones cliente de versiones anteriores.

Este enfoque tiene la ventaja de semántica que el mismo recurso siempre se recupera desde el mismo URI, pero depende el código que controla la solicitud para analizar la cadena de consulta y volver a enviar la respuesta HTTP adecuada. Este método también tiene el mismas complicaciones para implementar HATEOAS como mecanismo de control de versiones URI.

> [AZURE.NOTE] Algunos exploradores web y los servidores proxy web no se almacenan en caché respuestas para las solicitudes que incluyen una cadena de consulta en la dirección URL. Esto puede tener un impacto negativo en el rendimiento para aplicaciones web que usan un web API y que se ejecutan desde como un explorador web.

### <a name="header-versioning"></a>Control de versiones de encabezado

En lugar de anexar el número de versión como un parámetro de cadena de consulta, podría implementar un encabezado personalizado que indica la versión del recurso. Este enfoque requiere que la aplicación cliente agrega el encabezado correspondiente a las solicitudes, aunque el código de tratamiento de la solicitud del cliente puede utilizar un valor predeterminado (versión 1) si se omite el encabezado de versión. Los ejemplos siguientes utilizan un encabezado personalizado denominado _Encabezado personalizado_. El valor de este encabezado indica la versión de web API.

Versión 1:

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
...
Custom-Header: api-version=1
...
```

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

Versión 2:

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
...
Custom-Header: api-version=2
...
```

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","dateCreated":"2014-09-04T12:11:38.0376089Z","address":{"streetAddress":"1 Microsoft Way","city":"Redmond","state":"WA","zipCode":98053}}
```

Tenga en cuenta que al igual que con los dos métodos anteriores, implementar HATEOAS requiere incluyendo el encabezado personalizado adecuado en los vínculos.

### <a name="media-type-versioning"></a>Control de versiones de tipo de medio

Cuando una aplicación cliente envía una solicitud HTTP GET a un servidor web debe precisar el formato del contenido que puede controlar utilizando un encabezado Accept, tal como se describió anteriormente en esta guía. Con frecuencia del encabezado _Accept_ sirve para permitir que la aplicación cliente para especificar si el cuerpo de la respuesta debe ser XML, JSON o algún otro formato comunes que puede analizar el cliente. Sin embargo, es posible definir tipos de medios personalizados que incluyen información que permita la aplicación cliente indicar qué versión de un recurso que se espera. En el ejemplo siguiente se muestra una solicitud que especifica un encabezado _Accept_ con el valor _application/vnd.adventure-works.v1+json_. El elemento de _vnd.adventure works.v1_ indica que el servidor web que se debe devolver versión 1 del recurso, mientras que el elemento _json_ especifica que el formato del cuerpo de la respuesta debe ser JSON:

```HTTP
GET http://adventure-works.com/customers/3 HTTP/1.1
...
Accept: application/vnd.adventure-works.v1+json
...
```

El código que controla la solicitud es responsable de procesar el encabezado _Accept_ y respetar en lo posible (la aplicación cliente puede especificar varios formatos en el encabezado de _Aceptar_ , en el caso del servidor web puede elegir el formato más adecuado para el cuerpo de la respuesta). El servidor web confirma el formato de los datos en el cuerpo de la respuesta mediante el encabezado de tipo de contenido:

```HTTP
HTTP/1.1 200 OK
...
Content-Type: application/vnd.adventure-works.v1+json; charset=utf-8
...
Content-Length: ...
{"id":3,"name":"Contoso LLC","address":"1 Microsoft Way Redmond WA 98053"}
```

Si el encabezado Accept no especificar los tipos de medios conocidos, el servidor web puede generar un mensaje de respuesta de HTTP 406 (no aceptable) o devolver un mensaje con un tipo de medio predeterminado.

Este enfoque es posiblemente la más puros de los mecanismos de control de versiones y permite naturalmente HATEOAS, lo cual puede incluir el tipo MIME de datos relacionados en los vínculos de recursos.

> [AZURE.NOTE] Al seleccionar una estrategia de control de versiones, también debe considerar las implicaciones en el rendimiento, especialmente almacenamiento en caché en el servidor web. Las combinaciones de control de versiones de cadena de consulta y control de versiones URI son fáciles de caché en la medida en la misma combinación de cadena URI o consulta hace referencia a los mismos datos cada vez.

> El control de versiones de encabezado y mecanismos de control de versiones de tipo de medio normalmente requieren lógica adicional para examinar los valores en el encabezado personalizado o la aceptación. En un entorno a gran escala, muchos clientes con diferentes versiones de una API de web pueden dar lugar a una gran cantidad de datos duplicados en una caché del servidor. Este problema puede suponer agudo si una aplicación cliente se comunica con un servidor web mediante un servidor proxy que implementa la memoria caché y que solo reenvía una solicitud al servidor web si no contiene actualmente una copia de los datos solicitados en la memoria caché.

## <a name="more-information"></a>Más información

- El [Libro de cocina REST](http://restcookbook.com/) contiene una introducción a la API de REST de creación.
- La [Lista de comprobación de API](https://mathieu.fenniak.net/the-api-checklist/) de Web contiene una lista de elementos para tener en cuenta al diseñar e implementar una API Web útil.
