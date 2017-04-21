<properties 
    pageTitle="Escala SQL Azure elástico preguntas más frecuentes | Microsoft Azure" 
    description="Preguntas más frecuentes sobre escala elástico de base de datos SQL Azure." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/03/2016" 
    ms.author="ddove"/>

# <a name="elastic-database-tools-faq"></a>Herramientas de bases de datos elástico preguntas más frecuentes 

#### <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>¿Si dispone de un solo inquilino por partes y no hay ninguna clave sharding, cómo rellenar la clave de sharding para obtener la información de esquema?

El objeto de información de esquema solo se usa para dividir escenarios de combinación. Si una aplicación es por sí sola-inquilino, no se requiere la herramienta de división de combinación y, por tanto, no es necesario para rellenar el objeto de información de esquema.

#### <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>He configurado una base de datos y un administrador de mapa fragmentar dispone, cómo registrar esta nueva base de datos como un fragmentar?

Consulte **[Agregar una fragmentar a la aplicación con la biblioteca de cliente de base de datos elástico](sql-database-elastic-scale-add-a-shard.md)**. 

#### <a name="how-much-do-elastic-database-tools-cost"></a>¿Cómo herramientas de base de datos elástico costado?

Uso de la biblioteca de cliente elásticos de la base de datos no incurre en los costos. Acumulación de costos solo para las bases de datos de SQL Azure que use para shards y el Administrador de mapa de partes, así como los roles de web y trabajador que aprovisionar para la herramienta de división de combinación.

#### <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>¿Por qué mis credenciales no funcionan cuando agregue un partes de un servidor diferente?
No utilice credenciales en el formulario de "usuario ID=username@servername”, en su lugar simplemente usar" ID de usuario = username ".  Asegúrese de que el inicio de sesión de "nombre de usuario" tiene permisos en las partes.

#### <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>¿Debo crear un administrador de mapa fragmentar y rellenar shards cada vez que inicie mis aplicaciones?

No, la creación del Administrador de mapa fragmentar (por ejemplo, **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) es una operación de una sola vez.  La aplicación debería utilizar la llamada **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** en tiempo de inicio de la aplicación.  Debe solo una de estas llamadas por dominio de aplicación.

#### <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Tengo preguntas acerca del uso de herramientas de base de datos elástico, ¿cómo obtenerlas responda? 

Por favor, póngase en contacto con nosotros en el [foro de la base de datos de SQL Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted).

#### <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Al recibir una conexión de base de datos con una clave de sharding, puedo consultar los datos para otras teclas de sharding en la misma fragmentar.  ¿Es este diseño?

Las API de escala elástico le ofrecen una conexión a la base de datos correcto para la clave sharding, pero no proporciona sharding clave filtrado.  Agregar cláusulas **WHERE** a la consulta para restringir el ámbito a la clave sharding proporcionado, si es necesario.

#### <a name="can-i-use-a-different-azure-database-edition-for-each-shard-in-my-shard-set"></a>¿Usar una edición de base de datos de Azure diferente para cada fragmentar en mi conjunto fragmentar?

Sí, un fragmentar es una base de datos individual y, por tanto, una fragmentar podría ser una edición Premium mientras otro ser un servidor Standard edition. Además, la edición de un fragmentar puede escalar hacia arriba o hacia abajo varias veces durante la duración de las partes.

#### <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>¿Es la disposición de la herramienta de combinación de división (o elimina) una base de datos durante una operación de división o combinación? 

No. Para operaciones de **dividir** la base de datos de destino debe existir con el esquema adecuado y registrar con el Administrador de mapa fragmentar.  Para operaciones de **combinación de correspondencia** , debe eliminar las partes desde el Administrador de mapa fragmentar y, a continuación, eliminar la base de datos.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 