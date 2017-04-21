<properties
    pageTitle="Archivar una base de datos de SQL Azure a un archivo de MOCHILA con el Portal de Azure"
    description="Archivar una base de datos de SQL Azure a un archivo de MOCHILA con el Portal de Azure"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/15/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="archive-an-azure-sql-database-to-a-bacpac-file-using-the-azure-portal"></a>Archivar una base de datos de SQL Azure a un archivo de MOCHILA con el Portal de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

Este artículo proporcionan instrucciones para archivar su base de datos de SQL Azure a un archivo de MOCHILA (almacenado en el almacenamiento de blobs de Windows Azure) con el [portal de Azure](https://portal.azure.com).

Cuando necesite crear un archivo de una base de datos de SQL Azure, puede exportar el esquema de la base de datos y los datos a un archivo de MOCHILA. Un archivo de MOCHILA es simplemente un archivo ZIP con una extensión de MOCHILA. Un archivo de MOCHILA puede almacenarse más adelante en el almacenamiento de blobs de Windows Azure o en almacenamiento local en una ubicación local y más tarde importados atrás en la base de datos de SQL Azure o en un servidor SQL Server instalación local. 

***Consideraciones***

- En un archivo ser coherentes, no debe asegurarse de que ninguna escritura actividad se produce durante la exportación o que va a exportar una [copia coherente](sql-database-copy.md) de la base de datos de SQL Azure.
- El tamaño máximo de un archivo de MOCHILA archivado al almacenamiento de blobs de Windows Azure es 200 GB. Para archivar un archivo MOCHILA mayor al almacenamiento local, use la herramienta de símbolo de [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Esta utilidad se incluye con Visual Studio y SQL Server. También puede [Descargar](https://msdn.microsoft.com/library/mt204009.aspx) la última versión de SQL Server Data Tools para obtener esta utilidad.
- No se admite el archivado con el almacenamiento de Azure premium mediante un archivo de MOCHILA.
- Si la operación de exportación supera 20 horas, se puede cancelar. Para aumentar el rendimiento durante la exportación, puede:
 - Aumentar temporalmente el nivel de servicio.
 - Dejen de todos de lectura y escriben actividad durante la exportación.
 - Utilizar un [índice agrupado](https://msdn.microsoft.com/library/ms190457.aspx) con valores no nulos en todas las tablas de gran tamaño. Sin índices agrupados, una exportación puede fallar si tiene más de 6-12 horas. Esto es porque el servicio de exportación debe completar un recorrido de tabla para exportar la tabla completa. Ejecute **DBCC SHOW_STATISTICS** y asegúrese de que el *RANGE_HI_KEY* no es nulo y su valor tiene buena distribución es una buena forma de determinar si las tablas están optimizadas para exportar. Para obtener más información, consulte [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).


> [AZURE.NOTE] BACPACs no están diseñados para su uso para copia de seguridad y restaurar las operaciones. Base de datos de SQL Azure crea automáticamente las copias de seguridad para cada base de datos de usuario. Para obtener más información, vea [Información general de continuidad empresarial](sql-database-business-continuity.md).

Para completar este artículo, se necesita lo siguiente:

- Una suscripción de Azure.
- Una base de datos SQL Azure. 
- Una [cuenta de almacenamiento de Azure estándar](../storage/storage-create-storage-account.md) con un contenedor de blobs para almacenar la MOCHILA de almacenamiento estándar.

## <a name="export-your-database"></a>Exportar la base de datos

Abra el módulo de base de datos SQL para la base de datos que desea exportar.

> [AZURE.IMPORTANT] Para garantizar un archivo MOCHILA coherente debe [crear una copia de la base de datos](sql-database-copy.md) y, a continuación, exporte la copia de la base de datos. 

1.  Vaya al [portal de Azure](https://portal.azure.com).
2.  Haga clic en **bases de datos SQL**.
3.  Haga clic en la base de datos para archivar.
4.  En el módulo de base de datos de SQL, haga clic en **Exportar** para abrir el módulo de **exportación de la base de datos** :

    ![botón Exportar][1]

5.  Haga clic en **almacenamiento** y seleccione el contenedor de almacenamiento cuenta y blob donde se almacena la MOCHILA:

    ![Exportar a base de datos][2]

6. Seleccione el tipo de autenticación. 
7.  Escriba las credenciales de autenticación apropiado para el servidor de SQL Azure que contiene la base de datos que se va a exportar.
8.  Haga clic en **Aceptar** para archivar la base de datos. Haga clic en **Aceptar** crea una solicitud de base de datos de exportación y envía al servicio. La longitud de tiempo que tardará la exportación depende del tamaño y la complejidad de la base de datos y su nivel de servicio. Recibirá una notificación.

    ![notificación de exportación][3]

## <a name="monitor-the-progress-of-the-export-operation"></a>Supervisar el progreso de la operación de exportación

1.  Haga clic en **servidores de SQL Server**.
2.  Haga clic en el servidor que contiene la base de datos (origen) original que acaba de archivado.
3.  Desplácese a las operaciones.
4.  En el módulo SQL server, haga clic en **importar o exportar historial**:

    ![Importar Exportar historial][4]

## <a name="verify-the-bacpac-is-in-your-storage-container"></a>Compruebe que la MOCHILA está en el contenedor de almacenamiento

1.  Haga clic en **cuentas de almacenamiento**.
2.  Haga clic en la cuenta de almacenamiento que se ha guardado el archivo MOCHILA.
3.  Haga clic en **contenedores** y seleccione el contenedor que exportó la base de datos para obtener más información (puede descargar y guardar la MOCHILA desde aquí).

    ![Detalles del archivo .bacpac][5]  

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre la importación una MOCHILA a una base de datos de SQL Azure, consulte [importar un BACPCAC a una base de datos de SQL Azure](sql-database-import.md)
- Para obtener información sobre la importación una MOCHILA a una base de datos de SQL Server, consulte [importar un BACPCAC a una base de datos de SQL Server](https://msdn.microsoft.com/library/hh710052.aspx)



<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png

