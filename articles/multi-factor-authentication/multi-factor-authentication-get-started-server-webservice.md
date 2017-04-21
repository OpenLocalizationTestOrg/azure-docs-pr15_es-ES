<properties 
    pageTitle="Empezar a usar el servicio MFA Server Mobile aplicación Web"
    description="La aplicación de autenticación multifactor de Azure ofrece una opción de autenticación de banda adicionales.  Permite que el servidor AMF usar las notificaciones de inserción a los usuarios."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="getting-started-the-mfa-server-mobile-app-web-service"></a>Empezar a usar el servicio MFA Server Mobile aplicación Web

La aplicación de autenticación multifactor de Azure ofrece una opción de autenticación de banda adicionales. En lugar de realizar una llamada de teléfono automatizado o SMS al usuario durante el inicio de sesión, autenticación multifactor de Azure inserta una notificación a la aplicación de autenticación multifactor de Azure en el usuario smartphone o tableta. El usuario simplemente puntea "Autenticar" (o escriba un PIN y toca "Autenticación") en la aplicación para iniciar sesión.

Para usar la aplicación de autenticación multifactor de Azure, se requiere lo siguiente para que la aplicación puede comunicarse correctamente con el servicio móvil de aplicación Web:

- Consulte requisitos de Hardware y Software para los requisitos de hardware y software
- Debe estar usando v6.0 o posterior del servidor de autenticación multifactor de Azure
- Servicio móvil de Web App debe estar instalado en un servidor de web en Internet ejecuta Microsoft® Internet Information Services (IIS) IIS 7.x o superior.  Para obtener más información sobre IIS consulte [IIS.NET](http://www.iis.net/).
- Compruebe v4.0.30319 ASP.NET está instalado, registrados y permitido
- Servicios de rol requeridos incluyen ASP.NET y compatibilidad de Metabase de IIS 6
- Servicio móvil de aplicación Web debe ser accesible mediante una dirección URL pública
- Servicio móvil de aplicación Web deben estar protegido con un certificado SSL.
- Debe estar instalado el SDK del servicio Web de Azure de autenticación multifactor en IIS 7.x o superior en el servidor que el servidor de autenticación multifactor de Azure
- El SDK del servicio Web de Azure de autenticación multifactor deben estar protegido con un certificado SSL.
- Servicio móvil de aplicación Web puedan conectar con el SDK del servicio Web de Azure de autenticación multifactor sobre SSL
- Servicio móvil de aplicación Web puedan autenticarse en el SDK de servicio Azure multifactor autenticación Web con las credenciales de una cuenta de servicio que es miembro de un grupo de seguridad denominado "PhoneFactor administradores". Esta cuenta de servicio y el grupo existen en Active Directory si el servidor de autenticación multifactor de Azure se ejecuta en un servidor de dominio. Esta cuenta de servicio y grupo existen localmente en el servidor de autenticación multifactor de Azure si no se ha unido a un dominio.


Instalar el portal de usuario en un servidor distinto del servidor de autenticación multifactor de Azure requiere los siguientes pasos:

1. Instalar el servicio web SDK
2. Instalar el servicio web de una aplicación móvil
3. Establecer la configuración de la aplicación móvil en el servidor de autenticación multifactor de Azure
4. Activar la aplicación de autenticación multifactor de Azure para los usuarios finales

## <a name="install-the-web-service-sdk"></a>Instalar el servicio web SDK

Si el SDK del servicio Web de Azure de autenticación multifactor ya no está instalado en el servidor de autenticación multifactor de Azure, vaya a ese servidor y abra el servidor de autenticación multifactor de Azure. Haga clic en el icono de SDK del servicio Web, haga clic en el SDK de servicio Web instalar... botón y siga las instrucciones. El SDK de servicio Web deben estar protegido con un certificado SSL. Un certificado autofirmado es correcto para ello, pero tiene que se pueden importar a la tienda "Entidades de certificación de raíz de confianza" de la cuenta de equipo Local en el servidor web de Portal de usuario para que confíe certificado al iniciar la conexión SSL.

<center>![Programa de instalación](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## <a name="install-the-mobile-app-web-service"></a>Instalar el servicio web de una aplicación móvil
Antes de instalar el servicio web de una aplicación móvil, tenga en cuenta lo siguiente:

- Si el Portal de usuario de autenticación de varios factores de Azure ya está instalado en el servidor a través de Internet, el nombre de usuario, la contraseña y la dirección URL en el SDK de servicio Web se pueden copiar archivo de web.config del Portal de usuario.
- Resulta útil abrir un explorador web en el servidor web a través de Internet y vaya a la dirección URL del SDK de servicio Web que se especificó en el archivo web.config. Si el explorador puede acceder al servicio web correctamente, debe solicitar las credenciales. Escriba el nombre de usuario y la contraseña que se especificaron en el archivo web.config tal y como aparece en el archivo. Asegúrese de que no se muestran certificado advertencias ni errores.
- Si se encuentra un proxy inverso o un firewall delante del servidor de servicio Web de Mobile aplicaciones web y realizar descarga SSL, puede editar el archivo web.config de servicio de Web App Mobile y agregar la siguiente clave para el <appSettings> sección para que el servicio móvil de Web App puede utilizar http en lugar de https. Sin embargo SSL sigue siendo necesario desde la aplicación móvil al proxy de firewall/inversa. <add key="SSL_REQUIRED" value="false"/>

### <a name="to-install-the-mobile-app-web-service"></a>Para instalar el servicio web de una aplicación móvil

<ol>
<li>Abra el Explorador de Windows en el servidor de autenticación multifactor de Azure y vaya a la carpeta donde está instalado el servidor de autenticación multifactor de Azure (por ejemplo, C:\Program Files\Azure con autenticación multifactor). Elegir la versión de 32 bits o 64 bits del archivo de instalación Azure multifactor AuthenticationPhoneAppWebServiceSetup según corresponda para el servidor que se instalará el servicio móvil de aplicación Web en. Copie el archivo de instalación en el servidor a través de Internet.</li>

<li>En el servidor web a través de Internet, se debe ejecutar el archivo de instalación con derechos de administrador. La manera más sencilla de hacerlo es abrir un símbolo del sistema como administrador y desplácese hasta la ubicación donde se ha copiado el archivo de instalación.</li>  

<li>Ejecute el archivo de instalación AuthenticationMobileAppWebServiceSetup multifactor, cambie el sitio si lo desea y cambie el directorio Virtual a un nombre corto como "PA". Puesto que los usuarios deben introducir la URL del servicio de aplicación Web de Mobile en el dispositivo móvil durante la activación, se recomienda un nombre de directorio virtual breve.</li>

<li>Una vez finalizada la instalación de la AuthenticationMobileAppWebServiceSetup multifactor de Azure, vaya a C:\inetpub\wwwroot\PA (o directorio apropiado basado en el nombre del directorio virtual) y editar el archivo web.config.</li>  

<li>Busque las teclas WEB_SERVICE_SDK_AUTHENTICATION_USERNAME y WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD y establezca los valores en el nombre de usuario y la contraseña de la cuenta de servicio que es miembro de la seguridad PhoneFactor Admins grupo (vea la sección requisitos anterior). Puede ser la misma cuenta que se usa como la identidad del Portal de usuario de autenticación multifactor del Azure si se ha instalado previamente. Asegúrese de escribir el nombre de usuario y la contraseña entre las comillas al final de la línea (valor = "" / >). Se recomienda usar un nombre de usuario completo (por ejemplo, DOMINIO\nombre de usuario o equipo ombreDeUsuario).</li>  

<li>Busque la configuración de la aplicación Web Service_pfwssdk_PfWsSdk pfMobile y cambie el valor de "http://localhost:4898/PfWsSdk.asmx" a la dirección URL del SDK de servicio Web que se ejecuta en el servidor de autenticación multifactor de Azure (por ejemplo, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Puesto que se utiliza SSL para esta conexión, debe hacer referencia el SDK de servicio Web por el nombre del servidor y no la dirección IP ya habrá ha emitido el certificado SSL para el nombre del servidor y la dirección URL utilizada debe coincidir con el nombre del certificado. Si el nombre del servidor no se resuelve en una dirección IP del servidor a través de Internet, agregue una entrada al archivo hosts en el servidor para asignar el nombre del servidor de autenticación multifactor de Azure a su dirección IP. Guarde el archivo web.config después de que se han realizado cambios.</li>  

<li>Si el sitio Web que el servicio móvil de Web App se ha instalado en (por ejemplo, sitio Web predeterminado) ya no está enlazado con un certificado de firma públicamente, instale el certificado en el servidor si no están instalados, abra el Administrador de IIS y enlazar el certificado al sitio Web.</li>  

<li>Abra un navegador web desde cualquier equipo y vaya a la dirección URL donde se instaló el servicio móvil de aplicación Web (por ejemplo, https://www.publicwebsite.com/PA). Asegúrese de que no se muestran certificado advertencias ni errores.</li>

### <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Establecer la configuración de la aplicación móvil en el servidor de autenticación multifactor de Azure
Ahora que está instalado el servicio web de una aplicación móvil, debe configurar el servidor de autenticación multifactor de Azure para trabajar con el portal.

#### <a name="to-configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Para establecer la configuración de la aplicación móvil en el servidor de autenticación multifactor de Azure

1. En el servidor de autenticación multifactor de Azure, haga clic en el icono de Portal de usuario. Si los usuarios pueden controlar los métodos de autenticación, en la pestaña Configuración, debajo de permitir a los usuarios seleccionar método, consulte aplicación móvil. Sin esta característica habilitada, los usuarios finales deberán ponerse en contacto con el departamento de soporte técnico para completar la activación de la aplicación móvil.
2. Compruebe el permitir a los usuarios para activar la casilla de la aplicación móvil.
3. Active la casilla Permitir la inscripción del usuario.
4. Haga clic en el icono de la aplicación móvil.
5. Escriba la dirección URL que se usa con el directorio virtual que creó al instalar la AuthenticationMobileAppWebServiceSetup multifactor de Azure. Puede especificar un nombre de cuenta en el espacio proporcionado. Nombre de la compañía se mostrará en la aplicación móvil. Si deja en blanco, se mostrará el nombre de su proveedor de autenticación multifactor creado en el Portal de administración de Azure.



<center>![Programa de instalación](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>
