<properties
    pageTitle="Agregar el conector DB2 en las aplicaciones de lógica | Microsoft Azure"
    description="Información general del conector de DB2 con parámetros de la API de REST"
    services=""
    documentationCenter="" 
    authors="gplarsen"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="09/26/2016"
   ms.author="plarsen"/>


# <a name="get-started-with-the-db2-connector"></a>Empezar a trabajar con el conector de DB2
Conector de Microsoft para DB2 conecta lógica de aplicaciones a los recursos almacenados en una base de datos de IBM DB2. Este conector incluye un cliente de Microsoft para comunicarse con equipos remotos de servidor DB2 a través de una red TCP/IP. Esto incluye bases de datos de nube como IBM Bluemix dashDB o IBM DB2 para Windows que se ejecuta en Azure virtualización y un local bases de datos mediante la puerta de enlace de datos local. Vea la [lista compatibles](connectors-create-api-db2.md#supported-db2-platforms-and-versions) de IBM DB2 plataformas y versiones (en este tema).

>[AZURE.NOTE] Esta versión de este artículo se aplica a la disponibilidad de aplicaciones de lógica general (GA). 

El conector de DB2 admite las siguientes operaciones de base de datos:

- Tablas de base de datos de lista
- Leer una fila mediante SELECT
- Leer todas las filas con SELECT
- Agregar una fila mediante INSERT
- Modificar una fila con la actualización
- Quitar una fila mediante la opción Eliminar

Este tema muestra cómo usar el conector en una aplicación de lógica para operaciones de base de datos de proceso.

Para obtener más información acerca de la lógica de aplicaciones, vea [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="available-actions"></a>Acciones disponibles
El conector de DB2 admite las siguientes acciones de aplicación de lógica:

- GetTables
- GetRow
- GetRows
- InsertRow
- UpdateRow
- Borracelda


## <a name="list-tables"></a>Tablas de lista
Crear una aplicación de lógica para cualquier operación consta de varios pasos que se realizan a través del portal de Microsoft Azure.

Dentro de la aplicación de la lógica, puede agregar una acción para mostrar las tablas en una base de datos DB2. La acción indica que el conector para procesar una instrucción de esquema DB2, como por ejemplo `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Crear una aplicación de lógica
1.  En el **panel de inicio de Azure**, seleccione **+** (signo más), **Web + móvil**y, a continuación, en **Lógica de aplicación**.
2.  Escriba el **nombre**, como por ejemplo `Db2getTables`, **suscripción**, **grupo de recursos**, **ubicación**y **Plan de servicio de aplicación**. Seleccione **Anclar al panel**y haga clic en **crear**.

### <a name="add-a-trigger-and-action"></a>Agregar un desencadenador y la acción
1.  En el **Diseñador de lógica de aplicaciones**, seleccione **LogicApp en blanco** en la lista de **plantillas** .
2.  En la lista de **desencadenadores** , seleccione **Periodicidad**. 
3.  En el desencadenador de **Periodicidad** , seleccione **Editar**, seleccione la **frecuencia** de lista desplegable para seleccionar el **día**y a continuación, establezca el **intervalo** , escriba **7**.  
4.  Active la casilla **+ nuevo paso** y, a continuación, seleccione **Agregar una acción**.
5.  En la lista de **acciones** , escriba `db2` en la **búsqueda para ver más acciones** de cuadro de edición y seleccione **DB2 - obtener tablas (vista preliminar)**.

    ![](./media/connectors-create-api-db2/Db2connectorActions.png)  

6.  En el panel de configuración **DB2 - obtener tablas** , seleccione la **casilla de verificación** habilitar **Conectar a través de la puerta de enlace de datos local**. Observe que cambie la configuración de nube a local.
    - Escriba el valor de **servidor**, en el formato de número de puerto de dos puntos de dirección o alias. Por ejemplo, escriba `ibmserver01:50000`.
    - Escriba el valor de **base de datos**. Por ejemplo, escriba `nwind`.
    - Seleccione el valor para la **autenticación**. Por ejemplo, seleccione **básica**.
    - Escriba el valor de **nombre de usuario**. Por ejemplo, escriba `db2admin`.
    - Escriba el valor de **contraseña**. Por ejemplo, escriba `Password1`.
    - Seleccione el valor de **puerta de enlace**. Por ejemplo, seleccione **datagateway01**.
7. Seleccione **crear**y, a continuación, seleccione **Guardar**. 

    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

8.  En el módulo **Db2getTables** , dentro de la lista **todo se ejecuta** en **Resumen**, seleccione el elemento indican primero (ejecutar más reciente).
9.  En el módulo de **aplicación de lógica ejecutar** , seleccione **Detalles**. En la lista de **acciones** , seleccione **Get_tables**. Ver el valor de **estado**, que debe ser **correcta**. Seleccione el **vínculo de entradas** para ver las entradas. Seleccione el **vínculo de resultados**y ver los resultados; que debe incluir una lista de tablas.

    ![](./media/connectors-create-api-db2/Db2connectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Crear las conexiones
Este conector es compatible con conexiones a bases de datos hospedadas local y en la nube usando las siguientes propiedades de conexión. 

(Propiedad) | Descripción
--- | ---
servidor | Obligatorio. Acepta un valor de cadena que representa una dirección TCP/IP o alias, en formato IPv4 o IPv6, seguido (delimitado por dos puntos) por un número de puerto TCP/IP. 
base de datos | Obligatorio. Acepta un valor de cadena que representa un nombre de base de datos relacional DRDA (RDBNAM). DB2 para z/OS acepta una cadena de 16 bits (base de datos se conoce como un IBM DB2 para ubicación z/OS). DB2 para i5/OS acepta una cadena de 18 bytes (base de datos se conoce como un IBM DB2 para i relacional base de datos). DB2 para LUW acepta una cadena de 8 bytes.
autenticación | Opcional. Acepta un valor de elemento de lista, Basic o Windows (kerberos). 
nombre de usuario | Obligatorio. Acepta un valor de cadena. DB2 para z/OS acepta una cadena de 8 bytes. DB2 para i acepta una cadena de 10 bytes. DB2 para Linux o UNIX acepta una cadena de 8 bytes. DB2 para Windows acepta una cadena de 30 bytes.
contraseña | Obligatorio. Acepta un valor de cadena.
puerta de enlace | Obligatorio. Acepta un valor de elemento de lista, que representa la puerta de enlace de datos local definido para las aplicaciones de lógica dentro del grupo de almacenamiento.  

## <a name="create-the-on-premises-gateway-connection"></a>Crear el local de conexión de puerta de enlace
Este conector puede acceder a una base de datos local DB2 mediante la puerta de enlace local. Consulte los temas de la puerta de enlace para obtener más información. 

1. En el panel de configuración de **puertas de enlace** , seleccione la **casilla de verificación** habilitar **Conectar a través de la puerta de enlace**. Observe que cambie la configuración de nube a local.
2. Escriba el valor de **servidor**, en el formato de número de puerto de dos puntos de dirección o alias. Por ejemplo, escriba `ibmserver01:50000`.
3. Escriba el valor de **base de datos**. Por ejemplo, escriba `nwind`.
4. Seleccione el valor para la **autenticación**. Por ejemplo, seleccione **básica**.
5. Escriba el valor de **nombre de usuario**. Por ejemplo, escriba `db2admin`.
6. Escriba el valor de **contraseña**. Por ejemplo, escriba `Password1`.
7. Seleccione el valor de **puerta de enlace**. Por ejemplo, seleccione **datagateway01**.
8. Seleccione **crear** para continuar. 

    ![](./media/connectors-create-api-db2/Db2connectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Crear la conexión de la nube
Este conector puede acceder a una base de datos de nube DB2. 

1. En el panel de configuración de **puertas de enlace** , deje la **casilla de verificación** había deshabilitado (los) **Conectar a través de la puerta de enlace**. 
2. Escriba el valor de **nombre de la conexión**. Por ejemplo, escriba `hisdemo2`.
3. Escriba el valor de **nombre de servidor de DB2**, en el formato de número de puerto de dos puntos de dirección o alias. Por ejemplo, escriba `hisdemo2.cloudapp.net:50000`.
3. Escriba el valor de **nombre de la base de datos de DB2**. Por ejemplo, escriba `nwind`.
4. Escriba el valor de **nombre de usuario**. Por ejemplo, escriba `db2admin`.
5. Escriba el valor de **contraseña**. Por ejemplo, escriba `Password1`.
6. Seleccione **crear** para continuar. 

    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Recuperar todas las filas con SELECT
Puede definir una acción de aplicación lógica para recuperar todas las filas de una tabla de DB2. Esto indica que el conector para procesar una instrucción SELECT DB2, como por ejemplo `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Crear una aplicación de lógica
1.  En el **panel de inicio de Azure**, seleccione **+** (signo más), **Web + móvil**y, a continuación, en **Lógica de aplicación**.
2.  Escriba el **nombre**, como por ejemplo `Db2getRows`, **suscripción**, **grupo de recursos**, **ubicación**y **Plan de servicio de aplicación**. Seleccione **Anclar al panel**y haga clic en **crear**.

### <a name="add-a-trigger-and-action"></a>Agregar un desencadenador y la acción
1.  En el **Diseñador de lógica de aplicaciones**, seleccione **LogicApp en blanco** en la lista de **plantillas** .
2.  En la lista de **desencadenadores** , seleccione **Periodicidad**. 
3.  En el desencadenador de **Periodicidad** , seleccione **Editar**, seleccione la **frecuencia** de lista desplegable para seleccionar el **día**y a continuación, seleccione el **intervalo** , escriba **7**. 
4.  Active la casilla **+ nuevo paso** y, a continuación, seleccione **Agregar una acción**.
5.  En la lista de **acciones** , escriba `db2` en la **búsqueda para ver más acciones** de cuadro de edición y seleccione **DB2 - obtener filas (vista preliminar)**.
6. En la acción **obtener filas (vista preliminar)** , seleccione **Cambiar conexión**.
7. En el panel de configuración de **conexiones** , seleccione **Crear nuevo**. 

    ![](./media/connectors-create-api-db2/Db2connectorNewConnection.png)
  
8. En el panel de configuración de **puertas de enlace** , deje la **casilla de verificación** había deshabilitado (los) **Conectar a través de la puerta de enlace**.
    - Escriba el valor de **nombre de la conexión**. Por ejemplo, escriba `HISDEMO2`.
    - Escriba el valor de **nombre de servidor de DB2**, en el formato de número de puerto de dos puntos de dirección o alias. Por ejemplo, escriba `HISDEMO2.cloudapp.net:50000`.
    - Escriba el valor de **nombre de la base de datos de DB2**. Por ejemplo, escriba `NWIND`.
    - Escriba el valor de **nombre de usuario**. Por ejemplo, escriba `db2admin`.
    - Escriba el valor de **contraseña**. Por ejemplo, escriba `Password1`.
9. Seleccione **crear** para continuar.

    ![](./media/connectors-create-api-db2/Db2connectorCloudConnection.png)

10. En la lista **nombre de tabla** , seleccione la **flecha abajo**y, a continuación, seleccione **área**.
11. Si lo desea, seleccione **Mostrar opciones avanzadas** para especificar opciones de consulta.
12. Seleccione **Guardar**. 

    ![](./media/connectors-create-api-db2/Db2connectorGetRowsTableName.png)

13. En el módulo **Db2getRows** , dentro de la lista **todo se ejecuta** en **Resumen**, seleccione el elemento indican primero (ejecutar más reciente).
14. En el módulo de **aplicación de lógica ejecutar** , seleccione **Detalles**. En la lista de **acciones** , seleccione **Get_rows**. Ver el valor de **estado**, que debe ser **correcta**. Seleccione el **vínculo de entradas** para ver las entradas. Seleccione el **vínculo de resultados**y ver los resultados; que debe incluir una lista de filas.

    ![](./media/connectors-create-api-db2/Db2connectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Agregar una fila mediante INSERT
Puede definir una acción de aplicación lógica para agregar una fila de una tabla de DB2. Esta acción indica que el conector para procesar una instrucción INSERT DB2, como por ejemplo `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Crear una aplicación de lógica
1.  En el **panel de inicio de Azure**, seleccione **+** (signo más), **Web + móvil**y, a continuación, en **Lógica de aplicación**.
2.  Escriba el **nombre**, como por ejemplo `Db2insertRow`, **suscripción**, **grupo de recursos**, **ubicación**y **Plan de servicio de aplicación**. Seleccione **Anclar al panel**y haga clic en **crear**.

### <a name="add-a-trigger-and-action"></a>Agregar un desencadenador y la acción
1.  En el **Diseñador de lógica de aplicaciones**, seleccione **LogicApp en blanco** en la lista de **plantillas** .
2.  En la lista de **desencadenadores** , seleccione **Periodicidad**. 
3.  En el desencadenador de **Periodicidad** , seleccione **Editar**, seleccione la **frecuencia** de lista desplegable para seleccionar el **día**y a continuación, seleccione el **intervalo** , escriba **7**. 
4.  Active la casilla **+ nuevo paso** y, a continuación, seleccione **Agregar una acción**.
5.  En la lista de **acciones** , escriba **db2** en el cuadro de edición **Buscar más acciones** y, a continuación, seleccione **DB2 - Insertar fila (vista preliminar)**.
6. En la acción **obtener filas (vista preliminar)** , seleccione **Cambiar conexión**. 
7. En el panel de configuración de **conexiones** , seleccione una conexión. Por ejemplo, seleccione **hisdemo2**.

    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. En la lista **nombre de tabla** , seleccione la **flecha abajo**y, a continuación, seleccione **área**.
9. Especifique valores para todos los columnas (asterisco rojo). Por ejemplo, escriba `99999` **AREAID**, escriba `Area 99999`y escriba `102` para **REGIONID**. 
10. Seleccione **Guardar**.

    ![](./media/connectors-create-api-db2/Db2connectorInsertRowValues.png)
 
11. En el módulo **Db2insertRow** , dentro de la lista **todo se ejecuta** en **Resumen**, seleccione el elemento indican primero (ejecutar más reciente).
12. En el módulo de **aplicación de lógica ejecutar** , seleccione **Detalles**. En la lista de **acciones** , seleccione **Get_rows**. Ver el valor de **estado**, que debe ser **correcta**. Seleccione el **vínculo de entradas** para ver las entradas. Seleccione el **vínculo de resultados**y ver los resultados; que debe incluir la nueva fila.

    ![](./media/connectors-create-api-db2/Db2connectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Capturar una fila mediante SELECT
Puede definir una acción de aplicación lógica para capturar una fila de una tabla de DB2. Esta acción indica que el conector para procesar una instrucción DB2 seleccione donde, como por ejemplo `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Crear una aplicación de lógica
1.  En el **panel de inicio de Azure**, seleccione **+** (signo más), **Web + móvil**y, a continuación, en **Lógica de aplicación**.
2.  Escriba el **nombre** (por ejemplo, "**Db2getRow**"), **suscripción**, **grupo de recursos**, **ubicación**y **Plan de servicio de aplicación**. Seleccione **Anclar al panel**y haga clic en **crear**.

### <a name="add-a-trigger-and-action"></a>Agregar un desencadenador y la acción
1.  En el **Diseñador de lógica de aplicaciones**, seleccione **LogicApp en blanco** en la lista de **plantillas** . 
2.  En la lista de **desencadenadores** , seleccione **Periodicidad**. 
3.  En el desencadenador de **Periodicidad** , seleccione **Editar**, seleccione la **frecuencia** de lista desplegable para seleccionar el **día**y a continuación, seleccione el **intervalo** , escriba **7**. 
4.  Active la casilla **+ nuevo paso** y, a continuación, seleccione **Agregar una acción**.
5.  En la lista de **acciones** , escriba **db2** en el cuadro de edición **Buscar más acciones** y, a continuación, seleccione **DB2 - obtener filas (vista preliminar)**.
6. En la acción **obtener filas (vista preliminar)** , seleccione **Cambiar conexión**. 
7. En el panel de configuraciones de **conexiones** , seleccione una conexión existente. Por ejemplo, seleccione **hisdemo2**.

    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. En la lista **nombre de tabla** , seleccione la **flecha abajo**y, a continuación, seleccione **área**.
9. Especifique valores para todos los columnas (asterisco rojo). Por ejemplo, escriba `99999` para **AREAID**. 
10. Si lo desea, seleccione **Mostrar opciones avanzadas** para especificar opciones de consulta.
11. Seleccione **Guardar**. 

    ![](./media/connectors-create-api-db2/Db2connectorGetRowValues.png)

12. En el módulo **Db2getRow** , dentro de la lista **todo se ejecuta** en **Resumen**, seleccione el elemento indican primero (ejecutar más reciente).
13. En el módulo de **aplicación de lógica ejecutar** , seleccione **Detalles**. En la lista de **acciones** , seleccione **Get_rows**. Ver el valor de **estado**, que debe ser **correcta**. Seleccione el **vínculo de entradas** para ver las entradas. Seleccione el **vínculo de resultados**y ver los resultados; que debe incluir la fila.

    ![](./media/connectors-create-api-db2/Db2connectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Cambiar una fila con la actualización
Puede definir una acción de aplicación lógica para cambiar una fila de una tabla de DB2. Esta acción indica que el conector para procesar una instrucción UPDATE DB2, como por ejemplo `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Crear una aplicación de lógica
1.  En el **panel de inicio de Azure**, seleccione **+** (signo más), **Web + móvil**y, a continuación, en **Lógica de aplicación**.
2.  Escriba el **nombre**, como por ejemplo `Db2updateRow`, **suscripción**, **grupo de recursos**, **ubicación**y **Plan de servicio de aplicación**. Seleccione **Anclar al panel**y haga clic en **crear**.

### <a name="add-a-trigger-and-action"></a>Agregar un desencadenador y la acción
1.  En el **Diseñador de lógica de aplicaciones**, seleccione **LogicApp en blanco** en la lista de **plantillas** .
2.  En la lista de **desencadenadores** , seleccione **Periodicidad**. 
3.  En el desencadenador de **Periodicidad** , seleccione **Editar**, seleccione la **frecuencia** de lista desplegable para seleccionar el **día**y a continuación, seleccione el **intervalo** , escriba **7**. 
4.  Active la casilla **+ nuevo paso** y, a continuación, seleccione **Agregar una acción**.
5.  En la lista de **acciones** , escriba **db2** en el cuadro de edición **Buscar más acciones** y, a continuación, seleccione **DB2 - fila de actualización (vista preliminar)**.
6. En la acción **obtener filas (vista preliminar)** , seleccione **Cambiar conexión**. 
7. En el panel de configuraciones de **conexiones** , seleccione esta opción para seleccionar una conexión existente. Por ejemplo, seleccione **hisdemo2**.

    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. En la lista **nombre de tabla** , seleccione la **flecha abajo**y, a continuación, seleccione **área**.
9. Especifique valores para todos los columnas (asterisco rojo). Por ejemplo, escriba `99999` **AREAID**, escriba `Updated 99999`y escriba `102` para **REGIONID**. 
10. Seleccione **Guardar**. 

    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowValues.png)

11. En el módulo **Db2updateRow** , dentro de la lista **todo se ejecuta** en **Resumen**, seleccione el elemento indican primero (ejecutar más reciente).
12. En el módulo de **aplicación de lógica ejecutar** , seleccione **Detalles**. En la lista de **acciones** , seleccione **Get_rows**. Ver el valor de **estado**, que debe ser **correcta**. Seleccione el **vínculo de entradas** para ver las entradas. Seleccione el **vínculo de resultados**y ver los resultados; que debe incluir la nueva fila.

    ![](./media/connectors-create-api-db2/Db2connectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Quitar una fila mediante la opción Eliminar
Puede definir una acción de aplicación lógica para quitar una fila de una tabla de DB2. Esta acción indica que el conector para procesar una instrucción DELETE DB2, como por ejemplo `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Crear una aplicación de lógica
1.  En el **panel de inicio de Azure**, seleccione **+** (signo más), **Web + móvil**y, a continuación, en **Lógica de aplicación**.
2.  Escriba el **nombre**, como por ejemplo `Db2deleteRow`, **suscripción**, **grupo de recursos**, **ubicación**y **Plan de servicio de aplicación**. Seleccione **Anclar al panel**y haga clic en **crear**.

### <a name="add-a-trigger-and-action"></a>Agregar un desencadenador y la acción
1.  En el **Diseñador de lógica de aplicaciones**, seleccione **LogicApp en blanco** en la lista de **plantillas** . 
2.  En la lista de **desencadenadores** , seleccione **Periodicidad**. 
3.  En el desencadenador de **Periodicidad** , seleccione **Editar**, seleccione la **frecuencia** de lista desplegable para seleccionar el **día**y a continuación, seleccione el **intervalo** , escriba **7**. 
4.  Active la casilla **+ nuevo paso** y, a continuación, seleccione **Agregar una acción**.
5.  En la lista de **acciones** , seleccione **db2** en el cuadro de edición **Buscar más acciones** y, a continuación, seleccione **DB2 - Eliminar fila (vista preliminar)**.
6. En la acción **obtener filas (vista preliminar)** , seleccione **Cambiar conexión**. 
7. En el panel de configuraciones de **conexiones** , seleccione una conexión existente. Por ejemplo, seleccione **hisdemo2**.

    ![](./media/connectors-create-api-db2/Db2connectorChangeConnection.png)

8. En la lista **nombre de tabla** , seleccione la **flecha abajo**y, a continuación, seleccione **área**.
9. Especifique valores para todos los columnas (asterisco rojo). Por ejemplo, escriba `99999` para **AREAID**. 
10. Seleccione **Guardar**. 

    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowValues.png)

11. En el módulo **Db2deleteRow** , dentro de la lista **todo se ejecuta** en **Resumen**, seleccione el elemento indican primero (ejecutar más reciente).
12. En el módulo de **aplicación de lógica ejecutar** , seleccione **Detalles**. En la lista de **acciones** , seleccione **Get_rows**. Ver el valor de **estado**, que debe ser **correcta**. Seleccione el **vínculo de entradas** para ver las entradas. Seleccione el **vínculo de resultados**y ver los resultados; que debe incluir la fila eliminada.

    ![](./media/connectors-create-api-db2/Db2connectorDeleteRowOutputs.png)

## <a name="technical-details"></a>Detalles técnicos

## <a name="actions"></a>Acciones
Una acción es una operación que lleva a cabo por el flujo de trabajo que se definen en una aplicación de lógica. El conector de base de datos DB2 incluye las siguientes acciones. 

|Acción|Descripción|
|--- | ---|
|[GetRow](connectors-create-api-db2.md#get-row)|Recupera una sola fila de una tabla de DB2|
|[GetRows](connectors-create-api-db2.md#get-rows)|Recupera filas de una tabla de DB2|
|[InsertRow](connectors-create-api-db2.md#insert-row)|Inserta una nueva fila en una tabla DB2|
|[Borracelda](connectors-create-api-db2.md#delete-row)|Elimina una fila de una tabla de DB2|
|[GetTables](connectors-create-api-db2.md#get-tables)|Recupera tablas desde una base de datos DB2|
|[UpdateRow](connectors-create-api-db2.md#update-row)|Actualiza una fila en una tabla DB2 existente|

### <a name="action-details"></a>Detalles de acción

En esta sección, vea los detalles específicos acerca de cada acción, incluidas las propiedades de entrada opcionales o requeridas y ningún resultado correspondiente asociado con el conector.

#### <a name="get-row"></a>Obtener la fila 
Recupera una sola fila de una tabla de DB2.  

| Nombre de propiedad| Nombre para mostrar |Descripción|
| ---|---|---|
|tabla * | Nombre de tabla |Nombre de tabla de DB2|
|Id * | Identificador de fila |Identificador único de la fila para recuperar|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Elemento

| Nombre de propiedad | Tipo de datos |
|---|---|
|ItemInternalId|cadena|


#### <a name="get-rows"></a>Obtener filas 
Recupera filas de una tabla de DB2.  

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Nombre de tabla|Nombre de tabla de DB2|
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
Inserta una nueva fila en una tabla DB2.  

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Nombre de tabla|Nombre de tabla de DB2|
|elemento *|Fila|Fila para insertar en la tabla especificada en DB2|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Elemento

| Nombre de propiedad | Tipo de datos |
|---|---|
|ItemInternalId|cadena|


#### <a name="delete-row"></a>Eliminar fila 
Elimina una fila de una tabla de DB2.  

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Nombre de tabla|Nombre de tabla de DB2|
|Id *|Identificador de fila|Identificador único de la fila para eliminar|

Un asterisco (*) significa que la propiedad es necesaria.

##### <a name="output-details"></a>Detalles de salida
Ninguno.

#### <a name="get-tables"></a>Obtener tablas 
Recupera tablas desde una base de datos DB2.  

No hay parámetros para esta llamada. 

##### <a name="output-details"></a>Detalles de salida 
TablesList

| Nombre de propiedad | Tipo de datos |
|---|---|
|valor|matriz|

#### <a name="update-row"></a>Actualizar una fila 
Actualiza una fila en una tabla DB2 existente.  

|Nombre de propiedad| Nombre para mostrar|Descripción|
| ---|---|---|
|tabla *|Nombre de tabla|Nombre de tabla de DB2|
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


## <a name="supported-db2-platforms-and-versions"></a>Versiones y DB2 plataformas compatibles
Este conector admite las siguientes plataformas de IBM DB2 y de versiones, así como productos compatibles de IBM DB2 (por ejemplo, IBM Bluemix dashDB) que admiten la arquitectura de base de datos relacionales (DRDA) distribuido SQL Access Manager (SQLAM) versión 10 y 11:

- IBM DB2 para z/OS 11.1
- IBM DB2 para z/OS 10.1
- IBM DB2 para i 7.3
- IBM DB2 para i 7.2
- IBM DB2 para i 7.1
- IBM DB2 para LUW 11
- IBM DB2 para 10.5 LUW


## <a name="next-steps"></a>Pasos siguientes

[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Explore los otros conectores disponibles en las aplicaciones de lógica en nuestra [lista de API](apis-list.md).

