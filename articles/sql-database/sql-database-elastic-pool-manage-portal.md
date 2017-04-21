<properties
    pageTitle="Supervisar y administrar un grupo de elásticos de la base de datos con el portal de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo usar el portal de Azure e inteligencia integrada de base de datos de SQL para administrar, supervisar y ajustar un grupo de base de datos elástico scalable para optimizar el rendimiento de la base de datos y administrar los costos."
    keywords=""
    services="sql-database"
    documentationCenter=""
    authors="ninarn"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/22/2016"
    ms.author="ninarn"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="monitor-and-manage-an-elastic-database-pool-with-the-azure-portal"></a>Supervisar y administrar un grupo de elásticos de la base de datos con el portal de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Puede usar el portal de Azure para supervisar y administrar un grupo de elásticos de la base de datos y las bases de datos en el grupo. Desde el portal, puede supervisar el uso de un grupo de elástico y las bases de datos dentro de ese grupo. También puede realizar un conjunto de cambios en el grupo elástico y enviar todos los cambios al mismo tiempo. Estos cambios incluyen agregando o quitando bases de datos, cambiar la configuración de su grupo elástico o cambiar la configuración de la base de datos.

El siguiente gráfico muestra un grupo de elásticos de ejemplo. La vista incluye:

*  Gráficos para supervisar el uso de la agrupación elástica y las bases de datos en el grupo de recursos.
*  El botón de grupo **Configurar** , para realizar cambios en el grupo elástico.
*  Botón **Crear base de datos** que crea una nueva base de datos y lo agrega a la agrupación elástica actual.
*  Elásticos trabajos que le ayudan a administración un gran número de bases de datos mediante la ejecución de secuencias de comandos Transact-SQL en todas las bases de datos en una lista.

![Vista de grupo][2]

Para trabajar en los pasos de este artículo, tendrá un servidor de SQL server en Azure con al menos una base de datos y un grupo elástico. Si no tiene un grupo elástico, vea [crear un grupo](sql-database-elastic-pool-create-portal.md); Si no tiene una base de datos, consulte el [tutorial de base de datos SQL](sql-database-get-started.md).

## <a name="elastic-pool-monitoring"></a>Grupo elástico supervisión

Puede ir a un grupo determinado para ver el uso de recursos. De forma predeterminada, el grupo está configurado para mostrar eDTU de almacenamiento y el uso de la última hora. El gráfico puede estar configurado para mostrar diferentes métricas sobre varias ventanas de tiempo.

1. Seleccione un grupo para que funcione con.
2. En **Supervisión elástico de grupo** es un gráfico etiquetado **utilización de los recursos**. Haga clic en el gráfico.

    ![Grupo elástico supervisión][3]

    Se abrirá el módulo **métrica** , que muestra una vista detallada de las mediciones especificadas sobre la ventana de tiempo especificado.   

    ![Módulo métrica][9]

### <a name="to-customize-the-chart-display"></a>Para personalizar la presentación de gráfico

Puede editar el gráfico y el módulo métrico para mostrar otras métricas como porcentaje de CPU, porcentaje de IO de datos y porcentaje de IO de registro utilizado.

2. En el módulo métrico, haga clic en **Editar**.

    ![Haga clic en Editar][6]

- En el módulo de **Editar gráfico** , seleccione un nuevo intervalo de tiempo (más allá de la hora, en la actualidad, o semana pasada) o haga clic en **personalizada** para seleccionar un intervalo de fecha en las últimas dos semanas. Seleccione el tipo de gráfico (barra o línea) y luego seleccione los recursos para supervisar.

> Nota: Solo métricas con la misma unidad de medida se pueden mostrar en el gráfico al mismo tiempo. Por ejemplo, si selecciona "eDTU porcentaje", a continuación, solo podrá seleccionar otras métricas con porcentaje como la unidad de medida.

    ![Click edit](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- A continuación, haga clic en **Aceptar**.


## <a name="elastic-database-monitoring"></a>Supervisión elástico base de datos

También se pueden supervisar bases de datos individuales de un problema potencial.

1. En **Supervisión elástico de base de datos**, hay un gráfico que muestra métricas de cinco bases de datos. De forma predeterminada, el gráfico muestra las bases de datos de 5 superiores en el grupo por uso promedio eDTU en la última hora. Haga clic en el gráfico.

    ![Grupo elástico supervisión][4]

2. Aparece el módulo de **Uso de recursos de base de datos** . Esto proporciona una vista detallada de uso de base de datos en el grupo. Usando una cuadrícula en la parte inferior del módulo, puede seleccionar las bases de datos en el grupo para mostrar su uso en el gráfico (un máximo de 5 bases de datos). También puede personalizar la ventana de métricas y la hora que se muestra en el gráfico haciendo clic en **Editar gráfico**.

    ![Módulo de utilización de recursos de base de datos][8]

### <a name="to-customize-the-view"></a>Para personalizar la vista

1. En el módulo de **uso de recursos de base de datos** , haga clic en **Editar gráfico**.

    ![Haga clic en Editar gráfico](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. En la hoja de gráfico **Editar** , seleccione un nuevo intervalo de tiempo (más allá de hora o últimas 24 horas) o haga clic en **personalizada** para seleccionar otro día en las últimas dos semanas para mostrar.

    ![Haga clic en personalizada](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. Haga clic en la lista desplegable de **comparación de bases de datos** para seleccionar una métrica diferentes para usar cuando se comparan las bases de datos.

    ![Editar el gráfico](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>Para seleccionar las bases de datos para supervisar

En la lista de la base de datos en el módulo de **Uso de recursos de base de datos** , puede encontrar las bases de datos determinados desplazándose por las páginas en la lista o escriba el nombre de una base de datos. Use la casilla de verificación para seleccionar la base de datos.

![Búsqueda de bases de datos supervisar][7]


## <a name="add-an-alert-to-a-pool-resource"></a>Agregar una alerta para un recurso de grupo

Puede agregar reglas a los recursos que enviar correo electrónico a personas o cadenas de alerta a los extremos de la dirección URL cuando llega a un umbral de utilización que haya configurado el recurso.

> [AZURE.IMPORTANT]Utilización de los recursos supervisión para grupos de elástico tiene un retraso de al menos 20 minutos. Configurar alertas de menos de 30 minutos para los grupos de elásticos no es compatible actualmente. Configuración de todas las alertas para grupos elástico con un período (parámetro denominado "-tamañoDeVentana" en la API de PowerShell) de menos de 30 minutos posible no se desencadene. Asegúrese de que las alertas que definir para grupos de elástico use un punto (WindowSize) de 30 minutos o más.

**Para agregar una alerta para cualquier recurso:**

1. Haga clic en el gráfico de **uso de recursos** para abrir el módulo **métrica** , haga clic en **Agregar aviso**y, a continuación, rellene la información en el módulo de **Agregar una regla de alerta** (**recurso** se configura automáticamente a ser el grupo que está trabajando con).
2. Escriba un **nombre** y una **Descripción** que identifica la alerta y los destinatarios.
3. Elija una **métrica** que desee de la lista de alerta.

    El gráfico muestra dinámicamente la utilización de los recursos para esa métrica para que pueda elegir un umbral.

4. Elija una **condición** (superior, inferior, etc.) y un **umbral**.
5. Haga clic en **Aceptar**.



## <a name="move-a-database-into-an-elastic-pool"></a>Mover una base de datos a un grupo de elástico

Puede agregar o quitar las bases de datos de un grupo existente. Las bases de datos pueden ser de otros grupos. Sin embargo, sólo puede agregar bases de datos que se encuentran en el mismo servidor lógico.

1. En el módulo para el grupo, en **elásticos bases de datos** , haga clic en **Configurar grupo**.

    ![Haga clic en Configurar grupo][1]

2. En el módulo de **grupo configurar** , haga clic en **Agregar al grupo**.

    ![Haga clic en Agregar al grupo de servidores](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)


3. En el módulo de **agregar bases de datos** , seleccione la base de datos o las bases de datos para agregar al grupo. A continuación, haga clic en **Seleccionar**.

    ![Seleccione las bases de datos para agregar](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    Ahora enumera en el módulo de **grupo configurar** la base de datos seleccionada se agregue con su estado de **pendiente**.

    ![Adiciones de grupo pendiente](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. En el **módulo de grupo configurar**, haga clic en **Guardar**.

    ![Haga clic en Guardar](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="move-a-database-out-of-an-elastic-pool"></a>Mover una base de datos fuera de un grupo de elástico

1. En el módulo de **grupo configurar** , seleccione la base de datos o las bases de datos para quitar.

    ![lista de bases de datos](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. Haga clic en **Quitar del grupo**.

    ![lista de bases de datos](./media/sql-database-elastic-pool-manage-portal/click-remove.png)

    Ahora enumera en el módulo de **grupo configurar** la base de datos seleccionada que se eliminen con su estado de **pendiente**.

    ![Adición de base de datos de vista previa y eliminación](./media/sql-database-elastic-pool-manage-portal/pending-removal.png)

3. En el **módulo de grupo configurar**, haga clic en **Guardar**.

    ![Haga clic en Guardar](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="change-performance-settings-of-a-pool"></a>Cambiar la configuración de rendimiento de un grupo

Supervisar el uso de un grupo de recursos, es posible que algunos de los ajustes necesarios. ¿El grupo necesita un cambio en los límites de almacenamiento de rendimiento. Posiblemente desee cambiar la configuración de la base de datos en el grupo. Puede cambiar la configuración del grupo en cualquier momento para obtener el mejor equilibrio de rendimiento y costo. Consulte [cuándo se debe utilizar un grupo de la base de datos elástico?](sql-database-elastic-pool-guidance.md) para obtener más información.

**Para cambiar los límites de almacenamiento o eDTUs por grupo y eDTUs por base de datos:**

1. Abra el módulo de **grupo de configurar** .

    En **configuración de la agrupación de base de datos elástico**, use cualquier control deslizante para cambiar la configuración de grupo.

    ![Uso de recursos de grupo elásticas](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Cuando cambia la configuración de la pantalla muestra el costo mensual estimado del cambio.

    ![Actualizar un grupo y el nuevo coste mensual](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)


## <a name="create-and-manage-elastic-jobs"></a>Crear y administrar trabajos elásticos

Trabajos elásticos le permiten ejecutar secuencias de comandos de Transact-SQL en cualquier número de bases de datos en el grupo. Puede crear nuevas tareas o administrar trabajos existentes con el portal.

![Crear y administrar trabajos elásticos][5]


Antes de utilizar trabajos, instale los componentes de trabajos elásticos y proporcionar sus credenciales. Para obtener más información, vea [información general de los trabajos de elásticos de la base de datos](sql-database-elastic-jobs-overview.md).

Consulte [escala fuera con la base de datos de SQL Azure](sql-database-elastic-scale-introduction.md): use herramientas de base de datos elástica para escalado, mover los datos, la consulta o crear transacciones.



## <a name="additional-resources"></a>Recursos adicionales

- [Grupo elástico de base de datos de SQL](sql-database-elastic-pool.md)
- [Crear un grupo de elásticos de la base de datos con el portal](sql-database-elastic-pool-create-csharp.md)
- [Crear un grupo de la base de datos elástico con PowerShell](sql-database-elastic-pool-create-powershell.md)
- [Crear un grupo de la base de datos elástico con C#](sql-database-elastic-pool-create-csharp.md)
- [Consideraciones de precio y rendimiento para grupos de elásticos de la base de datos](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png
[2]: ./media/sql-database-elastic-pool-manage-portal/basic.png
[3]: ./media/sql-database-elastic-pool-manage-portal/basic-2.png
[4]: ./media/sql-database-elastic-pool-manage-portal/basic-3.png
[5]: ./media/sql-database-elastic-pool-manage-portal/elastic-jobs.png
[6]: ./media/sql-database-elastic-pool-manage-portal/edit-metric.png
[7]: ./media/sql-database-elastic-pool-manage-portal/select-dbs.png
[8]: ./media/sql-database-elastic-pool-manage-portal/db-utilization.png
[9]: ./media/sql-database-elastic-pool-manage-portal/metric.png
