<properties
    pageTitle="Analizar el rendimiento de borde en Azure CDN | Microsoft Azure"
    description="Analizar el rendimiento de nodo de borde en Microsoft Azure CDN. El análisis del rendimiento de borde proporciona el uso de ancho de banda y el tráfico de información detallada para la CDN."
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

# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analizar el rendimiento de nodo de borde en Microsoft Azure CDN

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Información general
El análisis del rendimiento de borde proporciona el uso de ancho de banda y el tráfico de información detallada para la CDN. Esta información puede utilizarse para generar estadísticas de tendencias, que le permiten obtener información sobre cómo se están los activos en caché y entregar a sus clientes. A su vez, esto le permite una estrategia de formulario en cómo optimizar la entrega de contenido y determinar los problemas que deben abordar aprovechar mejor la CDN. Por tanto, no solo podrá mejorar el rendimiento de la entrega de datos, pero también podrá reducir los costos CDN.

> [AZURE.NOTE] Todos los informes usan notación UTC/GMT cuando se especifica una fecha y hora.

## <a name="reports-and-log-collection"></a>Colección de registros e informes

Deben recopilar datos de actividad CDN por el módulo de análisis del rendimiento de borde antes de que pueden generar informes en él. Este proceso se produce una vez un día y trata sobre la actividad que se ha producido durante el día anterior. Esto significa que las estadísticas del informe representan una muestra de las estadísticas del día en el momento en se ha procesado y no necesariamente contienen el conjunto completo de datos para el día actual. La función principal de estos informes es evaluar el rendimiento. No se recomienda para fines de facturación o estadísticas numéricas exactas.

> [AZURE.NOTE] Los datos sin formato desde el que se generan informes de análisis de rendimiento del borde están disponibles al menos 90 días.

## <a name="dashboard"></a>Panel

El panel de análisis de rendimiento de borde realiza un seguimiento de tráfico CDN histórico y actual a través de un gráfico y estadísticas. Use este panel para detectar tendencias recientes y a largo plazo en el rendimiento del tráfico CDN para su cuenta.

Este panel consta de:

* Un gráfico interactivo que permite la visualización de estadísticas y tendencias.
* Una escala de tiempo que le ofrece una idea de patrones a largo plazo para tendencias y métricas clave.
* Métricas e información estadística sobre cómo nuestra red CDN mejora el tráfico del sitio con relación al rendimiento general, el uso y la eficiencia.

### <a name="accessing-the-edge-performance-dashboard"></a>Acceder al panel de rendimiento de borde

1. En el módulo de perfil CDN, haga clic en el botón **Administrar** .

    ![Módulo de perfil CDN botón Administrar](./media/cdn-edge-performance/cdn-manage-btn.png)

    Se abre el portal de administración de CDN.

2. Desplace el puntero sobre la pestaña **análisis** , a continuación, mantenga el mouse sobre el elemento de **Análisis del rendimiento de borde** .  Haga clic en el **panel**.

    Se muestra el panel de análisis de nodo de borde.

### <a name="chart"></a>Gráfico

El panel contiene un gráfico que realiza un seguimiento de una métrica durante el período de tiempo seleccionado en la escala de tiempo que aparece justo debajo de ella.  Se muestra una escala de tiempo que gráficos hasta la última arriba dos años de actividad CDN directamente debajo del gráfico.

#### <a name="using-the-chart"></a>Usar el gráfico

* De forma predeterminada, se pueden representar la tasa de eficiencia de caché de los últimos 30 días.
* Se genera este gráfico de datos intercaladas diariamente.
* Mantener el mouse sobre un día en el gráfico de líneas indicará una fecha y el valor de la métrica en esa fecha.
* Haga clic en Resaltar los fines de semana para activar o desactivar una superposición de verticales barras plateadas que representan los fines de semana en el gráfico. Este tipo de gráfico superpuesto es útil para identificar patrones de tráfico sobre los fines de semana.
* Haga clic en ver una hace del año para activar o desactivar una superposición de la actividad del año anterior en el mismo período de tiempo en el gráfico. Este tipo de comparación proporciona una perspectiva de los patrones de uso CDN a largo plazo. La esquina superior derecha del gráfico contiene una leyenda que indica el código de color para cada gráfico de líneas.

#### <a name="updating-the-chart"></a>Actualizar el gráfico

* Intervalo de tiempo: Realice uno de estos procedimientos:
    * Seleccione la región que desee en la escala de tiempo. El gráfico se actualizará con los datos que se corresponda con el período de tiempo seleccionado.
    * Haga doble clic en el gráfico para mostrar todos los datos históricos disponibles hasta un máximo de dos años.
* Métrica: Haga clic en el icono de gráfico que aparece junto a la métrica deseada. El gráfico y la escala de tiempo se actualizará con los datos de la métrica correspondiente.


### <a name="key-metrics-and-statistics"></a>Estadísticas y mediciones clave

#### <a name="efficiency-metrics"></a>Métricas de eficacia

El propósito de estas métricas es ver si se puede mejorar la eficacia de la memoria caché. Son las principales ventajas que se deriva de la eficacia de la memoria caché:

* Reducción de la carga en el servidor de origen que se puede producir:
    * Mejor rendimiento del servidor web.
    * Reducir los costos operativos.
* Mejorado aceleración de la entrega de datos ya que se servirá más solicitudes directamente desde la CDN.

Campo | Descripción
------|------------
Eficiencia de caché | Indica el porcentaje de datos que se transfieren y que se ha servido de caché. Esta métrica medidas cuando una versión en caché del contenido solicitado se sirve directamente desde la CDN (servidores perimetrales) a solicitantes (por ejemplo, un explorador web)
Tasa de visitas | Indica el porcentaje de solicitudes que han sido atendidas desde la caché. Esta métrica medidas cuando una versión en caché del contenido solicitado se sirve directamente desde la CDN (servidores perimetrales) a solicitantes (por ejemplo, un explorador web).
% de Bytes remotos - ninguna configuración de caché | Indica el porcentaje de tráfico que se ha servido de servidores de origen a CDN (servidores perimetrales) que no se almacena en caché como resultado de la función de caché de derivación (motor de reglas de HTTP).
% de Bytes remotos - caché expirados | Indica el porcentaje de tráfico que se ha servido de servidores de origen a la CDN (servidores perimetrales) como resultado de la validación de contenido obsoleto.

#### <a name="usage-metrics"></a>Métrica de uso

El propósito de estas métricas es proporcionar información sobre las medidas de reducción de costos siguientes:

* Reducir los costos operativos mediante la CDN.
* Reducción de gastos CDN a través de la eficacia de la memoria caché y la compresión.

> [AZURE.NOTE] Números de volumen de tráfico representan el tráfico que se usa en los cálculos de proporciones y porcentajes y a continuación, puede mostrar solo una parte del tráfico total para los clientes de gran volumen.

Campo | Descripción
------|------------
Promedio de Bytes fuera | Indica el promedio de bytes que se transfieren por cada solicitud atendida desde la CDN (servidores perimetrales) a la mando (por ejemplo, un explorador web).
Ningún tipo de Byte de configuración de caché | Indica el porcentaje de tráfico atendido desde la CDN (servidores perimetrales) a mando (por ejemplo, un explorador web) que no se almacena en caché debido a la característica de caché de derivación.
Tasa de bytes comprimidos | Indica el porcentaje de tráfico enviado desde la CDN (servidores perimetrales) a solicitantes (por ejemplo, un explorador web) en un formato comprimido.
Bytes de salida | Indica la cantidad de datos, en bytes, que se han entregado desde la CDN (servidores perimetrales) a la mando (por ejemplo, un explorador web).  
Bytes en | Indica la cantidad de datos, en bytes, que se envía desde solicitantes (por ejemplo, un explorador web) a la CDN (servidores perimetrales).
Bytes remota | Indica la cantidad de datos, en bytes, enviados desde el cliente y CDN servidores de origen a la CDN (servidores perimetrales).

#### <a name="performance-metrics"></a>Indicadores de rendimiento

El propósito de estas métricas es realizar un seguimiento del rendimiento general de CDN para el tráfico.

Campo | Descripción
------|------------
Velocidad de transferencia | Indica la velocidad media a la que se transfiere contenido desde la CDN a un mando.
Duración | Indica el promedio de tiempo en milisegundos, realizó para ofrecer un activo a un mando (por ejemplo, un explorador web).
Tasa de solicitud comprimido | Indica el porcentaje de visitas que se han entregado desde la CDN (servidores perimetrales) a la mando (por ejemplo, un explorador web) en un formato comprimido.
Tasa de Error 4xx | Indica el porcentaje de visitas que generan un código de estado 4xx.
Tasa de Error 5xx | Indica el porcentaje de visitas que generan un código de estado 5xx.
Visitas | Indica el número de solicitudes de contenido CDN.

#### <a name="secure-traffic-metrics"></a>Proteger el tráfico métricas

El propósito de estas métricas es un seguimiento del rendimiento de CDN para el tráfico HTTPS.

Campo | Descripción
------|------------
Proteger la eficiencia de caché | Indica el porcentaje de datos que se transfieren las solicitudes de HTTPS que han sido atendidas desde la caché. Esta métrica medidas cuando una versión en caché del contenido solicitado se sirve directamente desde la CDN (servidores perimetrales) a solicitantes (por ejemplo, un explorador web) a través de HTTPS.
Tasa de transferencia segura | Indica la velocidad media a la que se transfiere contenido desde la CDN (servidores perimetrales) a solicitantes (por ejemplo, servidores web) a través de HTTPS.
Duración promedio de seguro | Indica el promedio de tiempo en milisegundos, realizó para ofrecer un activo a un mando (por ejemplo, un explorador web) a través de HTTPS.
Visitas seguros | Indica el número de solicitudes HTTPS contenido CDN.
Proteger Bytes de salida | Indica la cantidad de tráfico HTTPS, en bytes, que se han entregado desde la CDN (servidores perimetrales) a la mando (por ejemplo, un explorador web).

## <a name="reports"></a>Informes

Cada informe en este módulo contiene un gráfico y estadísticas sobre el uso de ancho de banda y el tráfico para distintos tipos de métricas (por ejemplo, códigos de estado HTTP, códigos de estado de caché, solicitar dirección URL, etcetera.). Esta información puede utilizarse para profundizar más en el contenido que proporciona a los clientes y para ajustar el comportamiento de CDN para mejorar el rendimiento de la entrega de datos.

### <a name="accessing-the-edge-performance-reports"></a>Obtener acceso a los informes de rendimiento de borde

1. En el módulo de perfil CDN, haga clic en el botón **Administrar** .

    ![Módulo de perfil CDN botón Administrar](./media/cdn-edge-performance/cdn-manage-btn.png)

    Se abre el portal de administración de CDN.

2. Desplace el puntero sobre la pestaña **análisis** , a continuación, mantenga el mouse sobre el elemento de **Análisis del rendimiento de borde** .  Haga clic en **objeto grande de HTTP**.

    Se muestra la pantalla de informes de análisis de nodo de borde.

Informe | Descripción
-------|------------
Resumen diario | Le permite ver tendencias diarias de tráfico durante un período de tiempo especificado. Este gráfico cada barra representa una fecha determinada. El tamaño de la barra indica el número total de visitas que ocurrieron en esa fecha.
Resumen por hora | Le permite ver cada hora tendencias de tráfico durante un período de tiempo especificado. Cada barra en este gráfico representa una sola hora en una fecha determinada. El tamaño de la barra indica el número total de visitas que ocurrieron durante la hora.
Protocolos | Muestra el desglose de tráfico entre los protocolos HTTP y HTTPS. Gráfico de anillos indica el porcentaje de visitas que se ha producido para cada tipo de protocolo.
Métodos HTTP | Le permite hacerse una idea rápida de HTTP que están usando los métodos para solicitar los datos. Normalmente, los métodos de solicitud HTTP más comunes son GET, cabeza y publicación. Gráfico de anillos indica el porcentaje de visitas que se ha producido para cada tipo de método de solicitud HTTP.
Direcciones URL | Contiene un gráfico que muestra las direcciones URL solicitadas 10 superiores. Se muestra una barra para cada dirección URL. El alto de la barra indica cuántas visitas ha generado la dirección URL en particular en el intervalo de tiempo cubierto por el informe. Estadísticas de los primeros 100 solicitan que se muestran las direcciones URL directamente debajo de este gráfico.
CNAME | Contiene un gráfico que muestra la parte superior span 10 CNAME usada para solicitar activos durante el tiempo de un informe. Estadísticas de los primeros 100 solicitan que CNAME aparecen directamente debajo de este gráfico.
Orígenes | Contiene un gráfico que muestra la CDN 10 superior o servidores de origen de cliente desde el que se han solicitado activos durante un período de tiempo especificado. Estadísticas de los primeros 100 solicitan CDN o cliente de los servidores de origen se muestran directamente debajo de este gráfico. Los servidores de origen del cliente se identifican por el nombre definido en la opción de nombre de directorio.
Geo POP | Muestra la cantidad de tráfico de su se ha enrutado a través de una determinado punto de presencia (POP). La abreviatura de tres letras representa un POP en nuestra red CDN.
Clientes | Contiene un gráfico que muestra a los 10 clientes principales que solicita activos durante un período de tiempo especificado. Para el propósito de este informe, todas las solicitudes que proceden de la misma dirección IP se consideran del mismo cliente. Se muestran las estadísticas de los 100 clientes superiores directamente debajo de este gráfico. Este informe es útil para determinar los patrones de actividad de descarga para los clientes superiores.
Estados de caché | Proporciona un desglose detallado de comportamiento de la caché, que puede revelar enfoques para mejorar la experiencia global del usuario final. Dado que el mejor rendimiento procede de caché, puede optimizar la velocidad de entrega datos por minimizar errores de caché y expiración de la caché.
NINGUNO de detalles | Contiene un gráfico que muestra las 10 URL superiores para activos que no se ha activado actualización de contenido de la caché durante un período de tiempo especificado. Se muestran las estadísticas de la URL de 100 superiores para estos tipos de activos directamente debajo de este gráfico.
Detalles CONFIG_NOCACHE | Contiene un gráfico que muestra las direcciones URL de 10 principales para los activos que no se encontraban en caché debido a la configuración del cliente CDN. Estos tipos de activos se sirven directamente desde el servidor de origen. Se muestran las estadísticas de la URL de 100 superiores para estos tipos de activos directamente debajo de este gráfico.
Detalles ALMACENABLE | Contiene un gráfico que muestra las 10 URL superiores para los activos que no se pueden almacenar debido a los datos de encabezado de la solicitud. Se muestran las estadísticas de la URL de 100 superiores para estos tipos de activos directamente debajo de este gráfico.
Detalles TCP_HIT | Contiene un gráfico que muestra las 10 URL superiores para los activos que se proporcionan inmediatamente de caché. Se muestran las estadísticas de la URL de 100 superiores para estos tipos de activos directamente debajo de este gráfico.
Detalles TCP_MISS | Contiene un gráfico que muestra las 10 URL superiores para activos con un estado de caché de TCP_MISS. Se muestran las estadísticas de la URL de 100 superiores para estos tipos de activos directamente debajo de este gráfico.
Detalles TCP_EXPIRED_HIT | Contiene un gráfico que muestra las 10 URL superiores para activos obsoletos que han sido atendidas directamente desde el POP. Se muestran las estadísticas de la URL de 100 superiores para estos tipos de activos directamente debajo de este gráfico.
Detalles TCP_EXPIRED_MISS | Contiene un gráfico que muestra las 10 URL superiores para activos desfasados para que una nueva versión tenía recuperar desde el servidor de origen. Se muestran las estadísticas de la URL de 100 superiores para estos tipos de activos directamente debajo de este gráfico.
Detalles TCP_CLIENT_REFRESH_MISS | Contiene un gráfico de barras que muestra las direcciones URL de 10 mejores para activos se recuperaron de un servidor de origen debido a una solicitud de caché no desde el cliente. Se muestran las estadísticas de la URL de 100 superiores para estos tipos de solicitudes directamente debajo de este gráfico.
Tipos de solicitud de cliente | Indica el tipo de solicitudes realizados por los clientes HTTP (por ejemplo, los exploradores). Este informe incluye un gráfico de anillos que le ofrece una idea de cómo se administran las convocatorias. Información de tráfico y ancho de banda para cada tipo de solicitud se muestra debajo del gráfico.
Agente de usuario | Contiene un gráfico de barras que muestra los agentes de 10 usuario principales para solicitar el contenido a través de nuestra CDN. Normalmente, un agente de usuario es un explorador web, el Reproductor multimedia o un explorador del teléfono móvil. Se muestran las estadísticas de los agentes de 100 usuario superior directamente debajo de este gráfico.
Orígenes de referencia | Contiene un gráfico de barras que muestra los 10 remitentes principales al contenido que se tiene acceso a través de nuestra CDN. Normalmente, un sitio de referencia es la dirección URL de la página web o un recurso que se vincula a su contenido. Debajo del gráfico se proporciona información detallada de los remitentes de los 100 mejores.
Tipos de compresión | Contiene un gráfico de anillos divide activos solicitados por si ha comprimido nuestros servidores perimetrales. El porcentaje de activos comprimidos se divide por el tipo de compresión utilizado. Debajo del gráfico se proporciona información detallada para cada tipo de compresión y estado.
Tipos de archivo | Contiene un gráfico de barras que muestra los tipos de archivo de 10 principales que han solicitado a través de nuestra CDN para su cuenta. El propósito de este informe, se define un tipo de archivo por extensión de nombre de archivo del activo y tipo de medio de Internet (por ejemplo, .html \[texto o html\], .htm \[texto o html\], .aspx \[texto o html\], etcetera.). Debajo del gráfico se proporciona información detallada de los tipos de 100 archivo superior.
Archivos únicos | Contiene un gráfico que representa el número total de activos únicos que se han solicitado en un día determinado durante un período de tiempo especificado.
Resumen de token Auth | Contiene un gráfico circular que ofrece una breve introducción sobre si solicitados activos estaban protegidos por autenticación basada en Token. Activos protegidos se muestran en el gráfico según los resultados de su intento autenticación.
Auth token denegar detalles | Contiene un gráfico de barras que le permite ver las solicitudes de 10 principales que se han denegado debido a la autenticación basada en Token.
Códigos de respuesta HTTP | Proporciona un desglose de los códigos de estado HTTP (por ejemplo, 200 Aceptar, 403 Prohibido, 404 no encontrado, etc.) que se han entregado a los clientes HTTP por nuestros servidores perimetrales. Un gráfico circular le permite evaluar rápidamente cómo se servida los activos. Datos estadísticos detallados se proporcionan para cada código de respuesta debajo del gráfico.
404 errores | Contiene un gráfico de barras que le permite ver las 10 solicitudes principales que ha generado un código de respuesta 404 no encontrado.
403 errores | Contiene un gráfico de barras que le permite ver las 10 solicitudes principales que ha generado un código de respuesta 403 Prohibido. Un código de respuesta 403 Prohibido se produce cuando una solicitud es denegada por el servidor de origen de un cliente o un servidor perimetral en nuestra POP.
Errores de 4xx | Contiene un gráfico de barras que le permite ver las 10 solicitudes principales que ha generado un código de respuesta en el rango de 400. Excluir de este informe son 403 404 códigos de respuesta de prohibido y no se encuentra. Normalmente, un código de respuesta 4xx se produce cuando se aprueba como resultado un error de cliente.
504 errores | Contiene un gráfico de barras que le permite ver las 10 solicitudes principales que ha generado un código de respuesta 504 tiempo de espera de puerta de enlace. Un código de respuesta 504 tiempo de espera de puerta de enlace se produce cuando se produce un tiempo de espera cuando intenta comunicarse con otro servidor un proxy HTTP. En el caso de nuestra CDN, un código de respuesta de tiempo de espera de puerta de enlace 504 normalmente se produce cuando un servidor perimetral no se puede establecer comunicación con un servidor de origen del cliente.
502 errores | Contiene un gráfico de barras que le permite ver las 10 solicitudes principales que ha generado un código de respuesta 502 puerta de enlace incorrecta. Un código de respuesta 502 puerta de enlace incorrecta se produce cuando se produce un error de protocolo HTTP entre un servidor y un proxy HTTP. En el caso de nuestra CDN, un código de respuesta 502 puerta de enlace incorrecta normalmente se produce cuando el servidor de origen de un cliente devuelve una respuesta no válida para un servidor perimetral. Respuesta no es válida si no se puede analizar o si está incompleto.
Errores de 5xx | Contiene un gráfico de barras que le permite ver las 10 solicitudes principales que ha generado un código de respuesta en el rango 500.  Excluir de este informe son 502 puerta de enlace incorrecta y 504 códigos de respuesta de tiempo de espera de puerta de enlace.

## <a name="see-also"></a>Vea también
* [Información general de Azure CDN](cdn-overview.md)
* [Estadísticas en tiempo real en Microsoft Azure CDN](cdn-real-time-stats.md)
* [Reemplazar el comportamiento predeterminado de HTTP mediante el motor de reglas](cdn-rules-engine.md)
* [Informes HTTP avanzada](cdn-advanced-http-reports.md)
