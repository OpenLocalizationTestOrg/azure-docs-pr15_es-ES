<properties
   pageTitle="Almacén de datos SQL Azure (Visual Studio) de la consulta | Microsoft Azure"
   description="Almacén de datos SQL de consulta con Visual Studio."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="sonyama;barbkess"/>

# <a name="query-azure-sql-data-warehouse-visual-studio"></a>Almacén de datos SQL Azure de consulta (Visual Studio)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Aprendizaje del equipo de Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Use Visual Studio para consulta de almacén de datos de SQL Azure en tan solo unos minutos. Este método usa la extensión de SQL Server Data Tools (SSDT) en Visual Studio. 

## <a name="prerequisites"></a>Requisitos previos

Para usar este tutorial, necesita:

+ Un almacén de datos SQL existente. Para crear uno, consulte [crear un almacén de datos de SQL][].
+ SSDT para Visual Studio. Si tiene Visual Studio, probablemente ya tiene este. Para obtener instrucciones de instalación y opciones, vea [instalar Visual Studio y SSDT][].
+ El nombre completo del servidor SQL. Para encontrar este, vea [conectarse al almacén de datos de SQL][].

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Conecte el almacén de datos SQL

1. Abra 2013 de Visual Studio o de 2015.
2. Abra el Explorador de objetos SQL Server. Para ello, seleccione **vista** > **Explorador de objetos de SQL Server**.

    ![Explorador de objetos SQL Server][1]

3. Haga clic en el icono **Agregar SQL Server** .

    ![Agregar SQL Server][2]

4. Rellene los campos en la ventana conectar al servidor.

    ![Conectarse al servidor][3]

    - **Nombre del servidor**. Escriba el **nombre del servidor** identificado previamente.
    - **Autenticación**. Seleccione **autenticación de SQL Server** o **la autenticación integrada de Active Directory**.
    - **Nombre de usuario** y **contraseña**. Si se ha seleccionado la autenticación de SQL Server encima, escriba el nombre de usuario y contraseña.
    - Haga clic en **Conectar**.

5. Para explorar, expanda el servidor de SQL Azure. Puede ver las bases de datos asociados con el servidor. Expandir AdventureWorksDW para ver las tablas de la base de datos de ejemplo.

    ![Explorar AdventureWorksDW][4]

## <a name="2-run-a-sample-query"></a>2. ejecutar una consulta de ejemplo

Ahora que se estableció una conexión a la base de datos, vamos a escribir una consulta.

1. Haga clic en la base de datos en el Explorador de objetos de SQL Server.

2. Seleccione **nueva consulta**. Se abrirá una nueva ventana de consulta.

    ![Nueva consulta][5]

3. Copie esta consulta TSQL en la ventana de consulta:

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. Ejecutar la consulta. Para ello, haga clic en la flecha verde o usar el siguiente método abreviado: `CTRL` + `SHIFT` + `E`.

    ![Ejecutar consulta][6]

5. Ver los resultados de la consulta. En este ejemplo, la tabla FactInternetSales tiene 60398 filas.

    ![Resultados de la consulta][7]

## <a name="next-steps"></a>Pasos siguientes

Ahora que puede conectarse y la consulta, intente [visualizar los datos con PowerBI][].

Para configurar el entorno para la autenticación de Azure Active Directory, vea [autenticar al almacén de datos de SQL][].

<!--Arcticles-->
[Conectarse al almacén de datos SQL]: sql-data-warehouse-connect-overview.md
[Crear un almacén de datos SQL]: sql-data-warehouse-get-started-provision.md
[Instalación de SSDT y Visual Studio]: sql-data-warehouse-install-visual-studio.md
[Autenticar al almacén de datos SQL]: sql-data-warehouse-authentication.md
[visualizar los datos con PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png
