<properties
 pageTitle="Guía de desarrollador: controlar el acceso a concentrador IoT | Microsoft Azure"
 description="Guía de Azure programador IoT concentrador - cómo controlar el acceso a IoT concentrador y administrar la seguridad"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="control-access-to-iot-hub"></a>Controlar el acceso a IoT concentrador

## <a name="overview"></a>Información general

En este artículo se describe las opciones para proteger su centro IoT. Concentrador IoT utiliza *permisos* para conceder acceso a cada extremos de concentrador IoT. Permisos de limitan el acceso a un concentrador IoT basado en la funcionalidad.

En este artículo se describe:

- Los distintos permisos que puede conceder a una aplicación de dispositivo o back-end para tener acceso a su centro IoT.
- El proceso de autenticación y los tokens que se usa para comprobar los permisos.
- Cómo definir el ámbito de las credenciales para limitar el acceso a recursos específicos.
- Soporte de concentrador IoT certificados X.509.
- Mecanismos de autenticación de dispositivo personalizados que usan los registros de identidad de dispositivo existente o esquemas de autenticación.

### <a name="when-to-use"></a>Cuándo usar

Debe tener los permisos adecuados para tener acceso a cualquiera de los extremos de concentrador IoT. Por ejemplo, un dispositivo debe incluir un símbolo que contiene las credenciales de seguridad junto con todos los mensajes que envía a IoT concentrador.

## <a name="access-control-and-permissions"></a>Permisos y control de acceso

Puede conceder [permisos](#iot-hub-permissions) de las siguientes maneras:

* **Nivel de concentrador compartidos directivas de acceso**. Las directivas de acceso compartido pueden conceder cualquier combinación de [permisos](#iot-hub-permissions). Puede definir directivas en el [portal de Azure][lnk-management-portal], o mediante programación utilizando el [proveedor de recursos de concentrador IoT API de REST][lnk-resource-provider-apis]. Un concentrador IoT recién creado tiene las siguientes directivas predeterminadas:

    - **iothubowner**: directiva con todos los permisos.
    - **servicio**: directiva de permisos ServiceConnect.
    - **dispositivo**: directiva de permisos DeviceConnect.
    - **registryRead**: directiva de permisos RegistryRead.
    - **registryReadWrite**: directiva de permisos RegistryRead y RegistryWrite.


* **Credenciales de seguridad por dispositivo**. Cada concentrador IoT contiene un [registro de la identidad de dispositivo][lnk-identity-registry]. Para cada dispositivo en este registro, puede configurar las credenciales de seguridad que concesión permisos de **DeviceConnect** el ámbito de los extremos de dispositivo correspondiente.

Por ejemplo, en una solución IoT típica:

- El componente de administración de dispositivo usa la directiva *registryReadWrite* .
- El componente de procesador de evento utiliza la directiva de *servicio* .
- El componente de lógica empresarial de runtime dispositivo utiliza la directiva de *servicio* .
- Dispositivos individuales se conectan con las credenciales almacenadas en el registro de identidad del concentrador IoT.

## <a name="authentication"></a>Autenticación

Hub de Azure IoT concede acceso a extremos comprobando un token frente a las directivas de acceso compartido y las credenciales de seguridad de dispositivo identidad del registro.

Credenciales de seguridad, como las claves simétricas, nunca se envían durante la conexión.

> [AZURE.NOTE] El proveedor de recursos de Azure IoT concentrador es segura a través de su suscripción de Azure, como todos los proveedores en el [Administrador de recursos de Azure][lnk-azure-resource-manager].

Para obtener más información acerca de cómo crear y usar el token de seguridad, vea [tokens de seguridad de concentrador IoT][lnk-sas-tokens].

### <a name="protocol-specifics"></a>Características específicas de protocolo

Cada protocolo admitido, como MQTT, AMQP y HTTP, transportes tokens de diferentes formas.

Al utilizar MQTT, el paquete de conectar tiene deviceId como ClientId, {iothubhostname} / {deviceId} en el campo nombre de usuario y a continuación, un símbolo de SA en el campo contraseña. {iothubhostname} debería el registro CName completo del concentrador IoT (por ejemplo, devices.net contoso.azure).

Cuando se usa [AMQP][lnk-amqp], IoT concentrador admite [Sin formato SASL] [ lnk-sasl-plain] y [AMQP reclamaciones basada-seguridad][lnk-cbs].

Si usa AMQP notificaciones en función de seguridad, el estándar especifica cómo transmitir estos tokens.

Para sin formato SASL, puede ser el **nombre de usuario** :

* `{policyName}@sas.root.{iothubName}`Si usa tokens de nivel de concentrador.
* `{deviceId}@sas.{iothubname}`Si usa tokens ámbito del dispositivo.

En ambos casos, el campo de contraseña contiene el token, como se describe en el [token de seguridad de concentrador IoT][lnk-sas-tokens].

HTTP implementa autenticación incluyendo un token válido en el encabezado de la solicitud de **autorización** .

#### <a name="example"></a>Ejemplo

Nombre de usuario (DeviceId distingue mayúsculas de minúsculas):`iothubname.azure-devices.net/DeviceId`

Contraseña (generar SA con el Explorador de dispositivo):`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] El [SDK de concentrador de Azure IoT] [ lnk-sdks] generar automáticamente tokens al conectar con el servicio. En algunos casos, el SDK no admiten todos los protocolos o todos los métodos de autenticación.

### <a name="special-considerations-for-sasl-plain"></a>Consideraciones especiales para SASL normal

Cuando se usa sin formato SASL con AMQP, un cliente se conecta a un concentrador IoT puede usar un token único para cada conexión de TCP. Cuando expira el token, la conexión TCP desconecta del servicio y activa el restablecimiento de la conexión. Este comportamiento, aunque no problemático para un componente de back-end de la aplicación, es muy perjudicial para una aplicación del dispositivo por los siguientes motivos:

*  Normalmente conectan puertas de enlace en nombre de multitud de dispositivos. Cuando se usa sin formato SASL, tienen que crear una conexión de TCP distinta para cada dispositivo que se conecta a un concentrador IoT. Este escenario considerablemente aumenta el consumo de energía y recursos de red y aumenta la latencia de cada conexión de dispositivo.
* Dispositivos de recursos restringidos negativo se ven afectados por el aumento del uso de recursos para volver a conectar después de cada expiración token.

## <a name="scope-hub-level-credentials"></a>Credenciales de nivel de concentrador de ámbito

Puede definir el ámbito de las directivas de seguridad a nivel de concentrador creando tokens con un URI de recurso restringido. Por ejemplo, el extremo para enviar mensajes de dispositivo a la nube desde un dispositivo es **/devices/ {deviceId} / mensajes y eventos**. También puede usar una directiva de acceso de nivel de concentrador compartido con permisos de **DeviceConnect** para firmar un token cuyo resourceURI es **/devices/ {deviceId}**. Este enfoque crea un símbolo que solo se puede usar para enviar mensajes en nombre de dispositivo **deviceId**.

Este mecanismo es similar a la [Directiva de Editor de evento Hubs][lnk-event-hubs-publisher-policy]y le permiten implementar métodos de autenticación personalizada.

## <a name="security-tokens"></a>Símbolos de seguridad

Concentrador IoT usa tokens de seguridad para autenticar dispositivos y servicios para evitar enviar teclas en la conexión. Además, los tokens de seguridad están limitados en el ámbito y validez de tiempo. [Azure SDK de concentrador IoT] [ lnk-sdks] generar automáticamente tokens sin necesidad de configuración especial. Algunos escenarios, sin embargo, requieren que el usuario generar y utilizar tokens de seguridad directamente. Incluyen el uso de la expone MQTT, AMQP o HTTP directo o la implementación del modelo de servicio de token, como se explica en [autenticación de dispositivo personalizado][lnk-custom-auth].

Concentrador IoT también permite dispositivos autenticar con IoT concentrador con [los certificados X.509][lnk-x509]. 

### <a name="security-token-structure"></a>Estructura de token de seguridad
Utilizar tokens de seguridad para conceder acceso limitado a la vez a los dispositivos y servicios funcionalidad específica en IoT concentrador. Para asegurarse de que se pueden conectar solo a los servicios y dispositivos autorizados, tokens de seguridad deben haber iniciado sesión con una clave de directiva de acceso compartido o una clave simétrica almacenados con la identidad de un dispositivo en el registro de la identidad.

Un token de firma con un acceso compartido directiva clave concede acceso a todas las funciones asociadas con los permisos de la directiva de acceso compartido. Por otro lado, un token firmado con la clave de la identidad de un dispositivo sólo concede el permiso **DeviceConnect** para la identidad del dispositivo asociado.

El token de seguridad tiene el siguiente formato:

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

Estos son los valores esperados:

| Valor | Descripción |
| ----- | ----------- |
| {firma} | Una cadena de firma HMAC SHA256 del formulario: `{URL-encoded-resourceURI} + "\n" + expiry`. **Importante**: la clave se descodificar desde base 64 y se usa como clave para realizar el cálculo SHA256 HMAC. |
| {resourceURI} | Prefijo URI (por segmento) de los extremos que se puede acceder a este token, comenzando con el nombre de host del concentrador IoT (sin protocolo). Por ejemplo,`myHub.azure-devices.net/devices/device1` |
| {expiración} | Cadenas de UTF8 para el número de segundos transcurridos desde la UTC 00:00:00 de tiempo en el 1 de enero de 1970. |
| {URL-codificado-resourceURI} | Cuanto menor sea mayúsculas y minúsculas codificación URL de URI de recurso de minúsculas |
| nombre de directiva {} | El nombre de la directiva de acceso compartido al que hace referencia este token. Ausente en el caso de tokens que hacen referencia a las credenciales de registro de dispositivos. |

**Nota en prefijo**: prefijo URI se calcula por segmento y no por carácter. Por ejemplo `/a/b` es un prefijo de `/a/b/c` , pero no para `/a/bc`.

El siguiente fragmento de Node.js muestra una función denominada **generateSasToken** que calcula el token de las entradas `resourceUri, signingKey, policyName, expiresInMins`. Las siguientes secciones explican cómo iniciar las entradas diferentes para los casos de uso de token diferente.

    var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
        resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();

        // Set expiration in seconds
        var expires = (Date.now() / 1000) + expiresInMins * 60;
        expires = Math.ceil(expires);
        var toSign = resourceUri + '\n' + expires;

        // Use crypto
        var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
        hmac.update(toSign);
        var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

        // Construct autorization string
        var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
        + base64UriEncoded + "&se=" + expires;
        if (policyName) token += "&skn="+policyName;
        return token;
    };

Como una comparación, es el código equivalente de Python para generar un token de seguridad:

    from base64 import b64encode, b64decode
    from hashlib import sha256
    from time import time
    from urllib import quote_plus, urlencode
    from hmac import HMAC

    def generate_sas_token(uri, key, policy_name, expiry=3600):
        ttl = time() + expiry
        sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
        print sign_key
        signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

        rawtoken = {
            'sr' :  uri,
            'sig': signature,
            'se' : str(int(ttl))
        }

        if policy_name is not None:
            rawtoken['skn'] = policy_name

        return 'SharedAccessSignature ' + urlencode(rawtoken)

> [AZURE.NOTE] Dado que el periodo de validez del token se valida en equipos IoT concentrador, es importante que la deriva del reloj del equipo que genera el token sea mínima.

### <a name="use-sas-tokens-in-a-device-client"></a>Usar tokens SA en un cliente de dispositivo

Hay dos maneras de obtener permisos **DeviceConnect** con IoT concentrador con tokens de seguridad: utiliza una [clave de dispositivo simétrica desde el registro de la identidad de dispositivo](#use-a-symmetric-key-in-the-identity-registry), o use una [clave de la directiva de acceso compartido](#use-a-shared-access-policy).

Recuerde que todas las funciones accesibles desde dispositivos se exponen por diseño en los extremos con prefijo `/devices/{deviceId}`.

> [AZURE.IMPORTANT] La única manera de concentrador IoT autentica a un dispositivo específico está utilizando la clave simétrica de identidad de dispositivo. En casos cuando se usa una directiva de acceso compartido para tener acceso a la funcionalidad del dispositivo, la solución debe tener en cuenta el componente emitir el token de seguridad como subcomponente confianza.

Los extremos de la orientación de dispositivo son (con independencia de protocolo):

| Punto final | Funcionalidad |
| ----- | ----------- |
| `{iot hub host name}/devices/{deviceId}/messages/events` | Enviar mensajes de dispositivo a la nube. |
| `{iot hub host name}/devices/{deviceId}/devicebound` | Recibir mensajes de la nube al dispositivo. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Usar una clave simétrica en el registro de identidad

Cuando se usa la clave de la identidad de un dispositivo para generar un token de la directiva (`skn`) se omite el elemento del token.

Por ejemplo, un símbolo creado para acceder a todas las funciones de dispositivo debe tener los siguientes parámetros:

* URI de recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* clave de firma: cualquier clave simétrica para la `{device id}` identidad
* sin nombre de la directiva
* períodos de expiración.

Un ejemplo del uso de la función de nodo anterior sería:

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var deviceKey ="...";

    var token = generateSasToken(endpoint, deviceKey, null, 60);

El resultado, que conceda acceso a todas las funciones para dispositivo1, sería:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697

> [AZURE.NOTE] Es posible generar un token seguro mediante la herramienta de .NET [Explorer dispositivo][lnk-device-explorer].

### <a name="use-a-shared-access-policy"></a>Utilizar una directiva de acceso compartido

Al crear un símbolo de una directiva de acceso compartido, el campo de nombre de la directiva `skn` debe estar establecida en el nombre de la directiva que se utiliza. También es necesario que la directiva concede permiso **DeviceConnect** .

Los dos escenarios principales para el uso de directivas de acceso compartido para tener acceso a la funcionalidad de dispositivo son:

* [en la nube puertas de enlace de protocolo][lnk-endpoints],
* [Servicios de token] [ lnk-custom-auth] usado para implementar esquemas de autenticación personalizada.

Puesto que la directiva de acceso compartido potencialmente puede conceder acceso a conectar como cualquier dispositivo, es importante utilizar el recurso correcto URI al crear tokens de seguridad. Esto es especialmente importante para los servicios de tokens, que tienen que alcance el token a un dispositivo específico usando el URI del recurso. Este punto es menos relevante para puertas de enlace de protocolo, como los ya están intermedias del tráfico para todos los dispositivos.

Por ejemplo, un servicio de token mediante la directiva de acceso compartido creado previamente denominada **dispositivo** crearía un token con los siguientes parámetros:

* URI de recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* clave de firma: una de las teclas de la `device` directiva,
* nombre de la directiva: `device`,
* períodos de expiración.

Un ejemplo del uso de la función de nodo anterior sería:

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var policyName = 'device';
    var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

El resultado, que conceda acceso a todas las funciones para dispositivo1, sería:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device

Una puerta de enlace de protocolo podría usar el mismo token para todos los dispositivos que simplemente establecer el URI de recurso para `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Utilice los símbolos de seguridad de los componentes de servicio

Componentes de servicio sólo pueden generar tokens de seguridad con las directivas de acceso compartido para conceder los permisos adecuados, como se explica anteriormente.

Estas son las funciones de servicio expuestas en los extremos:

| Punto final | Funcionalidad |
| ----- | ----------- |
| `{iot hub host name}/devices` | Crear, actualizar, recuperar y eliminar las identidades de dispositivo. |
| `{iot hub host name}/messages/events` | Recibir mensajes de dispositivo a la nube. |
| `{iot hub host name}/servicebound/feedback` | Recibir comentarios para los mensajes de la nube al dispositivo. |
| `{iot hub host name}/devicebound` | Enviar mensajes de nube al dispositivo. |

Por ejemplo, un servicio para generar con la directiva de acceso compartido creado previamente denominada **registryRead** crearía un token con los siguientes parámetros:

* URI de recurso: `{IoT hub name}.azure-devices.net/devices`,
* clave de firma: una de las teclas de la `registryRead` directiva,
* nombre de la directiva: `registryRead`,
* períodos de expiración.

    extremo var = "myhub.azure-devices.net/devices";   Directiva de var = 'dispositivo';   var policyKey = '...';

    var token = generateSasToken (extremo, policyKey, 60 de la directiva);

El resultado, que desea conceder acceso para leer todas las identidades de dispositivo, sería:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead

## <a name="supported-x509-certificates"></a>Certificados X.509 compatibles

Puede usar cualquier certificado X.509 para autenticar un dispositivo con IoT concentrador. Esto incluye:

-   **Un certificado X.509 existente**. Un dispositivo que ya tenga un certificado X.509 asociado. El dispositivo puede usar este certificado para autenticar con IoT concentrador.

-   **Un certificado de X-509 autofirmado y generado automáticamente**. Un fabricante del dispositivo o implementador interna puede generar estos certificados y almacenar la clave privada correspondiente (y el certificado) en el dispositivo. Puede usar herramientas como [OpenSSL] [ lnk-openssl] y [Windows SelfSignedCertificate] [ lnk-selfsigned] utilidad para ello.

-   **Certificado X.509 firmado de entidad emisora de certificados**. También puede usar un certificado X.509 generado y firmado por una entidad emisora de certificados (CA) para identificar un dispositivo y autenticar un dispositivo con IoT concentrador.

Un dispositivo puede utilizar un certificado X.509 o un token de seguridad para la autenticación, pero no en ambos.

### <a name="register-an-x509-client-certificate-for-a-device"></a>Registrar un certificado de cliente X.509 de un dispositivo

El [SDK de servicio de Azure IoT para C#] [ lnk-service-sdk] (versión 1.0.8+) admite registrar un dispositivo que usa un certificado de cliente X.509 para la autenticación. Otras API de importación o exportación de dispositivos también admite certificados de cliente X.509.

### <a name="c-support"></a>C\# soporte técnico

La clase **RegistryManager** proporciona una manera mediante programación para registrar un dispositivo. En particular, los métodos **AddDeviceAsync** y **UpdateDeviceAsync** habilitar un usuario registrar y actualizar un dispositivo en el registro de identidad de dispositivo Iot concentrador. Estos dos métodos toman una instancia de **dispositivo** como entrada. La clase de **dispositivo** incluye una propiedad de **autenticación** que permite al usuario especificar primario y secundarios X.509 certificados de huella digital. La huella digital representa un hash SHA-1 del certificado X.509 (almacenado con codificación DER binaria). Los usuarios tienen la opción de especificar una huella digital principal o una huella digital secundaria o ambos. Huellas digitales primario y secundarios son compatibles con el fin de controlar los escenarios de conversión de certificado.

> [AZURE.NOTE] Concentrador IoT no requiere o no almacenar el certificado de cliente X.509 completo, solo la huella digital.

Este es un ejemplo C\# fragmento de código para registrar un dispositivo con un certificado de cliente X.509:

```
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-client-certificate-during-runtime-operations"></a>Usar un certificado de cliente X.509 durante las operaciones de tiempo de ejecución

El [dispositivo de Azure IoT SDK para .NET] [ lnk-client-sdk] (versión 1.0.11+) admite el uso de certificados de cliente X.509.

### <a name="c-support"></a>C\# soporte técnico

La clase **DeviceAuthenticationWithX509Certificate** es compatible con la creación de instancias de  **DeviceClient** mediante un certificado de cliente X.509.

Esto es un fragmento de código de ejemplo:

```
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-authentication"></a>Autenticación de dispositivos personalizados

Puede usar el concentrador IoT [registro de identidad de dispositivo] [ lnk-identity-registry] para configurar las credenciales de seguridad por dispositivo y acceso control utilizando [tokens][lnk-sas-tokens]. Sin embargo, si una solución IoT ya tiene una inversión importante en una combinación de registro o autenticación de identidad de dispositivo personalizado, puede integrar esta infraestructura existente con concentrador IoT mediante la creación de un *servicio de token*. De este modo, puede usar otras características IoT en su solución.

Un servicio de token es un servicio de nube personalizados. Utiliza un concentrador IoT *Directiva de acceso compartido* con permisos de **DeviceConnect** para crear tokens *ámbito del dispositivo* . Estos tokens habilitar un dispositivo para conectarse a su centro IoT.

  ![Pasos del modelo de servicio de token][img-tokenservice]

Estos son los pasos principales del modelo de servicio de token:

1. Crear una directiva de acceso de concentrador IoT compartido con los permisos de **DeviceConnect** para su centro IoT. Puede crear esta directiva en el [portal de Azure] [ lnk-management-portal] o mediante programación. El servicio de token usa esta directiva para firmar los tokens que crea.
2. Cuando un dispositivo necesita tener acceso a su centro IoT, solicita un símbolo de sesión de su servicio de token. El dispositivo puede autenticar con su esquema de registro/autenticación de identidad de dispositivos personalizados para determinar la identidad del dispositivo que usa el servicio de token para crear el token.
3. El servicio de token devuelve un token. El token se crea mediante `/devices/{deviceId}` como `resourceURI`, con `deviceId` como el dispositivo autenticarse. El servicio de token utiliza la directiva de acceso compartido para construir el token.
4. El dispositivo usa el token directamente con el concentrador IoT.

> [AZURE.NOTE] Puede usar la clase .NET [SharedAccessSignatureBuilder] [ lnk-dotnet-sas] o la clase de Java [IotHubServiceSasToken] [ lnk-java-sas] para crear un símbolo en el servicio de token.

El servicio de token puede establecer la fecha de expiración de token como desee. Cuando expira el token, el concentrador IoT servidores la conexión del dispositivo. A continuación, el dispositivo debe solicitar un token nuevo desde el servicio de token. Si usa una fecha de caducidad breve, se incrementa la carga en el dispositivo y el servicio de token.

Para que un dispositivo para conectarse a su centro, debe seguir agregarlo al registro de identidad del dispositivo IoT concentrador, aunque el dispositivo está usando un símbolo y no es una clave de dispositivo para conectar. Por lo tanto, aún puede usar el control de acceso por dispositivo, activando o desactivando las identidades de dispositivo en el [registro de la identidad de concentrador IoT] [ lnk-identity-registry] cuando el dispositivo se autentica con un símbolo. Esto reduce los riesgos de tokens con las horas de caducidad larga.

### <a name="comparison-with-a-custom-gateway"></a>Comparación con una puerta de enlace personalizado

El modelo de servicio de token es la manera recomendada para implementar un esquema de registro/autenticación de identidad personalizada con IoT concentrador. Se recomienda porque continúa IoT concentrador controlar la mayoría del tráfico de solución. Sin embargo, hay casos donde la combinación de autenticación personalizada así se entrelazados con el protocolo que se requiere un servicio de procesamiento de todo el tráfico (*puerta de enlace personalizada*). Un ejemplo de esto es la [seguridad de capa de transporte (TLS) y claves previamente compartidas (PSKs)][lnk-tls-psk]. Para obtener más información, consulte la [puerta de enlace de protocolo] [ lnk-protocols] tema.

## <a name="reference-topics"></a>Temas de referencia:

Los temas de referencia siguientes le proporciona más información acerca de cómo controlar el acceso a su centro IoT.

## <a name="iot-hub-permissions"></a>Permisos de concentrador IoT

La siguiente tabla enumeran los permisos que se puede usar para controlar el acceso a su centro IoT.

| Permisos            | Notas |
| --------------------- | ----- |
| **RegistryRead**      | Concede acceso de lectura a registro de identidad del dispositivo. Para obtener más información, vea [registro de identidad de dispositivo][lnk-identity-registry]. |
| **RegistryReadWrite** | Concede leer y acceso de escritura en el registro de la identidad de dispositivo. Para obtener más información, vea [registro de identidad de dispositivo][lnk-identity-registry]. |
| **ServiceConnect**    | Concede acceso a orientación de servicio de comunicación y supervisar los extremos de la nube. Por ejemplo, concede permiso a los servicios de nube de back-end para recibir mensajes de dispositivo a la nube, enviar mensajes de dispositivo de nube y recuperar las confirmaciones de entrega correspondiente. |
| **DeviceConnect**     | Concede acceso a los extremos de la comunicación de orientación del dispositivo. Por ejemplo, concede permiso para enviar mensajes del dispositivo a la nube y recibir mensajes de nube al dispositivo. Este permiso se usa en dispositivos. |

## <a name="additional-reference-material"></a>Material de referencia adicional

Otros temas de referencia en la Guía del programador incluyen:

- [Extremos de concentrador IoT] [ lnk-endpoints] describe los diversos extremos que expone cada concentrador IoT para operaciones de administración y el tiempo de ejecución.
- [Cuotas y regulación] [ lnk-quotas] describe las cuotas que se aplican al servicio IoT concentrador y el comportamiento de limitación que va a pasar al usar el servicio.
- [SDK de dispositivos y servicios de concentrador IoT] [ lnk-sdks] listas el idioma diversos SDK que un uso al desarrollar aplicaciones dispositivos y servicios que interactúan con IoT concentrador.
- [Lenguaje de consulta para gemelos, métodos y trabajos] [ lnk-query] describe el lenguaje de consulta que se puede usar para recuperar información de concentrador IoT sobre sus twins de dispositivo, métodos y trabajos.
- [Soporte técnico de IoT concentrador MQTT] [ lnk-devguide-mqtt] proporciona más información acerca del soporte técnico de IoT concentrador para el protocolo MQTT.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido controlar el acceso IoT concentrador, puede estar interesado en los siguientes temas de la Guía del programador:

- [Utilizar a twins dispositivo para sincronizar estado y configuraciones][lnk-devguide-device-twins]
- [Llamar a un método directo en un dispositivo][lnk-devguide-directmethods]
- [Programar trabajos en varios dispositivos][lnk-devguide-jobs]

Si desea probar algunos de los conceptos descritos en este artículo, puede estar interesado en los siguientes tutoriales de concentrador IoT:

- [Introducción a Azure IoT concentrador][lnk-getstarted-tutorial]
- [Cómo enviar mensajes de dispositivo de nube con concentrador IoT][lnk-c2d-tutorial]
- [Cómo procesar los mensajes de dispositivo a la nube Concentrador IoT][lnk-d2c-tutorial]

<!-- links and images -->

[img-tokenservice]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-openssl]: https://www.openssl.org/
[lnk-selfsigned]: https://technet.microsoft.com/library/hh848633

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-sas-tokens]: iot-hub-devguide-security.md#security-tokens
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-custom-auth]: iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: iot-hub-devguide-security.md#supported-x509-certificates
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
