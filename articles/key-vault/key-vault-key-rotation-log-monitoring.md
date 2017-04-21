<properties
    pageTitle="Cómo configurar clave depósito con auditoría y llevar a cabo la rotación de clave | Microsoft Azure"
    description="Use este procedimiento para obtener el programa de instalación con la rotación de clave y supervisar los registros de depósito clave"
    services="key-vault"
    documentationCenter=""
    authors="swgriffith"
    manager="mbaldwin"
    tags=""/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="jodehavi;stgriffi"/>
#<a name="how-to-setup-key-vault-with-end-to-end-key-rotation-and-auditing"></a>Cómo configurar clave depósito con auditoría y llevar a cabo la rotación de clave

##<a name="introduction"></a>Introducción

Después de crear su cámara de clave de Azure, podrá comenzar a utilizar dicho depósito para almacenar sus claves y la información confidencial. Las aplicaciones ya no necesita conservar sus claves o la información confidencial, sino se soliciten desde la cámara clave según sea necesario. Esto le permite actualizar las claves y la información confidencial sin afectar el comportamiento de la aplicación, que abre una gran variedad de posibilidades alrededor de la clave y el comportamiento de administración secreto.

En este artículo se recorre un ejemplo de aprovechamiento depósito de clave de Azure para almacenar un secreto, en este caso una clave de cuenta de almacenamiento de Azure que se tiene acceso a una aplicación. Mostrará también la implementación de un giro programado de dicha clave de cuenta de almacenamiento. Por último, le guiará a través de una demostración de cómo supervisar los registros de auditoría del depósito clave y generar alertas cuando se realizan las peticiones inesperadas.

> \[AZURE. Nota\] este tutorial no está pensado para explicar detalladamente el conjunto inicial hacia arriba de la cámara de clave de Azure. Para esta información, vea [Introducción a Azure clave depósito](key-vault-get-started.md). O bien, para obtener instrucciones de la interfaz de línea de comandos de varias plataformas, vea [este tutorial equivalente](key-vault-manage-with-cli.md).

##<a name="setting-up-keyvault"></a>Configurar KeyVault

Para habilitar una aplicación recuperar un secreto de la cámara de clave de Azure, primero debe crear el secreto y cárguelo en su cámara. Esto se puede conseguir fácilmente a través de PowerShell como se muestra a continuación.

Iniciar una sesión de Azure PowerShell e inicie sesión en su cuenta de Azure con el siguiente comando:

```powershell
Login-AzureRmAccount
```

En la ventana emergente del explorador, escriba su nombre de usuario de la cuenta de Azure y la contraseña. PowerShell Azure obtendrá todas las suscripciones que están asociados a esta cuenta y de forma predeterminada, se utiliza la primera.

Si tiene varias suscripciones, debe especificar una específica que se usó para crear su depósito de clave de Azure. Escriba lo siguiente para ver las suscripciones para su cuenta:

```powershell
Get-AzureRmSubscription
```

A continuación, para especificar la suscripción que está asociada a su cámara clave que inicie sesión, escriba:

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID> 
```

Como en este artículo se muestra cómo almacenar de una clave de cuenta de almacenamiento como un secreto, debe obtener dicha clave de cuenta de almacenamiento.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Después de recuperar su secreto, en este caso la clave de cuenta de almacenamiento, debe convertir una cadena segura y, a continuación, crear un secreto con ese valor en su cámara clave.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
A continuación se desea obtener el URI para el secreto que acaba de crear. Cuando está llamando el depósito clave para recuperar el secreto utilizará en un paso posterior. Ejecute el siguiente comando PowerShell y anote el valor de 'Id', que es el URI secreto.

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

##<a name="setting-up-application"></a>Configuración de aplicación

Ahora que ya tiene un secreto almacenado desea recuperar ese secreto y utilizarlo desde el código. Hay algunos pasos necesarios para ello, la primera y más importantes que se registrar la aplicación con Azure Active Directory y, a continuación, indicando que Azure clave depósito a la información de la aplicación para que pueden permitir las solicitudes de la aplicación.

> \[AZURE. Nota\] debe crearse la aplicación en el mismo inquilino de Azure Active Directory como su depósito de clave. 

Abrir la pestaña de aplicaciones de Azure Active Directory por primera vez

![Aplicaciones abiertas en Azure AD](./media/keyvault-keyrotation/AzureAD_Header.png)

Elija 'Agregar' para agregar una nueva aplicación para su Azure AD

![Elija Agregar aplicación](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

Deje el tipo de aplicación como 'Web o aplicación WEB API' y asigne un nombre a la aplicación.

![Nombre de la aplicación](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

Dar a la aplicación un 'inicio de sesión de URL' y un identificador de la URI del aplicación. Estos pueden ser cualquier cosa que desee para esta demostración y se pueden cambiar posteriormente si es necesario.

![Proporcionar URI necesarios](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

Una vez que la aplicación se agrega a Azure AD, aparecerá en la página de la aplicación. Desde ese punto haga clic en la ficha 'Configurar' y, a continuación, busque y copie el valor del identificador del cliente. Anote el identificador de cliente en pasos posteriores.

A continuación debe generar una clave de la aplicación para que pueda interactuar con su Azure AD. Se pueden crear en la sección 'Claves' en la ficha 'Configuración'. Tome nota de la la clave recién generada desde la aplicación de Azure AD para su uso en un paso posterior.

![Teclas de aplicación de Azure AD](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Antes de establecer las llamadas desde la aplicación en depósito de clave debe informar a la cámara clave sobre la aplicación y su ' permisos. El comando siguiente toma el nombre de la cámara y el identificador de cliente de la aplicación de Azure AD y conceda acceso 'Get' a su depósito clave para la aplicación.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

En este momento está listo para comenzar a crear las llamadas de la aplicación. En la aplicación primero deberá instalar los paquetes de NuGet necesarios para interactuar con depósito de clave de Azure y Azure Active Directory. Desde la consola del Administrador de paquetes de Visual Studio, escriba los siguientes comandos. Tenga en cuenta que en la escritura de este artículo de la versión actual del paquete de Active Directory es 3.10.305231913, por lo que es posible que desee confirmar la última versión y actualice.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

En el código de la aplicación, cree una clase que contenga el método para la autenticación de Active Directory. En este ejemplo que la clase se denomina 'Utilidades'. A continuación, debe agregar el siguiente mediante.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

A continuación, agregue el siguiente método para recuperar el token JWT de Azure AD. Para mantenimiento que desee mover el disco duro codificada valores de cadena en la configuración de aplicación o web.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Por último, puede agregar el código necesario para llamar a depósito clave y recuperar su valor secreto. Primero debe agregar la siguiente instrucción using.

```csharp
using Microsoft.Azure.KeyVault;
```

A continuación agregará el método llama invocar depósito clave y recuperar su secreto. En este método le proporcionará el secreto URI que guardó en el paso anterior. Observe el uso del método GetToken de la clase de utilidades creado anteriormente.
    
```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Cuando se ejecuta la aplicación, ahora puede autenticar a Azure Active Directory y, a continuación, recuperar el valor secreto de la cámara de clave de Azure.

##<a name="key-rotation-using-azure-automation"></a>Rotación de clave mediante la automatización de Azure

Existen varias opciones para implementar una estrategia de rotación para los valores que se guardan como información confidencial de Azure clave depósito. Información confidencial se puede girar como parte de un proceso manual, podrá hacer girar mediante programación aprovechando llamadas a la API o se pueden girar por medio de una secuencia de comandos de automatización. Para el propósito de este artículo se se a aprovechar Azure PowerShell combinada con la automatización de Azure para cambiar una tecla de acceso de la cuenta de almacenamiento de Azure y, a continuación, actualizaremos un secreto clave depósito con esa clave nueva. 

Para permitir la automatización de Azure definir valores secretos en su cámara clave debe obtener el identificador de cliente para la conexión con el nombre 'AzureRunAsConnection' se creó al establecer su instancia de automatización de Azure. Puede ir a este ID eligiendo 'Activos' desde la instancia de automatización de Azure. Desde allí seleccione 'Conexiones' y, a continuación, seleccione el principio del servicio de 'AzureRunAsConnection'. Desea anote el ID. 

![Identificador de cliente de automatización de Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Mientras está en la ventana de activos que desea elegir 'Módulos'. Desde módulos que seleccione 'Galería' y, a continuación, busque y 'Importar' versiones actualizadas de cada uno de los siguientes módulos.

    Azure
    Azure.Storage   
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage
    
> \[AZURE. Nota\] en la escritura de este artículo solo anteriores anotó módulos que necesita actualizarse para la secuencia de comandos compartido debajo. Si encuentra que haya errores en el trabajo de automatización, debe confirmar que tiene todos los módulos necesarios y sus dependencias importados.

Después de recuperar el identificador de aplicación para la conexión de automatización de Azure, debe saber la cámara de clave de Azure que esta aplicación tiene acceso al actualizar la información confidencial en su cámara. Esto se puede conseguir con el siguiente comando PowerShell.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

A continuación, se selecciona el recurso 'Runbooks' en su instancia de automatización de Azure y seleccione 'Agregar un Runbook'. Seleccione creación rápida. Asigne un nombre a su runbook y seleccione 'PowerShell' como el tipo de runbook. Si desea agregar una descripción. Por último, haga clic en "Crear".

![Crear Runbook](./media/keyvault-keyrotation/Create_Runbook.png)

En el panel del editor para su nuevo runbook se pegue el siguiente script de PowerShell.

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Optionally you may set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -StorageAccountName $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

Desde el panel del editor puede elegir 'Prueba panel' para probar la secuencia de comandos. Una vez que se está ejecutando el script sin error puede seleccionar la opción 'Publicar' y, a continuación, puede aplicar una programación para runbook en el panel de configuración de runbook.

##<a name="key-vault-auditing-pipeline"></a>Canalización de auditoría de clave de cámara

Cuando configura un depósito de clave de Azure puede activar la auditoría para recopilar registros de solicitudes de acceso realizadas en depósito de clave. Estos registros se almacenan en una cuenta de Azure almacenamiento designada y, a continuación, se pueden extraer, supervisar y analizar. A continuación recorre un escenario que aprovecha las funciones de Azure, Azure lógica de aplicaciones y depósito de clave de registros de auditoría para crear un proceso para enviar un correo electrónico cuando se recupera información confidencial de la caja fuerte mediante una aplicación que coincida con el identificador de aplicación de la aplicación web.

En primer lugar, debe habilitar el registro en su cámara clave. Esto puede hacerse a través de los siguientes comandos de PowerShell (pueden ver los detalles completos [aquí](key-vault-logging.md)):

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>' 
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Una vez habilitada, los registros de auditoría iniciará recopilar en la cuenta de almacenamiento designado. Estos registros contendrá eventos sobre cómo y cuando se tiene acceso a su clave de depósitos y por quién. 

> \[AZURE. Nota\] puede tener acceso a su información de registro máximo, 10 minutos después de la clave depósito operación. En la mayoría de los casos, es más rápido que esto.

El siguiente paso es [crear una cola de Bus de servicio de Azure](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Se trata de donde se insertan los registros de auditoría del depósito clave. Una vez en la cola de la aplicación de la lógica se les recoger y actuar en ellos. Para crear un Bus de servicio es relativamente sencillo y a continuación se muestran los pasos de alto niveles:

1. Crear un espacio de nombres de Bus de servicio (si ya tiene que desea utilizar para ello, a continuación, vaya al paso 2).
2. Busque el Bus de servicio en el portal y seleccione el espacio de nombres que desea crear la cola de.
3. Seleccione nuevo y elija Bus de servicio -> cola y escriba la información requerida.
4. Capturar información de conexión de servicio Bus elegir el espacio de nombres y haciendo clic en _Información de conexión_. Necesitará esta información para el elemento siguiente.

A continuación, podrá [crear una función de Azure](../azure-functions/functions-create-first-azure-function.md) para sondear los registros de depósito clave dentro de la cuenta de almacenamiento y seleccione eventos. Se trata de una función que se activa en una programación.

Crear una función de Azure (elija Nuevo -> aplicación de la función en el portal). Durante la creación de puede utilizar un plan de hospedaje existente o cree uno nuevo. También puede elegir para alojar dinámico. Pueden encontrar más detalles en función de opciones de alojamiento [aquí](../azure-functions/functions-scale.md).

Cuando se crea la función de Azure, desplácese hasta él y elija un temporizador de la función y C\# , a continuación, haga clic en **crear** desde la pantalla de inicio.

![Módulo de inicio de funciones de Azure](./media/keyvault-keyrotation/Azure_Functions_Start.png)

En la pestaña _desarrollar_ , reemplace el código de run.csx con lo siguiente:

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging; 
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log) 
{ 
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //required to order by time as they may not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob) 
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```
> \[AZURE. Nota\] Asegúrese de reemplazar las variables en el código anterior para que apunten a su cuenta de almacenamiento donde se escriben los registros del depósito de clave, el Bus de servicio que creó anteriormente y la ruta de acceso específico a los registros de almacenamiento de depósito clave.

La función toma el último archivo de registro de la cuenta de almacenamiento donde se escriben los registros del depósito de clave, toma los eventos más recientes de ese archivo y les inserta a una cola de Bus de servicio. Dado que un único archivo podría tener varios eventos, por ejemplo, más de una hora completa, a continuación, creamos un archivo de _sync.txt_ la función también busca en para determinar la marca de tiempo del último evento que se ha elegido el. Esto se asegurará de que se no push el mismo evento varias veces. Este archivo _sync.txt_ contiene una marca de tiempo para el último evento encontrado. Los registros, cuando se carga, tienen que ordenarán en función de la marca de tiempo para asegurarse de que están correctamente ordenados.

Para esta función, se hace referencia un par de bibliotecas adicionales que no están disponibles fuera de la caja en las funciones de Azure. Para incluir estos, debemos funciones de Azure para acceder a ellas mediante nuget. Elija la opción _Ver archivos_ 

![Ver la opción de archivos](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

y agregue un nuevo archivo llamado _project.json_ con el contenido siguiente:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```
Al _Guardar_ este activará las funciones de Azure para descargar los archivos binarios necesarios. 

Cambie a la pestaña **integrar** y asigne el parámetro temporizador un nombre significativo para usar dentro de la función. En el código anterior, se espera que el temporizador de llamar _myTimer_. Especificar una [expresión CRON](../app-service-web/web-sites-create-web-jobs.md#CreateScheduledCRON) como sigue: 0 \* \* \* \* \* para el temporizador que hará que la función que se ejecuta una vez por minuto. 

En la misma pestaña **integrar** , agregue una entrada que será del tipo de _Almacenamiento de blobs de Windows Azure_. Esto se seleccione el archivo _sync.txt_ que contiene la marca de hora del último evento bloqueado por la función. Esto estará disponible dentro de la función por el nombre del parámetro. En el código anterior, la entrada de almacenamiento de blobs de Windows Azure espera el nombre de parámetro se _inputBlob_. Elija la cuenta de almacenamiento donde reside el archivo _sync.txt_ (puede ser la misma o una cuenta de almacenamiento diferente) y en el campo ruta de acceso, proporcione la ruta de acceso donde se encuentra el archivo, en el formato de {container-name}/path/to/sync.txt.

Agregue un resultado que será del tipo de resultado de _Almacenamiento de blobs de Windows Azure_ . Esto también se seleccione el archivo _sync.txt_ que acaba de definir en la entrada. Se utilizará la función escribir la marca de hora del último evento mirado. El código anterior espera este parámetro al que se llama _outputBlob_.

En este momento, la función está lista. Asegúrese de que volver a la pestaña de **desarrollar** y _guarde_ el código. Compruebe la ventana de resultados para los errores de compilación y corrija los según corresponda. Si se compila, ahora debe ejecutar el código y cada minuto se compruebe los registros del depósito de clave y los eventos en la cola de Bus de servicio definido de inserción. Debería ver información de registro y escribir en la ventana de registro cada vez que se activa la función.

###<a name="azure-logic-app"></a>Aplicación de la lógica de Azure

A continuación debemos crear una aplicación de la lógica de Azure que recoger los eventos que la función es presión a la cola de Bus de servicio, analizar el contenido y enviar un correo electrónico basado en una condición que coincide.

[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) yendo a New -> lógica de aplicación. 

Una vez creada la aplicación lógica, desplácese hasta él y elija _Editar_. En el editor de la aplicación de la lógica, elija la que API administrada de la _Cola de Bus de servicio_ e introduzca sus credenciales de Bus de servicio para conectarse a la cola.

![Bus de servicios de aplicación de la lógica de Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

A continuación, elija Agregar _una condición_. En la condición, cambie al _editor avanzado_ y escriba lo siguiente, reemplazando la id_aplicación con el id_aplicación real de la aplicación web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Esta expresión esencialmente devolverá **Falso** si la propiedad **ID** del evento entrante (que es el cuerpo del mensaje de Bus de servicio) no es el **ID** de la aplicación. 

Ahora, cree una acción en la opción _Si no, no haga nada …_ .

![Aplicación de Azure lógica elija acción](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Para la acción, elija _Office 365: enviar correo electrónico_. Rellene los campos para crear un correo electrónico para enviar cuando la condición definida devuelve false. Si no tiene Office 365 puede mirar alternativas para lograr los mismos.

En este momento tiene una canalización de llevar a cabo que, una vez por minuto, buscará nuevos registros de auditoría del depósito de clave. Encuentra todos los registros nuevos, insertarlos a una cola de Bus de servicio. La aplicación de la lógica se activará tan pronto como un nuevo mensaje entre en la cola y si el ID dentro del evento no coincide con el id de aplicación de la aplicación de llamada, a continuación, enviar un correo electrónico. 
