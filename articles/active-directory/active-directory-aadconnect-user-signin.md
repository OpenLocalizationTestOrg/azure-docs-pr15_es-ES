<properties
    pageTitle="Azure AD Connect: Usuario inicie sesión en | Microsoft Azure"
    description="Azure AD Connect de inicio de sesión para una configuración personalizada."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/16/2016"
    ms.author="billmath"/>



# <a name="azure-ad-connect-user-sign-on-options"></a>Azure AD conectar de inicio de sesión en Opciones

Azure AD Connect permite a los usuarios iniciar sesión los recursos de nube y locales con las mismas contraseñas. Este artículo describe los conceptos clave para cada modelo de identidad para que pueda elegir la identidad que desea usar el inicio de sesión de Azure AD.

Si ya está familiarizado con el modelo de identidad de Azure AD y desea obtener más información sobre un método específico, haga clic debajo en el tema correspondiente.

* [Sincronización de la contraseña](#password-synchronization)
* [SSO federado (con ADFS)](#federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm)


## <a name="choosing-a-user-sign-in-method"></a>Elegir un método de inicio de sesión de usuario
Para la mayoría de las organizaciones que solo desea habilitar a usuario inicie sesión Office 365, aplicaciones SaaS y otro Azure AD basado en los recursos, se recomienda la opción de sincronización de la contraseña de forma predeterminada.
Algunas organizaciones, sin embargo, tienen determinadas razones para usar un inicio de sesión federado opción como AD FS.  Se incluyen:

- Su organización ya cuenta con AD FS o un 3 º implementado un proveedor de servicios de federación de terceros
- La directiva de seguridad prohíba sincronizar hash de contraseña en la nube
- Requerir que los usuarios experimentar SSO transparente (sin contraseña adicional solicita) al acceso a los recursos de la nube de dominio unido equipos en la red corporativa
- Requerir algunas capacidades específicas que tiene AD FS
    - Local con autenticación multifactor con un proveedor de terceros o las tarjetas inteligentes (más acerca de los proveedores de terceros AMF de AD FS en Windows Server 2012 R2)
    - Características de integración de Active Directory, como el bloqueo de cuenta suaves o directiva de horas de trabajo y la contraseña de AD
    - Acceso condicional a ambos local o en la nube recursos mediante el registro de dispositivo, combinación de Azure AD o directivas Intune MDM

### <a name="password-synchronization"></a>Sincronización de contraseñas
Con la sincronización de contraseña, los valores hash de las contraseñas de usuario se sincronizan desde Active Directory local a Azure AD.  Cuando las contraseñas se cambian o restablecer local, las nuevas contraseñas se sincronizan inmediatamente a Azure AD para que los usuarios siempre pueden usar la misma contraseña para los recursos de nube como lo hacen en local.  Las contraseñas nunca se envían a Azure AD ni almacenadas en Azure AD en texto sin cifrar.
Sincronización de contraseñas puede usarse junto con reescritura de contraseña para habilitar self restablece la contraseña de servicio de Azure AD.

<center>![Nube](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[Más información sobre la sincronización de contraseñas](active-directory-aadconnectsync-implement-password-synchronization.md)


### <a name="federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm"></a>Federación con ADFS nuevos o existentes en el conjunto de servidores de Windows Server 2012 R2
Con federados inicio de sesión, los usuarios pueden iniciar sesión Azure AD según servicios con sus contraseñas local y, mientras se encuentra en la red corporativa, sin tener que escribir sus contraseñas de nuevo.  La opción de la federación con AD FS le permite implementar una nueva o especifique un AD FS existente en el conjunto de servidores de Windows Server 2012 R2.  Si decide especificar una granja existente, Azure AD Connect configurará la confianza entre la granja y Azure AD para que los usuarios pueden iniciar sesión.

<center>![Nube](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="to-deploy-federation-with-ad-fs-in-windows-server-2012-r2-you-will-need-the-following"></a>Para implementar la federación con AD FS en Windows Server 2012 R2, necesitará la siguiente

Si va a implementar una nueva granja de:

- Un servidor de Windows Server 2012 R2 para el servidor de federación.
- Un servidor de Windows Server 2012 R2 para el Proxy de la aplicación Web.
- un archivo .pfx con un certificado SSL para el nombre de servicio de federación previsto, como fs.contoso.com.

Si va a implementar una nueva granja o utilizando una granja existente:

- Credenciales de administrador local en los servidores de federación.
- Credenciales de administrador local en cualquier grupo de trabajo (no unidos a dominio) en el que va a implementar la función de Proxy de la aplicación Web de servidores.
- El equipo en el que se ejecuta al asistente debe poder conectarse a todos los equipos en los que desea instalar AD FS o Proxy de aplicación Web mediante administración remota de Windows.

[Configuración de SSO con AD FS](./aad-connect/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) 

#### <a name="sign-on-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Iniciar sesión con una versión anterior de AD FS o una solución de terceros

Si ya ha configurado el inicio de sesión de nube sobre el uso de una versión anterior de AD FS (por ejemplo, AD FS 2.0) o un proveedor de servicios de federación de terceros, puede omitir el inicio de sesión de usuario en la configuración a través de Azure AD Connect.  Esto le permitirá obtener la última sincronización y otras funciones de Azure AD Connect sin dejar de usar la solución existente para iniciar sesión en.

[Lista de compatibilidad de federación de terceros de Azure AD](./active-directory-aadconnect-federation-compatibility.md)

## <a name="user-sign-in-and-user-principal-name-upn"></a>Inicio de sesión de usuario y nombre principal de usuario (UPN)

### <a name="understanding-user-principal-name"></a>Nombre principal de usuario de descripción

En Active Directory, el sufijo UPN predeterminado es el nombre DNS del dominio en el que se crea la cuenta de usuario. En la mayoría de los casos, este es el nombre de dominio registrado como dominio de empresa en Internet. Sin embargo, puede agregar más sufijos con dominios y Active Directory confianzas.

El UPN del usuario tiene el formato username@domai. Por ejemplo, para un active directory contoso.com usuario John podría tener UPN john@contoso.com. El UPN del usuario se basa en RFC 822. Aunque UPN y correo electrónico comparten el mismo formato, el valor de UPN para un usuario puede o no puede ser igual a la dirección de correo electrónico del usuario.

### <a name="user-principal-name-in-azure-ad"></a>Nombre principal de usuario en Azure AD

Asistente de Azure AD Connect se use el atributo userPrincipalName o le permite especificar el atributo (en la instalación personalizada) para su uso locales como el nombre de usuario principal en Azure AD. Este es el valor que se usará para iniciar sesión en Azure AD. Si el valor del atributo de nombre principal del usuario no se corresponde con un dominio comprobado en Azure AD, a continuación, Azure AD para reemplazarla con un valor predeterminado. onmicrosoft.com valor.

Todos los directorios de Azure Active Directory se suministra con un nombre de dominio integrados en la contoso.onmicrosoft.com de formulario que le permite empezar a usar Azure u otros servicios de Microsoft. Puede mejorar y simplificar la experiencia de inicio de sesión usa dominios personalizados. Para obtener información sobre el dominio personalizado nombres en Azure AD y cómo comprobar un dominio, lea [Agregar el nombre de dominio personalizado para Azure Active Directory](active-directory-add-domain.md#add-your-custom-domain-name-to-azure-active-directory)

## <a name="azure-ad-sign-in-configuration"></a>Configuración de inicio de sesión de anuncio Azure

### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Azure AD inicio de sesión en una configuración con Azure AD Connect
Experiencia de inicio de sesión de AD Azure depende de si es capaz de para que coincida con el sufijo del nombre principal de usuario de un usuario que se sincronizan con uno de los dominios personalizados comprobados en el directorio de Azure AD Azure AD. Azure AD Connect proporciona ayuda al configurar la configuración de inicio de sesión Azure AD, para que la experiencia de inicio de sesión de usuario en la nube es similar a la experiencia de local. Azure AD Connect muestra los sufijos UPN definidos para los dominios e intenta hacer coincidir con un dominio personalizado en Azure AD y ayuda con la acción apropiada que debe realizarse.
La página de inicio de sesión de Azure AD muestra los sufijos UPN definidos para el Active Directory local y muestra el estado correspondiente con cada sufijo. Los valores de estado pueden ser una de las a continuación:

| Estado | Descripción | Acción necesaria |
|:----|:----|:----|
|Comprobado| Azure AD Connect encuentra que una coincidencia comprobado el dominio en Azure AD. Todos los usuarios para este dominio puedan iniciar sesión con sus credenciales local| Es necesario realizar ninguna acción |
|No comprobado| Azure AD Connect puede encontrar un dominio personalizado coincidente en Azure AD pero no se ha verificado. Sufijo UPN de los usuarios de este dominio cambiará a predeterminado. onmicrosoft.com sufijo después de la sincronización si no se comprueba el dominio. | Comprobar el dominio personalizado en Azure AD. [Aprende más](./active-directory-add-domain.md#verify-the-domain-name-with-azure-ad) |  
|No agregar| Azure AD Connect no puede encontrar un dominio personalizado correspondiente al sufijo UPN. Sufijo UPN de los usuarios de este dominio cambiará a predeterminado. onmicrosoft.com si no se agrega el dominio y verifeid en Azure.| Agregar y comprobar un dominio personalizado correspondiente a [Aprender más](./active-directory-add-domain.md) el sufijo UPN|

Azure AD sesión en la página muestra la suffix(s) UPN definido para el Active Directory local y el dominio personalizado correspondiente en Azure AD con el estado de verificación actual. En la instalación personalizada, ahora puede seleccionar el atributo de nombre principal de usuario en la página de **Inicio de sesión** de Azure AD.

![Página de inicio de sesión de anuncio Azure](./media/active-directory-aadconnect-user-signin/custom_azure_sign_in.png)

Puede hacer clic en el botón Actualizar para volver a recuperar el último estado de los dominios personalizados de Azure AD.

###<a name="selecting-attribute-for-user-principal-name-in-azure-ad"></a>Seleccionar el atributo de nombre principal de usuario en Azure AD

UserPrincipalName - atributo userPrincipalName es el atributo usuarios vayan a usar cuando inicie sesión en Azure AD y Office 365. Los dominios que se utiliza, también conocido como-sufijo UPN, deben comprobarse en Azure AD antes de que los usuarios están sincronizados. Se recomienda mantener predeterminado atributo userPrincipalName. Si este atributo es no enrutables y no se puede comprobar que es posible que seleccionar otro atributo, por ejemplo correo electrónico, como el atributo contiene el identificador de inicio de sesión. Esto se conoce como Id. de alternativo El valor del atributo ID alternativo debe seguir el estándar RFC822. Un identificador alternativo puede usarse con federación SSO como la solución de inicio de sesión y la contraseña de inicio de sesión único (SSO).

>[AZURE.NOTE] Con un identificador alternativo no es compatible con todas las cargas de trabajo de Office 365. Para obtener más información, consulte [Configurar ID de inicio de sesión alternativo](https://technet.microsoft.com/library/dn659436.aspx).

#### <a name="different-custom-domain-states-and-effect-on-azure-sign-in-experience"></a>Estados de dominio personalizado diferente y efecto en la experiencia de inicio de sesión de Azure
Es muy importante comprender la relación entre los Estados de dominio personalizado en el directorio de Azure AD y los sufijos UPN definida en local. Deje que nosotros atraviesan las posibles Azure sesión experiencias diferentes al configurar sycnhronization con Azure AD creador.

Para obtener la información siguiente, supongamos que le interesa el contoso.com de sufijo UPN que se usa en el directorio local como parte del UPN, por ejemplo user@contoso.com.

###### <a name="express-settings--password-synchronization"></a>Configuración rápida y sincronización de contraseñas
| Estado         | Efecto en la experiencia del usuario de inicio de sesión Azure |
|:-------------:|:----------------------------------------|
| No agregar | En este caso se ha agregado ningún dominio personalizado para contoso.com en el directorio de Azure AD. Los usuarios que tienen UPN local con sufijo @contoso.com, no podrá usar su UPN local para iniciar sesión en Azure. En su lugar tendrá utilizar un UPN nuevo proporcionado por Azure AD agregando el sufijo para el directorio de Azure AD predeterminado. Por ejemplo, si la sincronización de los usuarios al directorio de Azure AD azurecontoso.onmicrosoft.com, a continuación, el usuario local user@contoso.com se proporcionará un UPNuser@azurecontoso.onmicrosoft.com|
| No comprobado | En este caso tenemos un contoso.com dominio personalizado que agregó en el directorio de Azure AD, pero no se ha comprobado. Si continuar con la sincronización de los usuarios sin comprobar el dominio, a continuación, los usuarios se asignará un nuevo UPN por Azure AD como lo hizo en el escenario 'Agregados no'.|
| Comprobado | En este caso, tenemos un dominio personalizado de contoso.com ya agregado y comprobada en Azure AD para el sufijo UPN. Los usuarios podrán usar su nombre principal de usuario local, por ejemplo, user@contoso.com, para iniciar sesión en Azure después de que se sincronizan con Azure AD|

###### <a name="ad-fs-federation"></a>Federación de AD FS
No se puede crear una federación con el valor predeterminado. dominio onmicrosoft.com en Azure AD o un dominio personalizado no verificado en Azure AD. Cuando se ejecuta el Asistente de Azure AD Connect, si selecciona un dominio no verificado para crear una federación con luego Azure AD Connect le plantean es necesarios crear registros donde se hospeda el DNS para el dominio. Para obtener más información, vea [aquí](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Si ha seleccionado la opción de inicio de sesión de usuario como "De la federación con AD FS", debe tener un dominio personalizado para continuar con la creación de una federación en Azure AD. Para nuestra discusión, esto significa que deberíamos tener un agregado en el directorio de Azure AD de dominio personalizado contoso.com.

| Estado         | Efecto en la experiencia del usuario de inicio de sesión Azure |
|:-------------:|:----------------------------------------|
| No agregar | En este caso Azure AD Connect no pudo encontrar un dominio personalizado coincidente para la contoso.com sufijo UPN en el directorio de Azure AD. Debe agregar un dominio personalizado contoso.com si necesita los usuarios para iniciar sesión con AD FS con su UPN local como user@contoso.com.|
| No comprobado | En este caso Azure AD Connect le plantean apropiados detalles acerca de cómo puede comprobar el dominio posteriormente|
| Comprobado | En este caso puede continuar con la configuración sin ninguna acción|  

## <a name="changing-user-sign-in-method"></a>Cambiar el método de inicio de sesión de usuario

Puede cambiar el método de inicio de sesión de usuario de federación para el uso de la tareas que estará disponible en Azure AD Connect después de la configuración inicial de Azure AD Connect con el Asistente para sincronización de la contraseña. Vuelva a ejecutar al Asistente de Azure AD Connect y se presentará con una lista de tareas que puede realizar. Seleccione **Inicio de sesión de usuario de cambio** de la lista de tareas. 

![Cambiar el inicio de sesión de usuario"](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

En la página siguiente, se le pedirá que proporcione las credenciales de Azure AD.

![Conectarse a Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

En la página **de inicio de sesión de usuario** , seleccione **La sincronización de contraseñas**. Cambiará el directorio de federado a una administrada.

![Conectarse a Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin3.png)

>[AZURE.NOTE] Si logran solo un modificador temporal a la sincronización de contraseñas, compruebe la **no convertir las cuentas de usuario**. No comprobar la opción llevará a la conversión de cada usuario federado y puede tardar varias horas.
  
## <a name="next-steps"></a>Pasos siguientes
Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

Obtenga más información sobre [Azure AD Connect: conceptos de diseño](active-directory-aadconnect-design-concepts.md)


