<properties
    pageTitle="Introducción a Azure CDN SDK para Node.js | Microsoft Azure"
    description="Obtenga información sobre cómo escribir aplicaciones Node.js administrar CDN de Azure."
    services="cdn"
    documentationCenter="nodejs"
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="casoper"/>

# <a name="get-started-with-azure-cdn-development"></a>Introducción a desarrollo CDN de Azure

> [AZURE.SELECTOR]
- [Node.js](cdn-app-dev-node.md)
- [.NET](cdn-app-dev-net.md)

Puede usar el [SDK de Azure CDN para Node.js](https://www.npmjs.com/package/azure-arm-cdn) para automatizar la creación y administración de perfiles CDN y extremos.  Este tutorial le guía a través de la creación de una aplicación de consola Node.js simple que muestra varias de las operaciones disponibles.  Este tutorial no está pensado para describir todos los aspectos de Azure CDN SDK para Node.js en detalle.

Para completar este tutorial, debe disponer de [Node.js](http://www.nodejs.org) **4.x.x** o posterior instalado y configurado.  Puede usar cualquier editor de texto que desea crear la aplicación Node.js.  Para escribir en este tutorial, utiliza el [Código de Visual Studio](https://code.visualstudio.com).  

> [AZURE.TIP] El [completado proyecto a partir de este tutorial](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) está disponible para su descarga en MSDN.

[AZURE.INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Crear un proyecto y agregar NPM dependencias

Ahora que hemos creado un grupo de recursos para nuestros perfiles CDN y nuestro permiso de aplicación de Azure AD para administrar perfiles CDN y extremos dentro del grupo, podemos comenzar a crear nuestra aplicación.

Crear una carpeta para almacenar la aplicación.  Desde una consola con las herramientas de Node.js en la ruta de acceso actual, establezca su ubicación actual en la nueva carpeta y ejecutar inicializarse el proyecto:
    
    npm init
    
A continuación, se presenta una serie de preguntas iniciar el proyecto.  Para el **punto de entrada**, este tutorial usa *app.js*.  Puede ver mi otras opciones en el ejemplo siguiente.

![Resultado de inicialización NPM](./media/cdn-app-dev-node/cdn-npm-init.png)

Nuestro proyecto está ahora inicializado con un archivo de *packages.json* .  Nuestro proyecto va a utilizar algunas bibliotecas Azure incluidos en NPM paquetes.  Usaremos el tiempo de ejecución del cliente de Azure para Node.js (ms-resto de azure) y la biblioteca de cliente de Azure CDN para Node.js (brazo de azure cd).  Vamos a agregarlos al proyecto como dependencias.
 
    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

Después de que se realizan los paquetes de instalar la *package.json* archivo debe ser similar a este ejemplo (versión números pueden variar):

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

Por último, mediante el editor de texto, cree un archivo de texto en blanco y guárdelo en la raíz de la carpeta de proyecto como *app.js*.  Ahora podemos empezar a escribir código.

## <a name="requires-constants-authentication-and-structure"></a>Requiere autenticación, constantes y la estructura

Con *app.js* abierto en nuestro editor, pongamos en marcha la estructura básica de nuestro programa escrito.

1. Agregar el "requiere" para nuestros paquetes NPM en la parte superior con los siguientes elementos:

    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```

2. Es necesario definir algunas constantes que usarán nuestros métodos.  Agregue lo siguiente.  Asegúrese de reemplazar los marcadores de posición, incluida la ** &lt;corchetes angulares&gt;**, con sus propios valores según sea necesario.

    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";

    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```

3. A continuación, deberá crear una instancia del cliente de administración de CDN y asígnele nuestras credenciales.

    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
    
    Si está utilizando la autenticación de usuario individual, estas dos líneas tendrá un aspecto ligeramente distintas.

    >[AZURE.IMPORTANT] Use este código de ejemplo únicamente si opta por la autenticación de usuario individual en lugar de un servicio principal.  Tenga cuidado de proteger sus credenciales de usuario individual y mantenerlas en secreto.

    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```

    Asegúrese de reemplazar los elementos de ** &lt;corchetes angulares&gt; ** con la información correcta.  Para `<redirect URI>`, usar la redirección URI especificado cuando se registra la aplicación en Azure AD.
    

4.  Nuestra aplicación de consola Node.js va a llevar algunos parámetros de línea de comandos.  Vamos a validar que al menos un parámetro se ha pasado.

    ```javascript
    //Collect command-line parameters
    var parms = process.argv.slice(2);

    //Do we have parameters?
    if(parms == null || parms.length == 0)
    {
        console.log("Not enough parameters!");
        console.log("Valid commands are list, delete, create, and purge.");
        process.exit(1);
    }
    ```

5. Eso nos lleva a la parte principal de nuestro programa, donde se separarlos a otras funciones en función de los parámetros que se han pasado.

    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;

        case "create":
            cdnCreate();
            break;
        
        case "delete":
            cdnDelete();
            break;

        case "purge":
            cdnPurge();
            break;

        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```

6.  En varios lugares en nuestro programa, debemos para asegurarse de que el número correcto de parámetros se ha pasado y mostrar ayuda si no son correctos.  Vamos a crear funciones para hacerlo.

    ```javascript
    function requireParms(parmCount) {
        if(parms.length < parmCount) {
            usageHelp(parms[0].toLowerCase());
            process.exit(1);
        }
    }

    function usageHelp(cmd) {
        console.log("Usage for " + cmd + ":");
        switch(cmd)
        {
            case "list":
                console.log("list profiles");
                console.log("list endpoints <profile name>");
                break;

            case "create":
                console.log("create profile <profile name>");
                console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
                break;
            
            case "delete":
                console.log("delete profile <profile name>");
                console.log("delete endpoint <profile name> <endpoint name>");
                break;

            case "purge":
                console.log("purge <profile name> <endpoint name> <path>");
                break;

            default:
                console.log("Invalid command.");
        }
    }
    ```

7. Por último, las funciones que se va a usar en el cliente de administración de CDN están asincrónicas, por lo que necesitan un método de llamada cuando haya terminado.  Vamos a hacer que puede mostrar el resultado desde el cliente de administración de CDN (si existe) y salir del programa correctamente.

    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Ahora que se ha escrito la estructura básica de nuestro programa, debemos creamos las funciones llamadas basándose en nuestras parámetros.

## <a name="list-cdn-profiles-and-endpoints"></a>Lista CDN perfiles y extremos

Comencemos con código de nuestra perfiles existentes y los extremos de la lista.  Mis comentarios de código proporcionan la sintaxis esperada para saber dónde va cada parámetro.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Crear extremos y perfiles CDN

A continuación, pasaremos a crear las funciones para crear perfiles y extremos.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>Purgar un punto final

Suponiendo que se ha creado el extremo, una tarea común que podría desear realizar en nuestro programa purga contenido en el extremo.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Eliminar extremos y perfiles CDN

La función última que incluimos elimina extremos y perfiles.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Ejecutar el programa

Ahora podemos ejecutar nuestro programa Node.js con nuestro depurador favorito o en la consola.

> [AZURE.TIP] Si está usando código de Visual Studio como depurador, debe configurar su entorno para pasar los parámetros de línea de comandos.  Código de Visual Studio para ello en el archivo **lanuch.json** .  Buscar una propiedad denominada **argumentos** y agregar una matriz de valores de cadena para los parámetros de la forma que tiene un aspecto similar a este: `"args": ["list", "profiles"]`.

Comencemos con una lista de nuestros perfiles.

![Perfiles de lista](./media/cdn-app-dev-node/cdn-list-profiles.png)

Volver tenemos una matriz vacía.  Puesto que no hay ningún perfil en el grupo de recursos, que se espera.  Ahora vamos a crear un perfil.

![Crear perfil](./media/cdn-app-dev-node/cdn-create-profile.png)

Ahora, vamos a agregar un extremo.

![Crear punto final](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Por último, vamos a eliminar el perfil.

![Eliminar perfil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Pasos siguientes

Para ver el proyecto completado en este tutorial, [descargar los datos de ejemplo](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Para ver la referencia de Azure CDN SDK para Node.js, vea la [referencia](http://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Para buscar documentación adicional en el SDK de Azure Node.js, vea la [referencia completa](http://azure.github.io/azure-sdk-for-node/).

Administrar los recursos de CDN con [PowerShell](./cdn-manage-powershell.md).

