<properties
    pageTitle="Iniciar el Diseñador de vistas de análisis | Microsoft Azure"
    description="Diseñador de vista de análisis de registro le permite crear vistas personalizadas en la consola OMS que contienen diferentes visualizaciones de datos en el repositorio OMS. En este artículo se proporciona una referencia de la configuración para cada uno de los elementos de visualización disponibles para su uso en sus vistas personalizadas."
    services="log-analytics"
    documentationCenter=""
    authors="bwren"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="bwren"/>

# <a name="log-analytics-view-designer-visualization-part-reference"></a>Referencia de registros del Diseñador de vistas de análisis visualización elemento
El Diseñador de vista de análisis de registro le permite crear vistas personalizadas en la consola OMS que contienen diferentes visualizaciones de datos del repositorio de OMS. En este artículo se proporciona una referencia de la configuración para cada uno de los elementos de visualización disponibles para su uso en sus vistas personalizadas.

Otros artículos disponibles para el Diseñador de vistas son:

- [Vista Diseñador](log-analytics-view-designer.md) : información general sobre el Diseñador de vistas y procedimientos para crear y editar vistas personalizadas.
- [Referencia de mosaico](log-analytics-view-designer-tiles.md) : referencia de la configuración para cada uno de los mosaicos disponibles para su uso en sus vistas personalizadas. 

La siguiente tabla describe los diferentes tipos de ventanas disponibles en el Diseñador de vistas.  Las secciones siguientes describen cada tipo de mosaico en detalle y sus propiedades.

| Tipo de vista | Descripción |
|:--|:--|
| [Lista de consultas](#list-of-queries-part) | Muestra una lista de las consultas de búsqueda de registro.  El usuario puede hacer clic en cada consulta para mostrar los resultados.  |
| [Número & lista](#number-amp-list-part) | Encabezado tiene un único número con recuento de registros de una consulta de búsqueda de registro.  Lista muestra los diez mejores resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o sus cambian a lo largo del tiempo. |
| [Dos números y lista](#two-numbers-amp-list-part) | Encabezado tiene dos números que muestra el recuento de registros de las consultas de búsqueda de registro independiente.  Lista muestra los diez mejores resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o sus cambian a lo largo del tiempo. |
| [Lista & anillo](#donut-amp-list-part) | Encabezado muestra un único número resumido en una columna de valor en una consulta de registro.  El anillo muestra gráficamente los resultados de los tres registros superiores. |
| [Dos escalas de tiempo y lista](#two-timelines-amp-list-part) | Encabezado muestra los resultados de dos consultas de registro con el tiempo, como los gráficos de columnas con un globo que muestra un único número resumido en una columna de valor en una consulta de registro.  Lista muestra los diez mejores resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o sus cambian a lo largo del tiempo. |   
| [Información](#information-part) | Encabezado muestra texto estático y un vínculo opcional.  Lista muestra uno o varios elementos con texto estático y un título. |
| [Gráfico de líneas, lista & llamada](#line-chart-callout-amp-list-part) | Encabezado muestra un gráfico de líneas con varias series de una consulta de registro con el tiempo y una llamada con un valor resumido.  Lista muestra los diez mejores resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o sus cambian a lo largo del tiempo. |
| [Lista y gráfico de líneas](#line-chart-amp-list-part) | Encabezado muestra un gráfico de líneas con varias series de una consulta de registro a lo largo del tiempo.  Lista muestra los diez mejores resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o sus cambian a lo largo del tiempo. |
| [Pila de parte de los gráficos de líneas](#stack-of-line-charts-part) | Muestra los tres gráficos de líneas separadas con varias series de una consulta de registro con el tiempo. |


## <a name="list-of-queries-part"></a>Lista de elementos de consultas

Muestra una lista de las consultas de búsqueda de registro.  El usuario puede hacer clic en cada consulta para mostrar los resultados.  La vista incluirá una única consulta de forma predeterminada y se puede hacer clic en **+ consulta** para agregar consultas adicionales.

![Lista de vista de consultas](media/log-analytics-view-designer/view-list-queries.png)

| Configuración | Descripción |
|:--|:--|
| **General** |
| Título | Texto para mostrar en la parte superior de la vista. |
| Nuevo grupo | Seleccione esta opción para crear un nuevo grupo en la vista comenzando en la vista actual. |
| Filtros previamente seleccionados | Lista delimitada por comas de propiedades para incluir en el panel izquierdo del filtro cuando el usuario selecciona una consulta. |
| Modo de representar | Vista inicial que se muestra cuando se selecciona la consulta.  El usuario puede seleccionar cualquier vistas disponibles después de abrir la consulta. |
| **Consultas** |
| Consulta de búsqueda | Ejecutar la consulta. |
| Nombre descriptivo | Nombre descriptivo de la consulta para mostrar al usuario. |


## <a name="number--list-part"></a>Elemento de lista y número

Encabezado tiene un único número con recuento de registros de una consulta de búsqueda de registro.  Lista muestra los diez mejores resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o sus cambian a lo largo del tiempo.


![Lista de vista de consultas](media/log-analytics-view-designer/view-number-list.png)

| Configuración | Descripción |
|:--|:--|
| **General** |
| Título de grupo | Texto para mostrar en la parte superior de la vista. |
| Nuevo grupo | Seleccione esta opción para crear un nuevo grupo en la vista comenzando en la vista actual. |
| Icono | Archivo de imagen para mostrar junto al resultado en el encabezado.
| Icono de uso | Seleccione esta opción para que la presentación del icono. |
| **Título** |
| Leyenda | Texto para mostrar en la parte superior del encabezado. |
| Consulta | Crear una consulta para ejecutar para el encabezado.  Se mostrará el recuento del número de registros devueltos por la consulta. |
| **Lista** |
| Consulta | Crear una consulta para ejecutar la lista.  Se mostrarán las primeras dos propiedades para los diez primeros registros en los resultados.  La primera propiedad debe ser un valor de texto y la segunda propiedad un valor numérico.  Barras se crean automáticamente en función del valor de la columna numérica relativo.<br><br>Use el comando Ordenar en la consulta para ordenar los registros en la lista.  El usuario puede haga clic en Ver todo para ejecutar la consulta y devolver todos los registros. |
| Ocultar gráfico | Seleccione esta opción para deshabilitar el gráfico a la derecha de la columna numérica. |
| Habilitar minigráficos | Seleccione esta opción para mostrar los minigráficos en lugar de la barra horizontal.  Para obtener más información, consulte [Ajustes comunes](#sparklines) . |
| Color | Color de las barras o los minigráficos. |
| Nombre & separador de valor | Delimitador de carácter si desea analizar la propiedad text en varios valores.  Para obtener más información, consulte [Ajustes comunes](#name-value-separator) . |
| Consulta de navegación | Consulta que se ejecuta cuando el usuario selecciona un elemento de la lista.  Para obtener más información, consulte [Ajustes comunes](#navigation-query) . |
| **Lista** | **> Títulos de columna** |
| Nombre | Texto para mostrar en la parte superior de la primera columna de la lista. |
| Valor | Texto para mostrar en la parte superior de la segunda columna de la lista. |
| **Lista** | **> Umbrales de** |
| Habilitar umbrales | Seleccione esta opción para habilitar los umbrales.  Para obtener más información, consulte [Ajustes comunes](#thresholds) . |


## <a name="two-numbers--list-part"></a>Elemento de lista y de dos números.

Encabezado tiene dos números que muestra el recuento de registros de las consultas de búsqueda de registro independiente.  Lista muestra los diez mejores resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o sus cambian a lo largo del tiempo.

![Vista de lista y de dos números.](media/log-analytics-view-designer/view-two-numbers-list.png)

| Configuración | Descripción |
|:--|:--|
| **General** |
| Título de grupo | Texto para mostrar en la parte superior de la vista. |
| Nuevo grupo | Seleccione esta opción para crear un nuevo grupo en la vista comenzando en la vista actual. |
| Icono | Archivo de imagen para mostrar junto al resultado en el encabezado.
| Icono de uso | Seleccione esta opción para que la presentación del icono. |
| **Título** |
| Leyenda | Texto para mostrar en la parte superior del encabezado. |
| Consulta | Crear una consulta para ejecutar para el encabezado.  Se mostrará el recuento del número de registros devueltos por la consulta. |
| **Lista** |
| Consulta | Crear una consulta para ejecutar la lista.  Se mostrarán las primeras dos propiedades para los diez primeros registros en los resultados.  La primera propiedad debe ser un valor de texto y la segunda propiedad un valor numérico.  Barras se crean automáticamente en función del valor de la columna numérica relativo.<br><br>Use el comando Ordenar en la consulta para ordenar los registros en la lista.  El usuario puede haga clic en Ver todo para ejecutar la consulta y devolver todos los registros. |
| Ocultar gráfico | Seleccione esta opción para deshabilitar el gráfico a la derecha de la columna numérica. |
| Habilitar minigráficos | Seleccione esta opción para mostrar los minigráficos en lugar de la barra horizontal.  Para obtener más información, consulte [Ajustes comunes](#sparklines) . |
| Color | Color de las barras o los minigráficos. |
| Operación | Operación que debe realizar para el minigráfico.  Para obtener más información, consulte [Ajustes comunes](#sparklines) . |
| Nombre & separador de valor | Delimitador de carácter si desea analizar la propiedad text en varios valores.  Para obtener más información, consulte [Ajustes comunes](#name-value-separator) . |
| Consulta de navegación | Consulta que se ejecuta cuando el usuario selecciona un elemento de la lista.  Para obtener más información, consulte [Ajustes comunes](#navigation-query) . |
| **Lista** | **> Títulos de columna** |
| Nombre | Texto para mostrar en la parte superior de la primera columna de la lista. |
| Valor | Texto para mostrar en la parte superior de la segunda columna de la lista. |
| **Lista** | **> Umbrales de** |
| Habilitar umbrales | Seleccione esta opción para habilitar los umbrales.  Para obtener más información, consulte [Ajustes comunes](#thresholds) . |

## <a name="donut--list-part"></a>Elemento de lista y anillo

Encabezado muestra un único número resumido en una columna de valor en una consulta de registro.  El anillo muestra gráficamente los resultados de los tres registros superiores.

![Vista de lista y de anillo](media/log-analytics-view-designer/view-donut-list.png)

| Configuración | Descripción |
|:--|:--|
| **General** |
| Título de grupo | Texto para mostrar en la parte superior de la ventana. |
| Nuevo grupo | Seleccione esta opción para crear un nuevo grupo en la vista comenzando en la vista actual. |
| Icono | Archivo de imagen para mostrar junto al resultado en el encabezado. |
| Icono de uso | Seleccione esta opción para que la presentación del icono. |
| **Encabezado** |
| Título | Texto para mostrar en la parte superior del encabezado.
| Subtítulo | Texto que se muestra debajo del título en la parte superior del encabezado.
| **Anillo** |
| Consulta | Crear una consulta para ejecutar para el anillo.  La primera propiedad debe ser un valor de texto y la segunda propiedad un valor numérico. |
| **Anillo** |  **> Centro de** |
| Texto | Texto para mostrar en el valor dentro del anillo. |
| Operación | La operación que realice en la propiedad valor para resumir a un único valor.<br><br>-Suma: Sumar los valores de todos los registros.<br>-Porcentaje: Porcentaje de los registros devueltos por los valores de **los valores de resultado utilizados en operación centro** para el número total de registros en la consulta. |
| Valores de resultado utilizados en operación centro | Opcionalmente, haga clic en el signo más para agregar uno o varios valores.  Los resultados de la consulta se limitarán a los registros con los valores de propiedad que especifique.  Si no se agrega ningún valor, se incluyen todos los registros en la consulta. |
| **Opciones adicionales** | **> Colores** |
| Color 1<br>Color 2<br>Color 3 | Seleccione el color para cada uno de los valores que aparecen en el anillo. |
| **Opciones adicionales** | **> Asignación de Color avanzado** |
| Valor del campo | Escriba el nombre de un campo para que se muestre como un color distinto si se incluye en el anillo. |
| Color | Seleccione el color para el campo único. |
| **Lista** |
| Consulta | Crear una consulta para ejecutar la lista.  Se mostrará el recuento del número de registros devueltos por la consulta. |
| Ocultar gráfico | Seleccione esta opción para deshabilitar el gráfico a la derecha de la columna numérica. |
| Habilitar minigráficos | Seleccione esta opción para mostrar los minigráficos en lugar de la barra horizontal.  Para obtener más información, consulte [Ajustes comunes](#sparklines) . |
| Color | Color de las barras o los minigráficos. |
| Operación | Operación que debe realizar para el minigráfico.  Para obtener más información, consulte [Ajustes comunes](#sparklines) . |
| Nombre & separador de valor | Delimitador de carácter si desea analizar la propiedad text en varios valores.  Para obtener más información, consulte [Ajustes comunes](#name-value-separator) . |
| Consulta de navegación | Consulta que se ejecuta cuando el usuario selecciona un elemento de la lista.  Para obtener más información, consulte [Ajustes comunes](#navigation-query) . |
| **Lista** | **> Títulos de columna** |
| Nombre | Texto para mostrar en la parte superior de la primera columna de la lista. |
| Valor | Texto para mostrar en la parte superior de la segunda columna de la lista. |
| **Lista** | **> Umbrales de** |
| Habilitar umbrales | Seleccione esta opción para habilitar los umbrales.  Para obtener más información, consulte [Ajustes comunes](#thresholds) . |

## <a name="two-timelines--list-part"></a>Dos elementos de lista & escalas de tiempo

Encabezado muestra los resultados de dos consultas de registro con el tiempo, como los gráficos de columnas con un globo que muestra un único número resumido en una columna de valor en una consulta de registro.  Lista muestra los diez mejores resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o sus cambian a lo largo del tiempo.

![Vista de dos escalas de tiempo y lista](media/log-analytics-view-designer/view-two-timelines-list.png)

| Configuración | Descripción |
|:--|:--|
| **General** |
| Título de grupo | Texto para mostrar en la parte superior de la ventana. |
| Nuevo grupo | Seleccione esta opción para crear un nuevo grupo en la vista comenzando en la vista actual. |
| Icono | Archivo de imagen para mostrar junto al resultado en el encabezado. |
| Icono de uso | Seleccione esta opción para que la presentación del icono. |
| **En primer lugar del gráfico<br>segundo gráfico** |
| Leyenda | Texto para mostrar en el globo de la primera serie. |
| Color | Color para las columnas de la serie. |
| Consulta | Consulta de ejecución de la primera serie.  El recuento del número de registros en cada intervalo de tiempo se representarán por las columnas del gráfico. |
| Operación | La operación que realice en la propiedad valor para resumir un valor único para la llamada.<br><br>-Suma: Suma el valor de todos los registros.<br>-Promedio: El promedio del valor de todos los registros.<br>-Ejemplo última: Valor desde el último intervalo incluido en el gráfico.<br>-Ejemplo primera: Valor desde el primer intervalo incluido en el gráfico.<br>-Recuento: El recuento de todos los registros devueltos por la consulta.|
| **Lista** |
| Consulta | Crear una consulta para ejecutar la lista.  Se mostrará el recuento del número de registros devueltos por la consulta. |
| Ocultar gráfico | Seleccione esta opción para deshabilitar el gráfico a la derecha de la columna numérica. |
| Habilitar minigráficos | Seleccione esta opción para mostrar los minigráficos en lugar de la barra horizontal.  Para obtener más información, consulte [Ajustes comunes](#sparklines) . |
| Color | Color de las barras o los minigráficos. |
| Operación | Operación que debe realizar para el minigráfico.  Para obtener más información, consulte [Ajustes comunes](#sparklines) . |
| Consulta de navegación | Consulta que se ejecuta cuando el usuario selecciona un elemento de la lista.  Para obtener más información, consulte [Ajustes comunes](#navigation-query) . |
| **Lista** | **> Títulos de columna** |
| Nombre | Texto para mostrar en la parte superior de la primera columna de la lista. |
| Valor | Texto para mostrar en la parte superior de la segunda columna de la lista. |
| **Lista** | **> Umbrales de** |
| Habilitar umbrales | Seleccione esta opción para habilitar los umbrales.  Para obtener más información, consulte [Ajustes comunes](#thresholds) . |

## <a name="information-part"></a>Parte de la información

Encabezado muestra texto estático y un vínculo opcional.  Lista muestra uno o varios elementos con texto estático y un título.

![Vista de información.](media/log-analytics-view-designer/view-information.png)

| Configuración | Descripción |
|:--|:--|
| **General** |
| Título de grupo | Texto para mostrar en la parte superior de la ventana. |
| Nuevo grupo | Seleccione esta opción para crear un nuevo grupo en la vista comenzando en la vista actual. |
| Color | Color de fondo del encabezado. |
| **Encabezado** |
| Imagen | Archivo de imagen para mostrar en el encabezado. |
| Etiqueta | Texto para mostrar en el encabezado. |
| **Encabezado** | **> Vínculo** |
| Etiqueta | Texto del vínculo. |
| Dirección URL | Dirección URL de vínculo. |
| **Elementos de información** |
| Título | Texto para mostrar para el título de cada elemento. |
| Contenido | Texto para mostrar de cada elemento. |


## <a name="line-chart-callout--list-part"></a>Gráfico de líneas, llamada y elemento de lista

Encabezado muestra un gráfico de líneas con varias series de una consulta de registro con el tiempo y una llamada con un valor resumido.  Lista muestra los diez mejores resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o sus cambian a lo largo del tiempo.

![Gráfico de líneas, la llamada y la vista de lista](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Configuración | Descripción |
|:--|:--|
| **General** |
| Título de grupo | Texto para mostrar en la parte superior de la ventana. |
| Nuevo grupo | Seleccione esta opción para crear un nuevo grupo en la vista comenzando en la vista actual. |
| Icono | Archivo de imagen para mostrar junto al resultado en el encabezado. |
| Icono de uso | Seleccione esta opción para que la presentación del icono. |
| **Encabezado** |
| Título | Texto para mostrar en la parte superior del encabezado. |
| Subtítulo | Texto que se muestra debajo del título en la parte superior del encabezado. |
| **Gráfico de líneas** |
| Consulta | Crear una consulta para ejecutar para el gráfico de líneas.  La primera propiedad debe ser un valor de texto y la segunda propiedad un valor numérico.  Suele ser una consulta que usa la palabra clave de **medida** a resumir los resultados.  Si la consulta utiliza la palabra clave de **intervalo** del eje x del gráfico usará este intervalo de tiempo.  Si la consulta no incluye la palabra clave de **intervalo** intervalos horarios se utilizan para el eje x. |
| **Gráfico de líneas** | **> Llamada** |
| Título de la llamada | Texto para mostrar por encima del valor de la llamada. |
| Nombre de la serie | Valor de propiedad de la serie a utilizar para el valor de la llamada.  Si no se proporciona ningún serie, se usan todos los registros de la consulta. |
| Operación | La operación que realice en la propiedad valor para resumir un valor único para la llamada.<br><br>-Promedio: El promedio del valor de todos los registros.<br>-Recuento de todos los registros devueltos por la consulta.<br>-Ejemplo última: Valor desde el último intervalo incluido en el gráfico.<br>-Max: Valor máximo uno de los intervalos que se incluyen en el gráfico.<br>-Min: Valor mínimo uno de los intervalos que se incluyen en el gráfico.<br>-Suma: Suma el valor de todos los registros. |
| **Gráfico de líneas** | **> Eje Y** |
| Usar escala logarítmica | Seleccione Usar una escala logarítmica del eje y. |
| Unidades | Especificar las unidades para los valores devueltos por la consulta.  Esta información se usa para mostrar las etiquetas en el gráfico que indica los tipos de valor y, opcionalmente, para convertir los valores.  El tipo de unidad especifica la categoría de la unidad y define los valores de tipo de unidad actual que están disponibles.  Si selecciona un valor en convertir en, a continuación, los valores numéricos se convierten en el tipo de unidad actual en el convertir para escribir. |
| Etiqueta personalizada | Texto para mostrar para el eje Y junto a la etiqueta para el tipo de unidad.  Si no se especifica etiqueta, se muestra el tipo de unidad. |
| **Lista** |
| Consulta | Crear una consulta para ejecutar la lista.  Se mostrará el recuento del número de registros devueltos por la consulta. |
| Ocultar gráfico | Seleccione esta opción para deshabilitar el gráfico a la derecha de la columna numérica. |
| Habilitar minigráficos | Seleccione esta opción para mostrar los minigráficos en lugar de la barra horizontal.  Para obtener más información, consulte [Ajustes comunes](#sparklines) . |
| Color | Color de las barras o los minigráficos. |
| Operación | Operación que debe realizar para el minigráfico.  Para obtener más información, consulte [Ajustes comunes](#sparklines) . |
| Nombre & separador de valor | Delimitador de carácter si desea analizar la propiedad text en varios valores.  Para obtener más información, consulte [Ajustes comunes](#name-value-separator) . |
| Consulta de navegación | Consulta que se ejecuta cuando el usuario selecciona un elemento de la lista.  Para obtener más información, consulte [Ajustes comunes](#navigation-query) . |
| **Lista** | **> Títulos de columna** |
| Nombre | Texto para mostrar en la parte superior de la primera columna de la lista. |
| Valor | Texto para mostrar en la parte superior de la segunda columna de la lista. |
| **Lista** | **> Umbrales de** |
| Habilitar umbrales | Seleccione esta opción para habilitar los umbrales.  Para obtener más información, consulte [Ajustes comunes](#thresholds) . |

## <a name="line-chart--list-part"></a>Elemento de gráfico y la lista de línea

Encabezado muestra un gráfico de líneas con varias series de una consulta de registro a lo largo del tiempo.  Lista muestra los diez mejores resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o sus cambian a lo largo del tiempo.

![Vista de gráfico y lista de línea](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Configuración | Descripción |
|:--|:--|
| **General** |
| Título de grupo | Texto para mostrar en la parte superior de la ventana. |
| Nuevo grupo | Seleccione esta opción para crear un nuevo grupo en la vista comenzando en la vista actual. |
| Icono | Archivo de imagen para mostrar junto al resultado en el encabezado. |
| Icono de uso | Seleccione esta opción para que la presentación del icono. |
| **Encabezado** |
| Título | Texto para mostrar en la parte superior del encabezado. |
| Subtítulo | Texto que se muestra debajo del título en la parte superior del encabezado. |
| **Gráfico de líneas** |
| Consulta | Crear una consulta para ejecutar para el gráfico de líneas.  La primera propiedad debe ser un valor de texto y la segunda propiedad un valor numérico.  Suele ser una consulta que usa la palabra clave de **medida** a resumir los resultados.  Si la consulta utiliza la palabra clave de **intervalo** del eje x del gráfico usará este intervalo de tiempo.  Si la consulta no incluye la palabra clave de **intervalo** intervalos horarios se utilizan para el eje x. |
| **Gráfico de líneas** | **> Eje Y** |
| Usar escala logarítmica | Seleccione Usar una escala logarítmica del eje y. |
| Unidades | Especificar las unidades para los valores devueltos por la consulta.  Esta información se usa para mostrar las etiquetas en el gráfico que indica los tipos de valor y, opcionalmente, para convertir los valores.  El tipo de unidad especifica la categoría de la unidad y define los valores de tipo de unidad actual que están disponibles.  Si selecciona un valor en convertir en, a continuación, los valores numéricos se convierten en el tipo de unidad actual en el convertir para escribir. |
| Etiqueta personalizada | Texto para mostrar para el eje Y junto a la etiqueta para el tipo de unidad.  Si no se especifica etiqueta, se muestra el tipo de unidad. |
| **Lista** |
| Consulta | Crear una consulta para ejecutar la lista.  Se mostrará el recuento del número de registros devueltos por la consulta. |
| Ocultar gráfico | Seleccione esta opción para deshabilitar el gráfico a la derecha de la columna numérica. |
| Habilitar minigráficos | Seleccione esta opción para mostrar los minigráficos en lugar de la barra horizontal.  Para obtener más información, consulte [Ajustes comunes](#sparklines) . |
| Color | Color de las barras o los minigráficos. |
| Operación | Operación que debe realizar para el minigráfico.  Para obtener más información, consulte [Ajustes comunes](#sparklines) . |
| Nombre & separador de valor | Delimitador de carácter si desea analizar la propiedad text en varios valores.  Para obtener más información, consulte [Ajustes comunes](#name-value-separator) . |
| Consulta de navegación | Consulta que se ejecuta cuando el usuario selecciona un elemento de la lista.  Para obtener más información, consulte [Ajustes comunes](#navigation-query) . |
| **Lista** | **> Títulos de columna** |
| Nombre | Texto para mostrar en la parte superior de la primera columna de la lista. |
| Valor | Texto para mostrar en la parte superior de la segunda columna de la lista. |
| **Lista** | **> Umbrales de** |
| Habilitar umbrales | Seleccione esta opción para habilitar los umbrales.  Para obtener más información, consulte [Ajustes comunes](#thresholds) . |

## <a name="stack-of-line-charts-part"></a>Pila de parte de los gráficos de líneas

Muestra los tres gráficos de líneas separadas con varias series de una consulta de registro con el tiempo.

![Pila de gráficos de líneas](media/log-analytics-view-designer/view-stack-line-charts.png)

| Configuración | Descripción |
|:--|:--|
| **General** |
| Título de grupo | Texto para mostrar en la parte superior de la ventana. |
| Nuevo grupo | Seleccione esta opción para crear un nuevo grupo en la vista comenzando en la vista actual. |
| Icono | Archivo de imagen para mostrar junto al resultado en el encabezado. |
| **Gráfico 1<br>gráfico 2<br>gráfico 3** | **> Encabezado** |
| Título | Texto para mostrar en la parte superior del gráfico. |
| Subtítulo | Texto que se muestra debajo del título en la parte superior del gráfico. |
| **Gráfico 1<br>gráfico 2<br>gráfico 3** | **Gráfico de líneas** |
| Consulta | Crear una consulta para ejecutar para el gráfico de líneas.  La primera propiedad debe ser un valor de texto y la segunda propiedad un valor numérico.  Suele ser una consulta que usa la palabra clave de **medida** a resumir los resultados.  Si la consulta utiliza la palabra clave de **intervalo** del eje x del gráfico usará este intervalo de tiempo.  Si la consulta no incluye la palabra clave de **intervalo** intervalos horarios se utilizan para el eje x. |
| **Gráfico** | **> Eje Y** |
| Usar escala logarítmica | Seleccione Usar una escala logarítmica del eje y. |
| Unidades | Especificar las unidades para los valores devueltos por la consulta.  Esta información se usa para mostrar las etiquetas en el gráfico que indica los tipos de valor y, opcionalmente, para convertir los valores.  El tipo de unidad especifica la categoría de la unidad y define los valores de tipo de unidad actual que están disponibles.  Si selecciona un valor en convertir en, a continuación, los valores numéricos se convierten en el tipo de unidad actual en el convertir para escribir. |
| Etiqueta personalizada | Texto para mostrar para el eje Y junto a la etiqueta para el tipo de unidad.  Si no se especifica etiqueta, se muestra el tipo de unidad. |

## <a name="common-settings"></a>Opciones comunes
Las secciones siguientes describen la configuración de varias partes de visualización.

### <a name="name-value-separator">Nombre & separador de valor</a>
Delimitador de carácter si desea analizar la propiedad texto de una consulta de lista en varios valores.  Si especifica un delimitador, puede proporcionar nombres para cada campo separados por el mismo delimitador en el cuadro Nombre.

Por ejemplo, considere la posibilidad de una propiedad de *ubicación* que incluye valores como *Redmond creación 41* y *Santoña Building12*.  Puede especificar: el nombre y el separador de valor y *Creación de la ciudad* del nombre.  Esto podría analizar cada valor en dos propiedades denominados *Ciudad* y *creación*. 

### <a name="navigation-query">Consulta de navegación</a>
Consulta que se ejecuta cuando el usuario selecciona un elemento de la lista.  Usar *{elemento seleccionado}* para incluir la sintaxis de elemento que el usuario seleccionado.

Por ejemplo, si la consulta contiene una columna denominada *equipo* y la consulta de navegación es *{elemento seleccionado}*, una consulta como *equipo = "Mi PC"* se ejecuta cuando el usuario selecciona un equipo.  Si la consulta de navegación es *tipo = evento {elemento seleccionado}* , a continuación, la consulta *tipo = equipo del evento = "Mi PC"* ¿se pueden ejecutar.

### <a name="sparklines">Minigráficos</a>
Un minigráfico es un gráfico de líneas pequeña que muestra el valor de una entrada de la lista a lo largo del tiempo.  Para los elementos de visualización con una lista, puede seleccionar si desea mostrar una barra horizontal que indique el valor relativo de una columna numérica o un minigráfico que indica que su valor a lo largo del tiempo.

La siguiente tabla describe la configuración de minigráficos.

| Configuración | Descripción |
|:--|:--|
| Habilitar minigráficos | Seleccione esta opción para mostrar los minigráficos en lugar de la barra horizontal. |
| Operación | Si se habilitan minigráficos, esta es la operación que debe realizar en todas las propiedades de la lista para calcular los valores para el minigráfico.<br><br>-Ejemplo última: Último valor de la serie sobre el intervalo de tiempo.<br>-Max: Valor máximo para la serie de intervalo de tiempo.<br>-Min: Valor mínimo para la serie de intervalo de tiempo.<br>-Suma: Suma los valores de la serie de intervalo de tiempo.<br>-Resumen: Se usa el mismo comando de medida que la consulta en el encabezado. |

### <a name="thresholds">Umbrales</a>
Umbrales le permiten mostrar un icono de color junto a cada elemento de una lista que le da un indicador visual rápido de los elementos que superan un valor determinado o no dentro de un intervalo concreto.  Por ejemplo, puede mostrar un icono verde para los elementos con un valor aceptable, amarillo si el valor está dentro del rango que indica una advertencia y rojo si supera un valor de error.

Al habilitar umbrales de un elemento, debe especificar uno o varios de los umbrales.  Si el valor de un elemento es mayor que un valor de umbral y menor que el valor de umbral siguiente, se utiliza ese color.  Si el elemento es mayor que, a continuación, mayor valor de umbral, dicho color se establece.   

Cada conjunto de umbral tiene un umbral con un valor **predeterminado**.  Este es el color establecido si no se excede ningún otro valor.  Puede agregar o quitar los umbrales haciendo clic en el **+** o el botón **x** .

La siguiente tabla describe la configuración de límites.

| Configuración | Descripción |
|:--|:--|
| Habilitar umbrales | Seleccione esta opción para mostrar un icono de color a la izquierda de cada valor que indica su estado en relación con los umbrales especificados. |
| Nombre | Nombre para identificar el valor de umbral. |
| Umbral | Valor de umbral.  El color del estado de cada elemento de lista se establece en el color del mayor valor de umbral superado por el valor del elemento.  Hay un umbral predeterminado que es el color si no se ha superado valores de umbral. |
| Color | Color para el valor de umbral. |


## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de las [búsquedas de registro](log-analytics-log-searches.md) admitir las consultas en elementos de visualización.