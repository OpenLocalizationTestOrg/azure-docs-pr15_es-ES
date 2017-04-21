<properties
    pageTitle="Agregar el conector de base de datos de SQL Azure en las aplicaciones de lógica | Microsoft Azure"
    description="Información general de conector de base de datos de SQL Azure con los parámetros de la API de REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="anneta"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="10/18/2016"
   ms.author="mandia"/>


# <a name="get-started-with-the-azure-sql-database-connector"></a>Empezar a trabajar con el conector de base de datos de SQL Azure
Usa el conector de base de datos de SQL Azure, crear flujos de trabajo para su organización que administran los datos de las tablas. 

Con la base de datos de SQL,:

- Crear un flujo de trabajo agregando a un nuevo cliente a una base de datos de clientes, o actualizar un pedido en una base de datos de pedidos.
- Usar acciones para obtener una fila de datos, insertar una fila nueva y eliminar. Por ejemplo, cuando se crea un registro de Dynamics CRM Online (un desencadenador), insertar una fila en una base de datos de SQL Azure (una acción). 

Este tema muestra cómo usar el conector de base de datos SQL en una aplicación de lógica y también enumera las acciones.

>[AZURE.NOTE] Esta versión de este artículo se aplica a la disponibilidad de aplicaciones de lógica general (GA). 

Para obtener más información acerca de la lógica de aplicaciones, vea [¿qué aplicaciones de lógica](../app-service-logic/app-service-logic-what-are-logic-apps.md) y [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-azure-sql-database"></a>Conectarse a la base de datos SQL Azure

Antes de la aplicación de lógica puede tener acceso a cualquier servicio, primero crear una *conexión* al servicio. Una conexión proporciona conectividad entre una aplicación de lógica y otro servicio. Por ejemplo, para conectarse a la base de datos de SQL, primero crear una *conexión*de base de datos de SQL. Para crear una conexión, escriba las credenciales a que normalmente se utilizan para tener acceso al servicio que se está conectando. Por lo tanto, en la base de datos de SQL, escriba sus credenciales de base de datos SQL para crear la conexión. 

#### <a name="create-the-connection"></a>Crear la conexión

>[AZURE.INCLUDE [Create the connection to SQL Azure](../../includes/connectors-create-api-sqlazure.md)]

## <a name="use-a-trigger"></a>Use un desencadenador

Este conector tener desencadenadores. Usar otros desencadenadores para iniciar la aplicación de la lógica, como un desencadenador de periodicidad, un desencadenador HTTP Webhook, desencadenadores disponibles con otros conectores y mucho más. [Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) proporciona un ejemplo.

## <a name="use-an-action"></a>Usar una acción
    
Una acción es una operación que lleva a cabo por el flujo de trabajo que se definen en una aplicación de lógica. [Más información acerca de las acciones](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Seleccione el signo más. Ver varias opciones: **Agregar una acción**, **Agregar una condición**o una de las opciones **más** .

    ![](./media/connectors-create-api-sqlazure/add-action.png)

2. Elija **Agregar una acción**.

3. En el cuadro de texto, escriba "sql" para obtener una lista de todas las acciones disponibles.

    ![](./media/connectors-create-api-sqlazure/sql-1.png) 

4. En nuestro ejemplo, seleccione **SQL Server - obtener la fila**. Si ya existe una conexión, seleccione el **nombre de la tabla** de la lista desplegable y escriba el **Identificador de fila** para devolver.

    ![](./media/connectors-create-api-sqlazure/sample-table.png)

    Si se le pide la información de conexión, a continuación, escriba los detalles para crear la conexión. [Crear la conexión](connectors-create-api-sqlazure.md#create-the-connection) en este tema se describen las propiedades. 

    > [AZURE.NOTE] En este ejemplo, se devuelve una fila de una tabla. Para ver los datos de fila, agregar otra acción que crea un archivo con los campos de la tabla. Por ejemplo, agregue una acción de OneDrive que usa los campos nombre y apellidos para crear un nuevo archivo en la cuenta de almacenamiento de la nube. 

5. **Guardar** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación de la lógica se guarda y se puede activar automáticamente.


## <a name="technical-details"></a>Detalles técnicos

## <a name="sql-database-actions"></a>Acciones de la base de datos SQL
Una acción es una operación que lleva a cabo por el flujo de trabajo que se definen en una aplicación de lógica. El conector de base de datos SQL incluye las siguientes acciones. 

|Acción|Descripción|
|--- | ---|
|[ExecuteProcedure](connectors-create-api-sqlazure.md#execute-stored-procedure)|Ejecuta un procedimiento almacenado en SQL|
|[GetRow](connectors-create-api-sqlazure.md#get-row)|Recupera una sola fila de una tabla SQL|
|[GetRows](connectors-create-api-sqlazure.md#get-rows)|Recupera filas de una tabla SQL|
|[InsertRow](connectors-create-api-sqlazure.md#insert-row)|Inserta una nueva fila en una tabla SQL|
|[Borracelda](connectors-create-api-sqlazure.md#delete-row)|Elimina una fila de una tabla SQL|
|[GetTables](connectors-create-api-sqlazure.md#get-tables)|Recupera tablas desde una base de datos SQL|
|[UpdateRow](connectors-create-api-sqlazure.md#update-row)|Actualiza una fila existente en una tabla SQL|

### <a name="action-details"></a>Detalles de acción

En esta sección, vea los detalles específicos acerca de cada acción, incluidas las propiedades de entrada opcionales o requeridas y ningún resultado correspondiente asociado con el conector.


#### <a name="execute-stored-procedure"></a>Ejecutar procedimiento almacenado
Ejecuta un procedimiento almacenado en SQL.  

| Nombre de propiedad| Nombre para mostrar |Descripción|
| ---|---|---|
|procedimiento * | Nombre de procedimiento | El nombre del procedimiento almacenado que desea ejecutar |
|parámetros * | Parámetros de entrada | Los parámetros son dinámicas y se basa en el procedimiento almacenado que elija. <br/><br/> Por ejemplo, si está usando la base de datos de ejemplo Adventure Works, elija el procedimiento almacenado de *ufnGetCustomerInformation* . Se muestra el parámetro de entrada de **ID de cliente** . Escriba "6" o uno de los otro ID de cliente. |

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
ProcedureResult: Lleva el resultado de la ejecución del procedimiento almacenado

| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|OutputParameters|objeto|Valores de parámetro de salida |
|ReturnCode|entero|Código de retorno de un procedimiento |
|Conjuntos de resultados|objeto| Conjuntos de resultados|


#### <a name="get-row"></a>Obtener la fila 
Recupera una sola fila de una tabla SQL.  

| Nombre de propiedad| Nombre para mostrar |Descripción|
| ---|---|---|
|tabla * | Nombre de tabla |Nombre de tabla SQL|
|Id * | Identificador de fila |Identificador único de la fila para recuperar|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Elemento

| Nombre de propiedad | Tipo de datos |
|---|---|
|ItemInternalId|cadena|


#### <a name="get-rows"></a>Obtener filas 
Recupera filas de una tabla SQL.  

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Nombre de tabla|Nombre de tabla SQL|
|$skip|Número de saltos|Número de entradas para omitir (predeterminado = 0)|
|$top|Recuento de obtener máximo|Número máximo de entradas para recuperar (predeterminado = 256)|
|$filter|Consulta de filtro|Una consulta de filtro ODATA para restringir el número de entradas|
|$orderby|Ordenar por|Una consulta ODATA orderBy para especificar el orden de las entradas.|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Elementos

| Nombre de propiedad | Tipo de datos |
|---|---|
|valor|matriz|


#### <a name="insert-row"></a>Insertar fila 
Inserta una nueva fila en una tabla SQL.  

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Nombre de tabla|Nombre de tabla SQL|
|elemento *|Fila|Fila para insertar en la tabla especificada en SQL|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Elemento

| Nombre de propiedad | Tipo de datos |
|---|---|
|ItemInternalId|cadena|


#### <a name="delete-row"></a>Eliminar fila 
Elimina una fila de una tabla SQL.  

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Nombre de tabla|Nombre de tabla SQL|
|Id *|Identificador de fila|Identificador único de la fila para eliminar|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="get-tables"></a>Obtener tablas 
Recupera tablas desde una base de datos SQL.  

No hay parámetros para esta llamada. 

##### <a name="output-details"></a>Detalles de salida 
TablesList

| Nombre de propiedad | Tipo de datos |
|---|---|
|valor|matriz|

#### <a name="update-row"></a>Actualizar una fila 
Actualiza una fila existente en una tabla SQL.  

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Nombre de tabla|Nombre de tabla SQL|
|Id *|Identificador de fila|Identificador único de la fila Actualizar|
|elemento *|Fila|Fila con valores actualizados|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida  
Elemento

| Nombre de propiedad | Tipo de datos |
|---|---|
|ItemInternalId|cadena|


### <a name="http-responses"></a>Respuestas HTTP

Cuando llame a las distintas acciones, obtendrá determinadas respuestas. La siguiente tabla describe las respuestas y sus descripciones:  

|Nombre|Descripción|
|---|---|
|200|Vale|
|202|Aceptado|
|400|Solicitud incorrecta|
|401|No autorizado|
|403|Prohibido|
|404|No se encontró|
|500|Error de servidor interno. Error desconocido|
|predeterminado|Error en la operación.|


## <a name="next-steps"></a>Pasos siguientes

[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Explore los otros conectores disponibles en las aplicaciones de lógica en nuestra [lista de API](apis-list.md).
