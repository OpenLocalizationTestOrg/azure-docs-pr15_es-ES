<properties 
    pageTitle="Cómo supervisar un servicio de nube | Microsoft Azure" 
    description="Aprenda a supervisar los servicios de nube mediante el portal de clásico de Azure." 
    services="cloud-services" 
    documentationCenter="" 
    authors="rboucher" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/04/2015" 
    ms.author="robb"/>


# <a name="how-to-monitor-cloud-services"></a>Cómo supervisar los servicios de nube

[AZURE.INCLUDE [disclaimer](../../includes/disclaimer.md)]

Puede supervisar `key` métricas del rendimiento para los servicios de nube en el portal de clásico de Azure. Puede establecer el nivel de supervisión mínima y detallados de cada rol de servicio y puede personalizar la supervisión muestra. Datos de supervisión detallados se almacenan en una cuenta de almacenamiento, que puede tener acceso fuera del portal. 

Muestra de supervisión en el portal de clásico Azure son altamente configurable. Puede elegir las medidas que desee supervisar en la lista de métricas en la página **Monitor** y puede elegir las métricas que desea trazar en gráficos de métricas en la página de **Monitor** y el panel. 

## <a name="concepts"></a>Conceptos

De forma predeterminada, supervisión mínima sirve para un nuevo servicio de nube con contadores de rendimiento recopilado desde el sistema operativo host para las instancias de roles (máquinas virtuales). Las medidas mínimas se limitan a porcentaje de CPU, de datos, los datos, rendimiento de lectura de disco y el rendimiento de escritura de disco. Mediante la configuración de supervisión detallado, puede recibir métricas adicionales basados en datos de rendimiento en las máquinas virtuales (instancias de rol). Las mediciones detalladas habilitar un análisis detallado de los problemas que se producen durante las operaciones de la aplicación.

De forma predeterminada se muestra datos de contador de rendimiento de las instancias de función y se transfieren desde la instancia de la función en intervalos de 3 minutos. Cuando se habilita la supervisión detallado, los datos de contador de rendimiento sin formato se agregan para cada instancia de la función y en varias instancias de la función de cada rol a intervalos de 5 minutos, 1 hora y 12 horas. Los datos agregados se purgan después de 10 días.

Después de habilitar la supervisión detallado, los datos de supervisión agregados se almacenan en las tablas de su cuenta de almacenamiento. Para habilitar la supervisión detallado para una función, debe configurar una cadena de conexión de diagnósticos que se vincula a la cuenta de almacenamiento. Puede usar cuentas de almacenamiento diferentes para diferentes funciones.

Tenga en cuenta que habilita la supervisión detallado aumentará los costos de almacenamiento relacionados con el almacenamiento de datos, la transferencia de datos y las transacciones de almacenamiento. Supervisión mínima no requiere una cuenta de almacenamiento. Los datos de las mediciones que se exponen en el nivel de supervisión mínima no se almacenan en su cuenta de almacenamiento, incluso si establece el nivel de supervisión en detallado.


## <a name="how-to-configure-monitoring-for-cloud-services"></a>Cómo: configurar la supervisión de servicios en la nube

Utilice los siguientes procedimientos para configurar detallado o mínimas supervisión en el portal de clásico de Azure. 

### <a name="before-you-begin"></a>Antes de empezar

- Crear una cuenta de almacenamiento para almacenar los datos de supervisión. Puede usar cuentas de almacenamiento diferentes para diferentes funciones. Para obtener más información, consulte Ayuda para **Cuentas de almacenamiento**o consulte [cómo crear una cuenta de almacenamiento](/manage/services/storage/how-to-create-a-storage-account/).

- Habilitar diagnósticos de Azure para las funciones de servicio de nube. Consulte [configuración de diagnósticos de los servicios en la nube](https://msdn.microsoft.com/library/azure/dn186185.aspx#BK_EnableBefore).

Asegúrese de que la cadena de conexión de diagnósticos se encuentra en la configuración de funciones. No se puede activar la supervisión detallado hasta habilitar diagnósticos de Azure e incluir una cadena de conexión de diagnóstico en la configuración de la función.   

> [AZURE.NOTE] Proyectos destinados a Azure SDK 2.5 no incluyen automáticamente la cadena de conexión de diagnóstico en la plantilla de proyecto. Para estos proyectos debe agregar manualmente la cadena de conexión de diagnósticos a la configuración de funciones.

**Agregar manualmente la cadena de conexión de diagnósticos a configuración de funciones**

1. Abra el proyecto de servicio de nube en Visual Studio
2. Haga doble clic en el **rol** para abrir el Diseñador de la función y seleccione la pestaña **configuración**
3. Busque una opción denominada **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**. 
4. Si esta opción no está presente, a continuación, haga clic en el botón de **Opción de agregar** para agregar a la configuración y cambiar el tipo de la nueva configuración a la **cadena de conexión**
5. Establecer el valor de cadena de conexión la haciendo clic en el botón **...** . Se abrirá un cuadro de diálogo que le permite seleccionar una cuenta de almacenamiento.

    ![Configuración de Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioDiagnosticsConnectionString.png)

### <a name="to-change-the-monitoring-level-to-verbose-or-minimal"></a>Para cambiar el nivel de supervisión a detallado o mínimas

1. En el [portal de clásica Azure](https://manage.windowsazure.com/), abra la página **Configurar** para la implementación de servicio de nube.

2. En el **nivel**, haga clic en **detallado** o **mínimas**. 

3. Haga clic en **Guardar**.

Después de activar la supervisión detallado, debe empezar a ver los datos de supervisión en el portal de clásico Azure dentro de la hora.

Los datos de contador de rendimiento y datos de supervisión agregados se almacenan en la cuenta de almacenamiento de tablas calificados por el identificador de implementación para las funciones. 

## <a name="how-to-receive-alerts-for-cloud-service-metrics"></a>Cómo: recibir alertas de métricas de servicio de nube

Puede recibir alertas en función de su servicio de nube supervisión métricas. En la página de **Servicios de administración** del portal clásica Azure, puede crear una regla para activar una alerta cuando llegue a la métrica que elige un valor que especifique. También puede tener un correo electrónico que se envía cuando se activa la alerta. Para obtener más información, vea [Cómo: recibir notificaciones de alerta y administrar reglas de alertas en Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

## <a name="how-to-add-metrics-to-the-metrics-table"></a>Cómo: agregar medidas a la tabla de métricas

1. En el [portal de clásica Azure](http://manage.windowsazure.com/), abra la página de **Monitor** para el servicio de nube.

    De forma predeterminada, la tabla métricas muestra un subconjunto de las mediciones disponibles. La ilustración muestra la métrica detallado predeterminado para un servicio de nube se limita al contador de rendimiento MB disponibles, con datos agregados en el nivel de función. Use **Agregar métricas** para seleccionar métricas agregadas y nivel de rol adicionales para supervisar en el portal de clásico de Azure.

    ![Presentación detallada](./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png)
 
2. Para agregar medidas a la tabla métricas:

    1. Haga clic en **Agregar métricas** para abrir **Elija métricas**, se muestra a continuación.

        La primera métrica disponible se expande para mostrar las opciones disponibles. Para cada métrica, la opción superior muestra los datos de supervisión agregados para todos los roles. Además, puede elegir funciones individuales para mostrar datos.

        ![Agregar métricas](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png)

    2. Para seleccionar la métrica para mostrar

        - Haga clic en la flecha hacia abajo por la métrica para expandir las opciones de supervisión.
        - Seleccione la casilla de verificación para cada opción de supervisión que desea mostrar.

        Puede mostrar hasta 50 métricas en la tabla métricas.

        > [AZURE.TIP] En la supervisión detallado, la lista de métricas puede contener docenas de métricas. Para mostrar una barra de desplazamiento, mantenga el mouse sobre el lado derecho del cuadro de diálogo. Para filtrar la lista, haga clic en el icono de búsqueda y escriba texto en el cuadro de búsqueda, como se muestra a continuación.
    
        ![Agregar búsqueda de métricas](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png)


3. Cuando termine de seleccionar las mediciones, haga clic en Aceptar (marca).

    Las mediciones seleccionadas se agregan a la tabla métricas, tal como se muestra a continuación.

    ![métricas del Monitor](./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png)

 
4. Para eliminar una métrica de la tabla de medidas, haga clic en la métrica para seleccionarlo y, a continuación, haga clic en **Eliminar métrica**. (Solo verá **Eliminar métrica** cuando tenga una métrica seleccionada.)

### <a name="to-add-custom-metrics-to-the-metrics-table"></a>Para agregar métricas personalizados a la tabla de métricas

El **detallado** en el nivel de supervisión proporciona una lista de métricas predeterminado que puede supervisar en el portal. Además de estos puede supervisar cualquier métricas personalizados o contadores definidos por la aplicación a través del portal.

Pasos siguientes se asume que ha activado **detallado** nivel de supervisión y configuración de la aplicación para recopilar y transferir contadores personalizados. 

Para mostrar los contadores personalizados en el portal debe actualizar la configuración del contenedor de control de torunda:
 
1. Abra el blob torunda contenedor de control en la cuenta de almacenamiento de información de diagnóstico. Puede usar Visual Studio o cualquier otro explorador de almacenamiento para realizar esta acción.

    ![Explorador de servidores de Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioBlobExplorer.png)

2. Vaya a la ruta de acceso de blobs con el patrón de **Nombre de rol/DeploymentId/RoleInstance** para buscar la configuración de la instancia de rol. 

    ![Explorador de almacenamiento de Visual Studio](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioStorage.png)
3. Descargar el archivo de configuración de la instancia de rol y lo actualiza para incluir cualquier contador de rendimiento personalizado. Por ejemplo, para supervisar *la escritura en disco Bytes/seg* para la *unidad C* , agregue lo siguiente bajo el nodo **PerformanceCounters\Subscriptions**

    ```xml
    <PerformanceCounterConfiguration>
    <CounterSpecifier>\LogicalDisk(C:)\Disk Write Bytes/sec</CounterSpecifier>
    <SampleRateInSeconds>180</SampleRateInSeconds>
    </PerformanceCounterConfiguration>
    ```
4. Guardar los cambios y cargue el archivo de configuración a la misma ubicación sobrescribir el archivo existente en el blob.
5. Cambie al modo detallado en la configuración del portal clásica Azure. Si se encuentra en modo detallado ya debe alternar mínimas y para mostrar información detallada.
6. Contador de rendimiento personalizado ahora estará disponible en el cuadro de diálogo **Agregar métricas** . 

## <a name="how-to-customize-the-metrics-chart"></a>Cómo: personalizar el gráfico de métrica

1. En la tabla métricas, seleccione métrico hasta 6 a trazar en el gráfico de métricas. Para seleccionar una métrica, haga clic en la casilla de verificación a su izquierda. Para quitar una métrica del gráfico métricas, desactive la casilla correspondiente en la tabla de métricas.

    Métricas que seleccione en la tabla métricas, las mediciones se agregan al gráfico métricas. En una pantalla estrecha, una lista desplegable de **n más** contiene encabezados de métrica que no se ajustan la presentación.

 
2. Para cambiar entre mostrar valores relativos (valor final solo para cada métrica) y los valores absolutos (se muestra un eje de Y), seleccione relativa o absoluta en la parte superior del gráfico.

    ![Relativa o absoluta](./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png)

3. Para cambiar el intervalo de tiempo métricas gráfico muestra, seleccione 1 hora, 24 horas o 7 días en la parte superior del gráfico.

    ![Período de presentación del Monitor](./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png)

    En el gráfico de métricas paneles, el método para trazar métricas es diferente. Un conjunto estándar de métrica está disponible y se agregan o se quitan seleccionando el encabezado métrico métricas.

### <a name="to-customize-the-metrics-chart-on-the-dashboard"></a>Para personalizar el gráfico de métricas en el panel

1. Abra el panel para el servicio de nube.

2. Agregar o quitar métricas del gráfico:

    - Para trazar una nueva métrica, active la casilla de verificación para la métrica en los encabezados de gráfico. En una pantalla estrecha, haga clic en la flecha hacia abajo por ** *n*??metrics** trazar una métrica que no puede mostrar el área de encabezado de gráfico.

    - Para eliminar una métrica que se traza en el gráfico, desactive la casilla de verificación por su encabezado.

3. Cambiar entre **relativas** y **absolutas** muestra.

4. Seleccione 1 hora, 24 horas o 7 días de datos para mostrar.

## <a name="how-to-access-verbose-monitoring-data-outside-the-azure-classic-portal"></a>Cómo: acceso detallado supervisión fuera del portal clásico de Azure

Datos de supervisión detallados se almacenan en tablas en las cuentas de almacenamiento que especifique para cada rol. Para cada implementación de servicio de nube, se crean seis tablas para la función. Dos tablas se crean para cada (5 minutos, 1 hora y 12 horas). Una de estas tablas almacena las agregaciones de nivel de la función; la otra tabla almacena las agregaciones para instancias de la función. 

Los nombres de tabla tienen el siguiente formato:

```
WAD*deploymentID*PT*aggregation_interval*[R|RI]Table
```

donde:

- *deploymentID* es el GUID asignado a la implementación de servicio de nube

- *aggregation_interval* = 5 M, 1 H o 12 H

- nivel de la función agregaciones = R

- agregaciones para instancias de la función = RI

Por ejemplo, las tablas siguientes podrían almacenar datos de supervisión detallados agregados a intervalos de 1 hora:

```
WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (hourly aggregations for the role)

WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (hourly aggregations for role instances)
```
