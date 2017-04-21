<properties
    pageTitle="Explorador de secuencias de comandos de DocumentDB, un editor de JavaScript | Microsoft Azure"
    description="Obtenga información sobre el Explorador de secuencias de comandos de DocumentDB, una herramienta de Azure Portal para administrar los artefactos de programación de servidor de DocumentDB incluidos procedimientos almacenados, desencadenadores y funciones definidas por el usuario."
    keywords="editor de JavaScript"
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

# <a name="create-and-run-stored-procedures-triggers-and-user-defined-functions-using-the-documentdb-script-explorer"></a>Crear y ejecutar procedimientos almacenados, desencadenadores y funciones definidas por el usuario mediante el Explorador de secuencias de comandos de DocumentDB

En este artículo se proporciona información general sobre el Explorador de secuencias de comandos de [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) , que es un editor de JavaScript en el portal de Azure que le permite ver y ejecutar artefactos programación del servidor de DocumentDB, incluidos los procedimientos almacenados, desencadenadores y funciones definidas por el usuario. Obtenga más información sobre la programación de servidor de DocumentDB en el artículo de [procedimientos almacenados, desencadenadores de base de datos y UDF](documentdb-programming.md) .

## <a name="launch-script-explorer"></a>Iniciar el Explorador de secuencias de comandos

1. En el portal de Azure en la Jumpbar, haga clic en **DocumentDB (NoSQL)**. Si **Las cuentas de DocumentDB** no está visible, haga clic en **Más servicios** y, a continuación, haga clic en **DocumentDB (NoSQL)**.

2. En el menú de recursos, haga clic en **Explorador de scripts**.

    ![Captura de pantalla del comando Explorador de scripts](./media/documentdb-view-scripts/scriptexplorercommand.png)
 
    Los cuadros de lista desplegable de **base de datos** y **colección** se ha rellenado previamente según el contexto en el que iniciar el Explorador de scripts.  Por ejemplo, si se inicia desde un módulo de base de datos, la base de datos actual se rellena previamente.  Si inicia desde un módulo de la colección, la colección actual se rellena previamente.

4.  Use los cuadros de lista desplegable de **base de datos** y **colección** para cambiar fácilmente la colección desde el que actualmente se ven las secuencias de comandos sin tener que cerrar y volver a iniciar el Explorador de secuencia de comandos.  

5. Explorador de secuencias de comandos también admite el filtrado el conjunto de secuencias de comandos cargado actualmente por su propiedad id.  Simplemente escriba en el cuadro Filtro y los resultados en la lista del explorador de scripts se filtran basándose en los criterios suministrados.

    ![Captura de pantalla del explorador de secuencia de comandos con los resultados filtrados](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)


    > [AZURE.IMPORTANT] El Explorador de scripts filtrar únicos filtros de la funcionalidad del conjunto ***actualmente*** cargado de secuencias de comandos y no se actualiza automáticamente la colección seleccionada actualmente.

5. Para actualizar la lista de secuencias de comandos cargada Script Explorer, simplemente haga clic en el comando **Actualizar** en la parte superior de la hoja.

    ![Comando de actualización de captura de pantalla del explorador de scripts](./media/documentdb-view-scripts/scriptexplorerrefresh.png)


## <a name="create-view-and-edit-stored-procedures-triggers-and-user-defined-functions"></a>Crear, ver y editar procedimientos almacenados, desencadenadores y funciones definidas por el usuario

Explorador de scripts permite fácilmente las operaciones de CRUD en artefactos de programación de servidor DocumentDB.  

- Para crear una secuencia de comandos, simplemente haga clic en la aplicable crear comando dentro del explorador de secuencias de comandos, proporcione un id, escriba el contenido de la secuencia de comandos y haga clic en **Guardar**.

    ![Captura de pantalla del explorador de secuencias de comandos crear la opción de mostrar el editor de JavaScript](./media/documentdb-view-scripts/scriptexplorercreatecommand.png)

- Al crear un desencadenador, también debe especificar la operación de tipo y desencadenar desencadenador

    ![Captura de pantalla del explorador de secuencias de comandos crear la opción de desencadenador](./media/documentdb-view-scripts/scriptexplorercreatetrigger.png)

- Para ver una secuencia de comandos, simplemente haga clic en la secuencia de comandos en el que está interesado.

    ![Experiencia de script de vista de captura de pantalla del explorador de scripts](./media/documentdb-view-scripts/scriptexplorerviewscript.png)

- Para editar una secuencia de comandos, realice los cambios deseados en el código JavaScript editor y haga clic en **Guardar**.

    ![Experiencia de script de vista de captura de pantalla del explorador de scripts](./media/documentdb-view-scripts/scriptexplorereditscript.png)

- Para descartar los cambios pendientes a una secuencia de comandos, simplemente haga clic en el comando **Descartar** .

    ![Captura de pantalla del explorador de scripts descartar cambios experiencia](./media/documentdb-view-scripts/scriptexplorerdiscardchanges.png)

- Explorador de secuencias de comandos también le permite ver fácilmente las propiedades del sistema de la secuencia de comandos cargada en ese momento haciendo clic en el comando **Propiedades** .

    ![Ver propiedades de secuencia de comandos de captura de pantalla del explorador de scripts](./media/documentdb-view-scripts/scriptproperties.png)

    > [AZURE.NOTE] La propiedad de marca de tiempo (_ts) se representan internamente como período de tiempo, pero Explorador de scripts muestra el valor en un formato legible GMT.

- Para eliminar una secuencia de comandos, selecciónela en el Explorador de scripts y haga clic en el comando **Eliminar** .

    ![Comando Eliminar de captura de pantalla del explorador de scripts](./media/documentdb-view-scripts/scriptexplorerdeletescript1.png)

- Haga clic en **Sí** para confirmar la eliminación o cancelar la acción eliminar haciendo clic en **No**.

    ![Comando Eliminar de captura de pantalla del explorador de scripts](./media/documentdb-view-scripts/scriptexplorerdeletescript2.png)

## <a name="execute-a-stored-procedure"></a>Ejecutar un procedimiento almacenado

> [AZURE.WARNING] Ejecutar procedimientos almacenados en el Explorador de secuencia de comandos no es todavía compatible para colecciones de particiones del lado servidor. Para obtener más información, visite [particiones y escala en DocumentDB](documentdb-partition-data.md).

Explorador de scripts permite ejecutar procedimientos almacenados de servidor desde el portal de Azure.

- Al abrir un nuevo módulo de procedimiento almacenado de crear, ya se proporcionará una secuencia de comandos predeterminada (*prefijo*). Para ejecutar la secuencia de comandos de *prefijo* o su propio, agregue un *identificador* y *entradas*. Para procedimientos almacenados que aceptan varios parámetros, todas las entradas deben estar dentro de una matriz (por ejemplo, *["foo", "barra"]*).

    ![Módulo de captura de pantalla de Script Explorer procedimientos almacenados para agregar la entrada y ejecutar un procedimiento almacenado](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-input.png)

- Para ejecutar un procedimiento almacenado, simplemente haga clic en el comando **Guardar y ejecutar** en el panel del editor de secuencias de comandos.

    > [AZURE.NOTE] El comando **Guardar y ejecutar** guarde el procedimiento almacenado antes de ejecutar, lo que significa sobrescriba la versión guardada anteriormente del procedimiento almacenado.

- Ejecuciones del procedimiento almacenado correcta tendrá un estado de *guardado correctamente y ejecutado el procedimiento almacenado* y los resultados devueltos se llenará en el panel de *resultados* .

    ![Módulo de captura de pantalla de Script Explorer procedimientos almacenados, para ejecutar un procedimiento almacenado](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure.png)

- Si la ejecución encuentra un error, el error se llenará en el panel de *resultados* .

    ![Ver propiedades de secuencia de comandos de captura de pantalla del explorador de scripts. Ejecutar un procedimiento almacenado con errores](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-error.png)

## <a name="work-with-scripts-outside-the-portal"></a>Trabajar con las secuencias de comandos fuera del portal

El Explorador de secuencias de comandos en el portal de Azure es una manera de trabajar con procedimientos almacenados, desencadenadores y funciones definidas por el usuario en DocumentDB. También puede trabajar con las secuencias de comandos con la API de REST y el [SDK de cliente](documentdb-sdk-dotnet.md). La documentación de la API de REST incluye ejemplos para trabajar con [procedimientos almacenados mediante el resto](https://msdn.microsoft.com/library/azure/mt489092.aspx), [con el resto de funciones definidas por el usuario](https://msdn.microsoft.com/library/azure/dn781481.aspx)y [desencadenadores con el resto](https://msdn.microsoft.com/library/azure/mt489116.aspx). Ejemplos también están disponible muestra cómo [trabajar con las secuencias de comandos mediante C#](documentdb-dotnet-samples.md#server-side-programming-examples) y [trabajar con secuencias de comandos mediante Node.js](documentdb-nodejs-samples.md#server-side-programming-examples).

## <a name="next-steps"></a>Pasos siguientes

Más información sobre la programación de servidor de DocumentDB en el artículo de [procedimientos almacenados, desencadenadores de base de datos y UDF](documentdb-programming.md) .

La [ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/documentdb/) también es un recurso útil para guiar a medida que obtiene más información sobre DocumentDB.  
