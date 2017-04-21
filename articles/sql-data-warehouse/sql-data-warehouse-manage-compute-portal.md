<properties
   pageTitle="Administrar la potencia de la informática en el almacén de datos de SQL Azure (portal de Azure) | Microsoft Azure"
   description="Azure tareas del portal administrar potencia. Recursos de cálculo escala ajustando DWUs. O bien, pausar y reanudar calculan recursos para ahorrar costos."
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
   ms.date="08/22/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-azure-portal"></a>Administrar la potencia de la informática en el almacén de datos de SQL Azure (portal de Azure)

> [AZURE.SELECTOR]
- [Información general](sql-data-warehouse-manage-compute-overview.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Ajustar el rendimiento del escalado calcular recursos y la memoria para satisfacer las necesidades cambiantes de su carga de trabajo. Ahorrar costos de recursos atrás escalado durante las horas no punta o pausar cálculo totalmente. 

Esta colección de tareas usa el portal de Azure para:

- Cálculo de escala
- Cálculo de pausa
- Cálculo de currículo

Para obtener más información, vea [Administrar calcular información general][].

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Potencia informática de escala

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para cambiar los recursos de cálculo:

1. Abra el [portal de Azure][], abra la base de datos y haga clic en **escala**.

    ![Haga clic en escala][1]

1. En el módulo de escala, mueva el control deslizante izquierda o derecha para cambiar la configuración de DWU.

    ![Mover el control deslizante][2]

1. Haga clic en **Guardar**. Aparece un mensaje de confirmación. Haga clic en **Sí** para confirmar o **no** para cancelar.

    ![Haga clic en Guardar][3]

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Cálculo de pausa

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar una base de datos:

1. Abra el [portal de Azure][] y abra la base de datos. Observe que el estado es **en línea**. 

    ![Estado de conexión][6]

1. Suspender recursos de memoria y de cálculo, haga clic en **Pausar**y, a continuación, en un mensaje de confirmación aparece. Haga clic en **Sí** para confirmar o **no** para cancelar.

    ![Confirmar pausa][7]

1. Mientras el almacenamiento de datos de SQL se inicia la base de datos, el estado es **Pausar**.
2. Cuando el estado está **en pausa**, se realiza la operación de pausar y ya no se está cargando para DWUs.

    ![Estado de pausa][4]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Cálculo de currículo

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]Para reanudar una base de datos:

1. Abra el [portal de Azure][] y abra la base de datos. Observe que el estado **en pausa**. 

    ![Base de datos de pausa][4]

1. Para reanudar la base de datos, haga clic en **Inicio**y, a continuación, aparece un mensaje de confirmación. Haga clic en **Sí** para confirmar o **no** para cancelar.

    ![Confirmar currículum vítae][5]

1. Mientras el almacenamiento de datos de SQL se inicia la base de datos, el estado es "Reanudar".
2. Cuando el estado es **en línea**, la base de datos está listo.

    ![Estado de conexión][6]

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, vea [información general de administración][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[Información general de administración]: ./sql-data-warehouse-overview-manage.md
[Administrar información general de cálculo]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[Portal de Azure]: http://portal.azure.com/
