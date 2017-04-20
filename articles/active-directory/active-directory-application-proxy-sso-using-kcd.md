<properties
    pageTitle="Inicio de sesión único con Proxy de la aplicación | Microsoft Azure"
    description="Trata sobre cómo proporcionar mediante Proxy de aplicación de Azure AD el inicio de sesión único."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="kgremban"/>


# <a name="single-sign-on-with-application-proxy"></a>Inicio de sesión único con Proxy de aplicación

Inicio de sesión único es un elemento clave Azure AD del Proxy de aplicación. Proporciona la mejor experiencia de usuario con los siguientes pasos:

1. Un usuario inicia sesión en la nube  
2. Todas las validaciones de seguridad ocurran en la nube (autenticación previa)  
3. Cuando la solicitud se envía a la aplicación local, el conector de Proxy de aplicación representa al usuario. La aplicación de back-end asume que este es un usuario habitual procedentes de un dispositivo de dominio.

![Diagrama de acceso de usuario final, a través de Proxy de la aplicación a la red corporativa](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

Proxy de aplicación de Azure AD le ayuda a proporcionar una experiencia de inicio de sesión único (SSO) para los usuarios. Use las siguientes instrucciones para publicar sus aplicaciones utilizando SSO de:


## <a name="sso-for-on-prem-iwa-apps-using-kcd-with-application-proxy"></a>SSO para aplicaciones IWA local con KCD Proxy de aplicación
Puede habilitar inicio de sesión único para las aplicaciones mediante la autenticación de Windows integrada (IWA) asignando permisos de aplicación Proxy conectores de Active Directory para representar a los usuarios y enviar y recibir tokens en su nombre.


### <a name="network-diagram"></a>Diagrama de red

Este diagrama explica el flujo cuando un usuario intenta obtener acceso a una aplicación local que usa IWA.

![Diagrama de flujo de autenticación de Microsoft AAD](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

1. El usuario escribe la dirección URL para tener acceso a la aplicación local a través de Proxy de aplicación.
2. Proxy de la aplicación redirige la solicitud a servicios de autenticación de Azure AD para realizar la autenticación previa. En este momento, aplica Azure AD cualquier autenticación correspondiente y directivas de autorización, como la autenticación multifactor. Si el usuario se valida, Azure AD crea un símbolo y envía al usuario.
3. El usuario pasa el token a Proxy de la aplicación.
4. Proxy de aplicación valida el token, extrae el nombre Principal de usuario (UPN) y envía la solicitud, el UPN y el nombre Principal de servicio (SPN) para el conector a través de un canal seguro las autenticado.
5. El conector lleva a cabo negociación de delegación restringida de Kerberos (KCD) con la local AD, representando al usuario para obtener un token Kerberos a la aplicación.
6. Active Directory envía el token de Kerberos para que el conector de la aplicación.
7. El conector envía la solicitud original al servidor de aplicaciones, utilizando el token de Kerberos procedente de AD.
8. La aplicación envía la respuesta al conector que devuelve el servicio Proxy de la aplicación y, finalmente, al usuario.

### <a name="prerequisites"></a>Requisitos previos

Antes de empezar con SSO para Proxy de aplicación, asegúrese de que su entorno está listo con las configuraciones y los valores siguientes:

- Sus aplicaciones, como aplicaciones SharePoint Web, se establecen para usar autenticación de Windows integrada. Para obtener más información vea [Habilitar la compatibilidad con la autenticación Kerberos](https://technet.microsoft.com/library/dd759186.aspx)o de SharePoint, vea [Planear la autenticación Kerberos en SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).

- Todas las aplicaciones tienen nombres principales de servicio.

- El servidor que ejecuta el conector y el servidor que ejecuta la aplicación son dominio unido y parte del mismo dominio o dominios de confianza. Para obtener más información sobre la unión a un dominio, vea [unirse a un equipo a un dominio](https://technet.microsoft.com/library/dd807102.aspx).

- El servidor que ejecuta el conector tiene acceso a leer el TokenGroupsGlobalAndUniversal para los usuarios. Esta es la configuración predeterminada que podría se se han visto afectada por el entorno de consolidación de seguridad. Obtener más ayuda con este en [KB2009157](https://support.microsoft.com/en-us/kb/2009157).

### <a name="active-directory-configuration"></a>Configuración de Active Directory

La configuración de Active Directory varía, dependiendo de si el conector de Proxy de aplicación y el servidor publicado están en el mismo dominio o no.

#### <a name="connector-and-published-server-in-the-same-domain"></a>Conector y servidor publicado en el mismo dominio

1. En Active Directory, vaya a **Herramientas** > **usuarios y equipos**.
2. Seleccione el servidor que ejecuta el conector.
3. Haga clic y seleccione **Propiedades** > **delegación**.
4. Seleccione **Confiar en este equipo para la delegación sólo a servicios especificados** y en los **servicios a los que esta cuenta puede presentar credenciales delegadas**, agregue el valor de la identidad SPN del servidor de aplicaciones.
5. Esto permite que el conector de Proxy de aplicación representar a los usuarios en Active Directory con las aplicaciones definidas en la lista.

![Captura de pantalla de ventana de propiedades de conector SVR](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### <a name="connector-and-published-server-in-different-domains"></a>Conector y servidor publicado en dominios diferentes

1. Para obtener una lista de los requisitos previos para trabajar con KCD entre dominios, consulte [Delegación restringida de Kerberos en dominios](https://technet.microsoft.com/library/hh831477.aspx).
2. En Windows 2012 R2, use la `principalsallowedtodelegateto` propiedad en el servidor de conector para habilitar el Proxy de aplicación al delegado para el servidor de conector, ¿dónde está el servidor publicado `sharepointserviceaccount` y el servidor delegación es `connectormachineaccount`.

        $connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

        Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

        Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE] `sharepointserviceaccount`puede ser la cuenta de máquina SPS o una cuenta de servicio en el que se está ejecutando el grupo de aplicaciones SPS.


### <a name="azure-classic-portal-configuration"></a>Configuración del portal clásica Azure

1. Publicar su aplicación según las instrucciones descritas en [publicar aplicaciones con Proxy de la aplicación](active-directory-application-proxy-publish.md). Asegúrese de seleccionar **Azure Active Directory** como **Método de autenticación previa**.
2. Después de la aplicación aparece en la lista de aplicaciones, selecciónelo y haga clic en **Configurar**.
3. En **Propiedades**, establezca el **Método de autenticación interno** para **La autenticación de Windows**.  
  ![Configuración avanzada de la aplicación](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. Escriba el **SPN de aplicación interno** del servidor de aplicaciones. En este ejemplo, el SPN para nuestra aplicación publicada es http/lob.contoso.com.  

>[AZURE.IMPORTANT] Si el UPN local y el UPN en Azure Active Directory no son idénticos, debe configurar el [delegado la identidad de inicio de sesión](#delegated-login-identity) para la autenticación previa trabajar.

|  |  |
| --- | --- |
| Método de autenticación interno | Si utiliza Azure AD para la autenticación previa, puede establecer un método de autenticación interno para permitir a los usuarios beneficiarse de inicio de sesión único (SSO) para esta aplicación. <br><br> Seleccione **La autenticación de Windows** (IWA) si su aplicación utiliza IWA y puede configurar la delegación de restringida de Kerberos (KCD) para habilitar SSO para esta aplicación. Las aplicaciones que utilizan IWA deben estar configuradas con KCD, en caso contrario, el Proxy de aplicación no podrán publicar estas aplicaciones. <br><br> Seleccione **Ninguno** si su aplicación no utiliza IWA. |
| Aplicación interna SPN | Este es el nombre Principal de servicio (SPN) de la aplicación interna tal como está configurado en Azure AD en local. El SPN se usa el conector de Proxy de aplicación para capturar tokens Kerberos para la aplicación utilizando KCD. |


## <a name="sso-for-non-windows-apps"></a>SSO para aplicaciones no sean de Windows
El flujo de la delegación de Kerberos en Proxy de aplicación de Azure AD se iniciará Azure AD autentica al usuario en la nube. Una vez que la solicitud llegue local, el conector de Proxy de Azure AD aplicación emite un vale de Kerberos en nombre del usuario mediante la interacción con Active Directory local. Este proceso se conoce como delegación restringida de Kerberos (KCD). En la siguiente fase, se envía una solicitud a la aplicación de back-end con este vale Kerberos. Hay una serie de protocolos que definen cómo enviar estas solicitudes. La mayoría de los servidores de Windows no esperan negociar/SPNego ahora es compatible con Proxy de aplicación de Azure AD.

![Diagrama de SSO no son de Windows](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

### <a name="delegated-login-identity"></a>Identidad de inicio de sesión delegada
Identidad de inicio de sesión delegada le ayuda a controlar los dos escenarios de inicio de sesión diferente:

- Aplicaciones de Windows no que normalmente obtener identidad de usuario en el formulario de un nombre de usuario o el nombre de cuenta SAM, no una dirección de correo electrónico (username@domain).
- Configuraciones de inicio de sesión alternativo donde el UPN en Azure AD y el UPN en Active Directory local son diferentes.

Con Proxy de la aplicación, puede seleccionar qué identidad se debe usar para obtener el vale Kerberos. Esta configuración es por aplicación. Algunas de estas opciones son adecuadas para sistemas que no aceptan el formato de la dirección de correo electrónico, otros usuarios han sido diseñados para inicio de sesión alternativo.

![Captura de pantalla de parámetro de identidad de inicio de sesión delegada](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

Si se utiliza la identidad de inicio de sesión delegada, puede que el valor no ser único para todos los dominios o bosques de su organización. Puede evitar este problema mediante la publicación de estas dos veces con dos diferentes grupos de conector de aplicaciones. Puesto que cada aplicación tiene una audiencia de otro usuario, puede unirse a sus conectores a un dominio diferente.


## <a name="working-with-sso-when-on-premises-and-cloud-identities-are-not-identical"></a>Trabajar con SSO cuando local y la nube de identidades no son idénticos.
A menos que se configura en caso contrario, se supone Proxy de aplicación que los usuarios tienen exactamente la misma identidad en la nube y locales. Puede configurar, para cada aplicación, qué identidad se debe usar cuando se realiza el inicio de sesión único.  

Esta característica permite muchas organizaciones que tienen diferentes identidades local y la nube tener SSO de la nube a aplicaciones local sin necesidad de que los usuarios especifiquen contraseñas y nombres de usuario diferentes. Esto incluye organizaciones que:

- Tener varios dominios internamente (joe@us.contoso.com, joe@eu.contoso.com) y un único dominio en la nube (joe@contoso.com).

- Tiene el nombre de dominio no enrutables internamente (joe@contoso.usa) y uno legal en la nube.

- No use nombres de dominio internamente (joe)

- Usar alias diferentes local y en la nube. Por ejemplo joe-johns@contoso.comVS.joej@contoso.com  

También le ayudará a con aplicaciones que no se aceptan las direcciones en el formulario de dirección de correo electrónico, que es un escenario muy común para servidores de Windows no back-end.


### <a name="setting-sso-for-different-cloud-and-on-prem-identities"></a>Configurar SSO para diferentes identidades de nube y local

1. Establecer la configuración de Azure AD Connect para que la identidad principal será la dirección de correo electrónico (correo). Se realiza como parte del proceso de personalizar, cambiando el campo **Nombre de usuario principal** de la configuración de sincronización. Nota que estos valores también determinan cómo los usuarios iniciar sesión en Office 365, Windows10 dispositivos y otras aplicaciones que usen Azure AD como su almacén de identidades.  
  ![Identificar la captura de pantalla de los usuarios: la lista desplegable de nombre Principal de usuario](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. En la aplicación configuración de la aplicación que desea modificar, seleccione el **Delegado la identidad de inicio de sesión** que se utilizarán:
  - Nombre principal del usuario:joe@contoso.com  
  - Nombre principal de usuario alternativo:joed@contoso.local  
  - Parte del nombre de usuario principio del nombre de usuario: Juan  
  - Parte del nombre de usuario alternativo principio del nombre de usuario: joed  
  - Nombre de cuenta SAM local: configuración de controlador de dominio local según

  ![Captura de pantalla del menú desplegable de identidad de inicio de sesión delegada](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)  

### <a name="troubleshooting-sso-for-different-identities"></a>Solución de problemas de SSO para identidades diferentes
Si hay un error en el proceso SSO aparecerá en el registro de eventos del equipo de conector como se explica en [solución de problemas](active-directory-application-proxy-troubleshoot.md).
Sin embargo, en algunos casos, la solicitud se enviarán correctamente a la aplicación de back-end mientras se responde a esta aplicación en diversas otras respuestas HTTP. Solución de problemas de estos casos debe empezar examinando el número de evento 24029 en el equipo de conector en el registro de eventos de sesión de Proxy de la aplicación. La identidad del usuario que se utilizó para delegación aparecerá en el campo "usuario" en los detalles del evento. Para activar el registro de sesión, seleccione **Mostrar analítico y depurar registros** en la vista de Visor de menú eventos.


## <a name="see-also"></a>Vea también

- [Publicar aplicaciones con Proxy de aplicación](active-directory-application-proxy-publish.md)
- [Solucionar problemas que tiene con Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)
- [Trabajar con aplicaciones de notificaciones](active-directory-application-proxy-claims-aware-apps.md)
- [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)

Para las últimas noticias y actualizaciones, consulte el [blog de Proxy de aplicación](http://blogs.technet.com/b/applicationproxyblog/)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg
