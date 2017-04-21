<properties
   pageTitle="Orientación técnica de la resistencia de recuperación de daños en los datos o el borrado accidental | Microsoft Azure"
   description="Artículo en comprender cómo recuperar de daños en los datos de los datos o eliminación de datos accidental a y diseñar aplicaciones tolerancia a errores de errores flexibles, altamente disponible, así como planificación de recuperación"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-data-corruption-or-accidental-deletion"></a>Orientación técnica de Azure resistencia: recuperación de daños en los datos o el borrado accidental

Parte de un plan de continuidad empresarial sólida tiene un plan si los datos obtiene daña o se elimina por accidente. La siguiente es información acerca de la recuperación después de daña o se elimina accidentalmente, debido a errores de aplicación o errores del operador de datos.

##<a name="virtual-machines"></a>Máquinas virtuales de Windows

Para proteger sus máquinas virtuales de Azure (a veces denominado máquinas virtuales de infraestructura como servicio) de errores de aplicación o el borrado accidental, use la [Copia de seguridad de Azure](https://azure.microsoft.com/services/backup/). Copia de seguridad de Azure permite la creación de copias de seguridad que sean coherentes en varios discos VM. Además, la cámara de copia de seguridad se pueden replicar en regiones para proporcionar recuperación de pérdida de región.

##<a name="storage"></a>Almacenamiento de información

Tenga en cuenta que mientras el almacenamiento de Azure proporciona la resistencia de datos a través de réplicas automatizadas, esto no impide que el código de la aplicación (o los desarrolladores/usuarios) dañe los datos a través de eliminación no deseada o accidental, actualización y así sucesivamente. Mantener la fidelidad de datos frente a errores de aplicación o usuario requiere técnicas más avanzadas, como copiar los datos a una ubicación de almacenamiento secundario con un registro de auditoría. Los desarrolladores pueden sacar partido de la blob [funcionalidad de instantáneas](https://msdn.microsoft.com/library/azure/ee691971.aspx), que puede crear instantáneas de punto en el tiempo de sólo lectura de contenido de blob. Puede utilizar como base de una solución de fidelidad de datos para el almacenamiento de Azure BLOB.

###<a name="blob-and-table-storage-backup"></a>BLOB y copia de seguridad de almacenamiento de tabla

Aunque son muy resistentes BLOB y tablas, siempre que representan el estado actual de los datos. Recuperación de no deseada modificación o eliminación de datos puede obligarle a restaurar los datos a un estado anterior. Para ello puede aprovechar las capacidades de Azure para almacenar y conservar una copia de un momento.

Para Blobs de Azure, puede realizar copias de seguridad de punto en el tiempo con la [función de instantánea blob](https://msdn.microsoft.com/library/ee691971.aspx). Para cada instantánea sólo se cargan para el almacenamiento necesario para almacenar las diferencias en el blob desde el último estado de instantánea. Las instantáneas dependen de la existencia de blob original que se basan, por lo que es aconsejable una operación de copia a otro blob o incluso otra cuenta de almacenamiento. Esto garantiza que los datos de copia de seguridad está protegidos correctamente contra eliminación accidental. Para las tablas de Azure, puede hacer copias de un momento a una tabla diferente o Blobs de Azure. Obtener más instrucciones y ejemplos de cómo realizar copias de seguridad de nivel de aplicación de tablas y BLOB pueden encontrarse aquí:

  * [Proteger las tablas de los errores de aplicación](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/05/03/protecting-your-tables-against-application-errors/)
  * [Proteger su BLOB contra los errores de aplicación](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/04/29/protecting-your-blobs-against-application-errors/)

##<a name="database"></a>Base de datos

Existen varias opciones de [continuidad empresarial](../sql-database/sql-database-business-continuity.md) (copia de seguridad, restaurar) para la base de datos de SQL Azure. Pueden copiar bases de datos utilizando la funcionalidad de [Copia de la base de datos](../sql-database/sql-database-copy.md) , o [Exportar](../sql-database/sql-database-export.md) e [Importar](https://msdn.microsoft.com/library/hh710052.aspx) un archivo de mochila de SQL Server. Copia de la base de datos proporciona resultados coherentes, mientras que una mochila (mediante el servicio de importación o exportación) no. Ambas de estas opciones ejecutan como servicios basados en cola dentro del centro de datos y no proporciona actualmente un SLA de hora de finalización.

>[AZURE.NOTE]Las opciones de base de datos de copia e importar/exportar coloque un grado significativo de carga en la base de datos de origen. Pueden desencadenar conflictos de recursos o límite de eventos.

###<a name="sql-database-backup"></a>Copia de seguridad de base de datos SQL

Copias de seguridad de punto en el tiempo de base de datos de SQL de Microsoft Azure se obtienen copiando [la base de datos de SQL Azure](../sql-database/sql-database-copy.md). Puede usar este comando para crear una copia de una base de datos coherente en el mismo servidor de base de datos lógica o a otro servidor. En cualquier caso, la copia de la base de datos es totalmente funcional y completamente independiente de la base de datos de origen. Cada copia que crear representa una opción de recuperación de punto en el tiempo. Puede recuperar el estado de la base de datos completamente cambiando la nueva base de datos con el nombre de la base de datos de origen. Como alternativa, puede recuperar un subconjunto específico de datos de la nueva base de datos mediante el uso de consultas Transact-SQL. Para obtener más información acerca de la base de datos de SQL, vea [información general sobre continuidad empresarial con la base de datos de SQL Azure](../sql-database/sql-database-business-continuity.md).

###<a name="sql-server-on-virtual-machines-backup"></a>SQL Server en máquinas virtuales copia de seguridad

Para utilizar con Azure infraestructura como un máquinas virtuales de servicio (a menudo denominadas IaaS o máquinas virtuales de IaaS) de SQL Server, hay dos opciones: tradicionales copias de seguridad y el envío de registro. Usar copias de seguridad tradicionales permite restaurar a un punto específico en el tiempo, pero el proceso de recuperación es lento. Restaurar copias de seguridad tradicionales requiere comenzando con una copia de seguridad completa inicial y, a continuación, aplicar después de las copias de seguridad. La segunda opción es configurar un registro de envío de una sesión para retrasar la restauración de copias de seguridad de registro (por ejemplo, por dos horas). Esto proporciona una ventana para recuperar de errores en el principal.

##<a name="other-azure-platform-services"></a>Otros servicios de la plataforma Windows Azure

Algunos servicios de la plataforma Windows Azure almacenan información en una cuenta de almacenamiento controlada por el usuario o la base de datos de SQL Azure. Si el recurso cuenta o almacenamiento se elimina o se daña, esto podría provocar errores graves con el servicio. En estos casos, es importante mantener copias de seguridad que le permitirá volver a crear estos recursos si se han eliminado o dañado.

Para sitios Web de Azure y Azure Servicios para dispositivos móviles, debe hacer copia de seguridad y mantener las bases de datos asociados. Para el servicio de Azure Media y máquinas virtuales, debe mantener la cuenta de almacenamiento de Azure asociada y todos los recursos en esa cuenta. Por ejemplo, para máquinas virtuales, debe realizar copias de seguridad y administrar los discos VM en el almacenamiento de blobs de Windows Azure.

##<a name="checklists-for-data-corruption-or-accidental-deletion"></a>Listas de comprobación para daños en los datos o el borrado accidental

##<a name="virtual-machines-checklist"></a>Lista de comprobación de máquinas virtuales

  1. Revise la sección de máquinas virtuales de este documento.
  2. Realizar copias de seguridad y mantener los discos VM con copia de seguridad de Azure (o su propio sistema de copia de seguridad con el almacenamiento de blobs de Windows Azure e instantáneas de disco duro virtual).

##<a name="storage-checklist"></a>Lista de comprobación de almacenamiento

  1. Revise la sección de almacenamiento de este documento.
  2. Copia con regularidad de recursos de almacenamiento de información crítica.
  3. Puede usar la función de instantánea para blobs.

##<a name="database-checklist"></a>Lista de comprobación de base de datos

  1. Revise la sección de la base de datos de este documento.
  2. Crear copias de seguridad en un momento mediante el comando Copiar de la base de datos.

##<a name="sql-server-on-virtual-machines-backup-checklist"></a>SQL Server en la lista de comprobación de copia de seguridad de máquinas virtuales

  1. Revise el servidor de SQL Server en la sección de copia de seguridad de máquinas virtuales de este documento.
  2. Utilizar tradicional copia de seguridad y restauración técnicas.
  3. Crear un registro retrasado sesión de envío.

##<a name="web-apps-checklist"></a>Lista de comprobación de aplicaciones Web

  1. Hacer copia de seguridad y mantener la base de datos asociado, si los hay.

##<a name="media-services-checklist"></a>Lista de comprobación de Media Services

  1. Realizar copias de seguridad y mantener los recursos de almacenamiento asociado.

##<a name="more-information"></a>Más información

Para obtener más información sobre las características de copia de seguridad y restauración de Azure, vea [almacenamiento, escenarios de copia de seguridad y recuperación](https://azure.microsoft.com/documentation/scenarios/storage-backup-recovery/).

##<a name="next-steps"></a>Pasos siguientes

En este artículo forma parte de una serie centrada en [orientación técnica de la resistencia de Azure](./resiliency-technical-guidance.md). Si está buscando más resistencia, recuperación y recursos de alta disponibilidad, consulte la orientación técnica de Azure resistencia [recursos adicionales](./resiliency-technical-guidance.md#additional-resources).