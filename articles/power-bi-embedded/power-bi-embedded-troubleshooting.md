<properties
   pageTitle="Solución de problemas de vista previa incrustada de Microsoft Power BI"
   description="Solución de problemas de vista previa incrustada de Microsoft Power BI"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="microsoft-power-bi-embedded-preview-troubleshooting"></a>Solución de problemas de vista previa incrustada de Microsoft Power BI
En este artículo se proporciona respuestas acerca de cómo solucionar problemas de **Power BI incrustado**.

<a name="connection-string"/>
## <a name="setting-sql-server-connection-strings"></a>Configuración de las cadenas de conexión de SQL Server
Para establecer una cadena de conexión de SQL Server, debe seguir un formato específico. A continuación es un ejemplo de la cadena de conexión de SQL Server.

```
"Persist Security Info=False;Integrated Security=true;Initial Catalog=Northwind;server=(local)"
```

Para obtener más información sobre las cadenas de conexión de SQL Server, consulte los siguientes artículos:

-   [Cadenas de conexión de SQL Server](https://msdn.microsoft.com/library/jj653752.aspx)
-   [SqlConnection.ConnectionString](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.connectionstring.aspx)

<a name="credentials"/>
## <a name="setting-credentials"></a>Establecer credenciales
En el caso de que tenga las credenciales para un entorno de ensayo, como nombre de usuario y contraseña, o el desarrollo posible que necesite actualizar credenciales que coincidan con una solución de producción.

## <a name="see-also"></a>Vea también
- [Introducción a muestra](power-bi-embedded-get-started-sample.md)
- [¿Qué es Power BI incrustado](power-bi-embedded-what-is-power-bi-embedded.md)
