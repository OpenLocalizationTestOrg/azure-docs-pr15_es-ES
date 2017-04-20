<properties
    pageTitle="Habilitar la sincronización sin conexión para la aplicación móvil de Azure (iOS)"
    description="Aprenda a usar aplicaciones de aplicación de servicio móvil a la memoria caché y sincronización de datos sin conexión en la aplicación de iOS"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="enable-offline-sync-for-your-ios-mobile-app"></a>Habilitar la sincronización sin conexión para la aplicación móvil de iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Información general

Este tutorial trata sobre la característica de sincronización sin conexión de aplicaciones móviles de Azure para iOS. Sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil&mdash;ver, agregar o modificar datos&mdash;incluso cuando no hay ninguna conexión de red. Cambios se almacenan en una base de datos local. una vez que se vuelve a conectar el dispositivo, estos cambios se sincronizan con el servidor remoto.

Si esta es su primera experiencia con aplicaciones móviles de Azure, debe completar el tutorial [crear una aplicación de iOS]. Si no utiliza el proyecto de servidor de inicio rápido descargado, debe agregar los paquetes de extensión de acceso de datos a su proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [trabajar con el servidor back-end de .NET SDK para las aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para obtener más información sobre la característica de sincronización sin conexión, vea el tema de la [Sincronización sin conexión de datos en las aplicaciones móviles de Azure].

## <a name="review-sync"></a>Revise el código de sincronización de cliente

El proyecto de cliente que ha descargado para el tutorial de [crear una aplicación de iOS] ya contiene código admite la sincronización sin conexión con una base de datos local basado en datos básicos. Esta sección es un resumen de lo que ya está incluido en el código del tutorial. Para obtener información general de la característica, consulte [Sincronización de datos sin conexión en las aplicaciones móviles de Azure].

La característica de sincronización de la sincronización de datos sin conexión de aplicaciones móviles de Azure permite a los usuarios finales interactuar con una base de datos local cuando la red no es accesible. Para usar estas características en la aplicación, inicializar el contexto de sincronización de `MSClient` y hacer referencia a un almacén local. A continuación, hacer referencia a la tabla a través de la `MSSyncTable` interfaz.

1. En **QSTodoService.m** (objetivo-C) o **ToDoTableViewController.swift** (Swift), observe el tipo del miembro `syncTable` es `MSSyncTable`. Sincronización sin conexión usa esta interfaz de tabla de sincronización en lugar de `MSTable`. Cuando se utiliza una tabla de sincronización, todas las operaciones ir a la tienda local y solo se sincronizan con el servidor remoto con operaciones de inserción y extracción explícitas.

    Para obtener una referencia a una tabla de sincronización, use el método `syncTableWithName` en `MSClient`. Para quitar la funcionalidad de sincronización sin conexión, utilice `tableWithName` en su lugar.

2. Antes de pueden realizar las operaciones de tabla, debe inicializarse el almacén local. Este es el código relevante. 
    
    **Objetivo C**:
    
    En la `QSTodoService.init` método:
    
    
            MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
            self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
    
    
    **Swift**:
    
    En la `ToDoTableViewController.viewDidLoad` método:
    
    
            let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
            let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
            self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
            client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
    

    Esto crea un almacén local mediante la interfaz de `MSCoreDataStore`, que se proporciona en el SDK de aplicaciones móviles. En su lugar puede un proporcionar un almacén local diferente mediante la implementación de la `MSSyncContextDataSource` protocolo. 
    
    Además, el primer parámetro de `MSSyncContext` se usa para especificar un controlador de conflicto. Dado que nos hemos pasado `nil`, obtenemos el controlador de conflicto de forma predeterminada, que se produce un error en cualquier conflicto.
    
3. Ahora, vamos a realizar la operación de sincronización real y obtener datos del servidor remoto.

    **Objetivo C**:
    
    `syncData`Inserta primero los nuevos cambios, a continuación, se llama `pullData` para obtener datos desde el servidor remoto. A su vez, el método `pullData` obtiene datos nuevos que coincide con una consulta:
    
    
            -(void)syncData:(QSCompletionBlock)completion
            {
                // push all changes in the sync context, then pull new data
                [self.client.syncContext pushWithCompletion:^(NSError *error) {
                    [self logErrorIfNotNil:error];
                    [self pullData:completion];
                }];
            }
    
            -(void)pullData:(QSCompletionBlock)completion
            {
                MSQuery *query = [self.syncTable query];
    
                // Pulls data from the remote server into the local table.
                // We're pulling all items and filtering in the view
                // query ID is used for incremental sync
                [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
                    [self logErrorIfNotNil:error];
    
                    // Let the caller know that we have finished
                    if (completion != nil) {
                        dispatch_async(dispatch_get_main_queue(), completion);
                    }
                }];
            }
        
        
      **Swift**:
        
        
        func onRefresh(sender: UIRefreshControl!) {
            UIApplication.sharedApplication().networkActivityIndicatorVisible = true
            
            self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
                (error) -> Void in
                
                UIApplication.sharedApplication().networkActivityIndicatorVisible = false
                
                if error != nil {
                    // A real application would handle various errors like network conditions,
                    // server conflicts, etc via the MSSyncContextDelegate
                    print("Error: \(error!.description)")
                    
                    // We will just discard our changes and keep the servers copy for simplicity
                    if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                        for opError in opErrors {
                            print("Attempted operation to item \(opError.itemId)")
                            if (opError.operation == .Insert || opError.operation == .Delete) {
                                print("Insert/Delete, failed discarding changes")
                                opError.cancelOperationAndDiscardItemWithCompletion(nil)
                            } else {
                                print("Update failed, reverting to server's copy")
                                opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                            }
                        }
                    }
                }
                self.refreshControl?.endRefreshing()
            }
        } 
    
    
    En la versión de objetivo C en `syncData`, primero llamamos `pushWithCompletion` en el contexto de sincronización. Este método es un miembro de `MSSyncContext` (en lugar de la sincronización de la tabla propia) porque se insertar los cambios en todas las tablas. Solo los registros que se han modificado de algún modo localmente (a través de las operaciones CUD) se enviarán al servidor. A continuación, en la aplicación auxiliar `pullData` se denomina qué llamadas `MSSyncTable.pullWithQuery` para recuperar datos remotos y almacenar en la base de datos local.
    
    En la versión rápida, no hay ninguna llamada a `pushWithCompletion`. Esto es porque la operación de inserción no estrictamente necesaria. Si hay cambios pendientes en el contexto de sincronización para la tabla que está realizando una operación de inserción, extracción siempre problemas push en primer lugar. Sin embargo, si tiene más de una tabla de sincronización, es mejor llamar explícitamente push para asegurarse de que todo está coherente en tablas relacionadas.
    
    En las versiones rápido y objetivo C, el método `pullWithQuery` le permite especificar una consulta para filtrar los registros que desea recuperar. En este ejemplo, la consulta recupera simplemente todos los registros en el equipo remoto `TodoItem` tabla.
    
    El segundo parámetro `pullWithQuery` es un identificador de consulta que se usa para la *sincronización incremental*. Sincronización incremental recupera sólo aquellos registros modificados desde la última sincronización, con el registro `UpdatedAt` marca de tiempo (llamado `updatedAt` en el almacén local.) El identificador de la consulta debe ser una cadena descriptiva que sea única para cada consulta lógica en la aplicación. Para cambiar de opinión-fuera de la sincronización incremental, pasar `nil` como el identificador de la consulta. Tenga en cuenta que esto puede ser potencialmente ineficaz, ya que recuperará todos los registros en cada operación de extracción.

5. El objetivo C aplicación se sincroniza cuando se modifiquen o agreguen datos, un usuario realiza el movimiento de actualización y en Inicio. La aplicación rápida sincroniza cuando un usuario realiza el gesto de actualización y en Inicio. 

Porque la sincroniza aplicación siempre que hay datos modificado (objetivo-C) o cada vez que inicia la aplicación (objetivo C & Swift), la aplicación se presupone que el usuario está en línea. En otra sección, actualizaremos la aplicación para que los usuarios pueden editar incluso cuando estén sin conexión.

## <a name="review-core-data"></a>Revisar el modelo de datos principal

Cuando se usa el almacén sin conexión de datos principal, debe definir determinados tablas y campos en el modelo de datos. La aplicación de ejemplo ya incluye un modelo de datos con el formato correcto. En esta sección se le guiará a través de estas tablas y cómo se utilizan.

- Abra **QSDataModel.xcdatamodeld**. Hay cuatro tablas definidos: tres que se utilizan en el SDK y los elementos de una tabla para el todo:     *MS_TableOperations: para realizar el seguimiento de los elementos que necesitan para sincronizarse con el servidor    * MS_TableOperationErrors: para realizar el seguimiento de los errores que se producen durante la sincronización sin conexión     *MS_TableConfig: para realizar el seguimiento de la última actualización hora de la última operación de sincronización para todas las operaciones de extracción    * TodoItem : Para almacenar los elementos todo. El sistema columnas **registroen**, **updatedAt**y la **versión** son propiedades del sistema opcional.

>[AZURE.NOTE] El SDK de aplicaciones móviles de Azure reserva nombres de columna que está con "**``**". No debe utilizar este prefijo en distinto de columnas del sistema, en caso contrario, los nombres de columna se modificará cuando se usa el servidor remoto.

- Cuando se usa la característica de sincronización sin conexión, debe definir las tablas del sistema, tal como se muestra a continuación.

    ### <a name="system-tables"></a>Tablas del sistema

    **MS_TableOperations**

    ![][defining-core-data-tableoperations-entity]

  	| Atributo  |    Tipo     |
  	|----------- |   ------    |
  	| Id.         | Entero de 64  |
  	| itemId     | Cadena      |
  	| propiedades | Datos binarios |
  	| tabla      | Cadena      |
  	| tableKind  | Entero de 16  |

    <br>**MS_TableOperationErrors**

    ![][defining-core-data-tableoperationerrors-entity]

  	| Atributo  |    Tipo     |
  	|----------- |   ------    |
  	| Id.         | Cadena      |
  	| operationId | Entero de 64 |
  	| propiedades | Datos binarios |
  	| tableKind  | Entero de 16  |

    <br>**MS_TableConfig**

    ![][defining-core-data-tableconfig-entity]

  	| Atributo  |    Tipo     |
  	|----------- |   ------    |
  	| Id.         | Cadena      |
  	| clave        | Cadena      |
  	| tipo de clave    | Entero de 64  |
  	| tabla      | Cadena      |
  	| valor      | Cadena      |

    ### <a name="data-table"></a>Tabla de datos

    **TodoItem**

  	| Atributo    |  Tipo   | Nota                                                   |
  	|-----------   |  ------ | -------------------------------------------------------|
  	| Id.           | Cadena de marcado necesaria  | clave principal en el almacén remoto                            |
  	| completar     | Valor booleano | campo de elemento de todo                                        |
  	| texto         | Cadena  | campo de elemento de todo                                        |
  	| registroen | Fecha    | (opcional) se asigna a la propiedad del sistema registroen         |
  	| updatedAt | Fecha    | (opcional) se asigna a la propiedad del sistema updatedAt         |
  	| Versión   | Cadena  | utiliza (opcional) para detectar conflictos, se asigna a la versión |


## <a name="setup-sync"></a>Cambiar el comportamiento de sincronización de la aplicación

En esta sección, modificará la aplicación para que no se sincroniza en el inicio de la aplicación o al insertar y actualizar elementos, pero solo cuando se realiza el botón gesto de actualización.

**Objetivo C**:

1. En **QSTodoListViewController.m**, cambie el método **viewDidLoad** para quitar la llamada a `[self refresh]` al final del método. Ahora, los datos no se sincronizarán con el servidor de inicio de la aplicación, pero en su lugar será el contenido del almacén local.

2. En **QSTodoService.m**, modifique la definición de `addItem` para que no se sincroniza después de inserta el elemento. Quitar el `self syncData` bloquear y reemplazar con la siguiente:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. Modificar la definición de `completeItem` arriba; Quite el bloque de `self syncData` y reemplazar con la siguiente:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

**Swift**:

1. En `viewDidLoad` en **ToDoTableViewController.swift**, comente estas dos líneas, para detener la sincronización de inicio de la aplicación. En el momento de escritura de este artículo, la aplicación Swift Todo no actualiza el servicio cuando alguien agrega o completa un elemento, solo en el inicio de la aplicación.

        self.refreshControl?.beginRefreshing()
        self.onRefresh(self.refreshControl)


## <a name="test-app"></a>Probar la aplicación

En esta sección, se conectará a una dirección URL válida para simular una situación sin conexión. Al agregar elementos de datos, llevan a cabo en el almacén de datos principal local, pero no se sincronizan con el servidor de móvil.

1. Cambiar la URL de la aplicación móvil en **QSTodoService.m** a una dirección URL válida y vuelva a ejecutar la aplicación:

    **Objetivo C** en QSTodoService.m:
    
            self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
    
    **Swift** en ToDoTableViewController.swift:

        let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")

2. Agregue algunos elementos todo. Cierre el simulador (o Forzar cierre la aplicación) y reinicie. Compruebe que se han almacenado los cambios.

3. Ver el contenido de la tabla de TodoItem remota:

    + Para Node.js back-end, vaya al [portal de Azure](https://portal.azure.com/)y, en la aplicación móvil back-end haga clic en **Tablas fácil** > **TodoItem** para ver el contenido de la `TodoItem` tabla.
    + Para .NET back-end, vea la tabla de contenido con una herramienta SQL, como SQL Server Management Studio o un cliente de resto como Fiddler o Postman.

    Compruebe que los nuevos elementos *no* se han sincronizado en el servidor:

4. Cambiar la dirección URL para el correcto en **QSTodoService.m** y vuelva a ejecutar la aplicación. Movimiento de la actualización se realiza mediante la extracción de la lista de elementos. Se muestra un control de número de progreso.

5. Volver a ver los datos de TodoItem. Ahora debe aparecer la TodoItems nuevas y modificadas.

## <a name="summary"></a>Resumen

Para admitir la característica de sincronización sin conexión, hemos usado el `MSSyncTable` interfaz e inicializado `MSClient.syncContext` con un almacén local. En este caso, el almacén local era una base de datos basados en datos básicos.

Cuando se usa un almacén de datos principal local, debe definir varias tablas con las [Propiedades del sistema correcta](#review-core-data).

Las operaciones de CRUD normales para las aplicaciones móviles de Azure funcionan como si la aplicación aún está conectada pero todas las operaciones que se producen en el almacén local.

Cuando deseamos sincronizar el almacén local con el servidor, hemos usado el `MSSyncTable.pullWithQuery`método.


## <a name="additional-resources"></a>Recursos adicionales

* [Sincronización de datos sin conexión en aplicaciones móviles de Azure]

* [Portada en la nube: sincronización sin conexión en Azure servicios móviles] \(Nota: el vídeo de servicios móviles, pero funciona la sincronización sin conexión de manera similar en las aplicaciones móviles de Azure\)

<!-- URLs. -->


[Crear una aplicación de iOS]: app-service-mobile-ios-get-started.md
[Sincronización de datos sin conexión en aplicaciones móviles de Azure]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Portada de la nube: Sincronización sin conexión en Azure servicios móviles]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
