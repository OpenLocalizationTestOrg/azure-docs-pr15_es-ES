<properties
    pageTitle="Introducción a análisis de secuencia: detección de fraudes en tiempo real | Microsoft Azure"
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

Obtenga información sobre cómo crear una solución de llevar a cabo para detección de fraudes en tiempo real con el análisis de secuencia de Azure. Traer eventos a un concentrador de evento Azure, escribir consultas de análisis de secuencia para la agregación o alertas y enviar los resultados a un receptor de salida para obtener información sobre los datos con el procesamiento en tiempo real. Detección de anomalías en tiempo real de telecomunicaciones está cubierta pero la técnica de ejemplo igualmente es adecuada para otros tipos de detección de fraudes como tarjeta de crédito o identidad escenarios de robo.

Análisis de secuencia es un servicio de totalmente administrado proporcionar procesamiento de baja latencia, altamente disponible, scalable eventos compleja sobre la transmisión de datos en la nube. Para obtener más información, vea [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md).


## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Escenario: Detección de fraudes SIM y de telecomunicaciones en tiempo real

Una empresa de telecomunicaciones tiene un gran volumen de datos para las llamadas entrantes. La empresa tiene los siguientes puntos de sus datos:
* Comparar datos hacia abajo hasta un cierto y obtener información sobre el uso del cliente con el tiempo y las regiones geográficas.
* Detectar fraude SIM (varias llamadas procedentes de la misma identidad alrededor de la misma hora pero en ubicaciones diferentes geográfico) en tiempo real para que pueden responder fácilmente por notificar a los clientes o cerrando servicio.

En escenarios de Internet de cosas (IoT) canónicos hay una gran cantidad de datos de telemetría o sensor se genera – y clientes desean agregarlas o avisar sobre anomalías en tiempo real.

## <a name="prerequisites"></a>Requisitos previos

- Descargar [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) desde el centro de descarga de Microsoft 
- Opcional: El código del generador de eventos de [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TelcoGenerator) de fuente

## <a name="create-an-azure-event-hubs-input-and-consumer-group"></a>Crear una entrada de Azure evento Hubs y grupo de consumidor

La aplicación de ejemplo generará eventos e insertarlos a una instancia de concentrador de evento para el procesamiento en tiempo real. Servicio de Bus de evento Hubs son el método preferido de recopilación de eventos para los análisis de secuencia y puede aprender más sobre Hubs de evento en la [documentación de Bus de servicio de Azure](/documentation/services/service-bus/).

Para crear un concentrador de evento:

1.  En el [portal de Azure](https://manage.windowsazure.com/) , haga clic en **nuevo** > **Servicios de aplicación** > **Bus de servicio** > **Concentrador de evento** > **Crear rápido**. Proporcione un nombre, la región y el espacio de nombres nuevo o existente para crear un nuevo evento de concentrador.  
2.  Como práctica recomendada, debe leer cada trabajo de análisis de secuencia de un solo grupo de consumidores de concentrador de evento. Le guiaremos a través del proceso de creación de un grupo de consumidor siguiente, y puede [obtener más información acerca de los grupos de consumidores](https://msdn.microsoft.com/library/azure/dn836025.aspx). Para crear un grupo de consumidores, desplácese hasta el Hub de evento recién creado y haga clic en la pestaña de **Grupos de consumidores** , a continuación, haga clic en **crear** en la parte inferior de la página y proporcione un nombre para el grupo de consumidores.
3.  Para conceder acceso al evento concentrador, debemos crear una directiva de acceso compartido.  Haga clic en la ficha **Configurar** de su centro de evento.
4.  En **Directivas de acceso compartidos**, cree una nueva directiva con **Administrar** permisos.

    ![Compartir directivas de acceso, donde puede crear una directiva con administrar permisos.](./media/stream-analytics-get-started/stream-ananlytics-shared-access-policies.png)

5.  Haga clic en **Guardar** en la parte inferior de la página.
6.  Vaya a **panel** y haga clic en **Información de conexión** en la parte inferior de la página y, a continuación, copie y guarde la información de conexión.

## <a name="configure-and-start-event-generator-application"></a>Configurar e iniciar la aplicación del generador de eventos

Proporcionamos una aplicación de cliente que generará muestra los metadatos de llamada entrante y enviarlo a concentrador de evento. Siga los pasos siguientes para configurar esta aplicación.  

1.  Descargue el [archivo TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip). A continuación, descomprima a un directorio.

    **Nota**: Windows puede bloquear el archivo zip descargado. Haga clic con el botón secundario del mouse en el archivo y seleccione Propiedades. Si el mensaje "este archivo proviene de otro equipo y podría bloquearse para ayudar a proteger el equipo." a continuación, marque la casilla "Desbloquear" y haga clic en aplicar en el archivo zip.

2.  Reemplace los valores Microsoft.ServiceBus.ConnectionString y EventHubName en **telcodatagen.exe.config** con el nombre y la cadena de conexión de Hub de evento.

    **Nota**: la cadena de conexión de copiar de los sitios de portal Azure el nombre de la conexión al final. Asegúrese de quitar la "; EntityPath =<value>"de la clave de Agregar campo =.

3.  Inicie la aplicación. El uso es como sigue:

   telcodatagen.exe [#NumCDRsPerHour] [SIM tarjeta fraude probabilidad] [#DurationHours]

En el ejemplo siguiente se generará 1000 eventos con una probabilidad de 20 por ciento de fraude en el transcurso de 2 horas.

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


## <a name="create-stream-analytics-job"></a>Crear tarea de análisis de secuencia
Ahora que tenemos una secuencia de eventos de telecomunicaciones, podemos configurar un trabajo de análisis de secuencia para analizar estos eventos en tiempo real.

### <a name="provision-a-stream-analytics-job"></a>Aprovisionar a un trabajo de análisis de secuencia

1.  En el portal de Azure, haga clic en **Nuevo > Servicios de datos > análisis de secuencia > crear rápido**.
2.  Especifique los valores siguientes y, a continuación, haga clic en **Crear trabajo de análisis de secuencia**:

    * **Nombre del trabajo**: escriba un nombre de trabajo.

    * **Región**: seleccione la región donde desea ejecutar el trabajo. Considere la posibilidad de realizar el trabajo y el concentrador de eventos de la misma región para lograr un mejor rendimiento y asegurarse de no se pueden pagar transferir datos entre regiones.

    * **Cuenta de almacenamiento**: elija la cuenta de almacenamiento de Azure que desea usar para almacenar los datos del seguimiento de todos los trabajos de análisis de secuencia ejecutando dentro de esta área. Tiene la opción para elegir una cuenta de almacenamiento existente o cree uno nuevo.

3.  En el panel izquierdo para enumerar los trabajos de análisis de secuencia, haga clic en **Análisis de secuencia** .

    ![Icono de servicio de análisis de secuencia](./media/stream-analytics-get-started/stream-analytics-service-icon.png)

4.  La nueva tarea se mostrarán con el estado **creado**. Observe que el botón **Inicio** en la parte inferior de la página está deshabilitado. Configure la entrada de trabajo, resultados y consulta antes de empezar el trabajo.

### <a name="specify-job-input"></a>Especificar la entrada de trabajo
1.  En el trabajo de análisis de secuencia haga clic en **entradas** de la parte superior de la página y, a continuación, haga clic en **Agregar entrada**. El cuadro de diálogo que se abre le guiará a través de un número de pasos para configurar la entrada.
2.  Seleccione el **flujo de datos**y, a continuación, haga clic en el botón derecho.
3.  Seleccione **Hub de evento**y, a continuación, haga clic en el botón derecho.
4.  Escriba o seleccione los valores siguientes en la tercera página:

    * **Alias de entrada**: escriba un nombre descriptivo para este trabajo como *CallStream*de entrada. Tenga en cuenta que va a utilizar este nombre en la consulta más adelante.
    * **Evento concentrador**: si el concentrador de eventos que creó en la misma suscripción como el trabajo de análisis de secuencia, seleccione el espacio de nombres que en el hub de evento.

    Si es su centro de evento en una suscripción diferente, seleccione **Usar evento concentrador de otra suscripción** e introducir manualmente información para **Namespace de Bus de servicio**, **El nombre del concentrador de evento**, **Nombre de la directiva de evento concentrador**, **Clave de directiva de concentrador de evento**y **Evento concentrador partición recuento**.

    * **Nombre del evento concentrador**: seleccione el nombre del evento concentrador.

    * **Nombre de la directiva de evento concentrador**: seleccione la directiva de concentrador de evento que creó anteriormente en este tutorial.

    * **Grupo de consumidores de concentrador de evento**: escriba el grupo de consumidores que creó anteriormente en este tutorial.
5.  Haga clic en el botón derecho.
6.  Especifique los valores siguientes:

    * **Evento serializador formato**: JSON
    * **Codificación**: UTF8
7.  Haga clic en el botón Comprobar para agregar esta fuente y compruebe que el análisis de secuencia puede conectarse correctamente al concentrador de eventos.

### <a name="specify-job-query"></a>Especifique la consulta de la tarea

Análisis de secuencia es compatible con un modelo de consultas sencillas y descriptiva para describir las transformaciones de procesamiento en tiempo real. Para obtener más información acerca del idioma, vea la [Referencia del lenguaje de consulta de Azure secuencia de análisis](https://msdn.microsoft.com/library/dn834998.aspx). Este tutorial le ayudará a crear y probar varias consultas sobre su secuencia de llamada de datos en tiempo real.

#### <a name="optional-sample-input-data"></a>Opcional: Ejemplo de datos de entrada
Para validar la consulta con los datos de trabajo real, puede usar la característica de **Datos de ejemplo** para extraer los eventos de la secuencia y crear una. Archivo JSON de los eventos para realizar pruebas.  Los pasos siguientes muestran cómo hacerlo y también hemos proporcionado un archivo de [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) de ejemplo con fines de pruebas.

1.  Seleccione la entrada de concentrador de evento y haga clic en **Datos de ejemplo** en la parte inferior de la página.
2.  En el cuadro de diálogo que aparece, especifique una **Hora de inicio** para iniciar la recopilación de datos y una **duración** de la cantidad de datos adicional consumir.
3.  Haga clic en el botón Comprobar para empezar a los datos de muestra de la entrada.  Puede tardar un minuto o dos para el archivo de datos.  Cuando haya finalizado el proceso, haga clic en **Detalles** y descargue y guarde el. Archivo JSON que se genera.

    ![Descargar y guardar datos procesados en un archivo JSON](./media/stream-analytics-get-started/stream-analytics-download-save-json-file.png)

#### <a name="passthrough-query"></a>Consulta de paso a través

Si desea archivar todos los eventos, puede usar una consulta de paso a través de leer todos los campos de la carga del evento o mensaje. Inicialmente, realice una consulta de paso a través simple que todos los campos de un evento de proyectos.

1.  Haga clic en **consulta** desde la parte superior de la página de trabajo de análisis de la secuencia.
2.  En el editor de código, agregue lo siguiente:

        SELECT * FROM CallStream

    > Asegúrese de que el nombre del origen de entrada coincide con el nombre de la entrada especificada anteriormente.

3.  En el editor de consultas, haga clic en **prueba** .
4.  Proporcionar un archivo de prueba, ya sea uno que ha creado con los pasos anteriores o use [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json).
5.  Haga clic en el botón Comprobar y ver los resultados que se muestran debajo de la definición de consulta.

    ![Resultados de la definición de consulta](./media/stream-analytics-get-started/stream-analytics-sim-fraud-output.png)


### <a name="column-projection"></a>Proyección de columna

Ahora se deberá reducir los campos devueltos a un conjunto más pequeño.

1.  Cambiar la consulta en el editor de código:

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum
        FROM CallStream

2.  Haga clic en **volver a ejecutar** en el editor de consultas para ver los resultados de la consulta.

    ![Resultados en el editor de consultas.](./media/stream-analytics-get-started/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Recuento de entrada llamadas por región: ventana de rotación con agregación

Para comparar la cantidad que las llamadas entrantes por región podrá aprovechar un [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obtener el recuento de las llamadas entrantes agrupados por SwitchNum cada 5 segundos.

1.  Cambiar la consulta en el editor de código:

        SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount
        FROM CallStream TIMESTAMP BY CallRecTime
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    Esta consulta utiliza la **Marca de tiempo por** palabra clave para especificar un campo de marca de tiempo en la carga que se utilizarán en el cálculo temporal. Si este campo no se ha especificado, la operación de ventanas debería realizarse usando la hora que cada evento ha llegado a concentrador de evento. Vea la [referencia del lenguaje de consulta "Llegada Vs aplicación hora" en el análisis de la secuencia](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Tenga en cuenta que puede tener acceso a una marca de tiempo para el final de cada ventana mediante la propiedad **System.Timestamp** .

2.  Haga clic en **volver a ejecutar** en el editor de consultas para ver los resultados de la consulta.

    ![Resultados de la consulta para Timestand por](./media/stream-analytics-get-started/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>Detección de fraudes SIM con una autocombinación

Para identificar el uso fraudulento veremos para las llamadas del mismo usuario pero en ubicaciones diferentes de origen en menos de 5 segundos.  Nos [unirse a](https://msdn.microsoft.com/library/azure/dn835026.aspx) la secuencia de eventos de llamada consigo para comprobar estos casos.

1.  Cambiar la consulta en el editor de código:

        SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
        CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
        ON CS1.CallingIMSI = CS2.CallingIMSI
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum

2.  Haga clic en **volver a ejecutar** en el editor de consultas para ver los resultados de la consulta.

    ![Resultados de la consulta de una combinación](./media/stream-analytics-get-started/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>Crear receptor de salida

Ahora que hemos definido una secuencia de evento, un concentrador de eventos de entrada para recopilar eventos y una consulta para realizar una transformación sobre la secuencia, el último paso es definir un receptor de salida para el trabajo.  Pasaremos a crear eventos fraudulento comportamiento al almacenamiento de blobs de Windows.

Siga los pasos siguientes para crear un contenedor de almacenamiento de blobs si aún no tiene una.

1.  Usar una cuenta de almacenamiento existente o crear una nueva cuenta de almacenamiento haciendo clic en **Nuevo > Servicios de datos > almacenamiento > crear rápido** y siga las instrucciones.
2.  Seleccione la cuenta de almacenamiento, haga clic en **contenedores** en la parte superior de la página y, a continuación, haga clic en **Agregar**.
3.  Especifique un **nombre** para el contenedor y establezca su **acceso** a Blob público.

## <a name="specify-job-output"></a>Especificar la salida de trabajo

1.  En el trabajo de análisis de secuencia haga clic en **resultados** de la parte superior de la página y, a continuación, haga clic en **Agregar resultado**. El cuadro de diálogo que se abre le guiará a través de un número de pasos para configurar su resultado.
2.  Seleccione **Almacenamiento de blobs**y, a continuación, haga clic en el botón derecho.
3.  Escriba o seleccione los valores siguientes en la tercera página:

    * **ALIAS de salida**: escriba un nombre descriptivo para este resultado de la tarea.
    * **Suscripción**: si el almacenamiento de blobs de Windows que creó en la misma suscripción como el trabajo de análisis de secuencia, seleccione **Utilizar la cuenta de almacenamiento de suscripción actual**. Si el almacenamiento está en una suscripción diferente, seleccione **Usar la cuenta de almacenamiento de otra suscripción** y escribir manualmente la información de **Cuenta de almacenamiento**, **Clave de cuenta de almacenamiento**, **contenedor**.
    * **Cuenta de almacenamiento**: seleccione el nombre de la cuenta de almacenamiento.
    * **Contenedor**: seleccione el nombre del contenedor.
    * **PREFIJO de nombre de archivo**: escriba un prefijo de archivo a usar al escribir los resultados de blobs de Windows.

4.  Haga clic en el botón derecho.
5.  Especifique los valores siguientes:

    * **Evento SERIALIZADOR formato**: JSON
    * **Codificación**: UTF8

6.  Haga clic en el botón Comprobar para agregar esta fuente y compruebe que el análisis de secuencia puede conectarse correctamente a la cuenta de almacenamiento.

## <a name="start-job-for-real-time-processing"></a>Iniciar el trabajo para el procesamiento de tiempo real

Desde una entrada de trabajo, la consulta y la salida han todos ha especificado, se está listos para iniciar el trabajo de análisis de secuencia de detección de fraudes en tiempo real.

1.  Desde el **panel**de trabajo, haga clic en **Iniciar** en la parte inferior de la página.
2.  En el cuadro de diálogo que aparece, seleccione la **hora de comienzo del trabajo** y, a continuación, haga clic en el botón de verificación en la parte inferior del cuadro de diálogo. El estado del trabajo cambiará a **Iniciar** y poco moverá a **ejecutando**.

## <a name="view-fraud-detection-output"></a>Resultados de la detección de fraude de vista

Usar una herramienta como [El Explorador de almacenamiento de Azure](https://azurestorageexplorer.codeplex.com/) o en [El Explorador de Azure](http://www.cerebrata.com/products/azure-explorer/introduction) para ver eventos fraudulentos a medida que se escriben en el resultado en tiempo real.  

![Detección de fraudes: eventos fraudulentos visto en tiempo real](./media/stream-analytics-get-started/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>Obtener soporte técnico
Para obtener más ayuda, pruebe a nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)
