<properties
   pageTitle="Configurar una descripción general SQL server firewall | Microsoft Azure"
   description="Obtenga información sobre cómo configurar un servidor de seguridad de base de datos SQL con las reglas de firewall de nivel de servidor y de nivel de base de datos para administrar el acceso."
   keywords="servidor de seguridad de base de datos"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor="cgronlun"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/14/2016"
   ms.author="rickbyh"/>

# <a name="configure-azure-sql-database-firewall-rules---overview"></a>Configurar las reglas de firewall de base de datos de SQL Azure \- información general


> [AZURE.SELECTOR]
- [Información general](sql-database-firewall-configure.md)
- [Portal de Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API DE REST](sql-database-configure-firewall-settings-rest.md)


Base de datos de SQL de Microsoft Azure proporciona un servicio de base de datos relacional de Azure y otras aplicaciones basadas en Internet. Para ayudar a proteger los datos, firewalls prohibir el acceso a su servidor de base de datos hasta que se especifique qué equipos tienen permiso. El firewall concede acceso a las bases de datos en función de la dirección IP de origen de cada solicitud.

Para configurar el firewall, puede crear reglas de firewall que especifican rangos de direcciones IP aceptable. Puede crear reglas de firewall en los niveles de servidor y base de datos.

- **Reglas de firewall de nivel de servidor:** Estas reglas permiten a los clientes tener acceso a todo el servidor de SQL Azure, es decir, todas las bases de datos en el mismo servidor lógico. Estas reglas se almacenan en **la base de datos** . Las reglas de firewall de nivel de servidor pueden configurarse a través del portal o mediante instrucciones Transact-SQL.
- **Reglas de firewall de nivel de base de datos:** Estas reglas permiten a los clientes tener acceso a las bases de datos individuales dentro de su servidor de base de datos de SQL Azure. Puede crear estas reglas para cada base de datos y se almacenan en las bases de datos individuales. (Puede crear reglas de firewall de nivel de base de datos para **la base de datos** ). Estas reglas pueden resultar útiles para restringir el acceso a determinadas bases de datos (seguro) en el mismo servidor lógico. Solo se pueden configurar reglas de firewall de nivel de base de datos mediante instrucciones Transact-SQL.

**Recomendación:** Microsoft recomienda usar las reglas de firewall de nivel de base de datos siempre que sea posible para mejorar la seguridad y para hacer que la base de datos sea más portátil. Usar reglas de firewall de nivel de servidor para los administradores y cuando tiene muchas bases de datos que tienen los mismos requisitos de acceso y no desea perder tiempo configurar de forma individual cada base de datos.


## <a name="firewall-overview"></a>Información general de Firewall

Inicialmente, todo el acceso a su servidor de SQL Azure Transact-SQL está bloqueado por el firewall. Para empezar a usar su servidor de SQL Azure, debe ir al portal de Azure y especifique una o más reglas de firewall de nivel de servidor que permiten tener acceso a su servidor de SQL Azure. Use las reglas de firewall para especificar los intervalos de direcciones IP de Internet permitidos y, a continuación, si las aplicaciones de Azure pueden intentar conectarse al servidor de SQL Azure.

Para selectivamente conceder acceso a una de las bases de datos en su servidor de SQL Azure, debe crear una regla de nivel de base de datos para la base de datos necesario. Especificar un intervalo de direcciones IP para la regla de firewall de la base de datos que está fuera del intervalo de direcciones IP especificado en la regla de firewall de nivel de servidor y asegúrese de que la dirección IP del cliente se encuentra en el intervalo especificado en la regla de nivel de base de datos.

Intentos de conexión de Internet y Azure deben pasar primero a través del firewall antes de poder acceder a su servidor de SQL Azure o una base de datos de SQL, como se muestra en el diagrama siguiente.

   ![Configuración del firewall que describe del diagrama.][1]

## <a name="connecting-from-the-internet"></a>Conexión de Internet

Cuando un equipo intenta conectarse al servidor de base de datos de Internet, el firewall comprueba primero la dirección IP de origen de la solicitud con el conjunto completo de reglas de firewall:

- Si es la dirección IP de la solicitud dentro de uno de los intervalos especificados en las reglas de firewall de nivel de servidor, se le ha concedido la conexión al servidor de base de datos de SQL Azure.

- Si la dirección IP de la solicitud no está en uno de los intervalos especificados en la regla de firewall de nivel de servidor, se comprueban las reglas de firewall de nivel de base de datos. Si es la dirección IP de la solicitud dentro de uno de los intervalos especificados en las reglas de firewall de nivel de base de datos, la conexión se concede solo a la base de datos que tiene una regla de nivel de base de datos coincidente.

- Si la dirección IP de la solicitud no está dentro del rango especificado en cualquiera de las reglas de firewall del servidor o de base de datos, falla la solicitud de conexión.

> [AZURE.NOTE] Para obtener acceso a la base de datos de SQL Azure desde el equipo local, asegúrese de que el firewall de la red y el equipo local permite comunicaciones salientes en el puerto TCP 1433.


## <a name="connecting-from-azure"></a>Conexión de Azure

Cuando una aplicación de Azure intenta conectarse al servidor de base de datos, el firewall comprueba que se permiten conexiones de Azure. Un firewall establecer con iniciales y finales dirección igual a 0.0.0.0 indica permitidas estas conexiones. Si no se permite el intento de conexión, la solicitud no alcanza el servidor de base de datos de SQL Azure.

> [AZURE.IMPORTANT] Esta opción configura el firewall para permitir que todas las conexiones de Azure conexiones incluidas desde las suscripciones de otros clientes. Al seleccionar esta opción, asegúrese de que el inicio de sesión y permisos de usuario limitan el acceso a usuarios autorizados.

Puede habilitar las conexiones de Azure de dos maneras:

- En el [portal de Microsoft Azure](https://portal.azure.com/), seleccione la casilla de verificación **Permitir que los servicios de azure al servidor de acceso** al crear un nuevo servidor.

- En el [portal de clásica](http://go.microsoft.com/fwlink/p/?LinkID=161793), en la pestaña **Configurar** en un servidor, en la sección **Servicios permitidos** , haga clic en **Sí** para los **Servicios de Microsoft Azure**.

## <a name="creating-the-first-server-level-firewall-rule"></a>Crear la primera regla de firewall de nivel de servidor

La primera configuración de firewall de nivel de servidor puede crearse con el [portal de Azure](https://portal.azure.com/) o mediante programación con la API de REST o PowerShell de Azure. Las reglas de firewall de nivel de servidor posterior pueden crearse y administradas con estos métodos, así como a través de Transact-SQL. Para mejorar el rendimiento, las reglas de firewall de nivel de servidor temporalmente se almacenan en el nivel de base de datos. Para actualizar la memoria caché, consulte [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). Para obtener más información sobre las reglas de firewall de nivel de servidor, vea [Cómo: configurar un firewall de servidor de SQL Azure con el portal de Azure](sql-database-configure-firewall-settings.md).

## <a name="creating-database-level-firewall-rules"></a>Creación de reglas de firewall de nivel de base de datos

Después de haber configurado el primer firewall de nivel de servidor, desea restringir el acceso a determinadas bases de datos. Si especifica un intervalo de direcciones IP de la regla de firewall de nivel de base de datos que se encuentra fuera del rango especificado en la regla de firewall de nivel de servidor, sólo aquellos clientes que tengan direcciones IP en el intervalo de nivel de base de datos pueden tener acceso a la base de datos. Puede tener un máximo de reglas de 128 firewall de nivel de base de datos para una base de datos. Las reglas de firewall de nivel de base de datos para bases de datos de usuario y patrón se pueden crea y administra a través de Transact-SQL. Para obtener más información sobre cómo configurar reglas de firewall de nivel de base de datos, vea [sp_set_database_firewall_rule (bases de datos de SQL Azure)](https://msdn.microsoft.com/library/dn270010.aspx).

## <a name="programmatically-managing-firewall-rules"></a>Administrar mediante programación reglas de firewall

El portal de Azure, además de las reglas de firewall pueden administrarse mediante programación utilizando Transact-SQL, API de REST y PowerShell de Azure. Las tablas siguientes describen el conjunto de comandos disponibles para cada método.


### <a name="transact-sql"></a>Transact-SQL

| Procedimiento almacenado o la vista de catálogo                                                           | Nivel     | Descripción                                          |
|--------------------------------------------------------------------------------------------|-----------|------------------------------------------------------|
| [Sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx)                   | Servidor    | Muestra las reglas de firewall de nivel de servidor actual     |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx)             | Servidor    | Crea o actualiza las reglas de firewall de nivel de servidor       |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx)          | Servidor    | Elimina las reglas de firewall de nivel de servidor                  |
| [Sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx)        | Base de datos  | Muestra las reglas de firewall de nivel de base de datos actual   |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx)    | Base de datos  | Crea o actualiza las reglas de firewall de nivel de base de datos |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) | Bases de datos | Elimina las reglas de firewall nivel de base de datos                |

### <a name="rest-api"></a>API DE REST

| API                  | Nivel  | Descripción                                                      |
|----------------------|--------|------------------------------------------------------------------|
| [Reglas de Firewall de lista](https://msdn.microsoft.com/library/azure/dn505715.aspx)  | Servidor | Muestra las reglas de firewall de nivel de servidor actual                 |
| [Crear reglas de Firewall](https://msdn.microsoft.com/library/azure/dn505712.aspx) | Servidor | Crea o actualiza las reglas de firewall de nivel de servidor                   |
| [Establecer reglas de Firewall](https://msdn.microsoft.com/library/azure/dn505707.aspx)    | Servidor | Actualiza las propiedades de una regla de firewall de nivel de servidor existente |
| [Eliminar regla de Firewall](https://msdn.microsoft.com/library/azure/dn505706.aspx) | Servidor | Elimina las reglas de firewall de nivel de servidor                              |


### <a name="azure-powershell"></a>Azure PowerShell

| Cmdlet                                                                                              | Nivel  | Descripción                                                      |
|-----------------------------------------------------------------------------------------------------|--------|------------------------------------------------------------------|
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx)    | Servidor | Devuelve las reglas de firewall de nivel de servidor actual                  |
| [Nueva AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx)    | Servidor | Crea una nueva regla de firewall de nivel de servidor                         |
| [Establecer AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx)    | Servidor | Actualiza las propiedades de una regla de firewall de nivel de servidor existente |
| [Quitar AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) | Servidor | Elimina las reglas de firewall de nivel de servidor                              |

> [AZURE.NOTE] Puede haber arriba como retrasar cinco minutos para que los cambios a la configuración de firewall surta efecto.

## <a name="troubleshooting-the-database-firewall"></a>Solución de problemas del firewall de la base de datos

Cuando el acceso al servicio de base de datos de SQL de Microsoft Azure no se comporta según lo esperado, tenga en cuenta los siguientes puntos:

- **Configuración del firewall local:** Antes de que su equipo puede tener acceso a la base de datos de SQL Azure, debe crear una excepción de firewall en el equipo para el puerto TCP 1433. Si va a realizar conexiones dentro del límite de nube de Azure, debe abrir puertos adicionales. Para obtener más información, consulte la **V12 de SQL base de datos: fuera vs dentro de** sección de [puertos más allá de 1433 para ADO.NET 4.5 y V12 de base de datos de SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Dirección traducción de red (NAT):** Debido a NAT, la dirección IP que usa el equipo para conectarse a la base de datos de SQL Azure puede ser diferente a la dirección IP que se muestra en las opciones de configuración de IP de equipo. Para ver la dirección IP que usa el equipo para conectarse a Azure, inicie sesión en el portal y navegue a la ficha **Configurar** en el servidor que hospeda la base de datos. En la sección **Direcciones IP permitidas** , se muestra la **Dirección IP del cliente actual** . Haga clic en **Agregar** a las **Direcciones IP permitidas** para permitir que este equipo para tener acceso al servidor.

- **Cambios a la lista Permitir no se han aplicado aún:** Puede haber como retrasar cinco minutos para que los cambios a la configuración de firewall de la base de datos de SQL Azure surta efecto.

- **No está autorizado el inicio de sesión o se ha usado una contraseña incorrecta:** Si un inicio de sesión no tiene permisos en el servidor de base de datos de SQL Azure o la contraseña que usa es correcta, se rechaza la conexión al servidor de base de datos de SQL Azure. Crear una configuración de firewall solo proporciona a los clientes una oportunidad para intentar conectarse al servidor; cada cliente debe proporcionar las credenciales de seguridad necesarias. Para obtener más información sobre cómo preparar inicios de sesión, vea Administrar bases de datos, inicios de sesión y los usuarios de la base de datos de SQL Azure.

- **Dirección IP dinámica:** Si tiene una conexión a Internet con direcciones IP dinámicas y tiene problemas al obtener a través del firewall, podría pruebe una de las siguientes soluciones:

 - Pida a su proveedor de servicios de Internet (ISP) para el intervalo de direcciones IP asignado a los equipos cliente que tienen acceso al servidor de base de datos de SQL Azure y, a continuación, agregue el intervalo de direcciones IP como una regla de firewall.

 - Obtener el direccionamiento IP estático en su lugar para los equipos cliente y, a continuación, agregue las direcciones IP como reglas de firewall.

## <a name="next-steps"></a>Pasos siguientes

Para saber cómo artículos sobre la creación de reglas de firewall de nivel de servidor y de nivel de base de datos, vea:

- [Configurar las reglas de firewall de nivel de servidor de base de datos de SQL Azure con el portal de Azure](sql-database-configure-firewall-settings.md)
- [Configurar reglas de firewall de nivel de base de datos y servidor de base de datos de SQL Azure con SQL T](sql-database-configure-firewall-settings-tsql.md)
- [Configurar las reglas de firewall de nivel de servidor de base de datos de SQL Azure con PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurar las reglas de firewall de nivel de servidor de base de datos de SQL Azure con la API de REST](sql-database-configure-firewall-settings-rest.md)

Para obtener un tutorial acerca de cómo crear una base de datos, vea [crear una base de datos SQL en minutos con el portal de Azure](sql-database-get-started.md).
Para obtener ayuda para la conexión a una base de datos de SQL Azure de Abrir origen o aplicaciones de terceros, vea [ejemplos de código para la base de datos SQL de inicio de rápido de cliente](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Para comprender cómo navegar a bases de datos, consulte [Administrar la seguridad de access e inicio de sesión de base de datos](https://msdn.microsoft.com/library/azure/ee336235.aspx).



## <a name="additional-resources"></a>Recursos adicionales

- [Proteger la base de datos](sql-database-security.md)
- [Centro de seguridad de base de datos SQL Azure y motor de base de datos SQL Server](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
