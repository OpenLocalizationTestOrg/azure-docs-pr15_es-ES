<properties
    pageTitle="Azure AD Connect: Instalación personalizada | Microsoft Azure"
    description="Este documento describe las opciones de instalación personalizada para Azure AD Connect. Siga estas instrucciones para instalar Active Directory a través de Azure AD Connect."
    services="active-directory"
    keywords="¿Qué es Azure AD Connect, instalar componentes necesarios para Azure AD de Active Directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/13/2016"
    ms.author="billmath"/>

# <a name="custom-installation-of-azure-ad-connect"></a>Instalación personalizada de Azure AD Connect
De conexión de Azure AD **configuración personalizada** se utiliza cuando desea más opciones para la instalación. Se usa si tiene varios bosques o si desea configurar características opcionales que no se tratan en la instalación rápida. Se utiliza en todos los casos donde la opción de [**instalación express**](active-directory-aadconnect-get-started-express.md) no cumpla su implementación o la topología.

Antes de iniciar la instalación de Azure AD Connect, asegúrese de descargar [Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) y completar el requisito previo pasos en [Azure AD Connect: Hardware y los requisitos previos](../active-directory-aadconnect-prerequisites.md). También asegúrese de que haber requerido cuentas disponibles como se describe en [permisos y cuentas de Azure AD Connect](active-directory-aadconnect-accounts-permissions.md).

Si una configuración personalizada no coincide con la topología, por ejemplo, para la sincronización de directorios de actualización, consulte [la documentación relacionada con](#related-documentation) otros escenarios.

## <a name="custom-settings-installation-of-azure-ad-connect"></a>Instalación de una configuración personalizada de Azure AD Connect

### <a name="express-settings"></a>Configuración rápida
En esta página, haga clic en **Personalizar** para iniciar una instalación de configuración personalizada.

### <a name="install-required-components"></a>Instalar componentes necesarios
Cuando instale los servicios de sincronización, puede dejar la sección Configuración opcional desactivada y Azure AD Connect configura todo automáticamente. Establece una instancia de SQL Server 2012 Express LocalDB, crear los grupos correspondientes y asignar permisos. Si desea cambiar los valores predeterminados, puede usar la siguiente tabla para conocer las opciones de configuración opcionales que están disponibles.

![Componentes necesarios](./media/active-directory-aadconnect-get-started-custom/requiredcomponents.png)

Configuración opcional  | Descripción
------------- | -------------
Usar un servidor SQL existente | Le permite especificar el nombre de SQL Server y el nombre de instancia. Elija esta opción si ya tiene un servidor de base de datos que desea usar. Escriba el nombre de instancia seguido por un coma y número de puerto en el **Nombre de instancia** si SQL Server no tiene la exploración habilitada.
Usar una cuenta de servicio existente | De forma predeterminada Azure AD Connect crea una cuenta de servicio local para usar los servicios de sincronización. La contraseña es generada automáticamente y desconocidas a la persona que instale Azure AD Connect. Si utiliza un servidor SQL remoto o usar un servidor proxy que requiere autenticación, necesitará un servicio de la cuenta del dominio y conocer la contraseña. En estos casos, especifique la cuenta de servicio para usar. Asegúrese de que el usuario que ejecuta la instalación es una SA en SQL, por lo que se puede crear un inicio de sesión de la cuenta de servicio. Ver [permisos y cuentas de Azure AD Connect](active-directory-aadconnect-accounts-permissions.md#custom-settings-installation)
Especificar grupos de sincronización personalizada | De forma predeterminada Azure AD Connect crea cuatro grupos local en el servidor cuando se instalan los servicios de sincronización. Estos grupos son: grupo de administradores, grupo operadores, grupo examinar y el grupo de restablecer la contraseña. Puede especificar sus propios grupos aquí. Los grupos deben ser locales en el servidor y no se encuentra en el dominio.

### <a name="user-sign-in"></a>Inicio de sesión de usuario
Después de instalar los componentes necesarios, se le pedirá que seleccione el método de inicio de sesión único de usuarios. La tabla siguiente proporciona una breve descripción de las opciones disponibles. Para obtener una descripción completa de los métodos de inicio de sesión, vea [Inicio de sesión de usuario](../active-directory-aadconnect-user-signin.md).

![Inicio de sesión de usuario en](./media/active-directory-aadconnect-get-started-custom/usersignin.png)

Opción de inicio de sesión único | Descripción
------------- | -------------
Sincronización de la contraseña | Los usuarios tienen que iniciar sesión en servicios de nube de Microsoft, como Office 365, con la misma contraseña que utilizan en su red local. Las contraseñas de los usuarios se sincronizan con Azure AD como un valor de hash de contraseña y la autenticación se produce en la nube. Para obtener más información, consulte [sincronización de contraseñas](../active-directory-aadconnectsync-implement-password-synchronization.md) .
Federación con AD FS | Los usuarios tienen que iniciar sesión en servicios de nube de Microsoft, como Office 365, con la misma contraseña que utilizan en su red local.  Los usuarios se redirigen a sus local AD FS instancia para iniciar sesión y produce la autenticación local.
No configurar | Ninguna característica está instalada y configurada. Elija esta opción si ya tiene un servidor de federación de fiesta 3ª u otra solución existente en su lugar.

### <a name="connect-to-azure-ad"></a>Conectarse a Azure AD
En conectarse a la pantalla de Azure AD, escriba un nombre y una contraseña de cuenta de administrador global. Si ha seleccionado **la federación con AD FS** en la página anterior, no inicie sesión con una cuenta en un dominio que se va a habilitar para la federación. Una recomendación es usar una cuenta en el dominio de **onmicrosoft.com** predeterminado, que viene con el directorio de Azure AD.

Esta cuenta solo se utiliza para crear una cuenta de servicio en Azure AD y no se usa cuando el asistente haya finalizado.  
![Inicio de sesión de usuario en](./media/active-directory-aadconnect-get-started-custom/connectaad.png)

Si su cuenta de administrador global tiene AMF habilitado, debe proporcionar la contraseña en el menú emergente inicio de sesión y complete el desafío AMF. El desafío puede proporcionar un código de verificación o una llamada de teléfono.  
![Inicio de sesión de usuario en AMF](./media/active-directory-aadconnect-get-started-custom/connectaadmfa.png)

La cuenta de administrador global también puede tener [La administración de identidades con privilegios](../active-directory-privileged-identity-management-getting-started.md) habilitado.

Si recibe un error y tiene problemas con la conectividad, consulte [solucionar problemas de conectividad](../active-directory-aadconnect-troubleshoot-connectivity.md).

## <a name="pages-under-the-section-sync"></a>Páginas en la sección sincronización

### <a name="connect-your-directories"></a>Conectar los directorios
Para conectarse a su servicio de dominio de Active Directory, Azure AD Connect necesita las credenciales de una cuenta con los permisos necesarios. Puede especificar la parte del dominio en el formato de NetBios o FQDN, es decir, FABRIKAM\syncuser o fabrikam.com\syncuser. Esta cuenta puede ser una cuenta de usuario habitual porque sólo tiene los permisos de lectura de forma predeterminada. Sin embargo, dependiendo de su situación, puede que tenga más permisos. Para obtener más información, vea [cuentas de conexión de Azure AD y permisos](../active-directory-aadconnect-accounts-permissions.md#create-the-ad-ds-account)

![Conectar directorio](./media/active-directory-aadconnect-get-started-custom/connectdir.png)

### <a name="azure-ad-sign-in-configuration"></a>Configuración de inicio de sesión de anuncio Azure
Esta página le permite revisar los dominios UPN en local AD DS y que se haya verificado en Azure AD. Esta página también le permite configurar el atributo userPrincipalName utilicen.

![Dominios no verificadas](./media/active-directory-aadconnect-get-started-custom/aadsigninconfig.png)  
Revise cada dominio marcado **No agrega** y **No verificado**. Asegúrese de que se haya verificado esos dominios que usar en Azure AD. Cuando haya comprobado sus dominios, haga clic en el símbolo de actualización. Para obtener más información, vea [Agregar y comprobar el dominio](../active-directory-add-domain.md)

**UserPrincipalName** - atributo userPrincipalName es el atributo uso cuando inicie sesión en Azure AD y Office 365. Los dominios que se utiliza, también conocido como-sufijo UPN, deben comprobarse en Azure AD antes de que los usuarios están sincronizados. Microsoft recomienda mantener predeterminado atributo userPrincipalName. Si este atributo es no enrutables y no se puede comprobar, es posible seleccionar otro atributo. Por ejemplo, puede seleccionar correo electrónico como el atributo contiene el identificador de inicio de sesión. Usar otro atributo que userPrincipalName se conoce como **Identificador alternativo**. El valor del atributo ID alternativo debe seguir el estándar RFC822. Un identificador alternativo puede usarse con la sincronización de la contraseña y federación.

>[AZURE.WARNING]
Con un identificador alternativo no es compatible con todas las cargas de trabajo de Office 365. Para obtener más información, consulte [Configurar ID de inicio de sesión alternativo](https://technet.microsoft.com/library/dn659436.aspx).

### <a name="domain-and-ou-filtering"></a>Filtrado de OU y dominio
De forma predeterminada se sincronizan todos los dominios y unidades organizativas. Si hay algunos dominios o unidades organizativas que no desea sincronizar con Azure AD, puede anular la selección de estos dominios y unidades organizativas.  
![Filtrado de DomainOU](./media/active-directory-aadconnect-get-started-custom/domainoufiltering.png) esta página del asistente está configurando el filtrado basado en el dominio. Para obtener más información, consulte [filtrado basado en el dominio](../active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering).

También es posible que algunos dominios no son accesibles debido a las restricciones del firewall. Estos dominios desmarcados de forma predeterminada y tienen una advertencia.  
![Dominios inaccesibles](./media/active-directory-aadconnect-get-started-custom/unreachable.png)  
Si ve esta advertencia, asegúrese de que estos dominios son en realidad inaccesible y se espera la advertencia.

### <a name="uniquely-identifying-your-users"></a>Identifica los usuarios
La coincidencia a través de la característica de bosques le permite definir cómo se representan los usuarios de su bosques de AD DS en Azure AD. Un usuario o bien puede estar representado una sola vez de todos los bosques o una combinación de cuentas habilitadas y deshabilitadas. El usuario también puede estar representado como un contacto en algunos bosques.

![Único](./media/active-directory-aadconnect-get-started-custom/unique.png)

Configuración | Descripción
------------- | -------------
[Los usuarios solo se representan una vez de todos los bosques](../active-directory-aadconnect-topologies.md#multiple-forests-separate-topologies) | Todos los usuarios se crean como objetos individuales en Azure AD. No se unen los objetos en el metaverso.
[Atributo de correo](../active-directory-aadconnect-topologies.md#multiple-forests-full-mesh-with-optional-galsync) | Esta opción une los usuarios y los contactos si el atributo de correo tiene el mismo valor en bosques diferentes. Use esta opción cuando los contactos se han creado con GALSync.
[ObjectSID y msExchangeMasterAccountSID / msRTCSIP-OriginatorSid](../active-directory-aadconnect-topologies.md#multiple-forests-account-resource-forest) | Esta opción unan a un usuario habilitado en un bosque de cuentas con un usuario deshabilitado en un bosque de recursos. En Exchange, esta configuración se conoce como un buzón vinculado. También se puede usar esta opción si solo utiliza Lync y Exchange no está presente en el bosque de recursos.
sAMAccountName y MailNickName | Esta opción se unan atributos esperamos donde que se encuentra el identificador de inicio de sesión para el usuario.
Un atributo específico | Esta opción permite seleccionar su propio atributo. **Limitación:** Asegúrese de elegir un atributo que ya se encuentra en el metaverso. Si elige un atributo personalizado (no en el metaverso), no podrá completar el asistente.

**Delimitador de origen** - sourceAnchor de atributo es un atributo que es inmutable durante la duración de un objeto de usuario. Es la clave principal, el usuario local con el usuario de vinculación en Azure AD. Puesto que no se puede cambiar el atributo, debe planear un buen atributo que desea usar. Un buen candidato es GUID de objeto. Este atributo no cambia a menos que la cuenta de usuario se mueve entre bosques/dominios. En un entorno de varios bosque donde mover cuentas entre bosques, debe usarse otro atributo, como un atributo con el Id. Evitar atributos que cambia cuando se combina una persona o cambiar las asignaciones. No puede usar atributos con un @-sign, para que no se puede usar el correo electrónico y userPrincipalName. El atributo también distingue mayúsculas de minúsculas así que al mover un objeto entre bosques, asegúrese de mantener la mayúscula o minúscula. Atributos binarios son codificado base 64, pero otros tipos de atributo permanecerán en su estado sin codificar. En escenarios de federación y algunas interfaces de Azure AD, este atributo es también conocida como immutableID. Encontrará más información sobre el delimitador de origen en los [conceptos de diseño](../active-directory-aadconnect-design-concepts.md#sourceAnchor).

### <a name="sync-filtering-based-on-groups"></a>Filtrado de sincronización basado en grupos
El filtrado de la característica de grupos permite sincronizar solo un pequeño subconjunto de los objetos de un programa piloto. Para usar esta característica, cree un grupo para ello en Active Directory local. A continuación, agregar usuarios y grupos a los que se deben sincronizar a Azure AD como miembros directos. Más adelante, puede agregar y quitar usuarios a este grupo para mantener la lista de objetos que se deben presentar en Azure AD. Todos los objetos que desea sincronizar deben ser miembro del grupo directo. Los usuarios, grupos, contactos y equipos y dispositivos deben ser miembros directos. No se resuelve la pertenencia a grupos anidados. Cuando se agrega un grupo al agregar un miembro, solo el propio grupo y no a sus miembros.

![Filtrado de sincronización](./media/active-directory-aadconnect-get-started-custom/filter2.png)

>[AZURE.WARNING]
Esta característica solo está pensada para ser compatible con una implementación piloto. No use en una implementación de producción completas.

En una implementación de producción completas que va a resultar difícil mantener un único grupo con todos los objetos para sincronizar. Debe utilizar uno de los métodos de [filtrado de configurar](../active-directory-aadconnectsync-configure-filtering.md).

### <a name="optional-features"></a>Características opcionales
Esta pantalla le permite seleccionar las características opcionales para sus escenarios específicos.

![Características opcionales](./media/active-directory-aadconnect-get-started-custom/optional.png)

>[AZURE.WARNING]
Si actualmente tiene DirSync o sincronización de AD de Azure active, no Active cualquiera de las características de reescritura de Azure AD Connect.

Características opcionales | Descripción
------------------- | -------------
Implementación híbrida de Exchange | La característica de implementación híbrida de Exchange permite la coexistencia de buzones de Exchange en ambos local y en Office 365. Azure AD Connect está sincronizando un conjunto de [atributos](../active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) de Azure AD específico en el directorio local.
Aplicación de Azure AD y el filtrado de atributo | Al habilitar la aplicación de Azure AD y filtrado de atributo, se puede personalizar el conjunto de atributos sincronizados. Esta opción agrega dos más páginas de configuración para el asistente. Para obtener más información, consulte [filtrado de atributo y aplicación de Azure AD](#azure-ad-app-and-attribute-filtering).
Sincronización de contraseñas | Si ha seleccionado la federación como la solución de inicio de sesión, puede habilitar esta opción. Sincronización de contraseñas, se usa como una opción de copia de seguridad. Para obtener más información, consulte [sincronización de contraseñas](../active-directory-aadconnectsync-implement-password-synchronization.md).
Reescritura de contraseña | Al habilitar la reescritura de contraseña, los cambios de contraseña que proceden de Azure AD se vuelve a escribir el directorio local. Para obtener más información, vea [Introducción a la administración de contraseñas](../active-directory-passwords-getting-started.md).
Reescritura de grupo | Si usa la característica de **Grupos de Office 365** , puede tiene estos grupos representados en Active Directory local. Esta opción solo está disponible si tiene Exchange presentes en Active Directory local. Para obtener más información, vea [reescritura de grupo](../active-directory-aadconnect-feature-preview.md#group-writeback).
Reescritura de dispositivo | Permite a los objetos de dispositivo de reescritura en Azure AD a su Active Directory local para escenarios de acceso condicional. Para obtener más información, vea [Habilitar reescritura de dispositivo en Azure AD Connect](../active-directory-aadconnect-feature-device-writeback.md).
Sincronización de directorio extensión atributo | Al habilitar la sincronización de directorio extensiones atributo, se sincronizan los atributos especificados a Azure AD. Para obtener más información, vea [extensiones de directorio](../active-directory-aadconnectsync-feature-directory-extensions.md).

### <a name="azure-ad-app-and-attribute-filtering"></a>Aplicación de Azure AD y el filtrado de atributo
Si desea limitar qué atributos para sincronizarse con Azure AD, a continuación, inicie seleccionando los servicios que está usando. Si realiza cambios en la configuración de esta página, un nuevo servicio debe estar seleccionada explícitamente volviendo a ejecutar el Asistente para instalación.

![Características opcionales aplicaciones](./media/active-directory-aadconnect-get-started-custom/azureadapps2.png)

En función de los servicios seleccionados en el paso anterior, esta página muestra todos los atributos que se sincronizan. Esta lista es una combinación de todos los tipos de objeto que se está sincronizando. Si hay algunos atributos determinados que tiene que no se sincronizan, puede cancelar la selección de los atributos.

![Atributos de características opcionales](./media/active-directory-aadconnect-get-started-custom/azureadattributes2.png)

>[AZURE.WARNING]
Quitar atributos puede afectar a la funcionalidad. Para obtener mejores prácticas y las recomendaciones, vea [atributos sincronizados](../active-directory-aadconnectsync-attributes-synchronized.md#attributes-to-synchronize).

### <a name="directory-extension-attribute-sync"></a>Sincronización de directorio extensión atributo
Puede extender el esquema de Azure AD con atributos personalizados agregados por otros atributos de Active Directory o de su organización. Para usar esta característica, seleccione **sincronizar de atributo de extensión del directorio** en la página **Características opcionales** . Puede seleccionar más atributos sincronizar en esta página.

![Extensiones de directorio](./media/active-directory-aadconnect-get-started-custom/extension2.png)

Para obtener más información, vea [extensiones de directorio](../active-directory-aadconnectsync-feature-directory-extensions.md).

## <a name="configuring-federation-with-ad-fs"></a>Configurar la federación con AD FS
Configuración de AD FS con Azure AD Connect es sencilla con unos pocos clics. Se necesita lo siguiente antes de la configuración.

- Un servidor de Windows Server 2012 R2 para el servidor de federación con administración remota habilitada
- Un servidor de Windows Server 2012 R2 para el servidor Proxy de la aplicación Web con administración remota habilitada
- Un certificado SSL para el nombre de servicio de federación que desea utilizar (por ejemplo, sts.contoso.com)

### <a name="ad-fs-configuration-pre-requisites"></a>Requisitos previos de AD FS configuración
Para configurar su granja de AD FS con Azure AD Connect, asegúrese de que WinRM está habilitada en los servidores remotos. Además, vaya a través de los requisitos de puertos enumerados en la [tabla 3 - Azure AD Connect y servidores de federación o WAP](../active-directory-aadconnect-ports.md#table-3---azure-ad-connect-and-federation-serverswap).

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>Crear una nueva granja de AD FS o usar un conjunto existente de AD FS
Puede usar un conjunto de AD FS existente o puede crear una nueva granja de AD FS. Si elige crear una nueva, deberá proporcionar el certificado SSL. Si el certificado SSL está protegido mediante contraseña, se pedirá la contraseña.

![Conjunto de servidores de AD FS](./media/active-directory-aadconnect-get-started-custom/adfs1.png)

Si elige usar un conjunto existente de AD FS, pasará directamente a la configuración de la relación de confianza entre la pantalla de AD FS y Azure AD.

### <a name="specify-the-ad-fs-servers"></a>Especificar los servidores de AD FS
Especifique los servidores que desea instalar AD FS en. Puede agregar uno o varios servidores según su necesidades de planificación de la capacidad. Unirse a todos los servidores en Active Directory antes de realizar esta configuración. Microsoft recomienda instalar a un servidor de AD FS solo para las implementaciones de prueba y piloto. A continuación, agregar e implementar más servidores para satisfacer sus necesidades escaladas ejecutando Azure AD Connect tras la configuración inicial.

>[AZURE.NOTE]
Asegúrese de que todos los servidores se unen a un dominio de Active Directory antes de realizar esta configuración.

![Servidores de AD FS](./media/active-directory-aadconnect-get-started-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>Especificar los servidores Proxy de aplicación Web
Especifique los servidores que desee que los servidores proxy de aplicación Web. El servidor de proxy de aplicación web se implementa en su DMZ (extranet orientación) y es compatible con las solicitudes de autenticación desde la extranet. Puede agregar uno o varios servidores según su necesidades de planificación de la capacidad. Microsoft recomienda instalar a un servidor de proxy de aplicación Web solo para las implementaciones de prueba y piloto. A continuación, agregar e implementar más servidores para satisfacer sus necesidades escaladas ejecutando Azure AD Connect tras la configuración inicial. Recomendamos tener un número equivalente de servidores proxy para cumplir con la autenticación de la intranet.

>[AZURE.NOTE]
<li> Si la cuenta que utilice no es un administrador local en los servidores de AD FS, se le pedirá las credenciales de administrador.</li>
<li> Asegúrese de que hay conectividad HTTP/HTTPS entre el servidor de Azure AD Connect y el servidor Proxy de la aplicación Web antes de ejecutar este paso.</li>
<li> Asegúrese de que hay conectividad HTTP/HTTPS entre el servidor de aplicaciones Web y el servidor AD FS para permitir las solicitudes de autenticación fluya a través de.</li>

![Aplicación Web](./media/active-directory-aadconnect-get-started-custom/adfs3.png)

Se le solicita que introduzca credenciales para que el servidor de aplicaciones web puede establecer una conexión segura en el servidor de AD FS. Estas credenciales deben ser un administrador local en el servidor de AD FS.

![Proxy](./media/active-directory-aadconnect-get-started-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>Especificar la cuenta de servicio del servicio de AD FS
El servicio de AD FS requiere una cuenta de servicio de dominio para autenticar usuarios y la información de usuario de búsqueda en Active Directory. Pueden admitir dos tipos de cuentas de servicio:

- **Grupo administra la cuenta de servicio** - presentada en servicios de dominio de Active Directory con Windows Server 2012. Este tipo de cuenta proporciona servicios, como AD FS, una sola cuenta sin necesidad de actualizar la contraseña de la cuenta con regularidad. Use esta opción si ya dispone de los controladores de dominio de Windows Server 2012 en el dominio que pertenecen los servidores de AD FS.
- **Cuenta de usuario** : este tipo de cuenta requiere que proporcione una contraseña y actualizar la contraseña cuando la contraseña cambia o caduca. Use esta opción solo cuando no tiene controladores de dominio de Windows Server 2012 en el dominio que pertenecen los servidores de AD FS.

Si ha seleccionado cuenta de servicio de grupo administrada y nunca se ha usado esta característica en Active Directory, se le pedirá las credenciales de administrador de empresa. Estas credenciales se utilizan para iniciar el almacén de claves y habilitar la característica de Active Directory.

![Cuenta de servicio de AD FS](./media/active-directory-aadconnect-get-started-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-wish-to-federate"></a>Seleccione el dominio de Azure AD que desea federarse
Esta configuración se usa para configurar la relación de federación entre AD FS y Azure AD. Configura AD FS a símbolos de seguridad de problema para Azure AD y Azure AD para confiar en los símbolos de la instancia específica de AD FS. Esta página, sólo puede configurar un único dominio en la instalación inicial. Puede configurar más dominios posterior ejecutando Azure AD Connect de nuevo.

![Dominio de Active Directory de Azure](./media/active-directory-aadconnect-get-started-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>Compruebe el dominio de Azure AD seleccionado para la federación
Cuando seleccione el dominio a ser federado, Azure AD Connect le proporciona la información necesaria y comprobar un dominio sin verificar. Consulte [Agregar y verificar el dominio](../active-directory-add-domain.md) acerca de cómo usar esta información.

![Dominio de Active Directory de Azure](./media/active-directory-aadconnect-get-started-custom/verifyfeddomain.png)

>[AZURE.NOTE]
AD Connect intenta comprobar el dominio durante la fase de configurar. Si sigues configurar sin agregar los registros DNS necesarios, el asistente no es capaz de completar la configuración.

## <a name="configure-and-verify-pages"></a>Configurar y comprobar las páginas
La configuración se realiza en esta página.

>[AZURE.NOTE]
Antes de continuar con la instalación y si ha configurado la federación, asegúrese de ha configurado la [resolución de nombres de los servidores de federación](../active-directory-aadconnect-prerequisites.md#name-resolution-for-federation-servers).

![¿Está listo para configurar](./media/active-directory-aadconnect-get-started-custom/readytoconfigure2.png)

### <a name="staging-mode"></a>Modo de ensayo
Es posible configurar un nuevo servidor de sincronización en paralelo con el modo de ensayo. Solo es compatible para tener un servidor de sincronización de la exportación a un directorio en la nube. Pero si desea mover desde otro servidor, por ejemplo una ejecución de sincronización de directorios, puede habilitar Azure AD Connect en modo de ensayo. Cuando está habilitado, el motor de sincronización importar y sincronizar los datos como normal, pero no exporta nada en Azure AD o AD. La reescritura de sincronización y la contraseña de la contraseña de características están deshabilitadas mientras esté en modo de ensayo.

![Modo de ensayo](./media/active-directory-aadconnect-get-started-custom/stagingmode.png)

Mientras esté en modo de ensayo, es posible realizar los cambios necesarios en el motor de sincronización y revisar lo que se va a exportar. Cuando la configuración está bien, vuelva a ejecutar al Asistente para instalación y desactivar el modo de ensayo. Ahora se exportan los datos a Azure AD desde este servidor. Asegúrese de deshabilitar al otro servidor al mismo tiempo, por lo que solo un servidor conocidas está exportando.

Para obtener más información, vea [modo de ensayo](../active-directory-aadconnectsync-operations.md#staging-mode).

### <a name="verify-your-federation-configuration"></a>Comprobar la configuración de la federación
Azure AD Connect comprueba la configuración de DNS para al hacer clic en el botón comprobar.

![Completar](./media/active-directory-aadconnect-get-started-custom/completed.png)

![Comprobar](./media/active-directory-aadconnect-get-started-custom/adfs7.png)

Además, realice los siguientes pasos de comprobación:

- Validar que puede iniciar sesión desde un explorador desde un equipo combinados de dominio en la intranet: conectar con https://myapps.microsoft.com y compruebe la sesión con su cuenta ha iniciado sesión. La cuenta de administrador de AD DS no está sincronizada y no se pueden usar para la verificación.
- Validar que puede iniciar sesión desde un dispositivo desde la extranet. En el equipo doméstico o un dispositivo móvil, conectarse a https://myapps.microsoft.com y proporcione sus credenciales.
- Validar inicio de sesión de cliente enriquecido. Conectarse a https://testconnectivity.microsoft.com, elija la pestaña **Office 365** y elija la **Office 365 solo inicio de sesión de prueba**.

## <a name="next-steps"></a>Pasos siguientes
Cuando haya finalizado la instalación, cerrar sesión y volver a iniciar sesión en Windows antes de utilizar el administrador del servicio de sincronización o Editor de reglas de sincronización.

Ahora que ya tiene instalado de Azure AD Connect puede [comprobar la instalación y asignar licencias](../active-directory-aadconnect-whats-next.md).

Obtenga más información sobre estas funciones, que se han habilitado con la instalación: [accidental evitar eliminaciones](../active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) y [Estado de conexión de Azure AD](../active-directory-aadconnect-health-sync.md).

Obtenga más información sobre estos temas comunes: [programador y cómo desencadenar una sincronización](../active-directory-aadconnectsync-feature-scheduler.md).

Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](../active-directory-aadconnect.md).

## <a name="related-documentation"></a>Documentación relacionada

Tema |  
--------- | ---------
Introducción a Azure AD Connect | [Integrar las identidades locales con Azure Active Directory](../active-directory-aadconnect.md)
Instalar usando la configuración de Express | [Instalación Express de Azure AD Connect](active-directory-aadconnect-get-started-express.md)
Actualización de sincronización de directorios | [Actualizar desde la herramienta de sincronización de Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)
Cuentas utilizadas para la instalación | [Más información sobre permisos y cuentas de Azure AD Connect](active-directory-aadconnect-accounts-permissions.md)
