<properties 
    pageTitle="Introducción a las herramientas de base de datos elásticas" 
    description="Explicación básica de la característica de herramientas de base de datos elástico de base de datos de SQL Azure, incluidos fácil para ejecutar la aplicación de ejemplo." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor="CarlRabeler"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="get-started-with-elastic-database-tools"></a>Introducción a las herramientas de base de datos elásticas

Este documento presenta la experiencia del desarrollador, ejecute la aplicación de ejemplo. El ejemplo crea una sencilla aplicación sharded y explora capacidades clave de herramientas de base de datos elástico. Muestra las funciones de la [biblioteca de cliente de base de datos elástico](sql-database-elastic-database-client-library.md)

Para instalar la biblioteca, vaya a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Observe que la biblioteca se instala con la aplicación de ejemplo que se describe a continuación.

## <a name="prerequisites"></a>Requisitos previos

1. Se requiere Visual Studio 2012 o posterior con C#. Descargar una versión gratuita de [Descargas de Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
2. NuGet 2.7 o superior. Para obtener la última versión, consulte [Instalar NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)

## <a name="download-and-run-the-sample-app"></a>Descargar y ejecutar la aplicación de ejemplo

La **elásticos base de datos con SQL Azure: Introducción** aplicación de ejemplo muestra los aspectos más importantes de la experiencia de desarrollo de aplicaciones sharded con herramientas de elásticos de la base de datos de SQL Azure. Se centra en casos de uso clave para [fragmentar asignar administración](sql-database-elastic-scale-shard-map-management.md), [enrutamiento según los datos](sql-database-elastic-scale-data-dependent-routing.md) y [consultas de varias partes](sql-database-elastic-scale-multishard-querying.md). Para descargar y ejecutar el ejemplo, siga estos pasos: 

1. Abra Visual Studio y seleccione **archivo -> Nuevo -> proyecto**.
2. En el cuadro de diálogo, haga clic **en línea**.

    ![Nuevo proyecto > en línea][2]
3. A continuación, haga clic en **Visual C#** **ejemplos**.

    ![Haga clic en Visual C#][3]
4. En el cuadro Buscar, escriba **db elástica** para buscar los datos de ejemplo. El título que aparecerá **Elástico herramientas DB para SQL Azure - Introducción** .

    ![Cuadro de búsqueda][1]
 
5. Seleccione los datos de ejemplo, elija un nombre y una ubicación para el nuevo proyecto y haga clic en **Aceptar** para crear el proyecto.
6. Abra el archivo **app.config** en la solución para el proyecto de ejemplo y siga las instrucciones en el archivo para agregar el nombre del servidor de base de datos de SQL Azure y su información de inicio de sesión (nombre de usuario y contraseña).
7. Crear y ejecutar la aplicación. Cuando se le solicite, permitir que Visual Studio restaurar los paquetes de NuGet de la solución. Esto descargue la última versión de la biblioteca de cliente de base de datos elástico desde NuGet.
8. Reproducir con las diferentes opciones para obtener más información sobre las funciones de la biblioteca de cliente. Tenga en cuenta los pasos que tiene la aplicación en la consola de salida y no dude en explorar el código en segundo plano.

    ![progreso][4]

Enhorabuena, ha creado y ejecute la aplicación sharded primera con herramientas de base de datos elásticos en base de datos de SQL Azure. Eche un vistazo rápido a la shards que el ejemplo que se creó mediante una conexión con Visual Studio o SQL Server Management Studio para su servidor de base de datos de Azure. Verá nuevas bases de datos de ejemplo fragmentar y una base de datos de administrador de mapa de partes que ha creado la muestra.

> [AZURE.IMPORTANT] Se recomienda usar siempre la versión más reciente de Management Studio permanezca sincronizado con las actualizaciones de Microsoft Azure y base de datos de SQL. [Actualizar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


### <a name="key-pieces-of-the-code-sample"></a>Partes clave de código de ejemplo

1. **Administración de Shards y mapas de partes**: el código muestra cómo trabajar con shards, rangos, y las asignaciones de archivo **ShardMapManagerSample.cs**. Puede encontrar más información sobre este tema aquí: [Administración del mapa de partes](http://go.microsoft.com/?linkid=9862595).  
2. **Enrutamiento según los datos**: enrutamiento de transacciones a las partes de la derecha se muestra en **DataDependentRoutingSample.cs**. Para obtener más detalles, consulte [Enrutamiento según los datos](http://go.microsoft.com/?linkid=9862596). 
3. **Realizar consultas sobre varios Shards**: realizar consultas a través de shards se muestran en el archivo **MultiShardQuerySample.cs**. Para obtener más detalles, consulte [Consultas de varias partes](http://go.microsoft.com/?linkid=9862597).
4. **Agregar vacías shards**: agregar iterativo shards vacías nuevas realiza el código en el archivo **AddNewShardsSample.cs**. Detalles de este tema se abordan: [Administración del mapa de partes](http://go.microsoft.com/?linkid=9862595).

### <a name="other-elastic-scale-operations"></a>Otras operaciones de escala elásticas

1. **Dividir una fragmentar existente**: la capacidad para dividir shards se proporciona a través de la **herramienta de combinación de división**. Puede encontrar más información sobre esta herramienta aquí: [Introducción a la herramienta de combinación de división](sql-database-elastic-scale-overview-split-and-merge.md).
2. **Combinar shards existentes**: combinaciones de fragmentar también se realizan mediante la **herramienta de combinación de división**. Para obtener más información, consulte: [Introducción a la herramienta de combinación de división](sql-database-elastic-scale-overview-split-and-merge.md).   


## <a name="cost"></a>Costo

Las herramientas de base de datos elásticos son de forma gratuita. Herramientas de base de datos elástico imponer cargos adicionales sobre el costo para el uso de Azure. 

Por ejemplo, la aplicación de ejemplo crea nuevas bases de datos. El coste depende de la edición de la base de datos de base de datos de SQL Azure que elija y el uso de la aplicación de Azure.

Para obtener información sobre precios, consulte [Los detalles de precios de base de datos de SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de las herramientas de base de datos elástico, consulte:

* [Mapa de documentación de herramientas de elásticos de la base de datos](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/) 
-    Ejemplos de código: 
    -    [DB elástico con SQL Azure: Introducción](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [DB elástico con SQL Azure - integración con el marco de la entidad](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [Fragmentar elasticidad en el centro de secuencia de comandos](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
-    Blog: [anuncio elástico escala](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
-    Canal 9: [elástico escala de vídeo de introducción](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
-    Foro de discusión: [foro de la base de datos de SQL Azure](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
-    Para medir el rendimiento: [contadores de rendimiento para el administrador del mapa de partes](sql-database-elastic-database-client-library.md)


<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
 
