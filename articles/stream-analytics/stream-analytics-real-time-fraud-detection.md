<properties
    pageTitle="Análisis de secuencia: Detección de fraudes en tiempo real | Microsoft Azure"
    description="Aprenda a crear una solución de detección de fraudes en tiempo real con el análisis de secuencia. Utilizar un concentrador de evento para el procesamiento de eventos en tiempo real."
    keywords="detección de anomalías, detección de fraudes, detección de anomalías en tiempo real"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok" />



# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Empezar a usar el análisis de secuencia de Azure: detección de fraudes en tiempo real

Obtenga información sobre cómo crear una solución de llevar a cabo para detección de fraudes en tiempo real con el análisis de secuencia de Azure. Visualice eventos Azure evento Hubs, escribir consultas de análisis de secuencia para la agregación o alertas y enviar los resultados a un receptor de salida para obtener información sobre los datos con el procesamiento en tiempo real. Se explica la detección de anomalías en tiempo real de telecomunicaciones, pero la técnica de ejemplo igualmente es adecuada para otros tipos de detección de fraudes como tarjeta de crédito o identidad escenarios de robo.

Análisis de secuencia es un servicio totalmente gestionado que proporciona el procesamiento de eventos de baja latencia, altamente disponible, scalable, complejo sobre la transmisión de datos en la nube. Para obtener más información, vea [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md).


## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Escenario: Detección de fraudes SIM y de telecomunicaciones en tiempo real

Una empresa de telecomunicaciones tiene un gran volumen de datos para las llamadas entrantes. La empresa tiene los siguientes puntos de sus datos:

* Reducir los datos a un cierto y obtener información sobre el uso del cliente con el tiempo y por regiones geográficas.
* Detectar el fraude SIM (varias llamadas desde la misma identidad alrededor de la misma hora pero en ubicaciones diferentes geográfico) en tiempo real para que la compañía fácilmente responder puede notificar a los clientes o cerrando servicio.

Escenarios de Internet de cosas (IoT) canónicos tienen miles de telemetría o datos de sensores. Los clientes desean agregar los datos o recibir alertas sobre anomalías en tiempo real.

## <a name="prerequisites"></a>Requisitos previos

- Descargar [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) desde el centro de descarga de Microsoft
- Opcional: El código del generador de eventos de [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator) de fuente

## <a name="create-azure-event-hubs-input-and-consumer-group"></a>Crear grupo de entrada y los consumidores de Azure evento Hubs

La aplicación de ejemplo generará eventos e insertarlos a una instancia de Hubs de evento para el procesamiento en tiempo real. Servicio de Bus de evento Hubs son el método preferido de recopilación de eventos para los análisis de secuencia. Puede obtener más información sobre el evento Hubs en la [documentación de Bus de servicio de Azure](/documentation/services/service-bus/).

Para crear un concentrador de evento:

1.  En el [portal de Azure](https://manage.windowsazure.com/), haga clic en **nuevo** > **Servicios de aplicación** > **BUS de servicio** > **Concentrador de evento** > **Crear rápido**. Proporcione un nombre, la región y el espacio de nombres nuevo o existente para crear un nuevo concentrador de evento.  
2.  Como práctica recomendada, debe leer cada trabajo de análisis de secuencia de un grupo de consumidores de concentrador de evento único. Le guiaremos a través del proceso de creación de un grupo de consumidor más adelante. [Más información acerca de los grupos de consumidores](https://msdn.microsoft.com/library/azure/dn836025.aspx). Para crear un grupo de consumidores, vaya al concentrador de eventos recién creado, haga clic en la ficha **Consumidor grupos** , haga clic en **crear** en la parte inferior de la página y, a continuación, proporcione un nombre para el grupo de consumidores.
3.  Para conceder acceso al concentrador de evento, debemos crear una directiva de acceso compartido. Haga clic en la ficha **Configurar** de su centro de evento.
4.  En **Directivas de acceso compartido**, cree una nueva directiva que tenga permisos de **Administrar** .

    ![Compartir directivas de acceso, donde puede crear una directiva con administrar permisos.](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-shared-access-policies.png)

5.  Haga clic en **Guardar** en la parte inferior de la página.
6.  Vaya a **panel**, haga clic en **Información de conexión** en la parte inferior de la página y, a continuación, copie y guarde la información de conexión.

## <a name="configure-and-start-the-event-generator-application"></a>Configurar e iniciar la aplicación del generador de evento

Proporcionamos una aplicación de cliente que generará muestra los metadatos de llamada entrante y enviarlo a Hubs de evento. Use los siguientes pasos para configurar esta aplicación.  

1.  Descargar el [archivo TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)y descomprima a un directorio.

    > [AZURE.NOTE] Windows puede bloquear el archivo zip descargado. Haga clic en el archivo y seleccione **Propiedades**. Si ve el mensaje "este archivo proviene de otro equipo y podría bloquearse para ayudar a proteger este equipo", active la casilla de **desbloquear** y, a continuación, haga clic en aplicar en el archivo zip.

2.  Reemplace los valores Microsoft.ServiceBus.ConnectionString y EventHubName en telcodatagen.exe.config con el nombre y la cadena de conexión de hub de evento.

    La cadena de conexión que ha copiado desde el portal de Azure coloca el nombre de la conexión al final. Asegúrese de quitar "; EntityPath =<value>"de la" Agregar clave = "campo.

3.  Inicie la aplicación. El uso es como sigue:

    telcodatagen.exe [#NumCDRsPerHour] [SIM tarjeta fraude probabilidad] [#DurationHours]

En el ejemplo siguiente se generará 1.000 eventos con una probabilidad de 20 por ciento de fraude a lo largo de dos horas.

    telcodatagen.exe 1000 .2 2

Verá los registros enviados a su centro de evento. Algunos campos de clave que se va a utilizar en esta aplicación de detección de fraudes en tiempo real se definen aquí:

| Registro | Definición |
| ------------- | ------------- |
| CallrecTime | Marca de tiempo para la hora de inicio de la llamada. |
| SwitchNum | Conmutador de teléfono que se usa para conectarse a la llamada. |
| CallingNum | Número de teléfono de la persona que llama. |
| CallingIMSI | Identidad de suscriptor móvil internacional (IMSI).  Identificador único de la persona que llama. |
| CalledNum | Número de teléfono de los destinatarios de la llamada. |
| CalledIMSI | Identidad de suscriptor móvil internacional (IMSI).  Identificador único del receptor de la llamada. |


## <a name="create-a-stream-analytics-job"></a>Crear un análisis de flujo de trabajo
Ahora que tenemos una secuencia de eventos de telecomunicaciones, podemos configurar un trabajo de análisis de secuencia para analizar estos eventos en tiempo real.

### <a name="provision-a-stream-analytics-job"></a>Aprovisionar a un trabajo de análisis de secuencia

1.  En el portal de Azure, haga clic en **nuevo** > **DATA SERVICES** > **Análisis de secuencia** > **Crear rápido**.
2.  Especifique los valores siguientes y, a continuación, haga clic en **Crear trabajo de análisis de secuencia**:

    * **Nombre del trabajo**: escriba un nombre de trabajo.

    * **Región**: seleccione la región donde desea ejecutar el trabajo. Considere la posibilidad de realizar el trabajo y el concentrador de eventos de la misma región para lograr un mejor rendimiento y asegurarse de no se pueden pagar transferir datos entre regiones.

    * **Cuenta de almacenamiento**: elija la cuenta de almacenamiento de Azure que desea usar para almacenar los datos del seguimiento de todos los trabajos de análisis de flujo que se ejecutan en esta área. Tiene la opción de elegir una cuenta de almacenamiento existente o cree uno nuevo.

3.  En el panel izquierdo para enumerar los trabajos de análisis de secuencia, haga clic en **Análisis de secuencia** .

    ![Icono de servicio de análisis de secuencia](./media/stream-analytics-real-time-fraud-detection/stream-analytics-service-icon.png)

    La nueva tarea se mostrarán con el estado **creado**. Observe que el botón **Inicio** en la parte inferior de la página está deshabilitado. Configure la entrada de trabajo, resultados y consulta antes de empezar el trabajo.

### <a name="specify-job-input"></a>Especificar la entrada de trabajo
1.  En el trabajo de análisis de secuencia, haga clic en **entradas** en la parte superior de la página y, a continuación, haga clic en **Agregar entrada**. El cuadro de diálogo que se abre le guiará a través de varios pasos para configurar la entrada.
2.  Haga clic en **flujo de datos**y, a continuación, haga clic en el botón derecho.
3.  Haga clic en **El HUB de evento**y, a continuación, haga clic en el botón derecho.
4.  Escriba o seleccione los valores siguientes en la tercera página:

    * **ALIAS de entrada**: escriba un nombre descriptivo, como *CallStream*, para este trabajo. Tenga en cuenta que va a utilizar este nombre en la consulta más adelante.

    * **Evento concentrador**: si el concentrador de eventos que ha creado está en la misma suscripción como el trabajo de análisis de secuencia, seleccione el espacio de nombres que en el hub de evento.

        Si es su centro de evento en una suscripción diferente, seleccione **Concentrador de eventos de uso de otra suscripción**y, a continuación, escribir información para **Espacio de nombres de BUS de servicio**, **El nombre del concentrador de evento**, **Nombre de la directiva de evento concentrador**, **Clave de directiva de concentrador de evento**y **Evento concentrador partición recuento**.

    * **Nombre del evento concentrador**: seleccione el nombre del concentrador de evento.

    * **Nombre de la directiva de evento concentrador**: seleccione la directiva de concentrador de evento que creó anteriormente en este tutorial.

    * **Grupo de CONSUMIDORES de concentrador de evento**: escriba el nombre del grupo de consumidores que creó anteriormente en este tutorial.

5.  Haga clic en el botón derecho.
6.  Especifique los valores siguientes:

    * **Evento SERIALIZADOR formato**: JSON
    * **Codificación**: UTF8
7.  Haga clic en el botón **Comprobar** para agregar esta fuente y compruebe que el análisis de secuencia puede conectarse correctamente al concentrador de eventos.

### <a name="specify-job-query"></a>Especifique la consulta de la tarea

Análisis de secuencia es compatible con un modelo de consulta simple, descriptiva que describe las transformaciones de procesamiento en tiempo real. Para obtener más información acerca del idioma, vea la [Referencia del lenguaje de consulta de Azure secuencia de análisis](https://msdn.microsoft.com/library/dn834998.aspx). Este tutorial le ayudará a crear y probar varias consultas sobre su secuencia de llamada de datos en tiempo real.

#### <a name="optional-sample-input-data"></a>Opcional: Ejemplo de datos de entrada
Para validar la consulta con los datos de trabajo real, puede usar la característica de **Datos de ejemplo** para extraer los eventos de la secuencia y crear una. Archivo JSON de los eventos para realizar pruebas.  Los pasos siguientes muestran cómo hacerlo. También proporcionamos un archivo de [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) de ejemplo con fines de pruebas.

1.  Seleccione la entrada de concentrador de evento y, a continuación, haga clic en **Datos de ejemplo** en la parte inferior de la página.
2.  En el cuadro de diálogo que se abre, especifique una **hora de inicio** para iniciar la recopilación de datos y una **duración** de la cantidad de datos adicional consumir.
3.  Haga clic en el botón **Comprobar** para empezar a los datos de muestra de la entrada.  Puede tardar un minuto o dos para el archivo de datos.  Cuando termine el proceso, haga clic en **Detalles**, descargue el archivo. JSON de archivo y guárdelo.

    ![Descargar y guardar datos procesados en un archivo JSON](./media/stream-analytics-real-time-fraud-detection/stream-analytics-download-save-json-file.png)

#### <a name="pass-through-query"></a>Consulta de paso a través

Si desea archivar todos los eventos, puede usar una consulta de paso para leer todos los campos de la carga del evento o mensaje. Para empezar, realice una consulta de paso simple que todos los campos de un evento de proyectos.

1.  Haga clic en **consulta** desde la parte superior de la página de trabajo de análisis de la secuencia.
2.  En el editor de código, agregue lo siguiente:

        SELECT * FROM CallStream

    > [AZURE.IMPORTANT] Asegúrese de que el nombre del origen de entrada coincide con el nombre de la entrada que ha especificado una versión anterior.

3.  En el editor de consultas, haga clic en **prueba** .
4.  Proporcionar un archivo de prueba. Utilice una que se creó con los pasos anteriores, o [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/SampleDataFiles/Telco.json).
5.  Haga clic en el botón **Buscar** y ver los resultados que se muestran debajo de la definición de consulta.

    ![Resultados de la definición de consulta](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sim-fraud-output.png)


### <a name="column-projection"></a>Proyección de columna

Ahora se reducirá los campos devueltos a un conjunto más pequeño.

1.  Cambiar la consulta en el editor de código:

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum
        FROM CallStream

2.  Haga clic en **volver a ejecutar** en el editor de consultas para ver los resultados de la consulta.

    ![Resultados en el editor de consultas.](./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Recuento de entrada llamadas por región: ventana de rotación con agregación

Para comparar el número de llamadas entrantes por región, usaremos un [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obtener el recuento de las llamadas entrantes agrupados por **SwitchNum** cada cinco segundos.

1.  Cambiar la consulta en el editor de código:

        SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount
        FROM CallStream TIMESTAMP BY CallRecTime
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    Esta consulta utiliza la **Marca de tiempo por** palabra clave para especificar un campo de marca de tiempo en la carga que se utilizarán en el cálculo temporal. Si este campo no se ha especificado, la operación de ventanas debería realizarse usando el tiempo que ha llegado cada evento en el hub de evento. Vea la [referencia del lenguaje de consulta "Llegada Vs aplicación hora" en el análisis de la secuencia](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Tenga en cuenta que puede tener acceso a una marca de tiempo para el final de cada ventana mediante la propiedad **System.Timestamp** .

2.  Haga clic en **volver a ejecutar** en el editor de consultas para ver los resultados de la consulta.

    ![Resultados de la consulta para Timestand por](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>Detección de fraudes SIM con una autocombinación

Para identificar el uso fraudulento, veremos para las llamadas que proceden de un mismo usuario pero en ubicaciones diferentes en menos de 5 segundos.  Nos [unirse a](https://msdn.microsoft.com/library/azure/dn835026.aspx) la secuencia de eventos de llamada consigo para comprobar estos casos.

1.  Cambiar la consulta en el editor de código:

        SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
        CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
        ON CS1.CallingIMSI = CS2.CallingIMSI
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum

2.  Haga clic en **volver a ejecutar** en el editor de consultas para ver los resultados de la consulta.

    ![Resultados de la consulta de una combinación](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>Crear receptor de salida

Ahora que hemos definido una secuencia de evento, un concentrador de eventos de entrada para recopilar eventos y una consulta para realizar una transformación sobre la secuencia, el último paso es definir un receptor de salida para el trabajo. Pasaremos a crear eventos fraudulento comportamiento al almacenamiento de blobs de Windows Azure.

Use los siguientes pasos para crear un contenedor de almacenamiento de blobs si aún no tiene una.

1.  Usar una cuenta de almacenamiento existente o crear una nueva cuenta de almacenamiento haciendo clic en **Nuevo > Servicios de datos > almacenamiento > crear rápido**y siga las instrucciones.
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

## <a name="start-job-for-real-time-processing"></a>Iniciar el trabajo para el procesamiento en tiempo real

Porque una entrada de trabajo, la consulta y la salida han todos ha especificado, se está listos para iniciar el trabajo de análisis de secuencia de detección de fraudes en tiempo real.

1.  Desde el **panel**de trabajo, haga clic en **Iniciar** en la parte inferior de la página.
2.  En el cuadro de diálogo que se abre, haga clic en la **hora de comienzo del trabajo**y, a continuación, haga clic en el botón **Buscar** en la parte inferior del cuadro de diálogo. El estado del trabajo cambiará a **Iniciar** y poco cambiará a **ejecutando**.

## <a name="view-fraud-detection-output"></a>Resultados de la detección de fraude de vista

Usar una herramienta como [El Explorador de almacenamiento de Azure](http://storageexplorer.com/) o en [El Explorador de Azure](http://www.cerebrata.com/products/azure-explorer/introduction) para ver eventos fraudulentos a medida que se escriben en el resultado en tiempo real.  

![Detección de fraudes: eventos fraudulentos visto en tiempo real](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>Obtener soporte técnico
Para obtener más ayuda, pruebe a nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)
