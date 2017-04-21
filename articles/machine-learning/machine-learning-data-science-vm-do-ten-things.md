<properties
    pageTitle="Diez cosas que puede hacer en la máquina Virtual de ciencias datos | Microsoft Azure"
    description="Realizar diversos exploración de datos y modelado de tareas en la máquina Virtual de ciencias de datos."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="gokuma;weig;bradsev" />

# <a name="ten-things-you-can-do-on-the-data-science-virtual-machine"></a>Diez cosas que puede hacer en la máquina Virtual de ciencias de datos

La máquina Virtual de ciencias de datos de Microsoft (DSVM) es un entorno de desarrollo de ciencias de datos que permite realizar diversas tareas de exploración y modelado de datos. El entorno ya viene integrado y agrupados con varias herramientas de análisis de datos más conocidos que facilitan la introducción rápida a su análisis para instalaciones, implementaciones de nube o híbrido. La DSVM trabaja con muchos servicios de Azure y puede leer y procesar datos que ya está almacenados en Azure, en el almacén de datos de SQL Azure, lago de datos de Azure, almacenamiento Azure o en DocumentDB. También puede aprovechar otras herramientas de análisis como generador de datos de Azure y Azure el aprendizaje.


En este artículo le guiaremos a través de cómo utilizar su DSVM para realizar diversas tareas de ciencias de datos e interactuar con otros servicios de Azure. Estas son algunas de las cosas que puede hacer en el DSVM:

1. Explorar datos y desarrollar modelos localmente en el DSVM utiliza Microsoft R Server, Python
2. Utilizar un bloc de notas de Jupyter para experimentar con los datos en un explorador mediante Python 2, 3 Python, Microsoft R una versión de empresa listo r diseñado para rendimiento y escalabilidad
3. Controle modelos generados mediante R y Python en Azure el aprendizaje para las aplicaciones cliente pueden tener acceso a los modelos mediante una interfaz de servicios web simple
4. Administrar los recursos de Azure con Azure portal o Powershell
5. Extender el espacio de almacenamiento y compartir conjuntos de datos a gran escala / código a través de todo el equipo mediante la creación de un archivo de almacenamiento de Azure como una unidad montaje en su DSVM
6. Compartir código con su equipo mediante Github y acceder a su repositorio mediante los preinstalado Git clientes - fiesta Git, Git gráfico.
7. Acceso a varios Azure datos y análisis de servicios como almacenamiento de blobs de Windows Azure, lago de datos de Azure, HDInsight de Azure (Hadoop), DocumentDB de Azure, almacenamiento de datos de SQL Azure y bases de datos
8. Generar informes y paneles mediante Power BI Desktop preinstalada en el DSVM e implementar ellos en la nube
9. Escalar dinámicamente su DSVM para satisfacer sus necesidades de proyecto
10. Instalar herramientas adicionales en la máquina virtual   


>[AZURE.NOTE] Para muchos de los servicios de almacenamiento y análisis de datos adicionales mencionados en este artículo, se aplicarán cargos de uso adicionales. Consulte la página de [Precios de Azure](https://azure.microsoft.com/pricing/) para obtener información detallada.


**Requisitos previos**

- Necesitará una suscripción de Azure. Puede registrarse para una gratuita prueba [aquí](https://azure.microsoft.com/free/).

- Instrucciones para el aprovisionamiento de una máquina de ciencias de datos en el portal de Azure están disponibles al [crear una máquina virtual](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).

## <a name="1-explore-data-and-develop-models-using-microsoft-r-server-or-python"></a>1. explorar datos y desarrollar modelos usando Microsoft R Server o Python

Puede utilizar lenguajes como R y Python hacer el análisis de datos en el DSVM.

Para R, puede usar un IDE denominado "Revolución R Enterprise 8.0" que se encuentra en el menú Inicio o el escritorio. Microsoft proporciona bibliotecas adicionales en la parte superior Abrir origen/CRAN-R para habilitar análisis scalable y la capacidad de analizar datos mayores que el tamaño de memoria permitido por la realización de análisis de bloques paralelos. También puede instalar un IDE R de su elección como [RStudio](https://www.rstudio.com/products/rstudio-desktop/).

Para Python, puede usar un IDE como Visual Studio Comunidad Edition que tiene las herramientas de Python para extensión de Visual Studio (PTVS) preinstalado. De forma predeterminada, solo un 2.7 Python básica está configurado en PTVS (sin cualquier biblioteca analytics como SciKit, Pandas). Para habilitar Anaconda Python 2.7 y 3.5, debe hacer lo siguiente:

* Crear entornos personalizados para cada versión, vaya a **Herramientas** -> **Herramientas Python** -> **Entornos de Python** y, a continuación, haciendo clic en "**+ personalizado**" en la edición de comunidad de Visual Studio de 2015
* Proporcionar una descripción y establecer las rutas de acceso de entorno prefijo como *c:\anaconda* para Anaconda Python 2.7 o *c:\anaconda\envs\py35* para Anaconda Python 3.5
* Haga clic en **Detectar automáticamente** y, a continuación, **Aplicar** para guardar el entorno.

Aquí es el aspecto de la configuración de entorno personalizado en Visual Studio.

![Configuración PTVS](./media/machine-learning-data-science-vm-do-ten-things/PTVSSetup.png)

Consulte la [documentación de PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) para obtener más información sobre cómo crear entornos de Python.

Ahora se configurarán para crear un nuevo proyecto de Python. Vaya al **archivo** -> **nuevo** -> **proyecto** -> **Python** y seleccione el tipo de aplicación de Python que está creando. Puede configurar el entorno de Python para el proyecto actual a la versión deseada (Anaconda 2.7 o 3.5): haga clic en el **entorno de Python**, seleccione **Agregar o quitar Python entornos**y, a continuación, seleccione el entorno que desee asociar con el proyecto. Puede encontrar más información sobre cómo trabajar con PTVS en la página de la [documentación del](https://github.com/Microsoft/PTVS/wiki) producto.

## <a name="2-using-a-jupyter-notebook-to-explore-and-model-your-data-with-python-or-r"></a>2. con un bloc de notas de Jupyter para explorar y del modelo de datos con Python o R

El Bloc de notas de Jupyter es un entorno eficaz que proporciona un basadas en navegador, "IDE" para la exploración de datos y modelado. Puede usar Python 2, 3 Python o R (Abrir origen y el servidor de Microsoft R) en un bloc de notas de Jupyter.

Para iniciar el clic de Bloc de notas de Jupyter en el icono del menú Inicio o del icono titulado **Jupyter Bloc de notas**. En la DSVM también puede ir a "https://localhost:9999 /" para tener acceso a los blocs de notas de Júpiter. Si le pide una contraseña, use instrucciones que aparecen en la sección ***cómo crear una contraseña segura en el servidor de Bloc de notas de Jupyter*** del tema [aprovisionar la máquina Virtual de Microsoft datos ciencias](machine-learning-data-science-provision-vm.md) para crear una contraseña segura para tener acceso a los blocs de notas de Jupyter. 

Una vez que ha abierto el Bloc de notas, verá un directorio que contiene unos blocs de notas de ejemplo que están preconfiguradas en la DSVM. Ahora puede:

- Haga clic en el Bloc de notas para ver el código.
- ejecutar cada celda, presione **MAYÚS+ENTRAR**.
- ejecutar el Bloc de notas completo haciendo clic en la **celda** -> **Ejecutar**
- crear un nuevo bloc de notas haciendo clic en el icono de Jupyter (esquina superior izquierda) y haga clic en botón **nuevo** de la derecha y, a continuación, elegir el idioma del Bloc de notas (también conocido como kernels).   


>[AZURE.NOTE] Actualmente se admiten Python 2.7, Python 3.5 y R. El núcleo R admite la programación en Abrir origen R así como la empresa scalable Microsoft R Server.   


Una vez que se encuentra en el Bloc de notas que puede explorar los datos, crear el modelo, probar el modelo de su elección de bibliotecas.


## <a name="3-build-models-using-r-or-python-and-operationalize-them-using-azure-machine-learning"></a>3. crear modelos con R o Python y operativa mediante el aprendizaje Azure

Una vez que ha creado y validar un modelo el siguiente paso es normalmente implementar en producción. Esto permite a su cliente aplicaciones invocar las predicciones de modelo en un tiempo real o en una base de modo por lotes. Aprendizaje de máquina Azure proporciona un mecanismo para controle un modelo integrado en R o Python.

Cuando controle su modelo de aprendizaje del equipo de Azure, se expone un servicio web que permite a los clientes realizar llamadas de resto que pasan parámetros de entrada y reciban predicciones del modelo como salidas.   


>[AZURE.NOTE] Si no tiene todavía dispone de AzureML, puede obtener un área de trabajo libre o un área de trabajo estándar visitar la página de inicio de [AzureML Studio](https://studio.azureml.net/) y haciendo clic en "Introducción".   


### <a name="build-and-operationalize-python-models"></a>Generar y operativa Python modelos

Aquí tiene un fragmento de código desarrollado en un bloc de notas de Python Jupyter que genera un modelo simple mediante la biblioteca de obtener SciKit.

    #IRIS classification
    from sklearn import datasets
    from sklearn import svm
    clf = svm.SVC()
    iris = datasets.load_iris()
    X, y = iris.data, iris.target
    clf.fit(X, y)

El método usado para implementar los modelos de python aprendizaje del equipo de Azure ajusta la predicción del modelo en una función y decora con atributos proporcionados por la biblioteca de python Azure aprendizaje preinstalada que denotan el identificador del área de trabajo de aprendizaje del equipo de Azure, la clave de API y la entrada y devuelven parámetros.  

    from azureml import services
    @services.publish(workspaceid, auth_token)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(int) #0, or 1, or 2
    def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]
    return clf.predict(inputArray)

Un cliente ahora puede realizar llamadas al servicio web. Hay contenedores de comodidad que las solicitudes de la API de REST de construcción. Aquí es un código de ejemplo para utilizar el servicio web.

    # Consume through web service URL and keys
    from azureml import services
    @services.service(url, api_key)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(float)
    def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
    pass

    IrisPredictor(3,2,3,4)


>[AZURE.NOTE] La biblioteca de aprendizaje del equipo de Azure solo es compatible actualmente en Python 2.7.   


### <a name="build-and-operationalize-r-models"></a>Modelos de generación y operativa R

Puede implementar modelos R integrados en la máquina Virtual de ciencias de datos o cualquier otro sitio en Azure el aprendizaje de manera similar a cómo se hace para Python. Su son los pasos:

- crear un archivo de settings.json como siguientes para proporcionar el identificador de área de trabajo y auth token.
- escribir un contenedor para el modelo de la función de predecir.
- llamar a ```publishWebService``` en la biblioteca de Azure el aprendizaje para pasar en el contenedor de la función.  

Aquí tiene los fragmentos de código de procedimiento y el código que se pueden usar para configurar, crear, publicar y consumir un modelo como un servicio web de aprendizaje del equipo de Azure.

#### <a name="setup"></a>Programa de instalación

1.  Instalar el paquete de AzureML R escribiendo ```install.packages("AzureML")``` en revolución R Enterprise 8.0 IDE o su IDE R.
2.  Descargar RTools desde [aquí](https://cran.r-project.org/bin/windows/Rtools/). Necesita la utilidad zip en la ruta de acceso (y zip.exe con nombre) que controle el paquete R en AzureML.
3.  Crear un archivo settings.json en un directorio denominado ```.azureml``` en el directorio de inicio y escriba los parámetros del área de trabajo de Azure m:

Settings.JSON estructura de archivo:

    {"workspace":{
    "id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
    "authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
    }}


#### <a name="build-a-model-in-r-and-publish-it-in-azure-ml"></a>Cree un modelo de R y publicar en Azure m

    library(AzureML)
    ws <- workspace(config="~/.azureml/settings.json")

    if(!require("lme4")) install.packages("lme4")
    library(lme4)
    set.seed(1)
    train <- sleepstudy[sample(nrow(sleepstudy), 120),]
    m <- lm(Reaction ~ Days + Subject, data = train)

    # Define a prediction function to publish based on the model:
    sleepyPredict <- function(newdata){
        predict(m, newdata=newdata)
    }

    ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)

#### <a name="consume-the-model-deployed-in-azure-ml"></a>Utilizar el modelo de implementada en Azure m

Para utilizar el modelo desde una aplicación de cliente, usamos la biblioteca de Azure el aprendizaje para buscar el servicio web publicado por nombre mediante el `services` llamada a la API para determinar el extremo. A continuación, se llama a la `consume` de función y pase el marco de datos para predecir.
El código siguiente se utiliza para consumir el modelo publicado como un servicio web de aprendizaje del equipo de Azure.


    library(AzureML)
    library(lme4)
    ws <- workspace(config="~/.azureml/settings.json")

    s <-  services(ws, name = "sleepy lm")
    s <- tail(s, 1) # use the last published function, in case of duplicate function names

    ep <- endpoints(ws, s)

    # OK, try this out, and compare with raw data
    ans = consume(ep, sleepstudy)$ans

Puede encontrar más información acerca de la biblioteca de Azure máquina aprendizaje R [aquí](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf).


## <a name="4-administer-your-azure-resources-using-azure-portal-or-powershell"></a>4. administrar los recursos de Azure con Azure portal o Powershell

La DSVM no sólo le permite crear la solución de análisis localmente en la máquina virtual, sino que también le permite acceder a los servicios de nube de Microsoft Azure. Azure proporciona varias cálculo, almacenamiento, servicios de análisis de datos y otros servicios que se pueden administrar y obtener acceso desde su DSVM.

Para administrar los recursos de suscripción y nube de Azure puede usar el explorador y seleccione el [portal de Azure](https://portal.azure.com). También puede usar Azure Powershell para administrar su suscripción de Azure y los recursos a través de una secuencia de comandos.
Puede ejecutar Azure Powershell desde un acceso directo en el escritorio o desde el menú Inicio titulado "Microsoft Azure Powershell". Para obtener más información sobre cómo puede administrar su suscripción de Azure y recursos mediante secuencias de comandos de Windows Powershell, consulte la [documentación de Microsoft Azure Powershell](../powershell-azure-resource-manager.md) .


## <a name="5-extend-your-storage-space-with-a-shared-file-system"></a>5. ampliar el espacio de almacenamiento con un sistema de archivos compartidos

Pueden compartir científicos datos grandes conjuntos de datos, código u otros recursos dentro del equipo. La DSVM propio tiene unos 70GB de espacio disponible. Para ampliar el almacenamiento, puede usar el servicio de archivo de Azure y cualquiera montaje en la DSVM o acceder a través de la API de REST.   


>[AZURE.NOTE] El espacio máximo de la cuota de servicio de archivo de Azure es 5TB y límite de tamaño de archivo individual es 1TB.   


Puede usar Powershell Azure para crear un recurso compartido de servicio de archivo de Azure. Aquí tiene el script para que se ejecute en Azure PowerShell para crear un recurso compartido de servicio de archivo de Azure.

    # Authenticate to Azure.
    Login-AzureRmAccount
    # Select your subscription
    Get-AzureRmSubscription –SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
    # Create a new resource group.
    New-AzureRmResourceGroup -Name <dsvmdatarg>
    # Create a new storage account. You can reuse existing storage account if you wish.
    New-AzureRmStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
    # Set your current working storage account
    Set-AzureRmCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

    # Create a Azure File Service Share
    $s = New-AzureStorageShare <<teamsharename>>
    # Create a directory under the FIle share. You can give it any name
    New-AzureStorageDirectory -Share $s -Path <directory name>
    # List the share to confirm that everything worked
    Get-AzureStorageFile -Share $s


Ahora que ha creado un recurso compartido de archivos de Azure, se puede montaje en cualquier máquina virtual en Azure. Se recomienda que la máquina virtual se encuentra en el mismo centro de datos de Azure como la cuenta de almacenamiento para evitar gastos de transferencia de latencia y los datos. Aquí tiene los comandos de la unidad de montaje en el DSVM que se puede ejecutar en Azure Powershell.


    # Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
    cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

    # Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
    net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>


Ahora puede tener acceso a esta unidad como lo haría con cualquier unidad normal en la máquina virtual.

## <a name="6-share-code-with-your-team-using-github"></a>6. código de compartir con su equipo mediante Github

Github es un repositorio de código donde puede encontrar una gran cantidad de código de ejemplo y orígenes de distintas herramientas usando diversas tecnologías compartidas por la Comunidad de desarrolladores. Utiliza Git como la tecnología para realizar un seguimiento y almacenar versiones de los archivos de código. Github es también una plataforma donde puede crear su propio repositorio para almacenar código compartido y la documentación de su equipo, implementar el control de versiones y también controlar que tienen acceso para ver y contribuir código. Visite la [Github páginas de ayuda](https://help.github.com/) para obtener más información acerca de cómo utilizar Git. Puede usar Github como una de las formas para colaborar con su equipo, use el código desarrollado por la Comunidad y aportar código volver a la Comunidad.

El DSVM ya viene cargado con herramientas de cliente en ambos línea de comandos como gráfico bien acceder Github repositorio. La herramienta de línea de comandos para trabajar con Git y Github se denomina Git intensiva. Visual Studio instalado en el DSVM tiene las extensiones de Git. Puede encontrar iconos de inicio para estas herramientas en el menú Inicio y el escritorio.

Para descargar el código de un repositorio de Github usará el ```git clone``` comando. Por ejemplo descargar repositorio de ciencias datos publicado por Microsoft en el directorio actual puede ejecutar el comando siguiente una vez que se encuentra en ```git-bash```.

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

En Visual Studio, puede realizar la misma operación de clonar. La captura de pantalla de abajo muestra cómo tener acceso a herramientas Git y Github en Visual Studio.

![GIT en Visual Studio](./media/machine-learning-data-science-vm-do-ten-things/VSGit.PNG)


Puede encontrar más información sobre cómo usar Git para trabajar con su repositorio Github desde varios recursos disponibles en github.com. La [hoja de referencia sobre](https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf) es una referencia útil.


## <a name="7-access-various-azure-data-and-analytics-services"></a>7. acceder a los diversos servicios de datos y análisis de Azure

### <a name="azure-blob"></a>Blobs de Windows Azure

Blobs de Windows Azure es un nube confiable y económico de almacenamiento de datos grandes y pequeños. Veamos cómo mover los datos a blobs de Windows Azure y acceso a datos almacenados en un blobs de Windows Azure.

**Requisito previo**

- **Crear su cuenta de almacenamiento de blobs de Windows Azure desde [el portal de Azure](https://portal.azure.com).**

![Create_Azure_Blob](./media/machine-learning-data-science-vm-do-ten-things/Create_Azure_Blob.PNG)


- Confirme que la herramienta de línea de comandos preinstalado AzCopy se encuentra en ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. Puede agregar el directorio que contiene el azcopy.exe a la variable de entorno de ruta de acceso para evitar tener que escribir la ruta de acceso completa del comando al ejecutar esta herramienta. Para obtener más información sobre la herramienta de AzCopy, consulte la documentación de [AzCopy](../storage/storage-use-azcopy.md)

- Inicie la herramienta Explorador de almacenamiento de Azure. Puede descargarse desde [El Explorador de almacenamiento de Microsoft Azure](http://storageexplorer.com/). 

![AzureStorageExplorer_v4](./media/machine-learning-data-science-vm-do-ten-things/AzureStorageExplorer_v4.png)


**Mover los datos de máquina virtual para blobs de Windows Azure: AzCopy**

Para mover datos entre sus archivos locales y el almacenamiento de blobs de Windows, puede usar AzCopy en la línea de comandos o PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Reemplace **C:\MyFolder** a la ruta de acceso donde está almacenado el archivo, **mystorageaccount** su nombre de cuenta de almacenamiento de blobs, **mycontainer** al nombre del contenedor, **clave de cuenta de almacenamiento** para la clave de acceso de almacenamiento de blobs. Puede encontrar sus credenciales de cuenta de almacenamiento en [el portal de Azure](https://portal.azure.com).

![StorageAccountCredential_v2](./media/machine-learning-data-science-vm-do-ten-things/StorageAccountCredential_v2.png)

Ejecutar comando AzCopy en PowerShell o desde un símbolo del sistema. Hay algunos ejemplos de uso de AzCopy comando:


    # Copy *.sql from local machine to a Azure Blob
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

    # Copy back all files from Azure Blob container to Local machine

    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S



Una vez que ejecute el comando AzCopy para copiar a un blobs de Windows Azure verá el archivo se muestra en el Explorador de almacenamiento de Azure breve.

![AzCopy_run_finshed_Storage_Explorer_v3](./media/machine-learning-data-science-vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)


**Mover los datos de máquina virtual para blobs de Windows Azure: Explorador de almacenamiento de Azure**

También puede cargar datos del archivo local en su máquina virtual con el Explorador de almacenamiento de Azure:

- Para cargar los datos en un contenedor, seleccione el contenedor de destino y haga clic en el botón **cargar** .![](./media/machine-learning-data-science-vm-do-ten-things/storage-accounts.png)
- Haga clic en el **...** a la derecha del cuadro de **archivos** , seleccione uno o varios archivos para cargar desde el sistema de archivos y haga clic en **cargar** para empezar a cargar los archivos.![](./media/machine-learning-data-science-vm-do-ten-things/upload-files-to-blob.png)


**Leer datos de blobs de Windows Azure: módulo de lector AML**

En Azure máquina aprendizaje Studio puede usar un **módulo de importar datos** para leer los datos de su blob.


![AML_ReaderBlob_Module_v3](./media/machine-learning-data-science-vm-do-ten-things/AML_ReaderBlob_Module_v3.png)


**Leer datos de blobs de Windows Azure: Python ODBC**

Puede usar **BlobService** biblioteca para leer datos directamente de blob en un programa de Bloc de notas de Jupyter o Python.

En primer lugar, importar paquetes necesarios:

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random

A continuación, conecte las credenciales de cuenta de blobs de Windows Azure y leer los datos de Blob:

    CONTAINERNAME = 'xxx'
    STORAGEACCOUNTNAME = 'xxxx'
    STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
    BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
    localfilename = 'trip_data_1.csv'
    LOCALDIRECTORY = os.getcwd()
    LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

    #download from blob
    t1 = time.time()
    blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
    t2 = time.time()
    print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

    #unzipping downloaded files if needed
    #with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
    #    z.extractall(LOCALDIRECTORY)

    df1 = pd.read_csv(LOCALFILE, header=0)
    df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
    print 'the size of the data is: %d rows and  %d columns' % df1.shape

Los datos se leen en como una trama de datos:

![IPNB_data_readin](./media/machine-learning-data-science-vm-do-ten-things/IPNB_data_readin.PNG)


### <a name="azure-data-lake"></a>Lago de datos de Azure

Almacenamiento de Azure datos lago es un repositorio de gran escala de cargas de trabajo de análisis de datos grande y compatible con sistema de archivos de distribuido de Hadoop (HDFS). Funciona con el ecosistema Hadoop y el análisis de lago de datos de Azure. Le mostraremos cómo puede mover los datos en el almacén de lago de datos de Azure y ejecutar análisis con análisis de lago de datos de Azure.

**Requisito previo**

- Crear su análisis de lago de datos de Azure en el [portal de Azure](https://portal.azure.com).

![Azure_Data_Lake_Create_v2](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_Create_v2.png)


- Las **Herramientas de lago de datos de Azure** en **Visual Studio** se encuentra en este [vínculo](https://www.microsoft.com/download/details.aspx?id=49504) ya está instalado en Visual Studio Comunidad Edition que se encuentra en la máquina virtual. Después de iniciar Visual Studio e iniciar sesión en su suscripción de Azure, verá su cuenta de análisis de datos de Azure y el almacenamiento en el panel izquierdo de Visual Studio.

![Azure_Data_Lake_PlugIn_v2](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)


**Mover los datos de VM a datos lago: Explorador de lago de datos de Azure**

Puede usar **El Explorador de lago de datos de Azure** para cargar los datos de los archivos locales en la máquina Virtual al almacenamiento de datos lago.

![Azure_Data_Lake_UploadData](./media/machine-learning-data-science-vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

También puede crear una canalización de datos para productionize el movimiento de datos o de lago de datos de Azure mediante la [Factory(ADF) de datos de Azure](https://azure.microsoft.com/services/data-factory/). Se nos referimos a este [artículo](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) que le guiará por los pasos para crear canalizaciones de datos.

**Leer datos de blobs de Windows Azure a datos lago: U SQL**

Si los datos residen en el almacenamiento de blobs de Windows Azure, puede leer datos directamente de blobs de Windows Azure almacenamiento en la consulta SQL U. Antes de redactar la consulta SQL U, asegúrese de que su cuenta de almacenamiento de blobs está vinculado a su lago de datos de Azure. Vaya al **portal de Azure**, buscar el panel de análisis de lago de datos de Azure, haga clic en **Agregar origen de datos**, seleccione el tipo de almacenamiento para **El almacenamiento de Azure** y conecte el nombre de la cuenta de Azure almacenamiento y la clave. A continuación, podrá hacer referencia a los datos almacenados en la cuenta de almacenamiento.

![](./media/machine-learning-data-science-vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)


En Visual Studio, puede leer los datos de almacenamiento de blobs, realice alguna manipulación de datos, ingeniería de características y enviar los datos resultantes lago de datos de Azure o el almacenamiento de blobs de Windows Azure. Cuando se hace referencia a los datos de almacenamiento de blobs, use **wasb: / /**; Cuando se hace referencia a los datos de lago de datos de Azure, use **swbhdfs: / /**

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_Read_Blob_v2.PNG)

Puede usar las siguientes consultas SQL U en Visual Studio:

    @a =
        EXTRACT medallion string,
                hack_license string,
                vendor_id string,
                rate_code string,
                store_and_fwd_flag string,
                pickup_datetime string,
                dropoff_datetime string,
                passenger_count int,
                trip_time_in_secs double,
                trip_distance double,
                pickup_longitude string,
                pickup_latitude string,
                dropoff_longitude string,
                dropoff_latitude string

        FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
        USING Extractors.Csv();

    @b =
        SELECT vendor_id,
        COUNT(medallion) AS cnt_medallion,
        SUM(passenger_count) AS cnt_passenger,
        AVG(trip_distance) AS avg_trip_dist,
        MIN(trip_distance) AS min_trip_dist,
        MAX(trip_distance) AS max_trip_dist,
        AVG(trip_time_in_secs) AS avg_trip_time
        FROM @a
        GROUP BY vendor_id;

    OUTPUT @b   
    TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
    USING Outputters.Csv();

    OUTPUT @b   
    TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
    USING Outputters.Csv();



Después de la consulta se envía al servidor, se mostrará un diagrama que muestra el estado de su trabajo.

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_Job_Status.PNG)


**Consultar datos en datos lago: U SQL**

Después de que el conjunto de datos es recopilar en lago de datos de Azure, puede usar [lenguaje SQL U](../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) a la consulta y explorar los datos. Lenguaje SQL U es similar a SQL T, pero combina algunas características de C# para que los usuarios pueden escribir módulos personalizados, las funciones definidas por el usuario y etcetera. Puede usar las secuencias de comandos en el paso anterior.

Después de la consulta se envía al servidor, tripdata_summary. CSV puede encontrarse breve en el **Explorador de lago de datos de Azure**, puede obtener una vista previa los datos con el botón secundario del archivo.

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_create_summary.png)

Para ver la información del archivo:

![](./media/machine-learning-data-science-vm-do-ten-things/USQL_tripdata_summary.png)


### <a name="hdinsight-hadoop-clusters"></a>HDInsight Hadoop clústeres

HDInsight de Azure es un servicio Apache Hadoop, motor, HBase y tormenta administrado en la nube. Puede trabajar fácilmente con HDInsight de Azure clústeres de la máquina virtual de ciencias de datos.

**Requisito previo**

- Crear su cuenta de almacenamiento de blobs de Windows Azure desde [el portal de Azure](https://portal.azure.com). Esta cuenta de almacenamiento se usa para almacenar datos para clústeres HDInsight.

![](./media/machine-learning-data-science-vm-do-ten-things/Create_Azure_Blob.PNG)

- Personalizar Azure HDInsight Hadoop clústeres desde [el portal de Azure](machine-learning-data-science-customize-hadoop-cluster.md)

  - Debe vincular creada con el clúster de HDInsight cuando se crea la cuenta de almacenamiento. Esta cuenta de almacenamiento se usa para obtener acceso a los datos que se pueden procesar dentro del clúster.

![](./media/machine-learning-data-science-vm-do-ten-things/Create_HDI_v4.PNG)

  - Debe habilitar el **Acceso remoto** al nodo principal del clúster después de crearlo. Recuerde las credenciales de acceso remoto que especifique aquí (diferentes a las especificadas para el clúster en su creación): los necesitará a continuación.

![](./media/machine-learning-data-science-vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

  - Crear un área de trabajo de Azure m. La experimentación de aprendizaje de máquina se almacenarán en esta área de trabajo de m. Seleccione las opciones resaltadas en Portal tal como se muestra en la siguiente captura de pantalla.

![](./media/machine-learning-data-science-vm-do-ten-things/Create_ML_Space.PNG)


  - A continuación, especifique los parámetros para el área de trabajo de Azure m

![](./media/machine-learning-data-science-vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

  - Cargar datos mediante el Bloc de notas de IPython. Primero importe los paquetes necesarios, conéctese credenciales, crear una base de datos en su cuenta de almacenamiento y después cargar datos HDI clústeres.


        #Import required Packages
        import pyodbc
        import time as time
        import json
        import os
        import urllib
        import urllib2
        import warnings
        import re
        import pandas as pd
        import matplotlib.pyplot as plt
        from azure.storage.blob import BlobService
        warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


        #Create the connection to Hive using ODBC
        SERVER_NAME='xxx.azurehdinsight.net'
        DATABASE_NAME='nyctaxidb'
        USERID='xxx'
        PASSWORD='xxxx'
        DB_DRIVER='Microsoft Hive ODBC Driver'
        driver = 'DRIVER={' + DB_DRIVER + '}'
        server = 'Host=' + SERVER_NAME + ';Port=443'
        database = 'Schema=' + DATABASE_NAME
        hiveserv = 'HiveServerType=2'
        auth = 'AuthMech=6'
        uid = 'UID=' + USERID
        pwd = 'PWD=' + PASSWORD
        CONNECTION_STRING = ';'.join([driver,server,database,hiveserv,auth,uid,pwd])
        connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
        cursor=connection.cursor()


        #Create Hive database and tables
        queryString = "create database if not exists nyctaxidb;"
        cursor.execute(queryString)

        queryString = """
                        create external table if not exists nyctaxidb.trip
                        (
                            medallion string,
                            hack_license string,
                            vendor_id string,
                            rate_code string,
                            store_and_fwd_flag string,
                            pickup_datetime string,
                            dropoff_datetime string,
                            passenger_count int,
                            trip_time_in_secs double,
                            trip_distance double,
                            pickup_longitude double,
                            pickup_latitude double,
                            dropoff_longitude double,
                            dropoff_latitude double)  
                        PARTITIONED BY (month int)
                        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                        STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
                    """
        cursor.execute(queryString)

        queryString = """
                        create external table if not exists nyctaxidb.fare
                        (
                            medallion string,
                            hack_license string,
                            vendor_id string,
                            pickup_datetime string,
                            payment_type string,
                            fare_amount double,
                            surcharge double,
                            mta_tax double,
                            tip_amount double,
                            tolls_amount double,
                            total_amount double)
                        PARTITIONED BY (month int)
                        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                        STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
                    """
        cursor.execute(queryString)


        #Upload data from blob storage to HDI cluster
        for i in range(1,13):
            queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);"%(i,i)
            cursor.execute(queryString)
            queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);"%(i,i)  
            cursor.execute(queryString)


- Como alternativa, puede seguir este [tutorial](machine-learning-data-science-process-hive-walkthrough.md) para cargar los datos de Nueva York Taxi en clúster HDI. Los pasos principales incluyen:

    - AzCopy: descargar del comprimido CSV de blobs pública a su carpeta local
    - AzCopy: cargar descomprimido CSV desde la carpeta local a clúster HDI
    - Inicie sesión en el nodo principal del clúster de Hadoop y prepararse para el análisis de datos de exploración

Después de que los datos se cargan a clúster HDI, puede comprobar los datos en el Explorador de almacenamiento de Azure. Y tiene una nyctaxidb de base de datos creado en el clúster HDI.


**Exploración de datos: consultas de subárbol en Python**

Puesto que los datos están en el clúster de Hadoop, puede usar el paquete de pyodbc para conectarse a clústeres Hadoop y consulta de base de datos mediante subárbol hacer exploración y ingeniería de características. Puede ver las tablas existentes que se creó en el paso como requisito previo.

    queryString = """
        show tables in nyctaxidb2;
        """
    pd.read_sql(queryString,connection)


![](./media/machine-learning-data-science-vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Echemos un vistazo a la cantidad de registros en cada mes y la frecuencia de superpuesto o no en la tabla de ida y vuelta:

    queryString = """
        select month, count(*) from nyctaxidb.trip group by month;
        """
    results = pd.read_sql(queryString,connection)

    %matplotlib inline

    results.columns = ['month', 'trip_count']
    df = results.copy()
    df.index = df['month']
    df['trip_count'].plot(kind='bar')


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)


    queryString = """
        SELECT tipped, COUNT(*) AS tip_freq
        FROM
        (
            SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
            FROM nyctaxidb.fare
        )tc
        GROUP BY tipped;
        """
    results = pd.read_sql(queryString,connection)

    results.columns = ['tipped', 'trip_count']
    df = results.copy()
    df.index = df['tipped']
    df['trip_count'].plot(kind='bar')


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Podemos también calcular la distancia entre la ubicación de origen y la ubicación de caída y compárelo con la distancia de viaje.

    queryString = """
                    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                        3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                        *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                        *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                        +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                        pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                        from nyctaxidb.trip
                        where month=1
                            and pickup_longitude between -90 and -30
                            and pickup_latitude between 30 and 90
                            and dropoff_longitude between -90 and -30
                            and dropoff_latitude between 30 and 90;
                """
    results = pd.read_sql(queryString,connection)
    results.head(5)


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

    results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                       'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
    df = results.loc[results['trip_distance']<=100] #remove outliers
    df = df.loc[df['direct_distance']<=100] #remove outliers
    plt.scatter(df['direct_distance'], df['trip_distance'])


![](./media/machine-learning-data-science-vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)


Ahora vamos a preparar un conjunto de (1%) muestra abajo de datos para un modelado. Podemos utilizar estos datos en el módulo de lector AML.


        queryString = """
        create  table if not exists nyctaxi_downsampled_dataset_testNEW (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\\n'
        stored as textfile;
        """
        cursor.execute(queryString)

        --- now insert contents of the join into the above internal table

        queryString = """
        insert overwrite table nyctaxi_downsampled_dataset_testNEW
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
        *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
        +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
        rand() as sample_key

        from trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01
        """
        cursor.execute(queryString)

Después de un tiempo, puede ver que los datos se ha cargado en clústeres Hadoop:

    queryString = """
        select * from nyctaxi_downsampled_dataset limit 10;
        """
    cursor.execute(queryString)
    pd.read_sql(queryString,connection)


![](./media/machine-learning-data-science-vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)


**Leer los datos de HDI con AML: módulo de lector**

También puede usar el módulo de **lector** en studio AML para tener acceso a la base de datos en el clúster de Hadoop. Conecte las credenciales de su cuenta de almacenamiento de Azure y HDI clústeres y podrá crear modelos de aprendizaje de equipo con la base de datos en clústeres HDI.

![](./media/machine-learning-data-science-vm-do-ten-things/AML_Reader_Hive.PNG)

A continuación, se puede ver el conjunto de datos puntuado:

![](./media/machine-learning-data-science-vm-do-ten-things/AML_Model_Results.PNG)


### <a name="azure-sql-data-warehouse--databases"></a>Almacén de datos SQL de Azure y bases de datos

Almacén de datos de SQL Azure es un almacén de datos elástico como un servicio con experiencia con SQL Server empresariales.

Puede aprovisionar el almacén de datos de SQL Azure siguiendo las instrucciones proporcionadas en este [artículo](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Una vez que se aprovisiona el almacén de datos de SQL Azure, puede usar este [tutorial](machine-learning-data-science-process-sqldw-walkthrough.md) para hacer la carga de datos, exploración y modelado de uso de datos en el almacén de datos de SQL.

#### <a name="azure-documentdb"></a>DocumentDB de Azure

DocumentDB Azure es una base de datos NoSQL en la nube. Le permite trabajar con documentos como JSON y permite almacenar y los documentos de la consulta.

Debe realizar los siguientes pasos por requisitos para acceder a DocumentDB desde el DSVM.

1. Instalar DocumentDB Python SDK (ejecutar ```pip install pydocumentdb``` desde el símbolo del sistema)
1. Crear cuenta de DocumentDB y base de datos de documento DB desde [el portal de Azure](https://portal.azure.com)
1. Descargar la "Herramienta de migración de DocumentDB" desde [aquí](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) y extraer en un directorio de su elección
1. Importar datos JSON (datos volcano) almacenados en un [objeto binario pública](https://cahandson.blob.core.windows.net/samples/volcano.json) en DocumentDB siguientes parámetros de comando a la herramienta de migración (dtui.exe desde el directorio donde ha instalado la herramienta de migración de DocumentDB). Escriba el origen y de los siguientes parámetros de la ubicación de destino.

    /s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/; AccountKey = [[clave]; base de datos = volcano /t.Collection:volcano1

Después de importar los datos, puede ir a Jupyter y abra el Bloc de notas titulado *DocumentDBSample* que contiene el código de python para acceder a DocumentDB y hacer algunas consultas básicas. Puede obtener más información sobre DocumentDB con una visita a la [página de la documentación](https://azure.microsoft.com/documentation/learning-paths/documentdb/) del servicio


## <a name="8-build-reports-and-dashboard-using-the-power-bi-desktop"></a>8. crear informes y paneles con Power BI Desktop

Deje que nosotros visualizar el archivo Volcano JSON que hemos visto en el ejemplo DocumentDB encima de Power BI para obtener recomendaciones visuales de los datos. Los pasos detallados están disponibles en el [artículo de Power BI](../documentdb/documentdb-powerbi-visualize.md). Los pasos de alto niveles se muestran a continuación:

1. Abra Power BI Desktop y realice "Obtener datos". Especifique la dirección URL como: https://cahandson.blob.core.windows.net/samples/volcano.json
2. Debería ver los registros JSON importados como una lista
3. Convertir la lista en una tabla para que PowerBI pueda trabajar con el mismo
4. Haga clic en el icono de expandir (aquella con el icono "flecha izquierda y una flecha derecha" a la derecha de la columna) para expandir las columnas
5. Observe que la ubicación es un campo de "Registro". Expanda el registro y seleccione solo las coordenadas. Coordenadas es una columna de lista
6. Agregar una nueva columna para convertir la columna coordenadas de lista en una columna de LatLong independiente de coma concatenar los dos elementos en el campo de lista coordenadas usando la fórmula ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Por último convertir la ```Elevation``` columna a Decimal y seleccione **Cerrar** y **Aplicar**.

En lugar de los pasos anteriores, puede pegar el código siguiente que secuencias de comandos de salida de los pasos anteriores en el Editor avanzado en PowerBI que le permite escribir las transformaciones de datos en un lenguaje de consulta.


    let
        Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
        #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
        #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
        #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
        #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
        #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
    in
        #"Changed Type"



Ahora tiene los datos del modelo de datos de Power BI. El escritorio de Power BI debe tener un aspecto como se muestra a continuación.

![](./media/machine-learning-data-science-vm-do-ten-things/PowerBIVolcanoData.png)

Puede comenzar a crear informes y visualizaciones mediante el modelo de datos. Puede seguir los pasos de este [artículo de Power BI](../documentdb/documentdb-powerbi-visualize.md#build-the-reports) para crear un informe. El resultado final es un informe que tiene un aspecto similar al siguiente.

![Power BI Desktop informe View - conector de Power BI](./media/machine-learning-data-science-vm-do-ten-things/power_bi_connector_pbireportview2.png)

## <a name="9-dynamically-scale-your-dsvm-to-meet-your-project-needs"></a>9. dinámicamente escalar su DSVM para satisfacer sus necesidades de proyecto

Puede escalar arriba y abajo de la DSVM para satisfacer las necesidades de su proyecto. Si no necesita usar la máquina virtual de la noche o los fines de semana, solo puede apagar la máquina virtual desde el [portal de Azure](https://portal.azure.com).

>[AZURE.NOTE]  Si utiliza solo el botón de cierre de sistema operativo en la máquina virtual se producirá una cargos de cálculo.  

Si necesita controlar algunos análisis de gran escala y necesita más capacidad de CPU, memoria o disco encontrará gran variedad de tamaños VM en términos de núcleos de CPU, capacidad de memoria y tipos de disco (incluidas las unidades de estado sólido) que cumplen sus necesidades presupuestarias y el cálculo. La lista completa de VM junto con sus horas calcular precios están disponible en la página de [Precios de máquinas virtuales de Windows Azure](https://azure.microsoft.com/pricing/details/virtual-machines/) .

Del mismo modo, si se reduce la necesidad de capacidad de procesamiento de VM (por ejemplo: mueve una carga de trabajo principal a un Hadoop o en un clúster de motor), puede reducir el clúster desde el [portal de Azure](https://portal.azure.com) e ir a la configuración de la instancia de máquina virtual. Esto es una captura de pantalla.


![](./media/machine-learning-data-science-vm-do-ten-things/VMScaling.PNG)


## <a name="10-install-additional-tools-on-your-virtual-machine"></a>10. instalar herramientas adicionales en la máquina virtual

Nos hemos empaqueta varias herramientas creemos podrá solucionar muchos de los datos comunes necesita análisis y que debería ahorrar tiempo en evitar tener que instalar y configurar los entornos uno por uno y ahorrar pagando solo para los recursos que usa.

Puede aprovechar otros servicios de datos y análisis de Azure en este artículo para mejorar su entorno de análisis. Comprendemos que en algunos casos sus necesidades requieren herramientas adicionales, incluidas algunas herramientas de terceros propietarios. Tiene acceso administrativo completo en la máquina virtual para instalar las nuevas herramientas que necesita. También puede instalar paquetes adicionales en Python y R que no están instaladas previamente. Para Python usar ```conda``` o ```pip```. R puede utilizar el ```install.packages()``` R consola o usar el IDE y elija "**paquetes** -> **Instalar paquetes**".

## <a name="summary"></a>Resumen
Estas son algunas de las cosas que puede hacer en la máquina Virtual de Microsoft datos ciencias. Hay muchas más cosas que puede hacer para que sea un entorno de análisis eficaz.
