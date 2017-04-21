<properties
    pageTitle="Iniciar el selector de datos HTTP análisis API | Microsoft Azure"
    description="Puede usar la API de selector de datos de registro de análisis de HTTP para agregar datos JSON de publicación en el repositorio de análisis de registro de cualquier cliente que puede llamar a la API de REST. En este artículo se describe cómo utilizar la API y tiene algunos ejemplos de cómo publicar datos utilizando diferentes lenguajes de programación."
    services="log-analytics"
    documentationCenter=""
    authors="bwren"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="bwren"/>


# <a name="log-analytics-http-data-collector-api"></a>Selector de datos HTTP de análisis de registro API

Cuando utiliza la API de selector de datos de Azure registro análisis HTTP, puede agregar datos de entrada JavaScript Object Notation (JSON) en el repositorio de análisis de registro de cualquier cliente que puede llamar a la API de REST. Con este método, puede enviar los datos de aplicaciones de terceros o de secuencias de comandos, como desde un runbook de automatización de Azure.  

## <a name="create-a-request"></a>Crear una solicitud de

Las dos tablas enumeran los atributos necesarios para cada solicitud a la API de selector de datos de registro de análisis de HTTP. Describimos cada atributo en más detalle más adelante en este artículo.

### <a name="request-uri"></a>URI de la solicitud

| Atributo | (Propiedad) |
|:--|:--|
| Método | Exponer |
| URI | https://\<IdCliente\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Tipo de contenido | aplicación/json |

### <a name="request-uri-parameters"></a>Parámetros de URI de solicitud
| Parámetro | Descripción |
|:--|:--|
| Identificador de cliente  | El identificador único para el área de trabajo de la serie de administración de operaciones de Microsoft. |
| Recursos    | El nombre del recurso API: / api/registros. |
| Versión API | La versión de la API para utilizar con esta solicitud. Actualmente, es 2016-04-01. |

### <a name="request-headers"></a>Encabezados de solicitud
| Encabezado | Descripción |
|:--|:--|
| Autorización | La firma de autorización. Más adelante en el artículo, puede leer información sobre cómo crear un encabezado SHA256 HMAC. |
| Tipo de registro | Especificar el tipo de registro de los datos que se está enviando. Actualmente, el tipo de registro admite sólo caracteres alfabéticos. No se admite valores numéricos o caracteres especiales. |
| fecha de ms x | La fecha en la que se ha procesado la solicitud, en formato RFC 1123. |
| campo generados por hora | El nombre de un campo de los datos que contiene la marca de tiempo del elemento de datos. Si especifica un campo de su contenido se usa para **TimeGenerated**. Si no se especifica este campo, el valor predeterminado para **TimeGenerated** es el momento en que el mensaje se ingiere. El contenido del campo mensaje debería seguir el formato ISO 8601 YYYY-MM-ddTHH. |


## <a name="authorization"></a>Autorización

Cualquier solicitud de la API de selector de datos de registro de análisis de HTTP debe incluir un encabezado de autorización. Para autenticar una solicitud, debe iniciar sesión a la solicitud con la principal o la clave secundaria para el área de trabajo que realiza la solicitud. A continuación, pase dicha firma como parte de la solicitud.   

Aquí es el formato para el encabezado de autorización:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* es el identificador único para el área de trabajo de la serie de administración de operaciones. *Firma* es un [Código de autenticación de mensajes basado en Hash (HMAC)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) que se crea desde la solicitud y, a continuación, se calcula mediante el [algoritmo de SHA256](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). A continuación, se codifica utilizando la codificación de base 64.

Use este formato para codificar la cadena de firma de **SharedKey** :

```
StringToSign = VERB + "\n" +
               Content-Length + "\n" +
               Content-Type + "\n" +
               x-ms-date + "\n" +
               "/api/logs";
```

Aquí tiene un ejemplo de una cadena de firma:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Cuando tenga la cadena de firma, codificar mediante el algoritmo de HMAC SHA256 en la cadena con codificación UTF-8 y, a continuación, codificar el resultado como base 64. Use este formato:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Los ejemplos de las siguientes secciones tienen código de ejemplo para crear un encabezado de autorización.

## <a name="request-body"></a>Cuerpo de la solicitud

El cuerpo del mensaje debe estar en JSON. Debe incluir uno o varios registros con los pares de nombre y el valor de propiedad en este formato:

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

Puede por lotes varios registros juntos en una sola solicitud mediante el siguiente formato. Todos los registros deben ser del mismo tipo de registro.

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
},
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

## <a name="record-type-and-properties"></a>Propiedades y tipo de registro

Definir un tipo de registro personalizado al enviar datos a través de la API de selector de datos de registro de análisis de HTTP. Actualmente, no puede escribir datos en tipos de registros existentes creadas por otras soluciones y tipos de datos. Análisis de registro lee los datos entrantes y, a continuación, crea propiedades que coinciden con los tipos de datos de los valores que especifique.

Cada solicitud a la API de análisis de registro debe incluir un encabezado de **Tipo de registro** con el nombre del tipo de registro. Se agrega automáticamente el sufijo **_CL** en el nombre escriba para distinguir de otros tipos de registro como un registro personalizado. Por ejemplo, si escribe el nombre **MyNewRecordType**, análisis de registro crea un registro con el tipo de **MyNewRecordType_CL**. Esto ayuda a garantizar que no hay ningún conflicto entre los nombres de tipo creados por el usuario y los enviados en soluciones de Microsoft actuales y futuras.

Para identificar el tipo de datos de la propiedad, el análisis de registro agrega un sufijo al nombre de propiedad. Si una propiedad contiene un valor nulo, la propiedad no se incluye en dicho registro. Esta tabla muestra el tipo de datos de propiedad y el sufijo correspondiente:

| Tipo de datos (propiedad) | Sufijo |
|:--|:--|
| Cadena    | _S |
| Valor booleano   | _B |
| Doble    | sufijo _D |
| Fecha y hora | _T |
| GUID      | _g |


El tipo de datos que usa el análisis de registro para cada propiedad depende de si ya existe el tipo de registro para el nuevo registro.

- Si el tipo de registro no existe, el análisis de registro crea uno nuevo. Análisis de registro usa la inferencia de tipo JSON para determinar el tipo de datos para cada propiedad para el nuevo registro.
- Si existe el tipo de registro, el análisis de registro intenta crear un nuevo registro, en función de las propiedades existentes. Si el tipo de datos para una propiedad en el nuevo registro no se corresponde con y no puede convertirse en el tipo de, o si el registro incluye una propiedad que no existe, el análisis de registro crea una nueva propiedad que tiene el sufijo relevante.

Por ejemplo, esta entrada de envío crearía un registro con tres propiedades, **number_d**, **boolean_b**y **string_s**:

![Registro de ejemplo 1](media/log-analytics-data-collector-api/record-01.png)

Si, a continuación, se envía esta entrada siguiente con todos los valores con formato de cadenas, las propiedades no cambiará. Estos valores se pueden convertir a los tipos de datos existentes:

![Registro de ejemplo 2](media/log-analytics-data-collector-api/record-02.png)

Sin embargo, si se han realizado esta presentación siguiente, el análisis de registro crear las nuevas propiedades **boolean_d** y **string_d**. No se puede convertir estos valores:

![Registro de ejemplo 3](media/log-analytics-data-collector-api/record-03.png)

Si, a continuación, se envía la entrada siguiente, antes de que se creó el tipo de registro, el análisis de registro crearía un registro con tres propiedades, **núm_éxitos**, **boolean_s**y **string_s**. En esta entrada, cada uno de los valores iniciales se formato como una cadena:

![Registro de ejemplo 4](media/log-analytics-data-collector-api/record-04.png)


## <a name="data-limits"></a>Límites de datos
Existen algunas limitaciones alrededor de los datos publicados en la API de recopilación de datos de análisis de registro.

- Máximo de 30 MB por entrada de la API de selector de datos de análisis de registro. Se trata de un límite de tamaño de un solo elemento para exponer. Si los datos de un único registro que supera los 30 MB, debe dividir los datos hasta menor tamaño fragmentos y enviarlos al mismo tiempo. 
- Máximo de límite de 32 KB para los valores de campo. Si el valor del campo es mayor que 32 KB, se truncará los datos. 
- Número máximo recomendado de campos para un tipo determinado es 50. Se trata de un límite práctico desde una perspectiva de experiencia de búsqueda y uso.  


## <a name="return-codes"></a>Códigos de retorno

El código de estado HTTP 202 significa que se ha aceptado la solicitud para el procesamiento, pero todavía no ha finalizado procesamiento. Esto indica que la operación se completó correctamente.

Esta tabla muestra el conjunto completo de códigos de estado que puede devolver el servicio:

| Código | Estado | Código de error | Descripción |
|:--|:--|:--|:--|
| 202 | Aceptado |  | La solicitud se ha aceptado correctamente. |
| 400 | Solicitud incorrecta | InactiveCustomer | El área de trabajo se ha cerrado. |
| 400 | Solicitud incorrecta | InvalidApiVersion | No se reconoce la versión de la API especificada por el servicio. |
| 400 | Solicitud incorrecta | InvalidCustomerId | El identificador de área de trabajo especificado no es válido. |
| 400 | Solicitud incorrecta | InvalidDataFormat | Se ha enviado JSON no válido. El cuerpo de la respuesta puede contener más información sobre cómo resolver el error. |
| 400 | Solicitud incorrecta | InvalidLogType | El tipo de registro especificado contiene caracteres especiales o valores numéricos. |
| 400 | Solicitud incorrecta | MissingApiVersion | La versión de la API no se ha especificado. |
| 400 | Solicitud incorrecta | MissingContentType | El tipo de contenido no se ha especificado. |
| 400 | Solicitud incorrecta | MissingLogType | El tipo de registro de valor requerido no se ha especificado. |
| 400 | Solicitud incorrecta | UnsupportedContentType | El tipo de contenido no se estableció en **application/json**. |
| 403 | Prohibido | InvalidAuthorization | Error en el servicio autenticar la solicitud. Compruebe que la clave de ID y la conexión de área de trabajo son válidas. |
| 500 | Error interno del servidor | UnspecifiedError | El servicio ha encontrado un error interno. Vuelva a intentar la solicitud. |
| 503 | Servicio no disponible | ServiceUnavailable | El servicio no está disponible para recibir solicitudes actualmente. Vuelva a intentar la solicitud. |

## <a name="query-data"></a>Consulta de datos

Para consultar los datos enviados por la API de selector de datos de registro de análisis de HTTP, buscar registros con **tipo** que es igual al valor **LogType** que especificado, anexado con **_CL**. Por ejemplo, si utiliza **MyCustomLog**, a continuación, debe devolver todos los registros con **tipo = MyCustomLog_CL**.


## <a name="sample-requests"></a>Solicitudes de ejemplo

En las siguientes secciones, encontrará ejemplos de cómo enviar datos a la API de selector de datos de registro de análisis HTTP utilizando diferentes lenguajes de programación.

Para cada muestra, siga estos pasos para establecer las variables para el encabezado de autorización:

1. En el portal de la serie de administración de operaciones, seleccione el icono **configuración** y, a continuación, seleccione la pestaña **Conectado a orígenes** .
2. A la derecha del **ID de área de trabajo**, seleccione el icono de copia y pega el identificador como el valor de la variable del **Identificador de cliente** .
3. A la derecha de la **Clave principal**, seleccione el icono de copia y pega el identificador como el valor de la variable de **Clave compartida** .

Como alternativa, puede cambiar las variables para el tipo de registro y los datos JSON.

### <a name="powershell-sample"></a>Ejemplo de PowerShell

```
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# Specify a field with the created time for the records
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-OMSData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -fileName $fileName `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-OMSData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>Ejemplo de C#

```
using System;
using System.Net;
using System.Security.Cryptography;

namespace OIAPIExample
{
    class ApiExample
    {
// An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField1"":""DemoValue3"",""DemoField2"":""DemoValue4""}]";

// Update customerId to your Operations Management Suite workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

// For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

// LogName is name of the event type that is being submitted to Log Analytics
        static string LogName = "DemoExample";

// You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
// Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            string stringToHash = "POST\n" + json.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

// Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

// Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            string url = "https://"+ customerId +".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";
            using (var client = new WebClient())
            {
                client.Headers.Add(HttpRequestHeader.ContentType, "application/json");
                client.Headers.Add("Log-Type", LogName);
                client.Headers.Add("Authorization", signature);
                client.Headers.Add("x-ms-date", date);
                client.Headers.Add("time-generated-field", TimeStampField);
                client.UploadString(new Uri(url), "POST", json);
            }
        }
    }
}
```

### <a name="python-sample"></a>Ejemplo de Python

```
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Operations Management Suite workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code == 202):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

## <a name="next-steps"></a>Pasos siguientes

- Utilizar el [Diseñador de vistas](log-analytics-view-designer.md) para crear vistas personalizadas en los datos que envíe.
