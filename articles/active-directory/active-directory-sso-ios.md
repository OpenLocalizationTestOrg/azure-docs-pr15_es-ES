<properties
    pageTitle="Cómo habilitar SSO entre aplicaciones en iOS con ADAL | Microsoft Azure"
    description="Cómo usar las características del SDK ADAL para permitir inicio de sesión único entre las aplicaciones. "
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>


# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Cómo habilitar SSO entre aplicaciones en iOS con ADAL


Proporcionar el inicio de sesión único (SSO) para que los usuarios solo tendrán que escribir sus credenciales una vez y dichas credenciales automáticamente trabaja en aplicaciones ahora se espera por los clientes. La dificultad de escribir su nombre de usuario y contraseña en una pantalla pequeña, a menudo veces combinadas con un factor adicional (2FA) como una llamada de teléfono o un código de texted resultados en insatisfacción rápido si un usuario tiene que hacerlo más de una vez para su producto.

Además, si aprovecha una plataforma de identidad que pueden utilizar otras aplicaciones como Microsoft Accounts o una cuenta de trabajo de Office 365, los clientes esperan que las credenciales para que esté disponible para usar todas las aplicaciones independientemente del proveedor.

La plataforma de Microsoft Identity, junto con nuestro SDK de identidad de Microsoft, hace este trabajo por usted y le ofrece la capacidad de satisfacen sus clientes con SSO bien en su propio conjunto de aplicaciones o, al igual que con nuestra capacidad de agente autenticador aplicaciones y, en el dispositivo completo.

Este tutorial le indicará cómo configurar nuestro SDK dentro de la aplicación para proporcionar esta prestación a sus clientes.

En este tutorial se aplica a:

* Azure Active Directory
* B2C de Azure Active Directory
* B2B de Azure Active Directory
* Acceso condicional de Azure Active Directory


Observe que el documento se supone que posee conocimientos sobre cómo [aprovisionar las aplicaciones en el portal de Azure Active Directory heredado](./develop/active-directory-how-to-integrate.md) así como ha integrado la aplicación con la [identidad de Microsoft iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Conceptos SSO en la plataforma de identidad de Microsoft

### <a name="microsoft-identity-brokers"></a>Agentes de identidad de Microsoft

Microsoft proporciona aplicaciones para cada plataforma móvil que permiten a los puentes de credenciales en todas las aplicaciones de diferentes proveedores así como permite especiales características mejoradas que requieren un único lugar seguro de dónde validar las credenciales. Llamamos a estos **agentes**. En iOS y Android se proporcionan a través de aplicaciones descargables que los clientes instalación de forma independiente o se pueden insertar en el dispositivo por una compañía que administra todos o algunos de los dispositivos de sus empleados. Estos agentes de soporte técnico de la administración de seguridad únicamente para algunas aplicaciones o el dispositivo completo en función de los administradores de TI que desea. En Windows, esta funcionalidad se proporciona un selector de cuenta integrado en el sistema operativo, que se conoce técnicamente como el agente de autenticación Web.

Para comprender cómo se utilizan a estos agentes y cómo pueden verlos sus clientes en su flujo de inicio de sesión para la plataforma de Microsoft Identity siga leyendo para obtener más información.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Patrones para iniciar sesión en un dispositivo móvil

Acceso a las credenciales en dispositivos siga dos patrones básicas para la plataforma de Microsoft Identity:

* Agente de no asistidas inicios de sesión
* Intermediario asistidas inicios de sesión

#### <a name="non-broker-assisted-logins"></a>Agente de no asistidas inicios de sesión

Agente de no asistidas inicios de sesión son experiencias de inicio de sesión que se producen en línea con la aplicación y usar el almacenamiento local en el dispositivo para la aplicación. Este almacenamiento pueden compartirse entre aplicaciones, pero las credenciales están enlazadas estrechamente a la aplicación o el conjunto de aplicaciones con esa credencial. Esta es la experiencia más probable es que ha experimentado en muchas aplicaciones móviles donde especificar un nombre de usuario y contraseña en la misma aplicación.

Estos inicios de sesión tienen las siguientes ventajas:

-  Experiencia de usuario existe completamente dentro de la aplicación.
-  Las credenciales pueden compartirse entre las aplicaciones que estén firmadas por el mismo certificado, lo que proporciona una experiencia de inicio de sesión único para el conjunto de aplicaciones.
-  Control de la experiencia de inicio de sesión se proporciona a la aplicación antes y después de iniciar sesión.

Estos inicios de sesión tienen los siguientes inconvenientes:

- Los usuarios no pueden experimentar el inicio de sesión único en a través de todas las aplicaciones que usan un Microsoft Identity sólo a través de esos Identities Microsoft que están es el propietario de la aplicación y ha configurado.
- La aplicación no se puede utilizar con las características más avanzadas de empresa como acceso condicional o use la gama de InTune de productos.
- La aplicación no admite la autenticación basada en certificados para los usuarios empresariales.

Aquí es una representación de cómo funciona el SDK de identidad de Microsoft con el almacenamiento de las aplicaciones para habilitar SSO compartido:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| Azure SDK  | | Azure SDK  |  | Azure SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Intermediario asistidas inicios de sesión

Agente de asistencia inicios de sesión son experiencias de inicio de sesión que se producen dentro de la aplicación de agente y usar el almacenamiento y la seguridad de los agentes para compartir credenciales en todas las aplicaciones en el dispositivo que utilizan la plataforma de Microsoft Identity. Esto significa que las aplicaciones se confían en el agente para usuarios de inicio de sesión. En iOS y Android se proporcionan a través de aplicaciones descargables que los clientes instalación de forma independiente o se pueden insertar en el dispositivo por una empresa que administra el dispositivo para sus usuarios. Un ejemplo de este tipo de aplicación es la aplicación de Azure autenticador en iOS. En Windows, esta funcionalidad se proporciona un selector de cuenta integrado en el sistema operativo, que se conoce técnicamente como el agente de autenticación Web.
La experiencia varía según la plataforma y a veces puede ser problemática para los usuarios si no administrado correctamente. Es probable más familiarizado con este patrón si tiene instalada la aplicación de Facebook y usar la funcionalidad de inicio de sesión de Facebook en otra aplicación. La plataforma de Microsoft Identity aprovecha el mismo patrón.

Para iOS que esto lleva a una transición de"" animación donde la aplicación se envía al fondo mientras las aplicaciones de Azure autenticador llegue a primer plano, seleccione la cuenta que desea que iniciar sesión con el usuario.  

Para Android y Windows se muestra el selector de cuenta sobre la aplicación que es menos interrupciones al usuario.

#### <a name="how-the-broker-gets-invoked"></a>¿Cómo se invoca el agente

Si un agente compatible está instalado en el dispositivo, como la aplicación de Azure autenticador, el SDK de identidad de Microsoft hará automáticamente el trabajo de invocar al agente para cuando un usuario indica que desean que inicie sesión con ninguna cuenta de la plataforma de Microsoft Identity. Esto puede deberse a una Account personal de Microsoft, un trabajo o cuenta de escuela, o una cuenta que proporciona y host en Azure con nuestros productos B2C y B2B. Mediante el cifrado y algoritmos muy seguros garantizamos que las credenciales se le pidan y entrega a su aplicación de forma segura. Los detalles técnicos exacto de estos mecanismos no se publica pero ha desarrollado con la colaboración de Apple y Google.

**El programador tiene la opción de si el SDK de identidad de Microsoft llama al agente o utiliza el flujo de agente no asistido.** Sin embargo si el desarrollador elige no usar el flujo de asistencia agente perder los beneficios de aprovechar las credenciales SSO que el usuario ya haya agregado en el dispositivo, así como impide que su aplicación que se usa con características de empresa que Microsoft proporciona a sus clientes como acceso condicional, capacidades de administración de Intune, y autenticación basada en certificados.

Estos inicios de sesión tienen las siguientes ventajas:

-  Usuario experiencias SSO todas las aplicaciones independientemente del proveedor.
-  La aplicación puede aprovechar las características más avanzadas de empresa como acceso condicional o usar el conjunto de InTune de productos.
-  La aplicación puede admitir autenticación basada en certificados para los usuarios empresariales.
- Experiencia de inicio de sesión mucho más segura como la identidad de la aplicación y el usuario sean verificados por la aplicación de agente con cifrado y algoritmos de seguridad adicional.

Estos inicios de sesión tienen los siguientes inconvenientes:

- En iOS el usuario de transición fuera de la experiencia de la aplicación mientras se escogen credenciales.
- Pérdida de la capacidad para administrar la experiencia de inicio de sesión para los clientes dentro de la aplicación.



Aquí es una representación de cómo funciona el SDK de identidad de Microsoft con las aplicaciones de agente para habilitar SSO de:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```

Cuentan con la información de fondo que debería poder comprender mejor e implementar SSO dentro de la aplicación con la plataforma de Microsoft Identity y SDK.


## <a name="enabling-cross-app-sso-using-adal"></a>Habilitar SSO de aplicación cruzado con ADAL

Aquí usaremos el SDK de iOS ADAL para:

- Activar no intermediario SSO asistido para el conjunto de aplicaciones
- Activa el soporte técnico asistido intermediario SSO

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Activar SSO para no agente asistido SSO

Para no intermediario SSO asistido entre aplicaciones el SDK de identidad de Microsoft administrar gran parte de la complejidad de SSO para usted. Esto incluye Buscar usuario adecuado en la memoria caché y mantener una lista de usuarios ha iniciado sesión para que la consulta.

Para habilitar SSO entre las aplicaciones que usted es el propietario que debe hacer lo siguiente:

1. Garantizar al usuario de aplicaciones el mismo identificador del cliente o identificador de aplicación.
* Asegúrese de que todas las aplicaciones comparten el mismo certificado de firma de Apple para que puedan compartir llaves
* Solicitar el mismo derecho llaves para cada una de las aplicaciones.
* Informar a los SDK de identidad de Microsoft sobre las llaves compartida que desee usar.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Con el mismo ID de cliente o Id. de aplicación para todas las aplicaciones en el conjunto de aplicaciones

Fin de la plataforma de Microsoft Identity saber que ha permitido compartir tokens en sus aplicaciones, cada una de las aplicaciones tendrán que comparten el mismo ID de cliente o identificador de aplicación. Este es el identificador único que le proporcionó al registrar su primera aplicación en el portal.

Es podrán que se pregunte cómo se identificarán diferentes aplicaciones del servicio de Microsoft Identity si utiliza el identificador de aplicación mismo. La respuesta es con los **URI redirigir**. Cada aplicación puede tener varios URI redirigir registrado en el portal de integrado. Cada aplicación en su conjunto tendrá una redirección diferentes URI. Un ejemplo del aspecto está por debajo de:

Redirigir App1 URI:`x-msauth-mytestiosapp://com.myapp.mytestapp`

Redirigir App2 URI:`x-msauth-mytestiosapp://com.myapp.mytestapp2`

Redirigir App3 URI:`x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Estos son anidados en el mismo identificador de cliente / ID de aplicación y busca según la redirección URI devuelto nos en la configuración de SDK.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```


*Tenga en cuenta que el formato de estos URI redirigir se explica a continuación. Puede usar cualquier URI redirigir a menos que desee admitir al agente, en cuyo caso debe buscar algo parecido a la anterior*



#### <a name="create-keychain-sharing-between-applications"></a>Crear llaves compartir entre aplicaciones

Habilitar el uso compartido de claves está fuera del ámbito de este documento y cubierto por Apple en sus documentos [Agregando capacidades](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). Lo importante es que decide que desea las llaves al que se llama y agregar esa capacidad a través de todas las aplicaciones.

Cuando tenga los derechos configurados correctamente, deberían ver un archivo en el directorio de proyectos derecho `entitlements.plist` que contiene un elemento que tiene un aspecto parecido al siguiente:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Una vez que tiene el derecho de llaves habilitado en cada una de las aplicaciones y está listos para usar SSO, más el SDK de identidad de Microsoft sobre las llaves utilizando la siguiente configuración en el `ADAuthenticationSettings` con la siguiente configuración:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [AZURE.WARNING]
Cuando comparte una llave en todas las aplicaciones de cualquier aplicación puede eliminar usuarios o peor eliminar todos los tokens a través de la aplicación. Esto es especialmente desastroso si tiene aplicaciones que se basan en los símbolos de trabajo en segundo plano. Uso compartido de una cadena de claves significa que debe ser muy cuidado en cualquier quita operaciones mediante el SDK de identidad de Microsoft.

¡Eso es todo! El SDK de identidad de Microsoft ahora se compartir credenciales en todas las aplicaciones. La lista de usuarios también se compartirán en varias instancias de aplicación.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Activar SSO para agente asistido SSO

La capacidad de una aplicación para usar a cualquier intermediario que está instalada en el dispositivo está **desactivada de forma predeterminada**. Para utilizar la aplicación con el agente debe realizar una configuración adicional y agregar código a la aplicación.

Siga estos pasos son:

1. Habilitar el modo de agente de llamada del código de la aplicación en el SDK de Microsoft.
2. Establecer un nuevo redireccionamiento URI y proporcionar a la aplicación y el registro de aplicación.
3. Registrar un esquema de dirección URL.
4. soporte técnico de iOS9: agregar un permiso a su archivo info.plist.


#### <a name="step-1-enable-broker-mode-in-your-application"></a>Paso 1: Habilitar el modo de agente en la aplicación
La capacidad para utilizar al agente de la aplicación está activada cuando se crea el "contexto" o la instalación inicial de un objeto de autenticación. Para ello, establezca el tipo de credenciales en el código:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
La `AD_CREDENTIALS_AUTO` configuración le permitirá el SDK de identidad de Microsoft intentar llamar al agente `AD_CREDENTIALS_EMBEDDED` impedirá que el SDK de identidad de Microsoft está llamando al intermediario.

#### <a name="step-2-registering-a-url-scheme"></a>Paso 2: Registrar un esquema de dirección URL
La plataforma de Microsoft Identity utiliza direcciones URL para llamar a los agentes y, a continuación, devuelve el control a la aplicación. Para terminar de ese ida y vuelta necesita un esquema de dirección URL registrado para la aplicación que se conoce la plataforma de Microsoft Identity. Esto puede además de cualquier otro programa de aplicación que puede haber previamente registrado con la aplicación.

> [AZURE.WARNING]
Se recomienda realizar la combinación de la dirección URL bastante únicos para minimizar las probabilidades de otra aplicación utilizando el mismo esquema de dirección URL. Apple no exigir la exclusividad de los esquemas de direcciones URL que se han registrado en la tienda de aplicaciones.

A continuación se muestra un ejemplo de cómo aparece en la configuración del proyecto. También puede hacerlo en XCode así:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Paso 3: Establecer una nueva redirección URI con el esquema de dirección URL

Para asegurarse de que siempre se devuelven los símbolos de credenciales para la aplicación correcta, es necesario para asegurarse de que llamamos a la aplicación de manera que puede comprobar el sistema operativo de iOS. El sistema operativo de iOS informes para las aplicaciones de agente de Microsoft el identificador del paquete de la aplicación de una llamada. Esto no puede ser suplantación por una aplicación no autorizada. Por lo tanto, aprovechar esto junto con el URI de nuestra aplicación de agente para asegurarse de que los tokens se devuelven a la aplicación correcta. Es necesario establecer este redireccionamiento único URI tanto en la aplicación y establecer como redirección URI nuestro portal de programadores.

El redireccionamiento URI debe estar en el formato correcto de:

`<app-scheme>://<your.bundle.id>`

ex: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Este URI redirigir debe ser especificado en el registro de la aplicación con el [portal clásica Azure](https://manage.windowsazure.com/). Para obtener más información sobre el registro de aplicación de Azure AD, consulte [integración con Azure Active Directory](./develop/active-directory-how-to-integrate.md).


##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Paso 3a: agregar una redirección URI en el portal de aplicación y desarrollo para admitir autenticación basada en certificados

Para admitir el certificado de autenticación que una segunda "msauth" debe estar registrado en la aplicación y el [portal clásica Azure](https://manage.windowsazure.com/) a controlar la autenticación de certificado si desea agregar esa compatibilidad en la aplicación basada en.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

ex: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*


#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>Paso 4: iOS9: agregar un parámetro de configuración para la aplicación

ADAL usa-canOpenURL: para comprobar si el agente está instalado en el dispositivo. En iOS Apple 9 bloqueados de lo que pueden consultar combinaciones de una aplicación. Debe agregar "msauth" a la sección LSApplicationQueriesSchemes de la `info.plist file`.

<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>

### <a name="youve-configured-sso"></a>¡Ha configurado el SSO!

Ahora el SDK de identidad de Microsoft automáticamente tanto compartirán credenciales entre las aplicaciones e invocar al agente si está presente en su dispositivo.
