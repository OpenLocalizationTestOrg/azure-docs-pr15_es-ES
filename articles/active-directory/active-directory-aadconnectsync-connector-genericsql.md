<properties
   pageTitle="Conector SQL genérico | Microsoft Azure"
   description="En este artículo se describe cómo configurar conector genérico de SQL de Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="generic-sql-connector-technical-reference"></a>Referencia técnica de conector SQL genérico

Este artículo describe el conector de SQL genérico. El artículo se aplica a los siguientes productos:

- Administrador de identidad de Microsoft 2016 (MIM2016)
- Administrador de identidad de Forefront 2010 R2 (FIM2010R2)
    -   Debe usar revisión 4.1.3671.0 o posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 y FIM2010R2, el conector está disponible como descarga desde el [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

Para ver este conector en acción, vea el artículo de [Conector de SQL genérico paso a paso](active-directory-aadconnectsync-connector-genericsql-step-by-step.md) .

## <a name="overview-of-the-generic-sql-connector"></a>Información general sobre el conector SQL genérico

El conector de SQL genérico permite integrar el servicio de sincronización con un sistema de base de datos que ofrece conectividad ODBC.  

Desde una perspectiva de alto nivel, las siguientes características son compatibles con la versión actual del conector:

Característica | Soporte técnico
--- | ---
Origen de datos conectado | El conector es compatible con todos los controladores ODBC de 64 bits. Se ha probado con los siguientes elementos: <li>Microsoft SQL Server y SQL Azure</li><li>IBM DB2 10.x</li><li>IBM DB2 9.x</li><li>Oracle 10 y 11 g</li><li>MySQL 5.x</li>
Escenarios   | <li>Administración del ciclo de vida de objeto</li><li>Administración de contraseñas</li>
Operaciones | <li>Importación completa y Delta importar, exportar</li><li>Para exportar: Agregar, eliminar, actualizar y reemplazar</li><li>Establecer contraseña, cambiar contraseña</li>
Esquema | <li>Detección dinámica de objetos y atributos</li>

### <a name="prerequisites"></a>Requisitos previos
Antes de utilizar el conector, asegúrese de que tiene los siguientes en el servidor de sincronización:

- 4.5.2 de Microsoft .NET Framework o versiones posteriores
- controladores de cliente ODBC de 64 bits

### <a name="permissions-in-connected-data-source"></a>Permisos en el origen de datos conectado
Para crear o realizar cualquiera de las tareas admitidas en conector SQL genérico, debe tener:

- db_datareader
- db_datawriter

### <a name="ports-and-protocols"></a>Puertos y protocolos
Para los puertos necesarios para que el controlador ODBC para que funcione, consulte documentación del proveedor de la base de datos.

## <a name="create-a-new-connector"></a>Crear un nuevo conector
Para crear un conector de SQL genérico, en **El servicio de sincronización** , seleccione **Agente de administración** y **crear**. Seleccione el conector **SQL genérico (Microsoft)** .

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### <a name="connectivity"></a>Conectividad
El conector utiliza un archivo de DSN ODBC para conectividad. Crear el archivo DSN con **Orígenes de datos ODBC** que se encuentra en el menú Inicio, en **Herramientas administrativas**. En la herramienta administrativa, cree un **DSN de archivo** para que se puedan proporcionar al conector.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

La pantalla de conectividad es la primera cuando se crea un nuevo conector de SQL genérico. En primer lugar debe proporcionar la siguiente información:

- Ruta de acceso de archivo DSN
- Autenticación
    - Nombre de usuario
    - Contraseña

La base de datos debe admitir uno de estos métodos de autenticación:

- **Autenticación de Windows**: la base de datos de autenticación usa las credenciales de Windows para comprobar el usuario. El nombre de usuario y contraseña especificada se utiliza para autenticar con la base de datos. Esta cuenta necesita permisos para la base de datos.
- **Autenticación de SQL**: los usos de base de datos autentica el nombre de usuario y la contraseña definida una pantalla de conectividad para conectarse a la base de datos. Si el nombre de usuario y contraseña que se guardan en el archivo DSN, las credenciales proporcionadas en la pantalla de conectividad tienen prioridad.
- **Autenticación de base de datos de SQL Azure**: para obtener más información, vea [conectarse a la base de datos por usando Azure Active Directory autenticación de SQL](..\sql-database\sql-database-aad-authentication.md).

**DN es delimitador**: Si selecciona esta opción, el DN también se usa como el atributo de anclaje. Puede usarse para una implementación simple pero también tiene las siguientes limitaciones:

-   Conector es compatible con un solo tipo de objeto. Por lo tanto, los atributos de referencia sólo pueden hacer referencia del mismo tipo de objeto.

**Exportar, escriba: objeto reemplazar**: durante la exportación, cuando han cambiado sólo algunos atributos, todo el objeto con todos los atributos se exporta y reemplaza el objeto existente.

### <a name="schema-1-detect-object-types"></a>Esquema 1 (tipos de objeto de detectar)
En esta página, va a configurar cómo el conector se va a buscar los diferentes tipos de objeto en la base de datos.

Cada tipo de objeto se presentan como una partición y se configura en **configurar particiones y jerarquías**.

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**Método de detección de tipo de objeto**: el conector es compatible con estos métodos de detección de tipo de objeto.

- **Valor fijo**: proporciona la lista de tipos de objeto con una lista de valores separados por comas. Por ejemplo: `User,Group,Department`.  
![Schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
- **Procedimiento de la tabla, vista o almacenado**: proporcionar el nombre del procedimiento tabla/vista/almacena y, a continuación, en el nombre de columna que proporciona la lista de tipos de objeto. Si utiliza un procedimiento almacenado, a continuación, también proporcionan parámetros para él en el formato **[nombre]: [dirección]: [valor]**. Proporcionar cada parámetro en una línea independiente (use Ctrl + Entrar para obtener una nueva línea).  
![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
- **Consulta SQL**: esta opción permite proporcionar una consulta SQL que devuelve una columna única con tipos de objeto, por ejemplo `SELECT [Column Name] FROM TABLENAME`. La columna devuelta debe ser de tipo cadena (varchar).

### <a name="schema-2-detect-attribute-types"></a>Esquema 2 (tipos de atributos de detectar)
En esta página, va a configurar cómo va a cambiar los nombres de atributos y tipos de detectar. Se muestran las opciones de configuración para cada tipo de objeto detectado en la página anterior.

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**Método de detección de tipo de atributo**: el conector es compatible con estos métodos de detección de tipo de atributo con cada tipo de objeto detectado en la pantalla de esquema 1.

- **Procedimiento de la tabla, vista o almacenado**: proporcionar el nombre del tabla/vista o procedimiento almacenado en que se debe utilizar para buscar los nombres de atributo. Si utiliza un procedimiento almacenado, a continuación, también proporcionan parámetros para él en el formato **[nombre]: [dirección]: [valor]**. Proporcionar cada parámetro en una línea independiente (use Ctrl + Entrar para obtener una nueva línea). Para detectar los nombres de atributos en un atributo multivalor, proporcione una lista de valores separados por comas de las tablas o vistas. No se admiten escenarios multivalor cuando tabla principal y secundario tienen los mismos nombres de columna.
- **Consulta SQL**: esta opción permite proporcionar una consulta SQL que devuelve una columna única con nombres de atributo, por ejemplo `SELECT [Column Name] FROM TABLENAME`. La columna devuelta debe ser de tipo cadena (varchar).

### <a name="schema-3-define-anchor-and-dn"></a>Esquema 3 (anclaje de definir y DN)
Esta página le permite configurar anclaje y atributo DN para cada tipo de objeto detectado. Puede seleccionar varios atributo para que el delimitador único.

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

- No se muestran los atributos de valores múltiple y booleanos.
- Mismo atributo no puede usar para DN y anclaje, a menos que **DN anclaje** está seleccionada en la página conectividad.
- Si se selecciona la **DN es ancla** en la página conectividad, esta página requiere únicamente el atributo DN. Este atributo también se utilizará como el atributo de anclaje.
![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### <a name="schema-4-define-attribute-type-reference-and-direction"></a>Esquema 4 (Definir tipo de atributo, referencia y dirección)
Esta página le permite configurar el tipo de atributo, como entero, binario, o booleano y dirección de cada atributo. Se muestran todos los atributos de **esquema 2** de la página incluidos los atributos de valores múltiple.

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

- **Tipo de datos**: utilizado para asignar el tipo de atributo a esos tipos conocidos por el motor de sincronización. El valor predeterminado es usar el mismo tipo detectados en el esquema SQL, pero no son fácilmente detectable DateTime y referencia. Para los usuarios, debe especificar la **fecha y hora** o **referencia**.
- **Dirección**: puede establecer la dirección de atributo para importar, exportar o ImportExport. ImportExport es el valor predeterminado.
![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

Notas:

- Si un tipo de atributo no es detectable por el conector, usa el tipo de datos String.
- **Tablas anidadas** pueden considerarse tablas de base de datos de una columna. Oracle almacena las filas de una tabla anidada en ningún orden determinado. Sin embargo, cuando se recupera la tabla anidada en una variable de PL/SQL, las filas que reciben consecutivos subíndices empezando por 1. Le proporciona acceso a filas individuales.
- **VARRYS** no son compatibles con el conector.

### <a name="schema-5-define-partition-for-reference-attributes"></a>Esquema 5 (definir partición para atributos de referencia)
En esta página, configurar para qué partición (tipo de objeto) un atributo hace referencia a todos los atributos de referencia.

![Schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

Si usa **DN es anclaje**, debe utilizar el mismo tipo de objeto como la que se hace referencia desde. No se puede hacer referencia a otro tipo de objeto.

### <a name="global-parameters"></a>Parámetros globales
La página de parámetros globales se usa para configurar Delta importar, el formato de fecha y hora y el método de contraseña.

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)

El conector de SQL genérico es compatible con los siguientes métodos para importar Delta:

- **Desencadenador**: vea [Generar vistas Delta usando desencadenadores](https://technet.microsoft.com/library/cc708665.aspx).
- **Marca de agua**: un enfoque genérico que se pueden usar con cualquier base de datos. La consulta de marca de agua se rellena previamente basadas en el proveedor de base de datos. Se debe haber en cada tabla o vista utiliza una columna de marca de agua. Esta columna debe realizar un seguimiento de las inserciones y las actualizaciones de las tablas y sus dependientes (multivalor o secundarios) tablas. Se debe sincronizar el reloj entre el servidor de base de datos y el servicio de sincronización. Si no es así, se pueden omitir algunas entradas de la importación de delta.  
Limitación:
    - Estrategia de marca de agua no objetos eliminado de soporte técnico.
- **Instantánea**: (sólo funciona con Microsoft SQL Server) [generar las vistas de Delta mediante instantáneas](https://technet.microsoft.com/library/cc720640.aspx)
- **Seguimiento de cambios**: (sólo funciona con Microsoft SQL Server) [sobre el control de cambios](https://msdn.microsoft.com/library/bb933875.aspx)  
Limitaciones:
    - Acoplar & atributo DN deben ser parte de la clave principal para el objeto seleccionado en la tabla.
    - Consulta SQL no es compatible durante la importación y exportación con seguimiento de cambios.

**Parámetros adicionales**: especifique la zona de hora del servidor de base de datos que indica dónde se encuentra el servidor de base de datos. Este valor se utiliza para admitir los distintos formatos de fecha y hora de atributos.

El conector almacena siempre fecha y la fecha y hora en formato UTC. Debe especificarse poder convertir correctamente la fecha y horas, la zona horaria del servidor de base de datos y el formato que usa. El formato se expresa en formato .net.

Durante la exportación cada atributo de tiempo de fecha debe proporcionarse al conector en formato de hora UTC.

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**Configuración de contraseña**: el conector ofrece capacidades de sincronización de contraseña y es compatible con establecer y cambiar contraseña.

El conector proporciona dos métodos para permitir la sincronización de contraseña:

- **Procedimiento almacenado**: este método requiere dos procedimientos almacenados para admitir establecer & Cambiar contraseña. Escriba todos los parámetros para agregar y cambiar la operación de contraseña en **Establecer contraseña SP** y parámetros de **Cambiar contraseña SP** respectivamente como por ejemplo siguiente.
![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
- **Extensión de contraseña**: este método requiere extensión de contraseña DLL (debe proporcionar el nombre de archivo DLL de extensión que está implementando la interfaz de [IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx) ). Ensamblado de la extensión de contraseña debe situarse en la carpeta de extensión para que el conector puede cargar el archivo DLL en tiempo de ejecución.
![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

También debe habilitar la administración de la contraseña en la página **Configurar extensión** .
![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### <a name="configure-partitions-and-hierarchies"></a>Configurar las particiones y jerarquías
En la página particiones y jerarquías, seleccione todos los tipos de objeto. Cada tipo de objeto es su propia partición.

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

También puede reemplazar los valores definidos en la página **conectividad** o **Parámetros globales** .

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### <a name="configure-anchors"></a>Configurar los delimitadores
Esta página es de solo lectura puesto que ya se ha definido el delimitador. El atributo de anclaje seleccionados se anexa siempre con el tipo de objeto de garantizar que permanezcan único en tipos de objeto.

![delimitadores](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## <a name="configure-run-step-parameter"></a>Configurar parámetros de paso de ejecución
Estos pasos se configuran en los perfiles de ejecución en el conector. Estas configuraciones de hacen el trabajo real de importación y exportación de datos.

### <a name="full-and-delta-import"></a>Completa y Delta de importación
Genérico soporte de conector SQL completa e importar Delta con estos métodos:

- Tabla
- Vista
- Procedimiento almacenado
- Consulta SQL

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**Tabla o vista**  
Para importar los atributos con varios valores de un objeto, debe proporcionar el nombre de tabla o vista separados por comas en **vistas de la tabla nombre de varios valores** y las condiciones de combinación correspondientes en la **condición de combinación** con la tabla principal.

Ejemplo: Que desea importar el objeto de empleado y todos sus atributos con varios valores. Existen dos tablas, con el nombre de empleado (tabla principal) y departamento (multivalor).
Haga lo siguiente:

- Tipo de **empleado** en la **Tabla, vista o SP**.
- Escriba departamento en **vistas de la tabla nombre de varios valores**.
- Escriba la condición de combinación entre empleados & departamento en la **Condición de combinación**, por ejemplo `Employee.DEPTID=Department.DepartmentID`.
![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**Procedimientos almacenados**  
![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

- Si tiene la cantidad de datos, se recomienda implementar la paginación con los procedimientos almacenados.
- Para que el procedimiento almacenado admitir la paginación, debe proporcionar el índice de inicio y finalización. Vea: [eficazmente paginar a través de grandes cantidades de datos](https://msdn.microsoft.com/library/bb445504.aspx).
- @StartIndexy @EndIndex se reemplazan en tiempo de ejecución con el valor de tamaño de página correspondiente configurado en el **Paso de configurar** página. Por ejemplo, el cuando el conector recupera la primera página y el tamaño de página se establece en situación 500, @StartIndex sería 1 y @EndIndex 500. Estos valores aumentan al conector recupera las páginas siguientes y cambie la @StartIndex & @EndIndex valor.
- Para ejecutar el procedimiento almacenado con parámetros, proporcionar los parámetros de `[Name]:[Direction]:[Value]` formato. Escriba cada parámetro en una línea independiente (utilizar Ctrl + Entrar para obtener una nueva línea).
- Conector SQL genérico también admite la operación de importación de servidores vinculados en Microsoft SQL Server. Si se debe recuperar información de una tabla en el servidor vinculado, debe proporcionarse tabla con el formato:`[ServerName].[Database].[Schema].[TableName]`
- Conector de SQL genérico admite sólo los objetos que tienen estructura similar (tanto alias nombre y tipo de datos) entre ejecutar los pasos de detección de esquemas y la información. Si el objeto seleccionado de esquema y la información proporcionada en el paso ejecutar es diferente, conector SQL es no admite este tipo de casos.

**Consulta SQL**  
![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

- No admitidas las consultas de conjunto de resultados de varias.
- Consulta SQL admite la paginación y proporcionar inicio índice y el índice de finalización como una variable para admitir la paginación.

### <a name="delta-import"></a>Importación de Delta
![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

Configuración de importación de delta requiere una configuración más en comparación con la importación completa.

- Si elige el enfoque desencadenador o una instantánea para realizar el seguimiento de cambios delta, a continuación, proporcionar la base de datos de tabla Historial o una instantánea en el cuadro **historial de tabla o nombre de la base de datos de instantánea** .
- También debe proporcionar la condición de combinación entre las tablas de historial y primario, por ejemplo`Employee.ID=History.EmployeeID`
- Para realizar un seguimiento de la transacción en la tabla principal de la tabla de historial, debe proporcionar el nombre de la columna que contiene la información de la operación (agregar, actualizar o eliminar).
- Si elige la marca de agua para realizar el seguimiento de cambios delta, a continuación, proporcione el nombre de columna que contiene la información de la operación en **Nombre de columna de marca de agua**.
- La columna **Cambiar tipo atributo** es necesaria para el tipo de cambio. Esta columna asigna un cambio que tiene lugar en la tabla principal o varios valores en un tipo de cambio en la vista de delta. Esta columna puede contener el tipo de cambio Modify_Attribute de cambio de nivel de atributo o un agregar, modificar o eliminar cambiar tipo para un tipo de cambio de nivel de objeto. Si se trata de algo que no sea el valor predeterminado agregar, modificar o eliminar, a continuación, se pueden definir esos valores con esta opción.

### <a name="export"></a>Exportar
![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

Conector de SQL genérico admiten exportación con cuatro métodos admitidos como:

- Tabla
- Vista
- Procedimiento almacenado
- Consulta SQL

**Tabla o vista**  
Si elige la opción de la tabla o vista, el conector genera las consultas correspondientes para realizar la exportación.

**Procedimientos almacenados**  
![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

Si elige la opción de procedimiento almacenado, exportar requiere tres procedimientos almacenados diferentes para realizar operaciones de actualización, insertar o eliminar.

- **Agregar nombre de SP**: este SP se ejecuta si cualquier objeto llegue a conector de inserción en la tabla correspondiente.
- **Actualizar el nombre de SP**: este SP se ejecuta si cualquier objeto llegue a conector para actualizar en la tabla correspondiente.
- **Eliminar el nombre de SP**: este SP se ejecuta si cualquier objeto llegue a conector para su eliminación en la tabla correspondiente.
- Atributo seleccionado en el esquema que se utiliza como un valor de parámetro al procedimiento almacenado. Por ejemplo, `EmployeeName: INPUT: @EmployeeName` (EmployeeName está seleccionada en el esquema de conector y el conector reemplaza el valor correspondiente al realizar la exportación)
- Para ejecutar el procedimiento almacenado con parámetros, proporcionar parámetros en `[Name]:[Direction]:[Value]` formato. Escriba cada parámetro en una línea independiente (utilizar Ctrl + Entrar para obtener una nueva línea).

**Consulta SQL**  
![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

Si elige la opción de consulta SQL, exportar requiere tres consultas para realizar operaciones de actualización, insertar o eliminar.

- **Insertar consulta**: esta consulta se ejecuta si cualquier objeto llegue a conector de inserción en la tabla correspondiente.
- **Consulta de actualización**: esta consulta se ejecuta si cualquier objeto llegue a conector para actualizar en la tabla correspondiente.
- **Consulta de eliminación**: esta consulta se ejecuta si cualquier objeto llegue a conector para su eliminación en la tabla correspondiente.
- Atributo seleccionado en el esquema que se utiliza como un valor de parámetro a la consulta, por ejemplo`Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## <a name="troubleshooting"></a>Solución de problemas

-   Para obtener información acerca de cómo habilitar el registro solucionar el conector, vea [cómo habilitar el seguimiento de ETW para los conectores](http://go.microsoft.com/fwlink/?LinkId=335731).
