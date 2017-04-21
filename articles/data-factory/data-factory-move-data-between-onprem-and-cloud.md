<properties 
    pageTitle="Mover datos - Data Management Gateway | Microsoft Azure"
    description="Configurar una puerta de enlace de datos para mover datos entre local y la nube. Utilice Data Management Gateway en el generador de datos de Azure para mover los datos." 
    keywords="puerta de enlace de datos, integración de datos, mover los datos, las credenciales de la puerta de enlace"
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="jingwang"/>

# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Mover datos entre orígenes locales y en la nube con Data Management Gateway
Este artículo proporciona una descripción general de integración de datos entre almacena de datos local y almacena datos de nube con el generador de datos. Se basa en el artículo de [Actividades de movimiento de datos](data-factory-data-movement-activities.md) y otros artículos de conceptos de datos fábrica principales: [conjuntos de datos](data-factory-create-datasets.md) y [canalizaciones](data-factory-create-pipelines.md). 

## <a name="data-management-gateway"></a>Data Management Gateway
Debe instalar Data Management Gateway en su equipo local para habilitar el movimiento de datos desde un almacén de datos local. La puerta de enlace se puede instalar en el mismo equipo que el almacén de datos o en un equipo diferente como la puerta de enlace puede conectarse al almacén de datos. 

> [AZURE.IMPORTANT] Consulte el artículo de [Data Management Gateway](data-factory-data-management-gateway.md) para obtener más información acerca de Data Management Gateway.   

El siguiente tutorial muestra cómo crear un generador de datos con una canalización que mueve los datos desde una base de datos de **SQL Server** local a un almacenamiento de blobs de Windows Azure. Como parte del tutorial, instalar y configurar Data Management Gateway en su equipo. 

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Tutorial: copiar datos locales a la nube
  
## <a name="create-data-factory"></a>Crear el generador de datos
En este paso, se utiliza el portal de Azure para crear una instancia de generador de datos de Azure denominada **ADFTutorialOnPremDF**. 

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com). 
2.  Haga clic en **+ nuevo**, haga clic en **inteligencia + análisis**y haga clic en **Generador de datos**.

    ![Nuevo -> DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
2. En el módulo de **Generador de datos nueva** , escriba **ADFTutorialOnPremDF** para el nombre.

    ![Agregar a Startboard](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

    > [AZURE.IMPORTANT] 
    > El nombre del generador de datos de Azure debe ser único global. Si recibe el error: **el nombre del generador de datos "ADFTutorialOnPremDF" no está disponible**, cambiar el nombre de la fábrica de datos (por ejemplo, yournameADFTutorialOnPremDF) y pruebe a crear de nuevo. Usar este nombre en lugar de ADFTutorialOnPremDF mientras lleva a cabo los pasos restantes de este tutorial.
    > 
    > El nombre de la fábrica de datos puede estar registrado como un nombre **DNS** en el futuro y por tanto se hacen visible públicamente.
3. Seleccione la **suscripción Azure** donde desea que el generador de datos que se cree. 
4.  Seleccione **el grupo de recursos** o crear un grupo de recursos. Para el tutorial, cree un grupo de recursos denominado: **ADFTutorialResourceGroup**. 
5.  En el módulo de **nuevo el generador de datos** , haga clic en **crear** .

    > [AZURE.IMPORTANT] Para crear instancias del generador de datos, debe ser miembro del rol de [Colaborador del generador de datos](../active-directory/role-based-access-built-in-roles.md/#data-factory-contributor) en el nivel de grupo de suscripción o recurso. 
11. Una vez completada la creación, consulte el módulo de **Generador de datos** tal como se muestra en la siguiente imagen:

    ![Página de inicio del generador de datos](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Crear la puerta de enlace
5. En el módulo de **Generador de datos** , haga clic en **autor e implementar** mosaico para iniciar el **Editor** de la fábrica de datos.

    ![Crear e implementar mosaico](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
6.  En el Editor del generador de datos, haga clic en **... Más** en la barra de herramientas y, a continuación, haga clic en **nueva puerta de enlace de datos**. Como alternativa, puede contextual **Puertas de enlace de datos** en la vista de árbol y haga clic en **nueva puerta de enlace de datos**. 

    ![Nueva puerta de enlace de datos en la barra de herramientas](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. En el módulo de **crear** , escriba **adftutorialgateway** para el **nombre**y haga clic en **Aceptar**.    

    ![Crear hoja de puerta de enlace](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)
3. En el módulo de **Configurar** , haga clic en **instalar directamente en este equipo**. Esta acción de descarga del paquete de instalación de la puerta de enlace, instala, configura y registra la puerta de enlace en el equipo.  

    > [AZURE.NOTE] 
    > Usar Internet Explorer o un explorador web compatible de Microsoft ClickOnce.
    > 
    > Si está utilizando Chrome, vaya a la [tienda web de Chrome](https://chrome.google.com/webstore/), buscar con la palabra clave "ClickOnce", elija una de las extensiones de ClickOnce e instalarlo. 
    >  
    > Haga lo mismo para Firefox (instalar complemento). Haga clic en **Abrir menú** en la barra de herramientas (**tres líneas horizontales** en la esquina superior derecha), haga clic en **complementos**, buscar con la palabra clave "ClickOnce", elija una de las extensiones de ClickOnce e instalarlo.    

    ![Puerta de enlace - configurar placa](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    De este modo es la manera más sencilla (un solo clic) descargar, instalar, configurar y registrar la puerta de enlace en un solo paso. Puede ver que la aplicación de **Administrador de configuración de Microsoft Data Management Gateway** está instalada en su equipo. También puede encontrar el ejecutable **ConfigManager.exe** en la carpeta: **C:\Program Files\Microsoft datos Management Gateway\2.0\Shared**.

    También puede descargar e instalar puerta de enlace de manualmente mediante los vínculos en esta placa y registrar mediante la clave que aparece en el cuadro de texto **Nueva clave** .
    
    Consulte el artículo de [Data Management Gateway](data-factory-data-management-gateway.md) para todos los detalles sobre la puerta de enlace.

    >[AZURE.NOTE] Debe ser un administrador en el equipo local para instalar y configurar Data Management Gateway correctamente. Puede agregar más usuarios al grupo local de **Usuarios de puerta de enlace de administración de datos** de Windows. Los miembros de este grupo pueden usar la herramienta de administrador de configuración de Data Management Gateway para configurar la puerta de enlace. 

5. Espere unos minutos o esperar hasta que vea el siguiente mensaje de notificación:

    ![Instalación de puerta de enlace correcta](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png) 
6. Iniciar aplicación de **Administrador de configuración de Data Management Gateway** en su equipo. En la ventana de **búsqueda** , escriba **Data Management Gateway** para obtener acceso a esta utilidad. También puede encontrar el ejecutable **ConfigManager.exe** en la carpeta: **C:\Program Files\Microsoft datos administración Gateway\2.0\Shared** 

    ![Administrador de configuración de puerta de enlace](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
6. Confirme que ve `adftutorialgateway is connected to the cloud service` mensaje. La barra de la parte inferior de estado muestra **conectado al servicio de nube** junto con una **marca de verificación verde**.

    En la pestaña **Inicio** , también puede realizar las siguientes operaciones: 
    - **Registrar** una puerta de enlace con una clave desde el portal de Azure mediante el botón registrar. 
    - **Detener** el servicio de Host de puerta de enlace de administración de datos de ejecución en el equipo de puerta de enlace. 
    - **Programar actualizaciones** en un momento determinado del día. 
    - Vista cuando la puerta de enlace se **actualizó por última vez**.
    - Especifique la hora a la que se puede instalar una actualización de la puerta de enlace. 

8. Cambie a la pestaña **configuración** . El certificado especificado en la sección de **certificado** se usa para las credenciales para el almacén de datos local que especifique en el portal de cifrado/descifrado. (opcional) Haga clic en **cambiar** para usar su propio certificado en su lugar. De forma predeterminada, la puerta de enlace utiliza el certificado que se genera automáticamente el servicio de generador de datos.

    ![Configuración de certificados de puerta de enlace](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    También puede realizar las siguientes acciones en la pestaña **configuración** : 
    - Ver o exportar el certificado usado por la puerta de enlace.
    - Cambiar el extremo HTTPS utilizado la puerta de enlace.    
    - Establecer un proxy HTTP para usarlo con la puerta de enlace.   
9. (opcional) Cambiar a la ficha **diagnóstico** , active la opción de **Habilitar el registro detallado** si desea habilitar el registro detallado que puede utilizar para solucionar problemas con la puerta de enlace. La información de registro se puede encontrar en el **Visor de eventos** en **aplicaciones y servicios de registros** -> nodo de**Data Management Gateway** . 

    ![Ficha diagnóstico](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    También puede realizar las siguientes acciones en la pestaña de **Diagnósticos** : 
    
    - Use la sección **Probar conexión** a un origen de datos local con la puerta de enlace.
    - Haga clic en **Ver registros** para ver el registro de Data Management Gateway en una ventana del Visor de eventos. 
    - Haga clic en **Enviar registros** para cargar un archivo zip con los registros de los siete últimos días a Microsoft para facilitar la solución de problemas de los problemas. 
10. En la ficha **diagnóstico** , en la sección **Probar conexión** , seleccione **SQL Server** para el tipo de almacén de datos, escriba el nombre del servidor de base de datos, nombre de la base de datos, especifique el tipo de autenticación, escriba el nombre de usuario y contraseña y haga clic en **probar** para comprobar si la puerta de enlace puede conectarse a la base de datos. 
11. Cambiar el explorador web y, en el **portal de Azure**, haga clic en **Aceptar** en el módulo de **Configurar** y, a continuación, en el módulo de **nueva puerta de enlace de datos** .
6. Debería ver **adftutorialgateway** en **Puertas de enlace de datos** en la vista de árbol de la izquierda.  Si hace clic en él, verá el JSON asociado. 
    

## <a name="create-linked-services"></a>Crear servicios vinculados 
En este paso, creará dos servicios vinculados: **AzureStorageLinkedService** y **SqlServerLinkedService**. La **SqlServerLinkedService** vínculos a una base de datos de SQL Server local y los vínculos de servicio vinculadas **AzureStorageLinkedService** almacenar un blobs de Windows Azure en el generador de datos. Crear una canalización más adelante en este tutorial que copie datos de la base de datos de SQL Server local para el almacenamiento de blobs de Windows Azure. 

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Agregar un servicio vinculado a una base de datos de SQL Server local
1.  En el **Editor del generador de datos**, haga clic en **almacenan nuevos datos** en la barra de herramientas y seleccione **SQL Server**. 

    ![Nuevo servicio de SQL Server vinculado](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png) 
3.  En el **editor de JSON** a la derecha, siga los pasos siguientes: 
    1. Para el **gatewayName**, especifique **adftutorialgateway**. 
    2. En la **cadena de conexión**, siga los pasos siguientes: 
        1. **Nombre de servidor**, escriba el nombre del servidor que hospeda la base de datos de SQL Server.
        2. Para **databasename**, escriba el nombre de la base de datos.
        3. Haga clic en el botón **cifrar** en la barra de herramientas. Se descargará y se inicia la aplicación de administrador de credenciales.
        
            ![Aplicación de administrador de credenciales](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
        5. En el cuadro de diálogo **Establecer credenciales** , especifique el tipo de autenticación, nombre de usuario y contraseña y haga clic en **Aceptar**. Si la conexión es correcta, se almacenan las credenciales cifradas en el JSON y cierra el cuadro de diálogo. 
        6. Cierre la pestaña de explorador vacío que inicia el cuadro de diálogo si no se cierra automáticamente y volver a la pestaña con el portal de Azure. 
  
            En el equipo de puerta de enlace, estas credenciales están **cifradas** mediante un certificado que posee el servicio del generador de datos. Si desea utilizar el certificado que está asociado a la puerta de enlace de administración de datos en su lugar, vea [establecer las credenciales de forma segura](#set-credentials-and-security).    
    1.  En la barra de comandos para implementar el servicio SQL Server vinculado, haga clic en **implementar** . Verá el servicio vinculado en la vista de árbol. 
        
        ![Servicio de SQL Server vinculada en la vista de árbol](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)  

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Agregar un servicio vinculado para una cuenta de almacenamiento de Azure
 
1. En el **Editor del generador de datos**, haga clic en **almacenan nuevos datos** en la barra de comandos y haga clic en **almacenamiento de Azure**.
2. Escriba el nombre de su cuenta de almacenamiento de Azure para el **nombre de la cuenta**.
3. Escriba la clave de su cuenta de Azure almacenamiento de la **clave de cuenta**.
4. Haga clic en **implementar** para implementar el **AzureStorageLinkedService**.
   
 
## <a name="create-datasets"></a>Crear conjuntos de datos
En este paso, creará entrada y salida conjuntos de datos que representan los datos de entrada y salidos de la operación de copia (base de datos local SQL Server = > almacenamiento de blobs de Windows Azure). Antes de crear conjuntos de datos, siga los pasos siguientes (los pasos detallados sigue la lista):

- Crear una tabla denominada **emp** en la base de datos de SQL Server que haya agregado como servicio vinculado a la fábrica de datos e insertar un par de entradas de ejemplo en la tabla.
- Crear un contenedor de blob denominado **adftutorial** en la cuenta de almacenamiento de blobs de Windows Azure que haya agregado como servicio vinculado al generador de datos.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Preparar la implementación local de SQL Server para el tutorial

1. En la base de datos especificada para SQL Server local vinculada servicio (**SqlServerLinkedService**), use el siguiente script SQL para crear la tabla de **emp** en la base de datos.

        CREATE TABLE dbo.emp
        (
            ID int IDENTITY(1,1) NOT NULL, 
            FirstName varchar(50),
            LastName varchar(50),
            CONSTRAINT PK_emp PRIMARY KEY (ID)
        )
        GO 
2. Insertar algunos muestra en la tabla: 

        INSERT INTO emp VALUES ('John', 'Doe')
        INSERT INTO emp VALUES ('Jane', 'Doe')

### <a name="create-input-dataset"></a>Crear conjunto de datos de entrada

1. En el **Editor del generador de datos**, haga clic en **... Más**, haga clic en **nuevo conjunto de datos** en la barra de comandos y haga clic en **tabla de SQL Server**. 
2.  Reemplazar el JSON en el panel derecho con el siguiente texto:
        
            {       
                "name": "EmpOnPremSQLTable",
                "properties": {
                    "type": "SqlServerTable",
                    "linkedServiceName": "SqlServerLinkedService",
                    "typeProperties": {
                        "tableName": "emp"
                    },
                    "external": true,
                    "availability": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "policy": {
                        "externalData": {
                            "retryInterval": "00:01:00",
                            "retryTimeout": "00:10:00",
                            "maximumRetry": 3
                        }
                    }
                }
            }    

    Tenga en cuenta los siguientes puntos: 

    - **tipo** se establece en **SqlServerTable**.
    - **nombre de tabla** se establece en **emp**.
    - **linkedServiceName** se establece en **SqlServerLinkedService** (creó este servicio vinculado anteriormente en este tutorial.).
    - Para un conjunto de datos entrada no generado por otra canalización en el generador de datos de Azure, debe establecer **externos** en **true**. Indica que los datos de entrada se produce externos con el servicio de generador de datos de Azure. Opcionalmente, puede especificar las directivas de datos externos con el elemento **externalData** en la sección de la **Directiva** .    

    Para obtener más información sobre las propiedades JSON, vea [mover datos de SQL Server](data-factory-sqlserver-connector.md) .
2. En la barra de comandos para implementar el conjunto de datos, haga clic en **implementar** .  


### <a name="create-output-dataset"></a>Crear conjunto de datos de salida

1.  En el **Editor del generador de datos**, haga clic en **nuevo conjunto de datos** en la barra de comandos y haga clic en **almacenamiento de blobs de Windows Azure**.
2.  Reemplazar el JSON en el panel derecho con el siguiente texto: 

            {
                "name": "OutputBlobTable",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "AzureStorageLinkedService",
                    "typeProperties": {
                        "folderPath": "adftutorial/outfromonpremdf",
                        "format": {
                            "type": "TextFormat",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Hour",
                        "interval": 1
                    }
                }
            }
  
    Tenga en cuenta los siguientes puntos: 
    
    - **tipo** se establece en **AzureBlob**.
    - **linkedServiceName** se establece en **AzureStorageLinkedService** (creó este servicio vinculada en el paso 2).
    - **folderPath** se establece en **adftutorial/outfromonpremdf** donde outfromonpremdf es la carpeta en el contenedor adftutorial. Crear el contenedor **adftutorial** si aún no existe. 
    - La **disponibilidad** se establece en **cada hora** (**frecuencia** establecido en **hora** e **intervalo** establecido en **1**).  El servicio de generador de datos genera un segmento de datos de salida cada hora en la tabla de **emp** en la base de datos de SQL Azure. 

    Si no especifica un **nombre de archivo** para una **tabla de resultados**, los archivos generados en el **folderPath** se denominan en el siguiente formato: datos. <Guid>.txt (por ejemplo:: Data.0a405f8a 93ff 4c6f b3be f69616f1df7a.txt.).

    Para establecer **folderPath** y **nombre de archivo** dinámicamente en función del tiempo de **SliceStart** , use la propiedad partitionedBy. En el ejemplo siguiente, folderPath usa el año, mes y día de la SliceStart (hora de inicio de la segmentación de datos que se está procesando) y nombre de archivo usa horas desde el SliceStart. Por ejemplo, si se produce un sector de 2014-10-20T08:00:00, el nombre de carpeta se establece en wikidatagateway, wikisampledataout, de 2014, 10/20 y el nombre de archivo se establece en 08.csv. 

        "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
            { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 
    Para obtener más información sobre las propiedades JSON, vea [mover datos desde el almacenamiento de blobs de Windows Azure](data-factory-azure-blob-connector.md) .
2.  En la barra de comandos para implementar el conjunto de datos, haga clic en **implementar** . Confirme que ve los conjuntos de datos en la vista de árbol.  

## <a name="create-pipeline"></a>Crear canalización
En este paso, cree una **canalización** con una **Copia actividad** que usa **EmpOnPremSQLTable** como entrada y **OutputBlobTable** como resultado.

1.  En el Editor del generador de datos, haga clic en **... Más**y haga clic en **nuevo canal**. 
2.  Reemplazar el JSON en el panel derecho con el siguiente texto: 
    
            {
                "name": "ADFTutorialPipelineOnPrem",
                "properties": {
                "description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
                "activities": [
                {
                    "name": "CopyFromSQLtoBlob",
                    "description": "Copy data from on-prem SQL server to blob",
                    "type": "Copy",
                    "inputs": [
                    {
                        "name": "EmpOnPremSQLTable"
                    }
                    ],
                    "outputs": [
                    {
                        "name": "OutputBlobTable"
                      }
                    ],
                    "typeProperties": {
                      "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from emp"
                      },
                      "sink": {
                        "type": "BlobSink"
                      }
                    },
                    "Policy": {
                      "concurrency": 1,
                      "executionPriorityOrder": "NewestFirst",
                      "style": "StartOfInterval",
                      "retry": 0,
                      "timeout": "01:00:00"
                    }
                  }
                ],
                "start": "2016-07-05T00:00:00Z",
                "end": "2016-07-06T00:00:00Z",
                "isPaused": false
              }
            }

    > [AZURE.IMPORTANT]
    > Reemplace el valor de la propiedad **empezar** con el valor de día y de **finalización** actual con el día siguiente.

    Tenga en cuenta los siguientes puntos:
 
    - En la sección actividades, hay solo actividad cuyo **tipo** se establece en **Copiar**.
    - **Entrada** de la actividad se establece en **EmpOnPremSQLTable** y **salida** de la actividad está establecida en **OutputBlobTable**.
    - En la sección **typeProperties** , **SqlSource** se especifica como el **tipo de origen** y **BlobSink **se especifica como el **tipo de receptor**.
    - Consulta SQL `select * from emp` especificado para la propiedad **sqlReaderQuery** de **SqlSource**.

     Ambas inicio y final fechas y horas deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La hora de **finalización** es opcional, pero se usa en este tutorial. 
    
    Si no especifica el valor de la propiedad **final** , se calcula como "**Inicio + 48 horas**". Para ejecutar la canalización de forma indefinida, especifique el **9/9/9999** como el valor de la propiedad **end** . 
    
    Definir la duración de la hora en que se procesan las segmentaciones de datos basándose en las propiedades de **disponibilidad** que han definido para cada conjunto de datos del generador de datos de Azure.
    
    En el ejemplo, hay 24 segmentaciones de datos como cada segmento de datos se produce cada hora.     
2. Haga clic en **implementar** en la barra de comandos para implementar el conjunto de datos (tabla es un conjunto de datos rectangular). Confirme que la canalización se muestra en la vista de árbol en el nodo de **canalizaciones** .  
5. Ahora, haga clic en **X** dos veces para cerrar los módulos para volver a la hoja de **Datos fábrica** para la **ADFTutorialOnPremDF**.

**¡Felicidades!** Ha creado un generador de datos de Azure, servicios vinculados, los conjuntos de datos y una canalización y programar la canalización.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Ver el generador de datos en una vista de diagrama 
1. En el **portal de Azure**, haga clic en **diagrama de** mosaico en la página principal para el generador de datos **ADFTutorialOnPremDF** . :

    ![Vínculo de diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. Verá el diagrama similar a la siguiente imagen:

    ![Vista de diagrama](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Puede acercar, alejar, zoom al 100%, zoom ajustar automáticamente coloque canalizaciones y conjuntos de datos y mostrar información de linaje (resalta elementos ascendentes y descendentes de los elementos seleccionados).  Hacer doble clic en un objeto (conjunto de datos de entrada y salida o canalización) para ver las propiedades para el mismo. 

## <a name="monitor-pipeline"></a>Canalización de Monitor
En este paso, usa el portal de Azure para supervisar lo que sucede en un generador de datos de Azure. También puede usar cmdlets de PowerShell para supervisar los conjuntos de datos y canalizaciones. Para obtener más información acerca de cómo supervisar, vea [supervisar y administrar canalizaciones](data-factory-monitor-manage-pipelines.md).

5. En el diagrama, haga doble clic en **EmpOnPremSQLTable**.  

    ![EmpOnPremSQLTable sectores](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. Observe que todos los datos se divide los están en estado **Listo** porque la duración de la canalización (hora de inicio a la hora de finalización) está en el pasado. También es porque ha insertado los datos en la base de datos de SQL Server y está allí todo el tiempo. Confirme que no hay sectores aparecen en la sección de **sectores de problema** en la parte inferior. Para ver todos los sectores, haga clic en **Ver más** en la parte inferior de la lista de sectores. 
7. Ahora, en el módulo de **conjuntos de datos** , haga clic en **OutputBlobTable**.

    ![OputputBlobTable sectores](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
9. Haga clic en cualquier sector de datos de la lista y verá el módulo de **Sectores de datos** . Vea la actividad se ejecute durante la segmentación de datos. Vea sólo una actividad ejecutar normalmente.  

    ![Módulo de sectores de datos](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Si la segmentación de datos no está en el estado **Listo** , puede ver los sectores precedentes que no están listos e impiden la segmentación de datos actual de la ejecución de la lista de **sectores precedentes que son no está listos** .

10. Haga clic en la **actividad ejecutar** desde la lista en la parte inferior para ver **Detalles de ejecución de la actividad**.

    ![Módulo de detalles de actividad](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

    Verá información como la puerta de enlace que se utiliza para transferir los datos, la duración y el rendimiento. 
11. Haga clic en **X** para cerrar todos los módulos hasta que usted 
12. volver a la hoja principal para el **ADFTutorialOnPremDF**.
14. (opcional) Haga clic en **canalizaciones**, haga clic en **ADFTutorialOnPremDF**y obtención de detalles de tablas de entrada (**consumido**) o conjuntos de datos de salida (**producción**).
15. Usar herramientas como uso, como el [Explorador de almacenamiento de Microsoft](http://storageexplorer.com/) para comprobar que se crea un archivo de blob para cada hora.

    ![Explorador de almacenamiento de Azure](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Pasos siguientes

- Consulte el artículo de [Data Management Gateway](data-factory-data-management-gateway.md) para todos los detalles sobre la puerta de enlace de administración de datos.
- Consulte [Copiar datos de blobs de Windows Azure a SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener información sobre cómo usar la actividad de copia para mover datos de un almacén de datos de origen a un almacén de datos de receptor. 
