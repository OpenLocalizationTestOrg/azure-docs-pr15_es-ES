<properties
    pageTitle="Azure AD Connect: Solucionar problemas de conectividad | Microsoft Azure"
    description="Se explica cómo solucionar problemas de conectividad con Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="billmath"/>

# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>Solucionar problemas de conectividad con Azure AD Connect
En este artículo se explica cómo funciona la conectividad entre Azure AD Connect y Azure AD y cómo solucionar problemas de conectividad. Estos problemas suelen verse en un entorno con un servidor proxy.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Solucionar problemas de conectividad en el Asistente para instalación
Azure AD Connect está utilizando autenticación moderna (con la biblioteca de ADAL) para la autenticación. El Asistente para instalación y el motor de sincronización correcta requieren machine.config estén configurados correctamente dado que son aplicaciones .NET.

En este artículo le mostrará cómo Fabrikam se conecta a Azure AD a través de su servidor proxy. El servidor proxy se denomina fabrikamproxy y está utilizando el puerto 8080.

En primer lugar, debemos para asegurarse de que [**machine.config**](active-directory-aadconnect-prerequisites.md#connectivity) está configurado correctamente.  
![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

>[AZURE.NOTE]
En algunos blogs no Microsoft documentar que deben realizar cambios a miiserver.exe.config. Sin embargo, este archivo se sobrescriben en cada actualización es así incluso que si funciona durante la instalación inicial, el sistema se dejan de funcionar en la primera actualización. Por este motivo se recomienda actualizar machine.config en su lugar.

El servidor proxy también debe tener las direcciones URL necesarias abiertas. La lista oficial documentada de [intervalos de direcciones IP ](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)y URL de Office 365.

De estos, en la tabla siguiente es al mínimo absoluto que puedan conectarse a Azure AD en absoluto. Esta lista no incluye las características opcionales, como reescritura de contraseña o el estado de conexión de Azure AD. Se documenta aquí para ayudar a solucionar problemas de la configuración inicial.

DIRECCIÓN URL | Puerto | Descripción
---- | ---- | ----
mscrl.Microsoft.com | HTTP/80 | Se usa para descargar listas CRL.
\*. verisign.com | HTTP/80 | Se usa para descargar listas CRL.
\*. entrust.com | HTTP/80 | Usar para descargar listas CRL para MFA.
\*. windows.net | HTTPS/443 | Se usa para iniciar sesión en Azure AD.
Secure.aadcdn.microsoftonline p.com | HTTPS/443 | Se usa para MFA.
\*. microsoftonline.com | HTTPS/443 | Se usa para configurar el directorio de Azure AD e importar o exportar datos.

## <a name="errors-in-the-wizard"></a>Errores en el Asistente
El Asistente para instalación usa dos contextos de seguridad diferentes. En la página **conectarse a Azure AD** que utiliza el usuario actualmente iniciado en. En la página **Configurar** está cambiando a la [cuenta se ejecuta el servicio del motor de sincronización](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts). Las configuraciones de proxy realizamos son globales en el equipo por lo que si hay un problema, el problema tendrá más probable es que aparezca ya en la página **conectarse a Azure AD** en el asistente.

Estos son los errores más comunes que verá en el Asistente para instalación.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>El Asistente para instalación no se ha configurado correctamente
Este error aparecerá cuando el propio asistente no puede llegar al proxy.
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

- Si ve esto, compruebe que el [archivo machine.config](active-directory-aadconnect-prerequisites.md#connectivity) se ha configurado correctamente.
- Si parece correcto, siga los pasos de [Compruebe la conexión de servidor proxy](#verify-proxy-connectivity) para ver si el problema está presente fuera del asistente también.

### <a name="the-mfa-endpoint-cannot-be-reached"></a>No se puede llegar al extremo AMF
Si no se puede llegar el extremo **https://secure.aadcdn.microsoftonline-p.com** y el administrador global tiene AMF habilitado, aparecerá este error.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

- Si ve esto, compruebe que se ha agregado el extremo secure.aadcdn.microsoftonline-p.com al proxy.

### <a name="the-password-cannot-be-verified"></a>No se puede comprobar la contraseña
Si el Asistente para instalación es correcta para la conexión a Azure AD, pero no se puede comprobar la propia contraseña verá esto: ![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

- ¿Es la contraseña de una contraseña temporal y deben cambiarse? ¿Es realmente la contraseña correcta? Intente iniciar sesión en https://login.microsoftonline.com (en otro equipo que el servidor de Azure AD Connect) y compruebe que la cuenta es utilizable.

### <a name="verify-proxy-connectivity"></a>Comprobar la conectividad de servidor proxy
Para comprobar si el servidor de Azure AD Connect tiene conectividad real con el servidor Proxy e Internet se utilizará algunas PowerShell para ver si el servidor proxy permite que las solicitudes de web o no. En un símbolo del sistema de PowerShell, ejecute `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Técnicamente la primera llamada es https://login.microsoftonline.com y esto también funciona, pero otro URI es más rápido responder.)

PowerShell usará la configuración de machine.config ponerse en contacto con el servidor proxy. La configuración de winhttp/netsh no debe influir en estos cmdlets.

Si el servidor proxy está configurado correctamente, debe obtener un estado de éxito: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

Si recibe **no se puede conectar al servidor remoto** , a continuación, trata de PowerShell realizar una llamada directa sin usar al servidor proxy o DNS no está configurado correctamente. Asegúrese de que el archivo **machine.config** está configurado correctamente.
![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

Si el servidor proxy no está configurado correctamente, se producirá un error: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png)
![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

Error | Texto de error | Comentario
---- | ---- | ---- |
403 | Prohibido | No ha abierto el servidor proxy para la dirección URL solicitada. Revisar la configuración de proxy y asegúrese de que las [direcciones URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) que se han abierto.
407 | Se requiere autenticación de proxy | El servidor proxy requiere inicio de sesión y se ha proporcionado ninguno. Si su servidor proxy requiere autenticación, asegúrese de que estén configurados en el archivo machine.config. Además, asegúrese de que está utilizando las cuentas de dominio para el usuario que ejecute al asistente, así como para la cuenta de servicio.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>El patrón de comunicación entre Azure AD Connect y Azure AD
Si ha seguido todos estos pasos anteriores y todavía no pueden conectarse en este momento puede iniciar examina los registros de red. Esta sección es documentar un modelo de conectividad normal y correcta. También están listado falsas rojo comunes que se pueden omitir si está leyendo los registros de red.

- Habrá llamadas a https://dc.services.visualstudio.com. No es necesario tener este abrir en el servidor proxy para la instalación correctamente y se pueden omitir estos.
- Verá que la resolución dns mostrará una lista de los hosts reales en la nsatc.net de espacio de nombre DNS y otros espacios de nombres no en microsoftonline.com. Sin embargo, no habrá ninguna solicitudes de servicio web en los nombres de servidor real y no es necesario agregarlas al proxy.
- Los extremos adminwebservice y provisioningapi (consulte a continuación en los registros) son extremos de detección y se usa para encontrar el extremo real a usar y se difiere dependiendo de su región.

### <a name="reference-proxy-logs"></a>Registros del servidor proxy de referencia
Aquí es una descarga de un registro de proxy real y la página del Asistente de instalación desde donde se tomó (se han quitado las entradas duplicadas al mismo extremo). Puede usarse como referencia para sus propios registros de proxy y la red. Los extremos reales podrían ser diferentes en su entorno (en particular en *cursiva*).

**Conectarse a Azure AD**

Hora | DIRECCIÓN URL
--- | ---
1/11/2016 8:31 | Connect://Login.microsoftonline.com:443
1/11/2016 8:31 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:32 | conectar: / /*anclaje bba800*. microsoftonline.com:443
1/11/2016 8:32 | Connect://Login.microsoftonline.com:443
1/11/2016 8:33 | Connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:33 | conectar: / /*bwsc02 retransmisión*. microsoftonline.com:443

**Configurar**

Hora | DIRECCIÓN URL
--- | ---
1/11/2016 8:43 | Connect://Login.microsoftonline.com:443
1/11/2016 8:43 | conectar: / /*anclaje bba800*. microsoftonline.com:443
1/11/2016 8:43 | Connect://Login.microsoftonline.com:443
1/11/2016 8:44 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | conectar: / /*anclaje bba900*. microsoftonline.com:443
1/11/2016 8:44 | Connect://Login.microsoftonline.com:443
1/11/2016 8:44 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:44 | conectar: / /*anclaje bba800*. microsoftonline.com:443
1/11/2016 8:44 | Connect://Login.microsoftonline.com:443
1/11/2016 8:46 | Connect://provisioningapi.microsoftonline.com:443
1/11/2016 8:46 | conectar: / /*bwsc02 retransmisión*. microsoftonline.com:443

**Sincronización inicial**

Hora | DIRECCIÓN URL
--- | ---
1/11/2016 8:48 | Connect://Login.Windows.NET:443
1/11/2016 8:49 | Connect://adminwebservice.microsoftonline.com:443
1/11/2016 8:49 | conectar: / /*anclaje bba900*. microsoftonline.com:443
1/11/2016 8:49 | conectar: / /*anclaje bba800*. microsoftonline.com:443

## <a name="authentication-errors"></a>Errores de autenticación
Esta sección describen los errores que pueden ser devueltos de ADAL (la biblioteca de autenticación usada Azure AD Connect) y PowerShell. El error explicado le ayudará en los pasos siguientes.

### <a name="invalid-grant"></a>Conceder no válido
Nombre de usuario no válido o contraseña. Para obtener más información, vea [no se puede comprobar la contraseña](#the-password-cannot-be-verified) .

### <a name="unknown-user-type"></a>Tipo de usuario desconocido
No se encuentra el directorio de Azure AD o se ha resuelto. ¿Intenta iniciar sesión con un nombre de usuario en un dominio no comprobado?

### <a name="user-realm-discovery-failed"></a>Error de descubrimiento de territorio de usuario
Problemas de configuración de proxy o la red. La red no puede ser alcanzado, consulte [solucionar problemas de conectividad en el Asistente para instalación](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Contraseña del usuario caducado
Las credenciales han expirado. Cambiar la contraseña.

### <a name="authorizationfailure"></a>AuthorizationFailure
Problema desconocido.

### <a name="authentication-cancelled"></a>Autenticación cancelada
El desafío de autenticación multifactor (AMF) se canceló.

### <a name="connecttomsonline"></a>ConnectToMSOnline
Autenticación se realizó correctamente, pero Azure AD PowerShell tiene un problema de autenticación.

### <a name="azurerolemissing"></a>AzureRoleMissing
Autenticación fue correcta. No es un administrador global.

### <a name="privilegedidentitymanagement"></a>PrivilegedIdentityManagement
Autenticación fue correcta. Se ha habilitado la administración de identidades con privilegios y actualmente no es un administrador global. Para obtener más información, vea [Administración de identidades con privilegios](active-directory-privileged-identity-management-getting-started.md) .

### <a name="companyinfounavailable"></a>CompanyInfoUnavailable
Autenticación fue correcta. No se pudo recuperar la información de la compañía de Azure AD.

### <a name="retrievedomains"></a>RetrieveDomains
Autenticación fue correcta. No se pudo recuperar la información del dominio de Azure AD.

## <a name="troubleshooting-steps-for-previous-releases"></a>Pasos para solucionar problemas de versiones anteriores.
Con las versiones comenzando por el número de versión 1.1.105.0 (publicado de 2016 febrero) se retiró el Asistente para inicio de sesión. Esta sección y la configuración no es necesarios, pero se mantiene como referencia.

Para el inicio de sesión único en el Asistente para que funcione, debe configurarse winhttp. Esto puede hacerse con [**netsh**](active-directory-aadconnect-prerequisites.md#connectivity).  
![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>El Asistente para inicio de sesión no se ha configurado correctamente
Este error aparece cuando el Asistente para inicio de sesión no puede llegar al proxy o el servidor proxy no permite la solicitud.
![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

- Si ve esto, consulte la configuración de proxy en [netsh](active-directory-aadconnect-prerequisites.md#connectivity) y compruebe que es correcta.
![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
- Si parece correcto, siga los pasos de [Compruebe la conexión de servidor proxy](#verify-proxy-connectivity) para ver si el problema está presente fuera del asistente también.

## <a name="next-steps"></a>Pasos siguientes
Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
