<properties
   pageTitle="Migrar: Utilidad de migración de almacenamiento de datos | Microsoft Azure"
   description="Migrar al almacén de datos SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# <a name="data-warehouse-migration-utility-preview"></a>Herramienta de migración de almacén de datos (vista preliminar)

> [AZURE.SELECTOR]
- [Descargue la herramienta de migración][]

La herramienta de migración del almacén de datos es una herramienta diseñada para migrar el esquema y los datos de SQL Server y base de datos de SQL Azure al almacén de datos de SQL Azure. Durante la migración de esquema, la herramienta asigna automáticamente el esquema de origen correspondiente al destino. Después de migrar el esquema, las herramientas proporciona la opción de mover los datos con scripts generados automáticamente.

Además de migración de esquema y los datos, esta herramienta proporciona la opción para generar informes de compatibilidad que resumen incompatibilidad entre las instancias de origen y de destino que podrían impedir que la migración simplificada.

## <a name="get-started"></a>Introducción
Como requisito previo para la instalación, necesitará la herramienta de línea de comandos bcp para ejecutar las secuencias de comandos de migración y Office para ver el informe de compatibilidad. Después de iniciar el archivo ejecutable que se descarga, se le pedirá que acepte a un CLUF estándar antes de que se instalará la herramienta.

Además, para ejecutar la Utiliy de migración, necesitará la siguiente permisos en la base de datos que desea para migrar: crear base de datos, modificar cualquier base de datos o cualquier definición de vista.

### <a name="launching-the-tool-and-connecting"></a>La herramienta y la conexión
Inicie la herramienta haciendo clic en el icono del escritorio que aparece después de la instalación. Al abrir la herramienta, se le pedirá una página de conexión inicial, donde puede elegir el origen y destino de la herramienta de migración. En este momento, se admiten SQL Server y la base de datos de SQL Azure como orígenes y almacenamiento de datos de SQL como destino. Después de seleccionar esta se solicitará que conectarse al servidor de origen rellenar en nombre del servidor y de autenticación y, a continuación, haciendo clic en 'Conectar'.

Después de autenticar, la herramienta mostrará una lista de bases de datos que se encuentran en el servidor que está conectado a. Puede comenzar la migración seleccionando una base de datos que desea migrar y, a continuación, haciendo clic en 'Migrar seleccionada'.

## <a name="migration-report"></a>Informe de migración
Seleccionar 'Comprobar la compatibilidad de base de datos' en la herramienta generará un informe de resumen incompatibilidad de todos los objetos de la base de datos solicitada para migrar. Puede encontrar una lista más amplia de algunas de las funciones de SQL Server no está presente en el almacén de datos de SQL en la [documentación de migración][]. Después de genera el informe podrá guardar y abrir el informe en Excel.

Tenga en cuenta al generar el esquema de migración, la mayoría de los problemas identificado como 'Objeto' se ajustará para permitir la migración inmediata de los datos. Revise los cambios para asegurarse de que no desea realizar ajustes adicionales antes de aplicar el esquema.

## <a name="migrate-schema"></a>Migrar esquema

Después de conectar, seleccionar el esquema de migrar generará un script de migración de esquema para las tablas seleccionadas. Puertos de esta secuencia de comandos la estructura de la tabla, mapas de datos incompatible tipos de formularios más compatibles y crea esquema y credenciales de seguridad si esto viene indicado por el usuario en la configuración de migración. Este código se puede ejecutar en la instancia de almacén de datos SQL de destino, guardado en un archivo, ha copiado en el Portapapeles o incluso editado en línea antes de realizar una acción adicional.  

Como se indicó anteriormente, cuando la migración de esquema de revisar la migración de los cambios que ha realizado la herramienta con el fin de asegurarse de que comprende todas ellas.  

## <a name="migrate-data"></a>Migrar datos

Haciendo clic en la opción 'Migrar datos' puede generar scripts BCP que mueve los datos primero a los archivos sin formato en su servidor y, a continuación, directamente en el almacén de datos de SQL. Se recomienda este proceso para mover pequeñas cantidades de datos y, como reintentos no están integrados y es posible que se produzcan errores si hay una pérdida de la conexión de red. Para ejecutar este ejemplo, debe tener instalada la utilidad de línea de comandos BCP y debe ya se han creado el esquema de los datos.

Una vez que se ha rellenado los parámetros anteriores necesita simplemente haga clic en Ejecutar migración y se generará un conjunto de dos paquetes a la ubicación especificada. Ejecute el archivo de exportación para exportar datos de su origen de migración a archivos sin formato y ejecute el archivo de importación para importar los datos en el almacén de datos de SQL.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha migrado algunos datos, consulte cómo [desarrollar][].

<!--Image references-->

<!--Article references-->
[documentación de migración]: sql-data-warehouse-overview-migrate.md
[desarrollar]: sql-data-warehouse-overview-develop.md

<!--Other Web references--> 
[Descargue la herramienta de migración]: https://migrhoststorage.blob.core.windows.net/sqldwsample/DataWarehouseMigrationUtility.zip
