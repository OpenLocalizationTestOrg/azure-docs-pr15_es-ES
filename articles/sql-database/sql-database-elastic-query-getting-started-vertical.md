<properties
    pageTitle="Introducción a las consultas de base de datos cruzados (partición vertical) | Microsoft Azure"   
    description="cómo usar la consulta de base de datos elástico con bases de datos divididas verticalmente"
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="torsteng" />

# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Introducción a las consultas de base de datos cruzados (partición vertical) (vista preliminar)

Consulta de base de datos elásticas (preview) para la base de datos de SQL Azure le permite ejecutar consultas SQL T que abarcan varias bases de datos con un punto de conexión. En este tema se aplica a [Dividir verticalmente bases de datos](sql-database-elastic-query-vertical-partitioning.md).  

Cuando finalice, podrá: Obtenga información sobre cómo configurar y usar una base de datos de SQL Azure para realizar consultas que abarcan varias bases de datos relacionados. 

Para obtener más información acerca de la característica de consulta elásticos de la base de datos, vea [información general de consulta de base de datos de SQL Azure elásticos de la base de datos](sql-database-elastic-query-overview.md). 

## <a name="create-the-sample-databases"></a>Crear las bases de datos de ejemplo

Inicialmente, es necesario crear dos bases de datos, **clientes** y **pedidos**, ya sea en el mismos o diferentes servidores lógicos.   

Ejecute las consultas siguientes en la base de datos de **pedidos** para crear la tabla **OrderInformation** y los datos de ejemplo de entrada. 

    CREATE TABLE [dbo].[OrderInformation]( 
        [OrderID] [int] NOT NULL, 
        [CustomerID] [int] NOT NULL 
        ) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

Ahora, ejecutar después de la consulta en la base de datos de **los clientes** para crear la tabla **CustomerInformation** y los datos de ejemplo de entrada. 

    CREATE TABLE [dbo].[CustomerInformation]( 
        [CustomerID] [int] NOT NULL, 
        [CustomerName] [varchar](50) NULL, 
        [Company] [varchar](50) NULL 
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
    ) 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## <a name="create-database-objects"></a>Crear objetos de base de datos
### <a name="database-scoped-master-key-and-credentials"></a>Ámbito de la base de datos de clave principal y las credenciales

1. Abra SQL Server Management Studio o SQL Server Data Tools en Visual Studio.
2. Conectarse a la base de datos de pedidos y ejecute los siguientes comandos T SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
        WITH IDENTITY = '<username>', 
        SECRET = '<password>';  

    El "usuario" y "contraseña" deben ser el nombre de usuario y la contraseña que utiliza para iniciar sesión en la base de datos de clientes.
    Autenticación con Azure Active Directory con consultas elásticas no es compatible actualmente.

### <a name="external-data-sources"></a>Orígenes de datos externos
Para crear un origen de datos externo, ejecute el comando siguiente en la base de datos de pedidos: 

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
        (TYPE = RDBMS, 
        LOCATION = '<server_name>.database.windows.net', 
        DATABASE_NAME = 'Customers', 
        CREDENTIAL = ElasticDBQueryCred, 
    ) ;

### <a name="external-tables"></a>Tablas externas
Crear una tabla externa en la base de datos de pedidos, que coincide con la definición de la tabla CustomerInformation:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
    ( [CustomerID] [int] NOT NULL, 
      [CustomerName] [varchar](50) NOT NULL, 
      [Company] [varchar](50) NOT NULL) 
    WITH 
    ( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Ejecutar una consulta de SQL T elásticos de la base de datos de ejemplo

Una vez que ha definido el origen de datos externo y las tablas externas ahora puede utilizar T SQL para consultar las tablas externas. Ejecute esta consulta en la base de datos de pedidos: 

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
    FROM OrderInformation 
    INNER JOIN CustomerInformation 
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## <a name="cost"></a>Costo

Actualmente, la característica de consulta de base de datos elástico se incluye en el coste de la base de datos de SQL Azure.  

Para obtener información sobre precios vea [Precios de base de datos de SQL](/pricing/details/sql-database). 


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->

<!--anchors-->
