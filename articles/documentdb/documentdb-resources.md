<properties 
    pageTitle="DocumentDB conceptos y modelo de recursos jerárquica | Microsoft Azure" 
    description="Obtenga información sobre el modelo jerárquico de DocumentDB de bases de datos, colecciones, función definida por el usuario (UDF), documentos, permisos para administrar recursos y mucho más."
    keywords="Modelo jerárquico, documentdb, azure, Microsoft azure"   
    services="documentdb" 
    documentationCenter="" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="anhoh"/>

# <a name="documentdb-hierarchical-resource-model-and-concepts"></a>Conceptos y el modelo de recursos jerárquica DocumentDB

Las entidades de base de datos que administra DocumentDB se conocen como **recursos**. Cada recurso se identifica mediante un URI lógico. Puede interactuar con los recursos con verbos HTTP estándar, los encabezados de solicitud y respuesta y códigos de estado. 

Lea este artículo, podrá responder a las preguntas siguientes:

- ¿Qué es el modelo de recursos de DocumentDB?
- ¿Qué sistema definido recursos en lugar de recursos definidos por el usuario?
- ¿Cómo escribo direcciones en un recurso?
- ¿Cómo funcionan con colecciones?
- Funcionamiento con procedimientos almacenados, desencadenadores y funciones definidas por el usuario (UDF)

## <a name="hierarchical-resource-model"></a>Modelo de recursos jerárquica
Como se muestra en el diagrama siguiente, el **modelo de recursos** de DocumentDB jerárquica consta de conjuntos de recursos en una cuenta de base de datos, cada accesible a través de un URI lógico y estable. Un conjunto de recursos se se conoce como una **fuente** de este artículo. 

>[AZURE.NOTE] DocumentDB ofrece un protocolo TCP muy eficaces que también es RESTful en su modelo de comunicación disponible a través del [cliente de .NET SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx).

![Modelo de recursos jerárquica DocumentDB][1]  
**Modelo de recursos jerárquica**   

Para empezar a trabajar con recursos, debe [crear una cuenta de base de datos DocumentDB](documentdb-create-account.md) con su suscripción de Azure. Una cuenta de base de datos puede constar de un conjunto de **bases de datos**, que contiene varias **colecciones**, cada una de las cuales a su vez contener **procedimientos almacenados, desencadenadores, UDF, documentos** y relacionada con **datos adjuntos** (característica de vista previa). Una base de datos también tiene asociados **los usuarios**, cada uno con un conjunto de **permisos** para tener acceso a las colecciones, procedimientos almacenados, desencadenadores, UDF, documentos o archivos adjuntos. Aunque las bases de datos, los usuarios, permisos y colecciones son recursos definidos por el sistema con esquemas conocidos, documentos y datos adjuntos contengan arbitrario, contenido JSON definidas por el usuario.  

|Recursos   |Descripción
|-----------|-----------
|Cuenta de base de datos   |Una cuenta de base de datos está asociada con un conjunto de bases de datos y una cantidad fija de almacenamiento de blobs de datos adjuntos (característica de vista previa). Puede crear una o varias cuentas de base de datos con su suscripción de Azure. Para obtener más información, visite nuestra [página de precios](https://azure.microsoft.com/pricing/details/documentdb/).
|Base de datos   |Una base de datos es un contenedor lógico de dividir entre colecciones de almacenamiento de documentos. También es un contenedor de usuarios.
|Usuario   |Espacio de nombres lógico para especificar el ámbito de permisos. 
|Permisos |Un token de autorización asociado a un usuario para obtener acceso a un recurso específico.
|Colección |Una colección es un contenedor de documentos JSON y la lógica de la aplicación de JavaScript asociada. Una colección es una entidad facturable, donde se determina el [costo](documentdb-performance-levels.md) por el nivel de rendimiento asociado a la colección. Colecciones pueden abarcar uno o más particiones o servidores y pueden escalar para manejar volúmenes prácticamente ilimitados de almacenamiento o el rendimiento.
|Procedimiento almacenado   |Lógica de aplicación escrita en JavaScript que se registra con una colección y transacciones se ejecuta en el motor de base de datos.
|Desencadenador    |Lógica de aplicación escrita en JavaScript que se ejecutan antes o después de una instrucción insert, reemplazar o eliminar la operación.
|UDF    |Lógica de aplicación escrita en JavaScript. UDF le permiten modelar un operador de consulta personalizada y, con ello, ampliar el lenguaje de consulta de DocumentDB básico.
|Documento   |Contenido JSON (arbitrario) definidas por el usuario. De forma predeterminada, ningún esquema debe definirse ni ¿índices secundarios que se proporciona para todos los documentos que se agregan a una colección.
|(Vista previa) Datos adjuntos   |Un archivo adjunto es un documento especial que contienen referencias y metadatos asociados externo blob/multimedia. El desarrollador puede tener el blob administrado por DocumentDB o guardarla con un proveedor de servicios externos blob como OneDrive, Dropbox, etcetera. 


## <a name="system-vs-user-defined-resources"></a>Sistema frente a recursos de definidas por el usuario
Recursos como cuentas de base de datos, bases de datos, colecciones, los usuarios, permisos, procedimientos almacenados, desencadenadores y UDF - tienen un esquema fijo y se denominan recursos del sistema. Por el contrario, recursos, como documentos y datos adjuntos no tienen restricciones en el esquema y ejemplos de recursos definidos por el usuario. En DocumentDB, sistema y definidas por el usuario se representan y se administran como JSON estándar compatible con recursos. Todos los recursos, sistema o usuario definido, tienen las siguientes propiedades comunes.

> [AZURE.NOTE] Prefijo de subrayado (_) en su representación JSON Nota propiedades generadas sistema todas en un recurso.

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>(Propiedad)</strong></p></td>
            <td valign="top"><p><strong>¿Puede establecer el usuario o generados por el sistema?</strong></p></td>
            <td valign="top"><p><strong>Propósito</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>Generados por el sistema</p></td>
            <td valign="top"><p>Generados por el sistema, identificador único y jerárquico del recurso</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>Generados por el sistema</p></td>
            <td valign="top"><p>eTag del recurso necesario para el control de simultaneidad optimista</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>Generados por el sistema</p></td>
            <td valign="top"><p>Último actualizado marca de tiempo del recurso</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_self</p></td>
            <td valign="top"><p>Generados por el sistema</p></td>
            <td valign="top"><p>URI accesible único del recurso</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Id.</p></td>
            <td valign="top"><p>Generados por el sistema</p></td>
            <td valign="top"><p>Nombre único del recurso (con el mismo valor de clave de partición) definidas por el usuario. Si el usuario no especifica un id, un identificador será generados por el sistema</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>Representación de alambre de recursos
DocumentDB no exige las extensiones de propietarias a las codificaciones JSON estándar o especiales; funciona con documentos JSON compatibles con estándar.  
 
### <a name="addressing-a-resource"></a>Escribir direcciones en un recurso
Todos los recursos están URI accesible. El valor de la propiedad **_self** de un recurso representa el URI relativo del recurso. El formato del URI consta de la /\<fuente\>/ {_rid} segmentos de trazado:  

|Valor de la _self |Descripción
|-------------------|-----------
|/DBS   |Fuente de bases de datos en una cuenta de base de datos
|/DBS/ {dbName}  |Base de datos con un identificador que coincide con el valor {dbName}
|/colls/ /DBS/ {dbName}   |Fuente de colecciones en una base de datos
|/colls/ /DBS/ {dbName} {collName} |Colección con un identificador que coincide con el valor de {collName}
|/colls/ /DBS/ {dbName} {collName} o documentos    |Fuente de documentos en una colección
|/DBS/ {dbName} /colls/ {collName} /docs/ {ID de doc}    |Documento con un identificador que coincide con el valor {doc}
|/DBS/ {dbName} / Users /   |Fuente de usuarios en una base de datos
|/DBS/ {dbName} / Users / {Id}   |Usuario con un identificador que coincida con el valor {usuario}
|/DBS/ {dbName} / Users / {ID} / permisos   |Fuente de permisos de un usuario
|/DBS/ {dbName} / Users / {ID} /permissions/ {permissionId}    |Permisos con un identificador que coincida con el valor {permiso}
  
Cada recurso tiene un nombre definido por el usuario único expone a través de la propiedad id. Nota: para documentos, si el usuario no especifica un Id., nuestro SDK compatible generará automáticamente un identificador único para el documento. El identificador es una cadena de definidas por el usuario, de hasta 256 caracteres que sea único en el contexto de un recurso primario específico. 

Cada recurso tiene también un identificador de recursos jerárquica generados por el sistema (también contemplado como un RID), que está disponible a través de la propiedad _rid. EL codifica toda la jerarquía de un recurso específico y es una representación interna adecuada utilizada para exigir la integridad referencial de manera distribuida. EL es único dentro de una cuenta de base de datos y se usa internamente por DocumentDB para enrutamiento eficaz sin necesidad de búsquedas de partición cruzada. Los valores de la _self y las propiedades de _rid son representaciones alternativos y canónicos de un recurso. 

Las API de REST DocumentDB admite direccionamiento de recursos y el enrutamiento de solicitudes por el número de identificación y las propiedades de _rid.

## <a name="database-accounts"></a>Cuentas de base de datos
Puede proporcionar una o varias cuentas de base de datos de DocumentDB con su suscripción de Azure.

Puede [crear y administrar cuentas de base de datos de DocumentDB](documentdb-create-account.md) a través del Portal de Azure en [http://portal.azure.com/](https://portal.azure.com/). Crear y administrar una cuenta de base de datos requieren acceso administrativo y solo se pueden realizar en su suscripción de Azure. 

### <a name="database-account-properties"></a>Propiedades de la cuenta de base de datos
Como parte de aprovisionamiento y administración de una cuenta de base de datos puede configurar y leer las siguientes propiedades:  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Nombre de propiedad</strong></p></td>
            <td valign="top"><p><strong>Descripción</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Directiva de coherencia</p></td>
            <td valign="top"><p>Establecer esta propiedad para configurar el nivel de coherencia predeterminado para todas las colecciones de su cuenta de base de datos. Puede reemplazar el nivel de coherencia según por solicitud mediante el encabezado de solicitud de [x-ms-nivel de coherencia]. <p><p>Tenga en cuenta que esta propiedad solo se aplica a los <i>recursos definidos por el usuario</i>. Sistema de todos los recursos definidos están configurados para admitir lecturas y consultas con fuerte coherencia.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Teclas de autorización</p></td>
            <td valign="top"><p>Estos son las teclas primaria y secundarias patrón y sólo lectura que proporcionan acceso administrativo a todos los recursos en la cuenta de base de datos.</p></td>
        </tr>
    </tbody>
</table>

Tenga en cuenta que además de aprovisionamiento, configurar y administrar su cuenta de base de datos desde el Portal de Azure, puede crear y administrar cuentas de base de datos de DocumentDB mediante la [API de REST de Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) , así como [cliente SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)también mediante programación.  

## <a name="databases"></a>Bases de datos
Una base de datos de DocumentDB es un contenedor lógico de uno o más colecciones y de los usuarios, como se muestra en el diagrama siguiente. Puede crear cualquier número de bases de datos en una cuenta de base de datos de DocumentDB sujeto a los límites de la oferta.  

![Modelo jerárquico de cuenta y las colecciones de base de datos][2]  
**Una base de datos es un contenedor lógico de usuarios y de colecciones**

Una base de datos puede contener almacenamiento de documentos prácticamente ilimitado dividida por colecciones, que forman los dominios de la transacción para los documentos contenidos en ellos. 

### <a name="elastic-scale-of-a-documentdb-database"></a>Escala elástico de una base de datos DocumentDB
Una base de datos DocumentDB es elástico predeterminada: comprendido entre unos GB petabytes de almacenamiento de documentos SSD copia y preparación de rendimiento. 

A diferencia de una base de datos en RDBMS tradicionales, una base de datos en DocumentDB no se aplica a un único equipo. Con DocumentDB, como escala de la aplicación se necesita crezca, puede crear más colecciones, bases de datos o ambos. De hecho, diferentes aplicaciones de terceros primera de Microsoft han estado utilizando DocumentDB en una escala de consumidor creando cada que contengan miles de colecciones de bases de datos de grandes DocumentDB con terabytes de almacenamiento de documentos. Puede aumentar o reducir una base de datos agregando o quitando colecciones para satisfacer los requisitos de escala de la aplicación. 

Puede crear cualquier número de colecciones de dentro de una base de datos están sujetos a la oferta. Cada colección tiene almacenamiento SSD copia y rendimiento proporciona según el nivel de rendimiento seleccionado.

Una base de datos de DocumentDB también es un contenedor de usuarios. Un usuario, a su vez, es un espacio de nombres lógico para un conjunto de permisos que proporciona acceso a las colecciones, documentos y datos adjuntos y autorización específica.  
 
Al igual que con otros recursos en el modelo de recursos de DocumentDB, bases de datos pueden crearse, reemplazan, eliminarán, leer o enumerar fácilmente con la [API de REST de Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) o cualquiera de los [SDK de cliente](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB garantiza la coherencia fuerte para leer o consultar los metadatos de un recurso de base de datos. Eliminar una base de datos automáticamente garantiza que no puede tener acceso a cualquiera de las colecciones o los usuarios dentro del mismo.   

## <a name="collections"></a>Colecciones
Una colección de DocumentDB es un contenedor para sus documentos JSON. Una colección también es una unidad de escala para las transacciones y las consultas. 

### <a name="elastic-ssd-backed-document-storage"></a>SSD elástico copia almacenamiento de documentos
Una colección es esencia elástica, crece automáticamente y se reduce al agregar o quitar documentos. Colecciones de recursos lógicos y pueden abarcar una partición física o servidores. El número de particiones dentro de una colección depende de DocumentDB basándose en el tamaño de almacenamiento y el rendimiento de la preparación de la colección. Todas las particiones DocumentDB tiene una cantidad fija de almacenamiento copia SSD asociada y replicarán para alta disponibilidad. Administración de partición es completamente administrado por DocumentDB de Azure y no tiene que escribir código complejo o administrar las particiones. Colecciones de DocumentDB son **prácticamente ilimitado** en términos de almacenamiento y rendimiento. 

### <a name="automatic-indexing-of-collections"></a>La indización automática de colecciones
DocumentDB es un sistema de base de datos libres de esquema es true. No se supone ni requerir cualquier esquema para los documentos JSON. Al agregar documentos a una colección, DocumentDB automáticamente los índices y están disponibles para la consulta. La indización automática de documentos sin necesidad de esquema o índices secundarios es una capacidad clave de DocumentDB y está habilitada por técnicas de mantenimiento de índice optimizado de escritura, sin bloqueo y estructura de registro. DocumentDB admite prolongado volumen de escritura muy rápido mientras sigue atendiendo consultas coherentes. Almacenamiento de documento e índice se usan para calcular el almacenamiento consumido por cada colección. Puede controlar el almacenamiento y el rendimiento desventajas asociadas con la indización mediante la configuración de la directiva de indización de una colección. 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>Configurar la directiva de indización de una colección
La directiva de indexación de cada colección le permite realizar el rendimiento y almacenamiento ventajas y desventajas asociadas con la indización. Las siguientes opciones están disponibles como parte de la configuración de indización:  

-   Elija si la colección automáticamente los índices todos los documentos o no. De forma predeterminada, todos los documentos se indizan de forma automática. Puede desactivar la indización automática y agregar de forma selectiva sólo los documentos específicos para el índice. Por el contrario, puede elegir selectivamente excluir solo los documentos específicos. Se puede conseguir estableciendo la propiedad automático sea verdadera o falsa en el indexingPolicy de una colección de y con el encabezado de la solicitud de [x-ms-indexingdirective] al insertar, reemplazar o eliminar un documento.  
-   Elija si desea incluir o excluir determinadas rutas de acceso o patrones en los documentos del índice. Se puede conseguir esta configuración includedPaths y excludedPaths en la indexingPolicy de una colección de respectivamente. También puede configurar el almacenamiento y el rendimiento compensaciones para las consultas de intervalo y hash para tramas de ruta de acceso específica. 
-   Elija entre sincrónico (coherente) y actualizaciones de índice (diferido) asincrónica. De forma predeterminada, el índice se actualiza sincrónicamente en cada Insertar, reemplazar o eliminar de un documento a la colección. Esto permite que las consultas respetar el mismo nivel de coherencia que de lecturas de documento. Mientras DocumentDB está optimizado de escritura y admite volúmenes prolongados de escritura de documento junto con el mantenimiento de índices sincrónico y servir consultas coherentes, puede configurar ciertas colecciones para actualizar su índice diferida. Indización diferida aumenta aún más el rendimiento de escritura y es ideal para escenarios de recopilación de masa para colecciones de lectura visible principalmente.

Puede cambiar la directiva de indexación mediante la ejecución de una ubicación en la colección. Esto se puede conseguir mediante el [SDK de cliente](https://msdn.microsoft.com/library/azure/dn781482.aspx), el [Portal de Azure](https://portal.azure.com) o las [API de REST de Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx).

### <a name="querying-a-collection"></a>Consultar una colección
Los documentos dentro de una colección pueden tener esquemas arbitrarios y puede consultar documentos dentro de una colección sin proporcionar cualquier esquema o por adelantado los índices secundarios. Puede consultar la colección utilizando la [sintaxis SQL DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx), que proporciona a enriquecido jerárquico y relacional y operadores espaciales y capacidad de ampliación a través de UDF basado en JavaScript. Gramática JSON permite modelar documentos JSON como árboles con etiquetas de los nodos de árbol. Esto es aprovechar las técnicas de indización automáticas de DocumentDB como lenguaje SQL de DocumentDB. El lenguaje de consulta DocumentDB consta de tres aspectos principales:   

1.  Un conjunto pequeño de operaciones de consulta que se asignan naturalmente a la estructura de árbol incluidos proyecciones y consultas jerárquicas. 
2.  Un subconjunto de operaciones relacionales incluidos composición, filtro, proyecciones, agregados y combinaciones automáticas. 
3.  JavaScript puro basada en UDF que funcionan con (1) y (2).  

El modelo de consulta DocumentDB intenta equilibrar la funcionalidad, eficiencia y simplificar. El motor de base de datos de DocumentDB forma nativa compila y ejecuta las instrucciones de la consulta SQL. Puede consultar una colección mediante las [API de REST de Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) o cualquiera del [cliente SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). El SDK de .NET viene con un proveedor LINQ.

> [AZURE.TIP] Puede probar DocumentDB y ejecutar consultas SQL contra nuestro conjunto de datos en la [Animación de la consulta](https://www.documentdb.com/sql/demo).

### <a name="multi-document-transactions"></a>Transacciones de varios documentos
Transacciones de la base de datos proporcionan un modelo de programación de seguro y predecible para abordar simultáneos cambios en los datos. En RDBMS, la manera tradicional de escribir la lógica empresarial es escribir **procedimientos almacenados** o **desencadenadores** y enviarlo al servidor de base de datos para la ejecución de transacciones. En RDBMS, es necesario que el programador de aplicaciones tratar con dos diferentes lenguajes de programación: 

- La aplicación (no transacciones) (por ejemplo, JavaScript, Python, C#, Java, etcetera) de lenguaje de programación
- T-SQL, el lenguaje de programación de transacciones que se ejecuta de forma nativa la base de datos

Gracias a su profundo compromiso de JavaScript y JSON directamente en el motor de base de datos, DocumentDB proporciona un modelo de programación intuitivo para ejecutar lógica JavaScript en función de la aplicación directamente en las colecciones en términos de procedimientos almacenados y desencadenadores. Esto permite las dos acciones siguientes:

- Controlar la implementación eficaz de simultaneidad, recuperación automática de indización de los gráficos de objeto JSON directamente en el motor de base de datos
- Expresar naturalmente flujo de control, variable de ámbito, asignación e integración de excepciones fundamentos con las transacciones de la base de datos directamente en el lenguaje de programación de JavaScript

La lógica de JavaScript registrada en un nivel de la colección puede ejecutar operaciones de base de datos en los documentos de la colección dada. DocumentDB ajusta implícitamente JavaScript según procedimientos almacenados y desencadenadores dentro de un ambiente transacciones ACID con aislamiento de instantánea en los documentos dentro de una colección. Durante el curso de su ejecución, si el código JavaScript inicia una excepción, se anula toda la transacción. El modelo de programación resultante es muy sencillo y potente. Los desarrolladores de JavaScript Obtén un modelo de programación "resistente" sin dejar de usar sus construcciones de lenguaje familiar y fundamentos de la biblioteca.   

La capacidad de ejecutar JavaScript directamente en el motor de base de datos en el mismo espacio de dirección que el búfer habilita eficaz y transacciones ejecución de operaciones de base de datos en los documentos de una colección. Además, un profundo compromiso para el JSON hace que el motor de base de datos de DocumentDB y JavaScript elimina cualquier diferencia de impedancia entre los sistemas de tipo de aplicación y la base de datos.   

Después de crear una colección, puede registrar procedimientos almacenados, desencadenadores y UDF con una colección mediante las [API de REST de Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) o cualquiera del [cliente SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). Después del registro, puede hacer referencia y ejecutarlos. Tenga en cuenta el siguiente procedimiento almacenado escrito completamente en JavaScript, el código siguiente acepta dos argumentos (nombre del libro y nombre del autor) y crea un nuevo documento, las consultas de un documento y lo actualiza: todo dentro de una transacción ACID implícita. En cualquier momento durante la ejecución, si se produce una excepción de JavaScript, toda la transacción se cancela.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();        
        var collectionLink = collectionManager.getSelfLink()
            
        // create a new document.
        collectionManager.createDocument(collectionLink,
            {id: name, author: author},
            function(err, documentCreated) {
                if(err) throw new Error(err.message);
                
                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function(err, matchingDocuments) {
                        if(err) throw new Error(err.message);
                        
                        context.getResponse().setBody(matchingDocuments.length);
                       
                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don’t need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);   
                        }
                    })
            })
    };

El cliente puede "Enviar" la lógica de JavaScript anterior a la base de datos para la ejecución de transacciones a través de HTTP POST. Para obtener más información sobre el uso de métodos HTTP, vea [REST interacciones con recursos DocumentDB](https://msdn.microsoft.com/library/azure/mt622086.aspx). 

    client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
       .then(function(createdStoredProcedure) {
            return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
                "NoSQL Distilled",
                "Martin Fowler");
        })
        .then(function(result) {
            console.log(result);
        },
        function(error) {
            console.log(error);
        });


Observe que, dado que la base de datos nativa comprende JSON y JavaScript, no hay ninguna discrepancia de sistema de tipo, "Asignación OR" o mágico de generación de código necesario.   

Procedimientos almacenados y desencadenadores interactúan con una colección y los documentos de una colección a través de un modelo de objeto bien definido que expone el contexto actual de la colección.  

Colecciones en DocumentDB se pueden crear, eliminarán, leer o enumerado fácilmente con cualquiera de las [API de REST de Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) o cualquiera del [cliente SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). DocumentDB siempre proporciona coherencia fuerte para leer o consultar los metadatos de una colección. Eliminar una colección automáticamente garantiza que no puede acceder a cualquiera de los documentos, datos adjuntos, procedimientos almacenados, desencadenadores y UDF dentro del mismo.   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>Procedimientos almacenados, desencadenadores y funciones definidas por el usuario (UDF)
Como se describe en la sección anterior, puede escribir lógica de la aplicación para que se ejecute directamente dentro de una transacción dentro del motor de base de datos. La lógica de aplicación puede escribirse completamente en JavaScript y se puede modelar como un procedimiento almacenado, desencadenador o una UDF. El código de JavaScript en un procedimiento almacenado o un desencadenador puede insertar, reemplazar, eliminar, documentos de lectura o consulta dentro de una colección. Por otro lado, el código de JavaScript dentro de un archivo UDF no se puede insertar, reemplazar o eliminar documentos. UDF enumerar los documentos del conjunto de resultados de la consulta y crear otro conjunto de resultados. Para usuarios múltiple, DocumentDB exige una gobernanza de recursos de reserva estrictas en función. Cada procedimiento almacenado, desencadenador o una UDF Obtiene un cuanto fijo de recursos del sistema operativo para hacer su trabajo. Además, procedimientos almacenados, desencadenadores o UDF no se pueden vincular con las bibliotecas de JavaScript externas y están en la lista negra si superan los presupuestos de recursos asignados a ellas. Puede registrar, anular el registro de procedimientos almacenados, desencadenadores o UDF con una colección usando las API de REST.  Tras el registro un procedimiento almacenado, desencadenador o una UDF es compilado previamente y almacenado como código de bytes que se ejecute más tarde. La siguiente sección muestran cómo puede usar el SDK de JavaScript DocumentDB registrar, ejecutar y anular el registro de un procedimiento almacenado, desencadenador y un archivo UDF. El SDK de JavaScript es un contenedor sencillo sobre las [API de REST de DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx). 

### <a name="registering-a-stored-procedure"></a>Registrar un procedimiento almacenado
Registro de un procedimiento almacenado crea un nuevo recurso de procedimiento almacenado en una colección a través de HTTP POST.  

    var storedProc = {
        id: "validateAndCreate",
        body: function (documentToCreate) {
            documentToCreate.id = documentToCreate.id.toUpperCase();
            
            var collectionManager = getContext().getCollection();
            collectionManager.createDocument(collectionManager.getSelfLink(),
                documentToCreate,
                function(err, documentCreated) {
                    if(err) throw new Error('Error while creating document: ' + err.message;
                    getContext().getResponse().setBody('success - created ' + 
                            documentCreated.name);
                });
        }
    };
    
    client.createStoredProcedureAsync(collection._self, storedProc)
        .then(function (createdStoredProcedure) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-stored-procedure"></a>Ejecutar un procedimiento almacenado
Ejecución de un procedimiento almacenado se realiza mediante la emisión de HTTP POST frente a un recurso existente de procedimiento almacenado pasando parámetros para el procedimiento descrito en el cuerpo de la solicitud.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>Anular el registro de un procedimiento almacenado
Anular el registro de un procedimiento almacenado se realiza simplemente mediante la emisión de un comando HTTP DELETE frente a un recurso de procedimiento almacenado existente.   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>Registrar un desencadenador anterior
Registro de un desencadenador se realiza mediante la creación de un nuevo recurso de desencadenador en una colección a través de HTTP POST. Puede especificar si el desencadenador es un pre o un desencadenador de entrada y el tipo de operación se pueden asociar con (por ejemplo, crear, reemplazar, eliminar o todos).   

    var preTrigger = {
        id: "upperCaseId",
        body: function() {
                var item = getContext().getRequest().getBody();
                item.id = item.id.toUpperCase();
                getContext().getRequest().setBody(item);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.All
    }
    
    client.createTriggerAsync(collection._self, preTrigger)
        .then(function (createdPreTrigger) {
            console.log("Successfully created trigger");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-pre-trigger"></a>Ejecutar un desencadenador anterior
Ejecución de un desencadenador se realiza especificando el nombre de un desencadenador existente en el momento de emitir la solicitud de publicación, SUPERPONER o eliminar de un recurso de documento a través del encabezado de la solicitud.  
 
    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>Eliminar del registro un desencadenador anterior
Eliminar del registro un desencadenador simplemente se realiza a través de emisión de un comando HTTP DELETE frente a un recurso de desencadenador existente.  

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

### <a name="registering-a-udf"></a>Registrar un archivo UDF
Registro de un archivo UDF se realiza mediante la creación de un nuevo recurso UDF en una colección a través de HTTP POST.  

    var udf = { 
        id: "mathSqrt",
        body: function(number) {
                return Math.sqrt(number);
        },
    };
    client.createUserDefinedFunctionAsync(collection._self, udf)
        .then(function (createdUdf) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-udf-as-part-of-the-query"></a>Ejecutar una UDF como parte de la consulta
Un archivo UDF puede especificarse como parte de la consulta SQL y se usa como una manera de ampliar el [lenguaje de consulta SQL de DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx)básico.

    var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-udf"></a>Anular el registro de un archivo UDF 
Anular el registro de un archivo UDF se realiza simplemente mediante la emisión de un comando HTTP DELETE frente a un recurso UDF existente.  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

Aunque los fragmentos de código anteriores mostraron el registro (POST), anulación (ubicación), lectura/lista (GET) y ejecución (publicación) mediante el [SDK de JavaScript DocumentDB](https://github.com/Azure/azure-documentdb-js), también puede usar las [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) u otro [cliente SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx). 

## <a name="documents"></a>Documentos
Puede insertar, reemplazar, eliminar, leer, enumerar y documentos JSON arbitrarios de una colección de la consulta. DocumentDB imponer cualquier esquema y no requiere índices secundarios para admitir la consulta de documentos de una colección.   

Ser un servicio de base de datos abierta realmente, DocumentDB no de inventario cualquier tipo de datos especializado (por ejemplo, fecha hora) o codificaciones específicas para documentos JSON. Tenga en cuenta que DocumentDB no requiere cualquier convenciones JSON especiales para codificar las relaciones entre varios documentos; la sintaxis SQL de DocumentDB proporciona muy eficaces consulta jerárquico y relacional operadores de consulta y proyecto documentos sin necesidad de codificar las relaciones entre los documentos con ni anotaciones especiales distinguen propiedades.  
 
Como con todos los demás recursos documentos pueden crearse, reemplazado, eliminados, leer, enumerado y consultado fácilmente mediante las API de REST o cualquiera de los [SDK de cliente](https://msdn.microsoft.com/library/azure/dn781482.aspx). Eliminar un documento al instante libera la cuota correspondiente a todos los datos adjuntos anidados. El nivel de coherencia de lectura de documentos sigue la directiva de coherencia en la cuenta de base de datos. Puede reemplazar esta directiva en una base por solicitud según los requisitos de coherencia de datos de la aplicación. Al consultar documentos, la coherencia de lectura sigue el modo de indización establecido en la colección. Para "coherente" Esto sigue directiva de coherencia de la cuenta. 

## <a name="attachments-and-media"></a>Datos adjuntos y elementos multimedia
>[AZURE.NOTE] Recursos de datos adjuntos y elementos multimedia son características de vista previa.
 
DocumentDB le permite almacenar BLOB binario/multimedia con DocumentDB o en su propio almacenamiento de medios remoto. También permite representar los metadatos de un medio de un documento especial denominado datos adjuntos. Datos adjuntos en DocumentDB son un documento (JSON) especial que hace referencia a la media/blob almacenado en otro lugar. Un archivo adjunto es simplemente un documento especial que captura los metadatos (por ejemplo, ubicación, autor, etc.) de un medio de almacenado en un medio remoto. 

Considere la posibilidad de una aplicación de lectura social que usa DocumentDB para almacenar anotaciones con lápiz y resalta los metadatos, incluidos los comentarios, marcadores, clasificaciones, me gusta/gustan etc. asociados para un libro electrónico de un usuario determinado.   

-   El contenido del libro propiamente dicho se almacena en el almacenamiento de archivos multimedia o disponible como parte de la cuenta de la base de datos de DocumentDB o un almacén de medios remoto. 
-   Una aplicación puede almacenar metadatos de cada usuario como un documento distinto, por ejemplo, se almacenan los metadatos de Juan para Libro1 en un documento al que hace referencia /colls/joe/docs/book1. 
-   Datos adjuntos que hace referencia a las páginas de contenido de un libro determinado de un usuario se almacenan en el documento correspondiente, por ejemplo, /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2 etcetera. 

Observe que los ejemplos anteriores utilizan identificadores descriptivos para transmitir la jerarquía de recursos. Se tiene acceso a los recursos a través de las API de REST a través de identificadores únicos de recursos. 

Para el medio administrado por DocumentDB, la propiedad _media del archivo adjunto hará referencia a los archivos multimedia por su identificador URI. Se asegurará de DocumentDB a basura recopilar multimedia cuando se eliminan todas las referencias pendientes. DocumentDB genera automáticamente los datos adjuntos al cargar los nuevos medios y los rellena el _media para que apunten a los archivos multimedia recién agregados. Si decide almacenar los archivos multimedia en un almacén de blobs remoto administrado por el usuario (por ejemplo, OneDrive, el almacenamiento de Azure DropBox etcetera), aún puede usar datos adjuntos para hacer referencia a los archivos multimedia. En este caso, se creará el archivo adjunto usted mismo y rellenar su propiedad _media.   

Al igual que con todos los recursos, datos adjuntos se pueden crear, reemplazan, eliminan, leen o enumerar fácilmente con las API de REST o cualquiera de lo SDK de cliente. Al igual que con los documentos, el nivel de coherencia de lectura de los datos adjuntos sigue la directiva de coherencia en la cuenta de base de datos. Puede reemplazar esta directiva en una base por solicitud según los requisitos de coherencia de datos de la aplicación. Al consultar datos adjuntos, la coherencia de lectura sigue el modo de indización establecido en la colección. Para "coherente" Esto sigue directiva de coherencia de la cuenta. 
 
## <a name="users"></a>Usuarios
Un usuario DocumentDB representa un espacio de nombres lógico para agrupar los permisos. Un usuario DocumentDB posible corresponden a un usuario en un sistema de administración de identidades o una función predefinida de la aplicación. Para DocumentDB, un usuario simplemente representa una abstracción para agrupar un conjunto de permisos en una base de datos.   

Para implementar varios usuarios en la aplicación, puede crear usuarios en DocumentDB que corresponde a los usuarios reales o los inquilinos de la aplicación. A continuación, puede crear permisos de un usuario que se corresponden con el control de acceso sobre varias colecciones, documentos, datos adjuntos, etcetera.   

Las aplicaciones necesite cambiar la escala con el crecimiento de usuarios, puede adoptar diversas maneras de fragmentar los datos. Puede modelar cada uno de los usuarios como sigue:   

-   Cada usuario se asigna a una base de datos.
-   Cada usuario se asigna a la colección. 
-   Documentos que corresponde a varios usuarios vaya a una colección dedicada. 
-   Documentos que corresponde a varios usuarios ir a un conjunto de colecciones.   

Independientemente de la estrategia de sharding específicos que elija, puede modelar usuarios reales como usuarios de base de datos de DocumentDB y asociar minucioso permisos para cada usuario.  

![Colecciones de usuario][3]  
**Estrategias de sharding y modelado de usuarios**

Como todos los recursos, los usuarios de DocumentDB pueden crearse, reemplazan, eliminan, leer o enumerar fácilmente con las API de REST o cualquiera de lo SDK de cliente. DocumentDB siempre proporciona coherencia fuerte para leer o consultar los metadatos de un recurso de usuario. Es la pena que eliminando un usuario automáticamente garantiza que no puede tener acceso a cualquiera de los permisos incluidos dentro de ella. Aunque la DocumentDB recupera la cuota de los permisos como parte del usuario eliminado en segundo plano, los permisos eliminados está disponible al instante nuevamente para que su uso.  

## <a name="permissions"></a>Permisos
Desde una perspectiva de control de acceso, recursos como cuentas de base de datos, bases de datos, los usuarios y permisos se consideran recursos *administrativos* , ya que estos requieren permisos administrativos. Por otro lado, incluidas las colecciones de documentos, datos adjuntos, procedimientos almacenados, desencadenadores y UDF de recursos se ámbito en una base de datos determinado y considera *recursos de la aplicación*. Correspondiente a los dos tipos de recursos y las funciones que tienen acceso a ellos (es decir, el administrador y el usuario), el modelo de autorización define dos tipos de *teclas de acceso*: *clave principal* y *clave de recurso*. La clave principal es un elemento de la cuenta de base de datos y se proporciona al programador (o administrador) que es el aprovisionamiento de la cuenta de base de datos. Esta clave maestra tiene semántica de administrador, en que se puede utilizar para autorizar el acceso a los recursos administrativos y de aplicación. En cambio, una clave de recurso es una tecla de acceso granulares que permite el acceso a un recurso *específico* de la aplicación. Por lo tanto, captura la relación entre el usuario de una base de datos y los permisos que tiene el usuario para un recurso específico (por ejemplo, colección, documento, datos adjuntos, procedimiento almacenado, desencadenador o UDF).   

La única manera de obtener una clave de recurso es mediante la creación de un recurso de permisos en un usuario determinado. Tenga en cuenta que para poder crear o recuperar un permiso, una clave principal se presentarán en el encabezado de autorización. Un recurso de permisos vincula un recurso, el acceso y el usuario. Después de crear un recurso de permiso, el usuario solo debe presentar la clave de recurso asociado con el fin de obtener acceso al recurso pertinente. Por lo tanto, una clave de recurso puede verse como una representación lógica y compacta del recurso de permisos.  

Al igual que con todos los demás recursos permisos en DocumentDB pueden crearse, reemplazan, eliminan, leer o enumerar fácilmente con las API de REST o cualquiera de lo SDK de cliente. DocumentDB siempre proporciona coherencia fuerte para leer o consultar los metadatos de un permiso. 

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre cómo trabajar con recursos mediante los comandos HTTP [REST interacciones con recursos DocumentDB](https://msdn.microsoft.com/library/azure/mt622086.aspx).


[1]: media/documentdb-resources/resources1.png
[2]: media/documentdb-resources/resources2.png
[3]: media/documentdb-resources/resources3.png

