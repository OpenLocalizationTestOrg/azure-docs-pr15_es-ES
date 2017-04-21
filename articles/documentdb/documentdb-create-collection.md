<properties 
    pageTitle="Crear una base de datos de DocumentDB y colección | Microsoft Azure" 
    description="Obtenga información sobre cómo crear bases de datos NoSQL y colecciones de documento JSON con el portal de servicio en línea para DocumentDB de Azure, una nube según la base de datos del documento. Obtener una prueba gratuita hoy mismo." 
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="mimig"/>

# <a name="how-to-create-a-documentdb-collection-and-database-using-the-azure-portal"></a>Cómo crear una colección de DocumentDB y la base de datos con el portal de Azure

Para usar Microsoft Azure DocumentDB, debe tener una [cuenta de DocumentDB](documentdb-create-account.md), una base de datos, una colección y documentos. Este tema describe cómo crear una colección de DocumentDB en el portal de Azure. 

¿No tiene claro qué colección es? Consulte [¿Qué es una colección de DocumentDB?](#what-is-a-documentdb-collection)

1.  En el [portal de Azure](https://portal.azure.com/), en la Jumpbar, haga clic en **DocumentDB (NoSQL)**y, a continuación, en el módulo **DocumentDB (NoSQL)** , seleccione la cuenta en la que desea agregar una colección. Si no tiene cuentas que se muestran, deberá [crear una cuenta de DocumentDB](documentdb-create-account.md).

    ![Captura de pantalla resaltado las cuentas de DocumentDB en la Jumpbar, la cuenta en el módulo de cuentas de DocumentDB y la base de datos en el módulo de cuenta DocumentDB, en la lente de bases de datos](./media/documentdb-create-collection/docdb-database-creation-1-2.png)
    
    Si no está visible en la Jumpbar **DocumentDB (NoSQL)** , haga clic en **Más servicios** y, a continuación, haga clic en **DocumentDB (NoSQL)**. Si no tiene cuentas que se muestran, deberá [crear una cuenta de DocumentDB](documentdb-create-account.md).

2. En el módulo **DocumentDB cuenta** para la cuenta seleccionada, haga clic en **Agregar**.

    ![Captura de pantalla resaltado las cuentas de DocumentDB en la Jumpbar, la cuenta en el módulo de cuentas de DocumentDB y la base de datos en el módulo de cuenta DocumentDB, en la lente de bases de datos](./media/documentdb-create-collection/docdb-database-creation-3.png)

3. En el módulo de **Agregar colección** , en el cuadro **ID** , escriba el identificador de la nueva colección. Nombres de la colección debe estar comprendido entre 1 y 255 caracteres y no pueden contener `/ \ # ?` o un espacio al final. Cuando se valida el nombre, aparece una marca de verificación verde en el cuadro ID.

    ![Captura de pantalla resaltado el botón Agregar colección en el módulo de la base de datos, la configuración en el módulo de agregar colección y el creador de base de datos de botón Aceptar - Azure portal para DocumentDB - basada en la nube para bases de datos NoSQL JSON](./media/documentdb-create-collection/docdb-collection-creation-5-8.png)

4. De forma predeterminada, el **Nivel de precios** se establece en **estándar** para que puede personalizar el rendimiento y el almacenamiento de la colección. Para obtener más información sobre el nivel de precios, vea [niveles de rendimiento en DocumentDB](documentdb-performance-levels.md).  

5. Seleccione un **modo de particiones** para la colección de **Una sola partición** o **con particiones**. 

    Una **sola partición** tiene una capacidad de almacenamiento reservado de 10 GB y puede tener los niveles de rendimiento de unidades de 10.000 400 solicitud/segundo (RU/s). Uno RU se corresponde con el rendimiento de lectura de un documento de 1KB. Para obtener más información acerca de las unidades de la solicitud, vea [unidades de la solicitud](documentdb-request-units.md). 

    Una **colección de particiones** puede escalar para manejar una cantidad ilimitada de almacenamiento a través de varias particiones y pueden tener niveles de rendimiento, comenzando por 10,100 RU/s. En el portal, el almacenamiento mayor que puede reservar es 250 GB y el rendimiento de la mayoría que puede reservar es 250.000 RU/s. Para aumentar la cuota de alguno, abrir una solicitud como se describe en [solicitud aumenta las cuotas de cuenta DocumentDB](documentdb-increase-limits.md). Para obtener más información acerca de las colecciones divididas, vea [colecciones divididas y una única partición](documentdb-partition-data.md#single-partition-and-partitioned-collections).

    De forma predeterminada, el rendimiento de una nueva colección de una sola partición se establece en 1000 RU/s con una capacidad de almacenamiento de 10 GB. Para una colección de particiones, el rendimiento de la colección se establece en 10100 RU/s con una capacidad de almacenamiento de 250 GB. Puede cambiar el rendimiento y el almacenamiento de la colección después de crear la colección. 

6. Si va a crear una colección de particiones, seleccione la **Clave de partición** para la colección. Seleccionar la clave de partición correcta es importante para crear una colección de rendimiento. Para obtener más información sobre cómo seleccionar una clave de partición, vea [diseñar para particiones](documentdb-partition-data.md#designing-for-partitioning).

7. En el módulo de **base de datos** , crear una nueva base de datos o usar una existente. Nombres de base de datos debe estar comprendido entre 1 y 255 caracteres y no pueden contener `/ \ # ?` o un espacio al final. Para validar el nombre, haga clic fuera del cuadro de texto. Cuando se valida el nombre, aparece una marca de verificación verde en el cuadro.

8. Haga clic en **Aceptar** en la parte inferior de la pantalla para crear la nueva colección. 

9. La nueva colección aparece en la lente de **colecciones** en el módulo de **Introducción** .
 
    ![Captura de pantalla de la nueva colección en el creador de base de datos de base de datos placa - Azure portal para DocumentDB - basada en la nube para bases de datos NoSQL JSON](./media/documentdb-create-collection/docdb-collection-creation-9.png)

10. **Opcional:** Para modificar el rendimiento de la colección en el portal, haga clic en **escala** en el menú de recursos. 

    ![Captura de pantalla del menú de recursos con escala seleccionada](./media/documentdb-create-collection/docdb-collection-creation-scale.png)

## <a name="what-is-a-documentdb-collection"></a>¿Qué es una colección de DocumentDB? 

Una colección es un contenedor de documentos JSON y la lógica de la aplicación de JavaScript asociada. Una colección es una entidad facturable, donde se determina el [costo](documentdb-performance-levels.md) por el rendimiento de preparación de la colección. Colecciones pueden abarcar uno o más particiones o servidores y pueden escalar para manejar volúmenes prácticamente ilimitados de almacenamiento o el rendimiento.

Colecciones se dividen automáticamente en uno o más servidores físicos por DocumentDB. Cuando se crea una colección, puede especificar el rendimiento aprovisiona en términos de unidades de la solicitud por segundo y una propiedad de clave de partición. DocumentDB utilizará el valor de esta propiedad para distribuir documentos entre particiones y las solicitudes de ruta como consultas. El valor de clave de partición también actúa como el límite de transacción para procedimientos almacenados y desencadenadores. Cada colección tiene una cantidad reservada de rendimiento específica de esa colección, que no se comparte con otras colecciones de la misma cuenta. Por lo tanto, se puede escalar la aplicación tanto en términos de almacenamiento y el rendimiento. 

Colecciones no tienen el mismo como tablas de bases de datos relacionales. Colecciones imponen esquema, en realidad DocumentDB imponen los esquemas, es una base de datos libres de esquema. Por lo tanto, puede almacenar distintos tipos de documentos con esquemas diferentes en la misma colección. Puede utilizar colecciones para almacenar objetos de un único tipo de como lo haría con las tablas. El modelo mejor sólo depende de cómo aparecen los datos juntos en consultas y transacciones.

## <a name="other-ways-to-create-a-documentdb-collection"></a>Otras maneras de crear una colección de DocumentDB

Colecciones no es necesario que pueden crearse con el portal, también puede crear mediante el [SDK DocumentDB](documentdb-sdk-dotnet.md) y las API de REST. 

- Un ejemplo de código de C#, vea los [ejemplos de colección de C#](documentdb-dotnet-samples.md#collection-examples). 
- Un ejemplo de código Node.js, vea los [ejemplos de la colección de Node.js](documentdb-nodejs-samples.md#collection-examples).
- Un ejemplo de código Python, vea [ejemplos de la colección de Python](documentdb-python-samples.md#collection-examples).
- Un ejemplo de la API de REST, vea [crear una colección](https://msdn.microsoft.com/library/azure/mt489078.aspx).

## <a name="troubleshooting"></a>Solución de problemas

Si **Agregar colección** está deshabilitada en el portal de Azure, significa que su cuenta está deshabilitada, que normalmente se produce cuando se usan los de créditos de beneficios para el mes.   

## <a name="next-steps"></a>Pasos siguientes

Ahora que ya tiene una colección, el siguiente paso es agregar documentos o importar documentos a la colección. Cuando se trata de agregar documentos a una colección, tiene algunas opciones:

- Puede [Agregar documentos](documentdb-view-json-document-explorer.md) mediante el Explorador de documentos en el portal.
- Puede [importar documentos y datos](documentdb-import-data.md) mediante la herramienta de migración de datos DocumentDB, que le permite importar archivos JSON y CSV, así como los datos de SQL Server, MongoDB, almacenamiento de tablas de Azure y otras colecciones de DocumentDB. 
- O bien, puede agregar documentos mediante uno de los [SDK DocumentDB](documentdb-sdk-dotnet.md). DocumentDB tiene. NET, Java, Python, Node.js y SDK de la API de JavaScript. Para los ejemplos de código C# que muestra cómo trabajar con documentos mediante el SDK de .NET DocumentDB, vea los [ejemplos de documento C#](documentdb-dotnet-samples.md#document-examples). Para obtener ejemplos de código de Node.js que muestra cómo trabajar con documentos mediante el SDK de Node.js DocumentDB, vea los [ejemplos de documento Node.js](documentdb-nodejs-samples.md#document-examples).

Una vez documentos de una colección, puede usar [DocumentDB SQL](documentdb-sql-query.md) para [Ejecutar consultas](documentdb-sql-query.md#executing-queries) frente a los documentos mediante el [Explorador de consulta](documentdb-query-collections-query-explorer.md) en el portal, la [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx)o uno de los [SDK](documentdb-sdk-dotnet.md). 
