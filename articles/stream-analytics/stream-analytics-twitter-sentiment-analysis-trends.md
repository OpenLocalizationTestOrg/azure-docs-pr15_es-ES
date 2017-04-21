<properties
    pageTitle="Análisis de opinión de Twitter en tiempo real con el análisis de secuencia | Microsoft Azure"
    description="Obtenga información sobre cómo usar el análisis de secuencia de análisis de opinión de Twitter en tiempo real. Instrucciones paso a paso de generación de eventos a los datos en un panel en vivo."
    keywords="análisis de tendencias de twitter en tiempo real, análisis de opinión, análisis de medios sociales, ejemplo de análisis de tendencia."
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="social-media-analysis-real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Análisis de medios sociales: análisis de opinión de Twitter en tiempo real en el análisis de secuencia de Azure

Aprenda a crear una solución de análisis de opinión para análisis de medios sociales poniendo eventos de Twitter en tiempo real en Azure evento Hubs. Deberá escribir una consulta de análisis de secuencia de Azure para analizar los datos. Deberá almacenar los resultados para un examen posterior o use un panel y [Power BI](https://powerbi.com/) para ofrecer perspectivas en tiempo real.

Herramientas de análisis de redes sociales a las organizaciones comprender tendencias temas, es decir, temas y actitudes que tienen un gran volumen de publicaciones en redes sociales. Análisis de opinión, que también denominada *minería de opinión*, usa herramientas de análisis de redes sociales para determinar actitudes de un producto, idea y así sucesivamente. Análisis de tendencias de Twitter en tiempo real es un buen ejemplo porque el modelo de suscripción hashtag le permite escuchar palabras clave específicas y desarrollar análisis de opinión en la fuente.

## <a name="scenario-sentiment-analysis-in-real-time"></a>Escenario: Análisis de opinión en tiempo real

Una compañía que tiene un sitio Web de medios le interesa recibir una ventaja sobre sus competidores gracias a contenido de sitio que es relevante inmediatamente a sus lectores. La empresa utiliza análisis de medios sociales sobre temas que son relevantes para los lectores realizando análisis de opinión en tiempo real de los datos de Twitter. Más concretamente, para identificar temas tendencias en tiempo real en Twitter, la empresa necesita análisis en tiempo real sobre el volumen de tweet y la opinión para temas clave. Por lo tanto, básicamente, la necesidad es un motor de análisis de análisis de opinión que se basa en esta fuente de medios sociales.

## <a name="prerequisites"></a>Requisitos previos
-   Cuenta de Twitter [token de acceso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
-   [TwitterClient.zip](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip) desde el centro de descarga de Microsoft
-   Opcional: Código cliente twitter desde [GitHub](https://aka.ms/azure-stream-analytics-twitterclient)

## <a name="create-an-event-hub-input-and-a-consumer-group"></a>Crear un grupo de consumidor y un concentrador de eventos de entrada

La aplicación de ejemplo generará eventos e insertarlos a una instancia de evento Hubs (un concentrador de evento, abreviado). Hubs de evento de Bus de servicio son el método preferido de recopilación de eventos para los análisis de secuencia. Consulte la documentación de Hubs de evento en la [documentación de Bus de servicio](/documentation/services/service-bus/).

Use los siguientes pasos para crear un concentrador de evento.

1.  En el portal de Azure, haga clic en **nuevo** > **Servicios de aplicación** > **BUS de servicio** > **Concentrador de evento** > **Crear rápida**y proporcione un nombre, la región y el espacio de nombres nuevo o existente.  
2.  Como práctica recomendada, debe leer cada trabajo de análisis de secuencia de un solo grupo consumidor de evento Hubs. Le guiaremos a través del proceso de creación de un grupo de consumidor más adelante. Puede obtener más información sobre los grupos de consumidores en [Introducción a Azure evento Hubs](https://msdn.microsoft.com/library/azure/dn836025.aspx). Para crear un grupo de consumidores, vaya al concentrador de eventos recién creado, haga clic en la ficha **Consumidor grupos** , haga clic en **crear** en la parte inferior de la página y, a continuación, proporcione un nombre para el grupo de consumidores.
3.  Para conceder acceso al concentrador de evento, debemos crear una directiva de acceso compartido. Haga clic en la ficha **Configurar** de su centro de evento.
4.  En **Directivas de acceso compartidos**, cree una nueva directiva con **Administrar** permisos.

    ![Compartir directivas de acceso, donde puede crear una directiva con administrar permisos.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)

5.  Haga clic en **Guardar** en la parte inferior de la página.
6.  Vaya a **panel**, haga clic en **Información de conexión** en la parte inferior de la página y, a continuación, copie y guarde la información de conexión. (Utilice el icono de copia que aparece en el icono de búsqueda).

## <a name="configure-and-start-the-twitter-client-application"></a>Configurar e iniciar la aplicación de cliente de Twitter

Proporcionamos una aplicación de cliente que se conecte a los datos de Twitter mediante [Las API de transmisión de Twitter](https://dev.twitter.com/streaming/overview) para recopilar eventos Tweet sobre un conjunto de temas con parámetros. La herramienta de código abierto [Sentiment140](http://help.sentiment140.com/) se usa para asignar el valor de opinión a cada tweet.

- 0 = negativo
- 2 = neutral
- 4 = positivo

A continuación, eventos Tweet se insertan en el hub de evento.  

Siga estos pasos para configurar la aplicación:

1.  [Descargar la solución TwitterClient](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip).
2.  Abra TwitterClient.exe.config y reemplace oauth_consumer_key, oauth_consumer_secret, oauth_token y oauth_token_secret con tokens de Twitter que tienen los valores.  

    [Pasos para generar un token de acceso OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

    Tenga en cuenta que debe realizar una aplicación vacía para generar un token.  
3.  Reemplace los valores EventHubConnectionString y EventHubName de TwitterClient.exe.config por la cadena de conexión y el nombre de su centro de evento. La cadena de conexión que copió anteriormente proporciona la cadena de conexión y el nombre de su centro de evento, así que asegúrese de separarlos y poner cada una en el campo correcto. Por ejemplo, considere la cadena de conexión siguientes:

        Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub

    El archivo TwitterClient.exe.config debe contener la configuración como en el ejemplo siguiente:

        add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
        add key="EventHubName" value="yourhub"

    Es importante que tenga en cuenta que el texto "EntityPath =" significa __no__ aparecen en el valor de EventHubName.

4.  *Opcional:* Ajustar las palabras clave para buscar.  De forma predeterminada, esta aplicación busca "Azure, Skype, XBox, Microsoft, Seattle".  Puede ajustar los valores de **twitter_keywords** en TwitterClient.exe.config, si lo desea.
5.  Ejecute TwitterClient.exe para iniciar la aplicación. Verá los eventos Tweet con los valores de **registroen**, **tema**y **SentimentScore** enviados a su centro de evento.

    ![Análisis de opinión: SentimentScore valores que se envían a un concentrador de evento.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## <a name="create-a-stream-analytics-job"></a>Crear un análisis de flujo de trabajo

Ahora que eventos Tweet son transmisión en tiempo real desde Twitter, podemos configurar un trabajo de análisis de secuencia para analizar estos eventos en tiempo real.

### <a name="provision-a-stream-analytics-job"></a>Aprovisionar a un trabajo de análisis de secuencia

1.  En el [portal de Azure](https://manage.windowsazure.com/), haga clic en **nuevo** > **DATA SERVICES** > **Análisis de secuencia** > **Crear rápido**.
2.  Especifique los valores siguientes y, a continuación, haga clic en **Crear trabajo de análisis de secuencia**:

    * **Nombre del trabajo**: escriba un nombre de trabajo.
    * **Región**: seleccione la región donde desea ejecutar el trabajo. Considere la posibilidad de realizar el trabajo y el concentrador de eventos de la misma región para lograr un mejor rendimiento y asegurarse de no se pueden pagar transferir datos entre regiones.
    * **Cuenta de almacenamiento**: elija la cuenta de almacenamiento de Azure que desea usar para almacenar los datos del seguimiento de todos los trabajos de análisis de flujo que se ejecutan en esta área. Tiene la opción para elegir una cuenta de almacenamiento existente o cree uno nuevo.

3.  En el panel izquierdo para enumerar los trabajos de análisis de secuencia, haga clic en **Análisis de secuencia** .  
    ![Icono de servicio de análisis de secuencia](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)

    La nueva tarea se mostrarán con el estado **creado**. Observe que el botón **Inicio** en la parte inferior de la página está deshabilitado. Configure la entrada de trabajo, resultados y consulta antes de empezar el trabajo.


### <a name="specify-job-input"></a>Especificar la entrada de trabajo
1.  En el trabajo de análisis de secuencia, haga clic en **entradas** de la parte superior de la página y, a continuación, haga clic en **Agregar entrada**. El cuadro de diálogo que se abre le guiará a través de un número de pasos para configurar la entrada.
2.  Haga clic en **flujo de datos**y, a continuación, haga clic en el botón derecho.
3.  Haga clic en **El HUB de evento**y, a continuación, haga clic en el botón derecho.
4.  Escriba o seleccione los valores siguientes en la tercera página:

    * **ALIAS de entrada**: escriba un nombre descriptivo para este trabajo de entrada, como *TwitterStream*. Tenga en cuenta que usará este nombre en la consulta más adelante.
    **Evento concentrador**: si el concentrador de eventos que ha creado está en la misma suscripción como el trabajo de análisis de secuencia, seleccione el espacio de nombres que en el hub de evento.

        Si es su centro de evento en una suscripción diferente, haga clic en **Usar evento concentrador de otra suscripción**y, a continuación, escribir información para **Espacio de nombres de BUS de servicio**, **El nombre del concentrador de evento**, **Nombre de la directiva de evento concentrador**, **Clave de directiva de concentrador de evento**y **Evento concentrador partición recuento**.

    * **Nombre del evento concentrador**: seleccione el nombre del concentrador de evento.

    * **Nombre de la directiva de evento concentrador**: seleccione la directiva de concentrador de evento que creó anteriormente en este tutorial.

    * **Grupo de CONSUMIDORES de concentrador de evento**: escriba el nombre del grupo de consumidores que creó anteriormente en este tutorial.
5.  Haga clic en el botón derecho.
6.  Especifique los valores siguientes:

    * **Evento SERIALIZADOR formato**: JSON
    * **Codificación**: UTF8

7.  Haga clic en el botón **Comprobar** para agregar esta fuente y compruebe que el análisis de secuencia puede conectarse correctamente al concentrador de eventos.

### <a name="specify-job-query"></a>Especifique la consulta de la tarea

Análisis de secuencia es compatible con un modelo de consulta simple, descriptiva que describe las transformaciones. Para obtener más información acerca del idioma, vea la [Referencia del lenguaje de consulta de Azure secuencia de análisis](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Este tutorial le ayudará a crear y probar varias consultas sobre datos de Twitter.

#### <a name="sample-data-input"></a>Entrada de datos de ejemplo

Para validar la consulta con los datos de trabajo real, puede usar la característica de **Datos de ejemplo** para extraer los eventos de la secuencia y crear un archivo de .json de los eventos para realizar pruebas.

1.  Seleccione la entrada de concentrador de evento y, a continuación, haga clic en **Datos de ejemplo** en la parte inferior de la página.
2.  En el cuadro de diálogo que se abre, especifique una **hora de inicio** para iniciar la recopilación de datos y una **duración** de la cantidad de datos adicional consumir.
3.  Haga clic en el botón **Detalles** y, a continuación, haga clic en el vínculo **haga clic aquí** para descargar y guardar el archivo .json generado.

#### <a name="pass-through-query"></a>Consulta de paso a través
Para empezar, haremos una consulta de paso simple que todos los campos de un evento de proyectos.

1.  Haga clic en **consulta** en la parte superior de la página de trabajo de análisis de la secuencia.
2.  En el editor de código, reemplace la plantilla de consulta inicial con los siguientes elementos:

        SELECT * FROM TwitterStream

    Asegúrese de que el nombre del origen de entrada coincide con el nombre de la entrada que ha especificado una versión anterior.

3.  En el editor de consultas, haga clic en **prueba** .
4.  Vaya a su archivo de .json de ejemplo.
5.  Haga clic en el botón **Buscar** y ver los resultados por debajo de la definición de consulta.

    ![Resultados que se muestran debajo de definición de consulta](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### <a name="count-of-tweets-by-topic-tumbling-window-with-aggregation"></a>Recuento de tweets por tema: ventana de rotación con agregación

Para comparar el número de menciones entre los temas, usaremos un [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obtener el recuento de menciones por tema cada cinco segundos.

1.  Cambiar la consulta en el editor de código:

        SELECT System.Timestamp as Time, Topic, COUNT(*)
        FROM TwitterStream TIMESTAMP BY CreatedAt
        GROUP BY TUMBLINGWINDOW(s, 5), Topic

    Esta consulta utiliza la **Marca de tiempo por** palabra clave para especificar un campo de marca de tiempo en la carga que se utilizarán en el cálculo temporal. Si este campo no se ha especificado, la operación de ventanas debería realizarse usando el tiempo que ha llegado cada evento en el hub de evento.  Más información en la sección "Llegada Vs aplicación hora" de [Referencia de consulta de análisis de secuencia](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Esta consulta también tiene acceso a una marca de tiempo para el final de cada ventana mediante la propiedad **System.Timestamp** .

2.  Haga clic en **volver a ejecutar** en el editor de consultas para ver los resultados de la consulta.

#### <a name="identify-trending-topics-sliding-window"></a>Identificar los temas tendencias: ventana deslizante

Para identificar tendencias temas, veremos para temas que atraviesan un valor de umbral de menciones en un período de tiempo determinado. Para el propósito de este tutorial, le enviaremos para temas que se mencionan más de 20 veces en los últimos cinco segundos usando un [SlidingWindow](https://msdn.microsoft.com/library/azure/dn835051.aspx).

1.  Cambiar la consulta en el editor de código a: seleccione System.Timestamp como la hora, tema, recuento (*) como menciones de TwitterStream marca de tiempo por registroen grupo por SLIDINGWINDOW(s, 5), tema HAVING COUNT (*) > 20

2.  Haga clic en **volver a ejecutar** en el editor de consultas para ver los resultados de la consulta.

    ![Resultado de la consulta de ventana deslizante](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### <a name="count-of-mentions-and-sentiment-tumbling-window-with-aggregation"></a>Recuento de menciones y opinión: ventana de rotación con agregación
La consulta final que se va a probar usa **TumblingWindow** para obtener el número de la desviación estándar de la puntuación de opinión, promedio, mínimo, máximo y menciones para cada tema cada cinco segundos.

1.  Cambiar la consulta en el editor de código:

        SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),
        Max(SentimentScore), STDEV(SentimentScore)
        FROM TwitterStream TIMESTAMP BY CreatedAt
        GROUP BY TUMBLINGWINDOW(s, 5), Topic

2.  Haga clic en **volver a ejecutar** en el editor de consultas para ver los resultados de la consulta.
3.  Esta es la consulta que se utilizará para el panel.  Haga clic en **Guardar** en la parte inferior de la página.


## <a name="create-output-sink"></a>Crear receptor de salida

Ahora que hemos definido una secuencia de evento, un concentrador de eventos de entrada para recopilar eventos y una consulta para realizar una transformación sobre la secuencia, el último paso es definir un receptor de salida para el trabajo.  Pasaremos a crear los eventos agregados tweet de nuestra consulta de trabajo con el almacenamiento de blobs de Windows Azure.  También puede insertar los resultados a la base de datos de SQL Azure, almacenamiento de tablas de Azure, o evento Hubs, dependiendo de la aplicación específica necesidades.

Realice los siguientes pasos para crear un contenedor de almacenamiento de blobs, si todavía no tiene uno:

1.  Usar una cuenta de almacenamiento existente o crear una nueva cuenta de almacenamiento haciendo clic en **nuevo** > **DATA SERVICES** > **almacenamiento** > **Crear rápido**y, a continuación, siga las instrucciones de la pantalla.
2.  Seleccione la cuenta de almacenamiento, haga clic en **contenedores** en la parte superior de la página y, a continuación, haga clic en **Agregar**.
3.  Especifique un **nombre** para el contenedor y establezca su **acceso** en **Blob público**.

## <a name="specify-job-output"></a>Especificar la salida de trabajo

1.  En el trabajo de análisis de secuencia, haga clic en **Imprimir** en la parte superior de la página y, a continuación, haga clic en **Agregar resultado**. El cuadro de diálogo que se abre le guiará a través de varios pasos para configurar su resultado.
2.  Haga clic en **Almacenamiento de blobs de Windows**y, a continuación, haga clic en el botón derecho.
3.  Escriba o seleccione los valores siguientes en la tercera página:

    * **ALIAS de salida**: escriba un nombre descriptivo para este resultado de la tarea.

    * **Suscripción**: si el almacenamiento de blobs de Windows que ha creado está en la misma suscripción como el trabajo de análisis de secuencia, haga clic en **Usar la cuenta de almacenamiento de suscripción actual**. Si el almacenamiento está en una suscripción diferente, haga clic en **Usar la cuenta de almacenamiento de otra suscripción**y escribir manualmente la información de **Cuenta de almacenamiento**, la **Clave de cuenta de almacenamiento**y el **contenedor**.

    * **Cuenta de almacenamiento**: seleccione el nombre de la cuenta de almacenamiento.

    * **Contenedor**: seleccione el nombre del contenedor.

    * **PREFIJO de nombre de archivo**: escriba un prefijo de archivo para escribir los resultados de blobs.

4.  Haga clic en el botón derecho.
5.  Especifique los valores siguientes:
    * **Evento SERIALIZADOR formato**: JSON
    * **Codificación**: UTF8
6.  Haga clic en el botón **Comprobar** para agregar esta fuente y compruebe que el análisis de secuencia puede conectarse correctamente a la cuenta de almacenamiento.

## <a name="start-job"></a>Iniciar el trabajo

Porque una entrada de trabajo, la consulta y la salida han todos ha especificado, se está listos iniciar el análisis de flujo de trabajo.

1.  Desde el **panel**de trabajo, haga clic en **Iniciar** en la parte inferior de la página.
2.  En el cuadro de diálogo que se abre, haga clic en la **hora de comienzo del trabajo**y, a continuación, haga clic en el botón **Buscar** en la parte inferior del cuadro de diálogo. El estado del trabajo cambiará a **Iniciar** y poco cambiará a **ejecutando**.


## <a name="view-output-for-sentiment-analysis"></a>Resultados de la vista de análisis de opinión

Una vez que el trabajo se ejecuta y procesamiento de la secuencia de Twitter en tiempo real, elija cómo desea ver la salida para el análisis de opinión. Utilice una herramienta como [El Explorador de almacenamiento de Azure](https://azurestorageexplorer.codeplex.com/) o en [El Explorador de Azure](http://www.cerebrata.com/products/azure-explorer/introduction) para ver la salida de trabajo en tiempo real. Desde aquí, puede usar [Power BI](https://powerbi.com/) para extender su aplicación para incluir un panel personalizado como el que se muestra en la siguiente captura de pantalla.

![Análisis de medios sociales: resultados de análisis de secuencia opinión análisis (minería de opinión) en un panel de Power BI.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## <a name="get-support"></a>Obtener soporte técnico
Para obtener más ayuda, pruebe a nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)
