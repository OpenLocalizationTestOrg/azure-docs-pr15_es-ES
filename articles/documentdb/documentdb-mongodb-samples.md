<properties 
    pageTitle="DocumentDB para obtener ejemplos de MongoDB | Microsoft Azure" 
    description="Buscar ejemplos de compatibilidad con el protocolo de DocumentDB para MongoDB." 
    keywords="ejemplos de MongoDB"
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

# <a name="documentdb-protocol-support-for-mongodb-examples"></a>Compatibilidad con el protocolo DocumentDB para obtener ejemplos de MongoDB
Para usar estos ejemplos, debe:

- [Crear](documentdb-create-mongodb-account.md) una cuenta de Azure DocumentDB con compatibilidad con el protocolo de MongoDB.
- Recuperar su cuenta de DocumentDB con compatibilidad con el protocolo de información de la [cadena de conexión](documentdb-connect-mongodb-account.md) de MongoDB.

## <a name="get-started-with-a-sample-aspnet-mvc-task-list-application"></a>Empezar a trabajar con una aplicación de lista de tareas de ASP.NET MVC de muestra

Puede usar el tutorial de [crear una aplicación web en Azure que se conecta a MongoDB que se ejecuta en una máquina virtual](../app-service-web/web-sites-dotnet-store-data-mongodb-vm.md) con una modificación mínima, configurar rápidamente una aplicación de MongoDB (ya sea localmente o se publica en una aplicación web de Azure) que se conecta a una cuenta de DocumentDB con compatibilidad con el protocolo de MongoDB.  

1. Siga el tutorial, con una modificación.  Reemplace el código de Dal.cs con esto:
    
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using MyTaskListApp.Models;
        using MongoDB.Driver;
        using MongoDB.Bson;
        using System.Configuration;
        using System.Security.Authentication;

        namespace MyTaskListApp
        {
            public class Dal : IDisposable
            {
                //private MongoServer mongoServer = null;
                private bool disposed = false;

                // To do: update the connection string with the DNS name
                // or IP address of your server. 
                //For example, "mongodb://testlinux.cloudapp.net
                private string connectionString = "mongodb://localhost:27017";
                private string userName = "<your user name>";
                private string host = "<your host>";
                private string password = "<your password>";

                // This sample uses a database named "Tasks" and a 
                //collection named "TasksList".  The database and collection 
                //will be automatically created if they don't already exist.
                private string dbName = "Tasks";
                private string collectionName = "TasksList";

                // Default constructor.        
                public Dal()
                {
                }

                // Gets all Task items from the MongoDB server.        
                public List<MyTask> GetAllTasks()
                {
                    try
                    {
                        var collection = GetTasksCollection();
                        return collection.Find(new BsonDocument()).ToList();
                    }
                    catch (MongoConnectionException)
                    {
                        return new List<MyTask>();
                    }
                }

                // Creates a Task and inserts it into the collection in MongoDB.
                public void CreateTask(MyTask task)
                {
                    var collection = GetTasksCollectionForEdit();
                    try
                    {
                        collection.InsertOne(task);
                    }
                    catch (MongoCommandException ex)
                    {
                        string msg = ex.Message;
                    }
                }
        
                private IMongoCollection<MyTask> GetTasksCollection()
                {
                    MongoClientSettings settings = new MongoClientSettings();
                    settings.Server = new MongoServerAddress(host, 10250);
                    settings.UseSsl = true;
                    settings.SslSettings = new SslSettings();
                    settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
        
                    MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                    MongoIdentityEvidence evidence = new PasswordEvidence(password);
        
                    settings.Credentials = new List<MongoCredential>()
                    {
                        new MongoCredential("SCRAM-SHA-1", identity, evidence)
                    };

                    MongoClient client = new MongoClient(settings);
                    var database = client.GetDatabase(dbName);
                    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                    return todoTaskCollection;
                }
        
                private IMongoCollection<MyTask> GetTasksCollectionForEdit()
                {
                    MongoClientSettings settings = new MongoClientSettings();
                    settings.Server = new MongoServerAddress(host, 10250);
                    settings.UseSsl = true;
                    settings.SslSettings = new SslSettings();
                    settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
        
                    MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                    MongoIdentityEvidence evidence = new PasswordEvidence(password);
        
                    settings.Credentials = new List<MongoCredential>()
                    {
                        new MongoCredential("SCRAM-SHA-1", identity, evidence)
                    };
                    MongoClient client = new MongoClient(settings);
                    var database = client.GetDatabase(dbName);
                    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                    return todoTaskCollection;
                }

                # region IDisposable
        
                public void Dispose()
                {
                    this.Dispose(true);
                    GC.SuppressFinalize(this);
                }

                protected virtual void Dispose(bool disposing)
                {
                    if (!this.disposed)
                    {
                        if (disposing)
                        {
                        }
                    }

                    this.disposed = true;
                }

                # endregion
            }
        }

2.  Modifique las variables siguientes en el archivo Dal.cs por la configuración de cuenta:

        private string userName = "<your user name>";
        private string host = "<your host>";
        private string password = "<your password>";

3. Use la aplicación.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [usar MongoChef](documentdb-mongodb-mongochef.md) con una cuenta de DocumentDB con protocolo de soporte técnico para MongoDB.

 
