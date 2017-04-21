<properties
    pageTitle="Iniciar el análisis vista Diseñador mosaico referencia | Microsoft Azure"
    description="Diseñador de vista de análisis de registro le permite crear vistas personalizadas en la consola OMS que contienen diferentes visualizaciones de datos en el repositorio OMS. En este artículo se proporciona una referencia de la configuración para cada uno de los mosaicos disponibles para su uso en sus vistas personalizadas."
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
    ms.date="09/27/2016"
    ms.author="bwren"/>

# <a name="log-analytics-view-designer-tile-reference"></a>Referencia de mosaico Diseñador de vista de análisis de registro
El Diseñador de vista de análisis de registro le permite crear vistas personalizadas en la consola OMS que contienen diferentes visualizaciones de datos en el repositorio OMS. En este artículo se proporciona una referencia de la configuración para cada uno de los mosaicos disponibles para su uso en sus vistas personalizadas.

Otros artículos disponibles para el Diseñador de vistas son:

- [Vista Diseñador](log-analytics-view-designer.md) : información general sobre el Diseñador de vistas y procedimientos para crear y editar vistas personalizadas.
- [Referencia de la parte de visualización](log-analytics-view-designer-parts.md) : referencia de la configuración para cada uno de los mosaicos disponibles para su uso en sus vistas personalizadas. 


La siguiente tabla enumeran los diferentes tipos de mosaicos disponibles en el Diseñador de vistas.  Las secciones siguientes describen cada tipo de mosaico en detalle y sus propiedades.

| Mosaico | Descripción |
|:--|:--|
| [Número](#number-tile) | Número único que muestra el recuento de registros de una consulta. |
| [Dos números.](#two-numbers-tile) | Dos números único que muestra los recuentos de registros de dos consultas diferentes. |
| [Anillo](#donut-tile) | Gráfico de anillos basado en una consulta con un valor de resumen en el centro. |
| [Gráfico de líneas & llamada](#line-chart-amp-callout-tile) | Gráfico de líneas basado en una consulta y una llamada con un valor de resumen. |
| [Gráfico de líneas](#line-chart-tile) | Gráfico de líneas basado en una consulta. |
| [Dos escalas de tiempo](#two-timelines-tile) | Gráfico de columnas con dos series que cada basándose en una consulta independiente. |



## <a name="number-tile"></a>Mosaico de número

El mosaico de **número** muestra un único número que muestra el recuento de registros de una consulta de registro y una etiqueta.

![Mosaico de número](media/log-analytics-view-designer/tile-number.png)

| Configuración | Descripción |
|:--|:--|
| Nombre        | Texto para mostrar en la parte superior de la ventana. |
| Descripción | Texto que se muestra debajo del nombre de mosaico.    |
| **Mosaico** |
| Leyenda | Texto para mostrar en el valor. |
| Consulta | Ejecutar la consulta.  Se mostrará el recuento del número de registros devueltos por la consulta. |
| **Avanzadas** |  **> Comprobación de flujo de datos** |
| Habilitado | Seleccione si se debe habilitar la comprobación de flujo de datos para el mosaico.  Proporciona un mensaje alternativo si datos no están disponibles para el mosaico.  Normalmente se utiliza para proporcionar un mensaje durante el período temporal cuando se instala la vista y datos proceden disponibles. |
| Consulta | Crear una consulta para ejecutar para comprobar si hay disponibles para la vista de datos.  Si la consulta no devuelve ningún resultado, se muestra un mensaje en lugar del valor de la consulta principal. |
| Mensaje | Mensaje para mostrar si la consulta de comprobación de flujo de datos no devuelve ningún dato.  Si se proporciona ningún mensaje, se muestra la *Realización de evaluación* . |
| **Intervalo de tiempo** |
| Duración | Duración de la fecha actual para el intervalo de tiempo de la consulta.  Por ejemplo, si se especifica **7 días** , la consulta se limita a registros creados a partir de 7 días a la fecha actual. |
| Desplazamiento de datos final | Desplazamiento opcional de los datos actuales para el intervalo de tiempo de la consulta principal.  Por ejemplo, si se utiliza **-1 día** para el **desplazamiento de la fecha de finalización** y **7 días** de la **duración**, la consulta se limita a registros creados a partir de 8 días en ayer. |

## <a name="two-numbers-tile"></a>Mosaico de dos números.

El mosaico de **Número dos** muestra que muestra el recuento de registros de dos consultas de registro diferentes y una etiqueta para cada uno de dos números.

![Mosaico de dos números.](media/log-analytics-view-designer/tile-two-numbers.png)

| Configuración | Descripción |
|:--|:--|
| Nombre        | Texto para mostrar en la parte superior de la ventana. |
| Descripción | Texto que se muestra debajo del nombre de mosaico.    |
| **Primer mosaico** |
| Leyenda | Texto para mostrar en el valor. |
| Consulta | Ejecutar la consulta.  Se mostrará el recuento del número de registros devueltos por la consulta. |
| **Mosaico de la segunda** |
| Leyenda | Texto para mostrar en el valor. |
| Consulta | Ejecutar la consulta.  Se mostrará el recuento del número de registros devueltos por la consulta. |
| **Avanzadas** | **> Comprobación de flujo de datos** |
| Habilitado | Seleccione si se debe habilitar la comprobación de flujo de datos para el mosaico.  Proporciona un mensaje alternativo si datos no están disponibles para el mosaico.  Normalmente se utiliza para proporcionar un mensaje durante el período temporal cuando se instala la vista y datos proceden disponibles. |
| Consulta | Crear una consulta para ejecutar para comprobar si hay disponibles para la vista de datos.  Si la consulta no devuelve ningún resultado, se muestra un mensaje en lugar del valor de la consulta principal. |
| Mensaje | Mensaje para mostrar si la consulta de comprobación de flujo de datos no devuelve ningún dato.  Si se proporciona ningún mensaje, se muestra la *Realización de evaluación* . |
| **Intervalo de tiempo** |
| Duración | Duración de la fecha actual para el intervalo de tiempo de la consulta.  Por ejemplo, si se especifica **7 días** , la consulta se limita a registros creados a partir de 7 días a la fecha actual. |
| Desplazamiento de datos final | Desplazamiento opcional de los datos actuales para el intervalo de tiempo de la consulta principal.  Por ejemplo, si se utiliza **-1 día** para el **desplazamiento de la fecha de finalización** y **7 días** de la **duración**, la consulta se limita a registros creados a partir de 8 días en ayer. |

## <a name="donut-tile"></a>Mosaico de anillo

El mosaico **anillo** muestra un único número resumido en una columna de valor en una consulta de registro.  El anillo muestra gráficamente los resultados de los tres registros superiores.

![Mosaico de anillo](media/log-analytics-view-designer/tile-donut.png)

| Configuración | Descripción |
|:--|:--|
| Nombre        | Texto para mostrar en la parte superior de la ventana. |
| Descripción | Texto que se muestra debajo del nombre de mosaico.    |
| **Anillo** |
| Consulta | Crear una consulta para ejecutar para el anillo.  La primera propiedad debe ser un valor de texto y la segunda propiedad un valor numérico.  Suele ser una consulta que usa la palabra clave de **medida** a resumir los resultados. |
| **Anillo** | **> Centro de** |
| Texto | Texto para mostrar en el valor dentro del anillo. |
| Operación | La operación que realice en la propiedad valor para resumir a un único valor.<br><br>-Suma: Sumar los valores de todos los registros con el valor de propiedad.<br>-Porcentaje: Porcentaje de los valores de sumado de los registros con el valor de propiedad en comparación con los valores de la sumado de todos los registros. |
| Valores de resultado utilizados en operación centro | Opcionalmente, haga clic en el signo más para agregar uno o varios valores.  Los resultados de la consulta se limitarán a los registros con los valores de propiedad que especifique.  Si no se agregan valores, que se incluyen todos los registros en la consulta. |
| **Anillo** | **> Opciones adicionales** |
| Colores | El color que se mostrará para cada una de las tres propiedades superiores.  Si desea especificar colores alternativos para los valores de propiedad específica, utilice avanzadas asignación de Color. |
| Asignación de Color avanzada | Muestra un color para los valores de propiedad específicos.  Si es el valor que se especifica en los tres principales, se muestra un color diferente en lugar del color estándar.  Si la propiedad no está en los tres principales, no se muestra el color. |
| **Avanzadas** | **> Comprobación de flujo de datos** |
| Habilitado | Seleccione si se debe habilitar la comprobación de flujo de datos para el mosaico.  Proporciona un mensaje alternativo si datos no están disponibles para el mosaico.  Normalmente se utiliza para proporcionar un mensaje durante el período temporal cuando se instala la vista y datos proceden disponibles. |
| Consulta | Crear una consulta para ejecutar para comprobar si hay disponibles para la vista de datos.  Si la consulta no devuelve ningún resultado, se muestra un mensaje en lugar del valor de la consulta principal. |
| Mensaje | Mensaje para mostrar si la consulta de comprobación de flujo de datos no devuelve ningún dato.  Si se proporciona ningún mensaje, se muestra la *Realización de evaluación* . |
| **Intervalo de tiempo** |
| Duración | Duración de la fecha actual para el intervalo de tiempo de la consulta.  Por ejemplo, si se especifica **7 días** , la consulta se limita a registros creados a partir de 7 días a la fecha actual. |
| Desplazamiento de datos final | Desplazamiento opcional de los datos actuales para el intervalo de tiempo de la consulta principal.  Por ejemplo, si se utiliza **-1 día** para el **desplazamiento de la fecha de finalización** y **7 días** de la **duración**, la consulta se limita a registros creados a partir de 8 días en ayer. |

## <a name="line-chart-tile"></a>Mosaico de gráfico de líneas

El mosaico de **gráfico de líneas** muestra un gráfico de líneas con varias series de una consulta de registro con el tiempo.  

![Mosaico de gráfico de líneas y de llamada](media/log-analytics-view-designer/tile-line-chart.png)

| Configuración | Descripción |
|:--|:--|
| Nombre        | Texto para mostrar en la parte superior de la ventana. |
| Descripción | Texto que se muestra debajo del nombre de mosaico.    |
| **Gráfico de líneas** |  
| Consulta | Crear una consulta para ejecutar para el gráfico de líneas.  La primera propiedad debe ser un valor de texto y la segunda propiedad un valor numérico.  Suele ser una consulta que usa la palabra clave de **medida** a resumir los resultados.  Si la consulta utiliza la palabra clave de **intervalo** del eje x del gráfico usará este intervalo de tiempo.  Si la consulta no incluye la palabra clave de **intervalo** intervalos horarios se utilizan para el eje x. |
| **Gráfico de líneas** | **> Eje Y** |
| Usar escala logarítmica | Seleccione Usar una escala logarítmica del eje y. |
| Unidades | Especificar las unidades para los valores devueltos por la consulta.  Esta información se usa para mostrar las etiquetas en el gráfico que indica los tipos de valor y, opcionalmente, para convertir los valores.  El **Tipo de unidad** especifica la categoría de la unidad y define los valores de **Tipo de unidad actual** que están disponibles.  Si selecciona un valor en **convertir en** los valores numéricos se convierten en el tipo de **Unidad actual** al **convertir en** tipo. |
| Etiqueta personalizada | Texto para mostrar para el eje Y junto a la etiqueta para el tipo de unidad.  Si no se especifica etiqueta, se muestra el tipo de unidad. |
| **Avanzadas** | **> Comprobación de flujo de datos** |
| Habilitado | Seleccione si se debe habilitar la comprobación de flujo de datos para el mosaico.  Proporciona un mensaje alternativo si datos no están disponibles para el mosaico.  Normalmente se utiliza para proporcionar un mensaje durante el período temporal cuando se instala la vista y datos proceden disponibles. |
| Consulta | Crear una consulta para ejecutar para comprobar si hay disponibles para la vista de datos.  Si la consulta no devuelve ningún resultado, se muestra un mensaje en lugar del valor de la consulta principal. |
| Mensaje | Mensaje para mostrar si la consulta de comprobación de flujo de datos no devuelve ningún dato.  Si se proporciona ningún mensaje, se muestra la *Realización de evaluación* . |
| **Intervalo de tiempo** |
| Duración | Duración de la fecha actual para el intervalo de tiempo de la consulta.  Por ejemplo, si se especifica **7 días** , la consulta se limita a registros creados a partir de 7 días a la fecha actual. |
| Desplazamiento de datos final | Desplazamiento opcional de los datos actuales para el intervalo de tiempo de la consulta principal.  Por ejemplo, si se utiliza **-1 día** para el **desplazamiento de la fecha de finalización** y **7 días** de la **duración**, la consulta se limita a registros creados a partir de 8 días en ayer. |


## <a name="line-chart--callout-tile"></a>Mosaico de llamada & gráfico de líneas

El mosaico de **llamada y gráfico de líneas** muestra un gráfico de líneas con varias series de una consulta de registro con el tiempo y una llamada con un valor resumido.  

![Mosaico de gráfico de líneas y de llamada](media/log-analytics-view-designer/tile-line-chart-callout.png)

| Configuración | Descripción |
|:--|:--|
| Nombre        | Texto para mostrar en la parte superior de la ventana. |
| Descripción | Texto que se muestra debajo del nombre de mosaico.    |
| **Gráfico de líneas** |  
| Consulta | Crear una consulta para ejecutar para el gráfico de líneas.  La primera propiedad debe ser un valor de texto y la segunda propiedad un valor numérico.  Suele ser una consulta que usa la palabra clave de **medida** a resumir los resultados.  Si la consulta utiliza la palabra clave de **intervalo** del eje x del gráfico usará este intervalo de tiempo.  Si la consulta no incluye la palabra clave de **intervalo** intervalos horarios se utilizan para el eje x. |
| **Gráfico de líneas** | **> Llamada** |
| Llamada | Texto de título que se muestra por encima del valor de la llamada. |
| Nombre de la serie | Valor de propiedad de la serie a utilizar para el valor de la llamada.  Si no se proporciona ningún serie, se usan todos los registros de la consulta. |
| Operación | La operación que realice en la propiedad valor para resumir un valor único para la llamada.<br>-Promedio: El promedio del valor de todos los registros.<br><br>-Recuento: El recuento de todos los registros devueltos por la consulta.<br>-Ejemplo última: Valor desde el último intervalo incluido en el gráfico.<br>-Max: Valor máximo uno de los intervalos que se incluyen en el gráfico.<br>-Min: Valor mínimo uno de los intervalos que se incluyen en el gráfico.<br>-Suma: Suma el valor de todos los registros. |
| **Gráfico de líneas** | **> Eje Y** |
| Usar escala logarítmica | Seleccione Usar una escala logarítmica del eje y. |
| Unidades | Especificar las unidades para los valores devueltos por la consulta.  Esta información se usa para mostrar las etiquetas en el gráfico que indica los tipos de valor y, opcionalmente, para convertir los valores.  El **Tipo de unidad** especifica la categoría de la unidad y define los valores de **Tipo de unidad actual** que están disponibles.  Si selecciona un valor en **convertir en** los valores numéricos se convierten en el tipo de **Unidad actual** al **convertir en** tipo. |
| Etiqueta personalizada | Texto para mostrar para el eje Y junto a la etiqueta para el tipo de unidad.  Si no se especifica etiqueta, se muestra el tipo de unidad. |
| **Avanzadas** | **> Comprobación de flujo de datos** |
| Habilitado | Seleccione si se debe habilitar la comprobación de flujo de datos para el mosaico.  Proporciona un mensaje alternativo si datos no están disponibles para el mosaico.  Normalmente se utiliza para proporcionar un mensaje durante el período temporal cuando se instala la vista y datos proceden disponibles. |
| Consulta | Crear una consulta para ejecutar para comprobar si hay disponibles para la vista de datos.  Si la consulta no devuelve ningún resultado, se muestra un mensaje en lugar del valor de la consulta principal. |
| Mensaje | Mensaje para mostrar si la consulta de comprobación de flujo de datos no devuelve ningún dato.  Si se proporciona ningún mensaje, se muestra la *Realización de evaluación* . |
| **Intervalo de tiempo** |
| Duración | Duración de la fecha actual para el intervalo de tiempo de la consulta.  Por ejemplo, si se especifica **7 días** , la consulta se limita a registros creados a partir de 7 días a la fecha actual. |
| Desplazamiento de datos final | Desplazamiento opcional de los datos actuales para el intervalo de tiempo de la consulta principal.  Por ejemplo, si se utiliza **-1 día** para el **desplazamiento de la fecha de finalización** y **7 días** de la **duración**, la consulta se limita a registros creados a partir de 8 días en ayer. |

## <a name="two-timelines-tile"></a>Mosaico de dos escalas de tiempo

El mosaico de **dos escalas de tiempo** muestra los resultados de dos consultas de registro con el tiempo, como los gráficos de columnas.  Se muestra una llamada para cada serie.  

![Mosaico de dos escalas de tiempo](media/log-analytics-view-designer/tile-two-timelines.png)

| Configuración | Descripción |
|:--|:--|
| Nombre        | Texto para mostrar en la parte superior de la ventana. |
| Descripción | Texto que se muestra debajo del nombre de mosaico.    |
| Primer gráfico   
| Leyenda | Texto para mostrar en el globo de la primera serie.
| Color | Color para las columnas de la primera serie.
| Consulta de gráfico | Consulta de ejecución de la primera serie.  El recuento del número de registros en cada intervalo de tiempo se representarán por las columnas del gráfico.
| Operación | La operación que realice en la propiedad valor para resumir un valor único para la llamada.<br><br>-Promedio: El promedio del valor de todos los registros.<br>-Recuento: El recuento de todos los registros devueltos por la consulta.<br>-Ejemplo última: Valor desde el último intervalo incluido en el gráfico.<br>-Max: Valor máximo uno de los intervalos que se incluyen en el gráfico.
| **Segundo gráfico** |
| Leyenda | Texto para mostrar en el globo para la segunda serie.
| Color | Color para las columnas de la segunda serie.
| Consulta de gráfico | Crear una consulta para ejecutar para la segunda serie.  El recuento del número de registros en cada intervalo de tiempo se representarán por las columnas del gráfico.
| Operación | La operación que realice en la propiedad valor para resumir un valor único para la llamada.<br><br>-Promedio: El promedio del valor de todos los registros.<br>-Recuento: El recuento de todos los registros devueltos por la consulta.<br>-Ejemplo última: Valor desde el último intervalo incluido en el gráfico.<br>-Max: Valor máximo uno de los intervalos que se incluyen en el gráfico. |
| **Avanzadas** | **> Comprobación de flujo de datos** |
| Habilitado | Seleccione si se debe habilitar la comprobación de flujo de datos para el mosaico.  Proporciona un mensaje alternativo si datos no están disponibles para el mosaico.  Normalmente se utiliza para proporcionar un mensaje durante el período temporal cuando se instala la vista y datos proceden disponibles. |
| Consulta | Crear una consulta para ejecutar para comprobar si hay disponibles para la vista de datos.  Si la consulta no devuelve ningún resultado, se muestra un mensaje en lugar del valor de la consulta principal. |
| Mensaje | Mensaje para mostrar si la consulta de comprobación de flujo de datos no devuelve ningún dato.  Si se proporciona ningún mensaje, se muestra la *Realización de evaluación* . |
| **Intervalo de tiempo** |
| Duración | Duración de la fecha actual para el intervalo de tiempo de la consulta.  Por ejemplo, si se especifica **7 días** , la consulta se limita a registros creados a partir de 7 días a la fecha actual. |
| Desplazamiento de datos final | Desplazamiento opcional de los datos actuales para el intervalo de tiempo de la consulta principal.  Por ejemplo, si se utiliza **-1 día** para el **desplazamiento de la fecha de finalización** y **7 días** de la **duración**, la consulta se limita a registros creados a partir de 8 días en ayer. |

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de las [búsquedas de registro](log-analytics-log-searches.md) a las consultas de soporte técnico en mosaicos.
- Agregar [Elementos de visualización](log-analytics-view-designer-parts.md) a la vista personalizada.