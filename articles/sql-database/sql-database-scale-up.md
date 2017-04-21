<properties
    pageTitle="Cambiar el nivel de rendimiento y los niveles de servicio de una base de datos de SQL Azure | Microsoft Azure"
    description="Cambiar el nivel de servicio y nivel de rendimiento de una base de datos de SQL Azure muestra cómo ampliar su base de datos SQL hacia arriba o hacia abajo. Cambiar el nivel de precio de una base de datos de SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-using-the-azure-portal"></a>Cambiar el nivel y rendimiento de nivel de servicio (nivel de precios) de una base de datos SQL con el portal de Azure


> [AZURE.SELECTOR]
- [**Portal de Azure**](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


Niveles de servicio y los niveles de rendimiento describen las características y recursos disponibles para la base de datos SQL y se pueden actualizar como las necesidades de la aplicación cambie. Para obtener más información, vea [Niveles de servicio](sql-database-service-tiers.md).

Tenga en cuenta que cambiar el nivel de servicio o nivel de rendimiento de una base de datos crea una réplica de la base de datos original en el nuevo nivel de rendimiento y, a continuación, cambia conexiones a la réplica. No se pierden datos durante este proceso pero durante brevemente cuando se cambie el a la réplica, están deshabilitadas conexiones a la base de datos, por lo que se pueden deshacer algunas transacciones en vuelo. Esta ventana varía, pero es Media en 4 segundos y en más del 99% de los casos es menor que 30 segundos. Con frecuencia, especialmente si existen grandes números de transacciones en vuelo en las conexiones de momento están deshabilitados, esta ventana puede ser mayor.  

La duración de todo el proceso de escalado depende en el nivel de servicio y tamaño de la base de datos antes y después del cambio. Por ejemplo, una base de datos de 250 GB que está cambiando a, desde o dentro de un nivel de servicio estándar, debe completar dentro de 6 horas. Para una base de datos del mismo tamaño que está cambiando los niveles de rendimiento en el nivel de servicio Premium, debe completarse dentro de 3 horas.


Use la información de los [niveles de servicio de base de datos de SQL Azure y niveles de rendimiento](sql-database-service-tiers.md) para determinar el nivel de rendimiento y de nivel de servicio adecuado para la base de datos de SQL Azure.

- Para degradar una base de datos, la base de datos debe ser menor que el tamaño máximo permitido de los niveles de servicio de destino. 
- Al actualizar una base de datos con la [Replicación de Geo](sql-database-geo-replication-overview.md) habilitada, primero debe actualizar sus bases de datos secundarios en el nivel de rendimiento deseado antes de actualizar la base de datos principal.
- Cuando cambio un nivel de servicio, primero debe finalizar todas las relaciones de replicación Geo. 
- Las ofertas de servicio de restauración son diferentes para los distintos niveles de servicio. Si se cambio puede perder la posibilidad de restaurar a un punto en el tiempo o tienen un período de retención de copia de seguridad inferior. Para obtener más información, vea [copia de seguridad de SQL Azure y restaurar](sql-database-business-continuity.md).
- Cambiar la base de datos nivel de precios no cambia el tamaño máximo de la base de datos. Para cambiar la base de datos tamaño máximo utilice [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- No se aplican a las nuevas propiedades de la base de datos hasta que se completan los cambios.



**Para completar este artículo, se necesita lo siguiente:**

- Una base de datos de SQL Azure. Si no tiene una base de datos SQL, crear una siguiendo los pasos de este artículo: [crear la primera base de datos de SQL Azure](sql-database-get-started.md).


## <a name="change-the-service-tier-and-performance-level-of-your-database"></a>Cambiar el nivel de rendimiento y los niveles de servicio de la base de datos


Abra el módulo de base de datos SQL para la base de datos que desee escalar hacia arriba o hacia abajo:

1.  En el [portal de Azure](https://portal.azure.com), haga clic en **más servicios** > **bases de datos SQL**.
2.  Haga clic en la base de datos que desea cambiar.
3.  En el módulo de la **base de datos SQL** , haga clic en **el nivel de precios (escala DTUs)**:

    ![nivel de precios][1]

1.  Elija un nuevo nivel y haga clic en **Seleccionar**:

    Haga clic en **Seleccionar** , se envía una solicitud de escala para cambiar el nivel de precios. Dependiendo del tamaño de la base de datos, la operación de escala puede tardar algún tiempo al completo (consulte la información en la parte superior de este artículo).

    > [AZURE.NOTE] Cambiar la base de datos nivel de precios no cambia el tamaño máximo de la base de datos. Para cambiar la base de datos tamaño máximo utilice [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).

    ![Seleccione el nivel de precios][2]

3.  Haga clic en el icono de notificación (campana), en la esquina superior derecha:

    ![notificaciones][3]

    Haga clic en el texto de notificación para abrir el panel de detalles, donde puede ver el estado de la solicitud.




## <a name="next-steps"></a>Pasos siguientes

- Cambiar el tamaño máximo de base de datos mediante [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) o [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- [Cambiar la escala y en](sql-database-elastic-scale-get-started.md)
- [Conectarse y consultar una base de datos SQL con SSMS](sql-database-connect-query-ssms.md)
- [Exportar una base de datos de SQL Azure](sql-database-export.md)

## <a name="additional-resources"></a>Recursos adicionales

- [Información general de continuidad empresarial](sql-database-business-continuity.md)
- [Documentación de la base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/new-tier.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png
