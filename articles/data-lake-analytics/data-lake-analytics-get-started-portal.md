<properties 
   pageTitle="Introducción a Azure datos lago análisis con Azure portal | Azure" 
   description="Aprenda a usar el portal de Azure para crear una cuenta de análisis de datos lago, cree una tarea de análisis de datos lago mediante SQL U y enviar el trabajo. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/06/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-portal"></a>Tutorial: Introducción a Azure datos lago análisis con Azure portal

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Obtenga información sobre cómo usar el portal de Azure para crear cuentas de análisis de lago de datos de Azure, definir los trabajos de análisis de datos lago en [SQL U](data-lake-analytics-u-sql-get-started.md)y enviar trabajos al servicio de análisis de datos lago. Para obtener más información sobre el análisis de datos lago, vea [información general de análisis de lago de datos de Azure](data-lake-analytics-overview.md).

En este tutorial, desarrollar un trabajo que lee una pestaña archivo de valores (TSV) separados y lo convierte en un archivo de separados por comas (CSV) de valores. Para pasar por el mismo tutorial utilizar otras herramientas compatibles, haga clic en las pestañas en la parte superior de esta sección. Una vez que se complete correctamente la primera tarea, puede empezar a escribir las transformaciones de datos más complejas con SQL U.

##<a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe tener los siguientes elementos:

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).

##<a name="create-data-lake-analytics-account"></a>Crear cuenta de análisis de datos lago

Debe tener una cuenta de análisis de datos lago antes de poder ejecutar los trabajos.

Cada cuenta de análisis de datos lago tiene una dependencia de cuenta [Lago almacén de datos de Azure]() .  Esta cuenta se conoce como la cuenta predeterminada de almacén de datos lago.  Puede crear la cuenta de almacén de datos lago previamente o cuando se crea la cuenta de análisis de datos lago. En este tutorial, creará la cuenta de la tienda de lago de datos con la cuenta de análisis de datos lago.

**Para crear una cuenta de análisis de datos lago**

1. Inicie sesión el [portal de Azure](https://portal.azure.com).
2. Haga clic en **nuevo**, haga clic en **inteligencia + análisis**y, a continuación, haga clic en **Análisis de datos lago**.
3. Escriba o seleccione los valores siguientes:

    ![Módulo de portal de análisis de lago de datos de Azure](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

    - **Nombre**: el nombre de la cuenta de análisis de datos lago.
    - **Suscripción**: elija la suscripción de Azure utilizada para la cuenta de análisis.
    - **Grupo de recursos**. Seleccione un grupo de recursos de Azure existente o cree uno nuevo. Administrador de recursos de Azure le permite trabajar con los recursos de la aplicación como un grupo. Para obtener más información, vea [Información general del Administrador de recursos de Azure](resource-group-overview.md). 
    - **Ubicación**. Seleccione un centro de datos de Azure para la cuenta de análisis de datos lago. 
    - **Almacén de lago de datos**: cuenta de cada análisis de lago de datos tiene una cuenta de almacén de datos lago dependiente. La cuenta de análisis de datos lago y el almacén de datos lago dependientes deben encontrarse en el mismo centro de datos de Azure. Siga las instrucciones para crear una nueva cuenta de almacén de datos lago o seleccione uno existente.

8. Haga clic en **crear**. Accederá a la pantalla principal del portal. Un nuevo mosaico se agrega a la StartBoard con la etiqueta que muestra "Implementación de análisis de lago de datos de Azure". Se necesitan unos minutos para crear una cuenta de análisis de datos lago. Cuando se crea la cuenta, el portal abre la cuenta en una hoja nueva.

Después de crea una cuenta de análisis de datos lago, puede agregar cuentas adicionales de almacén de datos lago y almacenamiento de Azure. Para obtener instrucciones, vea [administrar el análisis de datos lago orígenes de datos de la cuenta](data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

##<a name="prepare-source-data"></a>Preparar los datos de origen

En este tutorial, procesar algunos registros de búsqueda.  El registro de búsqueda puede almacenarse en la tienda de lago dData o almacenamiento de blobs de Windows Azure. 

El portal de Azure proporciona una interfaz de usuario para copiar algunos archivos de datos de ejemplo en la cuenta de almacén de datos lago predeterminada, que incluyen un archivo de registro de la búsqueda.

**Para copiar los archivos de datos de ejemplo**

1. Desde el [portal de Azure](https://portal.azure.com), abra su cuenta de análisis de datos lago.  Consulte [administrar el análisis de datos lago cuentas](data-lake-analytics-get-started-portal.md#manage-accounts) para crear uno y abrir la cuenta en el portal.
3. Expandir el panel de **Essentials** y, a continuación, haga clic en **explorar secuencias de comandos de ejemplo**. Se abre otro módulo denominado **Secuencias de comandos de ejemplo**.

    ![Secuencia de comandos de ejemplo portal de análisis de lago de datos de Azure](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-sample-scripts.png)

4. Haga clic en la **Falta de datos de ejemplo** para copiar los archivos de datos de ejemplo. Cuando haya terminado, el portal muestra **datos de ejemplo actualizados correctamente**.
7. En el módulo de cuenta de análisis de datos lago, haga clic en **Explorador de datos** en la parte superior. 

    ![Botón de explorador de datos de análisis de lago de datos de Azure](./media/data-lake-analytics-get-started-portal/data-lake-analytics-data-explorer-button.png)

    Se abre dos módulos. Uno es el **Explorador de datos**y la otra es la cuenta predeterminada de almacén de datos lago.
8. En el módulo de cuenta del almacén de datos lago predeterminado, haga clic en **ejemplos** para expandir la carpeta y a continuación, haga clic en **datos** para expandir la carpeta. Deberá ver los archivos y carpetas siguientes:

    - AmbulanceData /
    - AdsLog.tsv
    - SearchLog.tsv
    - Version.txt
    - WebLog.log
    
    En este tutorial, use SearchLog.tsv.

En la práctica, o bien programar sus aplicaciones para escribir datos en las cuentas de almacenamiento vinculado o cargar datos. Para cargar archivos, vea [cargar datos al almacén de datos lago](data-lake-analytics-manage-use-portal.md#upload-data-to-adls) o [cargar datos con el almacenamiento de blobs de Windows](data-lake-analytics-manage-use-portal.md#upload-data-to-wasb).

##<a name="create-and-submit-data-lake-analytics-jobs"></a>Crear y enviar trabajos de análisis de datos lago

Después de haber preparado el origen de datos, puede empezar a desarrollar una secuencia de comandos SQL U.  

**Para enviar un trabajo**

1. Módulo de cuenta de análisis de datos lago en el portal, haga clic en **Nueva tarea**. 

    ![Botón nueva de trabajo de lago de datos de Azure Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job-button.png)

    Si no ve el módulo, vea [Abrir una cuenta de análisis de datos lago desde el portal](data-lake-analytics-manage-use-portal.md#access-adla-account).
2. Escriba el **Nombre del trabajo**y el siguiente script de SQL U:

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
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    ![crear tareas de datos de Azure lago análisis U-SQL](./media/data-lake-analytics-get-started-portal/data-lake-analytics-new-job.png)

    Esta secuencia de comandos SQL U lee el archivo de datos de origen con **Extractors.Tsv()**y, a continuación, crea un archivo csv con **Outputters.Csv()**. 
    
    No modifique las dos rutas a menos que copie el archivo de origen en una ubicación diferente.  Análisis de datos lago crea la carpeta de salida si no existe.  En este caso, usamos rutas simples, relativas.  
    
    Es más fácil usar rutas relativas para los archivos almacenados en cuentas de lago de datos de forma predeterminada. También puede usar rutas de acceso absolutas.  Por ejemplo 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
      

    Para obtener más información acerca de SQL U, vea [Introducción a Azure datos lago análisis U-SQL idioma](data-lake-analytics-u-sql-get-started.md) y la [referencia del lenguaje SQL U](http://go.microsoft.com/fwlink/?LinkId=691348).
     
3. Haga clic en **Enviar trabajo** desde la parte superior.   
4. Espere a que el estado del trabajo cambia a **correcta**. Puede ver que el trabajo tardó aproximadamente un minuto en completarse.
    
    En caso de error en el trabajo, consulte [Monitor y solución de problemas de trabajos de análisis de datos lago](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

5. En la parte inferior de la hoja, haga clic en la pestaña de **salida** y, a continuación, haga clic en **SearchLog de datos Lake.csv**. Puede obtener una vista previa, descargar, cambiar el nombre y elimine el archivo de salida.

    ![Propiedades del archivo de salida de trabajo de análisis de lago de datos de Azure](./media/data-lake-analytics-get-started-portal/data-lake-analytics-output-file-properties.png)


##<a name="see-also"></a>Vea también

- Para ver una consulta más compleja, vea [registros de sitio Web de analizar mediante el análisis de lago de datos de Azure](data-lake-analytics-analyze-weblogs.md).
- Para empezar a desarrollar aplicaciones SQL U, vea [secuencias de comandos SQL de U desarrollar mediante datos lago Tools para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para obtener U SQL, consulte [Introducción a Azure datos lago análisis U-SQL idioma](data-lake-analytics-u-sql-get-started.md).
- Para las tareas de administración, vea [administrar el análisis de Azure datos lago con Azure portal](data-lake-analytics-manage-use-portal.md).
- Para obtener una visión general de análisis de lago de datos, vea [información general de análisis de lago de datos de Azure](data-lake-analytics-overview.md).
- Para ver el mismo tutorial con otras herramientas, haga clic en los selectores de pestaña en la parte superior de la página.
- Para registrar información de diagnóstico, consulte [registros de diagnósticos de acceso para el análisis de lago de datos de Azure](data-lake-analytics-diagnostic-logs.md)
