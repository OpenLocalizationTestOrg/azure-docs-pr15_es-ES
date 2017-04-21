<properties
    pageTitle="Rendimiento y precio de grupo elástico de base de datos SQL"
    description="Información de precios específicos de grupos de elásticos de la base de datos."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="05/27/2016"
    ms.author="srinia"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="elastic-database-pool-billing-and-pricing-information"></a>Información de precios y facturación de grupo elásticos de la base de datos

Grupos de base de datos elástico se cargarán por las características siguientes:

- Un grupo elástico se factura después de su creación, incluso cuando no hay ninguna base de datos en el grupo.
- Un grupo elástico se cobra por hora. Esta es la misma frecuencia medición como para niveles de rendimiento de bases de datos.
- Si se cambia el tamaño de un grupo de elástico a una nueva cantidad de eDTUs, a continuación, el grupo no se cobra según la nueva cantidad de eDTUS hasta que se complete la operación de cambio de tamaño. Esto sigue el mismo patrón que cambiar el nivel de rendimiento de bases de datos independiente.


- El precio de un grupo de elástico se basa en el número de eDTUs del grupo. El precio de un grupo de elástico es independiente del número y el uso de las bases de datos elásticos dentro de ella.
- Precio se calcula mediante (número de grupo eDTUs) x (precio unitario por eDTU).

Precio unitario eDTU para un grupo de elástico es mayor que el precio DTU para una base de datos independiente en el mismo nivel de servicio. Para obtener información detallada, consulte [precios de base de datos de SQL](https://azure.microsoft.com/pricing/details/sql-database/). 


Para conocer los niveles de servicio y eDTUs, consulte [Opciones de la base de datos SQL y el rendimiento](sql-database-service-tiers.md).

## <a name="next-steps"></a>Pasos siguientes

- [Crear un grupo de elásticos de la base de datos](sql-database-elastic-pool-create-portal.md)
- [Supervisar, administrar y cambiar el tamaño de un grupo de elásticos de la base de datos](sql-database-elastic-pool-manage-portal.md)
- [Opciones de la base de datos SQL y rendimiento: comprender qué está disponible en cada nivel de servicio](sql-database-service-tiers.md)
- [Script de PowerShell para identificar las bases de datos adecuados para un grupo de elásticos de la base de datos](sql-database-elastic-pool-database-assessment-powershell.md)
