<properties
   pageTitle="Usar el conector SQL en aplicaciones de lógica | Servicio de aplicaciones de Microsoft Azure"
   description="Cómo crear y configurar la aplicación conector SQL o API y usarla en una aplicación de la lógica de servicio de la aplicación de Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="09/01/2016"
   ms.author="sameerch"/>


# <a name="get-started-with-the-microsoft-sql-connector-and-add-it-to-your-logic-app"></a>Empezar a trabajar con el conector de SQL de Microsoft y agregar a su aplicación lógica
>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2014 12-01-vista previa de aplicaciones de lógica. La versión de esquema de Azure SQL 2015-08-01-vista previa, haga clic en [API de SQL Azure](../connectors/connectors-create-api-sqlazure.md).

Conectarse a la implementación local SQL Server o una base de datos de SQL Azure para crear y cambiar la información o los datos. Conectores pueden usarse en las aplicaciones de lógica para recuperar, proceso, o insertar datos como parte de un flujo de trabajo de"". Cuando se utiliza el conector de SQL en el flujo de trabajo, se puede conseguir una gran variedad de escenarios. Por ejemplo, puede:

- Exponer una sección de los datos que se encuentran en la base de datos SQL con un sitio web o aplicación móvil.
- Insertar datos en una tabla de base de datos SQL de almacenamiento. Por ejemplo, puede introducir los registros de empleado, actualizar pedidos de ventas y así sucesivamente.
- Obtener datos de SQL y usar en los procesos empresariales. Por ejemplo, puede obtener registros del cliente y poner los registros de clientes de SalesForce.

Puede agregar el conector de SQL a los datos de flujo de trabajo y proceso de negocio como parte de este flujo de trabajo dentro de una aplicación de lógica. 

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
*Desencadenadores* son eventos que se producen. Por ejemplo, se agrega cuando se actualiza un pedido o cuando un cliente nuevo. Una *acción* es el resultado del desencadenador. Por ejemplo, cuando se actualiza un pedido, enviar una alerta para el vendedor. O bien, cuando se agrega un cliente nuevo, enviar un correo electrónico de bienvenida para el nuevo cliente.

El conector de SQL se puede utilizar como un desencadenador o una acción en una lógica aplicación y es compatible con datos en formatos XML y JSON. Para cada tabla que se incluye en el paquete de configuración (más adelante en este tema), hay un conjunto de acciones de JSON y un conjunto de acciones de XML.

El conector de SQL tiene los siguientes desencadenadores y acciones disponibles:

Desencadenadores | Acciones
--- | ---
Datos de sondeo | <ul><li>Insertar en tabla</li><li>Actualizar tabla</li><li>Seleccione de tabla</li><li>Eliminar de la tabla</li><li>Llamar a un procedimiento almacenado</li></ul>

## <a name="create-the-sql-connector"></a>Crear el conector SQL

Un conector puede crear dentro de una aplicación de lógica o directamente desde el catálogo de soluciones de Azure. Para crear un conector desde el catálogo de soluciones:  

1. En la startboard Azure, seleccione **Marketplace**.
2. Buscar "Conector SQL", selecciónelo y seleccione **crear**.
3. Escriba el nombre, el Plan de aplicación de servicio y otras propiedades.
4. Escriba la configuración del paquete siguientes:

    Nombre | Obligatorio |  Descripción
--- | --- | ---
Nombre del servidor | Sí | Escriba el nombre de SQL Server. Por ejemplo, escriba *SQL Server/sqlexpress* o *SQLserver.mydomain.com*.
Puerto | No | El valor predeterminado es 1433.
Nombre de usuario | Sí | Escriba un nombre de usuario que puede iniciar sesión en SQL Server. Si va a conectarse a un servidor local SQL Server, escriba las credenciales de autenticación de SQL.
Contraseña | Sí | Escriba la contraseña de nombre de usuario.
Nombre de la base de datos | Sí | Escriba la base de datos que se está conectando. Por ejemplo, puede escribir *clientes* o *dbo o pedidos*.
Local | Sí | El valor predeterminado es False. Escriba falso si va a conectarse a una base de datos de SQL Azure. Escriba True si va a conectarse a un servidor de SQL en local.
Cadena de conexión de Bus de servicio | No | Si va a conectarse a la implementación local, escriba la cadena de conexión de retransmisión de Bus de servicio.<br/><br/>[Uso del Administrador de conexión híbrido](app-service-logic-hybrid-connection-manager.md)<br/>[Precios de Bus de servicio](https://azure.microsoft.com/pricing/details/service-bus/)
Nombre del servidor asociado | No | Si el servidor principal no está disponible, puede especificar un servidor asociado como un servidor de copia de seguridad o alternativo.
Tablas | No | Lista de las tablas de base de datos que se pueden actualizar el conector. Por ejemplo, escriba *OrdersTable* o *EmployeeTable*. Si no se introduce ninguna tabla, se pueden utilizar todas las tablas. Tablas válidas o procedimientos almacenados se requieren para usar este conector como una acción.
Procedimientos almacenados | No | Escriba un procedimiento almacenado existente que se puede llamar por el conector. Por ejemplo, escriba *sp_IsEmployeeEligible* o *sp_CalculateOrderDiscount*. Tablas válidas o procedimientos almacenados se requieren para usar este conector como una acción.
Consulta de datos disponibles | Asistencia de desencadenador | Instrucción SQL para determinar si cualquier dato está disponible para una tabla de base de datos de SQL Server de sondeo. Esto debe devolver un valor numérico que representa el número de filas de datos disponibles. Ejemplo: Seleccione COUNT(*) de table_name.
Consulta de datos de sondeo | Asistencia de desencadenador | La instrucción SQL para sondear la tabla de base de datos de SQL Server. Puede escribir cualquier número de instrucciones SQL separados por punto y coma. Esta declaración se ejecuta transacciones y confirmada solo cuando los datos se almacenan de forma segura en la aplicación de la lógica. Ejemplo: Seleccione *de table_name; ELIMINAR de table_name. <br/>**Note**<br/>Debe proporcionar una instrucción de sondeo que evita un bucle infinito al eliminar, mover o actualizar los datos seleccionados para asegurarse de que los mismos datos no sondean nuevamente.

5. Cuando haya terminado, la configuración del paquete un aspecto similar al siguiente:  
![][1]  

6. Seleccione **crear**. 


## <a name="use-the-connector-as-a-trigger"></a>Usar el conector como un desencadenador
Echemos un vistazo a una aplicación de lógica sencilla que explora datos de una tabla SQL, agrega los datos de otra tabla y a continuación, actualiza los datos.

Para usar el conector SQL como un desencadenador, escriba los valores de la **Consulta de datos disponibles** y **Consulta de datos de sondeo** . Se ejecuta la **Consulta de datos disponibles** en la programación que escriba y determina si los datos están disponibles. Dado que esta consulta solo devuelve un número escalar, puede ajustar y optimizado para la ejecución frecuentes.

**Consulta de datos de sondeo** solo se ejecuta cuando la consulta de datos disponibles indica que hay datos disponibles. Esta declaración se ejecuta dentro de una transacción y solo está confirmada cuando los datos extraídos duradera se almacenan en el flujo de trabajo. Es importante evitar indefinidamente volver a extraer los mismos datos. La naturaleza transacciones de esta ejecución de puede usarse para eliminar o actualizar los datos para asegurarse de que no se recopila la próxima vez que consultar datos.

> [AZURE.NOTE] El esquema devuelto por esta declaración identifica las propiedades disponibles en el conector. Deben tener el nombre de todas las columnas.

#### <a name="data-available-query-example"></a>Ejemplo de consulta disponible de datos

    SELECT COUNT(*) FROM [Order] WHERE OrderStatus = 'ProcessedForCollection'

#### <a name="poll-data-query-example"></a>Ejemplo de consulta de datos de sondeo

    SELECT *, GetData() as 'PollTime' FROM [Order]
        WHERE OrderStatus = 'ProcessedForCollection'
        ORDER BY Id DESC;
    UPDATE [Order] SET OrderStatus = 'ProcessedForFrontDesk'
        WHERE Id =
        (SELECT Id FROM [Order] WHERE OrderStatus = 'ProcessedForCollection' ORDER BY Id DESC)

### <a name="add-the-trigger"></a>Agregar el desencadenador
1. Al crear o editar una lógica de aplicación, seleccione el conector de SQL que creó como desencadenador. Enumera los desencadenadores disponibles: **Sondeo datos JSON ()** y **Datos de sondeo (XML)**:  
![][5]

2. Seleccione el desencadenador de **Sondeo datos JSON ()** , escriba la frecuencia y haga clic en la ✓:  
![][6]

3. El desencadenador aparece ahora como se ha configurado en la aplicación de la lógica. Las salidas del desencadenador se muestran y se pueden utilizar como entradas de las siguientes acciones:  
![][7]

## <a name="use-the-connector-as-an-action"></a>Usar el conector como una acción
Usar nuestro escenario de aplicación lógica sencilla que explora datos de una tabla SQL, agrega los datos de otra tabla y actualiza los datos.

Para usar el conector de SQL como una acción, escriba el nombre de las tablas y procedimientos almacenados que escribió cuando creó el conector de SQL:

1. El desencadenador After (o elija 'ejecutar esta lógica manualmente'), agregue el conector SQL que creó en la galería. Seleccione una de las acciones de inserción, como *Insertar en TempEmployeeDetails (JSON)*:  
![][8]

2. Escriba los valores de entrada del registro que se va a insertar y a continuación, haga clic en la ✓:  
![][9]

3. En la galería, seleccione el mismo conector SQL que creó. Como una acción, seleccione la acción de actualización en la misma tabla, como *EmployeeDetails de actualización*:  
![][11]

4. Escriba los valores de entrada para la acción Actualizar y haga clic en la ✓:  
![][12]

Puede probar la aplicación lógica agregando un nuevo registro en la tabla que se están consultando.

## <a name="what-you-can-and-cannot-do"></a>Qué puede o no puede hacer

Consulta SQL | Compatibles | No compatible
--- | --- | ---
Donde cláusula | <ul><li>Operadores: Y, o bien, = <>, <, < =, >, > = y como</li><li>Se pueden combinar varias condiciones de sub '(' y')'</li><li>Literales de cadena, Datetime (entre comillas simples), números (sólo debe contener caracteres numéricos)</li><li>Debe ser estrictamente en un formato binario de expresión, como ((operand operator operand) o (operando del operador operando)) *</li></ul> | <ul><li>Operadores: Entre, IN</li><li>Todas las funciones integradas, como ADD(), MAX() NOW(), POWER() y así sucesivamente</li><li>Operadores matemáticos como *, -, +, etc.</li><li>Concatenaciones de cadena usando +.</li><li>Todas las combinaciones</li><li>ES nulo y no es nulo</li><li>Los números con caracteres no es numérico, como números hexadecimal</li></ul>
Campos (en la consulta de selección) | <ul><li>Nombres de columna válida separados por comas. Prefijos de nombre de tabla no permiten (funciona de conector en una tabla en un momento).</li><li>Pueden ser de escape nombres con ' [' y ']'</li></ul> | <ul><li>Palabras clave como parte superior, DISTINCT etc.</li><li>Alias, como código postal, ciudad + dirección postal de AS</li><li>Todas las funciones integradas, como ADD(), MAX() NOW(), POWER() y así sucesivamente</li><li>Operadores matemáticos, como *, -, +, etc.</li><li>Concatenaciones de cadena usando +</li></ul>

#### <a name="tips"></a>Sugerencias

- Para las consultas avanzadas, le recomendamos crear un procedimiento almacenado y ejecutar mediante el procedimiento almacenado de execute API.
- Cuando el uso de consultas internas usarlas dentro de los procedimientos almacenados.
- Para unirse a varias condiciones, puede usar 'Y' y operadores 'O'.

## <a name="hybrid-configuration-optional"></a>Configuración híbrida (opcional)

> [AZURE.NOTE] Este paso es necesario únicamente si está utilizando SQL Server local detrás del firewall.

Aplicación de servicio usa el Administrador de configuración híbrida para conectarse a su sistema local de forma segura. Si es conector usa un servidor de SQL en local, el Administrador de conexión de híbrido es necesario.

> [AZURE.NOTE] Si desea empezar a trabajar con aplicaciones de Azure lógica antes de suscribirse para una cuenta de Azure, vaya a [Probar la aplicación de lógica](https://tryappservice.azure.com/?appservice=logic), donde puede crear inmediatamente una aplicación de la lógica de inicio de corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

Consulte [con el Administrador de conexión híbrido](app-service-logic-hybrid-connection-manager.md).


## <a name="do-more-with-your-connector"></a>Hacer más cosas con el conector
Una vez creado el conector, puede agregar un flujo de trabajo empresarial con una aplicación de lógica. Consulte [¿qué aplicaciones de lógica?](app-service-logic-what-are-logic-apps.md).

Ver la referencia de la API de REST Swagger en [conectores y API de aplicaciones de referencia](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede revisar la seguridad de las estadísticas y control de rendimiento para el conector. Consulte [administrar y supervisar la API de aplicaciones y los conectores integrados](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.png
[5]: ./media/app-service-logic-connector-sql/LogicApp1.png
[6]: ./media/app-service-logic-connector-sql/LogicApp2.png
[7]: ./media/app-service-logic-connector-sql/LogicApp3.png
[8]: ./media/app-service-logic-connector-sql/LogicApp4.png
[9]: ./media/app-service-logic-connector-sql/LogicApp5.png
[10]: ./media/app-service-logic-connector-sql/LogicApp6.png
[11]: ./media/app-service-logic-connector-sql/LogicApp7.png
[12]: ./media/app-service-logic-connector-sql/LogicApp8.png
