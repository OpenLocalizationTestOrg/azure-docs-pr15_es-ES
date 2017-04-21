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

# <a name="mapping-an-existing-web-service-to-odata-through-csdl"></a>Asignación de un servicio web existente a OData a través de CSDL

>[AZURE.IMPORTANT] **En este momento estamos ya no integrado cualquier nuevas publicaciones de servicio de datos. No se obtener aprobado dataservices nueva lista.** Si tiene una aplicación de empresa SaaS que desea publicar en AppSource puede encontrar más información [aquí](https://appsource.microsoft.com/partners). Si tiene un aplicaciones IaaS o servicio de programador que desea publicar en Azure Marketplace puede encontrar más información [aquí](https://azure.microsoft.com/marketplace/programs/certified/).

Este artículo ofrece información general sobre cómo usar un CSDL para asignar un servicio existente a un servicio compatible de OData. Explica cómo crear el documento de asignación (CSDL) transforma la solicitud de entrada desde el cliente a través de una llamada de servicio y el resultado (datos) devuelva al cliente a través de OData compatible de fuente. Azure Marketplace de Microsoft expone servicios a los usuarios finales mediante el protocolo de OData. Servicios que se exponen proveedores de contenido (datos propietarios) se exponen en una gran variedad de formularios, como el resto, SOAP, etcetera.

## <a name="what-is-a-csdl-and-its-structure"></a>¿Qué es un CSDL y su estructura?
Un CSDL (lenguaje de definición de esquema Conceptual) es una especificación definir cómo describir el servicio web o base de datos de texto XML comunes a Azure Marketplace.

Visión general sencilla de la **Solicitar flujo:**

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

El **flujo de datos** está en la dirección opuesta:

  `Client <- Azure Marketplace <- Content Provider’s WebService`

**Figura 1** diagramas cómo un cliente puede obtener datos de un proveedor de contenido (su servicio) a través de Azure Marketplace.  El componente de transformación de asignación o utiliza el CSDL para controlar la solicitud y pasan datos entre los servicios del proveedor de contenido y el cliente de la solicitud.

*Figura 1: Detallada flujo de solicitud de cliente de proveedor de contenido a través de Azure Marketplace*

  ![dibujo](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

Para información sobre Atom, Pub Atom y el protocolo de OData en los que las extensiones de Azure Marketplace integrar, consulte: [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

Extracto desde encima del vínculo:     *"es el objetivo del protocolo de datos abierta (en adelante denominado OData) proporcionar un protocolo basado en el resto de operaciones de estilo CRUD (crear, leer, actualizar y eliminar) con recursos que se exponen como servicios de datos. Un servicio de datos de"" es un extremo donde hay datos expuestos a partir de una o más "colecciones" cada con cero o más "entradas", que constan de escribió pares de valor con nombre. OData se publica Microsoft en estándares OASIS (organización para el avance de estructuradas estándares de información) para que todos los usuarios que desea pueden generar servidores, clientes o herramientas sin restricciones o derechos de autor."*

### <a name="three-critical-pieces-that-have-to-be-defined-by-the-csdl-are"></a>Tres partes esenciales que deben definirse por el CSDL son:

- El **extremo** del servicio proveedor de la Web dirección (URI) del servicio
- Los **parámetros de datos** que se pasa como entrada para el proveedor de servicios las definiciones de los parámetros que se envían al servicio de su proveedor de contenido hacia abajo hasta el tipo de datos.
- **Esquema** de los datos devueltos al servicio que solicita el esquema de los datos que se entregó el servicio del proveedor de contenido, incluidos el contenedor, colecciones o tablas, las variables o columnas y tipos de datos.

El siguiente diagrama muestra información general sobre el flujo desde donde el cliente entra en la instrucción de OData (llamada al servicio web de su proveedor de contenido) para obtener los datos de resultados.

  ![dibujo](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### <a name="steps"></a>Pasos siguientes:

1. Cliente envía la solicitud a través de la llamada al servicio con parámetros de entrada definidos en XML a Azure Marketplace
2. CSDL se usa para validar la llamada de servicio.
    - Formato de llamada de servicio, a continuación, se envía al servicio de proveedores de contenido por Azure Marketplace
3. El servicio Web se ejecuta y valida la acción del verbo Http (es decir, GET) los datos se devuelven a Azure Marketplace, donde los datos solicitados (si existe) están expone en formato XML en el cliente utilizando la asignación definida en el CSDL.
4. Se envía al cliente los datos (si existe) en formato XML o JSON

## <a name="definitions"></a>Definiciones

### <a name="odata-atom-pub"></a>Pub ATOM de OData

Establece una extensión de pub ATOM donde cada entrada representa una fila de un resultado. El elemento de contenido de la entrada se ha mejorado para que contenga los valores de la fila, como pares de valores clave. Más información se encuentra aquí: [https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### <a name="csdl---conceptual-schema-definition-language"></a>CSDL - lenguaje de definición de esquema Conceptual

Permite definir funciones (procedimientos almacenados) y entidades que se exponen a través de una base de datos. Más información aquí: [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)  

> [AZURE.TIP] Haga clic en la **otras versiones de** la lista desplegable y seleccione una versión si no ve el artículo.

### <a name="edm---entry-data-model"></a>EDM - modelo de datos de entrada
- Información general: [http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx][OverviewLink] [OverviewLink]: http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx
- Vista previa: [http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx][PreviewLink] [PreviewLink]: http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx
- Tipos de datos: [http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx][DataTypesLink] [DataTypesLink]: http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx

Realizar copia de la siguiente muestra la detallada de izquierda a derecha fluya desde donde el cliente entra en la instrucción de OData (llamada al servicio web de su proveedor de contenido) para obtener los datos de resultados:

  ![dibujo](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)


## <a name="csdl-basics"></a>Conceptos básicos CSDL

Un CSDL (lenguaje de definición de esquema Conceptual) es una especificación definir cómo describir el servicio web o base de datos de texto XML comunes a Azure Marketplace. Describe la crítica CSDL piezas que **permite la transferencia de datos desde el origen de datos a Azure Marketplace.** A continuación, se describen las partes principales:

- Información de la interfaz que describe todas las funciones disponibles al público (FunctionImport nodo)
- Información de tipo de datos para todos los mensajes requests(input) y responses(outputs) mensaje (EntityContainer, EntitySet/EntityType nodos)
- Información sobre el protocolo de transporte como enlace utiliza (nodo de encabezado)
- Información de dirección para encontrar el servicio especificado (BaseURI atributo)

En resumen, el CSDL representa un contrato de independiente de la plataforma y el idioma entre el solicitante de servicio y el proveedor de servicios. Usa el CSDL, un cliente puede buscar un servicio de base de datos o servicio web e invocar cualquiera de sus funciones disponibles públicamente.

### <a name="relating-a-csdl-to-a-database-or-a-collection"></a>Relativas una CSDL a una base de datos o una colección
**La especificación de CSDL**

CSDL es gramática XML para describir un servicio web. La especificación de sí se divide en 4 elementos principales: EntitySet, FunctionImport; Espacio de nombres y EntityType.

Para realizar esta abstracción fáciles de entender le permite relacionar un CSDL a una tabla.

Recordar;

  CSDL representa un contrato independiente de la plataforma y el idioma entre el **solicitante del servicio** y el **proveedor de servicios**. Usar CSDL, un **cliente** puede buscar un **servicio de base de datos o servicio web** e invocar ninguno de sus disponible públicamente **funciones.**

Para un servicio de datos pueden considerar las cuatro partes de un CSDL en términos de una base de datos, la tabla, la columna y el procedimiento almacenado.

Relativas como sigue para un servicio de datos:

- EntityContainer ~ = base de datos
- EntitySet ~ = tabla
- EntityType ~ = columnas
- FunctionImport ~ = procedimiento almacenado

**Verbos HTTP permitidos**
- GET-devuelve valores de la base de datos (devuelve un conjunto)
- PUBLICACIÓN: se usa para pasar datos a y valores devueltos opcionales de la base de datos (crear una nueva entrada en la colección, el identificador o URI devuelto)
- ELIMINAR: elimina los datos de la base de datos (elimina una colección de)
- PONER: actualizar datos en una base de datos (reemplazar una colección o crear uno)

## <a name="metadatamapping-document"></a>Documento de metadatos o asignación

El documento o la asignación de metadatos se utiliza para asignar los servicios web existentes del proveedor de contenido para que se pueden exponer como servicio web OData por el sistema de Azure Marketplace. Se basa en CSDL e implementa unas extensiones CSDL para satisfacer las necesidades de descanso según los servicios web expuestos a través de Azure Marketplace. Las extensiones se encuentran en el espacio de nombres [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04) .

Un ejemplo de CSDL sigue: (copiar y pegar el ejemplo CSDL en un editor XML y cambiar para que coincida con el servicio siguiente.  Pegar en CSDL asignación en la ficha DataService al crear el servicio en el [Portal de publicación de Azure Marketplace](https://publish.windowsazure.com)).

**Términos:** Relacionados los términos CSDL con los términos de la interfaz de usuario (PPUI) de [Portal de publicación](https://publish.windowsazure.com) .
- Ofrecer "Título" en el PPUI se relaciona con MyWebOffer
- MyCompany en la PPUI está relacionado con el **Nombre de presentación de Publisher** en el [Centro para desarrolladores de Microsoft](http://dev.windows.com/registration?accountprogram=azure) de interfaz de usuario
- La API se relaciona con una Web o un servicio de datos (un Plan en la PPUI)

**Jerarquía:** 
 una empresa (proveedor de contenido) posee ofertas que tienen planes, es decir, service(s), que se alinean con una API.

### <a name="webservice-csdl-example"></a>Ejemplo de CSDL WebService

Se conecta a un servicio que expone un extremo de la aplicación web (por ejemplo, una aplicación de C#)

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
            <!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

            <!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID" d:IsPrimaryKey="True" Type="Int32"  Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount" Type="Double"   Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"   Type="String"   Nullable="false" d:Map="./City"/>
        <Property Name="State"  Type="String"   Nullable="false" d:Map="./State"/>
        <Property Name="Zip"    Type="Int32"    Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"    Type="DateTime" Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [AZURE.TIP] Ver más ejemplos de servicio Web de CSDL en el artículo [ejemplos de asignación de un servicio web existente a OData a través de CSDLs](marketplace-publishing-data-service-creation-odata-mapping-examples.md)

###<a name="dataservice-csdl-example"></a>Ejemplo de CSDL DataService

Se conecta a un servicio que expone una tabla de base de datos o vista como un extremo de ejemplo siguiente muestra que dos API de base de datos en función CSDL API (puede utilizar vistas en lugar de tablas).

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
            Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
            Map is the schema.tabel or schema.view
            dals.TableName is the table Name
            Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
            dals:IsExposed determines if the table schema is exposed (generally true).
            dals:IsView (optional) true if this is based on a view rather than a table
            dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
            dals:ColumnName is the name of the column in the table /view.
            Type is the emd.SimpleType
            Nullable determines if NULL is a valid output value
            dals.CharMaxLenght is the maximum length of the output value
            Name is the name of the Property and is exposed to the client facing UI
            dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
            IsQueryable is the Boolean that determines if the column can be used in a database query
            (For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
            dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
            dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## <a name="see-also"></a>Vea también
- Si le interesa aprender y comprender los nodos específicos y sus parámetros, lea este artículo [OData asignación nodos de servicio de datos](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) para definiciones y explicaciones, ejemplos y usar contexto mayúsculas y minúsculas.
- Si está interesado en ejemplos de revisiones, lea este artículo [Ejemplos de asignación de datos servicios OData](marketplace-publishing-data-service-creation-odata-mapping-examples.md) para ver código de ejemplo y comprender el contexto y la sintaxis de código.
- Para volver a la ruta de acceso recomendada para publicar un servicio de datos en el catálogo de soluciones de Azure, lea este artículo [Guía de publicación de servicio de datos](marketplace-publishing-data-service-creation.md).
