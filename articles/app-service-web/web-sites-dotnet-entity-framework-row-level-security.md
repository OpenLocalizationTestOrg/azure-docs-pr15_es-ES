<properties
    pageTitle="Tutorial: Web app con una base de datos de varios inquilino mediante Framework de entidad y seguridad a nivel de fila"
    description="Obtenga información sobre cómo desarrollar una aplicación web de ASP.NET MVC 5 con un inquilino múltiples base de datos SQL backent, mediante el marco de entidad y seguridad a nivel de fila."
  metaKeywords="azure asp.net mvc entity framework multi tenant row level security rls sql database"
    services="app-service\web"
    documentationCenter=".net"
    manager="jeffreyg"
  authors="tmullaney"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="04/25/2016"
    ms.author="thmullan"/>

# <a name="tutorial-web-app-with-a-multi-tenant-database-using-entity-framework-and-row-level-security"></a>Tutorial: Web app con una base de datos de varios inquilino mediante Framework de entidad y seguridad a nivel de fila

Este tutorial muestra cómo crear una aplicación web de múltiples arrendatarios con un modelo de usuarios "de[base de datos compartida, esquema compartido](https://msdn.microsoft.com/library/aa479086.aspx)", con el marco de entidad y [Seguridad a nivel de fila](https://msdn.microsoft.com/library/dn765131.aspx). En este modelo, una base de datos contiene los datos de varios inquilinos y cada fila de cada tabla está asociado con un "inquilino Id." Nivel de la fila de seguridad, una nueva característica de base de datos de SQL Azure, se utiliza para evitar que los inquilinos de acceso a los datos de los demás. Para ello, simplemente un único, pequeño cambio en la aplicación. Al centralizar la lógica de acceso del inquilino dentro de la base de datos, RLS simplifica el código de la aplicación y reduce el riesgo de pérdida de datos accidental entre los inquilinos.

Comencemos con la aplicación de Contact Manager sencilla de [crear una aplicación de ASP.NET MVP con auth y DB de SQL e implementar en servicio de la aplicación de Azure](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Derecha ahora, la aplicación permite a los usuarios (inquilinos) para ver todos los contactos:

![Póngase en contacto con el Administrador de aplicación antes de habilitar RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-Before.png)

Con unos pocos cambios pequeños, agregaremos compatibilidad para usuarios múltiple, para que los usuarios puedan ver solo los contactos que pertenecen a ellos.

## <a name="step-1-add-an-interceptor-class-in-the-application-to-set-the-sessioncontext"></a>Paso 1: Agregar una clase Interceptor en la aplicación para establecer el SESSION_CONTEXT

Hay un cambio de aplicación que debemos realizar. Dado que todos los usuarios de la aplicación se conectan a la base de datos con la misma cadena de conexión (es decir, mismo SQL inicio de sesión), actualmente no hay ninguna forma de una directiva de RLS saber que deben filtrar por el usuario. Este enfoque es muy común en las aplicaciones web porque permite la agrupación de conexiones eficaz, pero significa que debemos identificar el usuario de la aplicación actual dentro de la base de datos de otra forma. La solución consiste en hacer que la aplicación establezca un par de valor de clave para el identificador de usuario actual en la [SESSION_CONTEXT](https://msdn.microsoft.com/library/mt590806) inmediatamente después de abrir una conexión antes de ejecutar las consultas. SESSION_CONTEXT es un almacén de valor de clave de ámbito de la sesión y nuestra directiva RLS usará el identificador de usuario almacenado en ella para identificar el usuario actual.

Vamos a agregar un [interceptor](https://msdn.microsoft.com/data/dn469464.aspx) (en particular, un [DbConnectionInterceptor](https://msdn.microsoft.com/library/system.data.entity.infrastructure.interception.idbconnectioninterceptor)), una nueva característica en Framework (EF) 6, establezca automáticamente el ID de usuario actual en la SESSION_CONTEXT al ejecutar una instrucción SQL T siempre EF abre una conexión de entidad.

1.  Abra el proyecto ContactManager en Visual Studio.
2.  Haga clic en la carpeta de modelos en el Explorador de soluciones y a continuación, haga clic en Agregar > clase.
3.  Asigne un nombre a la nueva clase "SessionContextInterceptor.cs" y haga clic en Agregar.
4.  Reemplazar el contenido de SessionContextInterceptor.cs con el código siguiente.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure.Interception;
using Microsoft.AspNet.Identity;

namespace ContactManager.Models
{
    public class SessionContextInterceptor : IDbConnectionInterceptor
    {
        public void Opened(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
            // Set SESSION_CONTEXT to current UserId whenever EF opens a connection
            try
            {
                var userId = System.Web.HttpContext.Current.User.Identity.GetUserId();
                if (userId != null)
                {
                    DbCommand cmd = connection.CreateCommand();
                    cmd.CommandText = "EXEC sp_set_session_context @key=N'UserId', @value=@UserId";
                    DbParameter param = cmd.CreateParameter();
                    param.ParameterName = "@UserId";
                    param.Value = userId;
                    cmd.Parameters.Add(param);
                    cmd.ExecuteNonQuery();
                }
            }
            catch (System.NullReferenceException)
            {
                // If no user is logged in, leave SESSION_CONTEXT null (all rows will be filtered)
            }
        }
        
        public void Opening(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void BeganTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void BeginningTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void Closed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Closing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void ConnectionStringGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSet(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSetting(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionTimeoutGetting(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void ConnectionTimeoutGot(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void DataSourceGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DataSourceGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void Disposed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Disposing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void EnlistedTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void EnlistingTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void ServerVersionGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ServerVersionGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void StateGetting(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }

        public void StateGot(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }
    }

    public class SessionContextConfiguration : DbConfiguration
    {
        public SessionContextConfiguration()
        {
            AddInterceptor(new SessionContextInterceptor());
        }
    }
}
```

Que es el cambio de aplicación única necesario. Continuar y crear y publicar la aplicación.

## <a name="step-2-add-a-userid-column-to-the-database-schema"></a>Paso 2: Agregar una columna de identificador de usuario en el esquema de la base de datos

A continuación, es necesario agregar una columna de identificador de usuario a la tabla de contactos para asociar cada fila con un usuario (inquilino). Se modificará el esquema directamente en la base de datos para que no hay que incluir este campo en el modelo de datos EF.

Conectarse a la base de datos directamente, con SQL Server Management Studio o Visual Studio y a continuación, ejecute el siguiente T-SQL:

```
ALTER TABLE Contacts ADD UserId nvarchar(128)
    DEFAULT CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
```

Esto agrega una columna de identificador de usuario a la tabla Contactos. Usamos el tipo de datos nvarchar (128) para que coincida con los identificadores de usuario almacenada en la tabla AspNetUsers y creamos una restricción que establece automáticamente el identificador de usuario para las filas recién insertadas sea el identificador de usuario actualmente almacenado en SESSION_CONTEXT.

Ahora la tabla tiene un aspecto similar a este:

![Tabla SSMS contactos](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-Contacts.png)

Cuando se crean nuevos contactos, que deberá asignarse automáticamente el identificador de usuario correcta. Con fines de demostración, sin embargo, vamos a asignar a algunos de estos contactos existentes a un usuario existente.

Si ha creado unos pocos usuarios en la aplicación ya (por ejemplo, usando local, Google o Facebook cuentas), verá en la tabla AspNetUsers. En la captura de pantalla siguiente, hay un solo usuario hasta ese momento.

![Tabla de SSMS AspNetUsers](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-AspNetUsers.png)

Copiar el identificador de user1@contoso.com, y péguela en la instrucción SQL T siguiente. Ejecute esta instrucción para asociar tres de los contactos con este ID de usuario.

```
UPDATE Contacts SET UserId = '19bc9b0d-28dd-4510-bd5e-d6b6d445f511'
WHERE ContactId IN (1, 2, 5)
```

## <a name="step-3-create-a-row-level-security-policy-in-the-database"></a>Paso 3: Crear una directiva de seguridad de la fila en la base de datos

El último paso es crear una directiva de seguridad que utiliza el identificador de usuario SESSION_CONTEXT para filtrar automáticamente los resultados de las consultas.

Mientras sigue conectado a la base de datos, ejecute el siguiente T-SQL:

```
CREATE SCHEMA Security
go

CREATE FUNCTION Security.userAccessPredicate(@UserId nvarchar(128))
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS accessResult
    WHERE @UserId = CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
go

CREATE SECURITY POLICY Security.userSecurityPolicy
    ADD FILTER PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts,
    ADD BLOCK PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts
go

```

Este código realiza tres acciones. En primer lugar, crea un nuevo esquema como práctica recomendada para centralizar y limitar el acceso a los objetos RLS. A continuación, crea una función de predicado que devolverá '1' cuando el identificador de usuario de una fila coincide con el identificador de usuario en SESSION_CONTEXT. Por último, crea una directiva de seguridad que esta función se agrega como un filtro y un bloque de predicado en la tabla de contactos. Hace que el predicado de filtro consultas devolver sólo las filas que pertenecen al usuario actual y el predicado bloque actúa como una medida de seguridad para impedir que la aplicación nunca accidentalmente insertar una fila para el usuario incorrecta.

Ahora ejecute la aplicación e inicie sesión como user1@contoso.com. Este usuario ahora ve únicamente los contactos se asigna a este ID de usuario anteriormente:

![Póngase en contacto con el Administrador de aplicación antes de habilitar RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-After.png)

Para validar aún más, intente registrar un nuevo usuario. No verán contactos, porque no se han asignado a ellos. Si crea un nuevo contacto, se asignará a ellas, y solo podrán verla.

## <a name="next-steps"></a>Pasos siguientes

¡Eso es todo! La aplicación web de ponerse en contacto con el administrador simple se ha convertido en un inquilino múltiples uno en los que cada usuario tiene su propia lista de contactos. Mediante la seguridad a nivel de fila, nos hemos evitar la complejidad de aplicar la lógica de acceso del inquilino en nuestro código de la aplicación. Esta transparencia permite a la aplicación para centrarse en el problema real del negocio a mano, y también reduce el riesgo de filtración accidentalmente datos mientras la aplicación del código base crece.

Este tutorial solo ha tratado la superficie de lo que puede hacer con RLS. Por ejemplo, es posible que haya más sofisticada o lógica de acceso granulares y del puede almacenar algo más que el identificador de usuario actual en la SESSION_CONTEXT. También es posible integrar [RLS con las bibliotecas de cliente de base de datos elástico herramientas](../sql-database/sql-database-elastic-tools-multi-tenant-row-level-security.md) admitir múltiples arrendatarios shards en un nivel de datos de escalado.

Más allá de estas posibilidades, también estamos trabajando para hacer RLS aún mejor. Si tiene alguna pregunta, ideas o cosas que le gustaría ver, háganoslo saber en los comentarios. Agradecemos sus comentarios.
