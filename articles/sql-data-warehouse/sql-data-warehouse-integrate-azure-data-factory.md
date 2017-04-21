<properties
   pageTitle="Usar el generador de datos de Azure con almacén de datos SQL | Microsoft Azure"
   description="Sugerencias para usar el generador de datos de Azure (ADF) con el almacenamiento de datos de SQL Azure para desarrollar soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>Usar el generador de datos de Azure con almacén de datos SQL

Generador de datos de Azure proporciona un método totalmente gestionado para coordinar la transferencia de datos y la ejecución de procedimientos almacenados en el almacén de datos de SQL.  Esto le permitirá más fácilmente configuración y programación extraer transformar y carga (ETL) procedimientos complejos con el almacén de datos de SQL. Para obtener una descripción más completa de generador de datos de Azure, consulte la [documentación del generador de datos de Azure][].

## <a name="data-movement"></a>Movimiento de datos

Generador de datos de Azure permite mover datos entre orígenes locales y diversos servicios de Azure.  Integración general, actual con el generador de datos de Azure admite el movimiento de datos hacia y desde las siguientes ubicaciones:

+ Almacenamiento de blobs de Windows Azure
+ Base de datos SQL Azure
+ Implementación local de SQL Server
+ SQL Server en IaaS

Para obtener información sobre cómo configurar los datos de una actividad de copia consulte [Copiar datos con el generador de datos de Azure][]

## <a name="stored-procedures"></a>Procedimientos almacenados
 En la misma manera que se puede utilizar para programar la transferencia de datos, el generador de datos de Azure también puede usarse para coordinar la ejecución de procedimientos almacenados.  Esto permite canalizaciones más complejas que debe crearse y amplía la capacidad del generador de datos de Azure aprovechar la capacidad de almacenamiento de datos de SQL.

## <a name="next-steps"></a>Pasos siguientes
Para obtener información general de integración, consulte [Descripción de la integración de almacén de datos de SQL][].
Para obtener más sugerencias de desarrollo, vea [información general sobre el desarrollo de almacén de datos de SQL][].

<!--Image references-->

<!--Article references-->

[Copiar datos con el generador de datos de Azure]: ../data-factory/data-factory-data-movement-activities.md
[Información general sobre el desarrollo de almacén de datos SQL]: ./sql-data-warehouse-overview-develop.md
[Descripción de la integración de almacén de datos SQL]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Documentación de generador de datos de Azure]:https://azure.microsoft.com/documentation/services/data-factory/

