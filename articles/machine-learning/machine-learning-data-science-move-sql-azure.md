<properties 
    pageTitle="Mover los datos a una base de datos de SQL Azure para el aprendizaje Azure | Azure" 
    description="Crear la tabla de SQL y cargar los datos de tabla de SQL" 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/14/2016"
    ms.author="bradsev" /> 

# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Mover los datos a una base de datos de SQL Azure para aprendizaje del equipo de Azure

En este tema se describen las opciones para mover datos de archivos sin formato (formatos CSV o TSV) o de los datos almacenados en un servidor de SQL local a una base de datos de SQL Azure. Estas tareas para mover datos en la nube son parte del proceso de ciencias de datos del grupo.

Para un tema que describe las opciones para mover datos a un servidor local SQL Server para el aprendizaje, vea [mover datos de SQL Server en una máquina virtual Azure](machine-learning-data-science-move-sql-server-virtual-machine.md).

Los siguientes vínculos de **menú** a temas que describen cómo recopilar datos en entornos de destino donde los datos se pueden almacenar y procesar durante el proceso de ciencias de datos de equipo (TDSP).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

La siguiente tabla resume las opciones para mover datos a una base de datos de SQL Azure.

<b>ORIGEN</b> |<b>DESTINO: Base de datos SQL Azure</b> |
-------------- |--------------------------------|
<b>Archivo plano (CSV o TSV con formato)</b> |<a href="#bulk-insert-sql-query">Consulta de SQL de inserción masiva |
<b>SQL Server local</b> | 1. <a href="#export-flat-file">exportar a archivo plano<br> 2. <a href="#insert-tables-bcp">Asistente para la migración de base de datos SQL<br> 3. <a href="#db-migration">base de datos back hacia arriba y restaurar<br> 4. <a href="#adf">generador de datos de azure |


## <a name="prereqs"></a>Requisitos previos
Es necesario que tenga los procedimientos descritos a continuación:

* Una **suscripción de Azure**. Si no tiene una suscripción, puede registrarse para una [prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Una **cuenta de almacenamiento de Azure**. Usar una cuenta de almacenamiento de Azure para almacenar los datos en este tutorial. Si no tiene una cuenta de almacenamiento de Azure, vea el artículo [crear una cuenta de almacenamiento](storage-create-storage-account.md#create-a-storage-account) . Después de crear la cuenta de almacenamiento, debe obtener la clave de cuenta que utiliza para acceder al almacenamiento. Vea [la vista, copia y las teclas de acceso de almacenamiento regenerar](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Acceso a una **base de datos SQL Azure**. Si debe configurar una base de datos de SQL Azure, [Introducción a la base de datos de SQL de Microsoft Azure](../sql-database/sql-database-get-started.md) proporciona información sobre cómo aprovisionar una nueva instancia de una base de datos de SQL Azure.
* Instalado y configurado **Azure PowerShell** localmente. Para obtener instrucciones, consulte [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

**Datos**: se muestran los procesos de migración mediante el [conjunto de datos de Nueva York Taxi](http://chriswhong.com/open-data/foil_nyc_taxi/). El conjunto de datos de Nueva York Taxi contiene información sobre los datos de viaje y ferias y está disponible, como se indica a esa publicación, en el almacenamiento de blobs de Windows Azure: [Nueva York Taxi datos](http://www.andresmh.com/nyctaxitrips/). Un ejemplo y una descripción de estos archivos se proporcionan en la [Descripción del conjunto de datos de viajes de Nueva York Taxi](machine-learning-data-science-process-sql-walkthrough.md#dataset).
 
Puede adaptar los procedimientos que se describen aquí a un conjunto de sus propios datos, o bien, siga los pasos que se describe utilizando el conjunto de datos de Nueva York Taxi. Para cargar el conjunto de datos de Nueva York Taxi en la base de datos de SQL Server local, siga el procedimiento descrito en [Masa importar datos en la base de datos de SQL Server](machine-learning-data-science-process-sql-walkthrough.md#dbload). Estas son instrucciones para un servidor de SQL Server en una máquina Virtual de Azure, pero el procedimiento para cargar en el servidor de SQL local es la misma.


## <a name="file-to-azure-sql-database"></a>Mover los datos de un origen de archivo plano a una base de datos de SQL Azure

Pueden mover los datos en archivos sin formato (el formato CSV o TSV) a una base de datos de SQL Azure mediante una consulta de SQL de masa insertar.

### <a name="bulk-insert-sql-query"></a>Consulta de SQL de inserción masiva

Los pasos del procedimiento utilizando la consulta de SQL de masa insertar son similares a los tratan en las secciones para mover datos de un origen de archivo plano a SQL Server en una máquina virtual de Azure. Para obtener información detallada, vea [Consulta SQL insertar de forma masiva](machine-learning-data-science-move-sql-server-virtual-machine.md#insert-tables-bulkquery).


##<a name="sql-on-prem-to-sazure-sql-database"></a>Mover los datos del servidor de SQL Server local a una base de datos de SQL Azure

Si el origen de datos se almacena en un servidor local SQL Server, hay varias posibilidades para mover los datos a una base de datos de SQL Azure:

1. [Exportar a archivo plano](#export-flat-file) 
2. [Asistente para la migración de base de datos SQL](#insert-tables-bcp)
3. [Base de datos back hacia arriba y restaurar](#db-migration)
4. [Generador de datos de Azure](#adf)

Los pasos necesarios para los tres primeros son muy similares a las secciones de [mover los datos a SQL Server en una máquina virtual Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) que cubren los mismos procedimientos. En las siguientes instrucciones se proporcionan vínculos a las secciones correspondientes en dicho tema.

###<a name="export-flat-file"></a>Exportar a archivo plano

Los pasos para este exportar a un archivo plano son similares a los cubierto en [Exportar a archivo plano](machine-learning-data-science-move-sql-server-virtual-machine.md#export-flat-file).

###<a name="insert-tables-bcp"></a>Asistente para la migración de base de datos SQL

Los pasos para usar al Asistente para la migración de base de datos de SQL son similares a los incluidos en el [Asistente para la migración de base de datos de SQL](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-migration).

###<a name="db-migration"></a>Base de datos back hacia arriba y restaurar

Los pasos para usar la base de datos de copia de seguridad y restauración son similares a los cubierto de [copia de seguridad y restaurar la base de datos](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-backup).

###<a name="adf"></a>Generador de datos de Azure

El procedimiento para mover datos a una base de datos de SQL Azure con el generador de datos de Azure (ADF) está disponible en el tema de [mover los datos desde un servidor SQL de local a SQL Azure con el generador de datos de Azure](machine-learning-data-science-move-sql-azure-adf.md). Este tema muestra cómo mover los datos de una base de datos de SQL Server local a una base de datos de SQL Azure a través de almacenamiento de blobs de Windows Azure con ADF. 

Considere usar ADF cuando los datos deben migrarse continuamente en un escenario híbrido que tiene acceso a los recursos locales y de la nube y cuando los datos se lleva a cabo o deben modificarse o tiene lógica de negocios agregada cuando se migran. ADF permite la programación y la supervisión de trabajos mediante sencillas secuencias de comandos JSON que administración el movimiento de datos de forma periódica. ADF también tiene otras funciones, como soporte para operaciones más complejas.




