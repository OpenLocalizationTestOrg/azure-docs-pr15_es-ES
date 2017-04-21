<properties
    pageTitle="Administrar una cuenta DocumentDB a través del Portal de Azure | Microsoft Azure"
    description="Aprenda a administrar su cuenta DocumentDB a través del Portal de Azure. Encontrará a una guía sobre cómo usar el Portal de Azure para ver, copiar, eliminar y obtener acceso a las cuentas."
    keywords="Portal de Azure, documentdb, azure, Microsoft azure"
    services="documentdb"
    documentationCenter=""
    authors="kirillg"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/14/2016"
    ms.author="kirillg"/>

# <a name="how-to-manage-a-documentdb-account"></a>Cómo administrar una cuenta de DocumentDB

Obtenga información sobre cómo establecer coherencia global, trabajar con claves y eliminar una cuenta de DocumentDB en el portal de Azure.

## <a id="consistency"></a>Administrar la configuración de la coherencia de DocumentDB

Seleccionar el nivel de coherencia derecha depende de la semántica de la aplicación. Debe familiarizarse con los niveles de coherencia disponibles en DocumentDB lea [Usar niveles de coherencia para maximizar la disponibilidad y el rendimiento en DocumentDB] [consistency]. DocumentDB proporciona coherencia, disponibilidad y las garantías, en cada nivel de coherencia disponible para su cuenta de base de datos. Configurar la cuenta de base de datos con un nivel de coherencia de seguro requiere que los datos están limitados a una sola región de Azure y no está disponible globalmente. Por otro lado, los niveles de coherencia Media - antigüedad limitada, sesión o habilitar final asociar cualquier número de áreas de Azure a su cuenta de base de datos. Los siguientes pasos simples muestran cómo seleccionar el nivel de coherencia predeterminada para su cuenta de base de datos. 

### <a name="to-specify-the-default-consistency-for-a-documentdb-account"></a>Para especificar la coherencia predeterminada para una cuenta de DocumentDB

1. En el [portal de Azure](https://portal.azure.com/), acceder a su cuenta de DocumentDB.
2. En el módulo de cuenta, haga clic en **predeterminado coherencia**.
3. En el módulo de **Coherencia predeterminada** , seleccione el nuevo nivel de coherencia y haga clic en **Guardar**.
    ![Sesión de coherencia predeterminada][5]

## <a id="keys"></a>Ver, copiar y volver a crear las teclas de acceso
Cuando se crea una cuenta de DocumentDB, el servicio genera dos teclas de acceso principal que se pueden usar para la autenticación cuando se tiene acceso a la cuenta DocumentDB. Proporcionando dos teclas de acceso, DocumentDB le permite volver a generar las claves sin interrupciones en su cuenta de DocumentDB. 

En el [portal de Azure](https://portal.azure.com/), obtener acceso a la hoja de **claves** desde el menú de recursos en el módulo **DocumentDB cuenta** para ver, copiar y volver a crear las teclas de acceso que se utilizan para acceder a su cuenta de DocumentDB.

![Captura de pantalla de Portal Azure, módulos de teclas](./media/documentdb-manage-account/keys.png)

> [AZURE.NOTE] El módulo de **claves** también incluye cadenas de conexión primaria y secundaria que se pueden usar para conectarse a su cuenta de la [Herramienta de migración de datos](documentdb-import-data.md).

Claves de solo lectura también están disponibles en este módulo. Lecturas y consultas son operaciones de sólo lectura, mientras crea, elimina, y reemplaza no.

### <a name="copy-an-access-key-in-the-azure-portal"></a>Copiar una tecla de acceso en el Portal de Azure

En el módulo de **teclas** , haga clic en el botón **Copiar** a la derecha de la clave que desea copiar.

![Ver y copiar una tecla de acceso en el Portal de Azure, módulos de teclas](./media/documentdb-manage-account/copykeys.png)

### <a name="regenerate-access-keys"></a>Volver a crear las teclas de acceso

Debe cambiar las teclas de acceso a su cuenta de DocumentDB periódicamente para ayudar a proteger sus conexiones. Se asignan dos teclas de acceso para que pueda mantener conexiones con la cuenta DocumentDB usando una tecla de acceso mientras regenerar la clave de acceso a otros.

> [AZURE.WARNING] Volver a generar las claves de acceso afecta a todas las aplicaciones que dependen de la clave actual. Todos los clientes que utilizan la tecla de acceso para acceder a la cuenta de DocumentDB se deben actualizar para usar la nueva clave.

Si tiene aplicaciones o servicios en la nube con la cuenta DocumentDB, perderá las conexiones si regenerar claves, a menos que funcionar las claves. Los pasos siguientes describen el proceso de distribución de las claves.

1. Actualice la tecla de acceso en el código de la aplicación para hacer referencia a la tecla de acceso secundario de la cuenta DocumentDB.
2. Regenerar la clave de acceso principal para su cuenta de DocumentDB. En el [Portal de Azure](https://portal.azure.com/), acceder a su cuenta de DocumentDB.
3. En el módulo **DocumentDB cuenta** , haga clic en **las teclas**.
4. En el módulo de **teclas** , haga clic en el botón volver a crear y luego haga clic en **Aceptar** para confirmar que desea generar una nueva clave.
    ![Volver a crear las teclas de acceso](./media/documentdb-manage-account/regenerate-keys.png)

5. Una vez que haya comprobado que la nueva clave está disponible para su uso (aproximadamente 5 minutos después de la regeneración), actualice la tecla de acceso en el código de la aplicación para hacer referencia a la nueva clave de acceso principal.
6. Regenerar la clave de acceso secundario.

    ![Volver a crear las teclas de acceso](./media/documentdb-manage-account/regenerate-secondary-key.png)


> [AZURE.NOTE] Puede tardar varios minutos antes de poder usar una clave recién generada para acceder a su cuenta de DocumentDB.

## <a name="get-the--connection-string"></a>Obtener la cadena de conexión

Para recuperar la cadena de conexión, haga lo siguiente: 

1. En el [portal de Azure](https://portal.azure.com), acceder a su cuenta de DocumentDB.
2. En el menú de recursos, haga clic en **las teclas**.
3. Haga clic en el botón **Copiar** junto al cuadro **Cadena de conexión primaria** o **Secundaria cadena de conexión** . 

Si está utilizando la cadena de conexión en la [Herramienta de migración de base de datos de DocumentDB](documentdb-import-data.md), agregue el nombre de la base de datos hasta el final de la cadena de conexión. `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a id="delete"></a>Eliminar una cuenta de DocumentDB
Para quitar una cuenta de DocumentDB desde el Portal de Azure que ya no está utilizando, use el comando **Eliminar cuenta** en el módulo **DocumentDB cuenta** .

![Cómo eliminar una cuenta de DocumentDB en el Portal de Azure](./media/documentdb-manage-account/deleteaccount.png)


1. En el [portal de Azure](https://portal.azure.com/), obtener acceso a la cuenta de DocumentDB que desea eliminar.
2. En el módulo **DocumentDB cuenta** , haga clic en **más**y, a continuación, haga clic en **Eliminar cuenta**. O bien, haga clic en el nombre de la base de datos y haga clic en **Eliminar cuenta**.
3. En el módulo de confirmación resultante, escriba el nombre de cuenta de DocumentDB para confirmar que desea eliminar la cuenta.
4. Haga clic en el botón **Eliminar** .

![Cómo eliminar una cuenta de DocumentDB en el Portal de Azure](./media/documentdb-manage-account/delete-account-confirm.png)

## <a id="next"></a>Pasos siguientes

Obtenga información sobre cómo [empezar a trabajar con su cuenta de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

Para obtener más información sobre DocumentDB, consulte la documentación de Azure DocumentDB en [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409).


<!--Image references-->
[1]: ./media/documentdb-manage-account/documentdb_add_region-1.png
[2]: ./media/documentdb-manage-account/documentdb_add_region-2.png
[3]: ./media/documentdb-manage-account/documentdb_change_write_region-1.png
[4]: ./media/documentdb-manage-account/documentdb_change_write_region-2.png
[5]: ./media/documentdb-manage-account/documentdb_change_consistency-1.png
[6]: ./media/documentdb-manage-account/chooseandsaveconsistency.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/
