<properties
   pageTitle="Introducir el catálogo de datos de Azure lago análisis U-SQL | Azure"
   description="Introducir el catálogo de datos de Azure lago análisis U-SQL"
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

# <a name="use-u-sql-catalog"></a>Usar el catálogo de SQL U

El catálogo de SQL U se usa para estructurar los datos y el código para que se pueden compartir por secuencias de comandos SQL U. El catálogo permite el mejor rendimiento posible con datos de lago de datos de Azure.

Cada cuenta de Azure datos lago análisis tiene exactamente un catálogo de SQL U asociada. No puede eliminar el catálogo de SQL U. Actualmente no se puede compartir U SQL catálogos entre cuentas de almacén de datos lago.

Cada catálogo U SQL contiene una base de datos denominado **patrón**. No se puede eliminar la base de datos de patrón.  Cada catálogo U SQL puede contener más de bases de datos adicionales.

Base de datos de SQL U contiene:

- Ensamblados: compartir código .NET entre secuencias de comandos SQL U.
- Funciones de valores de la tabla: compartir código SQL U entre secuencias de comandos SQL U.
- Tablas: compartir datos entre secuencias de comandos SQL U.
- Esquemas - compartir esquemas de tabla entre secuencias de comandos SQL U.

## <a name="manage-catalogs"></a>Administrar catálogos
Cada cuenta de Azure datos lago análisis tiene una cuenta de Azure datos lago almacén predeterminada asociada. Esta cuenta de almacén de datos lago se conoce como la cuenta predeterminada de almacén de datos lago. Catálogo de SQL U se almacena en la cuenta de almacén de datos lago predeterminada en la carpeta/catálogo al. No elimine los archivos en la carpeta/catálogo al.

### <a name="use-azure-portal"></a>Usar el portal de Azure

Consulte [administrar el análisis de datos lago con el portal](data-lake-analytics-manage-use-portal.md#view-u-sql-catalog)


### <a name="use-data-lake-tools-for-visual-studio"></a>Utilizar datos lago Tools para Visual Studio.

Puede usar datos lago Tools para Visual Studio para administrar el catálogo.  Para obtener más información acerca de las herramientas, consulte [Uso de datos lago Tools para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

**Para administrar el catálogo**

1. Abra Visual Studio y conectarse a azure. Para obtener las instrucciones, vea [conectarse a Azure](data-lake-analytics-data-lake-tools-get-started.md#connect-to-azure).
1. Abrir el **Explorador de servidores** , presione **CTRL + ALT + S**.
2. Desde el **Explorador de servidores**, expanda **Azure**, **Análisis de datos lago**, su cuenta de análisis de datos lago, expanda **bases de datos**y, a continuación, expanda **principal**.



    - Para agregar una nueva base de datos, haga clic en **base de datos**y, a continuación, haga clic en **Crear base de datos**.
    - Para agregar un nuevo ensamblado, haga clic con el botón **ensamblados**y, a continuación, haga clic en **Registrar ensamblado**.
    - Para agregar un nuevo esquema, haga clic en **los esquemas**y, a continuación, haga clic en "Crear esquema **.
    - Para agregar una nueva tabla, haga clic en **tablas**y, a continuación, haga clic en "" Crear tabla **.
    - Para agregar una nueva función con valores de tabla, consulte [operadores para las tareas de análisis de datos lago definidas por el usuario de desarrollar U-SQL](data-lake-analytics-u-sql-develop-user-defined-operators.md).


![Examinar catálogos de SQL U Visual Studio](./media/data-lake-analytics-use-u-sql-catalog/data-lake-analytics-browse-catalogs.png)


## <a name="see-also"></a>Vea también

- Introducción
    - [Introducción a análisis de lago de datos con el portal de Azure](data-lake-analytics-get-started-portal.md)
    - [Introducción a análisis de datos lago con PowerShell de Azure](data-lake-analytics-get-started-powershell.md)
    - [Introducción a análisis de lago de datos con Azure .NET SDK](data-lake-analytics-get-started-net-sdk.md)
    - [Desarrollar secuencias de comandos de SQL U con datos lago Tools para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
    - [Empezar a trabajar con el lenguaje de datos de Azure lago análisis U-SQL](data-lake-analytics-u-sql-get-started.md)

- U SQL y desarrollo
    - [Empezar a trabajar con el lenguaje de datos de Azure lago análisis U-SQL](data-lake-analytics-u-sql-get-started.md)
    - [Usar funciones de la ventana de SQL U para trabajos de análisis de lago de datos de Azure](data-lake-analytics-use-window-functions.md)
    - [Desarrollar operadores de SQL U definidas por el usuario para las tareas de análisis de datos lago](data-lake-analytics-u-sql-develop-user-defined-operators.md)

- Administración
    - [Administrar el análisis de lago de datos de Azure con Azure portal](data-lake-analytics-manage-use-portal.md)
    - [Administrar el análisis de lago de datos de Azure con PowerShell de Azure](data-lake-analytics-manage-use-powershell.md)
    - [Supervisar y solucionar problemas con Azure portal de trabajos de análisis de lago de datos de Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

- Tutorial de llevar a cabo
    - [Usar tutoriales interactivas de análisis de lago de datos de Azure](data-lake-analytics-use-interactive-tutorials.md)
    - [Analizar los registros de sitio Web mediante el análisis de lago de datos de Azure](data-lake-analytics-analyze-weblogs.md)
