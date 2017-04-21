<properties 
   pageTitle="Depurar trabajos U SQL | Microsoft Azure" 
   description="Obtenga información sobre cómo depurar SQL U vértice error con Visual Studio. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>



#<a name="debug-c-code-in-u-sql-for-data-lake-analytics-jobs"></a>Depurar código C# en SQL U para trabajos de análisis de datos lago 

Obtenga información sobre cómo usar herramientas de datos de Azure lago Visual Studio para depurar trabajos con SQL U error debido a errores dentro del código de usuario. 

La herramienta de Visual Studio le permite descargar código compilado y datos de vértice necesarios de clúster de seguimiento y depuración de errores trabajos.

Los sistemas de datos grande normalmente proporcionan modelo de capacidad de ampliación a través de idiomas como Java, C#, Python, etcetera. Muchos de estos sistemas proporcionan limitado runtime información de depuración, que dificulta la depuración de errores de tiempo de ejecución en el código personalizado. Las últimas herramientas de Visual Studio incluye una característica denominada "Error al vértice depurar". Uso de esta característica, puede descargar los datos en tiempo de ejecución de Azure en estación de trabajo local para que se puede depurar errores C# código personalizado con el mismo runtime y datos de entrada exactos de la nube.  Después de que se corrigen los problemas, volver a ejecutar el código revisado en Azure desde las herramientas.

Para ver una presentación de vídeo de esta característica, vea [Depurar el código personalizado en el análisis de lago de datos de Azure](https://mix.office.com/watch/1bt17ibztohcb).

>[AZURE.NOTE] Puede bloquear o bloquearse si no tiene las siguientes actualizaciones de dos windows Visual Studio: [Microsoft Visual C++ 2015 redistribuible actualización 2](https://www.microsoft.com/download/details.aspx?id=51682), [Universal en tiempo de ejecución de C para Windows](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0).


##<a name="prerequisites"></a>Requisitos previos
-   Han pasado por el artículo [Introducción](data-lake-analytics-data-lake-tools-get-started.md) .

## <a name="create-and-configure-debug-projects"></a>Crear y configurar proyectos de depuración

Cuando abre un trabajo con error en la herramienta de datos lago Visual Studio, recibirá una alerta. La información de error detallada se muestran en la ficha de error y la barra amarilla de alerta en la parte superior de la ventana. 

![Vértice de descarga de visual studio de depuración de datos lago análisis U-SQL Azure](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

**Para descargar el vértice y crear una solución de depuración**

1.  Abrir un trabajo de SQL U error en Visual Studio.
2.  Haga clic en **Descargar** para descargar todos los recursos necesarios y las secuencias de entrada. Si el error en la descarga, haga clic en **Reintentar** .
3.  Una vez completada la descarga para crear un proyecto de depuración local, haga clic en **Abrir** . Se creará una nueva solución de Visual Studio denominada **VertexDebug** con un proyecto vacío denominado **LocalVertexHost** .

Si los operadores definidas por el usuario se utilizan en el código SQL U detrás (Script.usql.cs), debe crear un proyecto de C# de biblioteca de clases con el código de operadores definidas por el usuario e incluir el proyecto en la solución VertexDebug.

Si ha registrado ensamblados DLL a la base de datos de análisis de datos lago, debe agregar el código fuente de los ensamblados a la solución VertexDebug.
 
Si ha creado una biblioteca de clases C# independiente para el código SQL U y ensamblados DLL registrados para la base de datos de análisis de datos lago, debe agregar el proyecto de código fuente C# de los ensamblados a la solución VertexDebug.

En algunos casos poco comunes, operadores de definidas por el usuario se usan en SQL U archivo de código subyacente (Script.usql.cs) en la solución original. Si desea hacer que funcione, debe crear una biblioteca de C# que contiene el código fuente y cambie el nombre de ensamblado a la que se registra en el clúster. Puede obtener el nombre de ensamblado registrado en el clúster activando la secuencia de comandos que obtuvo ejecuta en el clúster. Puede hacerlo abriendo el trabajo U SQL y haga clic en "índice" en el panel de trabajo. 

**Para configurar la solución**

1.  Desde el Explorador de soluciones, haga clic en el proyecto de C# que acaba de crear y, a continuación, haga clic en **Propiedades**.
2.  Establecer la ruta de acceso de resultados como proyecto LocalVertexHost ruta del directorio de trabajo. Puede obtener la ruta de acceso del directorio de trabajo de proyecto de LocalVertexHost a través de propiedades de LocalVertexHost.
3.  Crear un proyecto de C# para poner el archivo .pdb en el directorio de trabajo de proyecto LocalVertexHost o puede copiar el archivo .pdb manualmente a esta carpeta.
4.  En **Configuración de excepción**, active excepciones Common Language Runtime:

![Configuración de visual studio de depuración de datos lago análisis U-SQL Azure](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)
 
##<a name="debug-the-job"></a>Depurar el trabajo

Una vez que ha creado una solución de depuración descargando el vértice y ha configurado el entorno, puede empezar a depurar el código SQL U.

1.  Desde el Explorador de soluciones, haga clic en el proyecto **LocalVertexHost** que acaba de crear, elija **Depurar**y, a continuación, haga clic en **Iniciar nueva instancia**. La LocalVertexHost se debe establecer como proyecto de inicio. Puede ver el mensaje siguiente para la primera vez que se puede pasar por alto. Puede tardar hasta un minuto para ir a la pantalla de depuración.
 
    ![Advertencia de visual studio de depuración de datos lago análisis U-SQL Azure](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

4.  Use Visual Studio según la experiencia de depuración (inspección, variables, etc.) para solucionar el problema. 
5.  Después de que ha identificado un problema, corrija el código y volver a generar el proyecto C# antes de comprobar de nuevo hasta que se resuelven todos los problemas. Después de la depuración se ha completado correctamente, la ventana de resultados que muestra el mensaje siguiente 

        The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).
 
##<a name="resubmit-the-job"></a>Vuelva a enviar el trabajo

Cuando haya terminado de depurar el código SQL U, puede volver a enviar el trabajo con error.

1. Registrar nuevos ensamblados DLL a la base de datos ADLA.

    1.  Explorador/nube del explorador de servidores en herramienta datos lago de Visual Studio, expanda el nodo de **bases de datos** 
    2.  Haga clic con el botón ensamblados a registrar ensamblados. 
    3.  Registrar los ensamblados de DLL nuevo a la base de datos ADLA.
 
2.  O bien, copie el código de C# a script.usql.cs--C# archivo de código subyacente.
3.  Vuelva a enviar su trabajo.

##<a name="next-steps"></a>Pasos siguientes

- [Tutorial: Introducción a lenguaje de datos de Azure lago análisis U-SQL](data-lake-analytics-u-sql-get-started.md)
- [Tutorial: desarrollar secuencias de comandos de SQL U con datos lago Tools para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Desarrollar operadores U SQL definidas por el usuario para las tareas de análisis de lago de datos de Azure](data-lake-analytics-u-sql-develop-user-defined-operators.md)

