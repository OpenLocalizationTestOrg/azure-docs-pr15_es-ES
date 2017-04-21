
<!--
includes/sql-database-include-connection-string-40-config.md

Latest Freshness check:  2015-09-04 , GeneMi.

## Connection string
-->


### <a name="example-config-file-for-connection-string-security"></a>Archivo de configuración de ejemplo para la seguridad de la cadena de conexión


Es incorrecto para poner la cadena de conexión como literales en el código de C#. Es mejor poner la cadena de conexión en un archivo de configuración. Se puede editar la cadena cualquier momento sin necesidad de compilar.

Supongamos que el programa compilado de C# se denomina **ConsoleApplication1.exe**y que este .exe reside en un **bin\debug\* * directorio.

En este ejemplo, la mayoría de los elementos de la cadena de conexión se almacena en un archivo de configuración denominado exactamente **ConsoleApplication1.exe.config**. Este archivo de configuración también debe estar en **bin\debug\**.

En el XML del archivo de configuración siguiente verá una cadena de conexión denominada **ConnectionString4NoUserIDNoPassword**. El código de C# busca esta cadena.

Debe editar nombres reales en para los marcadores de posición:

- {your_serverName_here}
- {your_databaseName_here}



        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
        
            <connectionStrings>
                <clear />
                <add name="ConnectionString4NoUserIDNoPassword"
                providerName="System.Data.ProviderName"
        
                connectionString=
                "Server=tcp:{your_serverName_here}.database.windows.net,1433;
                Database={your_databaseName_here};
                Connection Timeout=30;
                Encrypt=True;
                TrustServerCertificate=False;" />
            </connectionStrings>
        </configuration>



Esta ilustración elegimos omite dos parámetros:

- Identificador de usuario = {your_userName_here};
- Contraseña = {your_password_here};


Puede incluirlas, pero a veces es mejor tener el programa obtener los valores de la entrada del teclado por el usuario. Depende.



<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
