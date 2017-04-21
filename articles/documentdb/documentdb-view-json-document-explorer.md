<properties
    pageTitle="DocumentDB Document Explorer, para ver JSON | Microsoft Azure"
    description="Obtenga información sobre el Explorador de documentos de DocumentDB, una herramienta de Portal de Azure JSON de ver, editar, crear y cargar documentos JSON con DocumentDB, una base de datos de documento NoSQL."
        keywords="Ver json"
    services="documentdb"
    authors="kirillg"
    manager="jhubbard"
    editor="monicar"
    documentationCenter=""/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="kirillg"/>

# <a name="view-edit-create-and-upload-json-documents-using-documentdb-document-explorer"></a>Ver, editar, crear y cargar documentos JSON con el Explorador de documento DocumentDB

En este artículo se proporciona información general sobre el Explorador de documentos de [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) , una herramienta de portal Azure que le permite ver, editar, crear, cargar y filtre JSON documentos con DocumentDB. 

Tenga en cuenta que el Explorador de documento no está habilitado en cuentas de DocumentDB con compatibilidad con el protocolo de MongoDB. Esta página se actualizará cuando esta característica está habilitada.

## <a name="launch-document-explorer"></a>Iniciar el Explorador de documento

1. En el portal de Azure en la Jumpbar, haga clic en **DocumentDB (NoSQL)**. Si **DocumentDB (NoSQL)** no está visible, haga clic en **Más servicios** y, a continuación, haga clic en **DocumentDB (NoSQL)**.

2. Seleccione el nombre de cuenta. 

3. En el menú de recursos, haga clic en **Explorador de documentos**. 
 
    ![Captura de pantalla del comando Document Explorer](./media/documentdb-view-JSON-document-explorer/documentexplorercommand.png)

    En el módulo del **Explorador de documentos** , las listas desplegables de **bases de datos** y **colecciones** se rellenan según el contexto en que se inició Document Explorer. 

## <a name="create-a-document"></a>Crear un documento

1. [Iniciar el Explorador de documento](#launch-document-explorer).

2. En el módulo del **Explorador de documentos** , haga clic en **Crear documento**. 

    En el módulo de **documento** se proporciona un fragmento JSON mínimo.

    ![Captura de pantalla del explorador de documento crear experiencia de documento, donde puede ver JSON y editar JSON](./media/documentdb-view-JSON-document-explorer/createdocument.png)

2. En el módulo de **documento** , escriba o pegue el contenido del documento JSON que desea crear y, a continuación, haga clic en **Guardar** para guardar el documento a la base de datos y la colección especificada en el módulo del **Explorador de documentos** .

    ![Captura de pantalla del explorador de documento comando Guardar](./media/documentdb-view-JSON-document-explorer/savedocument1.png)

    > [AZURE.NOTE] Si no se proporciona una propiedad "id", Document Explorer automáticamente agrega una propiedad de identificador y genera un GUID como el valor de id.

    Si ya tiene archivos de datos de JSON, MongoDB, SQL Server, archivos CSV, almacenamiento de tablas de Azure, Amazon DynamoDB, HBase, o en otras colecciones de DocumentDB, puede usar de DocumentDB [herramienta de migración de datos](documentdb-import-data.md) para importar rápidamente los datos.

## <a name="edit-a-document"></a>Editar un documento

1. [Iniciar el Explorador de documento](#launch-document-explorer).

2. Para editar un documento existente, selecciónela en el módulo del **Explorador de documentos** , edite el documento en el módulo de **documento** y, a continuación, haga clic en **Guardar**.

    ![Captura de pantalla del explorador de documento editar funcionalidad de documento usada para ver JSON](./media/documentdb-view-JSON-document-explorer/editdocument.png)

    Si está editando un documento y decide desea descartar el conjunto actual de cambios, simplemente haga clic en **Descartar** en el módulo de **documento** , confirme la acción Descartar y se vuelva a cargar el estado anterior del documento.

    ![Comando descartar de captura de pantalla del explorador de documento](./media/documentdb-view-JSON-document-explorer/discardedit.png)

## <a name="delete-a-document"></a>Eliminar un documento

1. [Iniciar el Explorador de documento](#launch-document-explorer).

2. Seleccione el documento en el **Explorador de documento**, haga clic en **Eliminar**y, a continuación, confirme la eliminación. Después de confirmar, el documento se quitará inmediatamente la lista del explorador de documentos.

    ![Comando Eliminar de captura de pantalla del explorador de documento](./media/documentdb-view-JSON-document-explorer/deletedocument.png)

## <a name="work-with-json-documents"></a>Trabajar con documentos JSON

Document Explorer valida que cualquier documento nuevo o editado contiene JSON válido.  Puede incluso ver errores de JSON pasando por encima de la sección incorrecta para obtener detalles sobre el error de validación.

![Captura de pantalla del explorador de documento con resaltado de JSON no válido](./media/documentdb-view-JSON-document-explorer/invalidjson1.png)

Además, Document Explorer le impide guardar un documento con contenido JSON no válido.

![Captura de pantalla del explorador de documento con JSON no válido Error al guardar](./media/documentdb-view-JSON-document-explorer/invalidjson2.png)

Por último, Document Explorer le permite ver fácilmente las propiedades del documento cargado actualmente haciendo clic en el comando **Propiedades** .

![Ver propiedades del documento de captura de pantalla del explorador de documento](./media/documentdb-view-JSON-document-explorer/documentproperties.png)

> [AZURE.NOTE] La propiedad de marca de tiempo (_ts) se representan internamente como período de tiempo, pero Document Explorer muestra el valor en un formato legible GMT.

## <a name="filter-documents"></a>Documentos de filtro
Explorador de documento es compatible con un número de opciones de navegación y configuración avanzada.

De forma predeterminada, Document Explorer carga a los primeros 100 documentos de la colección seleccionada por su fecha de creación de más antigua a más reciente.  Puede cargar documentos adicionales (en lotes de 100) seleccionando la opción **cargar más** en la parte inferior del módulo de explorador de documentos. Puede elegir qué documentos cargar mediante el comando **filtro** .

1. [Iniciar el Explorador de documento](#launch-document-explorer).

2. En la parte superior del módulo **Document Explorer** , haga clic en **filtro**.  

    ![Captura de pantalla de configuración de filtro de documento Explorer](./media/documentdb-view-JSON-document-explorer/documentexplorerfiltersettings.png)
  
3.  La configuración de filtro aparecerá debajo de la barra de comandos. En la configuración de filtro, proporcionar una cláusula WHERE o una cláusula ORDER BY y, a continuación, haga clic en **filtro**.

    ![Módulo de captura de pantalla de configuración de explorador de documentos](./media/documentdb-view-JSON-document-explorer/documentexplorerfiltersettings2.png)

    Document Explorer actualiza automáticamente los resultados con documentos que coincidan con la consulta de filtro. Obtenga más información sobre la gramática de SQL DocumentDB en la [consulta SQL y sintaxis SQL](documentdb-sql-query.md) artículo o impresión una copia de la [hoja de referencia sobre consulta SQL](documentdb-sql-query-cheat-sheet.md).

    Los cuadros de lista desplegable de **base de datos** y **colección** pueden usarse para cambiar fácilmente la colección desde el que actualmente se ven documentos sin tener que cerrar y volver a iniciar el Explorador de documento.  

    Document Explorer también es compatible con su propiedad id para filtrar el conjunto de documentos cargado actualmente.  Solo tiene que escribir en el filtro de documentos en el cuadro id.

    ![Captura de pantalla del explorador de documento con filtro de resaltado](./media/documentdb-view-JSON-document-explorer/documentexplorerfilter.png)

    Los resultados en el Explorador de documentos se filtran lista basan en los criterios suministrados.

    ![Captura de pantalla del explorador de documento con los resultados filtrados](./media/documentdb-view-JSON-document-explorer/documentexplorerfilterresults.png)

    > [AZURE.IMPORTANT] El Explorador de documentos filtro funcionalidad únicos filtros desde la ***actualmente*** cargado el conjunto de documentos y no se realiza una consulta en la colección seleccionada actualmente.

4. Para actualizar la lista de documentos cargados mediante el Explorador de documentos, haga clic en **Actualizar** en la parte superior de la hoja.

    ![Comando de actualización de captura de pantalla del explorador de documento](./media/documentdb-view-JSON-document-explorer/documentexplorerrefresh.png)

## <a name="bulk-add-documents"></a>Agregar en masa documentos

Explorador de documento es compatible con la recopilación de masa de uno o más JSON documentos existentes, hasta 100 archivos JSON por operación de carga.  

1. [Iniciar el Explorador de documento](#launch-document-explorer).

2. Para iniciar el proceso de carga, haga clic en **Cargar documento**.

    ![Funcionalidad de recopilación de masa de captura de pantalla del explorador de documento](./media/documentdb-view-JSON-document-explorer/uploaddocument1.png)

    Se abre el módulo de **Cargar documento** . 

2. Haga clic en el botón Examinar para abrir una ventana del explorador de archivos, seleccione uno o varios documentos JSON para cargar y, a continuación, haga clic en **Abrir**.

    ![Proceso de recopilación de masa de captura de pantalla del explorador de documento](./media/documentdb-view-JSON-document-explorer/uploaddocument2.png)

    > [AZURE.NOTE] Explorador de documentos actualmente admite hasta 100 JSON documentos por operación de carga individuales.

3. Cuando esté satisfecho con la selección, haga clic en el botón **cargar** .  Los documentos se agregan automáticamente a la cuadrícula del documento Explorer y se muestran los resultados de la carga progresa la operación. Se informaron de errores de importación para archivos individuales.

    ![Resultados de recopilación de masa de captura de pantalla del explorador de documento](./media/documentdb-view-JSON-document-explorer/uploaddocument3.png)

4. Una vez completada la operación, puede seleccionar hasta 100 otra documentos para cargar.

## <a name="work-with-json-documents-outside-the-portal"></a>Trabajar con documentos JSON fuera del portal

El Explorador de documentos en el portal de Azure es una manera de trabajar con documentos en DocumentDB. También puede trabajar con documentos de la [API de REST](https://msdn.microsoft.com/library/azure/mt489082.aspx) o el [SDK de cliente](documentdb-sdk-dotnet.md). Por ejemplo, código, vea los [ejemplos de documentos de .NET SDK](documentdb-dotnet-samples.md#document-examples) y los [ejemplos de SDK Node.js documento](documentdb-nodejs-samples.md#document-examples).

Si necesita importar o migrar archivos desde otro origen (JSON archivos, MongoDB, SQL Server, CSV archivos, tabla de Azure almacenamiento, Amazon DynamoDB o HBase), puede usar la [herramienta de migración de datos](documentdb-import-data.md) de DocumentDB a importar rápidamente los datos a DocumentDB.

## <a name="troubleshoot"></a>Solución de problemas

**Síntoma**: Document Explorer no devuelve **se encontraron documentos**.

**Solución**: asegúrese de que ha seleccionado la suscripción correcta, la base de datos y la colección en que se insertaron los documentos. Además, compruebe que funcionan en las cuotas de rendimiento. Si está trabajando en el rendimiento máximo nivel y obtener acelerado, reduzca el uso de la aplicación para funcionar con la cuota de rendimiento máximo para la colección.

**Explicación**: el portal es una aplicación como cualquier otra, realizar llamadas a su base de datos de DocumentDB y colección. Si las solicitudes actualmente se está limitando debido a que se realizan desde una aplicación independiente de llamadas, el portal puede también reducirá, causando recursos que no aparezca en el portal. Para resolver el problema, resolver la causa del uso de alto rendimiento y, a continuación, actualice el módulo portal. Obtener información sobre cómo medir y el uso de rendimiento inferior puede encontrarse en la sección [rendimiento](documentdb-performance-tips.md#throughput) del artículo [sugerencias de rendimiento](documentdb-performance-tips.md) .

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la gramática de SQL DocumentDB compatible con el Explorador de documentos, vea el artículo de la [consulta SQL y sintaxis SQL](documentdb-sql-query.md) o imprimir la [hoja de referencia sobre consulta SQL](documentdb-sql-query-cheat-sheet.md).

La [ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/documentdb/) también es un recurso útil para guiar a medida que obtiene más información sobre DocumentDB. 
