<properties
    pageTitle="Características de JSON de base de datos de SQL Azure | Microsoft Azure"
    description="Base de datos de SQL Azure le permite análisis, consulta y formato de datos en notación de notación de objetos JavaScript (JSON)."
    services="sql-database"
    documentationCenter=""
    authors="jovanpop-msft"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/17/2016"
    ms.author="jovanpop"
   ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Introducción a las características de JSON de base de datos de SQL Azure

Base de datos de SQL Azure le permite analizar y consultar datos representados en formato de notación de objetos JavaScript [(JSON)](http://www.json.org/) para exportar los datos relacionales como texto JSON.

JSON es un formato de datos más conocidos utilizado para el intercambio de datos en web moderna y aplicaciones móviles. JSON también se usa para almacenar datos semiestructurados en archivos de registro o bases de datos NoSQL como [DocumentDB de Azure](https://azure.microsoft.com/services/documentdb/). Muchos servicios web REST devuelven resultados con formato de texto JSON o aceptan los datos con formato JSON. Azure más servicios como [Búsqueda de Azure](https://azure.microsoft.com/services/search/), [Almacenamiento Azure](https://azure.microsoft.com/services/storage/)y [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) tienen extremos resto que devuelven o consuman JSON.

Base de datos de SQL Azure le permite trabajar fácilmente con datos JSON e integrar la base de datos con servicios modernos.

## <a name="overview"></a>Información general

Base de datos de SQL Azure proporciona las siguientes funciones para trabajar con datos JSON:

![Funciones JSON](./media/sql-database-json-features/image_1.png)

Si tiene texto JSON, puede extraer datos de JSON o comprobar que es el formato correcto JSON empleando las funciones integradas [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)y [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). La función [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) le permite actualizar el valor de texto JSON. Para consultar y análisis más avanzadas, función [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) puede transformar una matriz de objetos JSON en un conjunto de filas. Cualquier consulta SQL se puede ejecutar en el conjunto de resultados devuelto. Por último, hay una cláusula [JSON para](https://msdn.microsoft.com/library/dn921882.aspx) que le permite aplicar formato a los datos almacenados en las tablas relacionales como texto JSON.

## <a name="formatting-relational-data-in-json-format"></a>Formato de datos relacionales en formato JSON
Si tiene un servicio web que tiene datos de la base de datos de capas y proporcionan una respuesta de JSON dar formato o formato JSON marcos de JavaScript o bibliotecas que aceptan datos del cliente, puede dar formato al contenido de la base de datos como JSON directamente en una consulta SQL. Ya no tiene que escribir el código de la aplicación que da formato a los resultados de la base de datos de Azure SQL como JSON o incluir alguna biblioteca de serialización JSON para convertir los resultados de la consulta de tabla y, a continuación, serializar objetos en formato JSON. En su lugar, puede usar la cláusula JSON para aplicar el formato de resultados de la consulta SQL como JSON en base de datos de SQL Azure y usar directamente en la aplicación.

En el ejemplo siguiente, las filas de la tabla Sales.Customer tienen formato JSON mediante la cláusula FOR JSON:

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

La cláusula de JSON PATH da formato a los resultados de la consulta como texto JSON. Nombres de columna se utilizan como claves, mientras se generan los valores de celda como valores JSON:

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

El conjunto de resultados con el formato de una matriz JSON, donde cada fila tiene el formato como un objeto JSON independiente.

Ruta de acceso indica que puede personalizar el formato de salida de su resultado JSON utilizando la notación de punto en alias de columna. La siguiente consulta cambia el nombre de la clave "NombreCliente" en el formato JSON y coloca los números de teléfono y de fax en el objeto de subcarpetas "Contacto":

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

El resultado de esta consulta es similar a esta:

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

En este ejemplo se devuelve un único objeto JSON en lugar de una matriz especificando la opción [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) . Puede usar esta opción si sabe que va a devolver un único objeto como resultado de consulta.

El valor principal de la cláusula FOR JSON es que le permite devolver datos jerárquicos complejos de la base de datos con formato de objetos JSON anidados o matrices. En el ejemplo siguiente se muestra cómo incluir pedidos que pertenecen al cliente como una matriz anidada de pedidos:

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

En lugar de enviar separar consultas para obtener los datos del cliente y, a continuación, para obtener una lista de pedidos relacionados, puede obtener todos los datos necesarios con una única consulta, tal como se muestra en los resultados de ejemplo siguientes:

```
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
]
}
```

## <a name="working-with-json-data"></a>Trabajar con datos JSON

Si no tiene datos estructurados estrictamente, si tiene objetos secundarios complejos, matrices o datos jerárquicos o evolucionan las estructuras de datos, el formato JSON puede ayudarle para representar cualquier estructura de datos complejos.

JSON es un formato de texto que se pueden usar como cualquier otro tipo de cadena en la base de datos de SQL Azure. Puede enviar o almacenar datos JSON como un NVARCHAR estándar:

```
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

Los datos JSON usados en este ejemplo se representan mediante el tipo de nvarchar (max). JSON se puede insertar en esta tabla o proporcionada como argumento del procedimiento almacenado mediante la sintaxis de Transact-SQL estándar tal como se muestra en el ejemplo siguiente:

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Cualquier idioma del cliente o la biblioteca que trabaja con datos de cadena en la base de datos de SQL Azure también funcionan con datos JSON. JSON puede almacenarse en todas las tablas que admite el tipo NVARCHAR, como una tabla de memoria optimizada o una versión del sistema. JSON no presente cualquier restricción en el código de cliente o en la capa de base de datos.

## <a name="querying-json-data"></a>Consultar datos JSON

Si tiene datos con formato JSON almacenado en las tablas de SQL Azure, JSON funciones permiten utilizar estos datos en cualquier consulta SQL.

Funciones JSON que están disponibles en permitir de base de datos de SQL Azure se tratan los datos con formato JSON como cualquier otro tipo de datos SQL. Fácilmente puede extraer los valores de texto JSON y utilizar datos JSON en cualquier consulta:

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

La función JSON_VALUE extrae un valor de texto JSON almacenado en la columna de datos. Esta función utiliza una ruta de acceso de JavaScript para hacer referencia a un valor en texto JSON para extraer. El valor extraído puede utilizarse en cualquier parte de la consulta SQL.

La función JSON_QUERY es similar a JSON_VALUE. A diferencia de JSON_VALUE, esta función extrae objeto subcarpetas complejo como matrices o los objetos que se colocan en texto JSON.

La función JSON_MODIFY le permite especificar la ruta de acceso del valor en el texto JSON que debe actualizarse, así como un nuevo valor que sobrescribirá antiguo. De esta forma que puede actualizar fácilmente texto JSON sin volver a analizar la estructura completa.

Puesto que JSON se almacena en un texto estándar, no hay garantiza que los valores almacenados en las columnas de texto son el formato correcto. Puede comprobar que es el formato correcto texto almacenado en la columna JSON con restricciones de comprobación de base de datos de SQL Azure estándares y la función ISJSON:

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Si el formato de la entrada de texto es correcto JSON, la función ISJSON devuelve el valor 1. En cada insertar o actualización de la columna JSON, esta restricción comprobar que el nuevo valor de texto no es JSON con formato incorrecto.

## <a name="transforming-json-into-tabular-format"></a>Transformar JSON en formato tabular

Base de datos de SQL Azure también le permite transformar colecciones JSON en datos tabulares de JSON de formato y la carga o la consulta.

OPENJSON es una función con valores de tabla que analiza texto JSON, localiza una matriz de objetos JSON, itera los elementos de la matriz y devuelve una fila en el resultado de salida para cada elemento de la matriz.

![JSON tabular](./media/sql-database-json-features/image_2.png)

En el ejemplo anterior, podemos especificar dónde desea ubicar la matriz JSON que se debe abrir (en $. Ruta de acceso de pedidos), qué columnas se devuelve como resultado y dónde encontrar los valores JSON que se devolverán como celdas.

Nos podemos transformar una matriz JSON en el @orders variable en un conjunto de filas, analizar este conjunto de resultados, o insertar filas en una tabla estándar:

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
La colección de pedidos con formato como una matriz JSON y suministrada como un parámetro para el procedimiento almacenado se puede analizar e insertado en la tabla Orders.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo integrar JSON en la aplicación, consulte estos recursos:

- [TechNet Blog](https://blogs.technet.microsoft.com/dataplatforminsider/2016/01/05/json-in-sql-server-2016-part-1-of-4/)
- [Documentación de MSDN](https://msdn.microsoft.com/library/dn921897.aspx)
- [Canal de vídeo de 9](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Para obtener información sobre varios escenarios para integrar JSON en su aplicación, vea las demostraciones en este [canal 9 vídeo](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) o encontrar un escenario que coincida con el caso de uso de [entradas de Blog de JSON](http://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).
