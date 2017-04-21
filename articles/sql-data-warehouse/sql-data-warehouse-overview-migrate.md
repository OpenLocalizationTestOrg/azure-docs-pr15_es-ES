<properties
   pageTitle="Migrar la solución al almacén de datos de SQL | Microsoft Azure"
   description="Guía de migración para poner la solución a la plataforma de almacenamiento de datos de SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="barbkess;jrj;sonyama"/>

# <a name="migrate-your-solution-to-sql-data-warehouse"></a>Migrar la solución al almacén de datos de SQL

Almacén de datos de SQL es un sistema de base de datos distribuida elástica escala para satisfacer sus necesidades. Para mantener el rendimiento y escala, no todas las características de SQL Server se implementan dentro de almacén de datos de SQL. Pulse en los siguientes temas de migración en algunos de los factores clave para migrar su solución al almacén de datos de SQL. Diseño de almacén de datos para escala presenta diseño diferente tramas y enfoques tradicionales así que no están siempre la mejor. Por lo tanto, puede encontrarse con que adaptar la solución garantiza que aprovecha al máximo la plataforma distribuida proporcionada por el almacén de datos de SQL.

También es importante recordar que el almacén de datos de SQL es una plataforma basada en Microsoft Azure. Por lo tanto, parte de la migración puede incluir también puede transferir los datos en la nube. Transferencia de datos es un tema en su propia derecha y debería considerar cuidadosamente; especialmente a medida que aumenta el volumen. Transferencia de datos y la carga de datos son temas discretas.

## <a name="migration-guidance"></a>Guía de migración

Asegúrese de que ha leído a través de estos artículos para asegurarse de que algunas de las diferencias de producto y conceptos fundamentales comprende antes de embarcarse en la migración.

- [Migrar su esquema][]
- [Migrar los datos][]
- [Migrar el código][]

## <a name="next-steps"></a>Pasos siguientes

El gato (grupo consultivo de cliente) también tiene algunos consejos excelente de almacén de datos SQL que publicación a través de blogs.  Eche un vistazo a su artículo, [migrar datos al almacén de datos de SQL Azure en la práctica][] para obtener orientación adicional sobre la migración.

<!--Image references-->

<!--Article references-->
[Migrar su esquema]: sql-data-warehouse-migrate-schema.md
[Migrar los datos]: sql-data-warehouse-migrate-data.md
[Migrar el código]: sql-data-warehouse-migrate-code.md


<!--MSDN references-->


<!--Other Web references-->
[Migrar los datos al almacén de datos de SQL Azure en la práctica]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
