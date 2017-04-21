<properties
   pageTitle="Cómo usar Power BI incrustado con resto | Microsoft Azure"
   description="Aprenda a usar Power BI incrustado con el resto "
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="how-to-use-power-bi-embedded-with-rest"></a>Cómo usar Power BI incrustado con el resto


## <a name="power-bi-embedded-what-it-is-and-what-its-for"></a>Power BI incrustados: Qué es y para qué sirve
Una descripción general de Power BI incrustado se describe en el [sitio de Power BI incrustado](https://azure.microsoft.com/services/power-bi-embedded/)de oficial, pero Echemos un vistazo rápido antes de ver los detalles sobre cómo usar con el resto.

Es muy sencillo, realmente. Fabricantes independientes de software con qué frecuencia desea usar las visualizaciones de datos dinámicos de [Power BI](https://powerbi.microsoft.com) en su propia aplicación como bloques de creación de la interfaz de usuario.

Pero, sabe, la incrustación de informes de Power BI o mosaicos en su página web ya está posible sin el servicio Power BI incrustado Azure, mediante la **API de Power BI**. Cuando desee compartir los informes en la misma organización, puede insertar los informes con autenticación de Azure AD. El usuario que vea los informes debe iniciar sesión con su propia cuenta de Azure AD. Cuando desee compartir los informes para todos los usuarios (incluidos los usuarios externos), puede incrustar simplemente con acceso anónimo.

Pero, verá este sencillo incrustar solución bastante no se ajusten a las necesidades de una aplicación de ISV.
Muchas aplicaciones ISV necesitan para prestar los datos para sus propios clientes, no necesariamente a los usuarios de su organización. Por ejemplo, si se realiza algún servicio para la empresa A y empresa B, los usuarios de la empresa A deben solo vea datos para su propia empresa A. Es decir, el soporte para múltiples usuarios es necesaria para la entrega.

La aplicación de ISV también puede ofrecer sus propios métodos de autenticación como la autenticación de formularios, autenticación básica, etcetera... A continuación, la incrustación solución debe colaborar con este método de autenticación existente con seguridad. También es necesario para que los usuarios puedan usar las aplicaciones de ISV sin la compra adicional o licencia de suscripción de Power BI.

 **Power BI incrustado** está diseñado para exactamente con estos tipos de escenarios de ISV. Así que ahora que tenemos que introducción rápida de forma, vamos a ver algunos detalles

Puede usar .NET \(C#) o SDK Node.js, crear fácilmente la aplicación con Power BI incrustado. Pero, en este artículo, explicaremos sobre flujo HTTP \(incluido niveles de autenticación) de Power BI sin SDK. Descripción este flujo, puede crear su aplicación **con cualquier lenguaje de programación**y pueden comprender con la esencia de Power BI incrustado.

## <a name="create-power-bi-workspace-collection-and-get-access-key-provisioning"></a>Colección de área de trabajo de crear Power BI y obtener acceso clave \(aprovisionamiento)
Power BI incrustado es uno de los servicios de Azure. Solo el proveedor de software que usa el Portal de Azure se cargará las cuotas de uso de \(por cada hora la sesión de usuario), y el usuario que ve el informe no se cargan o incluso requiere una suscripción de Azure.
Antes de iniciar el desarrollo de aplicaciones, debemos creamos la **colección de área de trabajo de Power BI** usando el Portal de Azure.

Cada área de trabajo de Power BI incrustado es el área de trabajo de cada cliente (inquilino) y podemos agregar muchas áreas de trabajo de cada colección de área de trabajo. Se usa la misma tecla de acceso en cada colección de área de trabajo. Efecto, la colección de área de trabajo es el límite de seguridad para Power BI incrustado.

![](media\power-bi-embedded-iframe\create-workspace.png)

Al finalizar la creación de la colección de área de trabajo, copie la tecla de acceso de Portal de Azure.

![](media\power-bi-embedded-iframe\copy-access-key.png)

> [AZURE.NOTE] También podemos aprovisionar la colección de área de trabajo y obtener la clave de acceso a través de la API de REST. Para obtener más información, vea [API de proveedor de recursos de Power BI](https://msdn.microsoft.com/library/azure/mt712306.aspx).

## <a name="create-pbix-file-with-power-bi-desktop"></a>Crear archivo de .pbix con Power BI Desktop
A continuación, debemos creamos la conexión de datos e informes incrustar.
Para esta tarea, no hay ninguna programación o el código. Simplemente utilizamos Power BI Desktop.
En este artículo, no atraviesan los detalles sobre cómo usar Power BI Desktop. Si necesita alguna ayuda aquí, consulte [Introducción a Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/). En nuestro ejemplo, usaremos solo los datos de [Ejemplo de análisis por menor](https://powerbi.microsoft.com/documentation/powerbi-sample-datasets/).

![](media\power-bi-embedded-iframe\power-bi-desktop-1.png)

## <a name="create-a-power-bi-workspace"></a>Crear un área de trabajo de Power BI

Ahora que el aprovisionamiento se hace, vamos a empezar a crear área de trabajo de un cliente en la colección de área de trabajo a través de las API de REST. La siguiente HTTP POST solicitar (REST) es crear el área de trabajo de nuestra colección de área de trabajo existente. En nuestro ejemplo, el nombre de la colección de área de trabajo es **mypbiapp**.
Hemos establecido la tecla de acceso que se copió anteriormente, como **AppKey**. Es muy sencillo de autenticación.

**Solicitud HTTP**

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces
Authorization: AppKey MpaUgrTv5e...
```

**Respuesta HTTP**

```
HTTP/1.1 201 Created
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces
RequestId: 4220d385-2fb3-406b-8901-4ebe11a5f6da

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/$metadata#workspaces/$entity",
  "workspaceId": "32960a09-6366-4208-a8bb-9e0678cdbb9d",
  "workspaceCollectionName": "mypbiapp"
}
```

El devuelto **workspaceId** se utiliza para las siguientes llamadas API subsiguientes. Nuestra aplicación debe conservar este valor.

## <a name="import-pbix-file-into-the-workspace"></a>Importar archivo de .pbix en el área de trabajo
Cada área de trabajo puede hospedar un solo archivo de Power BI Desktop con un conjunto de datos \(incluida la configuración de origen de datos) e informes. Podemos importar el archivo .pbix al área de trabajo, tal como se muestra en el código siguiente. Como puede ver, nos podemos cargar el archivo binario de archivo .pbix con varias partes MIME en http.

El fragmento de uri **32960a09-6366-4208-a8bb-9e0678cdbb9d** es la workspaceId y de parámetro de consulta **datasetDisplayName** es el nombre del conjunto de datos para crear. El conjunto de datos creado contiene todos los datos relacionados con defectos en .pbix archivos tales como los datos importados, el puntero al origen de datos, etcetera...

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports?datasetDisplayName=mydataset01
Authorization: AppKey MpaUgrTv5e...
Content-Type: multipart/form-data; boundary="A300testx"

--A300testx
Content-Disposition: form-data

{the content (binary) of .pbix file}
--A300testx--
```

Puede ejecutar esta tarea de importación durante un tiempo. Cuando haya terminado, la aplicación puede solicitar el estado de la tarea con el identificador de importación. En nuestro ejemplo, el identificador de importación es **4eec64dd-533b-47c3-a72c-6508ad854659**.

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659?tenantId=myorg
RequestId: 658bd6b4-b68d-4ec3-8818-2a94266dc220

{"id":"4eec64dd-533b-47c3-a72c-6508ad854659"}
```

La siguiente pregunta estado usando este id de importación:

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659
Authorization: AppKey MpaUgrTv5e...
```

Si la tarea no está completa, la respuesta HTTP podría ser similar a esta:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: 614a13a5-4de7-43e8-83c9-9cd225535136

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Publishing",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "name": "mydataset01"
}
```

Si la tarea está completada, la respuesta HTTP podría ser más similar a esta:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: eb2c5a85-4d7d-4cc2-b0aa-0bafee4b1606

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Succeeded",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "reports": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://app.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c"
    }
  ],
  "datasets": [
    {
      "id": "458e0451-7215-4029-80b3-9627bf3417b0",
      "name": "mydataset01",
      "tables": [
      ],
      "webUrl": "https://app.powerbi.com/datasets/458e0451-7215-4029-80b3-9627bf3417b0"
    }
  ],
  "name": "mydataset01"
}
```

## <a name="data-source-connectivity-and-multi-tenancy-of-data"></a>Conectividad de origen de datos \(y multiempresa de datos)
Mientras se importan casi todos los defectos en el archivo .pbix nuestra área de trabajo, no son las credenciales de orígenes de datos. Como resultado, cuando se utiliza **el modo DirectQuery**, el informe incrustado no se muestran correctamente. Sin embargo, cuando se utiliza el **modo de importación**, podemos ver el informe con los datos importados existentes. En este caso, deberíamos establecer la credencial con los pasos siguientes mediante llamadas del resto.

En primer lugar, debemos obtener el origen de datos de la puerta de enlace. Sabemos que el **identificador** de conjunto de datos es el identificador devuelto previamente.

**Solicitud HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.GetBoundGatewayDatasources
Authorization: AppKey MpaUgrTv5e...
```

**Respuesta HTTP**

```
GET HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: 574b0b18-a6fa-46a6-826c-e65840cf6e15

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#gatewayDatasources",
  "value": [
    {
      "id": "5f7ee2e7-4851-44a1-8b75-3eb01309d0ea",
      "gatewayId": "ca17e77f-1b51-429b-b059-6b3e3e9685d1",
      "datasourceType": "Sql",
      "connectionDetails": "{\"server\":\"testserver.database.windows.net\",\"database\":\"testdb01\"}"
    }
  ]
}
```

Con el identificador de puerta de enlace devuelta y el identificador de origen de datos \(ver el anterior **gatewayId** y el **identificador** del resultado devuelto), podemos cambiar la credencial de este origen de datos como sigue:

**Solicitud HTTP**

```
PATCH https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/gateways/ca17e77f-1b51-429b-b059-6b3e3e9685d1/datasources/5f7ee2e7-4851-44a1-8b75-3eb01309d0ea
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "credentialType": "Basic",
  "basicCredentials": {
    "username": "demouser",
    "password": "P@ssw0rd"
  }
}
```

**Respuesta HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/octet-stream
RequestId: 0e533c13-266a-4a9d-8718-fdad90391099
```

En producción, podemos establecer también la cadena de conexión diferente para cada área de trabajo con la API de REST. \(es decir, nos podemos separar la base de datos para cada clientes.)

La siguiente está cambiando la cadena de conexión de origen de datos mediante el resto.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.SetAllConnections
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "connectionString": "data source=testserver02.database.windows.net;initial catalog=testdb02;persist security info=True;encrypt=True;trustservercertificate=False"
}
```

O bien, podemos utilizar seguridad a nivel de fila en Power BI incrustado y nos podemos separar los datos de cada usuario de un informe. As a result, nos podemos aprovisionar cada informe de cliente con el mismo .pbix \(interfaz de usuario, etcetera.) y distintos orígenes de datos.

> [AZURE.NOTE] Si está usando el **modo de importación** en lugar de en **el modo DirectQuery**, no hay ninguna manera de actualizar modelos de API. Y aún no se admiten orígenes de datos locales a través de la puerta de enlace de Power BI en Power BI incrustado. Sin embargo, que realmente desea seguir controlando el [blog de Power BI](https://powerbi.microsoft.com/blog/) para novedades y cuál es en futuras versiones.

## <a name="authentication-and-hosting-embedding-reports-in-our-web-page"></a>Autenticación y hospeda informes (incrustación) en la página web

En la API de REST anterior, podemos usar la tecla de acceso **AppKey** propio como el encabezado de autorización. Dado que estas llamadas pueden corregirse en el servidor back-end, es seguro.

Pero, cuando se inserta el informe en la página web, este tipo de información de seguridad debería corregirse mediante JavaScript \(front-end). A continuación, se debe proteger el valor de autorización de encabezado. Si un usuario malintencionado o código malintencionado descubre a nuestra tecla de acceso, pueden llamar a las operaciones con esta clave.

Cuando se inserta el informe en la página web, debemos usamos el token calculado en lugar de la tecla de acceso **AppKey**. Nuestra aplicación debe crear el Token de Web de Json OAuth \(JWT) que consta de las notificaciones y la firma digital calculada. Como se muestra a continuación, esta JWT OAuth es tokens de cadena codificado delimitada por el punto.

![](media\power-bi-embedded-iframe\oauth-jwt.png)

En primer lugar, nos debemos preparar el valor de entrada, que se firmó más adelante. Este valor es una cadena de url codificada (rfc4648) base 64 de la siguiente json y estos están delimitados por el punto \(.) carácter. Más adelante, explicaremos cómo obtener el identificador del informe.

> [AZURE.NOTE] Si queremos usar fila nivel de seguridad con Power BI incrustado, nos debemos especificar **nombre de usuario** y las **funciones** en las notificaciones.

```
{
  "typ":"JWT",
  "alg":"HS256"
}
```

```
{
  "wid":"{workspace id}",
  "rid":"{report id}",
  "wcn":"{workspace collection name}",
  "iss":"PowerBISDK",
  "ver":"0.2.0",
  "aud":"https://analysis.windows.net/powerbi/api",
  "nbf":{start time of token expiration},
  "exp":{end time of token expiration}
}
```

A continuación, debemos crear la cadena de base 64 codificado de HMAC \(la firma) con el algoritmo de SHA256. Este valor firmado de entrada es la cadena anterior.

Por último, nos debemos combinar la cadena de valor y firma entrada con período \(.) carácter. La cadena completada es el token de aplicación para la incrustación de informe. Incluso si un usuario malintencionado descubre el token de aplicación, no pueden obtener la clave de acceso original. Este token de aplicación caducará rápidamente.

Este es un ejemplo PHP para realizar estos pasos:

```
<?php
// 1. power bi access key
$accesskey = "MpaUgrTv5e...";

// 2. construct input value
$token1 = "{" .
  "\"typ\":\"JWT\"," .
  "\"alg\":\"HS256\"" .
  "}";
$token2 = "{" .
  "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
  "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
  "\"wcn\":\"mypbiapp\"," . // workspace collection name
  "\"iss\":\"PowerBISDK\"," .
  "\"ver\":\"0.2.0\"," .
  "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
  "\"nbf\":" . date("U") . "," .
  "\"exp\":" . date("U" , strtotime("+1 hour")) .
  "}";
$inputval = rfc4648_base64_encode($token1) .
  "." .
  rfc4648_base64_encode($token2);

// 3. get encoded signature
$hash = hash_hmac("sha256",
    $inputval,
    $accesskey,
    true);
$sig = rfc4648_base64_encode($hash);

// 4. show result (which is the apptoken)
$apptoken = $inputval . "." . $sig;
echo($apptoken);

// helper functions
function rfc4648_base64_encode($arg) {
  $res = $arg;
  $res = base64_encode($res);
  $res = str_replace("/", "_", $res);
  $res = str_replace("+", "-", $res);
  $res = rtrim($res, "=");
  return $res;
}
?>
```

## <a name="finally-embed-the-report-into-the-web-page"></a>Por último, insertar el informe en la página web

Para incrustar el informe, nos debemos obtener la dirección url de insertar y el **identificador** con la API de REST siguientes de informes.

**Solicitud HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/reports
Authorization: AppKey MpaUgrTv5e...
```

**Respuesta HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: d4099022-405b-49d3-b3b7-3c60cf675958

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#reports",
  "value": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c",
      "isFromPbix": false
    }
  ]
}
```

Nos podemos incrustar el informe en nuestra aplicación web mediante el token de aplicación anterior.
Si observamos el código de ejemplo siguiente, la primera parte es el mismo que el ejemplo anterior. En la última parte, este ejemplo muestra la **embedUrl** \(ver el resultado anterior) en el iframe y es publicar el token de aplicación en el iframe.

> [AZURE.NOTE] Debe cambiar el valor del identificador de informe a uno de sus propios. Además, debido a un error en nuestro sistema de administración de contenido, la etiqueta iframe en el ejemplo de código se lee literalmente. Quitar el texto con punta de la etiqueta si copia y pega este código de ejemplo.

```
    <?php
    // 1. power bi access key
    $accesskey = "MpaUgrTv5e...";

    // 2. construct input value
    $token1 = "{" .
      "\"typ\":\"JWT\"," .
      "\"alg\":\"HS256\"" .
      "}";
    $token2 = "{" .
      "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
      "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
      "\"wcn\":\"mypbiapp\"," . // workspace collection name
      "\"iss\":\"PowerBISDK\"," .
      "\"ver\":\"0.2.0\"," .
      "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
      "\"nbf\":" . date("U") . "," .
      "\"exp\":" . date("U" , strtotime("+1 hour")) .
      "}";
    $inputval = rfc4648_base64_encode($token1) .
      "." .
      rfc4648_base64_encode($token2);

    // 3. get encoded signature value
    $hash = hash_hmac("sha256",
        $inputval,
        $accesskey,
        true);
    $sig = rfc4648_base64_encode($hash);

    // 4. get apptoken
    $apptoken = $inputval . "." . $sig;

    // helper functions
    function rfc4648_base64_encode($arg) {
      $res = $arg;
      $res = base64_encode($res);
      $res = str_replace("/", "_", $res);
      $res = str_replace("+", "-", $res);
      $res = rtrim($res, "=");
      return $res;
    }
    ?>
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <title>Test page</title>
      <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    <body>
      <button id="btnView">View Report !</button>
      <div id="divView">
        <**REMOVE THIS CAPPED TEXT IF COPIED** iframe id="ifrTile" width="100%" height="400"></iframe>
      </div>
      <script>
        (function () {
          document.getElementById('btnView').onclick = function() {
            var iframe = document.getElementById('ifrTile');
            iframe.src = 'https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c';
            iframe.onload = function() {
              var msgJson = {
                action: "loadReport",
                accessToken: "<?=$apptoken?>",
                height: 500,
                width: 722
              };
              var msgTxt = JSON.stringify(msgJson);
              iframe.contentWindow.postMessage(msgTxt, "*");
            };
          };
        }());
      </script>
    </body>
```

Y este es el resultado:

![](media\power-bi-embedded-iframe\view-report.png)

En este momento, Power BI incrustado solo muestra el informe en el iframe. Sin embargo, seguir controlando el [Blog de Power BI](). Mejoras futuras podrían usar el nuevo cliente de la API que se deje que nosotros enviar información en iframe, así como obtener información. ¡Cosas interesantes!


## <a name="see-also"></a>Vea también
- [Autenticación y autorización en Power BI incrustados](power-bi-embedded-app-token-flow.md)
