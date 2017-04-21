<properties
    pageTitle="Obtenga información sobre cómo administrar servicios web de AzureML con API administración | Microsoft Azure"
    description="Una guía que muestra cómo administrar servicios web de AzureML con la API de administración."
    keywords="aprendizaje de administración de la api"
    services="machine-learning"
    documentationCenter=""
    authors="roalexan"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="roalexan" />


# <a name="learn-how-to-manage-azureml-web-services-using-api-management"></a>Obtenga información sobre cómo administrar servicios web de AzureML con la API de administración

##<a name="overview"></a>Información general

Esta guía le muestra cómo comenzar rápidamente a trabajar con la API de administración para administrar los servicios de web AzureML.

##<a name="what-is-azure-api-management"></a>¿Qué es la administración de la API de Azure?

Administración de API Azure es un servicio de Azure que le permite administrar los extremos de la API de REST definiendo acceso de usuario, el límite de uso y la supervisión de paneles. Haga clic [aquí](https://azure.microsoft.com/services/api-management/) para obtener más información sobre la administración de la API de Azure. Haga clic [aquí](../api-management/api-management-get-started.md) para obtener una guía sobre cómo empezar a trabajar con la administración de la API de Azure. Esta guía otra, que se basa en esta guía, abarca más temas, incluidas las configuraciones de notificación, nivel de precio, tratamiento de respuesta, autenticación de usuario, crear productos, suscripciones de desarrollador y paneles de uso.

##<a name="what-is-azureml"></a>¿Qué es AzureML?

AzureML es un servicio de Azure del equipo de aprendizaje que le permite crear, implementar y compartir las soluciones de análisis avanzadas fácilmente. Haga clic [aquí](https://azure.microsoft.com/services/machine-learning/) para obtener más información sobre AzureML.

##<a name="prerequisites"></a>Requisitos previos

Para completar a esta guía, debe:

* Una cuenta de Azure. Si no tiene una cuenta de Azure, haga clic [aquí](https://azure.microsoft.com/pricing/free-trial/) para obtener más información sobre cómo crear una cuenta de prueba gratuita.
* Una cuenta de AzureML. Si no tiene una cuenta de AzureML, haga clic [aquí](https://studio.azureml.net/) para obtener más información sobre cómo crear una cuenta de prueba gratuita.
* El área de trabajo, servicio y api_key para apropiados AzureML implementado como un servicio web. Haga clic [aquí](machine-learning-create-experiment.md) para obtener más información sobre cómo crear un ensayo AzureML. Haga clic [aquí](machine-learning-publish-a-machine-learning-web-service.md) para obtener más información sobre cómo implementar un ensayo AzureML como un servicio web. Como alternativa, Apéndice A contiene las instrucciones crear y probar un ensayo AzureML simple e implementarlo como un servicio web.

##<a name="create-an-api-management-instance"></a>Crear una instancia de administración de la API

A continuación se muestran los pasos para usar la administración de la API para administrar el servicio web de AzureML. Crear una instancia de servicio. Inicie sesión en el [Portal clásica](https://manage.windowsazure.com/) y haga clic en **nuevo** > **Servicios de aplicación** > **API administración** > **crear**.

![Crear instancia](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-instance.png)

Especificar una única **dirección URL**. Esta guía usa **demoazureml** : debe elegir algo diferente. Elija la **suscripción** y **región** que desee para la instancia de servicio. Después de realizar las selecciones, haga clic en el botón siguiente.

![Crear-servicio-1](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-1.png)

Especifique un valor para el **Nombre de la organización**. Esta guía usa **demoazureml** : debe elegir algo diferente. Escriba su dirección de correo electrónico en el campo de **correo electrónico del administrador** . Esta dirección de correo electrónico se usa para las notificaciones desde el sistema de administración de la API.

![2 Crear servicio](./media/machine-learning-manage-web-service-endpoints-using-api-management/create-service-2.png)

Haga clic en la casilla de verificación para crear su instancia de servicio. *Se tarda hasta 30 minutos para un nuevo servicio que debe crearse*.

##<a name="create-the-api"></a>Crear la API

Una vez creada la instancia de servicio, el siguiente paso es crear la API. Una API consta de un conjunto de operaciones que se pueden invocar desde una aplicación de cliente. Operaciones de API son proxy de servicios web existentes. Esta guía crea las API de ese proxy a los servicios web de AzureML RRS y BES existentes.

API se crean y se configuran desde el portal de publisher API, que se tiene acceso a través del Portal de clásico de Azure. Para alcanzar el portal de publisher, seleccione la instancia de servicio.

![Seleccionar instancia de servicio](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-service-instance.png)

Haga clic en **Administrar** en el Portal de Azure clásico de su servicio de administración de la API.

![administrar el servicio](./media/machine-learning-manage-web-service-endpoints-using-api-management/manage-service.png)

Haga clic en **las API de** desde el menú de **Administración de la API** de la izquierda y, a continuación, haga clic en **Agregar API**.

![menú de gestión de API](./media/machine-learning-manage-web-service-endpoints-using-api-management/api-management-menu.png)

Escriba **API de demostración de AzureML** como el **nombre de la API de Web**. Escriba **https://ussouthcentral.services.azureml.net** como la **dirección URL del servicio Web**. Escriba **demostración azureml** como la **dirección URL de Web API sufijo**. Active **HTTPS** como la combinación de la **Dirección URL de Web API** . Seleccione **Starter** como **productos**. Cuando haya terminado, haga clic en **Guardar** para crear la API.

![Agregar nuevo-api](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-new-api.png)

##<a name="add-the-operations"></a>Agregar las operaciones

Haga clic en la **operación de agregar** para agregar las operaciones a esta API.

![operación Agregar](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-operation.png)

Se mostrará la ventana de **nueva operación** y se selecciona la ficha **firma** predeterminada.

##<a name="add-rrs-operation"></a>Agregar operación RR

Primero, cree una operación para el servicio de AzureML RRS. Seleccione **Publicar** como el **verbo HTTP**. Tipo de **/services/ /workspaces/ {workspace} {servicio} / execute?api versión = {apiversion} & detalles = {detalles}** como la **plantilla de dirección URL**. Escriba **RR ejecutar** como el **nombre para mostrar**.

![Agregar RR-operación-firma](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

Haga clic en **respuestas** > **Agregar** a la izquierda y seleccione **200 Aceptar**. Haga clic en **Guardar** para guardar esta operación.

![Agregar RR-operación-respuesta](./media/machine-learning-manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

##<a name="add-bes-operations"></a>Agregar operaciones BES

Capturas de pantalla no están incluidos para las operaciones de BES como son muy similares a las de agregar la operación de RR.

###<a name="submit-but-not-start-a-batch-execution-job"></a>Enviar un trabajo de ejecución por lotes (pero no iniciar)

Haga clic en **Agregar** para agregar la operación de AzureML BES a la API. Seleccione **Publicar** para el **verbo HTTP**. Tipo de **/services/ /workspaces/ {workspace} {servicio} / jobs?api versión = {apiversion}** para la **plantilla de dirección URL**. El **nombre para mostrar**, escriba **BES enviar** . Haga clic en **respuestas** > **Agregar** a la izquierda y seleccione **200 Aceptar**. Haga clic en **Guardar** para guardar esta operación.

###<a name="start-a-batch-execution-job"></a>Iniciar una tarea de ejecución por lotes

Haga clic en **Agregar** para agregar la operación de AzureML BES a la API. Seleccione **Publicar** para el **verbo HTTP**. Tipo de **/jobs/ de /services/ {servicio de} /workspaces/ {workspace} {ID} / start?api versión = {apiversion}** para la **plantilla de dirección URL**. El **nombre para mostrar**, escriba **BES iniciar** . Haga clic en **respuestas** > **Agregar** a la izquierda y seleccione **200 Aceptar**. Haga clic en **Guardar** para guardar esta operación.

###<a name="get-the-status-or-result-of-a-batch-execution-job"></a>Obtener el estado o el resultado de una tarea de ejecución por lotes

Haga clic en **Agregar** para agregar la operación de AzureML BES a la API. Haga clic en **obtener** el **verbo HTTP**. Tipo de **?api-versión de /workspaces/ {workspace} /services/ {servicio} /jobs/ {ID} = {apiversion}** para la **plantilla de dirección URL**. Escriba **BES estado** para el **nombre para mostrar**. Haga clic en **respuestas** > **Agregar** a la izquierda y seleccione **200 Aceptar**. Haga clic en **Guardar** para guardar esta operación.

###<a name="delete-a-batch-execution-job"></a>Eliminar una tarea de ejecución por lotes

Haga clic en **Agregar** para agregar la operación de AzureML BES a la API. Para el **verbo HTTP**, seleccione **Eliminar** . Tipo de **?api-versión de /workspaces/ {workspace} /services/ {servicio} /jobs/ {ID} = {apiversion}** para la **plantilla de dirección URL**. El **nombre para mostrar**, escriba **BES eliminar** . Haga clic en **respuestas** > **Agregar** a la izquierda y seleccione **200 Aceptar**. Haga clic en **Guardar** para guardar esta operación.

##<a name="call-an-operation-from-the-developer-portal"></a>Llamar a una operación en el Portal de programadores

Las operaciones se pueden llamar directamente desde el portal de programadores, que proporciona un modo adecuado para ver y probar las operaciones de una API. En este paso Guía llamará la **RR ejecutar** método que se ha agregado a la **API de demostración de AzureML**. Haga clic en el **portal de programadores** en el menú en la parte superior derecha del Portal clásico.

![portal de programadores](./media/machine-learning-manage-web-service-endpoints-using-api-management/developer-portal.png)

Haga clic en **las API de** en el menú superior y, a continuación, haga clic en **AzureML API de demostración** para ver las operaciones disponibles.

![api de demoazureml](./media/machine-learning-manage-web-service-endpoints-using-api-management/demoazureml-api.png)

Seleccione **Ejecutar RR** para la operación. Haga clic en **probar la aplicación**.

![Try it](./media/machine-learning-manage-web-service-endpoints-using-api-management/try-it.png)

Para los parámetros de la solicitud, escriba su **área de trabajo**, **servicio**, **2.0** para el **apiversion**y **true** para los **Detalles**. Puede encontrar el **área de trabajo** y el **servicio** en el panel del servicio web AzureML (consulte **prueba del servicio web** en el apéndice A).

Para los encabezados de la solicitud, haga clic en **Agregar encabezado** y escriba el **Tipo de contenido** y **aplicación/json**, a continuación, haga clic en **Agregar encabezado** y escriba **autorización** y **portador <YOUR AZUREML SERVICE API-KEY> **. Puede encontrar la **clave api** en el panel del servicio web AzureML (consulte **prueba del servicio web** en el apéndice A).

Tipo **{"Entradas": {"Entrada1": {"ColumnNames": ["Col2"], "valores": [["Este es un buen día"]]}}, "GlobalParameters": {}}** para el cuerpo de la solicitud.

![api de demostración de azureml](./media/machine-learning-manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

Haga clic en **Enviar**.

![Enviar](./media/machine-learning-manage-web-service-endpoints-using-api-management/send.png)

Cuando se invoca una operación, el portal de programadores muestra la **Dirección URL solicitada** desde el servicio de back-end, el **estado de respuesta**, los **encabezados de respuesta**y cualquier **contenido de la respuesta**.

![estado de respuesta](./media/machine-learning-manage-web-service-endpoints-using-api-management/response-status.png)

##<a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Apéndice A - crear y probar una AzureML simple servicio web

###<a name="creating-the-experiment"></a>Crear el ensayo

A continuación son los pasos para crear un ensayo AzureML simple e implementar como un servicio web. La toma de servicio web como una columna de texto arbitrario de entrada y devuelve un conjunto de características que se representan como enteros. Por ejemplo:

Texto | Texto con hash
--- | ---
Este es un buen día | 1 1 2 2 0 2 0 1

En primer lugar, mediante un explorador de su elección, vaya a: [https://studio.azureml.net/](https://studio.azureml.net/) e introduzca sus credenciales para iniciar sesión. A continuación, cree un nuevo ensayo en blanco.

![Buscar plantillas de ensayo](./media/machine-learning-manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Cambiarle el nombre a **SimpleFeatureHashingExperiment**. Expandir **Guardado conjuntos de datos** y arrastre el ensayo **Reseñas de libros de Amazon** .

![simple-característica-hash-ensayo](./media/machine-learning-manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Expandir **manipulación** y **Transformación de datos** y arrastre el ensayo **Seleccionar columnas de conjunto de datos** . Conectar **reseñas de libros de Amazon** para **Seleccionar columnas de conjunto de datos**.

![Seleccionar columnas](./media/machine-learning-manage-web-service-endpoints-using-api-management/project-columns.png)

Haga clic en **Seleccionar columnas de conjunto de datos** y, a continuación, haga clic en **iniciar el selector de columna** y seleccione **Col2**. Haga clic en la marca de verificación para aplicar estos cambios.

![Seleccionar columnas](./media/machine-learning-manage-web-service-endpoints-using-api-management/select-columns.png)

Expanda **El análisis de texto** y arrastre **Característica hash** el ensayo. Conectar **Seleccionar columnas de conjunto de datos** a la **función hash**.

![columnas de proyecto conectar](./media/machine-learning-manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Escriba **3** para el **Hashing bitsize**. Esta opción creará 8 (23) columnas.

![bitsize hash](./media/machine-learning-manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

En este momento, desea haga clic en **Ejecutar** para probar el ensayo.

![ejecutar](./media/machine-learning-manage-web-service-endpoints-using-api-management/run.png)

###<a name="create-a-web-service"></a>Crear un servicio web

Crear un servicio web. Expanda **El servicio Web** y arrastre el ensayo **entrada** . Conectar **entrada** a la **función hash**. También puede arrastrar **salida** hasta el ensayo. Conectar **salida** con la **característica hash**.

![hash salida a característica](./media/machine-learning-manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Haga clic en **servicio de publicación web**.

![servicio web publicar](./media/machine-learning-manage-web-service-endpoints-using-api-management/publish-web-service.png)

Haga clic en **Sí** para publicar el ensayo.

![Sí para publicar](./media/machine-learning-manage-web-service-endpoints-using-api-management/yes-to-publish.png)

###<a name="test-the-web-service"></a>Probar el servicio web

Un servicio web de AzureML consta de RSS (servicio de solicitud y respuesta) y extremos de BES (servicio de ejecución de proceso por lotes). RSS es sincrónico ejecución. BES es de ejecución de la tarea asincrónica. Para probar el servicio web con el origen de Python muestra a continuación, tendrá que descargar e instalar el SDK de Azure para Python (vea: [cómo instalar Python](../python-how-to-install.md)).

También necesitará el **área de trabajo**, el **servicio**y **api_key** de su ensayo para el origen de muestra a continuación. Puede encontrar el área de trabajo y el servicio, haga clic en **Convocatoria y respuesta** o **Ejecución por lotes** para el ensayo en el panel de servicio web.

![Buscar área de trabajo y servicio](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Puede encontrar el **api_key** haciendo clic en el ensayo en el panel de servicio web.

![Buscar clave de api](./media/machine-learning-manage-web-service-endpoints-using-api-management/find-api-key.png)

####<a name="test-rrs-endpoint"></a>Extremo de RR de prueba

#####<a name="test-button"></a>Botón de prueba

Una forma sencilla de probar el extremo RR es hacer clic en **probar** en el panel de servicio web.

![prueba](./media/machine-learning-manage-web-service-endpoints-using-api-management/test.png)

Escriba **este es un buen momento** para **col2**. Haga clic en la marca de verificación.

![escribir datos](./media/machine-learning-manage-web-service-endpoints-using-api-management/enter-data.png)

Verá algo parecido a

![resultados de ejemplo](./media/machine-learning-manage-web-service-endpoints-using-api-management/sample-output.png)

#####<a name="sample-code"></a>Código de ejemplo

Otra forma de probar su RR es desde el código de cliente. Si hace clic en **convocatoria y respuesta** en el panel y desplácese hasta la parte inferior, verá el código de ejemplo para C#, Python y R. También verá la sintaxis de la solicitud de RR, incluida la solicitud URI, encabezados y el cuerpo.

Esta guía muestra un ejemplo de Python de trabajo. Debe modificar con el **área de trabajo**, el **servicio**y el **api_key** de su ensayo.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
    data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
    }
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
    headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
    body = str.encode(json.dumps(data))
    try:
        req = urllib2.Request(url, body, headers)
        response = urllib2.urlopen(req)
        result = response.read()
        print "result:" + result
            except urllib2.HTTPError, error:
        print("The request failed with status code: " + str(error.code))
        print(error.info())
        print(json.loads(error.read()))

####<a name="test-bes-endpoint"></a>Prueba BES extremo
En el panel y desplácese hasta la parte inferior, haga clic en **ejecución por lotes** . Verá el código de ejemplo para C#, Python y R. También verá la sintaxis de las solicitudes de BES para enviar un trabajo, iniciar un trabajo, obtener el estado o los resultados de un trabajo y eliminar un trabajo.

Esta guía muestra un ejemplo de Python de trabajo. Necesita modificar con el **área de trabajo**, el **servicio**y el **api_key** de su ensayo. Además, debe modificar el **nombre de la cuenta de almacenamiento**, la **clave de cuenta de almacenamiento**y el **nombre del contenedor de almacenamiento**. Por último, debe modificar la ubicación del **archivo de entrada** y la ubicación del **archivo de salida**.

    import urllib2
    import json
    import time
    from azure.storage import *
    workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
    service = "<REPLACE WITH YOUR SERVICE ID>"
    api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
    storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
    storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
    storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
    input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
    output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
    input_blob_name = "mydatablob.csv"
    output_blob_name = "myresultsblob.csv"
    def printHttpError(httpError):
    print("The request failed with status code: " + str(httpError.code))
    print(httpError.info())
    print(json.loads(httpError.read()))
    return
    def saveBlobToFile(blobUrl, resultsLabel):
    print("Reading the result from " + blobUrl)
    try:
        response = urllib2.urlopen(blobUrl)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    with open(output_file, "w+") as f:
        f.write(response.read())
    print(resultsLabel + " have been written to the file " + output_file)
    return
    def processResults(result):
    first = True
    results = result["Results"]
    for outputName in results:
        result_blob_location = results[outputName]
        sas_token = result_blob_location["SasBlobToken"]
        base_url = result_blob_location["BaseLocation"]
        relative_url = result_blob_location["RelativeLocation"]
        print("The results for " + outputName + " are available at the following Azure Storage location:")
        print("BaseLocation: " + base_url)
        print("RelativeLocation: " + relative_url)
        print("SasBlobToken: " + sas_token)
        if (first):
            first = False
            url3 = base_url + relative_url + sas_token
            saveBlobToFile(url3, "The results for " + outputName)
    return

    def invokeBatchExecutionService():
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
    blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
    print("Uploading the input to blob storage...")
    data_to_upload = open(input_file, "r").read()
    blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
    print "Uploaded the input to blob storage"
    input_blob_path = "/" + storage_container_name + "/" + input_blob_name
    connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
    payload =  {
        "Input": {
            "ConnectionString": connection_string,
            "RelativeLocation": input_blob_path
        },
        "Outputs": {
            "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
        },
        "GlobalParameters": {
        }
    }
        body = str.encode(json.dumps(payload))
    headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
    print("Submitting the job...")
    # submit the job
    req = urllib2.Request(url + "?api-version=2.0", body, headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = response.read()
    job_id = result[1:-1] # remove the enclosing double-quotes
    print("Job ID: " + job_id)
    # start the job
    print("Starting the job...")
    req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    url2 = url + "/" + job_id + "?api-version=2.0"

    while True:
        print("Checking the job status...")
        # If you are using Python 3+, replace urllib2 with urllib.request in the follwing code
        req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
        try:
            response = urllib2.urlopen(req)
        except urllib2.HTTPError, error:
            printHttpError(error)
            return
        result = json.loads(response.read())
        status = result["StatusCode"]
        print "status:" + status
        if (status == 0 or status == "NotStarted"):
            print("Job " + job_id + " not yet started...")
        elif (status == 1 or status == "Running"):
            print("Job " + job_id + " running...")
        elif (status == 2 or status == "Failed"):
            print("Job " + job_id + " failed!")
            print("Error details: " + result["Details"])
            break
        elif (status == 3 or status == "Cancelled"):
            print("Job " + job_id + " cancelled!")
            break
        elif (status == 4 or status == "Finished"):
            print("Job " + job_id + " finished!")
            processResults(result)
            break
        time.sleep(1) # wait one second
    return
    invokeBatchExecutionService()
