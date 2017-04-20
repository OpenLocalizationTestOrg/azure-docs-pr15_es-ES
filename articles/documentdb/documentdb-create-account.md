<properties
    pageTitle="Cómo crear una cuenta de DocumentDB | Microsoft Azure"
    description="Crear una base de datos NoSQL con DocumentDB de Azure. Siga estas instrucciones para crear una cuenta de DocumentDB y comenzar a crear la base de datos de NoSQL impresionante de rápidas y escala global." 
    keywords="crear una base de datos"
    services="documentdb"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/17/2016"
    ms.author="mimig"/>

# <a name="how-to-create-a-documentdb-nosql-account-using-the-azure-portal"></a>Cómo crear una cuenta de DocumentDB NoSQL con el portal de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](documentdb-create-account.md)
- [CLI Azure y Azure Administrador de recursos](documentdb-automation-resource-manager-cli.md)

Para crear una base de datos con Microsoft Azure DocumentDB, debe:

- Tiene una cuenta de Azure. Puede obtener una [cuenta gratuita de Azure](https://azure.microsoft.com/free) si aún no tiene una. 
- Crear una cuenta de DocumentDB.  

Puede crear una cuenta de DocumentDB mediante el portal de Azure, Administrador de recursos de Azure plantillas o Azure de línea de comandos interfaz. Este artículo le muestra cómo crear una cuenta de DocumentDB con el portal de Azure. Para crear una cuenta con el Administrador de recursos de Azure o CLI de Azure, vea [DocumentDB automatizar la creación de cuentas de base de datos](documentdb-automation-resource-manager-cli.md).

¿Es nuevo en DocumentDB? Vea [este](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) vídeo de cuatro minutos por Scott Hanselman para ver cómo completar las tareas más comunes en el portal en línea.

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com/).
2.  En el Jumpbar, haga clic en **nuevo**, haga clic en **bases de datos**y, a continuación, haga clic en **DocumentDB (NoSQL)**. 

    ![Captura de pantalla del portal de Azure, resaltado más servicios y DocumentDB (NoSQL)](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-1.png)  

3. En el módulo de la **nueva cuenta** , especifique la configuración que desee para la cuenta de DocumentDB.

    ![Captura de pantalla del módulo DocumentDB nueva](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-2.png)

    - En el cuadro **ID** , escriba un nombre para identificar la cuenta DocumentDB.  Cuando se valida el **identificador** , aparece una marca de verificación verde en el cuadro **ID** . El valor del **identificador** se convierte en el nombre de host en el URI. El **identificador** puede contener sólo letras minúsculas, números y el '-' caracteres y debe estar entre 3 y 50 caracteres. Tenga en cuenta que *documents.azure.com* se anexa al nombre del extremo que elija, el resultado es el extremo de la cuenta DocumentDB.

    - En el cuadro de la **API de NoSQL** , seleccione el modelo de programación para usar:
        - **DocumentDB**: la API de DocumentDB está disponible a través de .NET, Java, Node.js, Python y JavaScript [SDK](documentdb-sdk-dotnet.md), así como HTTP [resto](https://msdn.microsoft.com/library/azure/dn781481.aspx)y ofrece acceso mediante programación a todas las funciones de DocumentDB. 
       
        - **MongoDB**: DocumentDB también ofrece [soporte técnico de nivel de protocolo](documentdb-protocol-mongodb.md) para **MongoDB** API. Al elegir la opción de la API de MongoDB, puede usar el SDK de MongoDB existente y [Herramientas](documentdb-mongodb-mongochef.md) de hablar con DocumentDB. Puede [mover](documentdb-import-data.md) las aplicaciones de MongoDB existentes para usar DocumentDB, con [ningún cambio de código es necesario](documentdb-connect-mongodb-account.md)y aprovechar las ventajas de una base de datos totalmente administrado como un servicio, con escala ilimitada, réplica global y otras funciones.

    - Para la **suscripción**, seleccione la suscripción de Azure que desea usar para la cuenta de DocumentDB. Si su cuenta sólo tenga una suscripción, esa cuenta está activada de forma predeterminada.

    - En el **Grupo de recursos**, seleccione o cree un grupo de recursos para su cuenta de DocumentDB.  De forma predeterminada, se crea un nuevo grupo de recursos. Para obtener más información, consulte [con el portal de Azure para administrar los recursos de Azure](../articles/azure-portal/resource-group-portal.md).

    - Use la **ubicación** para especificar la ubicación geográfica en la que se va a hospedar su cuenta DocumentDB. 

4.  Una vez que se haya configurado las nuevas opciones de cuenta de DocumentDB, haga clic en **crear**. Para comprobar el estado de la implementación, active el hub de notificaciones.  

    ![Crear bases de datos rápidamente: captura de pantalla de concentrador notificaciones, que muestra que está creando la cuenta de DocumentDB](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-4.png)  

    ![Captura de pantalla del concentrador de notificaciones, que muestra que la cuenta de DocumentDB se ha creado correctamente y se implementa en un grupo de recursos - notificación de creador de base de datos en línea](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-5.png)

5.  Después de crea la cuenta de DocumentDB, está listo para su uso con la configuración predeterminada. La coherencia predeterminada de la cuenta de DocumentDB se establece en **sesión**.  Puede ajustar la coherencia predeterminada haciendo clic en **Coherencia predeterminada** en el menú de recursos. Para obtener más información sobre los niveles de coherencia ofrecidos por DocumentDB, vea [niveles de coherencia en DocumentDB](documentdb-consistency-levels.md).

    ![Captura de pantalla de la hoja de grupo de recursos: comience el desarrollo de aplicaciones](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-6.png)  

    ![Captura de pantalla de la placa de nivel de coherencia: coherencia de sesión](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-7.png)  

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]:../articles/documentdb/documentdb-manage.md


## <a name="next-steps"></a>Pasos siguientes

Ahora que ya tiene una cuenta de DocumentDB, el siguiente paso es crear una colección de DocumentDB y la base de datos. 

Puede crear una nueva colección y la base de datos mediante uno de estos procedimientos:

- El portal de Azure, tal como se describe en [crear una colección de DocumentDB con el portal de Azure](documentdb-create-collection.md).
- La exhaustiva tutoriales, que incluyen datos de ejemplo: [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node.js](documentdb-nodejs-application.md)o [Python](documentdb-python-application.md).
- El [.NET](documentdb-dotnet-samples.md#database-examples), [Node.js](documentdb-nodejs-samples.md#database-examples)o [Python](documentdb-python-samples.md#database-examples) código de ejemplo disponible en GitHub.
- El [.NET](documentdb-sdk-dotnet.md), [Node.js](documentdb-sdk-node.md), [Java](documentdb-sdk-java.md), [Python](documentdb-sdk-python.md)y [resto](https://msdn.microsoft.com/library/azure/mt489072.aspx) SDK.

Después de crear la base de datos y colección, debe [Agregar documentos](documentdb-view-json-document-explorer.md) a las colecciones.

Una vez documentos de una colección, puede usar [DocumentDB SQL](documentdb-sql-query.md) para [Ejecutar consultas](documentdb-sql-query.md#executing-queries) frente a los documentos. Puede ejecutar consultas con el [Explorador de consulta](documentdb-query-collections-query-explorer.md) en el portal, la [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx)o uno de los [SDK](documentdb-sdk-dotnet.md).

### <a name="learn-more"></a>Aprende más

Para obtener más información sobre DocumentDB, explore estos recursos:

-   [Ruta de aprendizaje para DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-   [Conceptos y el modelo de recursos jerárquica DocumentDB](documentdb-resources.md)
