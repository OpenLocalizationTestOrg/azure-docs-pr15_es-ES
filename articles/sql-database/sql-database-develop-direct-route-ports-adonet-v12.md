<properties 
    pageTitle="Puertos más allá de 1433 para la base de datos SQL | Microsoft Azure"
    description="Conexiones de cliente de ADO.NET a V12 de base de datos de SQL Azure a veces utilizar al servidor proxy e interactúan directamente con la base de datos. Se convierten en puertos distintos 1433 importantes."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor="" />


<tags 
    ms.service="sql-database" 
    ms.workload="drivers"
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016"
    ms.author="annemill"/>


# <a name="ports-beyond-1433-for-adonet-45-and-sql-database-v12"></a>Puertos más allá de 1433 para ADO.NET 4.5 y V12 de base de datos SQL


Este tema describe los cambios que recupera V12 de base de datos de SQL Azure en el comportamiento de la conexión de los clientes que utilizan ADO.NET 4.5 o una versión posterior.


## <a name="v11-of-sql-database-port-1433"></a>V11 de base de datos SQL: puerto 1433


Cuando el programa de cliente utiliza ADO.NET 4.5 para conectarse y consultar con V11 de base de datos de SQL, la secuencia interna es la siguiente:


1. ADO.NET intenta conectarse a la base de datos de SQL.

2. ADO.NET utiliza el puerto 1433 para llamar a un módulo de software intermedio y el software intermedio se conecta a la base de datos de SQL.

3. Base de datos SQL envía su respuesta al software intermedio, que reenvía la respuesta a ADO.NET al puerto 1433.


**Terminología:** Describimos la secuencia anterior diciendo que ADO.NET interactúa con la base de datos SQL utilizando la *ruta de proxy*. Si ningún software intermedio implicado, diremos que se utilizó la *ruta directa* .


## <a name="v12-of-sql-database-outside-vs-inside"></a>V12 de base de datos SQL: fuera vs dentro


Para conexiones con V12, debemos solicitamos si el programa cliente ejecuta *fuera* o *dentro* del límite de nube de Azure. Las subsecciones describen dos escenarios comunes.


#### <a name="outside-client-runs-on-your-desktop-computer"></a>*Externas:* Cliente se ejecuta en el equipo de escritorio


Puerto 1433 es el único que debe estar abierto en su equipo de escritorio que hospeda la aplicación de cliente de base de datos de SQL.


#### <a name="inside-client-runs-on-azure"></a>*Dentro de:* Cliente se ejecuta en Azure


Cuando se ejecuta el cliente dentro del límite de nube de Azure, usa lo que llamamos una *ruta directa* para interactuar con el servidor de base de datos de SQL. Después de establece una conexión, aún más interacciones entre el cliente y la base de datos no implican a ningún proxy de software intermedio.


La secuencia es la siguiente:


1. ADO.NET 4.5 (o posterior) inicia una breve interacción con la nube de Azure y recibe un número de puerto dinámicamente identificados.
 - Es el número de puerto dinámicamente identificados en el rango de 11000 11999 o 14000 a 14999.

2. ADO.NET, a continuación, se conecta al servidor de base de datos SQL directamente, con ningún software intermedio entre.

3. Las consultas se envían directamente a la base de datos y los resultados se devuelven directamente al cliente.


Asegúrese de que el puerto rangos de 11000 11999 y 14000 a 14999 en el equipo cliente Azure se encuentran disponibles para interacciones de cliente ADO.NET 4.5 con V12 de base de datos de SQL.

- En particular, los puertos del rango deben ser libres de los bloqueadores de salida.

- En la máquina virtual de Azure, el **Firewall de Windows con seguridad avanzada** controla la configuración de puerto.
 - Puede usar la [interfaz de usuario de firewall](http://msdn.microsoft.com/library/cc646023.aspx) para agregar una regla que especifique el protocolo **TCP** junto con un intervalo de puertos con la sintaxis **11000 11999**.


## <a name="version-clarifications"></a>Aclaraciones versión


Esta sección explica los monikers que hacen referencia a las versiones de producto. También se muestran algunos pares de versiones entre productos.


#### <a name="adonet"></a>ADO.NET


- ADO.NET 4.0 es compatible con el protocolo TDS 7.3, pero no 7.4.
- ADO.NET 4.5 y versiones posteriores es compatible con el protocolo TDS 7.4.


#### <a name="sql-database-v11-and-v12"></a>V12 y V11 de base de datos SQL


Las diferencias de conexión de cliente entre V11 de base de datos de SQL y V12 se resaltan en este tema.


*Nota:* La instrucción Transact-SQL `SELECT @@version;` devuelve un valor que comienzan con un número como '11'. o '12' y esas coincide con nuestros nombres de versión de V11 y V12 para base de datos de SQL.


## <a name="related-links"></a>Vínculos relacionados


- ADO.NET 4.6 se publicó de 20 de julio de 2015. El anuncio de un blog del equipo de .NET está disponible [aquí](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).


- ADO.NET 4.5 se publicó en 15 de agosto de 2012. El anuncio de un blog del equipo de .NET está disponible [aquí](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
 - Una entrada de blog sobre ADO.NET 4.5.1 está disponible [aquí](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).


- [Lista de versión de protocolo TDS](http://www.freetds.org/userguide/tdshistory.htm)


- [Información general sobre el desarrollo de base de datos SQL](sql-database-develop-overview.md)


- [Servidor de seguridad de base de datos de SQL Azure](sql-database-firewall-configure.md)


- [Cómo: configurar el firewall en base de datos SQL](sql-database-configure-firewall-settings.md)

