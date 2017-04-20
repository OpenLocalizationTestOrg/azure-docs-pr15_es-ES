<properties
    pageTitle="Proteger el acceso con privilegios de Azure AD | Microsoft Azure"
    description="Un tema que se explica los métodos para proteger el acceso con privilegios en Azure, Azure Active Directory y Microsoft Online Services."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="mwahl"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="kgremban"/>


# <a name="securing-privileged-access-in-azure-ad"></a>Proteger el acceso con privilegios de Azure AD

Proteger el acceso con privilegios es un primer paso fundamental para ayudar a proteger los activos empresariales de una organización moderna. Cuentas con privilegios son aquellos que administrar y administrar los sistemas de TI. Atacantes cibernético destino estas cuentas para tener acceso a los datos y sistemas de la organización. Para proteger el acceso con privilegios, debe aislar las cuentas y los sistemas de los riesgos de exposición a un usuario malintencionado.

Más usuarios han comenzado obtener acceso con privilegios a través de los servicios en la nube. Esto puede incluir los administradores globales de Office 365, los administradores de la suscripción de Azure y los usuarios que tienen acceso administrativo en máquinas virtuales o en aplicaciones de SaaS.

Microsoft recomienda que siga esta guía en [Proteger acceso con privilegios](https://technet.microsoft.com/library/mt631194.aspx).

Uso de Azure Active Directory para administrar el acceso a Azure, Office 365, u otros servicios de Microsoft y aplicaciones de los clientes, estos principios se aplican si las cuentas de usuario se administran y autenticadas por Active Directory o de Azure Active Directory. Las secciones siguientes proporcionan más información sobre las funciones de Azure AD a proteger el acceso con privilegios de soporte técnico.

## <a name="multi-factor-authentication"></a>Autenticación multifactor

Para aumentar la seguridad de autenticación de administrador, debe requerir autenticación multifactor (AMF) antes de conceder privilegios. AMF es un método para comprobar quién es usted que requiere el uso de más que un nombre de usuario y contraseña. Proporciona una segunda capa de seguridad para inicios de sesión de usuario y las transacciones.

Azure autenticación multifactor ayuda a proteger el acceso a datos y aplicaciones cumpliendo demanda de usuario de un proceso de inicio de sesión simple. Ofrece autenticación segura a través de un rango de opciones de comprobación fácil incluidos llamadas de teléfono, mensajes de texto, las notificaciones de la aplicación móvil, o código de comprobación y 3er tokens JURAMENTO de fiesta.

Para obtener información general sobre cómo funciona la autenticación multifactor de Azure, vea el siguiente vídeo.

>[AZURE.VIDEO windows-azure-multi-factor-authentication]

Para obtener más detalles, consulte [AMF para Office 365 y AMF para Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/).

## <a name="time-bound-privileges"></a>Privilegios de límite de tiempo

Algunas organizaciones pueden encontrarse con que tienen demasiados usuarios en roles con privilegios elevados. Un usuario podría se agregaron a la función de una actividad determinada, como inicio de sesión en un servicio, pero no usó esos privilegios con frecuencia más adelante.

Para reducir el tiempo de exposición de privilegios y aumentar la visibilidad en su uso, limitar los usuarios solo llevar a cabo ninguna sus privilegios Just in Time (JIT), cuando lo necesiten para realizar una tarea. Para Azure Active Directory y los servicios en línea de Microsoft, puede usar [la administración de identidades con privilegios de Azure AD (PIM)](http://aka.ms/AzurePIM).


![Panel PIM][2]


## <a name="attack-detection"></a>Detección de ataques

[Protección de identidad de Azure Active Directory](../active-directory-identityprotection.md) proporciona una vista consolidada en eventos de riesgos y posibles vulnerabilidad que afectan a las identidades de su organización. En función de eventos de riesgo, protección de identidad calcula un nivel de riesgo de usuario para cada usuario, lo que le permite configurar directivas basadas en el riesgo para proteger automáticamente las identidades de su organización. Estas directivas, junto con otros controles de acceso condicional proporcionados por Azure Active Directory y EMS, pueden impedir que el usuario o se ofrecen sugerencias que incluyen el restablecimiento de contraseñas y exigir la autenticación multifactor automáticamente.

![Protección de la identidad de Azure AD][3]

## <a name="conditional-access"></a>Acceso condicional

Con control de acceso condicional Azure Active Directory comprueba las condiciones específicas que elija cuando se autentica un usuario, antes de obtener acceso a la aplicación. Una vez que se cumplen las condiciones, el usuario autenticado y permite el acceso a la aplicación.


![Establecer reglas de acceso condicional con AMF][4]


## <a name="related-articles"></a>Artículos relacionados

- Habilitar [la autenticación multifactor Azure](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
- Habilitar [la administración de identidades de Azure con privilegios de AD](../active-directory-privileged-identity-management-configure.md)
- Habilitar la [protección de identidad de Azure AD](../active-directory-identityprotection.md)
- Habilitar [los controles de acceso condicional](../active-directory-conditional-access.md)


Para obtener más información sobre la creación de un plan de seguridad completo, vea la sección "Guía y responsabilidades de cliente" del documento de [Seguridad de la nube de Microsoft esta versión](http://aka.ms/securecustomer) . Para obtener más información sobre atractivos servicios de Microsoft para ayudar a cualquiera de estos temas, póngase en contacto con su representante de Microsoft o visite nuestra [página de soluciones de la ciberseguridad](https://www.microsoft.com/microsoftservices/campaigns/cybersecurity-protection.aspx).

<!--Image references-->
[1]: ../media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ../media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ../media/active-directory-identityprotection/29.png
[4]: ../media/active-directory-conditional-access/conditionalaccess-saas-apps.png
