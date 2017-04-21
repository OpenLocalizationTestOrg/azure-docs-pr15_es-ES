<properties 
    pageTitle="Conectarse a una cuenta de DocumentDB con compatibilidad con el protocolo de MongoDB | Microsoft Azure" 
    description="Obtenga información sobre cómo conectarse a una cuenta de DocumentDB con compatibilidad con el protocolo de MongoDB, ahora disponible para la vista previa. Conectar utilizando la cadena de conexión de MongoDB." 
    keywords="cadena de conexión de MongoDB"
    services="documentdb" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="anhoh"/>

# <a name="how-to-connect-to-a-documentdb-account-with-protocol-support-for-mongodb"></a>Cómo conectarse a una cuenta de DocumentDB con compatibilidad con el protocolo de MongoDB

Obtenga información sobre cómo conectarse a una cuenta de Azure DocumentDB con compatibilidad con el protocolo de MongoDB con el formato de URI de cadena de conexión MongoDB estándar.  

## <a name="get-the-accounts-connection-string-information"></a>Obtener información de la cuenta de la cadena de conexión

1. En una ventana nueva, inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la barra de **Navegación izquierda** de la hoja de la cuenta, haga clic en la **Cadena de conexión**. Para navegar hasta el **Módulo de cuenta**, en la Jumpbar, haga clic en **Más servicios**, haga clic en **DocumentDB (NoSQL)** y, a continuación, seleccione la cuenta de DocumentDB con compatibilidad con el protocolo de MongoDB.

    ![Captura de pantalla del módulo de toda la configuración](./media/documentdb-connect-mongodb-account/SettingsBlade.png)

3. El módulo de **Información de la cadena de conexión** se abre y tiene toda la información necesaria para conectarse a la cuenta con un controlador para MongoDB, incluyendo una cadena de conexión previamente construido.

    ![Captura de pantalla de la hoja de la cadena de conexión](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Requisitos de la cadena de conexión

Es importante que tenga en cuenta que DocumentDB es compatible con el formato de URI MongoDB conexión cadena estándar con un par de requisitos específicos: cuentas DocumentDB requieren autenticación y comunicación segura a través de SSL.  Por lo tanto, es el formato de la cadena de conexión:

    mongodb://username:password@host:port/[database]?ssl=true

Donde los valores de esta cadena están disponibles en el módulo de cadena de conexión anterior.

- Nombre de usuario (obligatorio)
    - Nombre de la cuenta de DocumentDB
- Contraseña (obligatorio)
    - Contraseña de la cuenta de DocumentDB
- Host (obligatorio)
    - FQDN de DocumentDB cuenta
- Puerto (obligatorio)
    - 10250
- Base de datos (opcional)
    - Utilizada la conexión de base de datos predeterminada
- SSL = true (obligatorio)

Por ejemplo, considere la posibilidad de la cuenta que se muestra en la información de la cadena de conexión anterior.  Una cadena de conexión válida es:
    
    mongodb://contoso123:<password@contoso123.documents.azure.com:10250/mydatabase?ssl=true

## <a name="connecting-with-the-c-driver-for-mongodb"></a>Conectar con el controlador de C# de MongoDB
Como se mencionó anteriormente, todas las cuentas de DocumentDB requieren autenticación y comunicación segura a través de SSL. Mientras que el formato de URI de cadena de conexión de MongoDB admite ssl = parámetro de cadena de consulta es true, trabajar con MongoDB C# controlador requiere el uso del objeto MongoClientSettings al crear un MongoClient.  Dado que la información de cuenta anterior, el fragmento de código siguiente muestra cómo conectarse a la cuenta y trabajar con la base de datos de "Tareas".

            MongoClientSettings settings = new MongoClientSettings();
            settings.Server = new MongoServerAddress("contoso123.documents.azure.com", 10250);
            settings.UseSsl = true;
            settings.SslSettings = new SslSettings();
            settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

            MongoIdentity identity = new MongoInternalIdentity("Tasks", "contoso123");
            MongoIdentityEvidence evidence = new PasswordEvidence("<password>");

            settings.Credentials = new List<MongoCredential>()
            {
                new MongoCredential("SCRAM-SHA-1", identity, evidence)
            };
            MongoClient client = new MongoClient(settings);
            var database = client.GetDatabase("Tasks",);
    

## <a name="next-steps"></a>Pasos siguientes


- Obtenga información sobre cómo [usar MongoChef](documentdb-mongodb-mongochef.md) con una cuenta de DocumentDB con protocolo de soporte técnico para MongoDB.
- Explorar DocumentDB con compatibilidad con el protocolo de MongoDB [ejemplos](documentdb-mongodb-samples.md).

 
