<properties
    pageTitle="Sustitución de la clave de firma en Azure AD | Microsoft Azure"
    description="Este artículo describen los procedimientos recomendados de sustitución de la clave firma para Azure Active Directory"
    services="active-directory"
    documentationCenter=".net"
    authors="gsacavdm"
    manager="krassk"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="gsacavdm"/>

# <a name="signing-key-rollover-in-azure-active-directory"></a>Sustitución de Azure Active Directory de la clave de firma

En este tema se describe lo que necesita saber sobre las claves públicas que se utilizan en Azure Active Directory (AD Azure) para firmar el token de seguridad. Es importante que tenga en cuenta que estas sustitución de claves de forma periódica y, en caso de emergencia, se podría ejecutar inmediatamente. Todas las aplicaciones que usan Azure AD debería poder controlar el proceso de sustitución de la clave o establecer un proceso de conversión manual periódico mediante programación. Siga leyendo para comprender cómo funcionan las teclas a evaluar el impacto de la conversión a la aplicación y cómo actualizar la aplicación o establecer un proceso de conversión manual periódica para controlar la sustitución de la clave si es necesario.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Información general sobre claves de firma en Azure AD

Azure AD usa cifrado de clave pública basado en estándares para establecer la confianza entre sí y las aplicaciones que usa. En la práctica, esto funciona de la siguiente manera: Azure AD utiliza una clave de firma que consta de un par de claves público y privado. Cuando un usuario inicia sesión en una aplicación que utiliza Azure AD para la autenticación, Azure AD crea un token de seguridad que contiene información sobre el usuario. Este token está firmado por Azure AD con su clave privada antes de enviarlo a la aplicación. Para comprobar que el token es válida y realmente se originaron en Azure AD, la aplicación debe validar firma del token mediante la clave pública expuesta por Azure AD contenida en el inquilino [OpenID conectar detección](http://openid.net/specs/openid-connect-discovery-1_0.html) o SAML, WS-Federal de tecnología [documento de metadatos de federación](active-directory-federation-metadata.md).

Por motivos de seguridad, Azure AD firma claves desplazamientos de forma periódica y, en caso de emergencia, podría ser pasado inmediatamente. Cualquier aplicación que se integra con Azure AD debe estar preparado para controlar un evento de sustitución de la clave, independientemente de la frecuencia con que se producen. Si no es así, y la aplicación intenta utilizar una clave de expiración para verificar la firma en un símbolo, se producirá un error en la solicitud de inicio de sesión.

Siempre hay más de una clave válida disponible en el documento de descubrimiento OpenID conectar y el documento de metadatos de federación. La aplicación debería estar preparado para usar cualquiera de las teclas especificadas en el documento, como una tecla puede se acumulen pronto, otro puede ser su reemplazo y así sucesivamente.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Cómo determinar si la aplicación se verán afectada y qué puede hacer al respecto

Cómo controla la aplicación sustitución de la clave depende de variables como el tipo de aplicación o se usó qué protocolo de identidad y la biblioteca. Las secciones siguientes evaluación si los tipos más comunes de aplicaciones se ven afectados por la sustitución de la clave y proporcionan instrucciones sobre cómo actualizar la aplicación de la conversión automática de soporte técnico o actualizar manualmente la clave.

* [Aplicaciones cliente nativo de acceso a los recursos](#nativeclient)
* [Aplicaciones Web y las API de acceso a los recursos](#webclient)
* [Aplicaciones Web / API proteger recursos y creadas con servicios de aplicación de Azure](#appservices)
* [Aplicaciones Web y las API de proteger recursos mediante .NET OWIN OpenID conectarse, WS Federal de tecnología o software de WindowsAzureActiveDirectoryBearerAuthentication intermedio](#owin)
* [Aplicaciones Web y las API de protección de los recursos con software intermedio .NET Core OpenID conectar o JwtBearerAuthentication](#owincore)
* [Aplicaciones Web y las API de proteger recursos mediante el módulo de ad de azure de passport Node.js](#passport)
* [Aplicaciones Web / API proteger recursos y creado con Visual Studio de 2015](#vs2015)
* [Aplicaciones Web de protección de los recursos y creado con Visual Studio de 2013](#vs2013)
* [API de Web protegen los recursos y creado con Visual Studio de 2013](#vs2013_webapi)
* [Aplicaciones Web de protección de los recursos y creado con Visual Studio 2012](#vs2012)
* [Aplicaciones Web de protección de los recursos y creado con Visual Studio 2010, o de 2008 con la identidad de Windows Foundation](#vs2010)
* [Aplicaciones Web y las API de protección de los recursos mediante cualquier otras bibliotecas o implementar manualmente cualquiera de los protocolos admitidos](#other)

Esta guía es **no** aplicable para:

* Aplicaciones agregadas desde la Galería de aplicación Azure AD (incluidos personalizada) tienen orientación independiente con respecto a claves de firma. [Obtener más información.](active-directory-sso-certs.md)
* Aplicaciones publicadas a través de proxy de aplicación no tienen que preocuparse de claves de firma en local.

### <a name="nativeclient"></a>Aplicaciones cliente nativo de acceso a los recursos

Aplicaciones que solo tienen acceso a los recursos (es decir Microsoft Graph, KeyVault, API de Outlook y otras APIs Microsoft) generalmente solo obtienen un token y lo pasan al propietario del recurso. Dado que no protege los recursos, no inspeccionan el token y, por tanto, no es necesario para asegurarse de que se firmó correctamente.

Aplicaciones cliente nativo, si el escritorio o portátil, entran en esta categoría y, por tanto, no se ven afectadas por la conversión.

### <a name="webclient"></a>Aplicaciones Web y las API de acceso a los recursos

Aplicaciones que solo tienen acceso a los recursos (es decir Microsoft Graph, KeyVault, API de Outlook y otras APIs Microsoft) generalmente solo obtienen un token y lo pasan al propietario del recurso. Dado que no protege los recursos, no inspeccionan el token y, por tanto, no es necesario para asegurarse de que se firmó correctamente.

Aplicaciones y web API que usan el flujo de aplicación (las credenciales del cliente / certificado de cliente), se dividen en esta categoría y, por tanto, no se ven afectados por la conversión.

### <a name="appservices"></a>Aplicaciones Web / API proteger recursos y creadas con servicios de aplicación de Azure

Autenticación de Azure Servicios de aplicación o funcionalidad de autorización (EasyAuth) ya tiene la lógica necesaria para controlar la sustitución de la clave automáticamente.

### <a name="owin"></a>Aplicaciones Web y las API de proteger recursos mediante .NET OWIN OpenID conectarse, WS Federal de tecnología o software de WindowsAzureActiveDirectoryBearerAuthentication intermedio

Si la aplicación está utilizando el .NET OWIN OpenID conectarse, WS Federal de tecnología o software intermedio WindowsAzureActiveDirectoryBearerAuthentication, ya tiene la lógica necesaria para controlar la sustitución de la clave automáticamente.

Puede confirmar que la aplicación está utilizando alguno de estos buscando cualquiera de los siguientes fragmentos de código en la aplicación Startup.cs o Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
    });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="owincore"></a>Aplicaciones Web y las API de protección de los recursos con software intermedio .NET Core OpenID conectar o JwtBearerAuthentication

Si la aplicación está utilizando el software intermedio .NET Core OWIN OpenID conectar o JwtBearerAuthentication, ya tiene la lógica necesaria para controlar la sustitución de la clave automáticamente.

Puede confirmar que la aplicación está utilizando alguno de estos buscando cualquiera de los siguientes fragmentos de código en la aplicación Startup.cs o Startup.Auth.cs

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
    });
```

### <a name="passport"></a>Aplicaciones Web y las API de proteger recursos mediante el módulo de ad de azure de passport Node.js

Si la aplicación está utilizando el módulo de passport ad Node.js, ya tiene la lógica necesaria para controlar la sustitución de la clave automáticamente.

Para confirmar que su aplicación passport ADFS buscando el fragmento de código siguiente en app.js la aplicación

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Aplicaciones Web / API proteger recursos y creado con Visual Studio de 2015

Si su aplicación se creó usando una plantilla de aplicación web en Visual Studio de 2015 y seleccionó **Cuentas de trabajo y escuela** desde el menú de **Cambio de la autenticación** , ya tiene la lógica necesaria para controlar la sustitución de la clave automáticamente. Esta lógica, incrustada en el software intermedio OWIN OpenID conectar recupera y almacena las claves desde el documento de descubrimiento OpenID conectarse y actualiza periódicamente.

Si agregó autenticación manualmente la solución, la aplicación que no tenga la lógica de sustitución de la clave es necesario. Debe escribir usted mismo o siga los pasos de [aplicaciones Web / API mediante cualquier otras bibliotecas o implementar manualmente cualquiera de los protocolos admitidos.](#other).

### <a name="vs2013"></a>Aplicaciones Web de protección de los recursos y creado con Visual Studio de 2013

Si su aplicación se creó usando una plantilla de aplicación web de 2013 de Visual Studio y seleccionó **Cuentas de la organización** en el menú de **Cambio de la autenticación** , ya tiene la lógica necesaria para controlar la sustitución de la clave automáticamente. Esta lógica almacena el identificador único de su organización y la información de clave de firma en dos tablas de base de datos asociados con el proyecto. Puede encontrar la cadena de conexión para la base de datos en el archivo Web.config del proyecto.

Si agregó autenticación manualmente la solución, la aplicación que no tenga la lógica de sustitución de la clave es necesario. Debe escribir usted mismo o siga los pasos de [aplicaciones Web / API mediante cualquier otras bibliotecas o implementar manualmente cualquiera de los protocolos admitidos.](#other).

Los siguientes pasos le ayudará a comprobar que la lógica funciona correctamente en la aplicación.

1. En 2013 de Visual Studio, abra la solución y, a continuación, haga clic en la pestaña del **Explorador de servidores** en la ventana de la derecha.
2. Expandir **conexiones de datos**, **DefaultConnection**y, a continuación, las **tablas**. Localizar la tabla **IssuingAuthorityKeys** , haga clic en él y, a continuación, haga clic en **Mostrar datos de tabla**.
3. En la tabla **IssuingAuthorityKeys** , habrá al menos una fila, que corresponde al valor de la huella digital para la clave. Eliminar todas las filas de la tabla.
4. Haga clic en la tabla de **inquilinos** y, a continuación, haga clic en **Mostrar datos de tabla**.
5. En la tabla de **inquilinos** , habrá al menos una fila, que corresponde a un identificador de inquilinos de directorio único. Eliminar todas las filas de la tabla. Si no elimina las filas de la tabla de **inquilinos** y la tabla **IssuingAuthorityKeys** , obtendrá un error en tiempo de ejecución.
6. Crear y ejecutar la aplicación. Una vez que haya iniciado sesión su cuenta, puede detener la aplicación.
7. Vuelva al **Explorador de servidores** y observe los valores de la tabla **IssuingAuthorityKeys** y **los inquilinos** . Verá que han sido rellena automáticamente con la información adecuada del documento de metadatos de federación.

### <a name="vs2013"></a>API de Web protegen los recursos y creado con Visual Studio de 2013

Si ha creado una aplicación de la API de web en Visual Studio de 2013 con la plantilla de Web API y, a continuación, había seleccionado **Cuentas de la organización** en el menú de **Cambio de la autenticación** , ya tiene la lógica necesaria en la aplicación.

Si ha configurado manualmente la autenticación, siga las instrucciones siguientes para obtener información sobre cómo configurar la API de Web para actualizar automáticamente su información de clave.

Fragmento de código siguiente muestra cómo obtener las últimas claves del documento de metadatos de federación y, a continuación, usar el [Controlador de Token JWT](https://msdn.microsoft.com/library/dn205065.aspx) para validar el token. El fragmento de código, se supone que usará su propio mecanismo de almacenamiento en caché para conservar la clave para validar futuros tokens de Azure AD, ya sea en una base de datos, el archivo de configuración o en otro sitio.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Classic Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a>Aplicaciones Web de protección de los recursos y creado con Visual Studio 2012

Si su aplicación se creó en Visual Studio 2012, probablemente ha utilizado la herramienta de acceso e identidad para configurar la aplicación. También es probable que esté utilizando la [Validación de registro de nombre de emisor (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). El VINR es responsable de mantener información acerca de los proveedores de identidad de confianza (Azure AD) y las teclas que se utiliza para validar tokens emitidos por ellos. El VINR también facilita actualizar automáticamente la información de clave almacenada en un archivo Web.config descargando el documento de metadatos de federación más reciente asociado con el directorio, comprobar si la configuración está actualizada con el documento más reciente y actualizar la aplicación para usar la nueva clave según sea necesario.

Si ha creado su aplicación mediante cualquiera de los ejemplos de código o la documentación de tutorial proporcionados por Microsoft, la lógica de sustitución de la clave ya está incluida en el proyecto. Observe que el código siguiente ya existe en el proyecto. Si la aplicación aún no tiene esta lógica, siga los pasos siguientes para agregar y comprobar que funciona correctamente.

1. En el **Explorador de soluciones**, agregue una referencia al ensamblado **System.IdentityModel** para el proyecto adecuado.
2. Abra el archivo **Global.asax.cs** y agregue las siguientes directivas using:
```
using System.Configuration;
using System.IdentityModel.Tokens;
```
3. Agregue el método siguiente al archivo **Global.asax.cs** :
```
protected void RefreshValidationSettings()
{
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
}
```
4. Invocar el método **RefreshValidationSettings()** en el método **Application_Start()** **Global.asax.cs** tal como se muestra:
```
protected void Application_Start()
{
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
}
```

Una vez que haya seguido estos pasos, Web.config de la aplicación se actualizará con la información más reciente del documento de metadatos de federación, incluidas las claves de más recientes. Esta actualización se producirá cada vez recicla su grupo de aplicaciones en IIS; de forma predeterminada IIS se establece en cada 29 horas de aplicaciones de reciclaje.

Siga los pasos siguientes para comprobar que la lógica de sustitución de la clave está funcionando.

1. Después de haber comprobado que la aplicación está utilizando el código anterior, abra el archivo **Web.config** y navegue hasta el **<issuerNameRegistry>** bloque, específicamente buscando siguiente algunas líneas:
```
<issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
```
2. En la **<add thumbprint=””>** configuración, cambie el valor de la huella digital reemplazando cualquier carácter con uno diferente. Guarde el archivo **Web.config** .

3. Generar la aplicación y, a continuación, ejecutarlo. Si puede completar el proceso de inicio de sesión, la aplicación está actualizando correctamente la clave descargando la información requerida de documento de metadatos de federación de su directorio. Si tiene problemas de inicio de sesión, asegúrese de que los cambios en la aplicación son correctos lea el tema [Agregar inicio de sesión en el uso de aplicaciones Web Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) o descargando e inspección en el siguiente ejemplo de código: [Aplicación de nube de varios inquilinos de Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).


### <a name="vs2010"></a>Aplicaciones Web de protección de los recursos y creado con Visual Studio 2008 o 2010 y la versión 1.0 de identidad de Windows Foundation (WIF) para .NET 3.5

Si ha creado una aplicación en la versión 1.0 WIF, no hay ningún mecanismo proporcionado para actualizar automáticamente la configuración de la aplicación para usar una nueva clave.

- *Manera más sencilla* Use las herramientas de FedUtil incluida en el SDK de WIF, que puede recuperar el documento de metadatos más reciente y actualizar su configuración.
- Actualizar la aplicación para .NET 4.5, que incluye la versión más reciente de WIF ubicada en el sistema de nombres. A continuación, puede usar la [Validación de registro de nombre de emisor (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) para realizar las actualizaciones automáticas de configuración de la aplicación.
- Realizar una conversión manual según las instrucciones al final de este documento de orientación.

Instrucciones para usar el FedUtil para actualizar la configuración:

1. Compruebe que tiene la versión 1.0 WIF SDK instalado en su equipo de desarrollo de Visual Studio 2008 o 2010. Si todavía no lo ha instalado, puede [descargarlo desde aquí](https://www.microsoft.com/en-us/download/details.aspx?id=4451) .
2. En Visual Studio, abra la solución, haga clic en el proyecto correspondiente y seleccione **Actualizar metadatos de federación**. Si esta opción no está disponible, FedUtil o la versión 1.0 WIF SDK no se ha instalado.
3. En el símbolo del sistema, seleccione **Actualizar** para actualizar los metadatos de federación. Si tiene acceso al entorno de servidor donde se hospeda la aplicación, puede utilizar opcionalmente del FedUtil [Programador de actualización automática de metadatos](https://msdn.microsoft.com/library/ee517272.aspx).
4. Haga clic en **Finalizar** para completar el proceso de actualización.

### <a name="other"></a>Aplicaciones Web y las API de protección de los recursos mediante cualquier otras bibliotecas o implementar manualmente cualquiera de los protocolos admitidos

Si está utilizando alguna otra biblioteca o implementado manualmente cualquiera de los protocolos admitidos, debe revisar su implementación para asegurarse de que se está recuperando la clave desde el documento de descubrimiento OpenID conectar o el documento de metadatos de federación o la biblioteca. Una forma de comprobarlo es realizar una búsqueda en el código o de la biblioteca en las llamadas a cabo para el documento de descubrimiento OpenID o el documento de metadatos de federación.

Si la clave se almacena en algún lugar o codificado en la aplicación, puede manualmente recuperar la clave y la actualización según corresponda al realizar una conversión manual según las instrucciones al final de este documento de orientación. **Se recomienda encarecidamente que mejorar su aplicación para admitir la conversión automática** con cualquiera de los métodos de esquema en este artículo para evitar futuras interrupciones y sobrecarga si aumenta Azure AD es cadencia de conversión o tiene un desplazamiento de banda emergencia.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Cómo probar la aplicación para determinar si se verán afectado

Puede validar si la aplicación admite la sustitución automática de la clave descargando las secuencias de comandos y siga las instrucciones de [este repositorio GitHub.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-you-application-does-not-support-automatic-rollover"></a>Cómo llevar a cabo una conversión manual si, su aplicación no admite la conversión automática

Si su aplicación no **no** conversión automática de soporte técnico, debe establecer un proceso que periódicamente supervisa claves de firma de Azure AD y realiza una conversión manual en consecuencia. [Este GitHub repositorio](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) contiene secuencias de comandos e instrucciones sobre cómo hacerlo.
