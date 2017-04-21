<properties
    pageTitle="Restaurar una sola tabla de copia de seguridad de base de datos de SQL Azure | Microsoft Azure"
    description="Obtenga información sobre cómo restaurar una sola tabla de copia de seguridad de base de datos de SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="daleche"/>


# <a name="how-to-restore-a-single-table-from-an-azure-sql-database-backup"></a>Cómo restaurar una sola tabla desde una copia de seguridad de base de datos de SQL Azure

Puede encontrar una situación en la que modificó por accidente algunos datos de una base de datos SQL y ahora desea recuperar la tabla afectada único. En este artículo se describe cómo restaurar una sola tabla en una base de datos desde una de las [copias de seguridad automáticas](sql-database-automated-backups.md)de base de datos de SQL.

## <a name="preparation-steps-rename-the-table-and-restore-a-copy-of-the-database"></a>Pasos de preparación: cambiar el nombre de la tabla y restaurar una copia de la base de datos
1. Identificar la tabla de la base de datos de SQL Azure que desea reemplazar con la copia restaurada. Use Microsoft SQL Management Studio para cambiar el nombre de la tabla. Por ejemplo, cambiar el nombre de la tabla como &lt;nombre de tabla&gt;_old.

    **Nota** Para evitar que se bloquee, asegúrese de que no hay ninguna actividad que se ejecuta en la tabla que va a cambiar nombre. Si tiene problemas, asegúrese de que realizar este procedimiento durante un período de mantenimiento.

2. Restaurar una copia de seguridad de la base de datos a un punto en el tiempo que se desea recuperar a con los pasos de [Punto In_Time restaurar](sql-database-recovery-using-backups.md#point-in-time-restore) .

    **Notas**:
    - El nombre de la base de datos estará en el formato de la marca de hora + DBName; Por ejemplo, **Adventureworks2012_2016-01-01T22-12Z**. Este paso no sobrescribe el nombre de la base de datos existente en el servidor. Esta es una medida de seguridad y que está diseñada para que pueda comprobar la base de datos antes de anular la base de datos actual y cambiar el nombre de la base de datos para su uso de producción.
    - Todos los niveles de rendimiento de básico a Premium se copian automáticamente el servicio con diferentes métricas de retención de copia de seguridad, según el nivel:

| Restaurar base de datos | Nivel básico | Niveles estándares | Niveles de Premium |
| :-- | :-- | :-- | :-- |
|  Restaurar el punto en el tiempo |  Cualquier punto de restauración dentro de 7 días|Cualquier punto de restauración dentro de 35 días| Cualquier punto de restauración dentro de 35 días|

## <a name="copying-the-table-from-the-restored-database-by-using-the-sql-database-migration-tool"></a>Copiar la tabla de la base de datos mediante la herramienta de migración de base de datos de SQL
1. Descargue e instale el [Asistente para la migración de base de datos de SQL](https://sqlazuremw.codeplex.com).

2. Abrir al Asistente de migración de base de datos de SQL, en la página **Seleccionar proceso** , seleccione la **base de datos en analizar o migrar**y, a continuación, haga clic en **siguiente**.
![Asistente para la migración de base de datos de SQL - Seleccionar proceso](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/1.png)
3. En el cuadro de diálogo **Conectar con el servidor** , se aplican las siguientes opciones:
 - **Nombre del servidor**: instancia de su SQL Azure
 - **Autenticación**: **autenticación de SQL Server**. Escriba sus credenciales de inicio de sesión.
 - **Base de datos**: **patrón DB (todas las bases de datos de la lista)**.
 - **Nota** De forma predeterminada, el asistente guarda la información de inicio de sesión. Si no desea que, seleccione **Olvide información de inicio de sesión**.
![Paso del asistente - Seleccionar origen - de migración de base de datos de SQL 1](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/2.png)
4. En el cuadro de diálogo **Seleccionar origen** , seleccione el nombre de la base de datos restaurada de la sección de **pasos de preparación** como el origen y, a continuación, haga clic en **siguiente**.

    ![Paso del asistente - Seleccionar origen - de migración de base de datos de SQL 2](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/3.png)

5. En el cuadro de diálogo **Seleccionar objetos** , seleccione la opción **Seleccionar objetos de base de datos** y, a continuación, seleccione la table(or tables) que desea migrar al servidor de destino.
![Asistente para la migración de base de datos de SQL - elija objetos](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/4.png)

6. En la página de **Resumen del Asistente para secuencias de comandos** , haga clic en **Sí** cuando se le pregunte por si está listo para generar una secuencia de comandos SQL. También tiene la opción de guardar la secuencia de comandos TSQL para su uso posterior.
![Asistente de migración de base de datos de SQL: resumen de Asistente de Script](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/5.png)

7. En la página de **Resumen de los resultados** , haga clic en **siguiente**.
![Asistente para la migración de base de datos de SQL - resumen de los resultados](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/6.png)

8. En la página de **Configuración de conexión de servidor de destino** , haga clic en **Conectar con el servidor**y, a continuación, escriba los detalles como sigue:
    - **Nombre del servidor**: instancia del servidor de destino
    - **Autenticación**: **autenticación de SQL Server**. Escriba sus credenciales de inicio de sesión.
    - **Base de datos**: **patrón DB (todas las bases de datos de la lista)**. Esta opción enumera todas las bases de datos en el servidor de destino.

    ![Asistente para la migración de base de datos de SQL - configuración de conexión de servidor de destino](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/7.png)

9. Haga clic en **Conectar**, seleccione la base de datos de destino que desea mover la tabla que desea y, a continuación, haga clic en **siguiente**. Esto debe finalizar ejecutando el script generado anteriormente y verá la tabla recién movida copiada a la base de datos de destino.

## <a name="verification-step"></a>Paso de verificación
1. La consulta y pruebe la tabla recién copiada para asegurarse de que los datos están intactos. Al recibir confirmación, puede quitar el formulario de la tabla con nombre cambiado sección de **pasos de preparación** . (por ejemplo, &lt;nombre de tabla&gt;_old).

## <a name="next-steps"></a>Pasos siguientes

[Copias de seguridad automáticas de base de datos SQL](sql-database-automated-backups.md)
