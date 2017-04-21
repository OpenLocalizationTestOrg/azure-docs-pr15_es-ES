<properties
   pageTitle="Guía para crear un servicio de datos para el catálogo de soluciones | Microsoft Azure"
   description="Instrucciones detalladas sobre cómo crear, certificar e implementar un servicio de datos para comprar en Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="08/26/2016"
      ms.author="hascipio; avikova" />

# <a name="understanding-the-nodes-schema-for-mapping-an-existing-web-service-to-odata-through-csdl"></a>Comprender el esquema de nodos de asignación de un servicio web existente a OData a través de CSDL

>[AZURE.IMPORTANT] **En este momento estamos ya no integrado cualquier nuevas publicaciones de servicio de datos. No se obtener aprobado dataservices nueva lista.** Si tiene una aplicación de empresa SaaS que desea publicar en AppSource puede encontrar más información [aquí](https://appsource.microsoft.com/partners). Si tiene un aplicaciones IaaS o servicio de programador que desea publicar en Azure Marketplace puede encontrar más información [aquí](https://azure.microsoft.com/marketplace/programs/certified/).

Este documento le ayudará a clarificar la estructura de nodo para asignar un Protocolo OData a CSDL. Es importante tener en cuenta que la estructura de nodo está bien XML con formato incorrecto. Por esquema raíz, primarios y secundarios es aplicable al diseñar su asignación de OData.

## <a name="ignored-elements"></a>Elementos omitidos
Los siguientes son los elementos CSDL de alto niveles (nodos XML) que no va a ser usada por el servidor de Azure Marketplace durante la importación de metadatos del servicio web. Pueden estar presentes, pero se omitirá.

| Elemento | Ámbito |
|----|----|
| Uso del elemento | El nodo, los nodos secundarios y todos los atributos |
| Elemento de documentación | El nodo, los nodos secundarios y todos los atributos |
| ComplexType | El nodo, los nodos secundarios y todos los atributos |
| Elemento de asociación | El nodo, los nodos secundarios y todos los atributos |
| Propiedad extendida | El nodo, los nodos secundarios y todos los atributos |
| EntityContainer | Se pasan por alto sólo los siguientes atributos: *extiende* y *AssociationSet* |
| Esquema | Se pasan por alto sólo los siguientes atributos: *Namespace* |
| FunctionImport | Se pasan por alto sólo los siguientes atributos: *modo* (el valor predeterminado de ln se asume) |
| EntityType | Se pasan por alto los siguientes nodos secundarios: *clave* y *PropertyRef* |

La siguiente describe los cambios (agregado y omite elementos) a distintos de los nodos XML de CSDL en detalle.

## <a name="functionimport-node"></a>Nodo de FunctionImport
Nodo FunctionImport representa una dirección URL (punto de entrada) que expone un servicio para el usuario final. El nodo permite que describe cómo se destina la dirección URL, se proporcionan los parámetros que están disponibles para el usuario final y cómo estos parámetros.

Detalles sobre este nodo se encuentran [aquí] [MSDNFunctionImportLink]

[MSDNFunctionImportLink]: (https://msdn.microsoft.com/library/cc716710 (v=vs.100).aspx)

Los siguientes son atributos adicionales (o las adiciones a atributos) que se exponen por el nodo FunctionImport:

**d:baseUri** -
plantilla de URI del recurso de resto se expone a Marketplace. Catálogo de soluciones utiliza la plantilla para crear consultas en el servicio web REST. La plantilla URI contiene marcadores de posición de los parámetros en el formulario de {parameterName}, donde parameterName es el nombre del parámetro. P. ej. apiVersion = {apiVersion}.
Parámetros pueden aparecer como parámetros URI o como parte de la ruta de acceso URI. En el caso de la apariencia de la ruta de acceso siempre son obligatorios (no se puede marcar como nullable). *Ejemplo:*`d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**Nombre** : el nombre de la función importada.  No puede ser el mismo que otros nombres definidos en el CSDL.  P. ej. Nombre = "GetModelUsageFile"

**EntitySet** *(opcional)* : si la función devuelve una colección de tipos de entidad, el valor del **EntitySet** debe ser la entidad conjunto al que pertenece la colección. El atributo **EntitySet** en caso contrario, no se debe utilizar. *Ejemplo:*`EntitySet="GetUsageStatisticsEntitySet"`

**ReturnType** *(Opcional)* : especifica el tipo de elementos devueltos por el URI.  No utilice este atributo si la función no devuelve un valor. Los siguientes son los tipos compatibles:

 - **Colección (<Entity type name>)**: especifica una colección de tipos de entidad definidos. El nombre está presente en el atributo Name del nodo EntityType. Un ejemplo es colección (WXC. HourlyResult).
 - **Materia (<mime type>)**: especifica un documento o blob sin formato que se devuelve al usuario. Un ejemplo es Raw(image/jpeg) otros ejemplos:

  - ReturnType="Raw(text/plain)"
  - ReturnType = "colección (sage. DeleteAllUsageFilesEntity) "*

**d:Paging** - especifica cómo trata el recurso resto paginación. Los valores de parámetro se utilizan dentro de bifurcaciones llaves, por ejemplo, página = {$page} & itemsperpage = {$size} las opciones disponibles son:

- **Ninguno:** ninguna paginación está disponible
- **Omitir:** paginación se expresa mediante una lógica "saltar" y "tomar" (arriba). Saltar comía M elementos y tomar devuelve los elementos de N siguientes. El valor del parámetro: $skip
- **Tomar:** Tomar devuelve los elementos de N siguientes. El valor del parámetro: $take
- **PageSize:** paginación se expresa a través de una página lógica y tamaño (elementos por página). Página representa la página actual que se devuelve. El valor del parámetro: $page
- **Tamaño:** tamaño representa el número de elementos devueltos para cada página. El valor del parámetro: $size

**d:AllowedHttpMethods** *(Opcional)* - especifica el verbo que controla el recurso de resto. Además, restringe verbo aceptada en el valor especificado.  Predeterminado = POST.  *Ejemplo:* `d:AllowedHttpMethods="GET"` Las opciones disponibles son:

- **Obtener:** normalmente se utiliza para devolver datos
- **POST:** normalmente se usa para insertar nuevos datos
- **Poner:** normalmente se utiliza para actualizar datos
- **Eliminar:** se utiliza para eliminar datos

Nodos secundarios adicionales (no cubiertos por la documentación de CSDL) en el nodo FunctionImport son:

**d:RequestBody** *(Opcional)* , el cuerpo de la solicitud se utiliza para indicar que la solicitud espera un cuerpo que se envíen. Parámetros pueden especificarse dentro del cuerpo de la solicitud. Se expresan dentro de llaves, por ejemplo, {parameterName}. Estos parámetros se asignan desde la entrada del usuario en el cuerpo que se transfiere al servicio del proveedor de contenido. El elemento requestBody tiene un atributo con nombre httpMethod. El atributo permite dos valores:

- **POST:** Utilizar si la convocatoria es una publicación de HTTP
- **Obtener:** Utilizar si la solicitud HTTP GET

    Ejemplo:

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:Namespaces** y **d:Namespace** - este nodo describe los espacios de nombres que se definen en el XML devuelto por la función de importación (URI del extremo). El XML devuelto por el servicio de back-end puede contener cualquier número de espacios de nombres para diferenciar el contenido que se devuelve. **Todos estos espacios de nombres, si se usan en las consultas XPath d:Map o d:Match necesitan figurar.** El nodo d:Namespaces contiene una conjunto o lista de nodos de d:Namespace. Cada uno de ellos muestra un espacio de nombres utilizado en la respuesta del servicio de back-end. Los siguientes son el atributo de nodo de d:Namespace:

-   **d:Prefix:** Prefijo del espacio de nombres, tal como se muestra en los resultados XML devueltos por el servicio, por ejemplo, f:FirstName, f:LastName, donde f es el prefijo.
- **d:Uri:** URI completo del espacio de nombres utilizado en el documento resultante. Representa el valor que se resuelve el prefijo en tiempo de ejecución.

**d:ErrorHandling** *(Opcional)* - este nodo contiene condiciones de control de errores. Cada una de las condiciones se valida con el resultado devuelto por el servicio del proveedor de contenido. Si una condición coincide con el código de error HTTP propuesto se devuelve un mensaje de error para el usuario final.

**d:ErrorHandling** *(Opcional)* y **d:Condition** *(opcional)* , un nodo de condición contiene una condición que se comprueba en el resultado devuelto por el servicio del proveedor de contenido. Los siguientes son los atributos **necesarios** :

- **d:Match:** Expresión XPath que valida si está presente en el resultado del proveedor de contenido XML un nodo o valor dado. La expresión XPath se ejecuta en el resultado y debe devolver true si la condición es una coincidencia o falso en caso contrario.
- **d:HttpStatusCode:** Coincide con el código de estado HTTP que se debe devolver al mercado en el caso de la condición. Catálogo de soluciones de signalizes errores al usuario mediante códigos de estado HTTP. Una lista de códigos de estado HTTP están disponibles en http://en.wikipedia.org/wiki/HTTP_status_code
- **d:ErrorMessage:** El mensaje de error devuelto: con el código de estado HTTP: para el usuario final. Debe ser un mensaje de error descriptivos que no contiene ningún secreto.

**d:Title** *(Opcional)* : permite que describe el título de la función. El valor para el título es procedentes de

- El atributo de mapa opcional (una expresión xpath) que especifica dónde se encuentra el título en la respuesta devuelta desde la solicitud de servicio.
- - O bien-el título especificado como valor del nodo.

**d:Rights** *(Opcional)* - los derechos (por ejemplo, copyright) asociados a la función. El valor de los derechos se procedentes de:

- El atributo de mapa opcional (una expresión xpath) que especifica dónde encontrar los derechos en la respuesta devuelta desde la solicitud de servicio.
-   - O bien-los derechos especificados como valor del nodo.

**d:Description** *(Opcional)* : una breve descripción para la función. El valor de la descripción proviene de

- El atributo de mapa opcional (una expresión xpath) que especifica dónde encontrar la descripción de la respuesta devuelta por la solicitud de servicio.
- - O bien-la descripción especificada como valor del nodo.

**d:EmitSelfLink** - *Ver ejemplo anterior "FunctionImport 'Paginación' de los datos devueltos"*

**d:EncodeParameterValue** - extensión opcional de OData

**d:QueryResourceCost** - extensión opcional de OData

**d:Map** - extensión opcional de OData

**d:Headers** - extensión opcional de OData

**d:Headers** - extensión opcional de OData

**d:Value** - extensión opcional de OData

**d:HttpStatusCode** - extensión opcional de OData

**d:ErrorMessage** - extensión opcional de OData

## <a name="parameter-node"></a>Nodo de parámetro

Este nodo representa un parámetro que se expone como parte de la plantilla URI o solicitar cuerpo que se ha especificado en el nodo FunctionImport.

Una página de documento muy útil detalles sobre el nodo "Parámetro elemento" se encuentra en [aquí](http://msdn.microsoft.com/library/ee473431.aspx) (Use la **Otra versión de** la lista desplegable para seleccionar una versión diferente si es necesario para ver la documentación). *Ejemplo:*`<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| Atributo de parámetro | Se requiere | Valor |
|----|----|----|
| Nombre | Sí | El nombre del parámetro. Distinción entre mayúsculas y minúsculas.  Coincidir con el caso de BaseUri. **Ejemplo:**`<Property Name="IsDormant" Type="Byte" />` |
| Tipo | Sí | El tipo de parámetro. El valor debe ser un **EDMSimpleType** o un tipo complejo en el ámbito del modelo. Para obtener más información, vea "tipos de propiedad o parámetro compatibles con 6".  (Distingue entre mayúsculas y minúsculas. Primer carácter está en mayúscula, resto son minúsculas).  Vea también, [conceptuales modelo tipos (CSDL)] [MSDNParameterLink]. **Ejemplo:**`<Property Name="LimitedPartnershipID " Type="Int32" />` |
| Modo | No | **En**, o entrada dependiendo de si el parámetro es una entrada, salida o parámetro de entrada y salida. ("IN" solo está disponible en Azure Marketplace.) **Ejemplo:**`<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| MaxLength | No | Número máximo permitido de longitud del parámetro. **Ejemplo:**`<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| Precisión | No | La precisión del parámetro. **Ejemplo:**`<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| Escala | No | La escala del parámetro. **Ejemplo:**`<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

[MSDNParameterLink]: (http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx)

Los siguientes son los atributos que se han agregado a la especificación de CSDL:

| Atributo de parámetro | Descripción |
|----|----|
| **d:Regex** *(Opcional)* | Una instrucción de regex utilizada para validar el valor de entrada para el parámetro. Si el valor de entrada no coincide con la instrucción se rechaza el valor. Esto le permite para especificar también un conjunto de valores posibles, por ejemplo, ^ [0-9] +? $ para permitir que solo los números. **Ejemplo:** ' < nombre de parámetro = "nombre" modo = "En" tipo = "Cadena" d: Nullable = "false" d:Regex = "^ [a-zA-Z] * $" d:Description = "un nombre que no puede contener espacios ni caracteres de inglés no alfabéticos" d:SampleValues = "George|John|Thomas|James "/ >' |
| **d:enum** *(Opcional)* | Lista de valores válidos para el parámetro de separados de una canalización. El tipo de los valores debe coincidir con el tipo del parámetro definido. Ejemplo: ' inglés|métrica|sin formato`. Enum will display as a selectable dropdown list of parameters in the UI (service explorer). **Example:** `< nombre de parámetro = "Duración" tipo = "Cadena" modo = "En" Nullable = "true" d:Enum = "1 año|5years|10years "/ >' |
| **d.: Nullable** *(Opcional)* | Permite definir si un parámetro puede ser null. El valor predeterminado es: true. Sin embargo, los parámetros que se exponen como parte de la ruta de acceso en la plantilla URI no pueden ser null. Cuando el atributo se establece en falso para estos parámetros: se reemplaza la entrada del usuario. **Ejemplo:**`<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue** *(Opcional)* | Un valor de ejemplo para mostrar como una nota al cliente en la interfaz de usuario.  Es posible agregar varios valores con una lista de canalización separado, es decir, ' un|b|c` **Example:** `< nombre de parámetro = "BikeOwner" tipo = "Cadena" modo = "En" d:SampleValues = "George|John|Thomas|James "/ >' |

## <a name="entitytype-node"></a>Nodo de EntityType

Este nodo representa uno de los tipos que se devuelven desde el catálogo de soluciones para el usuario final. También contiene la asignación en el resultado devuelto por el servicio del proveedor de contenido a los valores devueltos para el usuario final.

Detalles sobre este nodo se encuentran en [aquí](http://msdn.microsoft.com/library/bb399206.aspx) (Use la **Otra versión de** la lista desplegable para seleccionar una versión diferente si es necesario para ver la documentación).

| Nombre del atributo | Se requiere | Valor |
|----|----|----|
| Nombre | Sí | El nombre del tipo de entidad. **Ejemplo:**`<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| BaseType | No | El nombre de otro tipo de entidad que es el tipo base del tipo de entidad que se define. **Ejemplo:**`<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

Los siguientes son los atributos que se han agregado a la especificación de CSDL:

**d:Map** - expresión XPath ejecutada el resultado del servicio. Esto supone que la salida de servicio contiene un conjunto de elementos que se repiten, como una ATOM fuentes dónde hay un conjunto de nodos de entrada que se repiten. Cada uno de estos nodos de repetición contiene un registro. A continuación, se especifica la expresión XPath al punto en el nodo de repetición individual en un resultado de servicio del proveedor de contenido que contiene los valores de un registro individual. Ejemplo de salida del servicio:

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

La expresión XPath podría ser /foo/barra porque cada de la barra de nodo es el nodo de repetición en el resultado y que contiene el contenido real que se devuelve para el usuario final.

**Tecla** : este atributo omite Marketplace. Coloque los servicios basados en web, en general no exponer una clave principal.


## <a name="property-node"></a>Nodo de propiedad

Este nodo contiene una propiedad del registro.

Detalles sobre este nodo se encuentran en [http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx) (Use la **Otra versión de** la lista desplegable para seleccionar una versión diferente si es necesario para ver la documentación). *Ejemplo:*
        `<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name"   Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
        ...
        </EntityType>`

| AttributeName | Obligatorio | Valor |
|----|----|----|
| Nombre | Sí | El nombre de la propiedad. |
| Tipo | Sí | El tipo de valor de la propiedad. El tipo de valor de propiedad debe ser un **EDMSimpleType** o un tipo complejo (indicado por un nombre completo) que está dentro del ámbito del modelo. Para obtener más información, vea tipos de modelo Conceptual (CSDL). |
| Nullable | No | **True** (el valor predeterminado) o **Falso** dependiendo de si la propiedad puede tener un valor nulo. Nota: En la versión de CSDL indicado por el espacio de nombres [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm) , una propiedad de tipo complejo debe tener Nullable = "False". |
| DefaultValue | No | El valor predeterminado de la propiedad. |
|MaxLength | No | La longitud máxima del valor de propiedad. |
| FixedLength | No | **Verdadero** o **Falso** dependiendo de si el valor de propiedad se almacenará como una cadena de longitud fiexed. |
| Precisión | No | Se refiere a la cantidad máxima de dígitos para conservar el valor numérico. |
| Escala | No | Número máximo de posiciones decimales para conservar el valor numérico. |
| Unicode | No | **Verdadero** o **Falso** dependiendo de si el valor de propiedad ser almacenado como una cadena Unicode. |
| Intercalación | No | Una cadena que especifica la secuencia de ordenación que se utilizará en el origen de datos. |
| ConcurrencyMode | No | **Ninguno** (el valor predeterminado) o **fijo**. Si el valor se establece como **fijo**, el valor de propiedad se usará en las comprobaciones de simultaneidad optimista. |

Los siguientes son los atributos adicionales que se han agregado a la especificación de CSDL:

**d:Map** - expresión XPath ejecutada en el servicio de salida y extrae una propiedad de salida. Es el XPath especificado en relación con el nodo de repetición que se ha seleccionado en XPath del nodo EntityType. También es posible especificar una expresión XPath absoluta para permitir incluyendo un recurso estático en cada uno de los nodos de salida, como por ejemplo una declaración de copyright solo se encuentra una vez en el servicio original de salida, pero debe aparecer en cada una de las filas en el resultado de OData. Ejemplo del servicio:

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

La expresión XPath aquí sería ./bar/baz0 para obtener el nodo baz0 de servicio del proveedor de contenido.

**d:CharMaxLength** - tipo de cadena, puede especificar la longitud máxima. Vea Ejemplo DataService CSDL

**d:IsPrimaryKey** - indica si la columna es la clave principal en la vista de tabla. Vea Ejemplo DataService CSDL.

**d:isExposed** - determina si se expone el esquema de la tabla (generalmente true). Vea Ejemplo DataService CSDL

**d:IsView** *(Opcional)* - true si se basa en una vista, en lugar de una tabla.  Vea Ejemplo DataService CSDL

**d:Tableschema** : vea el ejemplo DataService CSDL

**d:ColumnName** - es el nombre de la columna en la vista de tabla.  Vea Ejemplo DataService CSDL

**d:IsReturned** - es el valor de tipo Boolean que determina si el servicio expone este valor al cliente.  Vea Ejemplo DataService CSDL

**d:IsQueryable** - es el valor de tipo Boolean que determina si se puede usar la columna en una consulta de base de datos.   Vea Ejemplo DataService CSDL

**d:OrdinalPosition** - es la posición de la columna numérica de apariencia, x, en la tabla o la vista, donde x es del 1 al número de columnas de la tabla.  Vea Ejemplo DataService CSDL

**d:DatabaseDataType** - es el tipo de datos de la columna de la base de datos, es decir, un tipo de datos SQL. Vea Ejemplo DataService CSDL

## <a name="supported-parametersproperty-types"></a>Tipos de propiedad o parámetros admitidos
Los siguientes son los tipos admitidos para los parámetros y propiedades. (Distingue entre mayúsculas y minúsculas)

| Tipos simples | Descripción |
|----|----|
| Null | Representa la ausencia de un valor |
| Valor booleano | Representa el concepto matemático de lógica de valores en binario.|
| Byte | Valor entero de 8 bits sin signo|
|Fecha y hora| Representa la fecha y hora con valores comprendidos entre la medianoche 12:00:00 del 1 de enero de 1753 D.C. a través de 11:59:59 P.M. diciembre de 9999 D.C.|
|Decimal | Representa valores numéricos con precisión y escala fijas. Este tipo puede describir un valor numérico comprendido entre 10 negativo ^ 255 + 1 a 10 positivo ^-255 1|
| Doble | Representa un punto flotante número con una precisión de 15 dígitos que puede representar valores con el intervalo aproximado de ± 2, 23E-308 hasta 1, 79E + 308. **Utilice Decimal debido a problemas de exportación de Excel**|
| Solo | Representa un punto flotante número con una precisión de 7 dígitos que puede representar valores con el intervalo aproximado de 1, 18E-38 ± a 3, 40E + 38|
|GUID |Representa un valor de identificador único de 16 bytes (128 bits) |
|Int16|Representa un valor entero de 16 bits |
|Int32|Representa un valor entero de 32 bits |
|Int64|Representa un valor entero de 64 bits |
|Cadena | Representa datos de caracteres de longitud fija o variable |


## <a name="see-also"></a>Vea también
- Si le interesa conocer el proceso de asignación de OData y propósito general, lea este artículo [Asignación de OData de servicio de datos](marketplace-publishing-data-service-creation-odata-mapping.md) para revisar las definiciones, estructuras e instrucciones.
- Si está interesado en ejemplos de revisiones, lea este artículo [Ejemplos de asignación de datos servicios OData](marketplace-publishing-data-service-creation-odata-mapping-examples.md) para ver código de ejemplo y comprender el contexto y la sintaxis de código.
- Para volver a la ruta de acceso recomendada para publicar un servicio de datos en el catálogo de soluciones de Azure, lea este artículo [Guía de publicación de servicio de datos](marketplace-publishing-data-service-creation.md).
