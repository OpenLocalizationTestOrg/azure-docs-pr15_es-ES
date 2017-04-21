<properties
   pageTitle="Base de datos SQL Azure crea inquilinos varias aplicaciones con aislamiento y eficacia"
   description="Obtenga información sobre cómo se crea la base de datos de SQL inquilinos varias aplicaciones"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="builds-multi-tenant-apps-with-azure-sql-database-with-isolation-and-efficiency"></a>Crea inquilinos varias aplicaciones con la base de datos SQL Azure con aislamiento y eficacia

## <a name="leverage-elastic-pools-and-build-more-efficient-multi-tenant-apps"></a>Aproveche elásticos grupos y generar aplicaciones de múltiples arrendatarios más eficaces

Si es un desarrollo SaaS escribir una aplicación de múltiples arrendatario y tratamiento de muchos clientes, a menudo lo compensaciones de cliente rendimiento, administración y seguridad. Con Azure grupos SQL base de datos de elástico base de datos, ya no tiene que hacer ese compromiso. Estas agrupaciones ayudan a administrar y supervisar las aplicaciones de múltiples arrendatarios y aprovechar las ventajas de aislamiento de cliente de datos. Vea [los patrones de diseño para las aplicaciones de múltiples arrendatarios SaaS con base de datos SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

![generar-multi-inquilino-apps](./media/sql-database-build-multi-tenant-apps/sql-database-build-multi-tenant-apps.png)

## <a name="auto-scaling-you-control"></a>Puede controlar Autoescala

Grupos de cambiar automáticamente la escala de rendimiento y capacidad de almacenamiento de bases de datos elásticos sobre la marcha. Puede controlar el rendimiento asignado a un grupo, agregar o quitar elásticos bases de datos a petición y definir el rendimiento de bases de datos elásticos sin que ello afecte el costo total de la agrupación. Esto significa que no tiene que preocuparse por administrar el uso de bases de datos individuales.

[Lea la documentación](sql-database-elastic-pool.md)

## <a name="intelligent-management-of-your-environment"></a>Administración inteligente de su entorno

Recomendaciones integrados identifican de manera proactiva bases de datos que se benefician con grupos. Estas recomendaciones permiten análisis "y si" para la optimización rápido cumplir con los objetivos de rendimiento. Supervisión y paneles de solución de problemas de rendimiento de enriquecido ayudarle a visualizar la utilización de grupo histórica.

[Lea la documentación](sql-database-elastic-pool-guidance.md)

## <a name="performance-and-price-to-meet-your-needs"></a>Rendimiento y precio para satisfacer sus necesidades

Básico, estándar y agrupaciones de Premium proporcionan una amplia gama de rendimiento, almacenamiento y opciones de precios. Grupos pueden contener hasta 400 elásticos bases de datos. Bases de datos elásticos pueden Autoescala hasta unidades transacción de base de datos elástico 1000 (eDTU).

[Lea la documentación](https://azure.microsoft.com/pricing/details/sql-database/?b=16.50)

## <a name="elastic-tools"></a>Herramientas elásticas

Además de los grupos de elásticos, hay características de base de datos SQL para ayudarle a administrar actividades operativas múltiples bases de datos:

**Realizar consultas entre bases de datos e informes.**  
[Consulta de base de datos elástico](sql-database-elastic-query-overview.md) permite ejecutar consultas o informes en bases de datos en el grupo elástico y tener acceso a datos remotos almacenados en muchas bases de datos del grupo de servidores a la vez.

**Ejecutar transacciones entre bases de datos.**  
[Transacciones de base de datos elástico](sql-database-elastic-transactions-overview.md) poder ejecutar las transacciones que abarcan varias bases de datos en bases de datos de SQL y realizan operaciones (es decir, cuando se procesan las transacciones financieras en bases de datos o cuando se actualiza el inventario en una base de datos y pedidos).

**Ejecute las mismas operaciones en varias bases de datos.**  
[Trabajos de base de datos elástico](sql-database-elastic-jobs-overview.md) ejecutar operaciones administrativas como reconstruir índices o actualizar los esquemas en cada base de datos en el grupo elástico.

Vaya a la página principal para ver qué otra base de datos de SQL tiene que ofrecer.
[Mira esto](https://azure.microsoft.com/services/sql-database/) 

## <a name="next-steps"></a>Pasos siguientes

Obtener una [suscripción gratuita de Azure](https://azure.microsoft.com/get-started/) y [crear la primera base de datos de SQL Azure](sql-database-get-started.md).

## <a name="additional-resources"></a>Recursos adicionales

Explore las [capacidades de base de datos de SQL](https://azure.microsoft.com/services/sql-database/).
 
Revise la [información general técnica de base de datos de SQL](sql-database-technical-overview.md).  
