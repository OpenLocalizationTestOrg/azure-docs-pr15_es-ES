<properties
    pageTitle="Conectarse a la base de datos SQL con Python | Microsoft Azure"
    description="Presenta una muestra de código de Python que puede usar para conectarse a la base de datos de SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="meet-bhagdev"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="meetb"/>


# <a name="connect-to-sql-database-by-using-python"></a>Conectarse a la base de datos SQL con Python


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


Este tema muestra cómo conectarse y consultar una base de datos de SQL Azure con Python. Puede ejecutar este ejemplo de plataformas Windows, Ubuntu Linux o Mac.


## <a name="step-1-create-a-sql-database"></a>Paso 1: Crear una base de datos SQL

Consulte la [página de introducción](sql-database-get-started.md) para obtener información sobre cómo crear una base de datos de ejemplo.  Es importante que siga la guía para crear una **plantilla de base de datos de AdventureWorks**. Los ejemplos que se muestra a continuación solo funcionan con el **esquema de AdventureWorks**. Una vez creada la creación de la base de datos seguro habilitar el acceso a su dirección IP habilitando las reglas de firewall, como se describe en la [página de introducción](sql-database-get-started.md)

## <a name="step-2-configure-development-environment"></a>Paso 2: Configurar el entorno de desarrollo

### <a name="mac-os"></a>**Mac OS**   
### <a name="install-the-required-modules"></a>Instalar los módulos necesarios
Abra su terminal e instalar

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew install FreeTDS
    sudo -H pip install pymssql=2.1.1

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Abra su terminal y navegue hasta un directorio donde tiene previsto crear su python script. Escriba los comandos siguientes para instalar **FreeTDS** y **pymssql**. pymssql utiliza FreeTDS para conectarse a bases de datos de SQL.

    sudo apt-get --assume-yes update
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    sudo apt-get --assume-yes install python-dev python-pip
    sudo pip install pymssql=2.1.1
    
### <a name="windows"></a>**Windows**

Instale pymssql desde [**aquí**](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql). 

Asegúrese de que elegir el archivo whl correcto. Por ejemplo: elija si está utilizando Python 2.7 en un equipo de 64 bits: pymssql‑2.1.1‑cp27‑none‑win_amd64.whl. Una vez que descargue el archivo .whl colocarla en la carpeta C:/Python27.

Instalar ahora el controlador de pymssql con puntos de la línea de comandos. CD en C:/Python27 y ejecutar el siguiente
    
    pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

Encontrará instrucciones para habilitar el uso de puntos [aquí](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)

## <a name="step-3-run-sample-code"></a>Paso 3: Ejecutar el código de ejemplo

Cree un archivo llamado **sql_sample.py** y pegue el código siguiente dentro de ella. Se puede ejecutar desde la línea de comandos mediante:
    
    python sql_sample.py

### <a name="connect-to-your-sql-database"></a>Conectarse a la base de datos SQL

La función [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) se usa para conectarse a la base de datos de SQL.

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


### <a name="execute-an-sql-select-statement"></a>Ejecutar una instrucción SQL SELECT

La función [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) puede usarse para recuperar un conjunto de resultados de una consulta en base de datos de SQL. Esta función acepta prácticamente cualquier consulta y devuelve que un conjunto de resultados que se puede iteración con el uso de [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).


    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])   
        row = cursor.fetchone()


### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>Insertar una fila, pasar parámetros y recuperar la clave principal generada

En la base de datos SQL la propiedad de [identidad](https://msdn.microsoft.com/library/ms186775.aspx) y el objeto de [secuencia](https://msdn.microsoft.com/library/ff878058.aspx) pueden usarse para generar automáticamente valores de [clave principal](https://msdn.microsoft.com/library/ms179610.aspx) . 


    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
    row = cursor.fetchone()
    while row:
        print "Inserted Product ID : " +str(row[0])
        row = cursor.fetchone()


### <a name="transactions"></a>Transacciones


Este ejemplo de código muestra el uso de transacciones en las:

* Comenzar una transacción
* Insertar una fila de datos
* Deshacer la transacción para deshacer la inserción 

Pegue el código siguiente dentro de sql_sample.py.
    
    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("BEGIN TRANSACTION")
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
    cnxn.rollback()

## <a name="next-steps"></a>Pasos siguientes

* Revise la [información general sobre el desarrollo de base de datos SQL](sql-database-develop-overview.md)
* Obtener más información sobre el [Controlador de Python de Microsoft para SQL Server](https://msdn.microsoft.com/library/mt652092.aspx)
* Visite el [Centro para desarrolladores de Python](/develop/python/).

## <a name="additional-resources"></a>Recursos adicionales 

* [Patrones de diseño para las aplicaciones de múltiples arrendatarios SaaS con base de datos SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explore las [capacidades de base de datos de SQL](https://azure.microsoft.com/services/sql-database/)
