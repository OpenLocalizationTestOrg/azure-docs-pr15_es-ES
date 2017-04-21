<properties 
    pageTitle="Actualizar al agente de PhoneFactor al servidor de Azure autenticación multifactor"
    description="Este documento describe cómo empezar a trabajar con el servidor de Azure AMF y cómo actualizar desde el agente de phonefactor anterior."
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

# <a name="upgrading-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Actualizar al agente de PhoneFactor al servidor de Azure autenticación multifactor

Actualizar desde el v5.x PhoneFactor agente o versiones anteriores en el servidor de autenticación multifactor de Azure requiere el agente de PhoneFactor y componentes filiales desinstalar antes de que el servidor de autenticación multifactor y sus componentes filiales se pueden instalar.

## <a name="to-upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Para actualizar al agente de PhoneFactor al servidor de autenticación multifactor de Azure
<ol>
<li>En primer lugar, copia el archivo de datos de PhoneFactor. La ubicación de instalación predeterminada es C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata.


<li>Si está instalado el Portal de usuario:</li>
<ol>
<li>Vaya a la carpeta de instalación y haga una copia del archivo web.config. La ubicación de instalación predeterminada es C:\inetpub\wwwroot\PhoneFactor.</li>


<li>Si ha agregado temas personalizados en el portal, realizar una copia de la carpeta personalizada debajo del directorio C:\inetpub\wwwroot\PhoneFactor\App_Themes.</li>


<li>Desinstalar el Portal de usuario mediante el agente de PhoneFactor (sólo está disponible si se instala en el mismo servidor que el agente de PhoneFactor) o a través de Windows programas y características.</li></ol>




<li>Si está instalado el servicio móvil de Web App:
<ol>
<li>Vaya a la carpeta de instalación y haga una copia del archivo web.config. La ubicación de instalación predeterminada es C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.</li>
<li>Desinstalar el servicio Web móvil de aplicación a través de Windows programas y características.</li></ol>

<li>Si está instalado el SDK de servicio Web, desinstale mediante el agente de PhoneFactor o Windows programas y características.

<li>Desinstalar el agente de PhoneFactor a través de Windows programas y características.

<li>Instalar al servidor de autenticación multifactor. Tenga en cuenta que la ruta de instalación se selecciona desde el registro de la instalación anterior de PhoneFactor agente por lo que debe instalar en la misma ubicación (por ejemplo, C:\Program Files\PhoneFactor). Nuevas instalaciones tendrá un valor predeterminado diferente (por ejemplo, C:\Program Files\Multi Factor autenticación de servidor) de la ruta de instalación. El archivo de datos a la izquierda por el agente de PhoneFactor anterior se debe actualizar durante la instalación, por lo que la configuración y los usuarios debe seguir siendo existe después de instalar al servidor de autenticación multifactor nuevo.

<li>Si se le solicita, activar el servidor de autenticación multifactor y asegúrese de que se le asigna al grupo de replicación correcto.

<li>Si previamente se ha instalado el SDK de servicio Web, instale el SDK de servicio Web nueva a través de la interfaz de usuario del servidor de autenticación multifactor. Observe que el nombre del directorio virtual predeterminado es ahora "MultiFactorAuthWebServiceSdk" en lugar de "PhoneFactorWebServiceSdk". Si desea usar el nombre anterior, debe cambiar el nombre del directorio virtual durante la instalación. En caso contrario, si permite la instalación usar el nuevo nombre de forma predeterminada, tendrá que cambiar la dirección URL en todas las aplicaciones que hacen referencia el SDK de servicio Web como el Portal de usuario y el servicio móvil de Web App para que apunten a la ubicación correcta.

<li>Si el Portal de usuario se instaló anteriormente en el servidor de agente de PhoneFactor, instale el nuevo Portal de usuario de autenticación multifactor a través de la interfaz de usuario del servidor de autenticación multifactor. Observe que el nombre del directorio virtual predeterminado es ahora "MultiFactorAuth" en lugar de "PhoneFactor". Si desea usar el nombre anterior, debe cambiar el nombre del directorio virtual durante la instalación. En caso contrario, si permite la instalación usar el nuevo nombre de forma predeterminada, debe haga clic en el icono de Portal de usuario en el servidor de autenticación multifactor y actualizar la URL del Portal de usuario en la pestaña Configuración.

<li>Si previamente se ha instalado el Portal de usuario o el servicio móvil de aplicación Web en un servidor diferente desde el agente de PhoneFactor:
<ol>
<li>Vaya a la ubicación de instalación (por ejemplo, C:\Program Files\PhoneFactor) y copie el installer(s) correspondiente al otro servidor. Hay instaladores de 32 y 64 bits para el Portal de usuario y el servicio móvil de aplicación Web. Se denominan MultiFactorAuthenticationUserPortalSetupXX.msi y MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi respectivamente.</li>
<li>Para instalar el Portal de usuario en el servidor web, abra un símbolo del sistema como administrador y ejecute la MultiFactorAuthenticationUserPortalSetupXX.msi. Observe que el nombre del directorio virtual predeterminado es ahora "MultiFactorAuth" en lugar de "PhoneFactor". Si desea usar el nombre anterior, debe cambiar el nombre del directorio virtual durante la instalación. En caso contrario, si permite la instalación usar el nuevo nombre de forma predeterminada, debe haga clic en el icono de Portal de usuario en el servidor de autenticación multifactor y actualizar la URL del Portal de usuario en la pestaña Configuración. Los usuarios existentes tendrán que estar informados sobre la nueva dirección URL.</li>
<li>Vaya al Portal de usuario (por ejemplo, C:\inetpub\wwwroot\MultiFactorAuth) de la ubicación de instalación y editar el archivo web.config. Copie los valores en las secciones appSettings y applicationSettings del archivo web.config original que se realizan copias de seguridad antes de la actualización en el nuevo archivo de web.config. Si el nuevo nombre del directorio virtual predeterminado se mantiene al instalar el SDK de servicio Web, cambie la dirección URL en la sección applicationSettings para que apunten a la ubicación correcta. Si el resto de valores predeterminados se ha cambiado en el archivo web.config anterior, aplicar los mismos cambios en el nuevo archivo de web.config.</li>
<li>Para instalar el servicio móvil de Web App en el servidor web, abra un símbolo del sistema como administrador y ejecute la MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi. Observe que el nombre del directorio virtual predeterminado es ahora "MultiFactorAuthMobileAppWebService" en lugar de "PhoneFactorPhoneAppWebService". Si desea usar el nombre anterior, debe cambiar el nombre del directorio virtual durante la instalación. Desea elegir un nombre más corto para facilitar a los usuarios finales a escribir en sus dispositivos móviles. En caso contrario, si permite la instalación usar el nuevo nombre de forma predeterminada, debe haga clic en el icono de la aplicación móvil en el servidor de autenticación multifactor y actualizar la dirección URL del servicio de Mobile aplicación Web.</li>
<li>Vaya al servicio de Web App Mobile instalar ubicación (por ejemplo, C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) y editar el archivo web.config. Copie los valores en las secciones appSettings y applicationSettings del archivo web.config original que se realizan copias de seguridad antes de la actualización en el nuevo archivo de web.config. Si el nuevo nombre del directorio virtual predeterminado se mantiene al instalar el SDK de servicio Web, cambie la dirección URL en la sección applicationSettings para que apunten a la ubicación correcta. Si el resto de valores predeterminados se ha cambiado en el archivo web.config anterior, aplicar los mismos cambios en el nuevo archivo de web.config.</li></ol>
