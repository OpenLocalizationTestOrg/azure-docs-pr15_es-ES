<properties
   pageTitle="Realizar copias de seguridad de base de datos SQL - automático, geo redundantes | Microsoft Azure" 
   description="Base de datos de SQL crea una copia de seguridad de base de datos local cada cinco minutos y usa Azure almacenamiento de geo redundantes de acceso de lectura (RA GRS) para proporcionar redundancia de geo automáticamente. "
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/20/2016"
   ms.author="carlrab;barbkess"/>

<!------------------
This topic is annotated with TEMPLATE guidelines for FEATURE TOPICS.


Metadata guidelines

pageTitle
    60 characters or less. Includes name of the feature - primary benefit. Not the same as H1. Its 60 characters or fewer including all characters between the quotes and the Microsoft Azure site identifier.

description
    115-145 characters. Duplicate of the first sentence in the introduction. This is the abstract of the article that displays under the title when searching in Bing or Google. 

    Example: "SQL Database automatically creates a local database backup every few minutes and uses Azure read-access geo-redundant storage for geo-redundancy."
------------------>

<!----------------

TEMPLATE GUIDELINES for feature topics

The Feature Topic is a one-pager (ok, sometimes longer) that explains a capability of the product or service. It explains what the capability is and characteristics of the capability.  

It is a "learning" topic, not an action topic.

DO explain this:
    • Definition of the feature terminology.  i.e., What is a database backup?
    • Characteristics and capabilities of the feature. (How the feature works)
    • Common uses with links to overview topics that recommend when to use the feature.
    • Reference specifications (Limitations and Restrictions, Permissions, General Remarks, etc.)
    • Next Steps with links to related overviews, features, and tasks.

DON'T explain this:
    • How to steps for using the feature (Tasks)
    • How to solve business problems that incorporate the feature (Overviews)
------------------->

<!------------------
GUIDELINES for the H1 
    
    The H1 should answer the question "What is in this topic?" Write the H1 heading in conversational language and use search key words as much as possible. Since this is a learning topic, make sure the title indicates that and doesn't mislead people to think this will tell them how to do tasks.  
    
    To help people understand this is a learning topic and not an action topic, start the title with "Learn about ... "

    Heading must use an industry standard term. If your feature is a proprietary name like "Elastic database pools", use a synonym. For example:    "Learn about elastic database pools for multi-tenant databases". In this case multi-tenant database is the industry-standard term that will be an anchor for finding the topic.

-------------------->

# <a name="learn-about-sql-database-backups"></a>Obtenga más información sobre las copias de seguridad de base de datos SQL

<!------------------
    GUIDELINES for introduction
    
    The introduction is 1-2 sentences.  It is optimized for search and sets proper expectations about what to expect in the article. It should contain the top key words that you are using throughout the article.The introduction should be brief and to the point of what the feature is, what it is used for, and what's in the article. 

    If the introduction is short enough, your article can pop to the top in Google Instant Answers.

    In this example:
    
 

Sentence #1 Explains what the article will cover, which is what the feature is or does. This is also the metadata description. 
    SQL Database automatically creates a local database backup every five minutes and uses Azure read-access geo-redundant storage (RA-GRS) to provide geo-redundancy. 

Sentence #2 Explains why I should care about this.  
    Database backups are an essential part of any business continuity and disaster recovery strategy because they protect your data from accidental corruption or deletion.

-------------------->

Base de datos SQL y crea automáticamente una copia de seguridad de base de datos local cada pocos minutos usa Azure almacenamiento de geo redundantes de acceso de lectura para geo redundancia. Realizar copias de seguridad de base de datos son una parte esencial de cualquier estrategia de recuperación de desastres y continuidad empresarial porque protegen los datos de daños accidentales o eliminación. 

<!-- This image needs work, so not putting it in right now.

This diagram shows SQL Database running in the US East region. It creates a database backup every five minutes, which it stores locally to Azure Read Access Geo-redundant Storage (RA-GRS). Azure uses geo-replication to copy the database backups to a paired data center in the US West region.

![geo-restore](./media/sql-database-geo-restore/geo-restore-1.png)

-->

<!---------------
GUIDELINES for the first ## H2.

    The first ## describes what the feature encompasses and how it is used. It points to related task articles.
    
    For consistency, being the heading with "What is ... "
----------------->

## <a name="what-is-a-sql-database-backup"></a>¿Qué es una copia de seguridad de base de datos SQL?  

<!-- 
    Explains what a SQL Database backup is and answers an important question that people want to know.
-->

Una copia de seguridad de base de datos SQL incluye tanto copias de seguridad de base de datos local y geo redundantes. Estas copias de seguridad se crean automáticamente y sin costo adicional. No es necesario hacer nada para que les suceda.

<!----------------- 
    Explains first component of the backup feature
------------------>

Copias de seguridad local, base de datos SQL utiliza tecnología de SQL Server para crear copias de seguridad [completa](https://msdn.microsoft.com/library/ms186289.aspx), [diferencial](https://msdn.microsoft.com/library/ms175526.aspx )y de [registro de transacciones](https://msdn.microsoft.com/library/ms191429.aspx) . Las copias de seguridad del registro de transacciones ocurrir cada cinco minutos, que le permite hacer una restauración de punto en el tiempo en el mismo servidor que hospeda la base de datos. Cuando se restaura una base de datos, el servicio determina qué registro completa, diferencial y transacción copias de seguridad deben restaurarse.

<!--------------- 
    Explicit list of what to do with a local backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

Use una copia de seguridad de base de datos local para:

- Restaurar una base de datos a un punto en el tiempo dentro de un período de retención. Con una copia de seguridad de base de datos puede restaurar una base de datos a un punto en el tiempo, restaurar una base de datos eliminado a la vez que se ha eliminado o restaurar una base de datos a otra región geográfica. Para realizar una restauración, consulte [restaurar una base de datos desde una copia de seguridad de base de datos](sql-database-recovery-using-backups.md).

- Copiar una base de datos en un servidor de SQL server en la misma u otra región. La copia es coherente con la base de datos SQL. Para realizar una copia, vea [Copiar de la base de datos](sql-database-copy.md).

- Archivar una copia de seguridad de base de datos más allá del período de retención de copia de seguridad. Para realizar un archivo, [exportar una base de datos SQL para una MOCHILA](sql-database-export.md) archivo. A continuación, puede archivar la MOCHILA al almacenamiento a largo plazo y almacenar más allá de su período de retención. O bien, use la MOCHILA para transferir una copia de la base de datos de SQL Server en modo local o en una máquina virtual Azure (VM).

<!----------------- 
    Explains first component of the backup feature
------------------>

Geo redundantes copias de seguridad, base de datos SQL utiliza la [replicación de almacenamiento de Azure](../storage/storage-redundancy.md). Base de datos SQL almacena los archivos de copia de seguridad de base de datos local en una cuenta de [Almacenamiento Geo redundantes de acceso de lectura (RA GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage) . Azure aplica los archivos de copia de seguridad a un [Centro de datos pareja](../best-practices-availability-paired-regions.md). 

<!--------------- 
    Explicit list of what to do with a geo-redundant backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

Use una copia de seguridad geo redundantes:

- Restaurar una base de datos a una región geográfica distinta en caso de que no puede acceder a la copia de seguridad de base de datos de la región de base de datos principal. 

>[AZURE.NOTE] En el almacenamiento de Azure, la *replicación* de términos se refiere a copiar archivos de una ubicación a otra. *Replicación de base de datos* SQL se refiere a mantener a varias bases de datos secundarios sincronizados con una base de datos principal. 

<!----------------
    The next ## H2's discuss key characteristics of how the feature works. The title is in conversational language and asks the question that will be answered.
------------------->
## <a name="how-much-backup-storage-is-included-at-no-cost"></a>¿Cuánto almacenamiento de copia de seguridad se incluye sin costo?

Base de datos SQL proporciona hasta 200% de su almacenamiento de base de datos de preparación máximo como almacenamiento de copia de seguridad sin costo adicional. Por ejemplo, si tiene una instancia de DB estándar con un tamaño de DB preparación de 250 GB, tiene 500 GB de almacenamiento de copia de seguridad sin costo adicional. Si la base de datos supera el almacenamiento de copia de seguridad proporcionado, puede reducir el período de retención por ponerse en contacto con soporte técnico de Azure. Otra opción es pago para el almacenamiento de copia de seguridad adicional que se factura a la tasa estándar de almacenamiento geográfico redundantes de acceso de lectura (RA GRS). 

## <a name="how-often-do-backups-happen"></a>¿Con qué frecuencia se producen copias de seguridad?

Copias de seguridad de la base de datos local, copias de seguridad de base de datos completa ocurrir semanalmente, las copias de seguridad de base de datos diferencial ocurrir por hora y registro de transacciones copias de seguridad ocurrir cada cinco minutos. La primera copia de seguridad se ha programado inmediatamente después de crea una base de datos. Normalmente se completa en 30 minutos, pero puede tardar más cuando la base de datos de tamaño significativo. Por ejemplo, la copia de seguridad inicial puede tardar más en una base de datos restaurada o una copia de la base de datos. Después de la primera copia completa, más todas las copias de seguridad se programada automáticamente y se administran silenciosamente en segundo plano. Se determina el tiempo exacto de copias de seguridad de base de datos completa y [diferencial](https://msdn.microsoft.com/library/ms175526.aspx) como equilibra la carga de trabajo general. 

Geo redundantes copias de seguridad, copias de seguridad completas y diferenciales se copian según la programación de replicación de almacenamiento de Azure.

## <a name="how-long-do-you-keep-my-backups"></a>¿Cuánto tiempo se mantener las copias de seguridad?

Cada copia de seguridad de base de datos de SQL tiene un período de retención que se basa en el [nivel de servicio](sql-database-service-tiers.md) de la base de datos. El período de retención para una base de datos en la:

<!------------------

    Using a list so the information is easy to find when scanning.
------------------->

- Nivel de servicio básico es siete días.
- Nivel de servicio estándar es de 35 días.
- Nivel de servicio Premium es de 35 días.


Si degradar la base de datos de los niveles de servicio estándar o Premium básica, se guardan las copias de seguridad para siete días. Todas las copias de seguridad existentes más de siete días ya no están disponibles. 

Si actualiza la base de datos desde el nivel de servicio básico a estándar o Premium, base de datos SQL mantiene copias de seguridad existentes hasta que alcancen 35 días de antigüedad. Mantiene nuevas copias de seguridad según se van produciendo 35 días.
 
Si elimina una base de datos, base de datos SQL mantiene las copias de seguridad de la misma manera que lo haría para una base de datos en línea. Por ejemplo, suponga que elimina una base de datos básico que tiene un período de retención de siete días. Se guarda una copia de seguridad de cuatro días de antigüedad de más de tres días.

>[AZURE.IMPORTANT]
    Si elimina el servidor de SQL Azure que aloja las bases de datos de SQL, todas las bases de datos que pertenecen al servidor también se eliminan y no se pueden recuperar. No puede restaurar un servidor eliminado.

<!-------------------
OPTIONAL section
## Best practices 
--------------------->

<!-------------------
OPTIONAL section
## General remarks
--------------------->

<!-------------------
OPTIONAL section
## Limitations and restrictions
--------------------->

<!-------------------
OPTIONAL section
## Metadata
--------------------->

<!-------------------
OPTIONAL section
## Performance
--------------------->

<!-------------------
OPTIONAL section
## Permissions
--------------------->

<!-------------------
OPTIONAL section
## Security
--------------------->

<!-------------------
GUIDELINES for Next Steps

    The last section is Next Steps. Give a next step that would be relevant to the customer after they have learned about the feature and the tasks associated with it.  Perhaps point them to one or two key scenarios that use this feature.

    You don't need to repeat links you have already given them.
--------------------->

## <a name="next-steps"></a>Pasos siguientes

Realizar copias de seguridad de base de datos son una parte esencial de cualquier estrategia de recuperación de desastres y continuidad empresarial porque protegen los datos de daños accidentales o eliminación. Para ver cómo las copias de seguridad de base de datos en una estrategia más amplia, vea [información general de continuidad empresarial](sql-database-business-continuity.md).


