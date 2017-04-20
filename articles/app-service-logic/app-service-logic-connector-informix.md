<properties
   pageTitle="Mediante el conector de Informix en la aplicación de servicio de Microsoft Azure | Microsoft Azure"
   description="Cómo usar el conector de Informix con desencadenadores de aplicación lógica y acciones"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="gplarsen"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="plarsen"/>

# <a name="informix-connector"></a>Conector de Informix
>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2014 12-01-vista previa de aplicaciones de lógica.

Conector de Microsoft para Informix es una aplicación de API para conectar aplicaciones a través del servicio de aplicación de Azure a los recursos almacenados en una base de datos de IBM Informix. Conector incluye un cliente de Microsoft para conectarse a equipos remotos de servidor de Informix a través de una conexión de red TCP/IP, incluidas las conexiones de Azure híbrido a los servidores de Informix local mediante la retransmisión de Bus de servicio de Azure. Conector admite las siguientes operaciones de base de datos:

- Seleccione las filas de lectura mediante
- Sondeo leer las filas con SELECT COUNT seguido seleccione
- Agregar una fila o mediante Insertar varias filas de (masa)
- Modificar una fila o varias filas de (masa) con la actualización
- Quitar una fila o varias filas de (masa) mediante la opción Eliminar
- Lectura para modificar filas con el CURSOR seleccione seguido de actualización donde actual del CURSOR
- Lectura para quitar las filas con el CURSOR seleccione seguido de actualización donde actual del CURSOR
- Ejecutar procedimiento con parámetros de entrada y salidas, devolver el valor de conjunto de resultados, mediante una llamada a
- Comandos personalizados y operaciones compuestas mediante seleccionar, insertar, actualizar, eliminar

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
Conector es compatible con los siguientes desencadenadores de aplicación lógica y las acciones:

Desencadenadores | Acciones
--- | ---
<ul><li>Datos de sondeo</li></ul> | <ul><li>Inserción masiva</li><li>Insertar</li><li>Actualización masiva</li><li>Actualización</li><li>Llamada</li><li>Eliminar de forma masiva</li><li>Eliminar</li><li>Seleccione</li><li>Actualización condicional</li><li>Entrada de EntitySet</li><li>Eliminar condicional</li><li>Seleccione la entidad</li><li>Eliminar</li><li>Upsert al EntitySet</li><li>Comandos personalizados</li><li>Operaciones de composición</li></ul>


## <a name="create-the-informix-connector"></a>Crear el conector de Informix
Puede definir un conector dentro de una aplicación de lógica o de Azure Marketplace, como en el ejemplo siguiente:  

1. En la startboard Azure, seleccione **Marketplace**.
2. En el módulo **todo** , escriba **informix** en el cuadro **Buscar todo** y, a continuación, haga clic en la tecla ENTRAR.
3. En la búsqueda todo panel de resultados, seleccione el **conector de Informix**.
4. En el módulo de descripción del conector de Informix, seleccione **crear**.
5. En el módulo de paquete de conector de Informix, escriba el nombre (por ejemplo, "InformixConnectorNewOrders"), el Plan de aplicación de servicio y otras propiedades.
6. Seleccione **Configuración del paquete**y escriba la siguiente configuración del paquete.

    Nombre | Obligatorio |  Descripción
--- | --- | ---
Cadena de conexión | Sí | Cadena de conexión de cliente de Informix (por ejemplo, "dirección de red = nombre_de_servidor; Puerto de red = 9089; Identificador de usuario = username; Contraseña = contraseña; catálogo inicial = Neptuno; esquema predeterminado = informix ").
Tablas | Sí | Lista separada por comas de nombres de tabla, vista y alias necesarios para operaciones de OData y generar documentación swagger con ejemplos (por ejemplo, "NEWORDERS").
Procedimientos | Sí | Lista separada por comas de nombres de función y de procedimiento (por ejemplo, "SPORDERID").
Línea | No | Implementar locales con Azure retransmisión de Bus de servicio.
ServiceBusConnectionString | No | Cadena de conexión de Azure retransmisión de Bus de servicio.
PollToCheckData | No | Instrucción SELECT COUNT para usar con un desencadenador de aplicación lógica (por ejemplo, "SELECT COUNT (\*) desde NEWORDERS WHERE FECHAENVÍO es nulo").
PollToReadData | No | Instrucción SELECT para usar con un desencadenador de aplicación lógica (por ejemplo, "seleccione \* de NEWORDERS donde FECHAENVÍO es nulo para actualización").
PollToAlterData | No | ACTUALIZAR o instrucción DELETE para usarlos con un desencadenador de aplicación lógica (por ejemplo, "FECHAENVÍO de actualización NEWORDERS establecer = actual fecha donde actual de &lt;CURSOR&gt;").

7. Haga clic en **Aceptar**y, a continuación, seleccione **crear**.
8. Cuando haya terminado, la configuración del paquete un aspecto similar al siguiente:  
![][1]


## <a name="logic-app-with-informix-connector-action-to-add-data"></a>Aplicación de lógica con la acción de conector de Informix para agregar datos ##
Puede definir una acción de aplicación lógica para agregar datos a una tabla de Informix mediante API Insert o entrada de la operación de OData de entidad. Por ejemplo, puede insertar un nuevo registro de pedido de cliente, mediante el procesamiento de una instrucción SQL INSERT frente a una tabla definida con una columna de identidad, que devuelve el valor de identidad o las filas afectadas a la aplicación de lógica (seleccione ORDID desde la tabla FINAL (valores de insertar en NEWORDERS (IDCLIENTE, NOMBREENVÍO, SHIPADDR, CIUDADENVÍO, SHIPREG, SHIPZIP) (?,?,?,?,?,?))).

> [AZURE.TIP] Conexión de Informix "*Publicar EntitySet*" devuelve el valor de la columna de identidad y "*Insertar API*" devuelve filas afectadas

1. En la startboard Azure, seleccione **+** (signo más), **Web + móvil**y, a continuación, en **aplicación de lógica**.
2. Escriba el nombre (por ejemplo, "NewOrdersInformix"), el Plan de servicio de aplicación, otras propiedades y haga clic en **crear**.
3. En la startboard Azure, seleccione la lógica aplicación que acaba de crear, **configuración**y, a continuación, **desencadenadores y acciones**.
4. En el módulo desencadenadores y las acciones, seleccione **crear desde cero** dentro de la aplicación de lógica plantillas.
5. En el panel de la API aplicaciones, seleccione **Periodicidad**, establezca una frecuencia e intervalo y, a continuación, **marca de verificación**.
6. En el panel de la API aplicaciones, seleccione **conector Informix**, expanda la lista de operaciones para seleccionar **Insertar en NEWORDER**.
7. Expandir la lista de parámetros para especificar los siguientes valores:  

    Nombre | Valor
--- | --- 
IDCLIENTE | 10042
IDENVÍO | 10000
NOMBREENVÍO | K diferida Kountry almacén 
SHIPADDR | Orquesta 12 terraza
CIUDADENVÍO | Walla Walla 
SHIPREG | WA
SHIPZIP | 99362 

8. Seleccione la **marca de verificación** para guardar la configuración de la acción y **Guardar**.
9. La configuración debe tener un aspecto como sigue:  
![][3]  
10. En la lista **todo se ejecuta** en **operaciones**, seleccione el elemento indican primero (ejecutar más reciente). 
11. En el módulo de **aplicación de lógica ejecutar** , seleccione el elemento de **acción** **informixconnectorneworders**.
12. En el módulo de **acción de la aplicación de lógica** , seleccione el **Vínculo de entradas**. Conector de Informix utiliza las entradas para procesar una instrucción INSERT con parámetros.
13. En el módulo de **acción de la aplicación de lógica** , seleccione el **Vínculo de resultados**. Las entradas deben tener el siguiente aspecto:  
![][4]

#### <a name="what-you-need-to-know"></a>¿Qué necesita saber

- Conector trunca los nombres de tabla de Informix al formar los nombres de acciones de aplicación lógica. Por ejemplo, la operación de **Insertar en NEWORDERS** se trunca a **Insertar en NEWORDER**.
- Después de guardar la aplicación lógica **desencadenadores y acciones**, lógica aplicación procesa la operación. Puede haber un retraso de un número de segundos (por ejemplo, 3-5) antes de la lógica de aplicación procesa la operación. Si lo desea, puede hacer clic en **Ejecutar ahora** para procesar la operación.
- Conector de Informix define a los miembros de EntitySet con atributos, incluso si el miembro corresponde a una columna de Informix con un valor predeterminado o generados por columnas (por ejemplo, identidad). Aplicación de lógica muestra un asterisco rojo junto al nombre del identificador del miembro de EntitySet, para indicar las columnas de Informix que requieren valores. No debe escribir un valor para el miembro ORDID, que corresponde a la columna de identidad de Informix. Puede especificar valores para otros miembros opcionales (elementos, ORDDATE, REQDATE, IDENVÍO, flete, SHIPCTRY), que corresponden a columnas de Informix con valores predeterminados. 
- Conector de Informix devuelve aplicación lógica la respuesta en la entrada de EntitySet que incluye los valores de columnas de identidad, que se deriva de la SQLDARD DRDA (datos de respuesta del área de datos de SQL) en la instrucción SQL INSERT preparada. Servidor de Informix no devuelve los valores insertados para las columnas con valores predeterminados.  


## <a name="logic-app-with-informix-connector-action-to-add-bulk-data"></a>Aplicación de lógica con la acción de conector de Informix para agregar datos de forma masiva ##
Puede definir una acción de aplicación lógica para agregar datos a una tabla de Informix mediante una operación de inserción masiva de API. Por ejemplo, puede insertar dos registros de pedidos de cliente nuevo, mediante el procesamiento de una instrucción SQL INSERT con una matriz de valores de fila en una tabla definida con una columna de identidad, que devuelve las filas afectadas a la aplicación de lógica (seleccione ORDID desde la tabla FINAL (valores de insertar en NEWORDERS (IDCLIENTE, NOMBREENVÍO, SHIPADDR, CIUDADENVÍO, SHIPREG, SHIPZIP) (?,?,?,?,?,?))).

1. En la startboard Azure, seleccione **+** (signo más), **Web + móvil**y, a continuación, en **aplicación de lógica**.
2. Escriba el nombre (por ejemplo, "NewOrdersBulkInformix"), el Plan de servicio de aplicación, otras propiedades y haga clic en **crear**.
3. En la startboard Azure, seleccione la lógica aplicación que acaba de crear, **configuración**y, a continuación, **desencadenadores y acciones**.
4. En el módulo desencadenadores y las acciones, seleccione **crear desde cero** dentro de la aplicación de lógica plantillas.
5. En el panel de la API aplicaciones, seleccione **Periodicidad**, establezca una frecuencia e intervalo y, a continuación, **marca de verificación**.
6. En el panel de aplicaciones de la API, seleccione **el conector de Informix**, expanda la lista de operaciones para seleccionar **Masa insertar en nuevo**.
7. Escriba el valor de **filas** como una matriz. Por ejemplo, copie y pegue lo siguiente:  

    ```
    [{"custid":10081,"shipid":10000,"shipname":"Trail's Head Gourmet Provisioners","shipaddr":"722 DaVinci Blvd.","shipcity":"Kirkland","shipreg":"WA","shipzip":"98034"},{"custid":10088,"shipid":10000,"shipname":"White Clover Markets","shipaddr":"305 14th Ave. S. Suite 3B","shipcity":"Seattle","shipreg":"WA","shipzip":"98128","shipctry":"USA"}]
    ```
        
8. Seleccione la **marca de verificación** para guardar la configuración de la acción y **Guardar**. La configuración debe tener un aspecto como sigue:  
![][6]

9. En la lista **todo se ejecuta** en **operaciones**, haga clic en el elemento indican primero (ejecutar más reciente).
10. En el módulo de **aplicación de lógica ejecutar** , haga clic en el elemento de **acción** .
11. En el módulo de **acción de la aplicación de lógica** , haga clic en el **Vínculo de entradas**. Los resultados deben tener el siguiente aspecto:  
[][7]
12. En el módulo de **acción de la aplicación de lógica** , haga clic en el **Vínculo de resultados**. Los resultados deben tener el siguiente aspecto:  
![][8]

#### <a name="what-you-need-to-know"></a>¿Qué necesita saber

- Conector trunca los nombres de tabla de Informix al formar los nombres de acciones de aplicación lógica. Por ejemplo, la operación de **Inserción masiva en NEWORDERS** se trunca a **Masa insertar en nuevo**.
- Base de datos de Informix puede ser mayúsculas y minúsculas en los nombres de tabla y columna. Por ejemplo, es podrán que los nombres de columna de matriz de operación de inserción masiva necesite especificarse en minúsculas ("IdCliente") en lugar de mayúsculas ("IDCLIENTE").
- Si se omite columnas con los valores predeterminados (por ejemplo, ORDDATE, REQDATE, IDENVÍO, flete, SHIPCTRY), columnas nullable (por ejemplo, FECHAENVÍO) y columnas de identidad (por ejemplo, ORDID), base de datos de Informix genera valores.
- Al especificar "hoy" y "mañana", conector de Informix genera "Fecha actual" y "Fecha actual + 1 día" funciones (por ejemplo, REQDATE). 


## <a name="logic-app-with-informix-connector-trigger-to-read-alter-or-delete-data"></a>Aplicación de lógica con desencadenador de conector de Informix a leer, modificar o eliminar datos ##
Puede definir un desencadenador de aplicación lógica para sondear y leer los datos de una tabla de Informix mediante una operación de composición de datos de sondeo de API. Por ejemplo, puede leer uno o más registros de pedidos de cliente nuevo, devolver los registros a la aplicación de la lógica. La configuración de conexión de Informix paquete/aplicación debe tener un aspecto como sigue:

    Configuración de la aplicación | Valor
--- | --- | ---
PollToCheckData | Seleccione recuento (\*) desde NEWORDERS donde FECHAENVÍO es nulo
PollToReadData | Seleccione \* de NEWORDERS donde FECHAENVÍO es nulo para la actualización
PollToAlterData | <no value specified>


Además, puede definir un desencadenador de aplicación lógica para sondear, leer y modificar los datos en una tabla de Informix mediante una operación de composición de datos de sondeo de API. Por ejemplo, puede leer uno o más registros de pedidos de cliente nuevo, actualice los valores de fila, que devuelve los registros seleccionados (antes de la actualización) a la aplicación de la lógica. La configuración de conexión de Informix paquete/aplicación debe tener un aspecto como sigue:

    Configuración de la aplicación | Valor
--- | --- | ---
PollToCheckData | Seleccione recuento (\*) desde NEWORDERS donde FECHAENVÍO es nulo
PollToReadData | Seleccione \* de NEWORDERS donde FECHAENVÍO es nulo para la actualización
PollToAlterData | ACTUALIZACIÓN NEWORDERS conjunto FECHAENVÍO = fecha actual donde actual OF &lt;CURSOR&gt;


Además, puede definir un desencadenador de aplicación lógica para sondear, lea y quitar datos de una tabla de Informix mediante una operación de composición de datos de sondeo de API. Por ejemplo, puede leer uno o más registros de pedidos de cliente nuevo, elimine las filas, que devuelve los registros seleccionados (antes de eliminar) a la aplicación de la lógica. La configuración de conexión de Informix paquete/aplicación debe tener un aspecto como sigue:

    Configuración de la aplicación | Valor
--- | --- | ---
PollToCheckData | Seleccione recuento (\*) desde NEWORDERS donde FECHAENVÍO es nulo
PollToReadData | Seleccione \* de NEWORDERS donde FECHAENVÍO es nulo para la actualización
PollToAlterData | ELIMINAR NEWORDERS donde actual OF &lt;CURSOR&gt;

En este ejemplo, aplicación lógica se sondeos, leer, actualizar y, a continuación, volver a leer los datos de la tabla de Informix.

1. En la startboard Azure, seleccione **+** (signo más), **Web + móvil**y, a continuación, en **aplicación de lógica**.
2. Escriba el nombre (por ejemplo, "ShipOrdersInformix"), el Plan de servicio de aplicación, otras propiedades y haga clic en **crear**.
3. En la startboard Azure, seleccione la lógica aplicación que acaba de crear, **configuración**y, a continuación, **desencadenadores y acciones**.
4. En el módulo desencadenadores y las acciones, seleccione **crear desde cero** dentro de la aplicación de lógica plantillas.
5. En el panel de la API aplicaciones, seleccione **Informix conector**, establezca una frecuencia e intervalo y, a continuación, **marca de verificación**.
6. En el panel de aplicaciones de la API, seleccione **el conector de Informix**, expandir la lista de acciones para seleccionar **desde NEWORDERS**.
7. Seleccione la **marca de verificación** para guardar la configuración de la acción y **Guardar**. La configuración debe tener un aspecto como sigue:  
![][10]
8. Haga clic en para cerrar el módulo **desencadenadores y las acciones** y, a continuación, haga clic en para cerrar el módulo de **configuración** .
9. En la lista **todo se ejecuta** en **operaciones**, haga clic en el elemento indican primero (ejecutar más reciente).
10. En el módulo de **aplicación de lógica ejecutar** , haga clic en el elemento de **acción** .
11. En el módulo de **acción de la aplicación de lógica** , haga clic en el **Vínculo de resultados**. Los resultados deben tener el siguiente aspecto:  
![][11]


## <a name="logic-app-with-informix-connector-action-to-remove-data"></a>Aplicación de lógica con la acción de conector de Informix para quitar los datos ##
Puede definir una acción de aplicación lógica para eliminar datos de una tabla de Informix mediante una API eliminar o publicar la operación de OData de entidad. Por ejemplo, puede insertar un nuevo registro de pedido de cliente, mediante el procesamiento de una instrucción SQL INSERT frente a una tabla definida con una columna de identidad, que devuelve el valor de identidad o las filas afectadas a la aplicación de lógica (seleccione ORDID desde la tabla FINAL (valores de insertar en NEWORDERS (IDCLIENTE, NOMBREENVÍO, SHIPADDR, CIUDADENVÍO, SHIPREG, SHIPZIP) (?,?,?,?,?,?))).

## <a name="create-logic-app-using-informix-connector-to-remove-data"></a>Crear aplicaciones de lógica con conector de Informix para quitar los datos ##
Puede crear una nueva aplicación de lógica de Azure Marketplace y, a continuación, utilice el conector de Informix como una acción a quitar pedidos de clientes. Por ejemplo, puede usar la operación de eliminación de Informix conector condicional para procesar una instrucción SQL DELETE (eliminar de NEWORDERS donde ORDID > = 10000).

1. En el menú de concentrador del panel Azure **Inicio** , haga clic en **+** (signo más), haga clic en **Web + móvil**y, a continuación, haga clic en **aplicación de la lógica**. 
2. En el módulo de **aplicación de la lógica de crear** , escriba un **nombre**, por ejemplo **RemoveOrdersInformix**.
3. Seleccione o defina los valores de la configuración (por ejemplo, un plan de servicio, grupo de recursos).
4. La configuración debería tener el siguiente aspecto. Haga clic en **crear**:  
![][12]
5. En el módulo de **configuración** , haga clic en **desencadenadores y acciones**.
6. En el módulo de **desencadenadores y acciones** , en la lista de **aplicaciones de lógica plantillas** , haga clic en **crear desde cero**.
7. En el módulo de **desencadenadores y acciones** , en el panel de **Aplicaciones de la API** , dentro del grupo de recursos, haga clic en **Periodicidad**.
8. En la superficie de diseño de aplicación lógica, haga clic en el elemento de **repetición** , establezca una **frecuencia** y el **intervalo**, por ejemplo, **días** y **1**y, a continuación, haga clic en la **marca de verificación** para guardar la configuración del elemento de periodicidad.
9. En el módulo de **desencadenadores y acciones** , en el panel de **Aplicaciones de la API** , dentro del grupo de recursos, haga clic en **conector de Informix**.
10. En la superficie de diseño de aplicación lógica, haga clic en el elemento de acción de **conector de Informix** , haga clic en los puntos suspensivos (**...**) para expandir la lista de acciones y, a continuación, haga clic en **Eliminar condicional de N**.
11. En el elemento de acción de conector Informix, escriba **ordid página 10000** para una **expresión que identifica un subconjunto de entradas**.
12. Haga clic en la **marca de verificación** para guardar la configuración de la acción y, a continuación, haga clic en **Guardar**. La configuración debe tener un aspecto como sigue:  
![][13]
13. Haga clic en para cerrar el módulo **desencadenadores y las acciones** y, a continuación, haga clic en para cerrar el módulo de **configuración** .
14. En la lista **todo se ejecuta** en **operaciones**, haga clic en el elemento indican primero (ejecutar más reciente).
15. En el módulo de **aplicación de lógica ejecutar** , haga clic en el elemento de **acción** .
16. En el módulo de **acción de la aplicación de lógica** , haga clic en el **Vínculo de resultados**. Los resultados deben tener el siguiente aspecto:  
![][14]

**Nota:** Diseñador de aplicación lógica trunca los nombres de tabla. Por ejemplo, la operación **condicional eliminar de NEWORDERS** se trunca **condicional**eliminar de N.


> [AZURE.TIP] Use las siguientes instrucciones SQL para crear la tabla de ejemplo y los procedimientos almacenados. 

Puede crear la tabla NEWORDERS de ejemplo con las siguientes instrucciones DDL de SQL de Informix:
 
    create table neworders (  
        ordid serial(10000) unique ,  
        custid int not null ,  
        empid int not null default 10000 ,  
        orddate date not null default today ,  
        reqdate date default today ,  
        shipdate date ,  
        shipid int not null default 10000 ,  
        freight decimal (9,2) not null default 0.00 ,  
        shipname char (40) not null ,  
        shipaddr char (60) not null ,  
        shipcity char (20) not null ,  
        shipreg char (15) not null ,  
        shipzip char (10) not null ,  
        shipctry char (15) not null default ''USA'' 
        )


Puede crear el procedimiento SPORDERID almacenado de ejemplo con la siguiente instrucción de Informix DDL:
 
    create procedure sporderid ( ord_id int)  
        returning int, int, int, date, date, date, int, decimal (9,2), char (40), char (60), char (20), char (15), char (10), char (15)  
        define xordid, xcustid, xempid, xshipid int;  
        define xorddate, xreqdate, xshipdate date;  
        define xfreight decimal (9,2);  
        define xshipname char (40);  
        define xshipaddr char (60);  
        define xshipcity char (20);  
        define xshipreg, xshipctry char (15);  
        define xshipzip char (10);  
        select ordid, custid, empid, orddate, reqdate, shipdate, shipid, freight, shipname, shipaddr, shipcity, shipreg, shipzip, shipctry  
            into xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry  
            from neworders where ordid = ord_id;  
        return xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry;  
    end procedure; 


## <a name="hybrid-configuration-optional"></a>Configuración híbrida (opcional)

> [AZURE.NOTE] Este paso es necesario únicamente si está utilizando DB2 conector local detrás del firewall.

Aplicación de servicio usa el Administrador de configuración híbrida para conectarse a su sistema local de forma segura. Si el conector utiliza un local IBM DB2 para Windows Server, el Administrador de conexión híbrido es necesario.

Consulte [con el Administrador de conexión híbrido](app-service-logic-hybrid-connection-manager.md).


## <a name="do-more-with-your-connector"></a>Hacer más cosas con el conector
Una vez creado el conector, puede agregar un flujo de trabajo empresarial con una aplicación de lógica. Consulte [¿qué aplicaciones de lógica?](app-service-logic-what-are-logic-apps.md).

Crear las aplicaciones de la API con la API de REST. Vea [referencia de aplicaciones de la API y conectores](http://go.microsoft.com/fwlink/p/?LinkId=529766).

También puede revisar la seguridad de las estadísticas y control de rendimiento para el conector. Consulte [administrar y supervisar la API de aplicaciones y los conectores integrados](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-informix/ApiApp_InformixConnector_Create.png
[2]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Create.png
[3]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_TriggersActions.png
[4]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Outputs.png
[5]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Create.png
[6]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_TriggersActions.png
[7]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Inputs.png
[8]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Outputs.png
[9]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Create.png
[10]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_TriggersActions.png
[11]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Outputs.png
[12]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Create.png
[13]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_TriggersActions.png
[14]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Outputs.png


