<properties
   pageTitle="Analizar los registros de sitio Web mediante el análisis de lago de datos de Azure | Azure"
   description="Obtenga información sobre cómo analizar registros de sitio Web mediante el análisis de datos lago. "
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-analyze-website-logs-using-azure-data-lake-analytics"></a>Tutorial: Analizar los registros de sitio Web mediante el análisis de lago de datos de Azure

Obtenga información sobre cómo analizar los registros de sitio Web mediante el análisis de datos lago, especialmente en averiguar qué orígenes de referencia detectaron errores cuando trata de visitar el sitio Web.

>[AZURE.NOTE] Si desea ver la aplicación trabajando, ahorra tiempo para ir a través de [análisis de uso Azure datos lago interactivas tutoriales](data-lake-analytics-use-interactive-tutorials.md). En este tutorial se basa en la misma situación y el mismo código. El propósito de este tutorial es proporcionar a los desarrolladores de la experiencia de creación y ejecución de una aplicación de análisis de datos lago de principio a fin.

## <a name="prerequisites"></a>Requisitos previos:

- **2015 de visual Studio, Visual Studio de 2013 update 4, o Visual Studio 2012 con Visual C++ instalado**.
- **Microsoft Azure SDK para .NET versión 2.5 o posterior**.  Instalar mediante el [Instalador de plataforma Web](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Datos lago Tools para Visual Studio](http://aka.ms/adltoolsvs)**.

    Una vez instalada datos lago Tools para Visual Studio, verá un menú de **Lago de datos** en Visual Studio:

    ![Menú de SQL U Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)

- **Conocimientos básicos de análisis de datos lago y las herramientas de lago de datos para Visual Studio**. Para obtener más información, consulte:

    - [Introducción a Azure datos lago análisis con el Portal de Azure](data-lake-analytics-get-started-portal.md).
    - [Secuencia de comandos SQL de U desarrollar con datos lago tools para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

- **Una cuenta de análisis de datos lago.**  Consulte [crear una cuenta de Azure datos lago análisis](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).

    Las herramientas de lago de datos no admite la creación de cuentas de análisis de datos lago.  Así que debe crear mediante el Portal de Azure, Azure PowerShell, .NET SDK o CLI de Azure.
- **Cargue los datos de ejemplo en la cuenta de análisis de datos lago.** Vea [Cargar SearchLog.tsv a la cuenta de almacenamiento de datos lago predeterminada](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account).

    Para ejecutar una tarea de análisis de datos lago, necesitará algunos datos. Aunque las herramientas de lago de datos admite cargar el contenido de los datos, usará el portal para cargar los datos de ejemplo para facilitar la siga este tutorial.

## <a name="connect-to-azure"></a>Conectarse a Azure

Para poder crear y probar las secuencias de comandos de SQL U, primero debe conectarse a Azure.

**Para conectarse a datos lago Analytics**

1. Abra Visual Studio.
2. En el menú de **Lago de datos** , haga clic en **Opciones y configuración**.
4. Haga clic en **Iniciar sesión**o **Cambiar usuario** si alguien ha iniciado sesión en y a continuación, siga las instrucciones.
5. Haga clic en **Aceptar** para cerrar el cuadro de diálogo Opciones y configuración.

**Para examinar sus cuentas de análisis de datos lago**

1. En Visual Studio, abra el **Explorador de servidor** , presione **CTRL + ALT + S**.
2. Desde el **Explorador de servidores**, expanda **Azure**y, a continuación, expanda **El análisis de datos lago**. Deberá ver una lista de sus cuentas de análisis de datos lago si hay alguna. No puede crear cuentas de análisis de datos lago de studio. Para crear una cuenta, consulte [Introducción a Azure datos lago análisis con el Portal de Azure](data-lake-analytics-get-started-portal.md) o [Introducción a análisis de lago de datos de Azure con PowerShell de Azure](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>Desarrolle aplicaciones U SQL

Una aplicación de SQL U es principalmente una secuencia de comandos SQL U. Para obtener más información acerca de SQL U, consulte [Introducción a U SQL](data-lake-analytics-u-sql-get-started.md).

Operadores de suma definidas por el usuario puede agregar a la aplicación.  Para obtener más información, consulte [operadores para las tareas de análisis de datos lago definidas por el usuario de desarrollar U-SQL](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Para crear y enviar un trabajo de análisis de datos lago**

1. En el menú **archivo** , haga clic en **nuevo**y, a continuación, haga clic en **proyecto**.
2. Seleccione el tipo de proyecto U SQL.

    ![nuevo proyecto de Visual Studio de SQL U](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Haga clic en **Aceptar**. Visual studio crea una solución con un archivo de Script.usql.
4. Escriba la siguiente secuencia de comandos en el archivo Script.usql:

        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema.
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string,
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string,
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string,
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int,
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN

            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
            RETURN;
        END;

        // Create a table for storing referrers and status
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            PARTITIONED BY HASH(Year)
        ) AS

        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer,
                sc_status;

    Para conocer U-SQL, vea [Introducción a análisis de lago de datos U-SQL idioma](data-lake-analytics-u-sql-get-started.md).    

5. Agregar una secuencia de comandos de SQL U nueva a su proyecto y escriba lo siguiente:

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();

6. Cambiar a la primera secuencia de comandos de SQL U y junto al botón **Enviar** , especifique la cuenta de análisis.
7. Desde el **Explorador de soluciones**, haga clic con el botón secundario del mouse en **Script.usql**y, a continuación, haga clic en **Generar secuencia de comandos**. Comprobar los resultados en el panel de resultados.
8. Desde el **Explorador de soluciones**, haga clic con el botón secundario del mouse en **Script.usql**y, a continuación, haga clic en **Enviar Script**.
9. Compruebe que la **Cuenta de análisis** es donde desea ejecutar el trabajo y, a continuación, haga clic en **Enviar**. Resultados de envío y vínculo de tarea están disponibles en las herramientas de lago de datos para la ventana de resultados de Visual Studio cuando se complete la presentación.
10. Espere a que la tarea se completó correctamente.  Si se produce un error en el trabajo, es muy probable que falta el archivo de origen.  Consulte la sección requisitos previa de este tutorial. Para obtener información de solución de problemas, consulte [Monitor y solución de problemas de trabajos de análisis de lago de datos de Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    Cuando haya finalizado el trabajo, se verá la siguiente pantalla:

    ![análisis de datos lago analizar los registros de sitio Web de blogs](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)

11. Repita los pasos 7 - 10 ahora para **Script1.usql**.

>[AZURE.NOTE]No puede leer o escribir en una tabla de SQL U que se ha creado o modificado en la misma secuencia de comandos.  Por eso usar dos secuencias de comandos para este ejemplo.

**Para ver los resultados de la tarea**

1. Desde el **Explorador de servidores**, expanda **Azure**, expanda **El análisis de datos lago**, expandir su cuenta de análisis de datos lago, expandir **Cuentas de almacenamiento**, haga clic en la cuenta de almacenamiento de datos lago predeterminada y, a continuación, haga clic en **Explorador**.
2.  Haga doble clic en **las muestras** para abrir la carpeta y, a continuación, haga doble clic en los **resultados**.
3.  Haga doble clic en **UnsuccessfulResponsees.log**.
4.  También puede hacer doble clic dentro de la vista de gráfico de la tarea en el archivo de salida para ir directamente a la salida.

## <a name="see-also"></a>Vea también

Para empezar con datos lago análisis con herramientas diferentes, consulte:

- [Introducción a análisis de lago de datos con el Portal de Azure](data-lake-analytics-get-started-portal.md)
- [Introducción a análisis de datos lago con PowerShell de Azure](data-lake-analytics-get-started-powershell.md)
- [Introducción a análisis de lago de datos con el SDK de .NET](data-lake-analytics-get-started-net-sdk.md)

Para ver más temas de desarrollo:

- [Desarrollar secuencias de comandos de SQL U con datos lago Tools para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Empezar a trabajar con el lenguaje de datos de Azure lago análisis U-SQL](data-lake-analytics-u-sql-get-started.md)
- [Desarrollar operadores de SQL U definidas por el usuario para las tareas de análisis de datos lago](data-lake-analytics-u-sql-develop-user-defined-operators.md)
