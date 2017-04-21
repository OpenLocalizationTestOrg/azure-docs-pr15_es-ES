<properties
   pageTitle="Distribuir datos globalmente con DocumentDB | Microsoft Azure"
   description="Obtenga información sobre la escala del mundo geo replicación, migración tras error y recuperación de datos con bases de datos globales de Azure DocumentDB, un servicio de base de datos NoSQL completamente administrado."
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="kipandya"/>
   
   
# <a name="distribute-data-globally-with-documentdb"></a>Distribuir datos globalmente con DocumentDB

> [AZURE.NOTE] Distribución global de bases de datos de DocumentDB está disponible de forma general y automáticamente habilitado para las cuentas de DocumentDB recién creadas. Estamos trabajando para habilitar la distribución global en todas las cuentas existentes, pero mientras tanto, si desea que la distribución global habilitado en su cuenta, por favor, [póngase en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) y se deberá habilitarlo para ahora.

Azure DocumentDB está diseñada para satisfacer las necesidades de aplicaciones IoT formada por millones de dispositivos globalmente distribuidos y aplicaciones de escala de internet que ofrecen experiencias de alta capacidad de respuesta a los usuarios en todo el mundo. Estos sistemas de base de datos enfrentan el desafío de alcanzar un acceso de latencia baja a los datos de la aplicación de varias regiones geográficas con garantías de coherencia y disponibilidad de datos bien definidos. Como un sistema de base de datos distribuida globalmente DocumentDB simplifica la distribución de datos global mediante cuentas de base de datos totalmente administrado, región múltiples que proporcionan borrar compensaciones entre coherencia, disponibilidad y rendimiento, todas las garantías correspondientes. Cuentas de base de datos de DocumentDB se ofrecen con alta disponibilidad, latencia de un único dígito ms, varios [niveles de coherencia bien definidos] [consistency], transparente migración tras error regional con las API de varias direcciones IP y la capacidad de escalar elástica rendimiento y almacenamiento en todo el mundo. 

  
## <a name="configuring-multi-region-accounts"></a>Configuración de región de varias cuentas

Configurar la cuenta DocumentDB escalar en todo el mundo puede hacerse en menos de un minuto a través del [portal de Azure](documentdb-portal-global-replication.md). Todo lo que debe hacer es seleccione el nivel de coherencia derecha entre varios niveles de coherencia bien definido compatibles y asociar cualquier número de áreas de Azure con su cuenta de base de datos. Los niveles de coherencia DocumentDB proporcionan borrar compensaciones entre garantía de coherencia específicos y el rendimiento. 

![DocumentDB ofrece varias, bien definidos modelos de coherencia (flexible) para elegir][1]

DocumentDB ofrece varias, bien definidos por coherencia (Media) modelos para elegir.

Seleccionar el nivel de coherencia derecha depende de datos necesarios para la aplicación garantizar la coherencia. DocumentDB duplica los datos en todas las regiones especificadas y garantiza la coherencia que ha seleccionado para su cuenta de base de datos automáticamente. 


## <a name="using-multi-region-failover"></a>Uso de múltiples región migración tras error 

DocumentDB Azure es capaz de cuentas de base de datos de migración tras error transparente en diferentes regiones Azure: la nueva [API de varias direcciones IP] [ developingwithmultipleregions] garantía que su aplicación puede seguir usando un extremo lógico y se interrumpe por la migración tras error. Migración tras error está controlado por el usuario, que proporciona la flexibilidad de reubicación de la base de datos de la cuenta en el evento cualquiera de las condiciones de posibles errores se producen, incluidos errores regionales (reales o simuladas), infraestructura, servicio o aplicación. Si se produce un error regional DocumentDB, se producirá un error transparente el servicio por su cuenta de base de datos y la aplicación continúa teniendo acceso a los datos sin perder la disponibilidad. Mientras DocumentDB ofrece [disponibilidad del 99,99% SLA][sla], puede probar el final de la aplicación a las propiedades de la disponibilidad de finalización al simular un error regional ambos, [mediante programación] [ arm] así como a través del Portal de Azure.


## <a name="scaling-across-the-planet"></a>Ajuste de escala en el mundo
DocumentDB le permite aprovisionar rendimiento y consumir almacenamiento para cada colección de DocumentDB a cualquier escala, globalmente a través de todas las regiones asociadas a su cuenta de base de datos de forma independiente. Una colección de DocumentDB automáticamente se distribuye globalmente y administrarse en todas las regiones asociadas a su cuenta de base de datos. Colecciones dentro de su cuenta de base de datos pueden distribuirse a través de cualquiera de las regiones de Azure en la que el [servicio de DocumentDB está disponible][serviceregions]. 

El rendimiento comprado y almacenamiento consumido para cada colección DocumentDB automáticamente se aprovisiona en todas las regiones igualmente. Esto permite a la aplicación sin problemas escalar el globo [pagar solo para el rendimiento y el almacenamiento está usando en cada hora][pricing]. Por ejemplo, si ha suministrado RUs 2 millones para una colección de DocumentDB, a continuación, cada una de las regiones asociadas a su cuenta de base de datos obtiene RUs de 2 millones de esa colección. Esto se muestra a continuación.

![Rendimiento escalado para una colección de DocumentDB en cuatro regiones][2]

DocumentDB garantiza < 10 ms leer y escribir el < 15 ms latencia en P99. Las solicitudes de lectura nunca span límite del centro de datos para garantizar los [requisitos de coherencia seleccionó][consistency]. La escritura es siempre quórum confirmada localmente antes de que se reconozcan en los clientes. Cada cuenta de la base de datos está configurado con la prioridad de la región de escritura. La región designada con prioridad más alta actúen como la región actual de escritura de la cuenta. Todos los SDK transparente dirigirá escritura de la cuenta de base de datos a la región de escritura actual. 

Por último, ya que DocumentDB completamente es [independiente del esquema] [ vldb] -que nunca tendrá que preocuparse por administrar o actualizar esquemas o índices secundarios en varios centros de datos. Las [consultas SQL] [ sqlqueries] continuar trabajando mientras siguen evolucionando en los modelos de datos y la aplicación. 


## <a name="enabling-global-distribution"></a>Habilitar distribución global 

Puede decidir realizar sus datos localmente o globalmente distribuidos asociando cualquier cuenta de base de datos de una o varias áreas de Azure con un DocumentDB. Puede agregar o quitar las áreas a su cuenta de base de datos en cualquier momento. Para habilitar la distribución global a través del portal, vea [cómo llevar a cabo la replicación de base de datos global DocumentDB con el portal de Azure](documentdb-portal-global-replication.md). Para habilitar la distribución global mediante programación, vea [desarrollar con cuentas de región múltiples DocumentDB](documentdb-developing-with-multiple-regions.md).

## <a name="next-steps"></a>Pasos siguientes

Más información sobre los datos distribuir globalmente con DocumentDB en los siguientes artículos:

* [Rendimiento y almacenamiento para una colección de aprovisionamiento] [throughputandstorage]
* [API de varias direcciones IP mediante el resto. NET, Java, Python y SDK de nodo] [developingwithmultipleregions]
* [Niveles de coherencia en DocumentDB] [consistency]
* [SLA de disponibilidad] [sla]
* [Administrar la cuenta de base de datos] [manageaccount]

[1]: ./media/documentdb-distribute-data-globally/consistency-tradeoffs.png
[2]: ./media/documentdb-distribute-data-globally/collection-regions.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[pcolls]: documentdb-partition-data.md
[consistency]: documentdb-consistency-levels.md
[consistencytradeooffs]: ./documentdb-consistency-levels/#consistency-levels-and-tradeoffs
[developingwithmultipleregions]: documentdb-developing-with-multiple-regions.md
[createaccount]: documentdb-create-account.md
[manageaccount]: documentdb-manage-account.md
[manageaccount-consistency]: documentdb-manage-account.md#consistency
[throughputandstorage]: documentdb-manage.md
[arm]: documentdb-automation-resource-manager-cli.md
[regions]: https://azure.microsoft.com/regions/
[serviceregions]: https://azure.microsoft.com/en-us/regions/#services 
[pricing]: https://azure.microsoft.com/pricing/details/documentdb/
[sla]: https://azure.microsoft.com/support/legal/sla/documentdb/ 
[vldb]: http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf
[sqlqueries]: documentdb-sql-query.md

