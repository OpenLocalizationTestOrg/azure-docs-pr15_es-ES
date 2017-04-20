<properties
    pageTitle="Tutorial de evento Hubs archivar Azure | Microsoft Azure"
    description="Ejemplo que usa el SDK de Python Azure que muestran el uso de la característica de evento Hubs archivo."
    services="event-hubs"
    documentationCenter=""
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="darosa;sethm"/>

# <a name="event-hubs-archive-walkthrough-python"></a>Evento Hubs archivar Tutorial: Python

Evento Hubs archivar es una nueva característica de Hubs de evento que le permite ofrecer automáticamente los datos en secuencia en el Hub de eventos a una cuenta de almacenamiento de blobs de Windows Azure de su elección. Esto facilita realizar procesamiento por lotes en los datos de transmisión por secuencias en tiempo real. Este artículo describe cómo usar el evento Hubs archivar con Python. Para obtener más información sobre el evento Hubs archivar, vea el [artículo información general](event-hubs-archive-overview.md).

En este ejemplo se usa el SDK de Python Azure para mostrar el uso de la característica de archivo. El sender.py envía simulada telemetría ambiental al evento Hubs en formato JSON. El concentrador de evento está configurado para usar la característica de archivo para escribir estos datos para almacenamiento en lotes de blobs. A continuación, la archivereader.py lee estos BLOB y crea un archivo de datos anexados por dispositivo y escribe los datos en archivos CSV.

¿Qué llevar a cabo

1.  Crear una cuenta de almacenamiento de blobs de Windows Azure y un contenedor de blob dentro de ella, con el portal de Azure

2.  Crear un espacio de nombres de concentrador de evento, con el portal de Azure

3.  Crear un concentrador de eventos con la característica de archivo habilitada, con el portal de Azure

4.  Enviar datos al concentrador de eventos con una secuencia de comandos de Python

5.  Leer los archivos desde el archivo y procesar con otra secuencia de comandos de Python

Requisitos previos

1.  Python 2.7.x

2.  Una suscripción de Azure

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-storage-account"></a>Crear una cuenta de almacenamiento de Azure

1.  Inicie sesión en el [portal de Azure][].

2.  En el panel de navegación izquierdo del portal, haga clic en nuevo, a continuación, haga clic en datos + almacenamiento y, a continuación, haga clic en cuenta de almacenamiento.

3.  Complete los campos en el módulo de la cuenta de almacenamiento y haga clic en **crear**.

    ![][1]

4.  Cuando vea el mensaje **Implementaciones se realizó correctamente** , haga clic en la nueva cuenta de almacenamiento y en el módulo de **Essentials** haga clic en **BLOB**. Cuando se abre el módulo de **servicio de blobs de Windows** , haga clic en **+ contenedor** en la parte superior. Asigne un nombre al **archivo**de contenedor, a continuación, cierre el módulo **Blob servicio** .

5.  Haga clic en **las teclas de acceso** en el módulo izquierdo y copiar el nombre de la cuenta de almacenamiento y el valor de **clave1**. Guardar estos valores en el Bloc de notas o cualquier otra ubicación temporal.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Crear un script de Python para enviar eventos a su centro de evento

1.  Abra el editor de Python favorito, como [Código de Visual Studio][].

2.  Crear un script denominado **sender.py**. Esta secuencia de comandos enviará 200 eventos a su centro de evento. Son simples lecturas ambientales enviadas en JSON.

3.  Pegue el código siguiente en sender.py:

    ```
    import uuid
    import datetime
    import random
    import json
    from azure.servicebus import ServiceBusService
    
    sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    for y in range(0,20):
        for dev in devices:
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading)
            sbs.send\_event('myhub', s)
        print y
    ```
4.  Actualice el código para usar el espacio de nombres y valores de clave que se obtienen cuando creó el espacio de nombres de evento Hubs anterior.

## <a name="create-a-python-script-to-read-your-archive-files"></a>Crear un script de Python para leer los archivos de datos

1.  Rellene el módulo y haga clic en **crear**.

2.  Crear un script denominado **archivereader.py**. Este script leerá los archivos de almacenamiento y crear un archivo por dispositivo para escribir los datos solo para ese dispositivo.

3.  Pegue el código siguiente en archivereader.py:

    ```
    import os
    import string
    import json
    import avro.schema
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    from azure.storage.blob import BlockBlobService
    
    def processBlob(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed\_json = json.loads(reading["Body"])
            if not 'id' in parsed\_json:
                return
            if not dict.has\_key(parsed\_json['id']):
            list = []
            dict[parsed\_json['id']] = list
        else:
            list = dict[parsed\_json['id']]
            list.append(parsed\_json)
        reader.close()
        for device in dict.keys():
            deviceFile = open(device + '.csv', "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\\n')

    def startProcessing(accountName, key, container):
        print 'Processor started using path: ' + os.getcwd()
        block\_blob\_service = BlockBlobService(account\_name=accountName, account\_key=key)
        generator = block\_blob\_service.list\_blobs(container)
        for blob in generator:
            if blob.properties.content\_length != 0:
                print('Downloaded a non empty blob: ' + blob.name)
                cleanName = string.replace(blob.name, '/', '\_')
                block\_blob\_service.get\_blob\_to\_path(container, blob.name, cleanName)
                processBlob(cleanName)
                os.remove(cleanName)
            block\_blob\_service.delete\_blob(container, blob.name)
    startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'archive')
    ```

4.  Asegúrese de pegar los valores apropiados para el nombre de la cuenta de almacenamiento y la clave en la llamada a `startProcessing`.

## <a name="run-the-scripts"></a>Ejecutar las secuencias de comandos

1.  Abra un símbolo que tiene Python en su ruta de acceso y luego ejecute estos comandos para instalar paquetes de requisitos previos de Python:

    ```
    pip install azure-storage
    pip install azure-servicebus
    pip install avro
    ```
  
    Si tiene una versión anterior de cualquier almacenamiento de azure o azure que necesite utilizar el **--Actualizar** opción

    También debe ejecutar los siguientes (no es necesario en la mayoría de los sistemas):

    ```
    pip install cryptography
    ```

2.  Cambiar el directorio donde se guarden sender.py y archivereader.py y ejecutar este comando:

    ```
    start python sender.py
    ```
    
    Esto inicia un nuevo proceso de Python para ejecutar el remitente.

3. Ahora, espere unos minutos para el archivo para que se ejecute. A continuación, escriba el comando siguiente en la ventana de comandos original:

    ```
    python archivereader.py
    ```

Procesador de este archivo utiliza el directorio local para descargar todos los BLOB de la cuenta o el contenedor de almacenamiento. Procesará cualquiera que no están vacías y escribir el resultado como archivos .csv en el directorio local.

## <a name="next-steps"></a>Pasos siguientes

Puede obtener más información sobre el evento Hubs visitando los siguientes vínculos:

- [Información general sobre el evento Hubs archivar][]
- Una [aplicación de ejemplo que usa el evento Hubs][]completa.
- Ejemplo de [escalado de procesamiento de evento con Hubs de evento][] .
- [Información general de Hubs de evento][]
 

[Portal de Azure]: https://portal.azure.com/
[Información general sobre evento Hubs archivar]: event-hubs-archive-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]: https://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/
[Código de Visual Studio]: https://code.visualstudio.com/
[Información general de Hubs de evento]: event-hubs-overview.md
[aplicación de ejemplo que usa Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Escalar evento procesamiento con Hubs de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
