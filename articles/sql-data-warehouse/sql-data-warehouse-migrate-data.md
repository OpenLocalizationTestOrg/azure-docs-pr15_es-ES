<properties
   pageTitle="Migrar los datos al almacén de datos de SQL | Microsoft Azure"
   description="Sugerencias para migrar los datos al almacén de datos de SQL Azure para desarrollar soluciones."
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
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="migrate-your-data"></a>Migrar los datos
Datos se pueden mover en el almacén de datos de SQL con herramientas de una gran variedad de orígenes diferentes.  Copiar ADF, SSIS y bcp pueden utilizarse para conseguir este objetivo. Sin embargo, como la cantidad de datos aumenta debe pensar desglosar el proceso de migración de datos en pasos. Esto le ofrece la oportunidad de optimizar cada paso para obtener un rendimiento y de la resistencia a garantizar una migración de datos sin problemas.

En primer lugar, en este artículo se describe los escenarios de migración simple de copia de ADF, SSIS y bcp. A continuación, aspecto un poco más en cómo se puede optimizar la migración.

## <a name="azure-data-factory-adf-copy"></a>Azure copiar datos fábrica (ADF)
[Copiar ADF][] forma parte del [Generador de datos de Azure][]. Puede utilizar Copiar ADF para exportar los datos a los archivos sin formato que se encuentran en el almacenamiento local a archivos plana remotos que llevan a cabo en el almacenamiento de blobs de Windows Azure o directamente en el almacén de datos de SQL.

Si los datos se inicia en archivos sin formato, primero debe transferir a blobs de Windows Azure almacenamiento antes de iniciar una carga en el almacén de datos de SQL. Una vez que los datos se transfieren en el almacenamiento de blobs de Windows Azure puede volver a utilizar [Copia ADF][] insertar los datos en el almacén de datos de SQL.

PolyBase también proporciona una opción de alto rendimiento para cargar los datos. Sin embargo, eso significa con dos herramientas en lugar de uno. Si necesita el mejor rendimiento, utilice PolyBase. Si desea una experiencia única herramienta (y los datos no son masivos) ADF es su respuesta.

> [AZURE.NOTE] PolyBase requiere los archivos de datos en UTF-8. Es predeterminado de la copia de ADF codificación por lo que no hay nada para cambiar. Se trata de un aviso de no cambiar el comportamiento predeterminado de copia de ADF.

Sede sobre el siguiente artículo para algunos [ejemplos ADF][]de excelente.

## <a name="integration-services"></a>Servicios de integración ##
Integration Services (SSIS) es una herramienta eficaz y flexible extraer transformar y carga (ETL) que admite flujos de trabajo complejos, transformación de datos y varias opciones de carga de datos. Use SSIS simplemente transferir datos a Azure o como parte de una migración más amplia.

> [AZURE.NOTE] SSIS puede exportar a UTF-8 sin marca de orden de bytes en el archivo. Para configurar esto primero debe utilizar el componente de columna derivada para convertir los datos de caracteres en el flujo de datos para utilizar la página de código UTF-8 65001. Una vez que se han convertido las columnas, escriba los datos en el adaptador de destino de archivo plano garantizar que 65001 también se ha seleccionado la página de códigos para el archivo.

SSIS se conecta al almacén de datos de SQL como lo haría conectarse a una implementación de SQL Server. Sin embargo, las conexiones tendrá que utilizar un administrador de conexión de ADO.NET. También debe configurar la "usar inserción masiva cuando esté disponible" configuración para maximizar el rendimiento. Consulte el artículo de [adaptador de destino de ADO.NET][] para obtener más información sobre esta propiedad

> [AZURE.NOTE] No se admite la conexión con el almacén de datos de SQL Azure mediante OLEDB.

Además, es siempre la posibilidad de que un paquete puede producir un error debido a problemas de red o limitación. Diseño de paquetes de modo que se pueden reanudar en el punto de error, sin rehacer trabajo que completar antes del error.

Para obtener más información, consulte la [documentación de SSIS][].

## <a name="bcp"></a>bcp
bcp es una herramienta de línea de comandos que está diseñada para el archivo plano importar y exportar datos. Algunos transformación tenga lugar durante la exportación de datos. Para llevar a cabo transformaciones simples usar una consulta para seleccionar y transformar los datos. Una vez exportado, los archivos planos pueden cargar directamente en el destino de la base de datos de almacén de datos de SQL.

> [AZURE.NOTE] A menudo es una buena idea encapsulados las transformaciones que se usa durante la exportación de datos en una vista en el sistema de origen. Esto garantiza que se mantiene la lógica y el proceso se puede repetir.

Ventajas de bcp son:

- Simplificar. comandos de bcp son sencillos crear y ejecutar
- Proceso de carga de volver a iniciar. Una vez exportado la carga se pueden ejecutar cualquier número de veces.

Limitaciones de bcp son:

- bcp funciona con tabulados archivos sin formato. No funciona con archivos como xml o JSON
- bcp no admite la exportación a UTF-8. Esto puede impedir que usa PolyBase en bcp exportar datos
- Capacidades de transformación de datos están limitadas a la fase de exportar solo y son sencillas de naturaleza
- bcp no se ha adaptado a ser sólida al cargar datos a través de internet. Cualquier inestabilidad de red puede causar un error de carga.
- bcp se basa en el esquema están presentes en la base de datos de destino antes de la carga

Para obtener más información, vea [Usar bcp para cargar los datos en el almacén de datos de SQL][].

## <a name="optimizing-data-migration"></a>Optimización de migración de datos
Un proceso de migración de datos SQLDW se puede dividir eficazmente en tres pasos:

1. Exportación de datos de origen
2. Transferencia de datos de Azure
3. Cargar en la base de datos de destino SQLDW

Optimizar de forma individual cada paso para crear un proceso de migración robusta, vuelva a iniciar y fiable que aumente el rendimiento en cada paso.

## <a name="optimizing-data-load"></a>Optimización de carga de datos
Mirar en orden inverso por un momento; es la manera más rápida de cargar los datos a través de PolyBase. Optimizar para un proceso de carga PolyBase coloca los requisitos previos en los pasos anteriores para que es aconsejable comprender esto planificaciones. Son:

1. Codificación de archivos de datos
2. Formato de archivos de datos
3. Ubicación de archivos de datos

### <a name="encoding"></a>Codificación
PolyBase requiere archivos de datos sean codificada UTF-8. Esto significa que al exportar los datos debe ajustarse a este requisito. Si los datos solo contienen caracteres ASCII básicos (no ASCII extendidos), a continuación, se asignan directamente el estándar de UTF-8 y no tiene que preocuparse demasiado la codificación. Sin embargo, si los datos contienen caracteres especiales, como diéresis, acentos o símbolos o los datos es compatible con los idiomas no latinos tendrá que asegurarse de que los archivos de exportación correctamente con codificación UTF-8.

> [AZURE.NOTE] bcp no admite la exportación de datos a UTF-8. Por lo tanto, la mejor opción es utilizar Integration Services o copia ADF para la exportación de datos. Es necesario señalar que no se requiere la marca de orden de bytes UTF-8 (BOM) en el archivo de datos.

Los archivos codificados mediante UTF-16 tendrá que volver a escribirse ***anterior*** a la transferencia de datos.

### <a name="format-of-data-files"></a>Formato de archivos de datos
PolyBase exige un terminador de fila fijo de \n o nueva línea. Este estándar deben cumplir los archivos de datos. No hay restricciones de terminadores cadena o columna.

Debe definir todas las columnas en el archivo como parte de la tabla externa en PolyBase. Asegúrese de que todas las columnas exportadas son necesarias y que los tipos se ajustan a los estándares necesarios.

Consulte volver a la [migrar su esquema] artículo para obtener información detallada sobre los tipos de datos compatibles.

### <a name="location-of-data-files"></a>Ubicación de archivos de datos
Almacén de datos SQL usa PolyBase para cargar los datos desde el almacenamiento de blobs de Windows Azure exclusivamente. Por lo tanto, los datos deben han primero transferidos en el almacenamiento de blobs de Windows.

## <a name="optimizing-data-transfer"></a>Optimización de transferencia de datos
Una de las partes más lentas de migración de datos es la transferencia de los datos de Azure. No solo puede ancho de banda un problema, pero también confiabilidad de red puede dañar gravemente el progreso. Migrar datos de Azure de forma predeterminada es a través de internet para que estén razonablemente es muy probable que las posibilidades de errores de transferencia que se producen. Sin embargo, estos errores pueden requerir volver a enviar en su totalidad o en parte de datos.

Afortunadamente tiene varias opciones para mejorar la velocidad y la resistencia de este proceso:

### <a name="expressroute"></a>[ExpressRoute][]
Desee considerar usar [ExpressRoute][] para acelerar la transferencia. [ExpressRoute][] le ofrece una conexión privada establecida en Azure para que la conexión no volver a través de internet público. Esto no es un paso obligatorio. Sin embargo, para mejorar el rendimiento al insertar datos a Azure en local o ubicación de la instalación.

Las ventajas de usar [ExpressRoute][] son:

1. Mayor confiabilidad
2. Velocidad de red más rápida
3. Menor latencia de red
4. mayor seguridad de red

[ExpressRoute][] es útil para un número de escenarios; no solo la migración.

¿Está interesado? Para obtener más información y precios, visite la [documentación de ExpressRoute][].

### <a name="azure-import-and-export-service"></a>Azure importar y exportar servicio
La importación de Azure y el servicio de exportación es un proceso de transferencia de datos diseñado para grandes (GB ++) masiva (TB ++) las transferencias de datos en Azure. Implica escribir los datos en los discos y enviarlos a un centro de datos de Azure. El contenido del disco se cargará en Azure almacenamiento de Blobs en su nombre.

Una vista de alto nivel del proceso de exportación de importación es como sigue:

1. Configurar un contenedor de almacenamiento de blobs de Windows Azure para recibir los datos
2. Exportar los datos al almacenamiento local
2. Copiar los datos en unidades de disco duro 3,5 SATA II/III con [herramienta de importación o exportación de Azure]
3. Crear un trabajo de importación con Azure importar y exportar servicio proporcionar los archivos de diario generados por el [herramienta de importación o exportación de Azure]
4. Enviar los discos su centro de datos de Azure designado
5. Los datos se transfieren a su contenedor de almacenamiento de blobs de Windows Azure
6. Cargar los datos en SQLDW con PolyBase

### <a name="azcopy-utility"></a>Utilidad de [AZCopy][]
La utilidad de [AZCopy][] es una excelente herramienta para obtener los datos que se transfieren en Azure almacenamiento de Blobs. Está diseñado para las pequeñas (MB ++) para las transferencias de datos (GB ++) muy grande. [AZCopy] también se ha diseñado para proporcionar buen rendimiento y son resistente al transferir datos Azure y lo es una excelente opción para el paso de transferencia de datos. Una vez transferido puede cargar los datos mediante PolyBase en almacén de datos de SQL. También puede incorporar AZCopy los paquetes SSIS mediante una tarea "Ejecutar el proceso".

Para usar AZCopy primero debe descargar e instalar. Hay una [versión de producción][] y una [versión de vista previa][] .

Para cargar un archivo desde el sistema de archivos, necesitará un comando como la siguiente:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Podría ser un resumen de proceso de alto nivel:

1. Configurar un contenedor de almacenamiento de Azure blob para recibir los datos
2. Exportar los datos al almacenamiento local
3. AZCopy los datos en el contenedor de almacenamiento de blobs de Windows Azure
4. Cargar los datos en el almacén de datos de SQL con PolyBase

Completa de documentación disponible: [AZCopy][].

## <a name="optimizing-data-export"></a>Optimización de exportación de datos
Además de garantizar que la exportación cumple los requisitos dispuestos por PolyBase puede intentar optimizar la exportación de los datos para mejorar aún más el proceso.

> [AZURE.NOTE] Como PolyBase requiere que los datos estén en formato UTF-8 es poco probable que usa bcp para realizar la exportación de datos. bcp no admite archivos de datos de salida a UTF-8. SSIS o copia ADF son mucho más adecuado para efectuar este tipo de exportación de datos.

### <a name="data-compression"></a>Compresión de datos
PolyBase puede leer datos gzip comprimido. Si puede comprimir los datos y archivos gzip se minimizará la cantidad de datos que se inserta en la red.

### <a name="multiple-files"></a>Varios archivos
No solo dividir las tablas grandes en varios archivos ayuda a mejorar la velocidad de exportación, también ayuda a re-startability de transferencia y la capacidad de administración general de los datos una vez en el almacenamiento de blobs de Windows Azure. Una de las muchas características armonía de PolyBase es que leerá todos los archivos dentro de una carpeta y tratar como una tabla. Por lo tanto, es una buena idea a aislar los archivos para cada tabla en su propia carpeta.

PolyBase también es compatible con una característica conocida como "desplazamiento de carpeta recursivo". Puede usar esta característica para mejorar la organización de los datos exportados para mejorar la administración de datos.

Para obtener más información sobre cómo cargar datos con PolyBase, vea [Usar PolyBase para cargar los datos en el almacén de datos de SQL][].


## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre la migración, consulte [migrar la solución al almacén de datos de SQL][].
Para obtener más sugerencias de desarrollo, vea [Introducción al desarrollo][].

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/storage-use-azcopy.md
[Copiar ADF]: ../data-factory/data-factory-data-movement-activities.md 
[Ejemplos ADF]: ../data-factory/data-factory-samples.md
[ADF Copy examples]: ../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md
[Introducción al desarrollo]: sql-data-warehouse-overview-develop.md
[Migrar la solución al almacén de datos de SQL]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Usar bcp para cargar los datos en el almacén de datos SQL]: sql-data-warehouse-load-with-bcp.md
[Usar PolyBase para cargar los datos en el almacén de datos SQL]: sql-data-warehouse-get-started-load-with-polybase.md


<!--MSDN references-->

<!--Other Web references-->
[Generador de datos de Azure]: http://azure.microsoft.com/services/data-factory/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[ExpressRoute documentación]: http://azure.microsoft.com/documentation/services/expressroute/

[versión de producción]: http://aka.ms/downloadazcopy/
[versión preliminar]: http://aka.ms/downloadazcopypr/
[Adaptador de destino de ADO.NET]: https://msdn.microsoft.com/library/bb934041.aspx
[Documentación de SSIS]: https://msdn.microsoft.com/library/ms141026.aspx
