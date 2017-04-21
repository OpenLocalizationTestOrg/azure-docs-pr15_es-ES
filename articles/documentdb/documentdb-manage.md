<properties
    pageTitle="DocumentDB almacenamiento y rendimiento | Microsoft Azure" 
    description="Obtenga información sobre el almacenamiento de datos y el almacenamiento de documentos en DocumentDB y cómo puede escalar DocumentDB para satisfacer las necesidades de capacidad de la aplicación." 
    keywords="almacenamiento de documentos"
    services="documentdb" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="cgronlun" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/18/2016" 
    ms.author="syamk"/>

# <a name="learn-about-storage-and-predictable-performance-provisioning-in-documentdb"></a>Obtenga más información sobre el almacenamiento y rendimiento predecible aprovisionamiento en DocumentDB
DocumentDB Azure es un totalmente administrado scalable orientados al documento NoSQL base de datos servicio para documentos JSON. Con DocumentDB, no tiene alquilar máquinas virtuales, implementar software o supervisar las bases de datos. DocumentDB es ofrecido y supervisa continuamente por ingenieros de Microsoft para ofrecer mundo disponibilidad, rendimiento y protección de datos.  

Puede empezar con DocumentDB mediante la [creación de una cuenta de base de datos](documentdb-create-account.md) y una [base de datos de DocumentDB](documentdb-create-database.md) a través del [portal de Azure](https://portal.azure.com/). Bases de datos de DocumentDB se ofrecen en unidades de unidad de estado sólido (SSD) copia almacenamiento y el rendimiento. Estas unidades de almacenamiento aprovisionadas mediante la [creación de colecciones de base de datos](documentdb-create-collection.md) dentro de su cuenta de base de datos, cada colección de rendimiento reservado que se puede escalar hacia arriba o hacia abajo en cualquier momento para satisfacer las necesidades de la aplicación. 

Si su aplicación supera el rendimiento de una o varias colecciones reservado, las solicitudes están limitadas en cada colección por. Esto significa que pueden tener éxito algunas solicitudes de aplicación mientras otras personas pueden ser acelerados.

Este artículo proporciona una descripción general de los recursos y métricas disponibles para administrar la capacidad y planear el almacenamiento de datos. 

## <a name="database-account"></a>Cuenta de base de datos
Como suscriptor de Azure, puede proporcionar una o varias cuentas de base de datos de DocumentDB para administrar los recursos de la base de datos. Cada suscripción está asociada a una sola suscripción Azure. 

Cuentas de DocumentDB pueden crearse a través del [portal de Azure](documentdb-create-account.md), o mediante [una plantilla ARM o CLI de Azure](documentdb-automation-resource-manager-cli.md).

## <a name="databases"></a>Bases de datos
Una base de datos DocumentDB puede contener prácticamente una cantidad ilimitada de almacenamiento de documentos que se agrupan en colecciones. Colecciones proporcionan aislamiento de rendimiento: cada colección puede provista de rendimiento que no se comparte con otras colecciones en la misma base de datos o la cuenta. Una base de datos DocumentDB es elástico en tamaño, comprendido entre GB TB de SSD copia almacenamiento de documentos y preparación de rendimiento. A diferencia de una base de datos RDBMS tradicional, una base de datos en DocumentDB no se aplica a un único equipo y puede abarcar varias máquinas o clústeres.  

Con DocumentDB, como necesite para ajustar el tamaño de sus aplicaciones, puede crear más colecciones o bases de datos o ambos. Bases de datos pueden crearse a través del [portal de Azure](documentdb-create-database.md) o a través de cualquiera de los [SDK DocumentDB](documentdb-dotnet-samples.md).   

## <a name="database-collections"></a>Colecciones de base de datos
Cada base de datos de DocumentDB puede contener una o varias colecciones. Colecciones actúen como particiones de datos altamente disponible para el procesamiento y almacenamiento de documentos. Cada colección puede almacenar documentos con esquema heterogéneo. La indización automática del DocumentDB y las capacidades de consulta permiten filtrar fácilmente y recuperar documentos. Una colección proporciona el ámbito de ejecución de consulta y almacenamiento de documentos. Una colección también es un dominio de transacciones para todos los documentos dentro del mismo. Colecciones se asignan según el valor establecido en el portal de Azure o mediante el SDK de rendimiento. 

Colecciones se dividen automáticamente en uno o más servidores físicos por DocumentDB. Cuando se crea una colección, puede especificar el rendimiento aprovisiona en términos de unidades de la solicitud por segundo y una propiedad de clave de partición. El valor de esta propiedad se usa por DocumentDB para distribuir documentos entre particiones y las solicitudes de ruta como consultas. El valor de clave de partición también actúa como el límite de transacción para procedimientos almacenados y desencadenadores. Cada colección tiene una cantidad reservada de rendimiento específica de esa colección, que no se comparte con otras colecciones de la misma cuenta. Por lo tanto, se puede escalar la aplicación tanto en términos de almacenamiento y el rendimiento. 

Colecciones pueden crearse a través del [portal de Azure](documentdb-create-collection.md) o a través de cualquiera de los [SDK DocumentDB](documentdb-sdk-dotnet.md).   
 
## <a name="request-units-and-database-operations"></a>Solicitar unidades y las operaciones de base de datos

Cuando se crea una colección, reserve rendimiento en términos de [unidades de solicitud (RU)](documentdb-request-units.md) por segundo. En su lugar de pensar y administración de recursos de hardware, puede considerar que una **unidad de solicitud** como una medida de los recursos necesitan para realizar diversas operaciones de base de datos y una solicitud de aplicación de servicio. Lectura de un documento de 1 KB consume la misma 1 RU independientemente del número de elementos almacenados en la colección o el número de solicitudes simultáneas que se ejecutan al mismo tiempo. Todas las solicitudes contra DocumentDB, incluyendo operaciones complejas como consultas SQL tienen un valor RU predecible que puede estar determinado en tiempo de desarrollo. Si conoce el tamaño de los documentos y la frecuencia de cada operación (lee, escribe y consultas) de soporte técnico para la aplicación, puede aprovisionar la cantidad exacta de rendimiento para satisfacer las necesidades de la aplicación y ajustar la base de datos hacia arriba y hacia abajo el rendimiento cambien las necesidades. 

Cada colección puede reservar con el rendimiento en bloques de 100 unidades de solicitud por segundo entre cientos hasta millones de unidades de la solicitud por segundo. Puede ajustar el rendimiento de preparación durante toda la vida de una colección de adaptarse a las necesidades de procesamiento y tener acceso a modelos de la aplicación. Para obtener más información, vea [niveles de rendimiento de DocumentDB](documentdb-performance-levels.md). 

>[AZURE.IMPORTANT] Colecciones son entidades facturables. El costo se establece mediante el rendimiento de la preparación de la colección de medidas en unidades de solicitud por segundo junto con el almacenamiento total consumido en gigabytes. 

¿El número de unidades de solicitud ocupará una operación determinada, como insertar, eliminar, consulta o ejecución del procedimiento almacenado? Una unidad de solicitud es una medición normalizada de costo de procesamiento de solicitud. Lectura de un documento de 1 KB es 1 RU, pero una solicitud para insertar, reemplazar o eliminar el mismo documento ocupará más procesamiento del servicio y, con ello, más unidades de la solicitud. Cada respuesta del servicio incluye un encabezado personalizado (`x-ms-request-charge`) que informa de las unidades de solicitud consumidas para la solicitud. Este encabezado también está accesible a través de los [SDK](documentdb-sdk-dotnet.md). En el SDK de .NET [RequestCharge](https://msdn.microsoft.com/library/azure/dn933057.aspx#P:Microsoft.Azure.Documents.Client.ResourceResponse`1.RequestCharge) es una propiedad del objeto [ResourceResponse](https://msdn.microsoft.com/library/azure/dn799209.aspx) . Si desea estimar las necesidades de rendimiento antes de realizar una llamada única, puede usar el [Organizador de la capacidad](documentdb-request-units.md#estimating-throughput-needs) para ayudarle con este cálculo. 

>[AZURE.NOTE] La línea de base de 1 unidad de solicitud para un documento de 1 KB corresponde a obtener simple del documento con la [Coherencia de sesión](documentdb-consistency-levels.md). 

Hay varios factores que afectan a las unidades de solicitud consumidas para una operación con una cuenta de base de datos de DocumentDB. Estos factores incluyen:

- Tamaño del documento. Como los tamaños de documento aumentan las unidades consumidas para leer o escribir que los datos también aumentará.
- Recuento de propiedades. Suponiendo que la indización predeterminado de todas las propiedades, las unidades consumidas para escribir un documento aumentará como aumenta de recuento de la propiedad.
- Coherencia de los datos. Al utilizar niveles de coherencia de datos de texto en negrita o antigüedad limitada, se consume unidades adicionales para leer documentos.
- Propiedades indizadas. Una directiva de índice en cada colección determina las propiedades que se indizan de manera predeterminada. Puede reducir el consumo de unidades solicitud limitando el número de propiedades indizadas. 
- Indexación de documentos. De forma predeterminada, que todos los documentos se indizan automáticamente, se utilizará menos unidades de solicitud si decide no indizar algunos de los documentos.

Para obtener más información, vea [unidades de solicitud de DocumentDB](documentdb-request-units.md). 

Por ejemplo, aquí es una tabla que muestra el número de unidades de solicitud aprovisionar en tres tamaños de otro documento (1KB, 4KB y 64KB) y en dos niveles de rendimiento diferentes (500 lecturas por segundo escribe por segundo de 100 y 500 lecturas por segundo + 500 escribe por segundo). Se ha configurado la coherencia de los datos en la sesión y que se estableció la directiva de indización en ninguna.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Tamaño del documento</strong></p></td>
            <td valign="top"><p><strong>Lecturas por segundo</strong></p></td>
            <td valign="top"><p><strong>Escribe por segundo</strong></p></td>
            <td valign="top"><p><strong>Unidades de solicitud</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1) + (100* 5) = 1.000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *5) + (100* 5) = 3.000 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1.3) + (100* 7) = 1,350 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *1.3) + (500* 7) = 4,150 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *10) + (100* 48) = 9.800 RU/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *10) + (500* 48) = 29,000 RU/s</p></td>
        </tr>
    </tbody>
</table>

Consultas, procedimientos almacenados y desencadenadores consuman unidades de solicitud en función de la complejidad de las operaciones que se realicen. Desarrollar la aplicación, inspeccionar la cabecera de documento de solicitud para comprender mejor cómo cada operación consume capacidad de unidad de solicitud.  


## <a name="choice-of-consistency-level-and-throughput"></a>Opción de nivel de coherencia y rendimiento
La opción de nivel de coherencia predeterminado tiene un gran impacto en el rendimiento y la latencia. Puede establecer el nivel de coherencia predeterminado mediante programación y a través del portal de Azure. También puede reemplazar el nivel de coherencia en una base por solicitud. De forma predeterminada, el nivel de coherencia se establece en **sesión**, que proporciona monotónico lectura/escritura y leer sus garantías de escritura. Coherencia de sesión es ideal para aplicaciones de usuario y proporciona un equilibrio ideal de coherencia y rendimiento ventajas y desventajas.    

Para obtener instrucciones sobre cómo cambiar el nivel de coherencia en el portal de Azure, consulte [cómo administrar una cuenta de DocumentDB](documentdb-manage-account.md#consistency). O bien, para obtener más información sobre los niveles de coherencia, vea [niveles de coherencia de uso](documentdb-consistency-levels.md).

## <a name="provisioned-document-storage-and-index-overhead"></a>Documento de preparación sobrecarga de almacenamiento y de índice
DocumentDB es compatible con la creación de colecciones única partición y divididas. Cada partición en DocumentDB admite hasta 10 GB de almacenamiento SSD copia. Los 10GB de almacenamiento de documentos incluye los documentos más almacenamiento para el índice. De forma predeterminada, una colección de DocumentDB está configurada para indizar automáticamente todos los documentos sin necesidad de explícitamente cualquier índices secundarios o esquema. En función de las aplicaciones con DocumentDB, la sobrecarga de índice típica es entre 2-20%. La tecnología de indización utilizada por DocumentDB garantiza que independientemente de los valores de las propiedades, la sobrecarga de índice no supere superior al 80% del tamaño de los documentos con la configuración predeterminada. 

De forma predeterminada todos los documentos estén indizados por DocumentDB automáticamente. Sin embargo, si desea ajustar la sobrecarga de índice, puede quitar algunos documentos de la indización en el momento de insertar o reemplazar un documento, como se describe en [las directivas de indexación de DocumentDB](documentdb-indexing-policies.md). Puede configurar una colección de DocumentDB para excluir todos los documentos dentro de la colección de la indización. También puede configurar una colección de DocumentDB para indizar selectivamente solo determinadas propiedades o rutas con caracteres comodín de los documentos JSON, como se describe en [configuración de la directiva de una colección de indización](documentdb-indexing-policies.md#configuring-the-indexing-policy-of-a-collection). También se excluyendo documentos o propiedades mejora el rendimiento de escritura, lo que significa que utilizará menos unidades de solicitud.   

## <a name="next-steps"></a>Pasos siguientes

Para continuar aprendiendo sobre el funcionamiento de DocumentDB, consulte [particiones y escala en DocumentDB de Azure](documentdb-partition-data.md).

Para obtener instrucciones sobre la supervisión de niveles de rendimiento en el portal de Azure, consulte [Monitor de una cuenta de DocumentDB](documentdb-monitor-accounts.md). Para obtener más información sobre la elección de niveles de rendimiento para colecciones, vea [niveles de rendimiento en DocumentDB](documentdb-performance-levels.md).
 
