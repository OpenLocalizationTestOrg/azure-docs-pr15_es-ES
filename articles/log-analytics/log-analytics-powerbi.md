<properties
   pageTitle="Exportar datos de análisis de registro a Power BI | Microsoft Azure"
   description="Power BI es un servicio de análisis de negocio basada en la nube de Microsoft que proporciona eficaces visualizaciones e informes de análisis de diferentes conjuntos de datos.  Análisis de registro puede exportar continuamente datos del repositorio de OMS en Power BI para puede aprovechar sus visualizaciones y herramientas de análisis.  En este artículo se describe cómo configurar las consultas de análisis de registro que exportar automáticamente a Power BI a intervalos regulares."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="export-log-analytics-data-to-power-bi"></a>Exportar datos de análisis de registro a Power BI

[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) es un servicio de análisis de negocio basada en la nube de Microsoft que proporciona eficaces visualizaciones e informes de análisis de diferentes conjuntos de datos.  Análisis de registro puede exportar automáticamente datos del repositorio de OMS en Power BI para puede aprovechar sus visualizaciones y herramientas de análisis.

Al configurar Power BI con análisis de registro, crear consultas de registro que exportación sus resultados a conjuntos de datos correspondiente en Power BI.  La consulta y la exportación continuará ejecutar automáticamente en una programación que defina para mantener el conjunto de datos actualizados con los últimos datos recopilados por el análisis de registro.

![Análisis de registro para Power BI](media/log-analytics-powerbi/overview.png)

## <a name="power-bi-schedules"></a>Programaciones de Power BI

Una *Programación de Power BI* incluye una búsqueda de registro que se exporta un conjunto de datos del repositorio de OMS a un conjunto de datos correspondiente en Power BI y una programación que define la frecuencia de ejecución de esta búsqueda para mantener el conjunto de datos actual.

Los campos en el conjunto de datos se coinciden con las propiedades de los registros devueltos por la búsqueda de registros.  Si la búsqueda devuelve los registros de los diferentes tipos el conjunto de datos incluirá todas las propiedades de cada uno de los tipos de registro incluidos.  

> [AZURE.NOTE] Es recomendable usar una consulta de búsqueda de registro que devuelve los datos sin formato en lugar de llevar a cabo cualquier consolidación con los comandos como [medida](log-analytics-search-reference.md#measure).  Puede realizar ninguna agregación y cálculos en Power BI de los datos sin formato.

## <a name="connecting-oms-workspace-to-power-bi"></a>Área de trabajo OMS a conectarse a Power BI

Antes de que puede exportar de análisis de registro para Power BI, debe conectar el área de trabajo OMS a su cuenta de Power BI mediante el procedimiento siguiente.  

1. Haga clic en el icono de **configuración de** la consola OMS.
2. Seleccione **cuentas**.
3. En la sección **Información del área de trabajo** , haga clic en **conectarse a la cuenta de Power BI**.
4. Escriba las credenciales de su cuenta de Power BI.

## <a name="create-a-power-bi-schedule"></a>Crear una programación de Power BI

Crear una programación de Power BI para cada conjunto de datos mediante el procedimiento siguiente.

1. En la consola OMS haga clic en el icono de **Búsqueda de registros** .
2. Escriba una nueva consulta o seleccione una búsqueda guardada que devuelve los datos que desea exportar a **Power BI**.  
3. Haga clic en el botón **Power BI** en la parte superior de la página para abrir el cuadro de diálogo de **Power BI** .
4. Proporcione la información de la tabla siguiente y haga clic en **Guardar**.

| (Propiedad) | Descripción |
|:--|:--|
| Nombre | Nombre para identificar la programación cuando se ve la lista de programaciones de Power BI. |
| Búsqueda guardada | Para ejecutar la búsqueda de registros.  Puede seleccionar la consulta actual o seleccione una búsqueda guardada existente en el cuadro de lista desplegable. |
| Programación | La frecuencia de ejecutar la búsqueda guardada y exportar en el conjunto de datos de Power BI.  El valor debe estar entre 15 minutos y 24 horas. |
| Nombre del conjunto de datos | El nombre del conjunto de datos en Power BI.  Se crea si no existe y se actualiza si existe. |

## <a name="viewing-and-removing-power-bi-schedules"></a>Ver y quitar las programaciones de Power BI

Ver la lista de programaciones de BI de Power existentes con el siguiente procedimiento.

1. Haga clic en el icono de **configuración de** la consola OMS.
2. Seleccione **Power BI**.

Además de los detalles de la programación, se muestran el número de veces que se ha ejecutado la programación de la semana pasada y el estado de la última sincronización.  Si la sincronización produce errores, puede hacer clic en el vínculo para ejecutar una búsqueda de registro de registros con los detalles del error.

Puede quitar una programación haciendo clic en la **X** en la **columna de quitar**.  Puede deshabilitar una programación seleccionando **desactivar**.  Para modificar una programación debe quitarlo y volver a crearla con la nueva configuración.

![Programaciones de Power BI](media/log-analytics-powerbi/schedules.png)

## <a name="sample-walkthrough"></a>Tutorial de ejemplo
La siguiente sección le guía a través de un ejemplo de creación de una programación de Power BI y el uso de su conjunto de datos para crear un informe sencillo.  En este ejemplo, todos los datos de rendimiento de un conjunto de equipos se exporta a Power BI y, a continuación, se crea un gráfico de líneas para mostrar la utilización del procesador.

### <a name="create-log-search"></a>Crear la búsqueda de registros
Empezaremos por crear una búsqueda de registro para los datos que se va a enviar al conjunto de datos.  En este ejemplo, usaremos una consulta que devuelve todos los datos de rendimiento para equipos con un nombre que comienza con *srv*.  

![Programaciones de Power BI](media/log-analytics-powerbi/walkthrough-query.png)

### <a name="create-power-bi-search"></a>Crear búsqueda de Power BI
Hacer clic en el botón de **Power BI** para abrir el cuadro de diálogo de Power BI y proporcione la información requerida.  Queremos esta búsqueda para ejecutar una vez cada hora y crear un conjunto de datos denominado *Contoso rendimiento*.  Dado que ya tenemos la búsqueda abierto que crea los datos queremos, mantendremos el valor predeterminado de *la consulta de búsqueda actual de uso* de la **Búsqueda guardada**.

![Búsqueda de Power BI](media/log-analytics-powerbi/walkthrough-schedule.png)

### <a name="verify-power-bi-search"></a>Comprobar la búsqueda de Power BI
Para comprobar que se creó la programación correctamente, nos ver la lista de búsquedas de Power BI en el icono **configuración** en el panel OMS.  Se espere unos minutos y actualiza esta vista hasta que informa de que se ha ejecutado la sincronización.

![Búsqueda de Power BI](media/log-analytics-powerbi/walkthrough-schedules.png)

### <a name="verify-the-dataset-in-power-bi"></a>Compruebe el conjunto de datos en Power BI
Se inicie sesión en la cuenta en [powerbi.microsoft.com](http://powerbi.microsoft.com/) y desplácese hasta **conjuntos de datos** en la parte inferior del panel izquierdo.  Podemos ver que aparece el conjunto de datos de *Rendimiento de Contoso* que indica que se ha ejecutado correctamente nuestra exportar.

![Conjunto de datos de Power BI](media/log-analytics-powerbi/walkthrough-datasets.png)

### <a name="create-report-based-on-dataset"></a>Crear informe basado en el conjunto de datos
Seleccione el conjunto de datos de **Rendimiento de Contoso** y, a continuación, haga clic en **resultados** en el panel **campos** a la derecha para ver los campos que forman parte de este conjunto de datos.  Para crear un gráfico de líneas que muestra la utilización del procesador para cada equipo, realizar las siguientes acciones.

1. Seleccione la visualización del gráfico de líneas.
2. Arrastre **ObjectName** al **nivel filtro de informe** y compruebe **procesador**.
3. Arrastre **CounterName** al **nivel filtro de informe** y comprobar **% tiempo de procesador**.
4. Arrastre **CounterValue** **valores**.
5. Arrastre el **equipo** a la **leyenda**.
6. Arrastre **TimeGenerated** **eje**.

Podemos ver que se muestra el gráfico de líneas resultante con los datos de nuestro conjunto de datos.

![Gráfico de líneas de Power BI](media/log-analytics-powerbi/walkthrough-linegraph.png)

### <a name="save-the-report"></a>Guardar el informe
Se guarda el informe haciendo clic en el botón Guardar en la parte superior de la pantalla y validar que aparece ahora en la sección de informes en el panel izquierdo.

![Informes de Power BI](media/log-analytics-powerbi/walkthrough-report.png)

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de las [búsquedas de registro](log-analytics-log-searches.md) crear consultas que se pueden exportar a Power BI.
- Obtenga más información acerca de [Power BI](http://powerbi.microsoft.com) para crear visualizaciones en función de las exportaciones de análisis de registro.
