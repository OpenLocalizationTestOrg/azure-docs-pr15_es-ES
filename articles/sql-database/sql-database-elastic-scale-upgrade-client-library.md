< propiedades
    
    pageTitle="Upgrade to the latest elastic database client library | Microsoft Azure" 
    description="Upgrade apps and libraries using Nuget" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Actualizar una aplicación para usar la biblioteca de cliente de base de datos elástico más reciente

Las nuevas versiones de la [biblioteca de cliente de base de datos elástico](sql-database-elastic-database-client-library.md) están disponibles a través de NuGetand la interfaz de administrador de NuGetPackage en Visual Studio. Actualizaciones contienen correcciones de errores y soporte técnico para las nuevas funciones de la biblioteca de cliente.

**Para la versión más reciente:** Vaya a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Volver a crear la aplicación con la nueva biblioteca, así como cambiar los metadatos de administrador de mapa fragmentar existentes almacenados en las bases de datos de SQL Azure para admitir características nuevas.

Realizar estos pasos en orden garantiza que las versiones anteriores de la biblioteca de cliente ya no están presentes en un entorno cuando se actualizan los objetos de metadatos, lo que significa que no se crearán objetos de metadatos de la versión anterior después de la actualización.   

## <a name="upgrade-steps"></a>Pasos de la actualización

**1. actualizar las aplicaciones.** En Visual Studio, descargar y hacer referencia a la última versión de la biblioteca de cliente en todos los proyectos de desarrollo que utilizan la biblioteca; a continuación, volver a crear e implementar. 

 * En la solución de Visual Studio, seleccione **Herramientas** --> **Administrador de paquetes de NuGet** -->  **Administrar paquetes de NuGet para la solución**. 
 * (Visual Studio 2013) En el panel izquierdo, seleccione **actualizaciones**y, a continuación, seleccione el botón de **actualización** en el paquete de **Base de datos de SQL Azure elástico biblioteca de cliente escala** que aparece en la ventana.
 * (Visual Studio 2015) Establezca el cuadro Filtro actualización **disponible**. Seleccione el paquete de actualización y haga clic en el botón **Actualizar** .
    
 
 * Compile e implemente. 

**2. actualizar las secuencias de comandos.** Si está utilizando las secuencias de comandos de **PowerShell** para administrar shards, [Descargue la nueva versión de la biblioteca](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) y copiarla en el directorio que se ejecutan secuencias de comandos. 

**3. actualizar su servicio de combinación de división.** Si utiliza la herramienta de combinación de división de elásticos de la base de datos para reorganizar los datos sharded, [descargar e instalar la última versión de la herramienta](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Detallada de pasos de la actualización para el servicio puede encontrarse [aquí](sql-database-elastic-scale-overview-split-and-merge.md). 

**4. actualice las bases de datos de administrador de mapa fragmentar**. Actualizar los metadatos auxiliares los mapas de partes de la base de datos de SQL Azure.  Hay dos formas para hacer esto, con PowerShell o C#. Ambas opciones se muestran a continuación.

***Opción 1: Actualizar metadatos con PowerShell***

1. Descargue la herramienta de línea de comandos más reciente para NuGet desde [aquí](http://nuget.org/nuget.exe) y guardar en una carpeta. 

2. Abra un símbolo del sistema, desplácese hasta la misma carpeta y ejecute el comando:`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`

3. Vaya a la subcarpeta que contiene la nueva versión DLL de cliente que acaba de descargar, por ejemplo:`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`

4. Descargar el scriptlet de actualización de cliente elásticos de la base de datos desde el [Centro de secuencia de comandos](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)y guárdelo en la misma carpeta que contiene el archivo DLL.

5. Desde esa carpeta, ejecute ".\upgrade.ps1 PowerShell" desde el símbolo del sistema y siga las indicaciones.
 
***Opción 2: Actualizar metadatos mediante C#***

También puede crear una aplicación de Visual Studio que abre su ShardMapManager, iteración en todos los shards y realiza la actualización de metadatos mediante una llamada a los métodos [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) y [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) como en este ejemplo: 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 
    
    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

Estas técnicas para actualizaciones de metadatos se pueden aplicar varias veces sin daños. Por ejemplo, si una versión anterior de cliente crea accidentalmente un fragmentar después de que ya ha actualizado, puede volver a ejecutar actualización en todas las shards para asegurarse de que la última versión de metadatos está presente en toda la infraestructura. 

**Nota:**  Las nuevas versiones de la biblioteca de cliente publicación hasta la fecha seguir trabajando con las versiones anteriores de los metadatos del Administrador de mapa fragmentar en base de datos de SQL Azure y viceversa.   Sin embargo, para aprovechar las ventajas de algunas de las características nuevas en el cliente más reciente, deben actualizarse metadatos.   Tenga en cuenta que las actualizaciones de metadatos no afectará a los datos del usuario o los datos específicos de la aplicación, solo objetos creados y utilizados por el administrador del mapa de partes.  Y las aplicaciones siguen funcionando a través de la secuencia de actualización que se indica más arriba. 

## <a name="elastic-database-client-version-history"></a>Historial de versiones de cliente de base de datos elástico 

Historial de versiones, vaya a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]  


<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
 