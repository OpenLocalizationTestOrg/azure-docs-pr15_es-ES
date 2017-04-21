<properties 
    pageTitle="Prácticas recomendadas de seguridad para usar AMF de Azure"
    description="Este documento proporciona las prácticas recomendadas de uso de Azure AMF con cuentas de Azure"
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
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="security-best-practices-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Prácticas recomendadas de seguridad para usar autenticación multifactor de Azure con cuentas de Azure AD

Cuando se trata de mejorar el proceso de autenticación, autenticación multifactor es la opción preferida para la mayoría de las organizaciones. Autenticación de Azure multifactor (AMF) permite a las empresas satisfacer sus requisitos de seguridad y cumplimiento al tiempo que ofrece una experiencia de inicio de sesión simple para sus usuarios. En este artículo cubrirá algunas prácticas recomendadas que debe tener en cuenta al planear la adopción de Azure AMF.

## <a name="best-practices-for-azure-multi-factor-authentication-in-the-cloud"></a>Procedimientos recomendados para la autenticación multifactor de Azure en la nube
Para proporcionar a todos los usuarios con autenticación multifactor y aprovechar las ventajas de las características extendidas que ofrece autenticación multifactor de Azure, debe habilitar la autenticación multifactor de Azure en todos los usuarios.  Esto se logra mediante uno de estos procedimientos:

- Premium de Azure AD o el conjunto de instrucciones de movilidad de empresa
- Proveedor de autenticación multifactor

### <a name="azure-ad-premiumenterprise-mobility-suite"></a>Serie de movilidad de Azure AD Premium/empresarial

![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

El primer paso recomendado para adoptar AMF de Azure en la nube con Azure AD Premium o la serie de movilidad empresarial es asignar licencias a los usuarios.  Azure autenticación multifactor forma parte de los conjuntos de aplicaciones y así su organización no necesita nada adicional para ampliar la capacidad de autenticación multifactor a todos los usuarios.

Configurar la autenticación multifactor cuando considere siguientes:

- No es necesario crear un proveedor de autenticación multifactor.  Premium de Azure AD y el conjunto de movilidad de empresa se suministra con autenticación multifactor de Azure.  Si crea un proveedor de servicios de autenticación que podría obtener doble facturado.
- Azure AD Connect es solo un requisito si está sincronizando su entorno local de Active Directory con un directorio de Azure AD. Si solo utiliza un directorio de Azure AD no está sincronizado con una instancia local de Active Directory, no es necesario Azure AD Connect.


### <a name="multi-factor-auth-provider"></a>Proveedor de autenticación multifactor

![Proveedor de autenticación multifactor](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Si no tiene Azure AD Premium o la serie de movilidad empresarial el primer paso recomendado para adoptar AMF de Azure en la nube es crear un proveedor de Auth AMF. Aunque AMF está disponible para los administradores globales que tienen Azure Active Directory, cuando se implementa AMF para su organización que necesita para ampliar la capacidad de autenticación multifactor a todos los usuarios y hacer que necesita un proveedor de autenticación multifactor de forma predeterminada.
Al seleccionar el proveedor de servicios de autenticación, debe seleccionar un directorio y considere lo siguiente:

- No es necesario un directorio de Azure AD para crear un proveedor de autenticación multifactor.
- Debe asociar el proveedor de servicios de autenticación multifactor con un directorio de Azure AD si desea ampliar la autenticación multifactor a todos los usuarios o los administradores globales para poder aprovechar las características, como el portal de administración, saludos personalizados e informes.
- Sincronización de directorios o AAD Sync son solo un requisito si está sincronizando su entorno local de Active Directory con un directorio de Azure AD. Si solo utiliza un directorio de Azure AD no está sincronizado con una instancia local de Active Directory, no es necesario DirSync o AAD Sync.
- Si dispone de Azure AD Premium o la serie de movilidad empresarial, no es necesario crear un proveedor de autenticación multifactor. Solo debe asignar una licencia de un usuario y, a continuación, puede empezar a activar AMF para los usuarios.
- Asegúrese de elegir el modelo de uso adecuado para su empresa (por auth o usuario habilitado), una vez seleccionado el modelo de uso no se puede cambiar.

### <a name="user-account"></a>Cuenta de usuario
Al habilitar AMF para sus usuarios, cuentas de usuario pueden estar en uno de tres estados principales: deshabilitado, habilitado o forzada.
Utilice las instrucciones siguientes para asegurarse de que está utilizando la opción más adecuada para su implementación:

- Cuando el estado del usuario se establece en deshabilitado, ese usuario no está utilizando la autenticación multifactor. Este es el estado predeterminado.
- Cuando el estado del usuario se establece en habilitado, significa que el usuario está habilitado, pero no ha finalizado el proceso de registro. Se le pedirá que completar el proceso de inicio de sesión en la siguiente. Esta configuración no afecta a las aplicaciones de explorador no. Todas las aplicaciones seguirán funcionando hasta que se complete el proceso de registro.
- Cuando el estado del usuario es obligatoria, significa que el usuario puede o no haya completado el registro. Si se han completado el proceso de registro que están usando autenticación multifactor. En caso contrario, se le indicará al usuario para completar el proceso de registro en el inicio de sesión siguiente. Esta configuración afecta a aplicaciones del explorador no. Estas aplicaciones no funcionará hasta que se crean y se utilizan contraseñas de aplicaciones.
- Usar la plantilla de notificación de usuario disponible en el artículo [Introducción a la autenticación multifactor de Azure en la nube](multi-factor-authentication-get-started-cloud.md) para enviar un correo electrónico a los usuarios sobre la adopción de MFA.

### <a name="supportability"></a>Compatibilidad

Puesto que la mayoría de los usuarios está acostumbrado a usar solo contraseñas para autenticar, es importante que su compañía Traer concienciación a todos los usuarios sobre este proceso. Este aviso de charla puede reducir la probabilidad de que los usuarios llamará a su departamento de soporte técnico para secundarias problemas relacionados con AMF.
Sin embargo, hay algunos escenarios donde es necesario deshabilitar temporalmente AMF. Utilice las instrucciones siguientes para comprender cómo manejar los escenarios:

- Asegúrese de que el personal de soporte técnico está capacitado para escenarios de controlador donde la aplicación móvil o el teléfono no recibe una notificación o una llamada de teléfono y por este motivo que es no se puede iniciar sesión en el usuario. Puede habilitar una opción de derivación puntuales para permitir que un usuario autenticar una sola vez "omitiendo" autenticación multifactor. La derivación es temporal y caduca después del número especificado de segundos.
- Si es necesario, puede aprovechar la capacidad de IP fiables en Azure AMF. Esta característica permite a los administradores de un inquilino administrado o federado la capacidad omitir la autenticación multifactor para los usuarios que está iniciando sesión de intranet local de la empresa. Las características están disponibles para los inquilinos de Azure AD que tengan licencias de Azure AD Premium, Enterprise movilidad Suite o autenticación multifactor de Azure.


## <a name="best-practices-for-azure-multi-factor-authentication-on-premises"></a>Procedimientos recomendados para la autenticación multifactor Azure local
Si su organización ha decidido aprovechar su propia infraestructura para habilitar AMF, será necesario implementar un servidor de autenticación multifactor Azure local. Los componentes de servidor AMF se muestran en el diagrama siguiente:

![Proveedor de autenticación multifactor](./media/multi-factor-authentication-security-best-practices/server.png)
*no se instala de forma predeterminada ** instalado pero no está habilitada de forma predeterminada


Servidor de autenticación multifactor Azure puede utilizarse para proteger recursos de la nube y local que tiene acceso a cuentas de Azure AD.  Sin embargo esto sólo puede conseguir usando federación.  Es decir, debe tener AD FS y que se federa con el inquilino de Azure AD.
Configurar el servidor de autenticación multifactor tener en cuenta al siguiente:

- Si es proteger recursos de Azure AD mediante servicios de federación de Active Directory, a continuación, se realiza la 1ª factor de autenticación local con AD FS y el factor de 2 º es local realizadas por teniendo en cuenta la notificación.
- No es necesario que el servidor de autenticación multifactor de Azure esté instalado en su servidor de federación de AD FS a pesar de que el adaptador de autenticación multifactor de AD FS debe estar instalado en un Windows Server 2012 R2 ejecutando AD FS. Puede instalar al servidor en un equipo distinto, como es una versión compatible e instalar al adaptador de AD FS por separado en su servidor de federación de AD FS. Vea el procedimiento siguiente para obtener instrucciones sobre cómo instalar al adaptador por separado.
- El Asistente para instalación de autenticación multifactor AD FS adaptador crea un grupo de seguridad denominado PhoneFactor Admins en Active Directory y, a continuación, agrega la cuenta de servicio de AD FS de los servicios de federación a este grupo. Le recomendamos que compruebe en el controlador de dominio que ya se ha creado el grupo PhoneFactor Admins y que cuenta de servicio de AD FS es un miembro de este grupo. Si es necesario, agregue la cuenta de servicio de AD FS manualmente al grupo PhoneFactor Admins en el controlador de dominio.

### <a name="user-portal"></a>Portal de usuario
Este portal se ejecuta en un sitio web de Internet Information Server (IIS), que permite capacidades de autoservicio y proporciona un conjunto completo de capacidades de administración de usuario. Utilice las instrucciones siguientes para configurar este componente:

- Se requiere IIS 6 o superior
- ASP.NET v2.0.507207 debe estar instalado y registrado
- Este servidor se puede implementar en una red perimetral.



### <a name="app-passwords"></a>Contraseñas de aplicaciones
Si su organización se federa con SSO Azure AD y va a usar AMF Azure, a continuación, tenga en cuenta de los siguientes al usar contraseñas de la aplicación (Recuerde que esto solo se aplica a federado (SSO) se utiliza):

- La contraseña de aplicación verificar Azure AD y, por tanto, evita la federación. Federación solo se usa cuando configure la contraseña de aplicación.
- Para federado (SSO) las contraseñas de los usuarios se almacenarán en el identificador de la organización. Si el usuario deja la empresa, tiene esa información de flujo de identificador de organización con DirSync en tiempo real. Deshabilitar o eliminar la cuenta puede tardar hasta 3 horas para sincronizar, retrasar deshabilitar o eliminación de contraseña de aplicación en Azure AD.
- No se respeta la configuración de Control de acceso de cliente local mediante una contraseña de aplicación
- Autenticación local registro / capacidad de auditoría no está disponible para la contraseña de aplicación
- Más formación del usuario final se requiere para el cliente de Microsoft Lync 2013.
- Algunos diseños de arquitectura avanzados pueden requerir mediante una combinación de organización nombre de usuario y contraseñas y contraseñas de aplicaciones cuando se utiliza la autenticación multifactor con clientes, en función de dónde se autentican. Para los clientes que autenticarse una infraestructura local, debe usar un usuario profesional y la contraseña. Para los clientes que autenticarse Azure AD, debería usar la contraseña de aplicación.
- De forma predeterminada, los usuarios no pueden crear contraseñas de aplicaciones, si su compañía requiere o si desea permitir a los usuarios crear contraseña de aplicación en algunos casos, asegúrese de que está seleccionada la opción Permitir a los usuarios crear contraseñas de aplicaciones para iniciar sesión en las aplicaciones del explorador no.

## <a name="additional-considerations"></a>Consideraciones adicionales
Use la siguiente lista para conocer algunas consideraciones adicionales y los procedimientos recomendados para cada componente que se implementan local:

Método|Descripción
:------------- | :------------- |
[Servicios de federación de Active Directory](multi-factor-authentication-get-started-adfs.md)|Información sobre cómo configurar la autenticación multifactor de Azure con AD FS.
[Autenticación RADIUS](multi-factor-authentication-get-started-server-radius.md)|  Información sobre la instalación y configuración del servidor de AMF de Azure con RADIUS.
[Autenticación de IIS](multi-factor-authentication-get-started-server-iis.md)|Información sobre la instalación y configuración del servidor de AMF de Azure con IIS.
[Autenticación de Windows](multi-factor-authentication-get-started-server-windows.md)|  Información sobre la instalación y configuración del servidor de AMF de Azure con autenticación de Windows.
[Autenticación LDAP](multi-factor-authentication-get-started-server-ldap.md)|Información sobre la instalación y configuración del servidor de AMF de Azure con autenticación LDAP.
[Remoto puerta de enlace de escritorio y servidor de autenticación multifactor de Azure con RADIUS](multi-factor-authentication-get-started-server-rdg.md)|  Información sobre la instalación y configuración del servidor de AMF de Azure con la puerta de enlace de escritorio remoto con RADIUS.
[Sincronización con Active Directory de Windows Server](multi-factor-authentication-get-started-server-dirint.md)|Información sobre la instalación y configuración de sincronización entre Active Directory y el servidor de Azure AMF.
[Implementar el servicio Web aplicación móvil de servidor de Azure autenticación multifactor](multi-factor-authentication-get-started-server-webservice.md)|Información sobre la instalación y configuración del servicio web de servidor de Azure AMF.
[Configuración de la VPN avanzadas con Azure autenticación multifactor](multi-factor-authentication-advanced-vpn-configurations.md)|Información sobre cómo configurar dispositivos Cisco ASA, Citrix Netscaler y Juniper o Pulse Secure VPN mediante LDAP o RADIUS.


## <a name="additional-resources"></a>Recursos adicionales
Mientras en este artículo, se resalta algunas prácticas recomendadas para Azure AMF, existen otros recursos que también puede usar al planear la implementación de MFA. La siguiente lista tiene algunos artículos claves que pueden ayudarle durante este proceso:

- [Informes en Azure autenticación multifactor](multi-factor-authentication-manage-reports.md)
- [Experiencia de instalación para la autenticación multifactor de Azure](multi-factor-authentication-end-user-first-time.md)
- [Preguntas más frecuentes de Azure autenticación multifactor](multi-factor-authentication-faq.md)
