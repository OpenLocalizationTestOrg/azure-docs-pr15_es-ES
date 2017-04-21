<properties
    pageTitle="Actualización a V12 de base de datos de SQL Azure con el portal de Azure | Microsoft Azure"
    description="Se explica cómo actualizar a V12 de base de datos de SQL Azure incluyendo cómo actualizar bases de datos Web y empresa y cómo se actualiza un servidor V11 migrar sus bases de datos directamente en un grupo de elásticos de la base de datos con el portal de Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="08/08/2016"
    ms.author="sstein"/>


# <a name="upgrade-to-azure-sql-database-v12-using-the-azure-portal"></a>Actualización a V12 de base de datos de SQL Azure con el portal de Azure


> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


V12 de base de datos de SQL es la versión más reciente, por lo que se recomienda actualizar los servidores existentes a V12 de base de datos de SQL.
V12 de base de datos de SQL tiene incluidos de muchas [ventajas con respecto a la versión anterior](sql-database-v12-whats-new.md) :

- Mayor compatibilidad con SQL Server.
- Rendimiento mejorado premium y nuevos niveles de rendimiento.
- [Grupos de elásticos de la base de datos](sql-database-elastic-pool.md).

Este artículo ofrece instrucciones para actualizar los servidores existentes V11 de base de datos de SQL y bases de datos a V12 de base de datos de SQL.

Durante el proceso de actualización a V12 actualizará las bases de datos Web y empresa a un nuevo nivel de servicio para que se incluyen instrucciones para actualizar bases de datos Web y empresa.

Además, puede ser más eficaz que la actualización a niveles de rendimiento individuales (precios niveles) para bases de datos único migrar a un [grupo elásticos de la base de datos](sql-database-elastic-pool.md) . Grupos también simplifican la administración de la base de datos porque solo necesita para administrar la configuración de rendimiento para el grupo en lugar de administrar por separado los niveles de rendimiento de bases de datos individuales. Si tiene bases de datos en varios servidores considere la posibilidad de ponerlos en el mismo servidor y aprovechar las ventajas de ponerlos en un grupo. Puede fácilmente [bases de datos de servidores V11 directamente en grupos de base de datos elástico con PowerShell de migrar automáticamente](sql-database-upgrade-server-powershell.md). También puede usar el portal de migrar V11 bases de datos a un grupo, pero en el portal ya debe tener un servidor V12 para crear un grupo. Se proporcionan instrucciones más adelante en este artículo para crear el grupo después de actualizar el servidor si tiene [bases de datos que pueden beneficiarse de un grupo](sql-database-elastic-pool-guidance.md).

Tenga en cuenta que las bases de datos permanecerá en línea y continuar trabajando en toda la operación de actualización. En el momento de la transición real para el nuevo nivel de rendimiento temporal de colocación de las conexiones de la base de datos puede ocurrir durante un período muy pequeño que normalmente es alrededor de 90 segundos pero puede ser 5 minutos. Si su aplicación tiene [errores transitorias tratamiento de interrupción de la conexión](sql-database-connectivity-issues.md) es suficiente para protegerse frente a las conexiones interrumpidas al final de la actualización.

Actualizar a V12 de base de datos de SQL no se puede deshacer. Después de actualizar el servidor no se puede revertir a V11.

Después de actualizar a V12, [recomendaciones de nivel de servicio](sql-database-service-tier-advisor.md) y [Consideraciones de rendimiento de grupo elásticos](sql-database-elastic-pool-guidance.md) no inmediatamente estarán disponibles hasta que el servicio tenga tiempo para evaluar las cargas de trabajo en el servidor de nuevo. Historial de recomendación V11 server no se aplica a V12 servidores para que no se conserva.

## <a name="prepare-to-upgrade"></a>Preparar la actualización

- **Actualizar todas las bases de datos Web y empresarial**: vea [Actualizar todas las bases de datos Web y empresa](sql-database-upgrade-server-portal.md#upgrade-all-web-and-business-databases) sección debajo o [Monitor y administrar un grupo de base de datos elásticas (PowerShell)](sql-database-elastic-pool-manage-powershell.md).
- **Revisar y suspender la replicación Geo**: si la base de datos de SQL Azure está configurada para replicación Geo debe documentar su configuración actual y [Detener la replicación de Geo](sql-database-geo-replication-portal.md#remove-secondary-database). Después de que finalice la actualización de volver a configurar la base de datos para la replicación de Geo.
- **Abrir estos puertos si dispone de clientes en una máquina virtual de Azure**: si el programa cliente se conecta a V12 de base de datos de SQL mientras el cliente se ejecuta en una máquina virtual (VM) en Azure, debe abrir intervalos de puerto 11000 11999 y 14000 a 14999 en la máquina virtual. Para obtener información detallada, consulte [puertos V12 de base de datos de SQL](sql-database-develop-direct-route-ports-adonet-v12.md).



## <a name="start-the-upgrade"></a>Iniciar la actualización

1. En el [portal de Azure](https://portal.azure.com/) , busque el servidor que desea actualizar, seleccione **Examinar** > **servidores SQL Server**y seleccionando el servidor de la versión 2.0 que desea actualizar.
2. Seleccionar la **Última actualización de la base de datos de SQL**, seleccione **actualizar este servidor**.

      ![actualizar el servidor][1]

3. Actualizar un servidor a la última actualización de la base de datos de SQL es permanente y irreversible. Para confirmar la actualización, escriba el nombre del servidor y haga clic en **Aceptar**.

## <a name="upgrade-all-web-and-business-databases"></a>Actualizar todas las bases de datos Web y empresa

Si el servidor tiene las bases de datos Web o empresa debe actualizarlos. Durante el proceso de actualización a V12 de base de datos de SQL se actualizarán todas las bases de datos Web y empresa a un nuevo nivel de servicio.    

Para ayudarle con la actualización, el servicio de base de datos SQL recomienda un nivel y rendimiento de nivel de servicio adecuado (nivel de precio) para cada base de datos. El servicio recomienda el nivel mejor para ejecutar la carga de trabajo de la base de datos existente mediante el análisis del uso histórico para la base de datos.

3. En el módulo **actualizar este servidor** seleccione cada base de datos para revisar y seleccione el precio recomendado nivel para actualizar a. Siempre puede examinar los niveles de precios disponibles y seleccione la opción que mejor se adapte a su entorno.


     ![bases de datos][2]


7. Tras hacer clic en el nivel sugerido aparecerá el módulo de **Elegir el nivel de precios** , donde puede seleccionar un nivel y, a continuación, haga clic en el botón **Seleccionar** para cambiar a ese nivel. Seleccione un nuevo nivel para cada base de datos Web o empresa.

    ¿Qué es importante tener en cuenta es que la base de datos SQL no está bloqueado en cualquier nivel de rendimiento o nivel de servicio específico, para que los requisitos de los cambios de la base de datos que pueda cambiar fácilmente entre los niveles de servicio disponible y los niveles de rendimiento. De hecho, Basic, estándar y bases de datos de SQL de Premium se cargarán por hora y tiene la capacidad de escalar cada base de datos hacia arriba o hacia abajo 4 veces en un plazo de 24 horas.

    ![recomendaciones][6]


Después de todas las bases de datos en el servidor son elegibles está listo para iniciar la actualización

## <a name="confirm-the-upgrade"></a>Confirme la actualización

3. Cuando todas las bases de datos en el servidor son elegibles para actualización debe **Escriba el nombre del servidor** para comprobar que va a realizar la actualización y, a continuación, haga clic en **Aceptar**.

    ![comprobar la actualización][3]


4. La actualización se inicia y muestra el progreso de notificación. Se inicia el proceso de actualización. Dependiendo de los detalles de las bases de datos específicas actualizar a V12 puede tardar algún tiempo. Durante este tiempo todas las bases de datos en el servidor permanecerá en línea, pero se restringido server y acciones de administración de la base de datos.

    ![actualización en curso][4]

    En el momento de la transición real con el rendimiento nuevo nivel temporal de colocación de las conexiones de la base de datos puede ocurrir durante un período muy pequeño (normalmente se mide en segundos). Si tiene una aplicación de control de errores transitorias (lógica de reintento) bajas conexión es suficiente para protegerse frente a las conexiones interrumpidas al final de la actualización.

5. Después de que finalice la operación de actualización el módulo de **Actualización más reciente** mostrará **habilitado**.

    ![V12 habilitado][5]  

## <a name="move-your-databases-into-an-elastic-database-pool"></a>Mover las bases de datos a un grupo de elásticos de la base de datos

En el [portal de Azure](https://portal.azure.com/) , busque el servidor V12 y haga clic en **Agregar grupo**.

- o bien-

Si ve un mensaje que indica que **haga clic aquí para ver los grupos de base de datos elástico recomendada para este servidor**, haga clic en ella para crear fácilmente un grupo que está optimizado para las bases de datos de su servidor. Para obtener información detallada, vea [Consideraciones de precio y rendimiento de un grupo elásticos de la base de datos](sql-database-elastic-pool-guidance.md).

![Agregar grupo a un servidor][7]

Siga las instrucciones en el artículo [crear un grupo de la base de datos elástica](sql-database-elastic-pool.md) para terminar de crear el grupo.

## <a name="monitor-databases-after-upgrading-to-sql-database-v12"></a>Supervisar las bases de datos después de actualizar a V12 de base de datos de SQL

>[AZURE.IMPORTANT] Actualizar a la última versión de SQL Server Management Studio (SSMS) para aprovechar las nuevas capacidades de v12. [Descargar SQL Server Management Studio] (https://msdn.microsoft.com/library/mt238290.aspx).

Después de actualizar, conocidas supervisar la base de datos para asegurarse de que se están ejecutando aplicaciones en el rendimiento que desee y, a continuación, optimice la configuración según sea necesario.

Además de supervisión de bases de datos individuales, puede supervisar elásticos de la base de datos grupos [Monitor, administrar y cambiar el tamaño de un grupo de elásticos de la base de datos con el portal de Azure](sql-database-elastic-pool-manage-portal.md) o con [PowerShell](sql-database-elastic-pool-manage-powershell.md).


**Datos de consumo de recursos:** Para bases de datos básico, estándar y Premium están disponibles a través de la [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) DMV en la base de datos de usuario datos de consumo de recursos. Este DMV proporciona cerca de la información de consumo del recurso en tiempo real en detalle segunda 15 para la hora de operación anterior. El consumo de porcentaje DTU por un intervalo se calcula como el consumo de porcentaje máximo de las dimensiones de la CPU, IO y registro. Esta es una consulta para calcular el promedio consumo de porcentaje DTU sobre la última hora:

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Información de supervisión adicional:

- [Guía de rendimiento de base de datos de SQL azure para bases de datos](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Consideraciones de precio y rendimiento para un grupo de elásticos de la base de datos](sql-database-elastic-pool-guidance.md).
- [Supervisión con vistas dinámicas de administración de base de datos SQL Azure](sql-database-monitoring-with-dmvs.md)




**Alertas:** Configurar 'Alertas' en el portal de Azure para que le notifique cuando el consumo de DTU para una base de datos actualizado acerca determinado nivel alto. Alertas de base de datos puede configurar en el portal de Azure para varias métricas de rendimiento como DTU, CPU, IO y registro. Vaya a la base de datos y seleccione **las reglas de alertas** en el módulo de **configuración** .

Por ejemplo, puede configurar una alerta de correo electrónico en "Porcentaje DTU" si el valor promedio de porcentaje DTU supera el 75% sobre los últimos 5 minutos. Para obtener más información sobre cómo configurar las notificaciones de alerta, consulte [recibir notificaciones de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) .





## <a name="next-steps"></a>Pasos siguientes

- [Buscar recomendaciones de grupo y crear un grupo](sql-database-elastic-pool-create-portal.md).
- [Cambiar el nivel de rendimiento y los niveles de servicio de la base de datos](sql-database-scale-up.md).



## <a name="related-links"></a>Vínculos relacionados

- [Novedades de V12 de base de datos de SQL](sql-database-v12-whats-new.md)
- [Planear y prepararse para actualizar a V12 de base de datos de SQL](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-upgrade-server-portal/latest-sql-database-update.png
[2]: ./media/sql-database-upgrade-server-portal/upgrade-server2.png
[3]: ./media/sql-database-upgrade-server-portal/upgrade-server3.png
[4]: ./media/sql-database-upgrade-server-portal/online-during-upgrade.png
[5]: ./media/sql-database-upgrade-server-portal/enabled.png
[6]: ./media/sql-database-upgrade-server-portal/recommendations.png
[7]: ./media/sql-database-upgrade-server-portal/new-elastic-pool.png
