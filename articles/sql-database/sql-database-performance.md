<properties 
   pageTitle="Información de rendimiento de base de datos SQL Azure | Microsoft Azure" 
   description="La base de datos de SQL Azure proporciona herramientas de rendimiento para ayudarle a identificar las áreas que pueden mejorar el rendimiento de la consulta actual." 
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
   ms.date="07/19/2016"
   ms.author="sstein"/>

# <a name="sql-database-performance-insight"></a>Información de rendimiento de base de datos SQL

Base de datos de SQL Azure proporciona herramientas de rendimiento para ayudarle a identificar y mejorar el rendimiento de las bases de datos al proporcionar recomendaciones y acciones de optimización inteligentes. 

1. Vaya a la base de datos en el [Portal de Azure](http://portal.azure.com) y haga clic en **configuración de todos los** > **rendimiento **  >  **Descripción general** para abrir la página de **rendimiento** . 


2. Haga clic en **recomendaciones** para abrir el [Asistente de la base de datos de SQL](#sql-database-advisor)y haga clic en **las consultas** para abrir [Una perspectiva de rendimiento de consulta](#query-performance-insight).

    ![Ver el rendimiento](./media/sql-database-performance/entries.png)



## <a name="performance-overview"></a>Información general de rendimiento

Haga clic en **información general** o en el mosaico de **rendimiento** le llevará a Panel de rendimiento de la base de datos. Esta vista proporciona un resumen de rendimiento de su base de datos y ayuda con la optimización del rendimiento y solución de problemas. 

![Rendimiento](./media/sql-database-performance/performance.png)

- El mosaico de **recomendaciones** proporciona un desglose de ajuste recomendaciones para la base de datos (recomendaciones 3 principales se muestran si hay más). Haga clic en este mosaico le lleva a **Asesor de base de datos de SQL**. 
- El mosaico de la **actividad de ajuste** proporciona un resumen de la en curso y completadas ajuste acciones para la base de datos, que le da una vista rápida en el historial de ajuste de la actividad. Haga clic en este mosaico le lleva a la vista de historial de optimización completa de la base de datos.
- El mosaico de **ajuste automático** muestra la configuración de ajuste automático para la base de datos (se configuran qué acciones de ajuste que se aplican automáticamente a la base de datos). Haga clic en este mosaico, se abre el cuadro de diálogo de configuración de automatización.
- El mosaico de **consultas de base de datos** muestra el resumen del rendimiento de consulta para la base de datos (generales DTU principio y el uso de los recursos consumir consultas). Haga clic en este mosaico le lleva a **Una perspectiva de rendimiento de consulta**.



## <a name="sql-database-advisor"></a>Asesor de base de datos SQL


[Asesor de base de datos de SQL](sql-database-advisor.md) proporciona recomendaciones de optimización inteligentes que pueden ayudar a mejorar el rendimiento de la base de datos. 

- Recomendaciones sobre que indiza para crear o quitar (y una opción para aplicar las recomendaciones de índice automáticamente sin intervención del usuario y deshacer automáticamente recomendaciones que tienen un impacto negativo en el rendimiento).
- Recomendaciones cuando se identifican los problemas de esquema de la base de datos.
- Recomendaciones cuando las consultas pueden beneficiarse de consultas parametrizadas.




## <a name="query-performance-insight"></a>Información de rendimiento de consulta

[Información de rendimiento de consulta](sql-database-query-performance.md) permite invertir menos tiempo para solucionar problemas de rendimiento de la base de datos al proporcionar:

- Conocimiento más profundo en su consumo de recursos (DTU) de las bases de datos. 
- La CPU superior consumir consultas, lo que pueden ajustarse para mejorar el rendimiento. 
- La capacidad para profundizar en los detalles de una consulta. 


## <a name="additional-resources"></a>Recursos adicionales

- [Guía de rendimiento de base de datos de SQL Azure para bases de datos](sql-database-performance-guidance.md)
- [¿Cuándo se debe utilizar un grupo de la base de datos elástico?](sql-database-elastic-pool-guidance.md)