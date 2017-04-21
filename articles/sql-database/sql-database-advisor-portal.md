<properties 
   pageTitle="Asesor de base de datos de SQL Azure con el portal de Azure | Microsoft Azure" 
   description="Puede usar el Asistente de base de datos de SQL Azure en el portal de Azure para revisar e implementar recomendaciones para las bases de datos de SQL existente que puede mejorar el rendimiento de la consulta actual." 
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
   ms.date="09/30/2016"
   ms.author="sstein"/>

# <a name="sql-database-advisor-using-the-azure-portal"></a>Asesor de base de datos de SQL con el portal de Azure

> [AZURE.SELECTOR]
- [Información general de Asesor de base de datos SQL](sql-database-advisor.md)
- [Portal](sql-database-advisor-portal.md)

Puede usar el Asistente de base de datos de SQL Azure en el portal de Azure para revisar e implementar recomendaciones para las bases de datos de SQL existente que puede mejorar el rendimiento de la consulta actual.

## <a name="viewing-recommendations"></a>Visualización de recomendaciones

La página de recomendaciones es donde ver las recomendaciones superiores en función de su impacto potencial para mejorar el rendimiento. También puede ver el estado de las operaciones históricas. Seleccione una recomendación o estado para ver más detalles.

Para ver y aplicar recomendaciones, necesita los permisos de [control de acceso basado en roles](../active-directory/role-based-access-control-configure.md) de correcto en Azure. **Lector**, permisos de **Colaborador de DB de SQL** son necesarios para ver recomendaciones y **propietario**, se necesitan permisos de **Colaborador de DB de SQL** para ejecutar las acciones; crear o eliminar índices y cancelar la creación del índice.

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/).
2. Haga clic en **más servicios** > **bases de datos SQL**y seleccione la base de datos.
5. Haga clic en la **recomendación de rendimiento** para ver recomendaciones disponibles para la base de datos seleccionada.

> [AZURE.NOTE] Para obtener recomendaciones una base de datos debe tener aproximadamente un día de uso y debe haber alguna actividad. También debe haber alguna actividad coherente. El Asistente de la base de datos de SQL puede optimizar más fácilmente patrones de consulta coherente que sea posible para momentos de actividad aleatorios mucho manchas. Si no están disponibles las recomendaciones, la página de **recomendación de rendimiento** debe proporcionar un mensaje que explica por qué.

![Recomendaciones](./media/sql-database-advisor-portal/recommendations.png)

Aquí es un ejemplo de recomendación "Solucionar el problema de esquema" en el portal de Azure.

![Solucionar el problema de esquema](./media/sql-database-advisor-portal/sql-database-advisor-schema-issue.png)

Recomendaciones se ordenan por su potencial impacto en el rendimiento en las siguientes cuatro categorías:

| Impacto | Descripción |
| :--- | :--- |
| Alta | Recomendaciones de gran impacto deben proporcionar el impacto de rendimiento más importante. |
| Medio | Impacto medio recomendaciones deben mejorar el rendimiento, pero no considerablemente. |
| Bajo | Recomendaciones de bajo impacto deben proporcionar mejor rendimiento que sin pero mejoras no pueden ser importantes. 


### <a name="removing-recommendations-from-the-list"></a>Quitar las recomendaciones de la lista

Si su lista de recomendaciones contiene elementos que desea quitar de la lista, puede descartar la recomendación:

1. Seleccione una recomendación en la lista de **recomendaciones**.
2. En el módulo de **Detalles** , haga clic en **Descartar** .


Si lo desea, puede agregar elementos descartadas volver a la lista de **recomendaciones** :

1. Haga clic en el módulo de **recomendaciones** **vista descartado**.
1. Seleccione un elemento descartado de la lista para ver sus detalles.
1. Si lo desea, haga clic en **Deshacer descartar** para agregar el índice a la lista principal de **recomendaciones**.



## <a name="applying-recommendations"></a>Aplicando recomendaciones

Asesor de base de datos de SQL proporciona un control completo sobre cómo recomendaciones son habilitado con cualquiera de las tres opciones siguientes: 

- Aplicar recomendaciones individuales de uno en uno.
- Habilitar el Asesor de aplicar automáticamente recomendaciones (actualmente se aplica solo recomendaciones de índice).
- Para implementar una recomendación manualmente, ejecute el script de SQL T recomendado contra la base de datos.

Seleccione cualquier recomendación para ver sus detalles y, a continuación, haga clic en **Ver secuencia de comandos** para revisar los detalles de cómo se crea la recomendación exactos.

La base de datos permanece en línea mientras el Asesor aplica la recomendación--usando el Asistente de la base de datos de SQL nunca tiene una base de datos sin conexión.

### <a name="apply-an-individual-recommendation"></a>Aplicar una recomendación individual

Puede revisar y Aceptar recomendaciones de uno en uno.

1. En el módulo de **recomendaciones** , haga clic en una recomendación.
2. En el módulo de **Detalles** , haga clic en **Aplicar**.

    ![Aplicar recomendación](./media/sql-database-advisor-portal/apply.png)

### <a name="enable-automatic-index-management"></a>Habilitar la administración automática de índices

Puede establecer el Asesor de base de datos de SQL de implementar recomendaciones automáticamente. Recomendaciones estén disponibles automáticamente se aplicará. Como con todas las operaciones de índice administradas por el servicio si es negativo impacto en el rendimiento la recomendación recuperará.

1. En el módulo de **recomendaciones** , haga clic en **automatizar**:

    ![Configuración del Asesor de](./media/sql-database-advisor-portal/settings.png)

2. Establezca el Asesor automáticamente los índices **crear** o **Eliminar** :

    ![Índices recomendados](./media/sql-database-advisor-portal/automation.png)


### <a name="manually-run-the-recommended-t-sql-script"></a>Ejecutar manualmente la secuencia de comandos de SQL T recomendada

Seleccione cualquier recomendación y, a continuación, haga clic en **Ver secuencia de comandos**. Ejecute esta secuencia de comandos en la base de datos para aplicar manualmente la recomendación.

*Índices que se ejecutan manualmente no se supervisa y validados impacto en el rendimiento por el servicio* para que se sugiere que supervisar estos índices tras la creación de comprobar proporcionan mejoras de rendimiento y ajustar o eliminarlas si es necesario. Para obtener más información sobre cómo crear índices, consulte [CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).


### <a name="canceling-recommendations"></a>Cancelar recomendaciones

Se pueden cancelar las recomendaciones que se encuentran en un estado de **éxito** , **Comprobar**o **pendiente**. No se pueden cancelar las recomendaciones con un estado de **ejecución** .

1. Seleccione una recomendación en el área de **Optimización historial** para abrir el módulo de **Detalles de recomendaciones** .
2. Haga clic en **Cancelar** para cancelar el proceso de la aplicación de la recomendación.



## <a name="monitoring-operations"></a>Operaciones de seguimiento

Aplicar una recomendación podría no ejecutarse de forma inmediata. El portal ofrece detalles sobre el estado de las operaciones de recomendación. Los siguientes son posibles estados que puede ser un índice en:

| Estado | Descripción |
| :--- | :--- |
| Pendiente | Aplicar recomendación comando se recibió y se ha programado para la ejecución. |
| Ejecutar | Se aplica la recomendación. |
| Éxito | Recomendación se aplicó correctamente. |
| Error | Se produjo un error durante el proceso de la aplicación de la recomendación. Puede ser un problema temporal o posiblemente un esquema de cambiar a la tabla y la secuencia de comandos ya no es válida. |
| Revertir | La recomendación aplicada, pero ha sido considerada no influyen y se se revierte automáticamente. |
| Revertir | Se ha vuelto la recomendación. |

Haga clic en una recomendación en proceso de la lista para ver más detalles:

![Índices recomendados](./media/sql-database-advisor-portal/operations.png)


### <a name="reverting-a-recommendation"></a>Restaurar una recomendación

Si ha usado el Asesor para aplicar la recomendación (es decir, que no se efectúa manualmente la secuencia de comandos SQL T) cambiará automáticamente, si encuentra el impacto de rendimiento sea negativo. Si por algún motivo que desea revertir una recomendación puede hacer lo siguiente:


1. Seleccione una recomendación aplicó correctamente en el área **historial de ajuste** .
2. Haga clic en **Revertir** en el módulo de **Detalles de la recomendación** .

![Índices recomendados](./media/sql-database-advisor-portal/details.png)


## <a name="monitoring-performance-impact-of-index-recommendations"></a>Supervisar el rendimiento de recomendaciones de índice

Después de recomendaciones se ha implementado correctamente (en la actualidad, las operaciones de índice y agregar parámetros sólo recomendaciones de consultas) puede hacer clic en **Información de la consulta** en el módulo de detalles de la recomendación para abrir [Perspectivas de rendimiento de consulta](sql-database-query-performance.md) y ver el impacto de rendimiento de las consultas superiores.

![Supervisar el rendimiento](./media/sql-database-advisor-portal/query-insights.png)



## <a name="summary"></a>Resumen

Asesor de base de datos de SQL proporciona recomendaciones para mejorar el rendimiento de la base de datos SQL. Proporcionando secuencias de comandos SQL T, así como individual y totalmente automático (actualmente sólo índice), el Asesor proporciona asistencia útiles para optimizar la base de datos y, finalmente, mejora el rendimiento de consulta.



## <a name="next-steps"></a>Pasos siguientes

Supervisar las recomendaciones y continuar aplicando para ajustar el rendimiento. Las cargas de trabajo de la base de datos son dinámicas y cambiar continuamente. Seguirá Asesor de base de datos de SQL supervisar y proporcionar recomendaciones que posiblemente se pueden mejorar el rendimiento de la base de datos. 

 - Para obtener información general del Asesor de base de datos de SQL, vea [Asesor de base de datos de SQL](sql-database-advisor.md) .
 - Ver [Información de rendimiento de consulta](sql-database-query-performance.md) para obtener información acerca de cómo ver el impacto en el rendimiento de las consultas superiores.

## <a name="additional-resources"></a>Recursos adicionales

- [Almacén de consulta](https://msdn.microsoft.com/library/dn817826.aspx)
- [CREAR ÍNDICE](https://msdn.microsoft.com/library/ms188783.aspx)
- [Control de acceso basado en roles](../active-directory/role-based-access-control-configure.md)






