<properties
    pageTitle="Copiar datos de almacenamiento de blobs a la base de datos SQL | Microsoft Azure"
    description="En este tutorial se muestra cómo usar la actividad de copia en una canalización del generador de datos de Azure para copiar los datos de almacenamiento de blobs de base de datos de SQL."
    Keywords="BLOB sql, almacenamiento de blobs, copiar datos"
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="spelluru"/>

# <a name="copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Copiar datos de almacenamiento de blobs de base de datos de SQL mediante el generador de datos 
> [AZURE.SELECTOR]
- [Información general y los requisitos previos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Asistente para copiar](data-factory-copy-data-wizard-tutorial.md)
- [Portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Azure plantilla de administrador de recursos](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [API DE REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API DE .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)


En este tutorial, se crea un generador de datos con una canalización para copiar los datos de almacenamiento de blobs de base de datos de SQL.

La actividad de copia realiza el movimiento de datos en el generador de datos de Azure. Funciona con un servicio disponible globalmente que puede copiar datos entre varias almacena datos de forma segura, confiable y scalable. Consulte el artículo de [Actividades de movimiento de datos](data-factory-data-movement-activities.md) para obtener más información acerca de la actividad de copia.  

> [AZURE.NOTE] Para obtener información detallada de servicio del generador de datos, vea el artículo [Introducción al generador de datos de Azure](data-factory-introduction.md) .

##<a name="prerequisites-for-the-tutorial"></a>Requisitos previos para el tutorial
Antes de comenzar este tutorial, debe tener el siguiente:

- **Suscripción de azure**.  Si no tiene una suscripción, puede crear una cuenta de prueba gratuita de dos minutos. Vea el artículo de la [Versión de prueba gratuita](http://azure.microsoft.com/pricing/free-trial/) para obtener más detalles.
- **La cuenta de almacenamiento de azure**. Usar el almacenamiento de blobs como un almacén de datos de **origen** en este tutorial. Si no tiene una cuenta de almacenamiento de Azure, vea el artículo [crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account) para conocer los pasos crear uno.
- **Base de datos SQL azure**. Usar una base de datos de SQL Azure como un almacén de datos de **destino** en este tutorial. Si no tiene una base de datos de SQL Azure que puede usar en el tutorial, consulte [cómo crear y configurar una base de datos de SQL Azure](../sql-database/sql-database-get-started.md) para crear uno.
- **SQL Server 2012/2014 o Visual Studio de 2013**. Usar SQL Server Management Studio o Visual Studio para crear una base de datos de ejemplo y ver los datos de resultado de la base de datos.  

## <a name="collect-blob-storage-account-name-and-key"></a>Recopilar clave y nombre de cuenta de almacenamiento de blobs 
Necesita el nombre de cuenta y una clave de cuenta de la cuenta de almacenamiento de Azure para realizar este tutorial. Anote el **nombre de cuenta** y la **clave de cuenta** para su cuenta de almacenamiento de Azure.

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/).
2. Haga clic en **más servicios** en el menú de la izquierda y seleccione **Cuentas de almacenamiento**.

    ![Examinar - cuentas de almacenamiento](media\data-factory-copy-data-from-azure-blob-storage-to-sql-database\browse-storage-accounts.png)
3. En el módulo de **Cuentas de almacenamiento** , seleccione la **cuenta de almacenamiento de Azure** que desea utilizar en este tutorial.
4. Seleccione el vínculo de **las teclas de acceso** en **configuración**.
5.  Haga clic en **Copiar** (imagen) situado junto al cuadro de texto **nombre de la cuenta de almacenamiento** y guardar y pegarla en algún lugar (por ejemplo: en un archivo de texto).
6. Repita el paso anterior para copiar o nota hacia abajo la **clave1**.
    
    ![Tecla de acceso de almacenamiento](media\data-factory-copy-data-from-azure-blob-storage-to-sql-database\storage-access-key.png)
7. Cierre todos los módulos, haga clic en **X**.

## <a name="collect-sql-server-database-user-names"></a>Recopilar de SQL server, base de datos, nombres de usuario
Necesita los nombres de usuario para realizar este tutorial, base de datos y servidor de SQL Azure. Anote los nombres de **servidor**, la **base de datos**y el **usuario** de la base de datos de SQL Azure.

1. En el **portal de Azure**, haga clic en **más servicios** a la izquierda y seleccione **bases de datos SQL**.
2. En el **módulo de bases de datos SQL**, seleccione la **base de datos** que desea usar en este tutorial. Anote el **nombre de la base de datos**.  
3. En el módulo de **base de datos SQL** , haga clic en **Propiedades** , en **configuración**.
4. Tenga en cuenta los valores de **Nombre de servidor** y **El inicio de sesión de servidor de administración**.
5. Cierre todos los módulos, haga clic en **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Permitir que los servicios de Azure tener acceso a SQL server 
Asegúrese de **que **Permitir el acceso a servicios de Azure** opción activada para el servidor de SQL Azure para que el servicio de generador de datos puede tener acceso a su servidor de SQL Azure** . Para comprobar y activar esta opción, siga los pasos siguientes:

1. Haga clic en **más servicios** concentrador de la izquierda y haga clic en **servidores de SQL Server**.
2. Seleccione el servidor y haga clic en **Firewall** en **configuración**. 
4. En el módulo **configuraciones de Firewall** , haga clic en **Activar** para **Permitir el acceso a servicios de Azure**.
5. Cierre todos los módulos, haga clic en **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Preparar base de datos SQL y el almacenamiento de blobs 
Ahora, prepare el almacenamiento de blobs de Windows Azure y la base de datos de SQL Azure para el tutorial realizando los siguientes pasos:  

1. Inicie el Bloc de notas, pegue el texto siguiente y guardarlo como **emp.txt** a **C:\ADFGetStarted** carpeta en el disco duro.

        John, Doe
        Jane, Doe

2. Use herramientas como el [Explorador de almacenamiento de Azure](https://azurestorageexplorer.codeplex.com/) para crear el contenedor **adftutorial** y cargar el archivo **emp.txt** en el contenedor.

    ![Explorador de almacenamiento de Azure. Copiar datos de almacenamiento de blobs de base de datos de SQL](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
3. Utilice la siguiente secuencia de comandos SQL para crear la tabla de **emp** en la base de datos de SQL Azure.  


        CREATE TABLE dbo.emp
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

    **Si tiene instalado en el equipo de SQL Server 2012/2014:** siga las instrucciones de [paso 2: conectar con base de datos de SQL de la base de SQL Azure administrar con SQL Server Management Studio](../sql-database/sql-database-manage-azure-ssms.md#Step2) artículo para conectarse al servidor de SQL Azure y ejecutar la secuencia de comandos SQL. En este artículo, se utiliza el [portal de Azure clásico](http://manage.windowsazure.com), no el [nuevo portal de Azure](https://portal.azure.com), configurar firewall para un servidor de SQL Azure.

    Si el cliente no tiene permiso para acceder al servidor de SQL Azure, debe configurar el firewall de su servidor de SQL Azure para permitir el acceso desde su equipo (dirección IP). Vea [este artículo](../sql-database/sql-database-configure-firewall-settings.md) para conocer los pasos configurar el firewall para su servidor de SQL Azure.

Se han completado los requisitos previos. Puede crear un generador de datos con una de las siguientes maneras. Haga clic en una de las pestañas en la parte superior o en los siguientes vínculos para realizar el tutorial.     

- [Portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [API DE REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API DE .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Asistente para copiar](data-factory-copy-data-wizard-tutorial.md)
