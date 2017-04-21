<properties 
    pageTitle="Cifrar el contenido con el cifrado de almacenamiento con la API de REST de AMS" 
    description="Obtenga información sobre cómo cifrar el contenido con cifrado de almacenamiento mediante las API de REST de AMS." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


#<a name="encrypting-your-content-with-storage-encryption-using-ams-rest-api"></a>Cifrar el contenido con el cifrado de almacenamiento con la API de REST de AMS

Se recomienda para cifrar el contenido localmente mediante el cifrado AES de 256 bits y luego cárguelo en el almacenamiento de Azure donde se almacenarán cifrados en el resto.

Este artículo proporciona una descripción general de cifrado de almacenamiento de AMS y muestra cómo cargar el contenido de almacenamiento cifrado:

- Crear una clave de contenido.
- Crear un activo. Establezca el AssetCreationOption en StorageEncryption cuando se crea el activo.

     Cifrado activos tienen que estar asociadas con teclas de contenido.
- Vincular la clave de contenido activo.  
- Establecer el cifrado de los parámetros en las entidades AssetFile relacionados.
 
>[AZURE.NOTE]Si desea ofrecer a un activo cifrado de almacenamiento, debe configurar Directiva de entrega del activo. Antes de que puedan transmitir su activo, el servidor de transmisión quita el cifrado de almacenamiento y transmite el contenido con la directiva de entrega especificado. Para obtener más información, vea [Configuración de directivas de entrega activos](media-services-rest-configure-asset-delivery-policy.md).


>[AZURE.NOTE] Cuando se trabaja con la API de REST de servicios de medios, se aplican las consideraciones siguientes:
>
>Al acceder a las entidades de Media Services, debe establecer los campos de encabezado específico y los valores en las solicitudes HTTP. Para obtener más información, consulte [configuración de desarrollo de API de REST de servicios de multimedia](media-services-rest-how-to-use.md).

>Después de conectarse correctamente a https://media.windows.net, recibirá una redirección 301 especificando otro URI de servicios de medios. Debe realizar las siguientes llamadas para el nuevo URI tal como se describe en [Conectar con Media Services con la API de REST](media-services-rest-connect-programmatically.md). 

##<a name="storage-encryption-overview"></a>Introducción al cifrado de almacenamiento 

El cifrado de almacenamiento AMS aplica el cifrado **AES CTR** modo a todo el archivo.  Modo de CTR AES es un cifrado de bloques que puede cifrar datos de longitud arbitrario sin necesidad de relleno. Funciona mediante el cifrado de un bloque de contador con el algoritmo AES y luego xo-ión la salida de AES con los datos para cifrar o descifrar.  El bloque de contador utilizado se construye copiando el valor de la InitializationVector en bytes 0 a 7 del valor contador y bytes 8 a 15 del valor contador están establecidos en cero. Del bloque de contador 16 bytes, bytes 8 a 15 (es decir, los bytes menos significativos) se utilizan como un entero simple de 64 bits que se incrementa en uno para cada bloque de datos subsiguientes procesa y se guarda en el orden de bytes de red. Tenga en cuenta que si este entero alcanza el valor máximo (0xFFFFFFFFFFFFFFFF) luego incrementar el restablecimiento del contador bloque a cero (bytes 8 a 15) sin afectar a los 64 bits del contador (es decir, bytes 0 a 7).   Para mantener la seguridad de la AES CTR modo de cifrado, el valor de InitializationVector para un determinado identificador de clave para cada clave contenido deberá ser único para cada archivo y archivos será menor que 2 ^ 64 bloques de longitud.  Esto es para asegurarse de que un valor de contador nunca se reutiliza con una clave determinada. Para obtener más información sobre el modo CTR, consulte [esta página wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (el artículo wiki utiliza el término "Valor de seguridad" en lugar de "InitializationVector").

Uso del **Cifrado de almacenamiento** para cifrar el contenido de borrar localmente mediante el cifrado AES de 256 bits y luego cárguelo en el almacenamiento de Azure donde esté almacenado cifrado al resto. Activos protegidos con cifrado de almacenamiento automáticamente sin cifrar y colocados en un sistema de archivos cifrados antes de codificación y, opcionalmente, cifrados de nuevo antes de cargarlo como un nuevo activo de salida. El caso de uso principal para el cifrado de almacenamiento es cuando desea proteger los archivos de medios de entrada de alta calidad con cifrado almacenados en disco.

Para poder ofrecer a un activo cifrado de almacenamiento, debe configurar Directiva de entrega del activo para que Media Services sepa cómo desea entregar el contenido. Antes de que puedan transmitir su activo, el servidor de transmisión quita el cifrado de almacenamiento y transmite el contenido con la directiva de entrega especificada (por ejemplo, AES, cifrado comunes o sin cifrado).

##<a name="create-contentkeys-used-for-encryption"></a>Crear ContentKeys utilizado para el cifrado

Cifrado activos tienen que estar asociadas a la clave de cifrado de almacenamiento. Debe crear la clave de contenido que se utilizará para el cifrado antes de crear los archivos de activos. Esta sección describe cómo crear una clave de contenido.

Los siguientes son los pasos generales para generar claves de contenido que se asociará con activos que desea cifrar. 

1. Cifrado de almacenamiento aleatoriamente generar una clave AES de 32 bytes. 

    Se trata de la clave de contenido para su activo, lo que significa que todos los archivos asociados con ese activo tendrá que utilizar la misma clave de contenido durante el descifrado. 
2.  Llamar a los métodos [GetProtectionKeyId](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkeyid) y [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) para obtener el certificado X.509 correcto que se debe usar para cifrar la clave de contenido.
3.  Cifrar la clave de contenido con la clave pública del certificado X.509. 

    Media Services .NET SDK utiliza RSA con OAEP cuando se realiza el cifrado.  Puede ver un ejemplo de .NET en la [función EncryptSymmetricKeyData](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4.  Crear un valor de suma de comprobación calculado utilizando el identificador de clave y la clave de contenido. En el siguiente ejemplo .NET calcula la suma de comprobación mediante el elemento GUID del identificador clave y la clave de borrar contenido.
    

        public static string CalculateChecksum(byte[] contentKey, Guid keyId)
        {
            const int ChecksumLength = 8;
            const int KeyIdLength = 16;

            byte[] encryptedKeyId = null;

            // Checksum is computed by AES-ECB encrypting the KID
            // with the content key.
            using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
            {
                rijndael.Mode = CipherMode.ECB;
                rijndael.Key = contentKey;
                rijndael.Padding = PaddingMode.None;

                ICryptoTransform encryptor = rijndael.CreateEncryptor();
                encryptedKeyId = new byte[KeyIdLength];
                encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
            }

            byte[] retVal = new byte[ChecksumLength];
            Array.Copy(encryptedKeyId, retVal, ChecksumLength);

            return Convert.ToBase64String(retVal);
        }


5. Crear la clave de contenido con **EncryptedContentKey** (que se convierten en cadena codificado base 64), **ProtectionKeyId**, **ProtectionKeyType**, **ContentKeyType**y **suma** los valores que ha recibido en los pasos anteriores.

    
    Para el cifrado de almacenamiento, las siguientes propiedades deben incluirse en el cuerpo de la solicitud.
     
    Solicitud de cuerpo (propiedad)   | Descripción
    ---|---
    Id. | El identificador de ContentKey que se genera nosotros con el siguiente formato, "nb:kid:UUID:<NEW GUID>".
    ContentKeyType | Este es el tipo de clave de contenido como un número entero de esta clave de contenido. Se pasa el valor 1 para el cifrado de almacenamiento.
    EncryptedContentKey | Crear un nuevo valor de clave contenido que es un valor de 256 bits (32 bytes). La clave se cifra utilizando el certificado X.509 cifrado de almacenamiento que se recupera desde Microsoft Azure Media Services mediante la ejecución de una solicitud HTTP GET para los métodos de GetProtectionKey y GetProtectionKeyId. Por ejemplo, vea el siguiente código .NET: el método **EncryptSymmetricKeyData** definido [aquí](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    ProtectionKeyId | Este es el identificador de clave de protección para el certificado X.509 cifrado de almacenamiento que se usó para cifrar la clave de contenido.
    ProtectionKeyType | Este es el tipo de cifrado para la clave de protección que se usó para cifrar la clave de contenido. Este valor es StorageEncryption(1) en nuestro ejemplo.
    Suma de comprobación |Suma de comprobación calculada MD5 para la clave de contenido. Se calcula mediante el cifrado el identificador de contenido con la clave de contenido. El código de ejemplo muestra cómo calcular la suma de comprobación.
    

###<a name="retrieve-the-protectionkeyid"></a>Recuperar el ProtectionKeyId 
 

En el ejemplo siguiente se muestra cómo recuperar la ProtectionKeyId, una huella digital de certificado para el certificado que se debe usar para cifrar la clave de contenido. Realizar este paso para asegurarse de que ya tiene el certificado apropiado en su equipo.


Solicitar:
    
    
    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
    x-ms-version: 2.11
    Host: media.windows.net
    

Respuesta:
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 139
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:42:52 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}

###<a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Recuperar la ProtectionKey para el ProtectionKeyId

En el ejemplo siguiente se muestra cómo recuperar el certificado X.509 utilizando la ProtectionKeyId que ha recibido en el paso anterior.

Solicitar:
        
    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
    x-ms-version: 2.11
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    Host: media.windows.net
    


Respuesta:
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1227
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 05 Feb 2015 07:52:30 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
    "value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}

### <a name="create-the-content-key"></a>Crear la clave de contenido 

Después de recuperar el certificado X.509 y usa su clave pública para cifrar la clave de contenido, cree una entidad de **ContentKey** y establezca sus valores de propiedad según corresponda.

Uno de los valores que debe establecer cuándo crear el contenido de la clave es el tipo. En caso de que el cifrado de almacenamiento, el valor es '1'. 

En el ejemplo siguiente se muestra cómo crear un **ContentKey** con un conjunto de **ContentKeyType** para el cifrado de almacenamiento ("1") y la **ProtectionKeyType** se establece en "0" para indicar que la clave de protección Id es la huella digital del certificado X.509.  


Solicitar

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
    x-ms-version: 2.11
    Host: media.windows.net
    {
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
    "ContentKeyType":"1", 
    "ProtectionKeyType":"0",
    "EncryptedContentKey":"your encrypted content key",
    "Checksum":"calculated checksum"
    }


Respuesta:
    
    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 777
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
    Server: Microsoft-IIS/8.5
    request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:37:46 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
    "Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
    "LastModified":"2015-02-04T02:37:46.9684379Z",
    "ContentKeyType":1,
    "EncryptedContentKey":"your encrypted content key",
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
    "ProtectionKeyType":0,
    "Checksum":"calculated checksum"}

## <a name="create-an-asset"></a>Crear un activo

En el ejemplo siguiente se muestra cómo crear un activo.

**Solicitud HTTP**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    
    {"Name":"BigBuckBunny" "Options":1}


**Respuesta HTTP**

Si es correcto, se devuelve lo siguiente:
    
    HTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny.mp4",
       "Options":1,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }
    
##<a name="associate-the-contentkey-with-an-asset"></a>Asociar el ContentKey de un activo

Después de crear el ContentKey, asociarlo a su activo mediante la operación $links, tal como se muestra en el ejemplo siguiente:
    
Solicitar:
    
    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
    x-ms-version: 2.11
    Host: media.windows.net

    
    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

Respuesta:

    HTTP/1.1 204 No Content 

##<a name="create-an-assetfile"></a>Crear un AssetFile

La entidad [AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) representa un archivo de audio o vídeo que se almacena en un contenedor de blob. Un archivo de activos siempre está asociado a un activo y un recurso puede contener uno o varios archivos de activos. Se produce un error en la tarea de servicios de Media Encoder si un objeto de archivo activo no está asociado a un archivo digital en un contenedor de blob.

Tenga en cuenta que la instancia de **AssetFile** y el archivo multimedia reales son dos objetos. La instancia de AssetFile contiene metadatos acerca del archivo multimedia, mientras que el archivo multimedia contiene el contenido multimedia real.

Después de cargar el archivo de medios digitales en un contenedor de blob, usará la solicitud HTTP **Combinar** para actualizar la AssetFile con información sobre el archivo multimedia (no se muestra en este tema). 

**Solicitud HTTP**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net
    Content-Length: 164
    
    {  
       "IsEncrypted":"true",
       "EncryptionScheme" : "StorageEncryption", 
       "EncryptionVersion" : "1.0",       
       "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector" : "397304628502661816</d:InitializationVector",
       "Options":0,
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }

**Respuesta HTTP**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT
    
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion": "1.0",
       "EncryptionScheme": "StorageEncryption",
       "IsEncrypted":true,
       "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector":"397304628502661816</d:InitializationVector",
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }
