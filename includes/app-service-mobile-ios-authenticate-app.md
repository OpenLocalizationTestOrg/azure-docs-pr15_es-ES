**Objetivo C**: 

1. En el Mac, abra _QSTodoListViewController.m_ en Xcode y agregue el método siguiente. Cambiar _google_ a _microsoftaccount_, _twitter_, _facebook_o _windowsazureactivedirectory_ si no usa Google como su proveedor de identidades. Si usa Facebook, [necesitará blanca Facebook dominios en la aplicación](https://developers.facebook.com/docs/ios/ios9#whitelist).

            - (void) loginAndGetData
            {
                MSClient *client = self.todoService.client;
                if (client.currentUser != nil) {
                    return;
                }
            
                [client loginWithProvider:@"google" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                }];
            }


2. Reemplazar `[self refresh]` en `viewDidLoad` en _QSTodoListViewController.m_ con lo siguiente:

            [self loginAndGetData];

3. Presione _Ejecutar_ para iniciar la aplicación y, a continuación, inicie sesión. Cuando inicia sesión, debería poder ver la lista de tareas pendientes y realizar actualizaciones.

**Swift**:

1. En el Mac, abra _ToDoTableViewController.swift_ en Xcode y agregue el método siguiente. Cambiar _google_ a _microsoftaccount_, _twitter_, _facebook_o _windowsazureactivedirectory_ si no usa Google como su proveedor de identidades. Si usa Facebook, [necesitará blanca Facebook dominios en la aplicación](https://developers.facebook.com/docs/ios/ios9#whitelist).
        
            func loginAndGetData() {
                
                guard let client = self.table?.client where client.currentUser == nil else {
                    return
                }
                
                client.loginWithProvider("google", controller: self, animated: true) { (user, error) in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                }
            }


2. Quitar las líneas `self.refreshControl?.beginRefreshing()` y `self.onRefresh(self.refreshControl)` al final de `viewDidLoad()` en _ToDoTableViewController.swift_. Agregar una llamada a `loginAndGetData()` en su lugar:

            loginAndGetData()

3. Presione _Ejecutar_ para iniciar la aplicación y, a continuación, inicie sesión. Cuando inicia sesión, debería poder ver la lista de tareas pendientes y realizar actualizaciones.
