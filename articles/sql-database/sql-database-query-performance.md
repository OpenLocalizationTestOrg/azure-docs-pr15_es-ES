<properties 
   pageTitle="Información de Performance de consulta de base de datos SQL Azure" 
   description="Supervisión de rendimiento de consulta identifica la mayoría consumo de CPU de las consultas para una base de datos de SQL Azure." 
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
   ms.date="08/09/2016"
   ms.author="sstein"/>

# <a name="azure-sql-database-query-performance-insight"></a>Información de Performance de consulta de base de datos SQL Azure


Administrar y ajustar el rendimiento de bases de datos relacionales son una tarea difícil que requiere experiencia significativa e inversión de tiempo. Información de rendimiento de consulta permite invertir menos tiempo para solucionar problemas de rendimiento de la base de datos al proporcionar lo siguiente:

- Conocimiento más profundo en su consumo de recursos (DTU) de las bases de datos. 
- Las principales consultas por recuento de duración de la CPU y ejecución que pueda ajustarse para mejorar el rendimiento.
- La capacidad para profundizar en los detalles de una consulta, ver su texto y el historial de uso de recursos. 
- Las anotaciones que mostrar acciones realizadas por [Asesor de base de datos de SQL Azure](sql-database-advisor.md) el ajuste del rendimiento  



## <a name="prerequisites"></a>Requisitos previos

- Información de rendimiento de consulta solo está disponible con V12 de base de datos de SQL Azure.
- Información de rendimiento de consulta requiere que el [Almacén de consulta](https://msdn.microsoft.com/library/dn817826.aspx) está activo en la base de datos. Si no se está ejecutando el almacén de consulta, el portal le pide que Active.

 
## <a name="permissions"></a>Permisos

Los permisos de [control de acceso basado en roles](../active-directory/role-based-access-control-configure.md) siguientes se requieren para usar una perspectiva de rendimiento de consulta: 

- Se requieren permisos de **lector**, **propietario**, **Colaborador**, **SQL DB colaborador**o **Colaborador de SQL Server** para ver las consultas de consume recursos superior y los gráficos. 
- Se requieren permisos de **propietario**, **Colaborador**, **SQL DB colaborador**o **Colaborador de SQL Server** para ver el texto de la consulta.



## <a name="using-query-performance-insight"></a>Usar una perspectiva de rendimiento de consulta

Es fácil de usar una perspectiva de rendimiento de consulta:

- Abra [el portal de Azure](https://portal.azure.com/) y busque la base de datos que desea examinar. 
  - Desde el menú de la izquierda, bajo soporte técnico y solución de problemas, seleccione "Información de rendimiento de consulta".
- En la primera pestaña, revise la lista de principales consultas que consume recursos.
- Seleccione una consulta individual para ver sus detalles.
- Abra [Asesor de base de datos de SQL Azure](sql-database-advisor.md) y comprobar si las recomendaciones están disponibles.
- Utilizar los controles deslizantes o zoom iconos para cambiar el intervalo de observados.

    ![panel de rendimiento](./media/sql-database-query-performance/performance.png)

> [AZURE.NOTE] Un par de horas de datos necesita capturar tienda de consulta de base de datos de SQL proporcionar información de rendimiento de consulta. Si la base de datos no tiene ninguna actividad o almacén de consulta no estaba activa durante un período de tiempo determinado, los gráficos estará vacíos al mostrar ese período de tiempo. Puede habilitar almacenamiento de consulta en cualquier momento si no se está ejecutando.   



## <a name="review-top-cpu-consuming-queries"></a>Revise la CPU superior consumir consultas

En el [portal](http://portal.azure.com) , haga lo siguiente:

1. Vaya a una base de datos SQL y haga clic en **configuración de todos los** > **soporte + solución de problemas** > **una perspectiva de rendimiento de consulta**. 

    ![Información de rendimiento de consulta][1]

    Se abre la vista principales consultas y se muestran las principales consultas consume CPU.

1. Haga clic en alrededor del gráfico para obtener más detalles.<br>La línea superior muestra general % DTU para la base de datos, mientras que las barras muestran el porcentaje de CPU consumido por las consultas seleccionadas durante el intervalo seleccionado (por ejemplo, si se selecciona la **semana pasada** cada barra representa un día).

    ![principales consultas][2]

    La cuadrícula inferior representa información agregada para las consultas visibles.

  - Id. de consulta: identificador único de consulta de base de datos.
  - CPU por consulta durante el intervalo visible (depende de la función de agregación).
  - Duración por consulta (depende de la función de agregación).
  - Número total de ejecuciones para una consulta determinada.

    Active o desactive las consultas individuales para incluir o excluir a ellos desde el gráfico con casillas de verificación.

1. Si los datos se convierte en obsoletos, haga clic en el botón **Actualizar** .
1. Puede utilizar los controles deslizantes y botones para cambiar el intervalo de observación e investigar picos de zoom:  ![configuración](./media/sql-database-query-performance/zoom.png)
1. Opcionalmente, si desea una vista distinta, puede seleccionar pestaña **personalizada** y establecer:
  
  - Métrica (CPU, duración, recuento de la ejecución)
  - Intervalo de tiempo (últimas 24 horas, más allá de la semana, mes pasado). 
  - Número de consultas.
  - Función de agregación.

    ![Configuración](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>Ver los detalles de la consulta individual

Para ver los detalles de la consulta:

1. Haga clic en cualquier consulta en la lista de principales consultas.

    ![detalles](./media/sql-database-query-performance/details.png)

1. Se abre la vista de detalles y el recuento de duración de consumo y ejecución de consultas CPU se divide el tiempo.
1. Haga clic en alrededor del gráfico para obtener más detalles.
  - Gráfico superior muestra línea con general % DTU de base de datos y las barras son % de CPU consumido por la consulta seleccionada.
  - Segundo gráfico muestra la duración total por la consulta seleccionada.
  - Gráfico de la parte inferior muestra el número total de la consulta seleccionada.
    
    ![detalles de la consulta][3]

1. Si lo desea, use los controles deslizantes, botones de zoom o haga clic en **configuración** para personalizar cómo se muestran los datos de la consulta, o elegir un período de tiempo diferente.

## <a name="review-top-queries-per-duration"></a>Principales consultas por duración de revisión

En la última actualización del conocimiento de rendimiento de consulta, presentamos dos nuevas métricas que pueden ayudarle a identificar posible botella: recuento de duración y ejecución.<br>

Las consultas de larga ejecución tienen mayor potencial para bloquear más larga de recursos, bloquear a otros usuarios y limitar escalabilidad. También son las mejores candidatas para la optimización.<br>

Para identificar el tiempo de ejecución de consultas:

1. Abrir la pestaña **personalizada** en una perspectiva de rendimiento de consulta para base de datos seleccionada
1. Cambiar métricas ser **duración**
1. Seleccione el número de consultas y el intervalo de observación
1. Seleccione la función de agregación
  - **Suma** agrega el tiempo de ejecución de consulta todos los durante el intervalo de observación todo.
  - **Max** busca consultas que se ejecutó máximo en intervalo de observación todo.
  - **Avg** busca el tiempo de ejecución promedio de todas las ejecuciones de consulta y mostrar la parte superior de estos promedios. 

    ![duración de consulta][4]

## <a name="review-top-queries-per-execution-count"></a>Principales consultas por el recuento de la ejecución de revisión

Número mayor de ejecuciones no puede afectar propia base de datos y el uso de recursos puede ser bajo, pero obtendría general de la aplicación lenta.

En algunos casos, el recuento de ejecución muy alta puede producir para aumentar de ida y vuelta de red. Ida y vuelta significativamente afectar al rendimiento. Están sujetos a la latencia de red y a la latencia de servidor indirecto. 

Por ejemplo, muchos sitios Web controlados por datos intensamente obtener acceso a la base de datos para cada solicitud del usuario. Mientras conexión agrupación de ayuda, el tráfico de red y procesamiento de carga en el servidor de base de datos puede afectar al rendimiento.  Consejos generales es mantener ida y vuelta al mínimo absoluto.

Para identificar con frecuencia ejecuta consultas consultas ("conversadoras"):

1. Abrir la pestaña **personalizada** en una perspectiva de rendimiento de consulta para base de datos seleccionada
1. Cambiar métricas a ser **recuento de ejecución**
1. Seleccione el número de consultas y el intervalo de observación

    ![recuento de la ejecución de consulta][5]

## <a name="understanding-performance-tuning-annotations"></a>Descripción de las anotaciones de ajuste de rendimiento 

Al explorar la carga de trabajo en una perspectiva de rendimiento de consulta, es posible que note iconos con una línea vertical en la parte superior del gráfico.<br>

Estos iconos son anotaciones; representan el rendimiento que afectan a acciones realizadas por [Asesor de base de datos de SQL Azure](sql-database-advisor.md). Por suspensión anotación, obtendrá información básica sobre la acción:

![anotación de consulta][6]

Si desea más información o aplicar recomendación advisor, haga clic en el icono. Se abrirá detalles de la acción. Si se trata de una recomendación active que aplicarlo inmediatamente con el comando.

![detalles de anotación de consulta][7]

### <a name="multiple-annotations"></a>Varias anotaciones. ###

Es posible que debido a nivel de zoom, las anotaciones que cercanos se obtener contraídas en uno. Esto se representarán con un icono especial, haga clic en él se abrir placa nueva en la lista de agrupados anotaciones se muestran.
Correlación acciones de optimización del rendimiento de las consultas puede ayudarle a entender mejor la carga de trabajo. 


##  <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>Optimizar la configuración del almacén de consulta para una perspectiva de rendimiento de consulta

Durante el uso de una perspectiva de rendimiento de consulta, que pueden encontrar los siguientes mensajes de la tienda de consulta:

- "Almacén de consulta no está correctamente configurado en esta base de datos. Haga clic aquí para obtener más información".
- "Almacén de consulta no está correctamente configurado en esta base de datos. Haga clic aquí para cambiar la configuración". 

Estos mensajes aparecen normalmente cuando el almacén de consulta no puede recopilar datos nuevos. 

Primer caso ocurre al almacén de consulta se encuentra en estado de solo lectura y parámetros se establecen de manera óptima. Para corregir este error aumentar tamaño de almacenamiento de la consulta o desactivando la tienda de consulta.

![botón qds][8]

Segundo caso sucede cuando está desactivado de la tienda de consulta o parámetros no se establecen de manera óptima. <br>Puede cambiar la directiva de retención y la captura y habilitar almacenamiento consulta ejecutando los siguientes comandos o directamente desde el portal:

![botón qds][9]

### <a name="recommended-retention-and-capture-policy"></a>Directiva de retención y captura recomendada

Existen dos tipos de directivas de retención:

- Tamaño según – si establecida en automática limpie datos automáticamente cuando se alcance cerca de tamaño máximo.
- Según el tiempo - establecerá a 30 días, de manera predeterminada lo que significa que si consulta tienda se queda sin espacio, eliminará consultar la información de más de 30 días

Capturar directiva se puede establecer en:

- **Todo** : captura todas las consultas.
- **Automáticamente** consultas poco frecuentes y consultas con duración de compilación y ejecución no significativo se pasan por alto. Umbrales de duración de contar, compilación y en tiempo de ejecución de ejecución determinan internamente. Esta es la opción predeterminada.
- **Ninguno** : almacén de consulta detiene la captura las consultas nuevas, pero aún recopiladas estadísticas de tiempo de ejecución de consultas ya capturadas.
    
Se recomienda establecer todas las directivas en automática y la directiva de limpiar 30 días:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);
        
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));
    
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Aumentar tamaño de almacenamiento de la consulta. Esto podría realizarse al conectarse a una base de datos y emitir después de la consulta:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Aplicar esta configuración hará finalmente almacén de consulta de recopilación de las consultas nuevas, sin embargo, si no desea esperar puede borrar almacén de consulta. 
> [AZURE.NOTE] Ejecutar la consulta siguiente eliminará toda la información actual en el almacén de consulta. 


    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>Resumen

Información de rendimiento de consulta le ayuda a comprender el impacto de la carga de trabajo de la consulta y cómo se relaciona con consumo de recursos de base de datos. Con esta característica se obtenga más información sobre la parte superior consumir consultas e identificar fácilmente las corregir antes de que se conviertan en un problema.




## <a name="next-steps"></a>Pasos siguientes

Para obtener recomendaciones adicionales acerca de cómo mejorar el rendimiento de la base de datos SQL, haga clic en [recomendaciones](sql-database-advisor.md) en el módulo de **Una perspectiva de rendimiento de consulta** .

![Asesor de rendimiento](./media/sql-database-query-performance/ia.png)


<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png

