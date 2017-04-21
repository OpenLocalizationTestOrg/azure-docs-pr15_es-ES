<properties
    pageTitle="Copia de seguridad y restauración con DocumentDB | Microsoft Azure"
    description="Obtenga información sobre cómo realizar copia de seguridad automática y restauración de bases de datos NoSQL con DocumentDB de Azure."
    keywords="copia de seguridad y restauración, copia de seguridad en línea"
    services="documentdb"
    documentationCenter=""
    authors="RahulPrasad16"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="raprasa"/>

# <a name="automatic-online-backup-and-restore-with-documentdb"></a>Copia de seguridad automática en línea y restaurar con DocumentDB 

Azure DocumentDB realiza automáticamente copias de seguridad de todos los datos a intervalos regulares. Las copias de seguridad automáticas se toman sin que ello afecte al rendimiento o la disponibilidad de las operaciones de base de datos NoSQL. Todas las copias de seguridad se almacenan por separado en otro servicio de almacenamiento y las copias de seguridad se duplican globalmente para resistencia contra desastres regionales. Las copias de seguridad automáticas están pensados para escenarios cuando se elimina accidentalmente su colección DocumentDB y más adelante requieren recuperación de datos o una solución de recuperación de desastres.  

En este artículo se inicia con un resumen de la redundancia de datos y la disponibilidad en DocumentDB y, a continuación, se describe las copias de seguridad. 

## <a name="high-availability-with-documentdb---a-recap"></a>Alta disponibilidad con DocumentDB - un resumen

DocumentDB está diseñada para ser [distribuida globalmente](documentdb-distribute-data-globally.md) : le permite ajustar el rendimiento en diferentes regiones Azure junto con la directiva condicionada por el API de varias direcciones IP transparentes y migración tras error. Como un sistema de base de datos que se ofrece la [disponibilidad del 99,99% SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_0/), todo el en DocumentDB se escribe duradera en discos locales por un quórum de réplicas dentro de un centro de datos locales antes de enviar una confirmación al cliente. Observe que la alta disponibilidad de DocumentDB se basa en el almacenamiento local y no dependen de las tecnologías de almacenamiento externo. Además, si su cuenta de base de datos está asociada con más de una región de Azure, la escritura se duplican en otras regiones también. Para ajustar los datos de rendimiento y el acceso de latencia baja, puede hacer muchas leer regiones asociadas a su cuenta de base de datos como desee. En cada región de lectura, los datos (duplicados) duradera se conservan en un conjunto de réplica.  

Como se muestra en el diagrama siguiente, una sola colección DocumentDB es [dividir horizontalmente](documentdb-partition-data.md). Una partición de"" se denota con un círculo en el siguiente diagrama, y cada partición está altamente disponible a través de un conjunto de réplica. Esta es la distribución local dentro de una única región de Azure (indicado por el eje X). Además, cada partición (con el conjunto de réplica correspondiente), a continuación, se distribuye globalmente en diferentes regiones asociadas a su cuenta de base de datos (por ejemplo, en esta regiones de ilustración los tres – US oriental, occidental de Estados Unidos y India Central). "Conjunto de particiones" es una distribución global que incluya entidad de varias copias de los datos de cada región (indicado por el eje Y). Puede asignar prioridad a las regiones asociadas a su cuenta de base de datos y DocumentDB transparente se migración tras error a la siguiente área en caso de desastre. Manualmente, puede simular migración tras error para comprobar la disponibilidad de llevar a cabo de la aplicación.  

La imagen siguiente muestra el alto grado de redundancia con DocumentDB.

![Alto grado de redundancia con DocumentDB](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-redundancy.png)


![Alto grado de redundancia con DocumentDB](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-global-distribution.png)

## <a name="full-automatic-online-backups"></a>Copias de seguridad automáticas, completas y en línea

¡Vaya, he eliminado mi colección o base de datos! Con DocumentDB, no solo los datos, pero las copias de seguridad de los datos también se realizan altamente redundantes y y son resistentes a desastres regionales. Estas copias de seguridad automatizadas actualmente se toman aproximadamente cada cuatro horas. 

Las copias de seguridad se toman sin que ello afecte al rendimiento o la disponibilidad de las operaciones de base de datos. DocumentDB toma la copia de seguridad en segundo plano sin consumir la preparación RUs o afectar al rendimiento y sin que ello afecte la disponibilidad de la base de datos NoSQL. 

A diferencia de los datos que se almacenan en DocumentDB, las copias de seguridad automáticas se almacenan en el servicio de almacenamiento de blobs de Windows Azure. Para garantizar la carga de latencia baja y eficaz, la instantánea de la copia de seguridad se carga como una instancia de almacenamiento de blobs de Windows Azure en la misma región como la región de escritura actual de su cuenta de base de datos de DocumentDB. La resistencia contra desastres regionales, de cada instantánea de los datos de copia de seguridad en el almacenamiento de blobs de Windows Azure nuevo se duplica a través de almacenamiento geo redundantes (GRS) para otra región. El siguiente diagrama muestra que se realizan copias de seguridad de toda la colección de DocumentDB (con todas las tres particiones primarias en oeste de Estados Unidos, en este ejemplo) en una cuenta de almacenamiento de blobs de Windows Azure remota en Estados Unidos occidental y, a continuación, replicar GRS oriental US. 

La imagen siguiente muestra copias de seguridad periódicas completas de todas las entidades de DocumentDB de almacenamiento de Azure GRS.

![Copias de seguridad periódicas completas de todas las entidades de DocumentDB de almacenamiento de Azure GRS](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-automatic-backup.png)


## <a name="retention-period-for-a-given-snapshot"></a>Período de retención para una determinada instantánea

Como se describió anteriormente, nos periódicamente tomar instantáneas de los datos y por nuestras reglamentaciones, nos conservar la última instantánea hacia arriba a 90 días antes de que finalmente se purga. Si se elimina una cuenta o colección, DocumentDB almacena la última copia de seguridad de 90 días.

## <a name="restore-database-from-the-online-backup"></a>Restaurar base de datos de la copia de seguridad en línea

En caso de elimina accidentalmente los datos, puede [archivo una incidencia de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) o [llamar al soporte de Azure](https://azure.microsoft.com/support/options/) restaurar los datos desde la última copia de seguridad automática. Una instantánea específica de la copia de seguridad pueden restaurarse, DocumentDB requiere que los datos eran mínimo disponibles con nosotros para la duración del ciclo de copia de seguridad de instantánea.

## <a name="next-steps"></a>Pasos siguientes

Para replicar la base de datos NoSQL en varios centros de datos, vea [distribuir datos globalmente con DocumentDB](documentdb-distribute-data-globally.md). 

Contacto soporte de Azure, [archivo de un vale desde el portal de Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)de archivo.