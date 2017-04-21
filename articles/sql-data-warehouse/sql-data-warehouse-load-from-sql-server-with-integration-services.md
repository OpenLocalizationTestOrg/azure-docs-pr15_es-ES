<properties
   pageTitle="Cargar datos de SQL Server en almacén de datos de SQL Azure (SSIS) | Microsoft Azure"
   description="Muestra cómo crear un paquete de SQL Server Integration Services (SSIS) para mover los datos desde una amplia variedad de orígenes de datos al almacén de datos de SQL."
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
   ms.author="lodipalm;sonyama;barbkess"/>

# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>Cargar datos de SQL Server en almacén de datos de SQL Azure (SSIS)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)


Crear un paquete de SQL Server Integration Services (SSIS) para cargar los datos de SQL Server en el almacén de datos de SQL Azure. Si lo desea, puede reestructurar, transformar y limpiar los datos según pasa por el flujo de datos SSIS.

En este tutorial, podrá:

- Crear un nuevo proyecto de Integration Services en Visual Studio.
- Conectarse a orígenes de datos, incluidos SQL Server (como un origen) y el almacenamiento de datos de SQL (como destino).
- Diseñar un paquete SSIS que carga los datos del origen en el destino.
- Ejecutar el paquete de SSIS para cargar los datos.

Este tutorial utiliza SQL Server como origen de datos. Podría ejecutar SQL Server local o en una máquina virtual Azure.

## <a name="basic-concepts"></a>Conceptos básicos

El paquete es la unidad de trabajo de SSIS. Paquetes relacionados se agrupan en proyectos. Crear proyectos y paquetes de diseño en Visual Studio con las herramientas de datos de SQL Server. El proceso de diseño es un proceso visual en el que arrastra y colocar componentes del cuadro de herramientas a la superficie de diseño, conectarlos y establezca sus propiedades. Cuando termine el paquete, puede implementarlo opcionalmente en SQL Server para administración completa, supervisión y seguridad.

## <a name="options-for-loading-data-with-ssis"></a>Opciones para cargar los datos de SSIS

SQL Server Integration Services (SSIS) es un conjunto de herramientas flexible que le ofrece diversas opciones para conectarse a y cargar datos en el almacenamiento de datos de SQL.

1. Usar un destino de red ADO para conectarse al almacén de datos de SQL. Este tutorial utiliza un destino de red ADO porque tiene la menor cantidad opciones de configuración.
2. Usar un destino de OLE DB para conectarse al almacén de datos de SQL. Esta opción puede proporcionar ligeramente mejor rendimiento que el destino de red ADO.
3. Utilice la tarea de cargar blobs de Azure para organizar los datos en el almacenamiento de blobs de Windows Azure. A continuación, utilice la tarea de ejecución de SQL de SSIS para iniciar una secuencia de comandos de Polybase que carga los datos en el almacén de datos de SQL. Esta opción proporciona el mejor rendimiento de las tres opciones que se muestran aquí. Para obtener la tarea cargar de blobs de Windows Azure, descargue el [Microsoft SQL Server 2016 Integration Services Feature Pack para Azure][]. Para obtener más información sobre Polybase, consulte la [Guía de PolyBase][].

## <a name="before-you-start"></a>Antes de empezar

Paso a través de este tutorial, necesita:

1. **SQL Server Integration Services (SSIS)**. SSIS es un componente de SQL Server y requiere una versión de evaluación o una versión con licencia de SQL Server. Para obtener una versión de evaluación de SQL Server 2016 Preview, consulte [Evaluaciones de SQL Server][].
2. **Visual Studio**. Para obtener la edición de comunidad gratuita de Visual Studio 2015, consulte la [Comunidad de Visual Studio][].
3. **SQL Server Data Tools para Visual Studio (SSDT)**. Para obtener SQL Server Data Tools para Visual Studio de 2015, vea [Descargar SQL Server Data Tools (SSDT)][].
4. **Datos de ejemplo**. Este tutorial usa datos de ejemplo que se almacenan en SQL Server en la base de datos de ejemplo AdventureWorks como origen de datos que debe cargarse en el almacén de datos de SQL. Para obtener la base de datos de ejemplo AdventureWorks, consulte [Bases de datos de ejemplo AdventureWorks 2014][].
5. **Permisos y almacenamiento de datos de SQL de una base de datos**. En este tutorial se conecta a una instancia de almacén de datos de SQL y carga los datos en él. Debe tener permisos para crear una tabla y cargar los datos.
6. **Una regla de firewall**. Debe crear una regla de firewall en el almacén de datos de SQL con la dirección IP del equipo local antes de cargar los datos al almacén de datos de SQL.

## <a name="step-1-create-a-new-integration-services-project"></a>Paso 1: Crear un nuevo proyecto de Integration Services

1. Inicie Visual Studio de 2015.
2. En el menú **archivo** , seleccione nuevo **| Proyecto**.
3. Desplácese hasta la **instalado | Plantillas | Inteligencia empresarial | Servicios de integración de** tipos de proyecto.
4. Seleccione el **proyecto de Integration Services**. Proporcionar los valores de **nombre** y una **ubicación**y, a continuación, seleccione **Aceptar**.

Visual Studio se abre y crea un nuevo proyecto de Integration Services (SSIS). A continuación, en Visual Studio se abre el diseñador para el paquete SSIS nuevo solo (paquete.dtsx) en el proyecto. Vea las siguientes áreas de la pantalla:

- A la izquierda, el **cuadro de herramientas** de componentes SSIS.
- En el centro, la superficie de diseño con varias fichas. Normalmente se utiliza al menos el **Flujo de Control** y las fichas de **Flujo de datos** .
- A la derecha, el **Explorador de soluciones** y los paneles de **Propiedades** .

    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>Paso 2: Crear el flujo de datos básico

1. Arrastre una tarea de flujo de datos desde el cuadro de herramientas al centro de la superficie de diseño (en la pestaña **Flujo de Control** ).

    ![][02]

2. Haga doble clic en la tarea de flujo de datos para cambiar a la pestaña flujo de datos.
3. En la lista de otros orígenes en el cuadro de herramientas, arrastre un origen ADO.NET a la superficie de diseño. Con el adaptador de origen todavía seleccionado, cambiar su nombre al **origen de SQL Server** en el panel **Propiedades** .
4. En la lista de destinos otros en el cuadro de herramientas, arrastre un destino de ADO.NET a la superficie de diseño en el origen de ADO.NET. Con el adaptador de destino todavía seleccionado, cambiar su nombre al **destino DW SQL** en el panel **Propiedades** .

    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>Paso 3: Configurar el adaptador de origen

1. Haga doble clic en el adaptador de origen para abrir el **Editor de código fuente de ADO.NET**.

    ![][03]

2. En la pestaña **Administrador de conexión** del **Editor de código fuente de ADO.NET**, haga clic en el botón **nuevo** junto a la lista del **Administrador de conexión ADO.NET** para abrir el cuadro de diálogo **Administrador de conexión de ADO.NET de configurar** y crear una configuración de conexión para la base de datos de SQL Server desde la que este tutorial carga los datos.

    ![][04]

3. En el cuadro de diálogo **Configurar el Administrador de conexión de ADO.NET** , haga clic en el botón **nuevo** para abrir el cuadro de diálogo **Administrador de conexión** y crear una nueva conexión de datos.

    ![][05]

4. En el cuadro de diálogo **Administrador de conexión** , realice lo siguiente.

    1. **Proveedor**, seleccione el proveedor de datos SqlClient.
    2. **Nombre del servidor**, escriba el nombre de SQL Server.
    3. En la sección **iniciar sesión el servidor** , seleccione o escriba la información de autenticación.
    4. En la sección **Conectar con una base de datos** , seleccione la base de datos de ejemplo AdventureWorks.
    5. Haga clic en **Probar conexión**.
    
        ![][06]
    
    6. En el cuadro de diálogo que notifica los resultados de la prueba de conexión, haga clic en **Aceptar** para volver al cuadro de diálogo **Administrador de conexión** .
    7. En el cuadro de diálogo **Administrador de conexiones** , haga clic en **Aceptar** para volver al cuadro de diálogo **Configurar el Administrador de conexión de ADO.NET** .
 
5. En el cuadro de diálogo **Configurar el Administrador de conexión de ADO.NET** , haga clic en **Aceptar** para volver al **Editor de código fuente de ADO.NET**.
6. En el **Editor de código fuente de ADO.NET**, en la lista **nombre de la tabla o la vista** , seleccione la tabla **Sales.SalesOrderDetail** .

    ![][07]

7. Haga clic en **vista previa** para ver las primeras 200 filas de datos en la tabla de origen en el cuadro de diálogo **Vista previa de resultados de consulta** .

    ![][08]

8. En el cuadro de diálogo **Vista previa de resultados de consulta** , haga clic en **Cerrar** para volver al **Editor de código fuente de ADO.NET**.
9. En el **Editor de código fuente de ADO.NET**, haga clic en **Aceptar** para terminar de configurar el origen de datos.

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>Paso 4: Conectar el adaptador de origen con el adaptador de destino

1. Seleccione el adaptador de origen en la superficie de diseño.
2. Haga clic en la flecha azul que abarca desde el adaptador de origen y arrástrelo hasta el editor de destino hasta que encaje en su lugar.

    ![][10]

    En un paquete SSIS típico, se utiliza un número de otros componentes del cuadro de herramientas de SSIS entre el origen y destino reestructurar, transformar y limpiar los datos según pasa por el flujo de datos SSIS. Para mantener este ejemplo tan sencillo como sea posible, estamos a conectarse al origen directamente en el destino.

## <a name="step-5-configure-the-destination-adapter"></a>Paso 5: Configurar el adaptador de destino

1. Haga doble clic en el adaptador de destino para abrir el **Editor de destino de ADO.NET**.

    ![][11]

2. En la pestaña **Administrador de conexión** del **Editor de destino de ADO.NET**, haga clic en el botón **nuevo** junto a la lista del **Administrador de conexiones** para abrir el cuadro de diálogo **Administrador de conexión de ADO.NET de configurar** y crear una configuración de conexión para la base de datos de almacén de datos de SQL Azure en la que este tutorial carga los datos.
3. En el cuadro de diálogo **Configurar el Administrador de conexión de ADO.NET** , haga clic en el botón **nuevo** para abrir el cuadro de diálogo **Administrador de conexión** y crear una nueva conexión de datos.
4. En el cuadro de diálogo **Administrador de conexión** , realice lo siguiente.
    1. **Proveedor**, seleccione el proveedor de datos SqlClient.
    2. **Nombre del servidor**, escriba el nombre de almacén de datos de SQL.
    3. En la sección **iniciar sesión el servidor** , seleccione **autenticación de usar SQL Server** y escriba la información de autenticación.
    4. En la sección **Conectar con una base de datos** , seleccione una base de datos del almacén de datos de SQL.
    5. Haga clic en **Probar conexión**.
    6. En el cuadro de diálogo que notifica los resultados de la prueba de conexión, haga clic en **Aceptar** para volver al cuadro de diálogo **Administrador de conexión** .
    7. En el cuadro de diálogo **Administrador de conexiones** , haga clic en **Aceptar** para volver al cuadro de diálogo **Configurar el Administrador de conexión de ADO.NET** .
5. En el cuadro de diálogo **Configurar el Administrador de conexión de ADO.NET** , haga clic en **Aceptar** para volver al **Editor de destino de ADO.NET**.
6. En el **Editor de destino de ADO.NET**, haga clic en **nuevo** junto a la lista de **usar una tabla o vista** para abrir el cuadro de diálogo **Crear tabla** para crear una nueva tabla de destino con una lista de columnas que coincida con la tabla de origen.

    ![][12a]

7. En el cuadro de diálogo **Crear tabla** , hacer lo siguiente.

    1. Cambiar el nombre de la tabla de destino a **SalesOrderDetail**.
    2. Quitar la columna **rowguid** . El tipo de datos **uniqueidentifier** no es compatible en el almacén de datos de SQL.
    3. Cambiar el tipo de datos de la columna **LineTotal** a **dinero**. El tipo de datos **decimal** no es compatible en el almacén de datos de SQL. Para obtener información acerca de los tipos de datos compatibles, vea [Crear tabla (almacén de datos de SQL Azure, almacenamiento de datos paralelo)][].
    
        ![][12b]
    
    4. Haga clic en **Aceptar** para crear la tabla y volver al **Editor de destino de ADO.NET**.

8. En el **Editor de destino de ADO.NET**, seleccione la ficha **asignaciones** para ver cómo se asignan columnas del origen de para las columnas de destino.

    ![][13]

9. Haga clic en **Aceptar** para terminar de configurar el origen de datos.

## <a name="step-6-run-the-package-to-load-the-data"></a>Paso 6: Ejecutar el paquete para cargar los datos

Ejecutar el paquete haciendo clic en el botón **Inicio** en la barra de herramientas o seleccionando una de las opciones de **Ejecutar** en el menú **Depurar** .

A medida que comienza el paquete ejecutar, consulte amarillas ruedas girando para indicar la actividad, así como el número de filas que se procesan hasta ese momento.

![][14]

Cuando haya terminado de ejecutarse el paquete, consulte las marcas de verificación verdes para indicar el éxito, así como el número total de filas de datos que se cargan desde el origen al destino.

![][15]

¡Felicidades! Ha utilizado correctamente SQL Server Integration Services para cargar los datos en el almacén de datos de SQL Azure.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el flujo de datos SSIS. Empiece aquí: [Flujo de datos][].
- Obtenga información sobre cómo depurar y solucionar problemas de la derecha de paquetes en el entorno de diseño. Empiece aquí: [Herramientas de solución de problemas para el desarrollo de paquete][].
- Aprenda a implementar sus proyectos SSIS y paquetes al servidor de servicios de integración o a otra ubicación de almacenamiento. Empiece aquí: [implementación de proyectos y paquetes][].

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[Guía de PolyBase]: https://msdn.microsoft.com/library/mt143171.aspx
[Descargar SQL Server Data Tools (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[Crear tabla (almacén de datos SQL Azure, almacén de datos en paralelo)]: https://msdn.microsoft.com/library/mt203953.aspx
[Flujo de datos]: https://msdn.microsoft.com/library/ms140080.aspx
[Solución de problemas de herramientas de desarrollo de paquetes]: https://msdn.microsoft.com/library/ms137625.aspx
[Implementación de proyectos y paquetes]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Servicios de integración de Microsoft SQL Server 2016 Feature Pack de Azure]: http://go.microsoft.com/fwlink/?LinkID=626967
[Evaluaciones de SQL Server]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Comunidad de Visual Studio]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[Bases de datos de ejemplo AdventureWorks de 2014]: https://msftdbprodsamples.codeplex.com/releases/view/125550
