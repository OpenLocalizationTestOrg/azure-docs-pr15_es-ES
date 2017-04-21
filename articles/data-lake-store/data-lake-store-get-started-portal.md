<properties 
   pageTitle="Empezar a trabajar con el almacén de datos de lago | Azure" 
   description="Usar el portal para crear una cuenta de almacén de datos lago y realizar operaciones básicas en el almacén de datos de lago" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/13/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-the-azure-portal"></a>Introducción a Azure datos lago tienda con el Portal de Azure

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API DE REST](data-lake-store-get-started-rest-api.md)
- [CLI de Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Aprenda a usar el Portal de Azure para crear una cuenta de la tienda de lago de datos de Azure y realizar operaciones básicas, como crear carpetas, cargar y descargar archivos de datos, elimine la cuenta, etcetera. Para obtener más información acerca de la tienda de lago de datos, vea [Información general de Azure lago almacén de datos](data-lake-store-overview.md).

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener el siguiente:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).

## <a name="do-you-learn-fast-with-videos"></a>¿Puede obtener información rápidamente con vídeos?

Vea los siguientes vídeos para empezar a trabajar con el almacén de datos de lago.

* [Crear una cuenta de almacén de datos lago](https://mix.office.com/watch/1k1cycy4l4gen)
* [Administrar datos en el almacén de lago de datos mediante el Explorador de datos](https://mix.office.com/watch/icletrxrh6pc)

## <a name="create-an-azure-data-lake-store-account"></a>Crear una cuenta de la tienda de lago de datos de Azure

1. Inicie sesión el nuevo [Portal de Azure](https://portal.azure.com).

2. Haga clic en **nuevo**, haga clic en **datos + almacenamiento**y, a continuación, haga clic en **Almacén de lago de datos de Azure**. Lea la información en el módulo de **Almacén de lago de datos de Azure** y, a continuación, haga clic en **crear** en la esquina inferior izquierda de la hoja.

3. En el módulo de **Almacén de lago de datos de nuevo** , indique los valores tal como se muestra en la siguiente captura de pantalla:

    ![Crear una nueva cuenta de lago almacén de datos de Azure] (./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Crear una nueva cuenta de lago de datos de Azure")

    - **Suscripción**. Seleccione la suscripción en la que desea crear una nueva cuenta de almacén de datos lago.
    - **Grupo de recursos**. Seleccione un grupo de recursos existente o haga clic en **crear un grupo de recursos** para crear uno. Un grupo de recursos es un contenedor que contiene recursos relacionados para una aplicación. Para obtener más información, consulte [Grupos de recursos en Azure](azure-resource-manager/resource-group-overview.md#resource-groups).
    - **Ubicación**: seleccione una ubicación donde desee crear la cuenta de almacén de datos lago.

4. Si desea que la cuenta de almacén de datos lago sean accesibles desde el Startboard, seleccione **Anclar al Startboard** .

5. Haga clic en **crear**. Si opta por anclar la cuenta a la startboard, volverá a la startboard y puede ver el progreso de su cuenta de almacén de datos lago aprovisionamiento. Una vez que se aprovisiona la cuenta de la tienda de lago de datos, aparece el módulo de cuenta.

6. Expanda la **Essentials** desplegable para ver la información sobre su cuenta de almacén de datos lago como el recurso de grupo es una parte de la ubicación, etcetera. Haga clic en el icono de **Inicio rápido** para ver los vínculos a otros recursos relacionados con el almacén de datos de lago.

    ![Cuenta de su almacén de lago de datos de Azure] (./media/data-lake-store-get-started-portal/ADL.Account.QuickStart.png "Cuenta de su lago de datos de Azure")

## <a name="createfolder"></a>Crear carpetas en la cuenta de la tienda de lago de datos de Azure

Puede crear carpetas en su cuenta de almacén de datos de lago para administrar y almacenar datos.

1. Abra la cuenta de almacén de datos lago que acaba de crear. En el panel izquierdo, haga clic en **Examinar**, haga clic en **Almacén de datos de lago**y en el módulo de almacén de lago de datos, haga clic en el nombre de cuenta en la que desea crear carpetas. Si ancla la cuenta a la startboard, haga clic en mosaico de la cuenta.

2. En el módulo de almacén de datos lago cuenta, haga clic en **Explorador de datos**.

    ![Crear carpetas en el almacén de datos de lago cuenta] (./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Crear carpetas en el almacén de datos de lago cuenta")

3. En el módulo de almacén de datos lago cuenta, haga clic en **Nueva carpeta**, escriba un nombre para la nueva carpeta y, a continuación, haga clic en **Aceptar**.
    
    ![Crear carpetas en el almacén de datos de lago cuenta] (./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Crear carpetas en el almacén de datos de lago cuenta")
    
    La carpeta recién creada se mostrarán en el módulo de **Explorador de datos** . Puede crear carpetas anidadas hasta cualquier nivel.

    ![Crear carpetas en la cuenta de lago de datos] (./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Crear carpetas en la cuenta de lago de datos")


## <a name="uploaddata"></a>Cargar datos en la cuenta de la tienda de lago de datos de Azure

Puede cargar los datos a una cuenta de Azure datos lago almacén directamente en el nivel raíz o a una carpeta que ha creado dentro de la cuenta. En la captura de pantalla a continuación, siga los pasos para cargar un archivo a una subcarpeta de la hoja de **Datos el explorador** . En esta captura de pantalla, el archivo cargado en una subcarpeta que se muestra en las rutas de navegación (marcados en un cuadro rojo).

Si está buscando algunos datos de ejemplo cargar, puede obtener la carpeta **Ambulancia datos** del [Repositorio de Azure datos lago Git](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

![Cargar datos] (./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Cargar datos")


## <a name="properties"></a>Propiedades y acciones disponibles en los datos almacenados

Haga clic en el archivo recién agregado para abrir la hoja de **Propiedades** . Las propiedades asociadas con el archivo y las acciones que puede realizar en el archivo están disponibles en este módulo. También puede copiar la ruta completa al archivo en su cuenta de Azure datos lago almacén resaltado en el cuadro rojo en la siguiente captura de pantalla.

![Propiedades de los datos] (./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Propiedades de los datos")

* Haga clic en **vista previa** para ver una vista previa del archivo, directamente desde el explorador. Puede especificar el formato de la vista previa también. Haga clic en **vista previa**, haga clic en **formato** en la hoja de **Vista previa del archivo** y en el módulo de **Formato de archivo de vista previa** , especifique las opciones como el número de filas para mostrar, codificación usar delimitador usar, etcetera.

  ![Formato de archivo de vista previa] (./media/data-lake-store-get-started-portal/ADL.File.Preview.png "Formato de archivo de vista previa")

* Haga clic en **Descargar** para descargar el archivo en el equipo.

* Haga clic en **cambiar el nombre de archivo** para cambiar el nombre del archivo.

* Haga clic en **Eliminar archivos** para eliminar el archivo.


## <a name="secure-your-data"></a>Proteger los datos

Puede proteger los datos almacenados en su cuenta de Azure datos lago tienda con Azure Active Directory y control de acceso (ACL). Para obtener instrucciones sobre cómo hacerlo, vea [proteger datos Azure almacén de datos lago](data-lake-store-secure-data.md).


## <a name="delete-azure-data-lake-store-account"></a>Cuenta de la tienda de lago de datos de Azure eliminar

Para eliminar una cuenta de Azure datos lago tienda, desde el módulo de almacén de lago de datos, haga clic en **Eliminar**. Para confirmar la acción, se le pedirá que escriba el nombre de la cuenta que desea eliminar. Escriba el nombre de la cuenta y, a continuación, haga clic en **Eliminar**.

![Eliminar datos lago cuenta] (./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Eliminar datos lago cuenta")


## <a name="next-steps"></a>Pasos siguientes

- [Proteger los datos en el almacén de datos de lago](data-lake-store-secure-data.md)
- [Usar el análisis de datos de Azure lago con el almacén de datos de lago](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar Azure HDInsight con el almacén de datos de lago](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Registros de diagnóstico de acceso para el almacén de datos de lago](data-lake-store-diagnostic-logs.md)
