<properties 
   pageTitle="Precios recomendaciones de nivel de base de datos de SQL Azure" 
   description="Al cambiar los precios niveles en el portal de Azure, recomendaciones de nivel de precios son siempre que recomienda el nivel que mejor se adapta para ejecutar la carga de trabajo de existente Azure base de datos SQL. Niveles de precios describen el nivel de rendimiento y los niveles de servicio de una base de datos SQL." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="08/08/2016"
   ms.author="sstein"/>

# <a name="sql-database-pricing-tier-recommendations"></a>Recomendaciones de nivel de precios de base de datos SQL

 Recomendaciones de nivel de precios ilustra el nivel de rendimiento y los niveles de servicio es más adecuado para ejecutar la carga de trabajo de SQL Azure base de datos existente.

> [AZURE.NOTE] Recomendaciones de nivel de precios solo están disponibles para las bases de datos Web y empresa y agrupaciones elásticos de la base de datos y sólo está disponible en el [portal de Azure](https://portal.azure.com/).


Obtener precios recomendaciones de nivel durante las siguientes tareas:

- [Cambiar el nivel y rendimiento de nivel de servicio (nivel de precios) de una base de datos SQL](sql-database-scale-up.md)
- [Actualizar el servidor de SQL Azure a V12](sql-database-upgrade-server-portal.md)
- Vaya a su servidor V12. Vea [recomendaciones de nivel de precios de base de datos de SQL](sql-database-service-tier-advisor.md).
- [Crear un grupo de elásticos de la base de datos](sql-database-elastic-pool.md#elastic-database-pool-pricing-tier-recommendations)





## <a name="overview"></a>Información general

El servicio de base de datos SQL analiza el rendimiento actual y los requisitos de la función evaluar el uso de recursos histórica para una base de datos SQL. Además, el nivel de servicio mínimos aceptables se determina en función del tamaño de la base de datos y las características habilitadas [continuidad empresarial](sql-database-business-continuity.md) . 

Esta información se analiza y el nivel de rendimiento y los niveles de servicio que mejor se adapta para ejecutar la carga de trabajo de la base de datos típica y se recomienda mantener es actual conjunto de características.

- El servicio examina el anterior 15 a 30 días de datos históricos (uso de recursos, tamaño de la base de datos y actividad de la base de datos) y realiza una comparación entre la cantidad de consumo de recursos y las limitaciones de los niveles de servicio están disponibles y los niveles de rendimiento reales.
- Los datos se analizan en intervalos de 15 segundos y conjunto de resultados de cada intervalo se clasifica en el nivel de servicio existente y nivel de rendimiento que mejor se adapta para controlar la carga de trabajo de ese conjunto de resultados.
- Se recomienda el nivel de rendimiento y nivel de servicio que puede controlar óptima 95% de la carga de trabajo histórico y estos 15 segundos, a continuación, se agregan los ejemplos en el análisis de 15-30 días más grande.

### <a name="recommendations"></a>Recomendaciones

En función de uso de la base de datos, hay actualmente 2 categorías de recomendaciones que se pueden encontrar:


| Recomendación | Descripción |
| :--- | :--- |
| Actualizar | Actualizar a un nuevo nivel. |
| No disponible | Una base de datos requiere una carga de trabajo mínima o aproximadamente 35 días de la actividad. No es suficiente datos para proporcionar una recomendación válida. |

## <a name="getting-pricing-tier-recommendations"></a>Obtener recomendaciones de nivel de precios

Obtener recomendaciones de nivel de precios seleccionando una base de datos Web o empresa existente, haga clic en **toda la configuración**, haga clic en **el nivel de precios (escala DTUs)**. (Recomendaciones de nivel de precios también están disponibles cuando [actualice Azure SQL](sql-database-upgrade-server-portal.md)Server a V12.)

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/).
2. Haga clic en **Examinar** > **bases de datos SQL**.
4. En el módulo de **bases de datos SQL** , haga clic en la base de datos que desea ver una recomendación para:

    ![Seleccione la base de datos][1]

5. En el módulo de base de datos, seleccione **todas las opciones** , seleccione **el nivel de precios (escala DTUs)**.


7. Los **niveles de precios se recomienda** abrir donde puede haga clic en el nivel sugerido y, a continuación, haga clic en el botón **Seleccionar** para cambiar a ese nivel.

    ![Iniciar sesión en la vista previa][4]

8. Si lo desea, haga clic en **Ver detalles de uso** para abrir el módulo de **Detalles de recomendación de nivel de precios** , donde puede ver el nivel recomendado para la base de datos, una comparación de características entre niveles actuales y recomendados y un gráfico de análisis de uso de recursos histórica.

    ![Iniciar sesión en la vista previa][5]



## <a name="summary"></a>Resumen

Recomendaciones de nivel de precios proporcionan una experiencia automatizada de recopilación de datos de telemetría para cada base de datos SQL y recomendar la mejor servicio/performance de nivel nivel combinación según las necesidades de rendimiento real y requisitos de características de una base de datos. En el módulo de configuración haga clic en **el nivel de precios (escala DTUs)** para ver los precios recomendaciones de nivel para las bases de datos Web y empresa.



## <a name="next-steps"></a>Pasos siguientes

Dependiendo de los detalles de la base de datos específico, realizar una actualización o degradar normalmente no ocurre instantáneamente. El portal proporcionará notificaciones como las transiciones de la base de datos a su nuevo nivel, o puede supervisar el estado de actualización consultando la vista de [sys.dm_operation_status (base de datos de SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx) en la base de datos principal de la base de datos de SQL Server.


<!--Image references-->
[1]: ./media/sql-database-service-tier-advisor/select-database.png
[4]: ./media/sql-database-service-tier-advisor/choose-pricing-tier.png
[5]: ./media/sql-database-service-tier-advisor/usage-details.png


 
