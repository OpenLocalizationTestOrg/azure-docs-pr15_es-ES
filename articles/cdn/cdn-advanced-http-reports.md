<properties
    pageTitle="CDN Azure HTTP informes avanzados | Microsoft Azure"
    description="Informes HTTP avanzados en Microsoft Azure CDN. Estos informes proporcionan información detallada sobre la actividad CDN."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

# <a name="advanced-http-reports-in-microsoft-azure-cdn"></a>Informes HTTP avanzados en Microsoft Azure CDN

## <a name="overview"></a>Información general

Este documento explica la creación de informes avanzadas de HTTP en Microsoft Azure CDN. Estos informes proporcionan información detallada sobre la actividad CDN.

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Acceso a informes HTTP avanzados

1. En el módulo de perfil CDN, haga clic en el botón **Administrar** .

    ![Módulo de perfil CDN botón Administrar](./media/cdn-advanced-http-reports/cdn-manage-btn.png)

    Se abre el portal de administración de CDN.

2. Desplace el puntero sobre la pestaña **análisis** , a continuación, desplace el puntero sobre el flotante de **Informes de HTTP avanzados** .  Haga clic en la **plataforma de gran tamaño de HTTP**.

    ![Portal de administración de CDN - menú de informes avanzados](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)

    Se muestran las opciones de informe.

## <a name="geography-reports-map-based"></a>Informes de geografía (basados en mapas)

Hay cinco informes que aprovechar las ventajas de un mapa para indicar las regiones desde el que se solicita el contenido. Estos informes son mapa mundial, mapa de Estados Unidos, Canadá mapa, mapa de Europa y Asia Pacífico mapa.

Cada informe basados en mapas clasifica entidades geográficas (por ejemplo, países, Estados y provincia) según el porcentaje de visitas que se ha originado de esa región. Además, se proporciona un mapa para ayudarle a visualizar las ubicaciones desde donde se solicita el contenido. Es posible hacerlo por cada región según la cantidad de demanda experimentado en esa región de códigos de color. Ligero regiones sombreadas indican petición inferior para su contenido, mientras áreas oscuras indican niveles superiores de la demanda de su contenido.

Se proporciona información detallada de tráfico y ancho de banda para cada región directamente debajo de la asignación. Esto permite ver el número total de visitas, el porcentaje de visitas, la cantidad total de los datos se transfiere (en gigabytes) y el porcentaje de datos que se transfiere para cada región. Ver una descripción de cada una de estas métricas. Por último, cuando desplace el puntero sobre una región (es decir, país, estado o provincia), el nombre y el porcentaje de visitas que ocurrieron en la región se mostrará como información sobre herramientas.

Continuación se ofrece una breve descripción de cada tipo de informe basados en mapas geográficos.

Nombre del informe | Descripción
------------|------------
Mapa del mundo | Este informe le permite ver la demanda en todo el mundo para el contenido CDN. Cada país está codificada por colores en el mapa del mundo para indicar el porcentaje de visitas que se ha originado en esa región.
Mapa de Estados Unidos | Este informe le permite ver la demanda para el contenido CDN en los Estados Unidos. Cada estado es con códigos de color en este mapa para indicar el porcentaje de visitas que se ha originado en esa región.
Mapa de Canadá | Este informe le permite ver la demanda de contenido CDN en Canadá. Cada provincia es con códigos de color en este mapa para indicar el porcentaje de visitas que se ha originado en esa región.
Mapa de Europa | Este informe le permite ver la demanda para el contenido CDN en Europa. En este mapa para indicar el porcentaje de visitas que se ha originado en esa región, es con códigos de color en cada país.
Mapa de Asia Pacífico | Este informe le permite ver la demanda para el contenido CDN en Asia. En este mapa para indicar el porcentaje de visitas que se ha originado en esa región, es con códigos de color en cada país.

## <a name="geography-reports-bar-charts"></a>Informes de geografía (gráficos de barras)

Existen dos informes adicionales que proporcionan información estadística según la ubicación geográfica, que son las ciudades de la parte superior y los países de la parte superior. Estos informes clasifican ciudades y países, respectivamente, según el número de visitas que se ha originado en esas áreas. Tras la generación de este tipo de informe, un gráfico de barras indicará los 10 principales ciudades o países que solicita contenido sobre una plataforma específica. Este gráfico de barras le permite evaluar rápidamente las regiones que generan el número más alto de solicitudes de su contenido.

El lado izquierdo del gráfico (eje y) indica que se ha producido cuántas visitas en la región especificada. Directamente debajo del gráfico (eje x), encontrará una etiqueta para cada una de las principales áreas de 10.

### <a name="using-the-bar-charts"></a>Uso de los gráficos de barras

* Si desplace el puntero sobre una barra, el nombre y el número total de visitas que ocurrieron en la región se mostrará como información sobre herramientas.
* La información sobre herramientas para el informe de ciudades de la parte superior identifica una ciudad por su nombre, el estado o provincia y la abreviatura de país.
* Si no puede determinar la ciudad o región (por ejemplo, estado o provincia) desde el que se ha originado una solicitud, le indicará que son desconocidos. Si el país es desconocido y, a continuación, dos signos de interrogación (es decir,??), se mostrará.
* Un informe puede incluir métricas para "Europa" o "Región de Asia Pacífico". Los elementos no están diseñados para proporcionar información estadística en todas las direcciones IP de las regiones. En su lugar, solo se aplican a las solicitudes que proceden de direcciones IP que se extienden en Europa o Asia Pacífico en lugar de a una determinada ciudad o país.

Se pueden visualizar los datos que se utilizan para generar el gráfico de barras debajo de ella. Encontrará el número total de visitas, el porcentaje de visitas, la cantidad de datos que se transfiere (en gigabytes) y el porcentaje de datos que se transfiere de las regiones de la parte superior a 250. Ver una descripción de cada una de estas métricas.

Se proporciona una breve descripción para ambos tipos de informes siguiente.

Nombre del informe | Descripción
------------|------------
Ciudades de la parte superior | Este informe clasifica ciudades según el número de visitas que se ha originado de esa región.
Países principales | Este informe clasifica países según el número de visitas que se ha originado de esa región.

## <a name="daily-summary"></a>Resumen diario

El informe de resumen diario le permite ver el número total de visitas y datos que se transfieren a través de una plataforma en particular diariamente. Esta información puede usarse para distinguir rápidamente los patrones de actividad CDN. Por ejemplo, este informe puede ayudarle a detectar qué días experimentados superior o inferiores de tráfico esperado.

Tras la generación de este tipo de informe, un gráfico de barras le proporcionará una indicación visual de la cantidad de demanda específica de la plataforma experimentado diariamente durante el período de tiempo cubierto por el informe. Lo hará mediante una barra para cada día en el informe. Por ejemplo, seleccionar el período de tiempo denominado "Semana pasada" generará un gráfico de barras con barras de siete. Cada barra indica el número total de visitas experimentados en ese momento.

El lado izquierdo del gráfico (eje y) indica que se ha producido cuántas visitas en la fecha especificada. Directamente debajo del gráfico (eje x), encontrará una etiqueta que indica la fecha (formato: dd-MM-aaaa) para cada día a incluir en el informe.

> [AZURE.TIP] Si desplace el puntero sobre una barra, el número total de visitas que ocurrieron en esa fecha se mostrará como información sobre herramientas.

Se pueden visualizar los datos que se utilizan para generar el gráfico de barras debajo de ella. Encontrará el número total de visitas y la cantidad de datos que se transfieren (en gigabytes) para cada día cubierto por el informe.

## <a name="by-hour"></a>Por hora

El informe por hora le permite ver el número total de visitas y datos que se transfieren a través de una plataforma en particular en cada hora. Esta información puede usarse para distinguir rápidamente los patrones de actividad CDN. Por ejemplo, este informe puede ayudarle a detectar los períodos de tiempo durante el día que experimentar un valor superior o inferior a tráfico esperado.

Tras la generación de este tipo de informe, un gráfico de barras le proporcionará una indicación visual de la cantidad de demanda específica de la plataforma experimentado cada hora durante el período de tiempo cubierto por el informe. Lo hará mediante una barra para cada hora cubierto por el informe. Por ejemplo, la selección de 24 horas período de tiempo genera un gráfico de barras con barras de 24. Cada barra indica el número total de visitas experimentados durante la hora.

El lado izquierdo del gráfico (eje y) indica cuántas visitas ha producido en la hora especificada. Directamente debajo del gráfico (eje x), encontrará una etiqueta que indica la fecha y hora (formato: dd-MM-aaaa hh: mm) para cada hora incluido en el informe. Notifique el tiempo con formato de 24 horas y se especifica mediante la zona horaria de UTC/GMT.

> [AZURE.TIP] Si desplace el puntero sobre una barra, se mostrará el número total de visitas que ocurrieron durante la hora como información sobre herramientas.

Se pueden visualizar los datos que se utilizan para generar el gráfico de barras debajo de ella. Encontrará el número total de visitas y la cantidad de datos que se transfieren (en gigabytes) para cada hora cubierto por el informe.

## <a name="by-file"></a>Por archivo

El informe por archivo le permite ver la cantidad de demanda y el tráfico contraídos sobre una plataforma en particular de los activos más solicitadas. Tras la generación de este tipo de informe, se generará un gráfico de barras en los activos más solicitados 10 principales durante el período de tiempo especificado.

> [AZURE.NOTE] Para el propósito de este informe, las direcciones URL de borde CNAME se convierten en sus direcciones URL de CDN equivalentes. Esto permite un recuento del número total de visitas asociada a un activo, independientemente de la CDN o borde URL CNAME usada para solicitar precisa.

El lado izquierdo del gráfico (eje y) indica el número de solicitudes de cada activo en el período de tiempo especificado. Directamente debajo del gráfico (eje x), encontrará una etiqueta que indica el nombre de archivo para cada uno de los activos solicitados 10 superiores.

Se pueden visualizar los datos que se utilizan para generar el gráfico de barras debajo de ella. Encontrará la información siguiente para cada uno de los activos solicitados 250 superiores: ruta de acceso relativa, el número total de visitas, el porcentaje de visitas, la cantidad de datos que se transfieren (en gigabytes) y, a continuación, transferir el porcentaje de datos.

## <a name="by-file-detail"></a>Por archivo detalle

El informe de detalle por archivo le permite ver la cantidad de demanda y el tráfico contraídos sobre una plataforma en particular de un activo específico. En la parte superior de este informe es la opción detalles para el archivo. Esta opción proporciona una lista de los activos más solicitadas en la plataforma seleccionada. Con el fin de generar un informe de detalle por archivo, debe seleccione activo que desee en la opción detalles para el archivo. Después de esto, un gráfico de barras indicará la cantidad de demanda diaria que genera durante el período de tiempo especificado.

El lado izquierdo del gráfico (eje y) indica el número total de solicitudes que experimentado un activo en un momento determinado. Directamente debajo del gráfico (eje x), encontrará una etiqueta que indica la fecha (formato: dd-MM-aaaa) en qué CDN se ha notificado petición para el activo.

Se pueden visualizar los datos que se utilizan para generar el gráfico de barras debajo de ella. Encontrará el número total de visitas y la cantidad de datos que se transfieren (en gigabytes) para cada día cubierto por el informe.

## <a name="by-file-type"></a>Por tipo de archivo

El informe por tipo de archivo le permite ver la cantidad de demanda y el tráfico contraídos por tipo de archivo. Tras la generación de este tipo de informe, un gráfico de anillos indica el porcentaje de visitas generadas por los tipos de archivo de 10 principales.

> [AZURE.TIP] Si coloca sobre un sector en el gráfico de anillos, tipo del medio de Internet de que el tipo de archivo se mostrará como información sobre herramientas.

Se pueden visualizar los datos que se utilizan para generar el gráfico de anillos debajo de ella. Encontrará el tipo de medio de extensión o Internet de nombre de archivo, el número total de visitas, el porcentaje de visitas, la cantidad de datos que se transfiere (en gigabytes) y el porcentaje de datos que se transfiere para cada uno de los tipos de archivo de la parte superior a 250.

## <a name="by-directory"></a>Directorio

El informe por directorio le permite ver la cantidad de demanda y el tráfico contraídos sobre una plataforma en particular para el contenido de un directorio específico. Tras la generación de este tipo de informe, un gráfico de barras indica el número total de visitas generadas por el contenido en los directorios de 10 principales.

### <a name="using-the-bar-chart"></a>Usar el gráfico de barras

* Mantenga el mouse sobre una barra para ver la ruta de acceso relativa para el directorio correspondiente.
* El contenido almacenado en una subcarpeta de un directorio no contar al calcular demanda por directorio. Este cálculo se basa únicamente en el número de solicitudes generadas para el contenido almacenado en el directorio real.
* Para el propósito de este informe, las direcciones URL de borde CNAME se convierten en sus direcciones URL de CDN equivalentes. Esto permite un recuento preciso para todas las estadísticas asociada a un activo, independientemente de la CDN o borde URL CNAME usada para solicitar.

El lado izquierdo del gráfico (eje y) indica el número total de solicitudes de contenido almacenado en los directorios de 10 mejores. Cada barra del gráfico representa un directorio. Utilizar el esquema de codificación en colores para que coincida con una barra en un directorio enumerado en la sección superior 250 completa directorios.

Se pueden visualizar los datos que se utilizan para generar el gráfico de barras debajo de ella. Encontrará la información siguiente para cada uno de los directorios de la parte superior a 250: ruta de acceso relativa, el número total de visitas, el porcentaje de visitas, la cantidad de datos que se transfieren (en gigabytes) y, a continuación, transferir el porcentaje de datos.

## <a name="by-browser"></a>Explorador

El informe por explorador le permite ver qué exploradores se utilizaron para solicitar el contenido. Tras la generación de este tipo de informe, un gráfico circular indica el porcentaje de solicitudes controlada los 10 exploradores.

### <a name="using-the-pie-chart"></a>Usar el gráfico circular

* Mantenga el mouse sobre un sector del gráfico circular para ver el nombre y la versión del explorador.
* Para el propósito de este informe, cada combinación único y versión del explorador se considera un explorador diferente.
* La segmentación de datos denominada "Otros" indica el porcentaje de las solicitudes controladas por todos los otros exploradores y las versiones.

Se pueden visualizar los datos que se utilizan para generar el gráfico circular debajo de ella. Encontrará el número de tipo y versión del explorador, el número total de visitas y el porcentaje de visitas para cada uno de los exploradores de la parte superior a 250.

## <a name="by-referrer"></a>Por referencia

El informe por Referrer le permite ver los remitentes principales al contenido de la plataforma seleccionada. Una referencia indica el nombre de host desde el que se ha generado una solicitud. Tras la generación de este tipo de informe, un gráfico de barras indica la cantidad de demanda (es decir, visitas) generado por los remitentes de 10 principales.

El lado izquierdo del gráfico (eje y) indica el número total de solicitudes que experimentado un activo para cada sitio de referencia. Cada barra del gráfico representa una referencia. Utilizar el esquema de codificación en colores para que coincida con una barra de un sitio de referencia aparece en la sección superior 250 Referrer.

Se pueden visualizar los datos que se utilizan para generar el gráfico de barras debajo de ella. Encontrará la dirección URL, el número total de visitas y el porcentaje de visitas generadas de cada uno de los remitentes de 250 principales.

## <a name="by-download"></a>Descargar

El informe por descargar le permite analizar los patrones de descarga para el contenido más solicitado. La parte superior del informe contiene un gráfico de barras que compara ha intentado descargas con descargas completadas para los activos solicitados 10 superiores. Cada barra está codificada por color según si se trata de una intento de descarga (azul) o una descarga completada (verde).

> [AZURE.NOTE] Para el propósito de este informe, las direcciones URL de borde CNAME se convierten en sus direcciones URL de CDN equivalentes. Esto permite un recuento preciso para todas las estadísticas asociada a un activo, independientemente de la CDN o borde URL CNAME usada para solicitar.

El lado izquierdo del gráfico (eje y) indica el nombre de archivo para cada uno de los activos solicitados 10 superiores. Directamente debajo del gráfico (eje x), encontrará las etiquetas que indican el número total de descargas intentado o completado.

Directamente debajo del gráfico de barras, la información siguiente se mostrarán los activos solicitado 250 superior: ruta de acceso relativa (incluido el nombre de archivo), el número de veces que lo descargó hasta su finalización, el número de veces que se ha solicitado y el porcentaje de solicitudes que ha generado una descarga completa.

> [AZURE.TIP] No se le informará de nuestra CDN por un cliente HTTP (es decir, explorador) cuando un activo se ha descargado por completo. Como resultado, hay que calcular si un activo se ha descargado completamente según los códigos de estado y el intervalo de bytes solicitudes. Lo primero que se busque ese cálculo es si da un código de 200 estado Aceptar la solicitud. Si es así, a continuación, observamos solicitudes de intervalo de bytes para asegurarse de que cubren el activo completo. Por último, se compara la cantidad de datos que se transfieren al tamaño del activo solicitado. Si los datos que se transfieren están igual o mayor que el tamaño de archivo y las solicitudes de intervalo de bytes son adecuadas para ese activo, el posicionamiento se contará como una descarga completa.
>
>Debido a la naturaleza interpretada de este informe, debe tener en cuenta los siguientes puntos que pueden alterar la coherencia y la precisión de este informe.
>
>* Patrones de tráfico no se puede predecir con precisión cuando agentes de usuario tienen un comportamiento diferente. Esto puede producir resultados de descarga completada que sean superiores a 100%.
>* Los activos que aprovechar las ventajas de la descarga de HTTP no pueden representarse con precisión este informe. Esto es debido a los usuarios que desean posiciones diferentes en un vídeo.

## <a name="by-404-errors"></a>Errores 404

El informe de errores por 404 le permite identificar el tipo de contenido que genera el mayor número de códigos de estado 404 no encontrado. La parte superior del informe contiene un gráfico de barras para los activos de 10 principales que se devolvió un código de estado 404 no encontrado. Este gráfico de barras compara el número total de solicitudes de solicitudes que ha generado un código de estado no encontrado 404 para dichos activos. Cada barra está codificada por color. Se usa una barra amarilla para indicar que la solicitud dio como resultado un código de estado 404 no encontrado. Se usa una barra de color rojo para indicar el número total de solicitudes de activo.

> [AZURE.NOTE] Para el propósito de este informe, tenga en cuenta lo siguiente:
>
>* Una visita representa cualquier solicitud de un activo, independientemente del código de estado.
>* Borde CNAME URL se convierten en sus direcciones URL de CDN equivalentes. Esto permite un recuento preciso para todas las estadísticas asociada a un activo, independientemente de la CDN o borde URL CNAME usada para solicitar.

El lado izquierdo del gráfico (eje y) indica el nombre de archivo para cada uno de los activos solicitados 10 principales que ha generado un código de estado 404 no encontrado. Directamente debajo del gráfico (eje x), encontrará las etiquetas que indican el número total de solicitudes y el número de solicitudes que ha generado un código de estado 404 no encontrado.

Directamente debajo del gráfico de barras, la información siguiente se mostrarán los activos solicitado 250 superior: ruta de acceso relativa (incluido el nombre de archivo), el número de solicitudes que ha generado un código de estado 404 no encontrado, el número total de veces que ha solicitado el activo y el porcentaje de solicitudes que ha generado un código de estado 404 no encontrado.

## <a name="see-also"></a>Vea también
* [Información general de Azure CDN](cdn-overview.md)
* [Estadísticas en tiempo real en Microsoft Azure CDN](cdn-real-time-stats.md)
* [Reemplazar el comportamiento predeterminado de HTTP mediante el motor de reglas](cdn-rules-engine.md)
* [Analizar el rendimiento de borde](cdn-edge-performance.md)
