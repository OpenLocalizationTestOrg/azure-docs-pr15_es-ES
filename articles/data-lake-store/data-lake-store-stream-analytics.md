<properties
   pageTitle="Flujo de datos de análisis de secuencia en el almacén de datos de lago | Azure"
   description="Usar el análisis de secuencia de Azure para transmitir datos a lago almacén de datos de Azure"
   services="data-lake-store,stream-analytics" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/07/2016"
   ms.author="nitinme"/>

# <a name="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics"></a>Datos de la secuencia de Azure almacenamiento de blobs de Windows en el almacén de datos de lago mediante el análisis de secuencia de Azure

En este artículo aprenderá cómo usar el almacén de lago de datos de Azure como salida para una tarea de análisis de secuencia de Azure. En este artículo se muestra un escenario simple que lee datos de un blobs de Windows Azure almacenamiento (entrada) y escribe los datos en el almacén de datos de lago (resultado).

>[AZURE.NOTE] En este momento, creación y configuración de almacén de datos lago salidas para análisis de secuencia sólo se admite en el [Portal clásico de Azure](https://manage.windowsazure.com). Por lo tanto, algunas partes de este tutorial usará el Portal de clásico de Azure.

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).

- **Habilitar la suscripción Azure** de vista previa de datos lago almacén público. Vea [las instrucciones](data-lake-store-get-started-portal.md#signup).

- **Cuenta de almacenamiento de azure**. Use un contenedor de blob desde esta cuenta a datos para una tarea de análisis de secuencia de entrada. En este tutorial, se supone que crea una cuenta de almacenamiento denominado **datalakestoreasa** y un contenedor dentro de la cuenta denominada **datalakestoreasacontainer**. Una vez que haya creado el contenedor, cargar un archivo de datos de ejemplo en ella. Puede obtener un archivo de datos de ejemplo del [Lago Git repositorio de datos de Azure](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Puede usar a varios clientes, como [El Explorador de almacenamiento de Azure](http://storageexplorer.com/), para cargar los datos en un contenedor de blob.

    >[AZURE.NOTE] Si crea la cuenta desde el Portal de Azure, asegúrese de que cree con el modelo de implementación **clásico** . Así se garantiza que se puede tener acceso a la cuenta de almacenamiento desde el Portal de clásico de Azure, ya que es lo que usamos para crear un análisis de flujo de trabajo. Para obtener instrucciones sobre cómo crear una cuenta de almacenamiento desde el Portal de Azure mediante la implementación de clásico, vea [crear una cuenta de almacenamiento de Azure](../storage/storage-create-storage-account/#create-a-storage-account).
    >
    > O bien, puede crear una cuenta de almacenamiento desde el Portal de clásico de Azure.

- **Cuenta de la tienda de lago de datos de azure**. Siga las instrucciones en [Introducción a Azure datos lago tienda con el Portal de Azure](data-lake-store-get-started-portal.md).  


## <a name="create-a-stream-analytics-job"></a>Crear una tarea de análisis de secuencia

Iniciar mediante la creación de una tarea de análisis de secuencia que incluye un origen de entrada y un destino de salida. En este tutorial, el origen es un contenedor de blobs de Windows Azure y el destino es el almacén de datos de lago.

1. Inicie sesión el [Portal de clásico de Azure](https://manage.windowsazure.com).

2. En la parte inferior izquierda de la pantalla, haga clic en **nuevo**, **Servicios de datos**, **El análisis de secuencia**, **Crear rápido**. Proporcionar los valores tal y como se muestra a continuación y, a continuación, haga clic en **Crear trabajo de análisis de secuencia**.

    ![Crear una tarea de análisis de secuencia] (./media/data-lake-store-stream-analytics/create.job.png "Crear un análisis de flujo de trabajo")

## <a name="create-a-blob-input-for-the-job"></a>Crear una entrada de blobs para el trabajo

1. Abra la página de la tarea de análisis de secuencia, haga clic en la pestaña **entradas** y, a continuación, haga clic en **Agregar una entrada** para iniciar el asistente.

2. En la página **Agregar una entrada de su trabajo** , seleccione el **flujo de datos**y, a continuación, haga clic en la flecha hacia adelante.

    ![Agregar una entrada de su trabajo] (./media/data-lake-store-stream-analytics/create.input.1.png "Agregar una entrada de su trabajo")

3. En la página **Agregar un flujo de datos a su trabajo** , seleccione **almacenamiento de blobs de Windows**y, a continuación, haga clic en la flecha hacia adelante.

    ![Agregar un flujo de datos para el trabajo] (./media/data-lake-store-stream-analytics/create.input.2.png "Agregar un flujo de datos para el trabajo")

4. En la página de **configuración de almacenamiento de blobs** , proporcionar detalles para el almacenamiento de blobs de Windows que usará como origen de datos de entrada.

    ![Proporcionar el blob configuración de almacenamiento] (./media/data-lake-store-stream-analytics/create.input.3.png "Proporcionar el blob configuración de almacenamiento")

    * **Escriba un alias de entrada**. Se trata de un nombre único que proporciona para el trabajo de entrada.
    * **Seleccione una cuenta de almacenamiento**. Asegúrese de que la cuenta de almacenamiento está en la misma región como el trabajo de análisis de la secuencia o se producirá un costo adicional de mover datos entre regiones.
    * **Proporcionar un contenedor de almacenamiento**. Puede crear un nuevo contenedor o seleccione un contenedor existente.

    Haga clic en la flecha hacia adelante.

5. En la página **configuración de serialización** , establecer el formato de serialización como **CSV**, delimitador como **ficha**codificación como **UTF8**y, a continuación, haga clic en la marca de verificación.

    ![Proporcione la configuración de serialización] (./media/data-lake-store-stream-analytics/create.input.4.png "Proporcione la configuración de serialización")

6. Una vez finalizado el asistente, se agregará la entrada blob en la pestaña **entradas** y el **diagnóstico** de la columna debe mostrar **Aceptar**. Explícitamente puede probar la conexión a la entrada mediante el botón **Probar conexión** en la parte inferior.

## <a name="create-a-data-lake-store-output-for-the-job"></a>Crear una salida de almacén de datos lago para el trabajo

1. Abra la página de la tarea de análisis de secuencia, haga clic en la ficha de **resultados** y, a continuación, haga clic en **Agregar un resultado** para iniciar el asistente.

2. En la página **Agregar un resultado a su trabajo** , seleccione el **Almacén de datos de lago**y, a continuación, haga clic en la flecha hacia adelante.

    ![Agregar una salida para el trabajo] (./media/data-lake-store-stream-analytics/create.output.1.png "Agregar una salida para el trabajo")

3. En la página de **conexión de autorizar** , si ya ha creado una cuenta de la tienda de lago de datos, haga clic en **Autorizar ahora**. En caso contrario, haga clic en **registrarse ahora** para crear una nueva cuenta. En este tutorial, supongamos que ya tiene una cuenta de almacén de datos lago creada (como se mencionó en el requisito previo). Usted se autorizado automáticamente por con las credenciales con el que ha iniciado sesión en el Portal de clásico de Azure.

    ![Autorizar el almacén de datos de lago] (./media/data-lake-store-stream-analytics/create.output.2.png "Autorizar el almacén de datos de lago")

4. En la página **Configuración del almacenamiento de datos lago** , escriba la información como se muestra en la siguiente captura de pantalla.

    ![Configuración del almacenamiento de especificar datos lago] (./media/data-lake-store-stream-analytics/create.output.3.png "Configuración del almacenamiento de especificar datos lago")

    * **Escriba un alias de salida**. Se trata de un nombre único que se proporcionan para la salida de trabajo.
    * **Especificar una cuenta de almacén de datos de lago**. Debe ya ha creado, como se mencionó en el requisito previo.
    * **Especificar una trama de prefijo de ruta de acceso**. Esto es necesario para identificar los archivos de resultados que se escriben en el almacén de datos de lago por el trabajo de análisis de la secuencia. Los títulos de resultados, escritos por el trabajo, ya están en un formato GUID con un prefijo le ayudará a identificar la salida escrita. Si desea incluir una marca de fecha y hora como parte del prefijo Asegúrese de incluir `{date}/{time}` en el modelo de prefijo. Si se incluye, los campos de **Fecha **y **Hora formato** están habilitados y puede seleccionar el formato de elección.

    Haga clic en la flecha hacia adelante.

5. En la página **configuración de serialización** , establecer el formato de serialización como **CSV**, delimitador como **ficha**codificación como **UTF8**y, a continuación, haga clic en la marca de verificación.

    ![Especificar el formato de salida] (./media/data-lake-store-stream-analytics/create.output.4.png "Especificar el formato de salida")

6. Una vez finalizado el asistente, se agregará el resultado de la tienda de lago de datos en la pestaña **salidas** y el **diagnóstico** de la columna debe mostrar **Aceptar**. Puede probar la conexión a los resultados de explícitamente usando el botón **Probar conexión** en la parte inferior.

## <a name="run-the-stream-analytics-job"></a>Ejecutar el trabajo de análisis de secuencia

Para ejecutar un análisis de flujo de trabajo, debe ejecutar una consulta desde la pestaña de la consulta. En este tutorial, puede ejecutar la consulta de ejemplo reemplazando los marcadores de posición con el trabajo de entrada y salida alias, como se muestra en la siguiente captura de pantalla.

![Ejecutar consulta] (./media/data-lake-store-stream-analytics/run.query.png "Ejecutar consulta")

Haga clic en **Guardar** en la parte inferior de la pantalla y, a continuación, haga clic en **Inicio**. Desde el cuadro de diálogo, seleccione **Hora personalizado**y, a continuación, seleccione una fecha en el pasado, como **1/1/2016**. Haga clic en la marca de verificación para iniciar el trabajo. Puede tardar hasta un par de minutos para iniciar el trabajo.

![Establecer el tiempo de trabajo] (./media/data-lake-store-stream-analytics/run.query.2.png "Establecer el tiempo de trabajo")

Cuando se inicie el trabajo, haga clic en la pestaña **Monitor** para ver cómo se procesan los datos.

![Trabajo de monitor] (./media/data-lake-store-stream-analytics/run.query.3.png "Trabajo de monitor")

Por último, puede usar el [Portal de Azure](https://portal.azure.com) para abrir la cuenta de almacén de datos lago y comprobar si los datos se escribieron correctamente a la cuenta.

![Comprobar resultados] (./media/data-lake-store-stream-analytics/run.query.4.png "Comprobar resultados")

En el panel Explorador de datos, observe que el resultado se escribe en una carpeta según lo especificado en el almacén de datos lago ajustes de salida (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Vea también

* [Crear un clúster de HDInsight para usar el almacén de datos de lago](data-lake-store-hdinsight-hadoop-use-portal.md)
