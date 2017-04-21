<properties
    pageTitle="Conectarse a la base de datos SQL con una consulta de C# | Microsoft Azure"
    description="Escribir un programa en C# de la consulta y conectarse a la base de datos SQL. Información sobre las direcciones IP, las cadenas de conexión, inicio de sesión seguro y libre de Visual Studio."
    services="sql-database"
    keywords="consulta de base de datos de c#, c# consulta, conectarse a la base de datos SQL C#"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="stevestein"/>



# <a name="connect-to-a-sql-database-with-visual-studio"></a>Conectarse a una base de datos SQL con Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Obtenga información sobre cómo conectarse a una base de datos de SQL Azure en Visual Studio. 

## <a name="prerequisites"></a>Requisitos previos


Para conectarse a una base de datos SQL mediante Visual Studio, necesita lo siguiente: 


- Base de datos SQL para conectarse a. En este artículo, se utiliza la base de datos de ejemplo **AdventureWorks** . Para obtener la base de datos de ejemplo AdventureWorks, vea [crear la base de datos de demostración](sql-database-get-started.md).


- Visual Studio 2013 actualización 4 (o posterior). Microsoft proporciona ahora la Comunidad de Visual Studio para *gratuita*.
 - [Comunidad Visual Studio, descargar](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [Más opciones para liberar Visual Studio](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)




## <a name="open-visual-studio-from-the-azure-portal"></a>Abra Visual Studio desde el portal de Azure


1. Inicie sesión en el [portal de Azure](https://portal.azure.com/).

2. Haga clic en **Más servicios** > **bases de datos SQL**
3. Abra el módulo de base de datos **AdventureWorks** localizar y haciendo clic en la base de datos de *AdventureWorks* .

6. Haga clic en el botón de **Herramientas** en la parte superior del módulo de base de datos:

    ![Nueva consulta. Conectarse al servidor de base de datos SQL: SQL Server Management Studio](./media/sql-database-connect-query/tools.png)

7. Haga clic en **Abrir en Visual Studio** (si necesita Visual Studio, haga clic en el vínculo de descarga):

    ![Nueva consulta. Conectarse al servidor de base de datos SQL: SQL Server Management Studio](./media/sql-database-connect-query/open-in-vs.png)


8. Visual Studio se abre con la ventana de **Conectar con el servidor** ya está establecida para conectarse al servidor y base de datos seleccionada en el portal.  (Haga clic en **Opciones** para comprobar que la conexión se establece en la base de datos correcto). Escriba la contraseña de administrador del servidor y haga clic en **Conectar**.


    ![Nueva consulta. Conectarse al servidor de base de datos SQL: SQL Server Management Studio](./media/sql-database-connect-query/connect.png)


8. Si no tiene establecer una regla de firewall para la dirección IP de su equipo, recibe un mensaje *no se puede conectar* a continuación. Para crear una regla de firewall, vea [Configurar una regla de firewall de nivel de servidor de base de datos de SQL Azure](sql-database-configure-firewall-settings.md).


9. Después de conectarse correctamente, se abre la ventana del **Explorador de objetos de SQL Server** con una conexión a la base de datos.

    ![Nueva consulta. Conectarse al servidor de base de datos SQL: SQL Server Management Studio](./media/sql-database-connect-query/sql-server-object-explorer.png)


## <a name="run-a-sample-query"></a>Ejecutar una consulta de ejemplo

Ahora que estamos conectados a la base de datos, los pasos siguientes muestran cómo ejecutar una consulta simple:

2. Haga clic en la base de datos y, a continuación, seleccione **Nueva consulta**.

    ![Nueva consulta. Conectarse al servidor de base de datos SQL: SQL Server Management Studio](./media/sql-database-connect-query/new-query.png)

3. En la ventana de consulta, copie y pegue el código siguiente.

        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;

4. Haga clic en el botón **Ejecutar** para ejecutar la consulta:

    ![Correcto. Conectarse al servidor de base de datos SQL: SVisual Studio](./media/sql-database-connect-query/run-query.png)

## <a name="next-steps"></a>Pasos siguientes

- Abrir bases de datos SQL en Visual Studio usa las herramientas de datos de SQL Server. Para obtener más detalles, consulte [Las herramientas de datos de SQL Server](https://msdn.microsoft.com/library/hh272686.aspx).
- Para conectarse a una base de datos SQL con el código, vea [conectarse a la base de datos de SQL mediante .NET (C#)](sql-database-develop-dotnet-simple.md).



