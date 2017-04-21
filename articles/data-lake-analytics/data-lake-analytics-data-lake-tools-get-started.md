<properties
   pageTitle="Desarrollar secuencias de comandos de SQL U con datos lago Tools para Visual Studio | Azure"
   description="Obtenga información sobre cómo instalar herramientas de lago de datos para Visual Studio, cómo desarrollar y secuencias de comandos de prueba U SQL. "
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-develop-u-sql-scripts-using-data-lake-tools-for-visual-studio"></a>Tutorial: desarrollar secuencias de comandos de SQL U con datos lago Tools para Visual Studio

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Obtenga información sobre cómo instalar Data lago Tools para Visual Studio y use datos lago Tools para Visual Studio para escribir y probar secuencias de comandos SQL U.

U-SQL es un lenguaje muy extensible hyper scalable para preparar, transformar y analizar todos los datos en el lago datos y versiones posteriores. Para obtener más información, vea [referencia SQL U] (http://go.microsoft.com/fwlink/p/?LinkId=691348).


##<a name="prerequisites"></a>Requisitos previos

- **2015 de Visual Studio, Visual Studio de 2013 actualizar 4 o Visual Studio 2012. Enterprise (Ultimate/Premium), Professional, Comunidad son compatibles con las ediciones; Express edition no es compatible. Visual Studio "15" no es compatible actualmente y estamos trabajando en él.**
- **Microsoft Azure SDK para .NET versión 2.7.1 o posterior**.  Instalar mediante el [Instalador de plataforma Web](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Datos lago Tools para Visual Studio](http://aka.ms/adltoolsvs)**.

    Una vez instalada datos lago Tools para Visual Studio, verá un nodo "Análisis de datos lago" en el Explorador de servidores bajo el nodo "Azure" (puede abrir el Explorador de servidor presionando Ctrl + Alt + S).

- **Vaya a través de las dos secciones siguientes en [Introducción a análisis de lago de datos de Azure con el portal de Azure](data-lake-analytics-get-started-portal.md)**.

    - [Crear una cuenta de Azure datos lago análisis](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).
    - [Cargar SearchLog.tsv a la cuenta de almacenamiento de datos lago predeterminada](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account).

    Para su comodidad, puede encontrarse un script de PowerShell de ejemplo para crear un servicio de datos lago analítico y cargar el archivo de origen de datos de [muestra de PowerShell Appx-A para preparar el tutorial](data-lake-analytics-data-lake-tools-get-started.md#appx-a-powershell-sample-for-preparing-the-tutorial).

    Las herramientas de lago de datos no admite la creación de cuentas de análisis de datos lago. Así que debe crearla con el portal de Azure, Azure PowerShell, .NET SDK o CLI de Azure. Para ejecutar una tarea de análisis de datos lago, necesitará algunos datos. Aunque las herramientas de lago de datos admite cargar el contenido de los datos, usará el portal para cargar los datos de ejemplo para facilitar la siga este tutorial.

## <a name="connect-to-azure"></a>Conectarse a Azure

**Para conectarse a datos lago Analytics**

1. Abra Visual Studio.
2. En el menú **Ver** , haga clic en **Explorador de servidores** para abrir el Explorador de servidores. O presione **[CTRL] + [ALT] + S**.
3. Haga clic en **Azure**, haga clic en "Conectarse a Microsoft Azure suscripción" y, a continuación, siga las instrucciones.
4. Desde el **Explorador de servidores**, expanda **Azure**y, a continuación, expanda **El análisis de datos lago**. Deberá ver una lista de sus cuentas de análisis de datos lago si hay alguna. No puede crear cuentas de análisis de datos lago de Visual Studio. Para crear una cuenta, consulte [Introducción a análisis de lago de datos de Azure con el portal de Azure](data-lake-analytics-get-started-portal.md) o [Introducción a análisis de lago de datos de Azure con PowerShell de Azure](data-lake-analytics-get-started-powershell.md).

## <a name="upload-source-data-files"></a>Cargar archivos de datos de origen

Algunos datos de la sección **como requisito previo** que haya cargado anteriormente en el tutorial.  

En caso de que desea usar sus propios datos, estos son los procedimientos para cargar los datos de las herramientas de lago de datos.

**Cargar archivos a la cuenta de Azure datos lago dependiente**

1. Desde el **Explorador de servidores**, expanda **Azure**, expanda **El análisis de datos lago**, expandir su cuenta de análisis de datos lago, **Cuentas de almacenamiento**. Se verá la cuenta de almacenamiento de datos lago predeterminada y las cuentas de almacenamiento de datos lago vinculadas y las cuentas de almacenamiento de Azure vinculadas. La cuenta predeterminada de lago de datos tiene una etiqueta "Cuenta de almacenamiento predeterminada".
2. Haga clic en la cuenta de almacenamiento de datos lago predeterminada y, a continuación, haga clic en **el explorador**.  Se abre las herramientas de lago de datos para el panel del explorador de Visual Studio.  En la parte izquierda, muestra una vista de árbol, que la vista de contenido está a la derecha.
3. Vaya a la carpeta donde desea cargar archivos,
4. Haga clic en cualquier espacio en blanco y, a continuación, haga clic en **cargar**.

    ![Proyecto de Visual Studio de SQL U U SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-upload-files.png)

**Cargar archivos a una cuenta vinculada de almacenamiento de blobs de Windows Azure**

1. Desde el **Explorador de servidores**, expanda **Azure**, expanda **El análisis de datos lago**, expandir su cuenta de análisis de datos lago, **Cuentas de almacenamiento**. Se verá la cuenta de almacenamiento de datos lago predeterminada y las cuentas de almacenamiento de datos lago vinculadas y las cuentas de almacenamiento de Azure vinculadas.
2. Expanda la cuenta de almacenamiento de Azure.
3. Haga clic en el contenedor donde desea cargar archivos y, a continuación, haga clic en **el explorador**. Si no tiene un contenedor, primero debe crear una mediante el portal de Azure, Azure PowerShell u otras herramientas.
4. Vaya a la carpeta donde desea cargar archivos,
5. Haga clic en cualquier espacio en blanco y, a continuación, haga clic en **cargar**.

## <a name="develop-u-sql-scripts"></a>Desarrollar secuencias de comandos SQL U

Los trabajos de análisis de datos lago se escriben en lenguaje SQL U. Para obtener más información acerca de SQL U, consulte [referencia de lenguaje SQL U](http://go.microsoft.com/fwlink/?LinkId=691348)y [empezar a trabajar con el lenguaje SQL U](data-lake-analytics-u-sql-get-started.md) .

**Para crear y enviar un trabajo de análisis de datos lago**

1. En el menú **archivo** , haga clic en **nuevo**y, a continuación, haga clic en **proyecto**.
2. Seleccione el tipo de **Proyecto U SQL** .

    ![nuevo proyecto de Visual Studio de SQL U](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Haga clic en **Aceptar**. Visual studio crea una solución con un archivo de **Script.usql** .
4. Escriba la siguiente secuencia de comandos en **Script.usql**:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();

        @res =
            SELECT *
            FROM @searchlog;        

        OUTPUT @res   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Esta secuencia de comandos SQL U lee el archivo de datos de origen con **Extractors.Tsv()**y, a continuación, crea un archivo csv con **Outputters.Csv()**.

    No modifique las dos rutas a menos que ha copiado el archivo de origen en una ubicación diferente.  Análisis de datos lago creará la carpeta de salida si no existe.

    Es más fácil usar rutas relativas de los archivos almacenan de forma predeterminada en cuentas de lago de datos. También puede usar rutas de acceso absolutas.  Por ejemplo

        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Debe usar rutas de acceso absolutas para acceder a los archivos en las cuentas de almacenamiento vinculados.  La sintaxis de los archivos almacenados en cuenta vinculada de almacenamiento de Azure es:

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Contenedor de blobs de Windows Azure con BLOB público o permisos de acceso de los contenedores público no se admiten actualmente.  

    Observe las siguientes características:

    - **IntelliSense**

        Completar automáticamente de nombre y se muestran los miembros de conjunto de filas, clases, bases de datos, esquemas y objetos definidos por el usuario (UDOs).

        IntelliSense para entidades de catálogo (bases de datos, esquemas, tablas, etc. UDOs) relacionada con su cuenta de cálculo. Puede comprobar la cuenta actual de cálculo activa, la base de datos y el esquema en la barra de herramientas superior y cambiarlas a través de las listas desplegables.

    - **Expandir* columnas**

        Haga clic en la derecha de *, se verá un subrayado azul debajo de la *. Desplace el cursor del mouse sobre el subrayado azul y, a continuación, haga clic en la flecha hacia abajo.
        ![Expanda Herramientas de visual studio datos lago *](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-expand-asterisk.png)

        Haga clic en **Expandir columnas**, la herramienta reemplazará la * con los nombres de columna.

    - **Formato automático**

        Los usuarios pueden cambiar la sangría de la secuencia de comandos de SQL U en función del código estructura debajo de edición -> Opciones avanzadas:

        - Dar formato al documento (CTRL+e, D): Da formato a todo el documento   
        - Aplicar formato a selección (CTRL+k, CTRL+f): Da formato a la selección. Si no se ha realizado ninguna selección, este método abreviado da formato a la línea que el cursor está en.  

        El formato de reglas están configurable en Herramientas -> Opciones -> Editor de texto - > SIP -> formato.  
    - **Sangría automática**

        Datos lago Tools para Visual Studio es capaz de aplicar sangría expresiones automáticamente mientras escribe scripts. Esta característica está deshabilitada de forma predeterminada, los usuarios necesitan habilitar mediante la comprobación de U-SQL -> Opciones y configuración -> modificadores -> Habilitar sangría inteligente.

    - **Ir a la definición y buscar todas las referencias**

        Doble clic en el nombre de un conjunto de filas o parámetro/columnas/UDO, etc. y haciendo clic en Ir a definición (F12) le permite navegar a su definición. Haciendo clic en Buscar todas las referencias (MAYÚS+F12), se muestran todas las referencias.

    - **Insertar ruta de Azure**

        En lugar de recordar la ruta de acceso de archivo de Azure y tipo manualmente al escribir secuencias de comandos de datos lago Tools para Visual Studio proporciona una forma sencilla: ratón en el editor, haga clic en Insertar ruta de Azure. Desplácese hasta el archivo en el cuadro de diálogo del explorador de blobs de Windows Azure. Haga clic en **Aceptar**. la ruta de acceso del archivo se inserta en el código.

5. Especifique la cuenta, base de datos y el esquema de análisis de datos lago. Puede seleccionar **(local)** para ejecutar el script localmente para el propósito de la prueba. Para obtener más información, vea [Ejecutar U-SQL localmente](#run-u-sql-locally).

    ![Enviar proyecto SQL U Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

    Para obtener más información, vea [usar U-SQL catálogo](data-lake-analytics-use-u-sql-catalog.md).

5. Desde el **Explorador de soluciones**, haga clic con el botón **Script.usql**y, a continuación, haga clic en **Generar secuencia de comandos**. Compruebe el resultado en el panel de resultados.
6. Desde el **Explorador de soluciones**, haga clic con el botón **Script.usql**y, a continuación, haga clic en **Enviar Script**. Si lo desea, puede hacer clic en **Enviar** desde el panel de Script.usql.  Consulte la captura de pantalla anterior.  Haga clic en la flecha abajo situada junto al botón Enviar para enviar con las opciones de avance:
7. Especifique el **Nombre del trabajo**, compruebe la **Cuenta de análisis**y, a continuación, haga clic en **Enviar**. Resultados de envío y vínculo de tarea están disponibles en las herramientas de lago de datos para la ventana de resultados de Visual Studio cuando se complete la presentación.

    ![Enviar proyecto SQL U Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)

8. Debe hacer clic en el botón Actualizar para ver el estado del trabajo más reciente y actualizar la pantalla. Cuando los éxitos de trabajo, se mostrará el **Gráfico de trabajo**, **Las operaciones de datos de metadatos**, **Historial de estado**, **Diagnósticos**:

    ![Gráfico de rendimiento de trabajo de análisis de SQL U Visual Studio datos lago](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

    * Resumen de tareas. Mostrar la información de resumen de la tarea actual, por ejemplo: estado, progreso, el tiempo de ejecución, nombre de tiempo de ejecución, etcetera del remitente.   
    * Detalles del trabajo. Se proporciona información detallada sobre esta tarea, incluida la secuencia de comandos de recursos, vista de ejecución de vértice.
    * Gráfico de trabajo. Cuatro gráficos se proporcionan para visualizar la información del trabajo: progreso, lectura de datos, escribir datos, el tiempo de ejecución, el tiempo de ejecución promedio por nodo, rendimiento de entrada, rendimiento de salida.
    * Operaciones de metadatos. Muestra todas las operaciones de metadatos.
    * Historial de estado.
    * Diagnósticos. Datos lago Tools para Visual Studio se diagnosticar automáticamente la ejecución del trabajo. Recibirá las alertas cuando hay algunos errores o problemas de rendimiento en su trabajo. Vea trabajo parte diagnósticos (vínculo por determinar) para obtener más información.

**Para comprobar el estado del trabajo**

1. Desde el Explorador de servidores, expanda **Azure**, expanda **El análisis de datos lago**, expanda el nombre de cuenta de análisis de datos lago
2. Haga doble clic en **tareas** para ver una lista de los trabajos.
2. Haga clic en una tarea para ver el estado.

**Para ver los resultados de la tarea**

1. Desde el **Explorador de servidores**, expanda **Azure**, expanda **El análisis de datos lago**, expandir su cuenta de análisis de datos lago, expandir **Cuentas de almacenamiento**, haga clic en la cuenta de almacenamiento de datos lago predeterminada y, a continuación, haga clic en **Explorador**.
2.  Haga doble clic en **Imprimir** para abrir la carpeta
3.  Haga doble clic en **SearchLog de adltools.csv**.


###<a name="job-playback"></a>Reproducción de trabajo

Reproducción de trabajo le permite ver el progreso de la ejecución de trabajo y detectar visualmente botella y anomalías de rendimiento. Esta característica puede utilizarse antes de que finalice el trabajo ejecución (es decir, durante el tiempo que actualmente se está ejecutando el trabajo) como una vez completada la ejecución. Procedimiento reproducción durante la ejecución del trabajo permitirá al usuario reproducir el progreso hasta la hora actual.

**Para ver el progreso de la ejecución de trabajo**  

1. En la esquina superior derecha, haga clic en **El perfil de carga** . Ver la pantalla anterior captura.
2. Haga clic en el botón Reproducir en la esquina inferior izquierda para revisar el progreso de la ejecución de trabajo.
3. Durante la reproducción, haga clic en **Pausa** para detener o directamente arrastrar la barra de progreso a posiciones específicas.


###<a name="heat-map"></a>Mapa de calor

Datos lago Tools para Visual Studio proporciona superposiciones de colores seleccionable por el usuario en la vista de trabajo para indicar el progreso, datos i/OS, tiempo de ejecución, rendimiento de i/OS de cada fase. De este modo, los usuarios pueden calcular posibles problemas y distribución de propiedades del trabajo intuitiva y directamente. Puede elegir un origen de datos para mostrar de la lista desplegable.  

## <a name="run-u-sql-locally"></a>Ejecutar SQL U localmente

Con el equipo local U SQL ejecutar experiencia en Visual Studio, puede:

- Ejecutar secuencias de comandos de SQL U localmente, junto con C# ensamblados.
- Depurar C# ensamblados localmente.
- Crear o eliminar o vista de bases de datos locales, ensamblados, esquemas y tablas en el Explorador de servidor igual que pueden hacer por el servicio de análisis de lago de datos de Azure.

Verá una cuenta *Local* en Visual Studio y el instalador crea una *DataRoot* carpeta ubicada *C:\LocalRunRoot*. Se utilizará la carpeta DataRoot:

- Tienda metadatos incluidos tablas, DBs, TVF, etcetera.
- Para una determinada secuencia de comandos: si se hace referencia a una ruta de acceso relativa en rutas de acceso de entrada y salida, vamos a consultar la DataRoot (así como ruta de acceso de la secuencia de comandos si la entrada de TI)
- NO se hará referencia a la carpeta DataRoot si intenta registrar un ensamblado y utilizar una ruta de acceso relativa (consulte "usar ensamblados al ejecutar local de forma" elemento para obtener más detalles)

El siguiente vídeo muestra la característica de ejecución de SQL U local:

>[AZURE.VIDEO usql-localrun]

### <a name="known-issues-and-limitations"></a>Problemas conocidos y limitaciones

- No puede crear tabla/DB etc. en Explorador de servidores para la cuenta local.
- Cuando se hace referencia a una ruta de acceso relativa:

    - En la entrada de secuencia de comandos (extraer * FROM "/ ruta de acceso/abc")-buscarán la ruta de acceso de DataRoot y la ruta de acceso de secuencia de comandos.
    - En la salida de la secuencia de comandos (salida a "ruta/abc"): la ruta de acceso de DataRoot se utilizará como la carpeta de salida.
    - En el registro de ensamblado (xyz crear ENSAMBLADO desde "/ ruta de acceso/abc"): se buscarán la ruta de acceso de secuencia de comandos, pero no la DataRoot.
    - En registrado TVF o vista u otras entidades de metadatos: se buscarán la ruta de acceso de DataRoot, pero no la ruta de acceso de la secuencia de comandos.

    Las secuencias de comandos ha encontrado en el servicio de lago de datos, la cuenta de almacenamiento predeterminada se utilizará como carpeta raíz y buscará en consecuencia.

### <a name="test-u-sql-scripts-locally"></a>Probar scripts de SQL U localmente
Para obtener instrucciones sobre el desarrollo de secuencias de comandos SQL U, vea [secuencias de comandos SQL de U desarrollar](#develop-and-test-u-sql-scripts). Para crear y ejecutar SQL U scripts localmente, seleccione **(Local)** en la lista desplegable de clúster y, a continuación, haga clic en **Enviar**. Asegúrese de que tiene los datos adecuados al que hace referencia, ya sea hacen referencia a la ruta de acceso absoluta o ubicar los datos en la carpeta DataRoot.

![Enviar proyecto de Visual Studio de SQL U localmente](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-local-run.png)

Puede también haga clic en una secuencia de comandos y, a continuación, haga clic en **Ejecutar Plan Local** en el menú contextual o presione **CTRL + F5** para ejecución local de desencadenador.

### <a name="use-assemblies-in-local-run"></a>Utilizar ensamblados de ejecución local

Hay dos formas para ejecutar los archivos personalizados de C#:

- Escribir ensamblados en el archivo de código subyacente y los ensamblados se registrado automáticamente y se eliminan cuando haya terminado la secuencia de comandos.
- Crear un proyecto de ensamblado C# y registrar la dll de salida a la cuenta local a través de una secuencia de comandos como debajo. Tenga en cuenta que la ruta es relativa a la secuencia de comandos en lugar de la carpeta DataRoot.

![Utilizar ensamblados de ejecución de sql u local](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-local-run-assembly.png)

### <a name="debug-scripts-and-c-assemblies-locally"></a>Depurar secuencias de comandos y C# ensamblados localmente

Puede depurar C# ensamblados sin enviar y registrar el servicio de análisis de lago de datos de Azure. Puede establecer puntos de interrupción en ambos el archivo de código subyacente y en un proyecto de C# que se hace referencia.

**Depurar código local en el archivo de código subyacente**
1.  Establecer puntos de interrupción en el archivo de código subyacente.
2.  Presione **F5** para depurar la secuencia de comandos localmente.

El siguiente procedimiento solo funciona en Visual Studio de 2015. En versiones anteriores de Visual Studio debe agregar manualmente los archivos pdb.

**Depurar código local en un proyecto C#**
1.  Crear un proyecto de ensamblado de C# y generar para generar la dll de salida.
2.  Registrar el archivo dll mediante una instrucción SQL U:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
3.  Establecer puntos de interrupción en el código C#.
4.  Presione **F5** para depurar la secuencia de comandos que hacen referencia a la dll de C# localmente.  

##<a name="see-also"></a>Vea también

Para empezar con datos lago análisis con herramientas diferentes, consulte:

- [Introducción a análisis de lago de datos con el portal de Azure](data-lake-analytics-get-started-portal.md)
- [Introducción a análisis de datos lago con PowerShell de Azure](data-lake-analytics-get-started-powershell.md)
- [Introducción a análisis de lago de datos con el SDK de .NET](data-lake-analytics-get-started-net-sdk.md)
- [Depurar código C# en trabajos U SQL](data-lake-analytics-debug-u-sql-jobs.md)

Para ver más temas de desarrollo:

- [Analizar mediante el análisis de datos lago de blogs](data-lake-analytics-analyze-weblogs.md)
- [Desarrollar secuencias de comandos de SQL U con datos lago Tools para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Empezar a trabajar con el lenguaje de datos de Azure lago análisis U-SQL](data-lake-analytics-u-sql-get-started.md)
- [Desarrollar operadores de SQL U definidas por el usuario para las tareas de análisis de datos lago](data-lake-analytics-u-sql-develop-user-defined-operators.md)

##<a name="appx-a-powershell-sample-for-preparing-the-tutorial"></a>Ejemplo de PowerShell A AppX para preparar el tutorial

El siguiente script de PowerShell prepara una cuenta de Azure datos lago análisis y el origen de datos, por lo que puede pasar a [las secuencias de comandos de desarrollar U-SQL](data-lake-analytics-data-lake-tools-get-started.md#develop-u-sql-scripts).

    #region - used for creating Azure service names
    $nameToken = "<Enter an alias>"
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion

    #region - service names
    $resourceGroupName = $namePrefix + "rg"
    $dataLakeStoreName = $namePrefix + "adas"
    $dataLakeAnalyticsName = $namePrefix + "adla"
    $location = "East US 2"
    #endregion


    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an Azure Data Lake Analytics service account
    Write-Host "Create a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
    New-AzureRmDataLakeStoreAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeStoreName `
        -Location $location

    Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
    New-AzureRmDataLakeAnalyticsAccount `
        -Name $dataLakeAnalyticsName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultDataLake $dataLakeStoreName

    Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
    Get-AzureRmDataLakeAnalyticsAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeAnalyticsName  
    #endregion

    #region - prepare the source data
    Write-Host "Import the source data ..."  -ForegroundColor Green
    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file.
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

    Write-Host "List the source data ..."  -ForegroundColor Green
    Get-AzureRmDataLakeStoreChildItem -Account $dataLakeStoreName -Path  "/Samples/Data/"
    #endregion
