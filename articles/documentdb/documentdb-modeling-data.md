<properties 
    pageTitle="Modelado de datos en Azure DocumentDB | Microsoft Azure" 
    description="Obtenga información sobre el modelado de datos para DocumentDB, una base de datos de documento NoSQL." 
    keywords="modelado de datos"
    services="documentdb" 
    authors="kiratp" 
    manager="jhubbard" 
    editor="mimig1" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/05/2016" 
    ms.author="kipandya"/>

#<a name="modeling-data-in-documentdb"></a>Modelado de datos en DocumentDB#
Mientras libre de esquema de bases de datos, como DocumentDB de Azure, que sea muy fácil de adoptar cambios en el modelo de datos debe seguir gastar algún tiempo a pensar acerca de los datos. 

¿Cómo datos va a almacenar? ¿Cómo la aplicación va a recuperar y datos de la consulta? ¿Es su aplicación intensivo de lectura o escritura pesado? 

Después de leer este artículo, podrá responder a las preguntas siguientes:

- ¿Cómo debo pensar un documento en una base de datos de documentos?
- ¿Qué son los modelos de datos y por qué debería interesarle? 
- ¿Cómo es diferente a una base de datos relacional modelado de datos en una base de datos de documentos?
- ¿Cómo se hace express relaciones de datos en una base de datos no relacionales?
- ¿Cuándo se puede incrustar datos y cuando vinculan a los datos?

##<a name="embedding-data"></a>Incrustar datos##
Cuando se inicia el modelado de datos en un almacén de documentos, como DocumentDB, intente tratar las entidades como **documentos autocontenidos** representado en JSON.

Antes de profundizar demasiado mucho más allá, deje que nosotros recuperar unos cuantos pasos y eche un vistazo a cómo podríamos modelo algo en una base de datos relacional, un asunto muchos usuarios ya están familiarizados con. En el ejemplo siguiente se muestra cómo se podría almacenar una persona en una base de datos relacional. 

![Modelo de base de datos relacional](./media/documentdb-modeling-data/relational-data-model.png)

Al trabajar con bases de datos relacionales, nos hemos ha aprendido años normalizar, normalizar, normalizar.

Normalizar los datos normalmente implica tomar una entidad, como una persona y dividirla a discretas fragmentos de datos. En el ejemplo anterior, una persona puede tener varios registros de detalles de contacto, así como varios registros de dirección. Estamos incluso ir un paso más allá y desglosar detalles de contacto mediante la extracción más comunes campos como un tipo. Misma dirección, cada registro aquí tiene un tipo como *casa* o *empresa* 

La guía premise cuando Normalizar datos es **evitar almacenar datos redundantes** cada registro y prefiere hacer referencia a los datos. En este ejemplo, para leer a una persona con todos sus detalles de contacto y direcciones, debe usar combinaciones para agregar un modo eficaz de los datos en tiempo de ejecución.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Actualizar a una única persona con su información de contacto y direcciones requiere operaciones de escritura en muchas tablas individuales. 

Ahora Echemos un vistazo a cómo se debería modelar los mismos datos como una entidad independiente en una base de datos de documento.
        
    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "addresses": [
            {            
                "line1": "100 Some Street",
                "line2": "Unit 1",
                "city": "Seattle",
                "state": "WA",
                "zip": 98012
            }
        ],
        "contactDetails": [
            {"email: "thomas@andersen.com"},
            {"phone": "+1 555 555-5555", "extension": 5555}
        ] 
    }

Con el enfoque anterior, tenemos ahora grabar **sin normalizar** la persona dónde se **incrustado** toda la información relativa a esta persona, como su información de contacto y direcciones, en un único documento JSON.
Además, ya que no estamos limitados a un esquema fijo tenemos la flexibilidad para hacer cosas como tener por completo los detalles de contacto de diferentes formas. 

Recuperar un registro completo de la persona de la base de datos es ahora una única operación con un conjunto único y para un único documento de lectura. Actualizar un registro de la persona, con su información de contacto y direcciones, también es una operación de escritura solo en un solo documento.

Por desnormalizar los datos, es posible que su aplicación tenga menos consultas y actualizaciones para completar las operaciones comunes de problemas. 

###<a name="when-to-embed"></a>Incrustar

En general, utilice datos incrustados modelos cuando:

- Hay **contiene** relaciones entre entidades.
- Hay **uno a algunas** relaciones entre entidades.
- No hay datos incrustados **no cambian con frecuencia**.
- Está incrustada hay datos no crecen **sin límite**.
- No hay datos incrustados que se **integral** a los datos de un documento.

> [AZURE.NOTE] Normalmente los modelos de datos sin normalizar proporcionan mejor rendimiento de **lectura** .

###<a name="when-not-to-embed"></a>Cuándo no incrustar

Mientras que la regla general de la base de datos de un documento es reducir la normalización todo e incrustar todos los datos en un único documento, esto puede producir algunas situaciones que deben evitar.

Tomar este fragmento de código JSON.

    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "comments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            …
            {"id": 100001, "author": "jane", "comment": "and on we go ..."},
            …
            {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
            …
            {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
        ]
    }

Puede ser una entidad de publicación con comentarios incrustados aspecto si se nos modelado un blog típico o CMS, sistema. El problema de este ejemplo es que la matriz de comentarios está **fuera de los límites**, lo que significa que no hay ningún límite (práctico) en el número de comentarios que puede tener cualquier entrada único. Esto se convertirá en un problema como puede aumentar el tamaño del documento de forma significativa.

> [AZURE.TIP] Documentos de DocumentDB tienen un tamaño máximo. Para obtener más información sobre esto, consulte [límites de DocumentDB](documentdb-limits.md).

Como el tamaño del documento aumenta la capacidad para transmitir los datos sobre el cable, así como leer y actualizar el documento, en escala, se verán afectados.

En este caso sería mejor tener en cuenta el siguiente modelo.
        
    Post document:
    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "recentComments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            {"id": 3, "author": "jane", "comment": "....."}
        ]
    }

    Comment documents:
    {
        "postId": "1"
        "comments": [
            {"id": 4, "author": "anon", "comment": "more goodness"},
            {"id": 5, "author": "bob", "comment": "tails from the field"},
            ...
            {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
        ]
    },
    {
        "postId": "1"
        "comments": [
            {"id": 100, "author": "anon", "comment": "yet more"},
            ...
            {"id": 199, "author": "bored", "comment": "will this ever end?"}
        ]
    }

En este modelo tiene los tres más recientes comentarios incrustados en la entrada, que es una matriz con un fijo enlazado este momento. Los demás comentarios se agrupan en lotes de 100 comentarios y se almacenan en documentos distintos. El tamaño del lote se eligió como 100 porque nuestra aplicación ficticio permite al usuario cargar 100 comentarios a la vez.  

Otro caso donde la incrustación de datos no son una buena idea es cuando los datos incrustados se usan con frecuencia en los documentos y se cambian a menudo. 

Tomar este fragmento de código JSON.

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            {
                "numberHeld": 100,
                "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
            },
            {
                "numberHeld": 50,
                "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
            }
        ]
    }

Esto podría representar cartera de cotizaciones de una persona. Hemos elegido incrustar la información de stock a cada documento de la cartera de proyectos. En un entorno donde está cambiando los datos relacionados con frecuencia, como una acción cotización aplicación, incrustar datos que cambian con frecuencia va a significa que está actualizando constantemente cada documento cartera cada vez que una cotización.

Cotizaciones *zaza* puede ser objeto de intercambios cientos de horas en un solo día y miles de usuarios podrían tener *zaza* en su cartera. Con un modelo de datos del ejemplo tenemos actualizar miles de documentos de la cartera muchas veces cada día a lo que lleva a un sistema que no escala muy bien. 

##<a id="Refer"></a>Hacer referencia a datos##

Por lo tanto, incrustar datos funciona bien en muchos casos, pero está claro que hay escenarios cuando desnormalizar los datos hará que más problemas de vale la pena. Así que ¿qué es hacer ahora? 

Bases de datos relacionales no son los únicos lugares donde puede crear relaciones entre entidades. En una base de datos de documento pueden contener información en un documento que realmente se refiere a los datos de otros documentos. Ahora, estoy no abogan por incluso un minuto que creamos sistemas que podrían ser mejor a una base de datos relacional en DocumentDB o cualquier otra base de datos de documento, pero relaciones simples son correctos y pueden ser muy útiles. 

En el siguiente de JSON elegimos para usar el ejemplo de una cartera de cotizaciones desde una versión anterior, pero esta vez nos referimos al elemento de cotizaciones de la cartera en lugar de incrustarla. De este modo, cuando el elemento de cotizaciones cambia con frecuencia durante el día del documento único que necesita actualizarse es el único documento cotizaciones. 

    Person document:
    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            { "numberHeld":  100, "stockId": 1},
            { "numberHeld":  50, "stockId": 2}
        ]
    }
    
    Stock documents:
    {
        "id": "1",
        "symbol": "zaza",
        "open": 1,
        "high": 2,
        "low": 0.5,
        "vol": 11970000,
        "mkt-cap": 42000000,
        "pe": 5.89
    },
    {
        "id": "2",
        "symbol": "xcxc",
        "open": 89,
        "high": 93.24,
        "low": 88.87,
        "vol": 2970200,
        "mkt-cap": 1005000,
        "pe": 75.82
    }
    

No obstante, una desventaja inmediato de este enfoque es si la aplicación se necesita para mostrar información sobre cada acción que se mantiene al mostrar la cartera de una persona; en este caso necesarias para realizar varios viajes en la base de datos para cargar la información de cada documento cotizaciones. Aquí hemos realizado una decisión para mejorar la eficacia de las operaciones de escritura, que ocurre con frecuencia durante el día, pero comprometido por separado en las operaciones de lectura que posiblemente tienen menos impacto en el rendimiento de este sistema en particular.

> [AZURE.NOTE] Normalizar datos modelos **puede requerir más ida y vuelta** al servidor.

### <a name="what-about-foreign-keys"></a>¿Qué información sobre las claves externas?
Dado que actualmente no hay ningún concepto de una restricción, clave externa o en caso contrario, las relaciones entre documentos que tiene en documentos son en realidad "puntos débiles" y no comprobarse la base de datos. Si desea asegurarse de que los datos de que un documento hace referencia a realmente existe, debe hacerlo en la aplicación, o mediante el uso de desencadenadores de servidor o procedimientos almacenados en DocumentDB.

###<a name="when-to-reference"></a>Al hacer referencia a
En general, utilice los datos normalizados modelos cuando:

- Que representa las relaciones **uno a varios** .
- Que representa las relaciones de **varios a varios** .
- Relacionada con datos **cambian frecuentemente**.
- Datos de referencia pueden ser **ilimitada**.

> [AZURE.NOTE] Normalizar normalmente proporciona un mejor rendimiento de **escritura** .

###<a name="where-do-i-put-the-relationship"></a>¿Dónde se puede colocar la relación?
El crecimiento de la relación le ayudará a determinar en qué documento para almacenar la referencia.

Si observamos el siguiente JSON que modelos de libros y editores.

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press",
        "books": [ 1, 2, 3, ..., 100, ..., 1000]
    }

    Book documents:
    {"id": "1", "name": "DocumentDB 101" }
    {"id": "2", "name": "DocumentDB for RDBMS Users" }
    {"id": "3", "name": "Taking over the world one JSON doc at a time" }
    ...
    {"id": "100", "name": "Learn about Azure DocumentDB" }
    ...
    {"id": "1000", "name": "Deep Dive in to DocumentDB" }

Si el número de los libros por publisher es pequeño con crecimiento limitado, puede resultar útil después almacenar la referencia del libro en el documento de publisher. Sin embargo, si el número de libros por publisher es ilimitado, este modelo de datos puede llevar a matrices modificables crecientes, como en el documento de publisher de ejemplo anterior. 

Cambiar cosas un poco daría como resultado un modelo que representa sigue los mismos datos, pero ahora evita estas colecciones modificables grandes.

    Publisher document: 
    {
        "id": "mspress",
        "name": "Microsoft Press"
    }
    
    Book documents: 
    {"id": "1","name": "DocumentDB 101", "pub-id": "mspress"}
    {"id": "2","name": "DocumentDB for RDBMS Users", "pub-id": "mspress"}
    {"id": "3","name": "Taking over the world one JSON doc at a time"}
    ...
    {"id": "100","name": "Learn about Azure DocumentDB", "pub-id": "mspress"}
    ...
    {"id": "1000","name": "Deep Dive in to DocumentDB", "pub-id": "mspress"}

En el ejemplo anterior, hemos eliminado la colección ilimitada en el documento de publisher. En su lugar sólo tenemos una referencia a la publisher en cada documento del libro.

###<a name="how-do-i-model-manymany-relationships"></a>¿Cómo modelo de relaciones varios: varios?
En una base de datos relacional relaciones *varios: varios* a menudo modeladas con tablas de unión, que solo unir registros de otras tablas. 

![Combinar tablas](./media/documentdb-modeling-data/join-table.png)

Es posible que desee replicar lo mismo con documentos y producir un modelo de datos que tiene un aspecto similar al siguiente.

    Author documents: 
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }
    
    Book documents:
    {"id": "b1", "name": "DocumentDB 101" }
    {"id": "b2", "name": "DocumentDB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure DocumentDB" }
    {"id": "b5", "name": "Deep Dive in to DocumentDB" }
    
    Joining documents: 
    {"authorId": "a1", "bookId": "b1" }
    {"authorId": "a2", "bookId": "b1" }
    {"authorId": "a1", "bookId": "b2" }
    {"authorId": "a1", "bookId": "b3" }

Esto podría funcionar. Sin embargo, cargar a cada autor con sus libros o cargar un libro con el autor, siempre se requeriría al menos dos consultas adicionales en la base de datos. Una consulta para el documento de combinación y, a continuación, otra consulta para capturar el documento real que se combinan. 

Si todo está realizando esta tabla de combinación está pegando juntos dos conjuntos de datos, ¿por qué no colóquela completamente?
Tenga en cuenta lo siguiente.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}
    
    Book documents: 
    {"id": "b1", "name": "DocumentDB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "DocumentDB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure DocumentDB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive in to DocumentDB", "authors": ["a2"]}

Ahora, si tuviera un autor, inmediatamente sé qué libros que han escrito y a la inversa si tenía un documento del libro cargado sé si los identificadores de autores. Esto guarda esa consulta intermediaria frente a la tabla de combinación reducir el número de servidor redondear viajes su aplicación tiene que realizar. 

##<a id="WrapUp"></a>Modelos de datos híbrido##
Hemos visto incrustación de objetos (o desnormalizar) y datos de referencia (o normalización), cada una tiene sus upsides y cada uno tienen compromisos tal y como hemos visto. 

Siempre no tiene que ser o no ser temen que mezclar cosas un poco. 

En función de los patrones de uso específicos y puede haber casos donde insertada la mezcla de cargas de trabajo de la aplicación y los datos que se hace referencia tiene sentido y podría adelanto a lógica de la aplicación más sencilla con servidor menos viajes de ida y manteniendo un buen nivel de rendimiento.

Tenga en cuenta lo siguiente JSON. 

    Author documents: 
    {
        "id": "a1",
        "firstName": "Thomas",
        "lastName": "Andersen",     
        "countOfBooks": 3,
        "books": ["b1", "b2", "b3"],
        "images": [
            {"thumbnail": "http://....png"}
            {"profile": "http://....png"}
            {"large": "http://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "http://....png"}
        ]
    }
    
    Book documents:
    {
        "id": "b1",
        "name": "DocumentDB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "http://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "DocumentDB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
        ]
    }

Aquí nos hemos seguido (principalmente) el modelo incrustado, donde los datos de otras entidades están incrustados en el documento de nivel superior, pero se hace referencia a otros datos. 

Si mira el documento del libro, podemos ver algunas interesantes campos cuando observamos la matriz de autores. Hay un campo de *identificador* que es el campo que usamos para hacer referencia a un documento de autor, habitual en un modelo normalizado, pero a continuación, también tenemos *nombre* y *thumbnailUrl*. Podemos ha simplemente detiene con *id* y la izquierda de la aplicación para obtener información adicional que es necesaria del documento autor respectivos con el vínculo de"", pero dado que nuestra aplicación muestra el nombre del autor y una imagen en miniatura con cada libro muestra podemos guardar ida y vuelta al servidor por el libro en una lista por desnormalizar **algunos** datos del autor.

Claro, si ha cambiado el nombre del autor o que deseaba actualizar su foto que tenemos enviarse una actualización todos los libros que publiquen nunca pero para nuestra aplicación, basado en la suposición de que los autores no cambiar sus nombres muy a menudo, esta es una decisión aceptable de diseño.  

En el ejemplo hay valores **calculados previamente agregados** para guardar procesamiento costosa en una operación de lectura. En el ejemplo, algunos de los datos incrustados en el documento de autor son los datos que se calculan en tiempo de ejecución. Cada vez que se publica un libro nuevo, se crea un documento del libro **y** que el campo countOfBooks se establece en un valor calculado en función del número de documentos de libro que existen para un autor concreto. Esta optimización sería buena en los sistemas grandes lecturas donde nos podemos permitir hacer cálculos en escribe para optimizar lecturas.

La capacidad de un modelo de los campos calculados previamente se ha hecho posible porque DocumentDB es compatible con **las transacciones de varios documentos**. Muchos almacena de NoSQL no puede hacer las transacciones a través de documentos y, por tanto, Defensor de decisiones de diseño, como "siempre incrustar todo", debido a esta limitación. Con DocumentDB, puede usar desencadenadores de servidor o procedimientos almacenados, que insertar libros y actualización autores todo dentro de una transacción ACID. Ahora no **tiene** Insertar todo el contenido de un documento a Asegúrese de que los datos permanecerán coherentes.

##<a name="NextSteps"></a>Pasos siguientes

Los puntos principales de este artículo es comprender que el modelado de datos en un mundo libre de esquema están tan importantes que nunca. 

Como no hay ninguna manera única para representar un fragmento de datos en una pantalla, no hay ninguna manera única de los datos del modelo. Necesita conocer la aplicación y cómo se producen consumir y procesar los datos. A continuación, mediante la aplicación de algunas de las instrucciones que se presentan aquí puede establecer sobre la creación de un modelo que satisface las necesidades inmediatas de la aplicación. Cuando es necesario cambiar sus aplicaciones, puede aprovechar la flexibilidad una base de datos libres de esquema adoptar que cambian y evolucionando fácilmente en el modelo de datos. 

Para obtener más información sobre DocumentDB de Azure, consulte la página de la [documentación](https://azure.microsoft.com/documentation/services/documentdb/) del servicio. 

Para obtener información sobre la optimización de índices en Azure DocumentDB, consulte el artículo [directivas](documentdb-indexing-policies.md)de indización.

Para comprender cómo a fragmentar los datos en varias particiones, consulte [Partición de datos en DocumentDB](documentdb-partition-data.md). 

Y por último, para obtener instrucciones modelado de datos y sharding para las aplicaciones de múltiples arrendatarios, consulte [escalado de una aplicación de múltiples arrendatarios con DocumentDB de Azure](http://blogs.msdn.com/b/documentdb/archive/2014/12/03/scaling-a-multi-tenant-application-with-azure-documentdb.aspx).
 
