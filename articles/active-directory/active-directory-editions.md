<properties
    pageTitle="Ediciones de Azure Active Directory | Microsoft Azure"
    description="Un tema que explica las opciones de forma gratuita y pagada ediciones de Azure Active Directory. Azure Active Directory básicos, Azure Active Directory Premium P1 y Azure Active Directory Premium P2 son las ediciones de pagadas."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/09/2016"
    ms.author="curtand"/>

# <a name="azure-active-directory-editions"></a>Ediciones de Azure Active Directory

Todos los servicios de empresa de Microsoft Online dependen de Azure Active Directory (AD Azure) para iniciar sesión y necesita otra identidad. Si se suscribe a cualquiera de Microsoft Online services de empresa (por ejemplo, Office 365 o Microsoft Azure), obtendrá Azure AD con acceso a todas las características gratuitas, que se describen a continuación.  

Azure Active Directory es un servicio que proporciona completas capacidades de administración de identidades y acceso en la nube para los empleados, asociados y clientes. Combina los servicios de directorio, avanzada gobernanza de identidad, una plataforma basada en estándares para programadores y la administración de aplicaciones de access para su propio o cualquiera de las miles de aplicaciones integradas previamente. Con la edición de Azure Active Directory gratuita, puede administrar usuarios y grupos, sincronizar con directorios local, obtenga el inicio de sesión único a través Azure, Office 365 y miles de aplicaciones de SaaS populares como Salesforce, día laborable, Concur, DocuSign, Google Apps, cuadro, ServiceNow, Dropbox y mucho más. Para obtener más información acerca de Azure Active Directory, lea [¿Qué es Azure AD](active-directory-whatis.md).

Para mejorar su Azure Active Directory, puede agregar capacidades de pagadas con las ediciones de Azure Active Directory básicas, Premium P1 y P2 Premium. Azure Active Directory pagado ediciones se basan en el directorio gratuito existente, lo que proporciona enterprise capacidades de clase que ocupan mejorado, autoservicio supervisión, informes de seguridad, autenticación multifactor (AMF) y acceso seguro para su plantilla móvil.

Suscripciones a Office 365 incluyen características adicionales de Azure Active Directory se describe en la siguiente tabla de comparación.


> [AZURE.NOTE] Para ver las opciones de precios de estas ediciones, consulte [Precios de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Azure Active Directory Premium P1, P2 Premium y Azure Active Directory básica no se admiten actualmente en China. Póngase en contacto con nosotros en el foro de Azure Active Directory para obtener más información.


- **Azure Active Directory básicas** : diseñado para los trabajadores de la tarea con las necesidades de nube en primer lugar, esta edición proporciona nube soluciones de administración de identidades de acceso y autoservicio centrado en la aplicación. Con la edición básica de Azure Active Directory, obtener mejorar la productividad y características de reducción de costo como administración de acceso basado en grupo, autoservicio restablecimiento de contraseña para que las aplicaciones de la nube y Azure Active Directory Proxy de aplicación (publicar aplicaciones web de locales con Azure Active Directory), todo copia por un SLA de nivel empresarial de actividad del 99,9 por ciento.

- **Azure Active Directory Premium P1** - diseñado para permiten a las organizaciones con más solicitan necesidades de administración de identidades y acceso, Azure Active Directory Premium edition agrega las capacidades de administración de identidades de nivel empresarial muy y permite a los usuarios híbrido perfecta acceso local y las capacidades de la nube. Esta edición incluye todo lo que necesita para trabajadores de la información y administradores de identidad en entornos híbridos a través de acceso a la aplicación, autoservicio acceso de administración de identidades y (IAM), protección de identidad y seguridad en la nube. Es compatible con los recursos de administración y delegación avanzados como grupos dinámicos y administración de grupos de autoservicio. Incluye Administrador de identidad de Microsoft (un conjunto de administración de identidades y acceso local) y ofrece capacidades de reescritura habilitar soluciones como autoservicio restablecimiento de contraseña para los usuarios locales de nube.

- **Azure Active Directory Premium P2** - diseñado con protección avanzada para todos los usuarios y administradores, esta nueva oferta incluye todas las funciones de Azure AD Premium P1, así como nuestra nueva protección de identidad y administración de identidades con privilegios. Azure Active Directory identidad Protection aprovecha millones de señales para proporcionar acceso condicional basado en riesgo a sus aplicaciones y datos críticos de la empresa. Nos también le ayudarán a administra y proteger cuentas con privilegios con Azure Active Directory con privilegios la administración de identidades, por lo que puede detectar, restringir y supervisar los administradores y su acceso a los recursos y proporcionar acceso de en el tiempo cuando sea necesario.  

Para iniciar sesión y empezar a usar Premium de Active Directory en la actualidad, consulte [Introducción a Azure Active Directory Premium](active-directory-get-started-premium.md).


> [AZURE.NOTE]
>Un número de capacidades de Azure Active Directory está disponible en las ediciones "pago de inmediato":
>
>- B2C de Active Directory es la solución de administración de identidades y acceso para las aplicaciones de consumidor orientación. Para obtener más detalles, consulte [B2C de Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory-b2c/)

>-  La autenticación multifactor Azure puede usarse a través de por usuario o por proveedores de autenticación. Para obtener más detalles, consulte [¿Qué es la autenticación multifactor de Azure?](../multi-factor-authentication/multi-factor-authentication.md)


##<a name="comparing-generally-available-features"></a>Comparación de características generalmente disponibles

> [AZURE.NOTE] Para obtener una vista distinta de estos datos, vea las [Capacidades de Azure Active Directory](https://www.microsoft.com/en/server-cloud/products/azure-active-directory/features.aspx).




**Características comunes**

- [Objetos de directorio](#directory-objects)

- [Administración de usuario o grupo (agregar, actualizar o eliminar) / basada en el usuario de aprovisionamiento, registro de dispositivo](#usergroup-management-addupdatedelete-user-based-provisioning-device-registration)

- [Inicio de sesión único (SSO)](#single-sign-on-sso)

- [Autoservicio de cambio de contraseña para los usuarios de la nube](#self-service-password-change-for-cloud-users)

- [Conectar (motor de sincronización que extiende directorios locales con Azure Active Directory)](#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory)

- [Seguridad y los informes de uso](#securityusage-reports)



**Características básicas**

- [Administración de acceso basado en grupo / aprovisionamiento](#group-based-access-managementprovisioning)

- [Autoservicio de restablecimiento de contraseña para los usuarios de la nube](#self-service-password-reset-for-cloud-users)

- [Personalización de marca de empresa (personalización del Panel de acceso o páginas de inicio de sesión)](#company-branding-logon-pagesaccess-panel-customization)

- [Proxy de aplicación](#application-proxy)

- [SLA 99,9%](#sla-999)


**Características de P1 Premium**

- [Grupo de autoservicio y aplicación administración o servicio automáticamente Agregar aplicaciones o grupos dinámicos](#self-service-group-and-app-managementself-service-application-additions-dynamic-groups)

- [Reescritura de autoservicio contraseña restablecer o cambiar o desbloquear con local](#self-service-password-resetchangeunlock-with-on-premises-write-back)

- [Con autenticación multifactor (nube y local (servidor AMF))](#multi-factor-authentication-cloud-and-on-premises-mfa-server)

- [CAL MIM + MIM Server](#mim-cal-mim-server)

- [Detección de aplicación de nube](#cloud-app-discovery)

- [Estado de conexión](#connect-health)

- [Renovación automática de la contraseña para las cuentas de grupo](#automatic-password-rollover-for-group-accounts)

**Características de P2 Premium**

- [Protección de identidad](active-directory-identityprotection.md)

- [Administración de identidades con privilegios](active-directory-privileged-identity-management-configure.md)

**Azure Active Directory unirse a – Windows 10 solo relacionadas con las características**

- [Unirse a un dispositivo Passport de Azure AD, SSO de escritorio, Microsoft Azure AD, recuperación de Bitlocker de administrador](#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery)

- [Administradores locales adicionales para Windows 10 dispositivos a través de Azure AD unirse MDM automática inscripción, recuperación de autoservicio Bitlocker,](#mdm-auto-enrolment-self-service-bitlocker-recovery-additional-local-administrators-to-windows-10-devices-via-azure-ad-join)


## <a name="common-features"></a>Características comunes
#### <a name="directory-objects"></a>Objetos de directorio

**Tipo:** Características comunes

La cuota de uso predeterminado es 150.000 objetos. Un objeto es una entrada en el servicio de directorio, representado por su nombre distintivo único. Un ejemplo de un objeto es una entrada de usuario que se utiliza para la autenticación. Si necesita supere la cuota de forma predeterminada, póngase en contacto con el soporte técnico. No se aplica el límite de objetos de 500K para Office 365, Microsoft Intune o cualquier otro Microsoft pagado el servicio en línea que se basa en Azure Active Directory para servicios de directorio.


**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
| Hasta 500 000 objetos| Sin límite de objeto| Sin límite de objeto| Sin límite de objeto para cuentas de usuario de Office 365|



#### <a name="usergroup-management-addupdatedelete-user-based-provisioning-device--registration"></a>Administración de usuario o grupo (agregar, actualizar o eliminar) / basada en el usuario de aprovisionamiento, registro de dispositivo

**Tipo:** Características comunes

**Disponibilidad:**


| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
| ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]|

**Más detalles:**

- [Administrar el directorio de Azure AD](active-directory-administer.md)
- [Información general de registro de Active Directory dispositivo Azure](active-directory-conditional-access-device-registration-overview.md)




#### <a name="single-sign-on-sso"></a>Inicio de sesión único (SSO)

**Tipo:** Características comunes


**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
| 10 aplicaciones por usuario (1) | 10 aplicaciones por usuario (1) | Sin límite (2) | 10 aplicaciones por usuario (1)|

1. Con libre de AD Azure y Azure AD básica, los usuarios finales que se han asignado acceso a aplicaciones de SaaS, puede ver hasta 10 aplicaciones en su Panel de acceso y el SSO de obtener acceso a ellos. Los administradores pueden configurar SSO y asignar acceso de usuario a las aplicaciones de SaaS como deseen con Free y Basic sin embargo los usuarios finales sólo verán 10 aplicaciones en su Panel de acceso a la vez.

2. Integración de autoservicio de cualquier aplicación compatible con SAML, SCIM o autenticación basada en formularios con las plantillas disponibles en el menú de la Galería de aplicación. Para obtener más detalles, consulte [configuración de inicio de sesión único en aplicaciones que no están en la Galería de la aplicación de Azure Active Directory](active-directory-saas-custom-apps.md).

**Más detalles:**

- [Administrar aplicaciones de Azure Active Directory (AD)](active-directory-enable-sso-scenario.md)



#### <a name="self-service-password-change-for-cloud-users"></a>Autoservicio de cambio de contraseña para los usuarios de la nube

**Tipo:** Características comunes

**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
| ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]|

**Más detalles:**

- [Cómo actualizar su propia contraseña](active-directory-passwords-update-your-own-password.md)




#### <a name="connect--sync-engine-that-extends-on-premises-directories-to-azure-active-directory"></a>Conectar (motor de sincronización que extiende directorios locales con Azure Active Directory)

**Tipo:** Características comunes


**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
| ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]|

**Más detalles:**

- [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)



#### <a name="securityusage-reports"></a>Informes de uso o de seguridad

**Tipo:** Características comunes


**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
| 3 informes básicos| 3 informes básicos| Informes avanzados| 3 informes básicos|

**Más detalles:**

- [Ver los informes de access y el uso](active-directory-view-access-usage-reports.md)




## <a name="premium-and-basic-features"></a>Premium y características básicas
#### <a name="group-based-access-managementprovisioning"></a>Administración y aprovisionamiento de acceso basado en grupo

**Tipo:** Características básicas


**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
|  | ![Comprobar][12]| ![Comprobar][12]|  |

**Más detalles:**

- [Uso de un grupo para administrar el acceso a las aplicaciones de SaaS](active-directory-accessmanagement-group-saasapps.md)



#### <a name="self-service-password-reset-for-cloud-users"></a>Autoservicio de restablecimiento de contraseña para los usuarios de la nube

**Tipo:** Características básicas


**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
|  | ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]|

**Más detalles:**

- [Restablecer contraseña de Azure AD para los usuarios y administradores.](active-directory-passwords.md)



#### <a name="company-branding-logon-pagesaccess-panel-customization"></a>Personalización de marca de empresa (personalización del Panel de acceso o páginas de inicio de sesión)

**Tipo:** Características básicas


**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
|  | ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]|

**Más detalles:**

- [Agregar la marca para iniciar sesión y Panel de acceso a las páginas de la empresa](active-directory-add-company-branding.md)



#### <a name="application-proxy"></a>Proxy de aplicación

**Tipo:** Características básicas


**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
|  | ![Comprobar][12]| ![Comprobar][12]|  |

**Más detalles:**

- [Cómo proporcionar acceso remoto seguro a aplicaciones locales](active-directory-application-proxy-get-started.md)



#### <a name="sla-999"></a>SLA 99,9%

**Tipo:** Características básicas


**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
|  | ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]|

**Más detalles:**

- [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/)




## <a name="premium-features"></a>Características Premium
#### <a name="self-service-group-and-app-managementself-service-application-additionsdynamic-groups"></a>Grupo de autoservicio y aplicación de administración o servicio automáticamente aplicación adiciones dinámicos/grupos

**Tipo:** Características Premium


**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
|  |  | ![Comprobar][12]|  |





#### <a name="self-service-password-resetchangeunlock-with-on-premises-write-back"></a>Reescritura de autoservicio contraseña restablecer o cambiar o desbloquear con local

**Tipo:** Características Premium


**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
|  |  | ![Comprobar][12]|  |





#### <a name="multi-factor-authentication-cloud-and-on-premises-mfa-server"></a>Con autenticación multifactor (nube y local (servidor AMF))

**Tipo:** Características Premium


**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
|  |  | ![Comprobar][12]| Limitado a la nube solo aplicaciones de Office 365|

**Más detalles:**

- [¿Qué es la autenticación multifactor de Azure?](../multi-factor-authentication/multi-factor-authentication.md)



#### <a name="mim-cal--mim-server"></a>CAL MIM + MIM Server

Se le ha concedido derechos de software de servidor de administrador de identidad de Microsoft con las licencias de Windows Server (cualquier versión). Como administrador de identidad de Microsoft se ejecuta en el sistema operativo Windows Server, siempre y cuando el servidor está ejecutando una copia con licencia válida de Windows Server, Administrador de identidad de Microsoft se puede instalar y usar en el servidor. Ninguna otra licencia independiente es necesario para el servidor del Administrador de identidad de Microsoft.

**Tipo:** Características Premium


**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
|  |  | ![Comprobar][12]|  |





#### <a name="cloud-app-discovery"></a>Detección de aplicación de nube

**Tipo:** Características Premium


**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
|  |  | ![Comprobar][12]|  |

**Más detalles:**

- [Aplicaciones de nube de buscar no administrada con detección de aplicación de nube](active-directory-cloudappdiscovery-whatis.md)



#### <a name="azure-ad-connect-health"></a>Azure AD Connect estado

**Tipo:** Características Premium


**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
|  |  | ![Comprobar][12]|  |

**Más detalles:**

- [Supervisar los servicios de sincronización y la infraestructura de identidad local en la nube](active-directory-aadconnect-health.md)



#### <a name="automatic-password-rollover-for-group-accounts"></a>Renovación automática de la contraseña para las cuentas de grupo

**Tipo:** Características Premium


**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
|  |  | ![Comprobar][12]|  |


#### <a name="identity-protection"></a>Protección de identidad

**Tipo:** Características Premium

| Edición gratuita| Edición básica| Edición de P2 Premium | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
|  |  | ![Comprobar][12]|  |


#### <a name="privileged-identity-management"></a>Administración de identidades con privilegios

**Tipo:** Características Premium

| Edición gratuita| Edición básica| Edición de P2 Premium | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
|  |  | ![Comprobar][12]|  |


## <a name="azure-active-directory-join--windows-10-only--related-features"></a>Azure Active Directory unirse a – Windows 10 solo relacionadas con las características
#### <a name="join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery"></a>Unirse a un dispositivo Passport de Azure AD, SSO de escritorio, Microsoft Azure AD, recuperación de Bitlocker de administrador

**Tipo:** Azure Active Directory unirse a – Windows 10 solo relacionadas con las características


**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
| ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]| ![Comprobar][12]|




#### <a name="mdm-auto-enrollment--self-service-bitlocker-recovery-additional-local-administrators-to-windows-10-devices-via-azure-ad-join"></a>MDM inscripción automática, recuperación de autoservicio Bitlocker, administradores locales adicionales para Windows 10 dispositivos a través de Azure AD unirse

**Tipo:** Azure Active Directory unirse a – Windows 10 solo relacionadas con las características


**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
|  |  | ![Comprobar][12]|  |


#### <a name="enterprise-state-roaming"></a>Estado de Enterprise móviles

**Tipo:** Azure Active Directory unirse a – Windows 10 solo relacionadas con las características


**Disponibilidad:**

| Edición gratuita| Edición básica| Ediciones Premium (P1 y P2) | Sólo aplicaciones de Office 365 |
| :-: | :-: | :-: | :-: |
| | | ![Comprobar][12]| |

**Más detalles:**

- [Estado de Enterprise móviles](active-directory-windows-enterprise-state-roaming-overview.md)


## <a name="azure-ad-preview-features"></a>Características de vista previa de Azure AD
Además de las características disponibles, en general, libre, Basic y Premium (P1 y P2) ediciones, Azure AD también le ofrece un conjunto de características de vista previa. Puede usar las características de vista previa para obtener una impresión de cuál es en un futuro próximo y para determinar si estas características pueden ayudar a mejorar su entorno.

**Características de vista previa disponibles:**

- [Colaboración de B2B](active-directory-b2b-collaboration-overview.md)
- [Unidades administrativas](active-directory-administrative-units-management.md)
- [Integración de aplicación de recursos humanos](active-directory-saas-workday-inbound-tutorial.md)
- [Autenticación basada en certificados en iOS](active-directory-certificate-based-authentication-ios.md)
- [Autenticación basada en certificados en Android](active-directory-certificate-based-authentication-android.md)






## <a name="whats-next"></a>¿Qué es la siguiente

- [Introducción a Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Agregar la marca para iniciar sesión y Panel de acceso a las páginas de la empresa](active-directory-add-company-branding.md)
- [Ver los informes de access y el uso](active-directory-view-access-usage-reports.md)

<!--Image references-->
[12]: ./media/active-directory-editions/ic195031.png
