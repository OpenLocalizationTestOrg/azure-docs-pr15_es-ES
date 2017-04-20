<properties
   pageTitle="Cómo integrar con Azure Active Directory | Microsoft Azure"
   description="Guía de recursos para la integración con Azure Active Directory y ventajas de."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="integrating-with-azure-active-directory"></a>Integración con Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory proporciona a las organizaciones con la administración de identidades empresariales para las aplicaciones de la nube.  Integración de Azure AD proporciona a los usuarios una experiencia de inicio de sesión simplificada y ayuda a la aplicación se ajustan a la directiva de TI.

## <a name="how-to-integrate"></a>Cómo integrar

Hay varias formas de la aplicación para integrar con Azure AD.  Aproveche como muchos o pocos de estos escenarios como es adecuado para la aplicación.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Azure AD como una manera de iniciar sesión en la aplicación de soporte técnico

**Reducir fricción de inicio de sesión y a reducir los costos de soporte técnico.** Mediante el uso de Azure AD para iniciar sesión en la aplicación, los usuarios no tienen un nombre más y la contraseña de recordar.  Como desarrollador, tendrá menos contraseña para almacenar y proteger.  No tener que controlar el restablecimiento de contraseña olvidada puede ser un ahorro significativo únicamente.  Azure AD potencia de inicio de sesión para algunas aplicaciones de nube más populares del mundo, como Office 365 y Microsoft Azure.  Con cientos de millones los usuarios millones de las organizaciones, lo más probable es el usuario ya ha iniciado sesión en Azure AD.  Más información sobre [Agregar compatibilidad para Azure AD de inicio de sesión](../active-directory-authentication-scenarios.md).

**Simplificar el inicio de sesión hacia arriba para su aplicación.**  Durante el registro de la aplicación, Azure AD puede enviar información esencial sobre un usuario para que puedan rellenar el formulario de inicio de sesión o eliminarlo por completo.  Los usuarios pueden registrarse para su aplicación utilizando su cuenta de Azure AD a través de una experiencia familiar consentimiento similar a los que se encuentra en redes sociales y aplicaciones móviles.  Cualquier usuario puede registrarse e iniciar sesión en una aplicación que se integra con Azure AD sin necesidad de participación de TI.  Más información sobre [la aplicación de inicio de sesión de cuenta de Azure AD firma de seguridad](../../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Busque usuarios, administrar el aprovisionamiento de usuario y controlar el acceso a la aplicación

**Buscar usuarios en el directorio.**  Utilizar la API de gráfico para ayudar a los usuarios buscar y busque otras personas de su organización al invitar a otras personas o direcciones de concesión de acceso, en lugar de pedirles que escriba correo electrónico.  Los usuarios pueden explorar mediante una interfaz de estilo de la libreta de dirección conocida, incluida la visualización de los detalles de la jerarquía organizativa.  Más información acerca de la [API de gráfico](../active-directory-graph-api.md).

**Volver a usar su cliente ya es la administración de listas de distribución y grupos de Active Directory.**  Azure AD contiene los grupos que su cliente ya está usando para la distribución de correo electrónico y administrar el acceso.  Con la API de gráfico, reutilizar estos grupos en lugar de requerir su cliente crear y administrar un conjunto diferente de grupos en la aplicación.  Información del grupo también se envía a la aplicación en tokens de inicio de sesión.  Más información acerca de la [API de gráfico](../active-directory-graph-api.md).

**Use Azure AD para controlar quién tiene acceso a la aplicación.**  Los administradores y los propietarios de la aplicación en Azure AD pueden asignar acceso a las aplicaciones a usuarios y grupos específicos.  Con la API de gráfico, puede leer esta lista y usarlo para crear y eliminar de recursos de control y acceso dentro de la aplicación.

**Usar Azure AD para las funciones en función de Control de acceso.**  Los administradores y los propietarios de la aplicación pueden asignar usuarios y grupos a las funciones que defina al registrar la aplicación en Azure AD.  Información de la función se envía a la aplicación en el inicio de sesión en tokens y también pueden leerse con la API de gráfico.  Más información sobre el [uso de Azure AD para autorización](http://blogs.technet.com/b/ad/archive/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles.aspx).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Obtener acceso a perfil de usuario, calendario, correo electrónico, contactos, archivos y mucho más

**Azure AD es el servidor de autorización para Office 365 y otros servicios de empresa de Microsoft.**  Si admite Azure AD para iniciar sesión en su aplicación o la compatibilidad con la vinculación de sus cuentas de usuario actual para las cuentas de usuario de Azure AD mediante OAuth 2.0, puede solicitar el acceso de lectura y escritura a un perfil de usuario, calendario, correo electrónico, contactos, archivos y otra información.  Sin problemas puede escribir eventos al calendario del usuario y leer o escribir archivos en su OneDrive.  Más información sobre el [acceso a la API de Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Aumentar el nivel de la aplicación en Azure y mercados de Office 365

**Aumentar el nivel de la aplicación a los millones de las organizaciones que ya está usando Azure AD.**  Los usuarios que busquen y busque estos mercados ya está usando uno o más servicios en la nube, hacerle calificado clientes de servicio de nube.  Más información acerca de cómo promover la aplicación de [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Cuando se registran los usuarios de la aplicación, aparecerá en su panel de acceso de Azure AD y el iniciador de aplicaciones de Office 365.**  Los usuarios podrán rápida y fácilmente volver a la aplicación más tarde, mejorar el compromiso de usuario.  Más información sobre el [panel de acceso de Azure AD](../active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Servicio de dispositivos y servicio de servicio de comunicación segura

**Uso de Azure AD para la administración de identidades de los servicios y dispositivos reduce el código que necesario para escribir y permite a TI para administrar el acceso.**  Servicios y dispositivos pueden obtener tokens de Azure AD mediante OAuth y utilizar esos símbolos para tener acceso a las API de web.  Con Azure AD no puede escribir el código de autenticación complejas.  Dado que las identidades de los servicios y dispositivos se almacenan en Azure AD TI puede administrar claves y revocación en un solo lugar en lugar de hacerlo por separado en su aplicación.

## <a name="benefits-of-integration"></a>Ventajas de la integración

Integración con Azure AD incluye ventajas que no requieren escribir código adicional.

### <a name="integration-with-enterprise-identity-management"></a>Integración con la administración de identidades empresariales

**Ayuda de la aplicación cumplir con las directivas de TI.**  Las organizaciones integran los sistemas de administración de identidades de su empresa con Azure AD para que cuando una persona deja una organización, perderán automáticamente acceso a su aplicación sin necesidad de realizar pasos adicionales de TI.  TI puede administrar quién puede tener acceso a la aplicación y determinar qué directivas de acceso son necesarias - ejemplo con autenticación multifactor - reducir la necesidad de escribir código para cumplir con las directivas corporativas complejos.  Azure AD proporciona a los administradores un registro detallado de auditoría de quién ha iniciado sesión en la aplicación así que TI puede realizar un seguimiento de uso.

**Azure AD extiende Active Directory en la nube para que pueda integrar la aplicación con AD.**  Muchas organizaciones de todo el mundo usar Active Directory como su sesión principal y el sistema de administración de identidad y requieren sus aplicaciones para que funcione con AD.  Integración con Azure AD, la aplicación integra con Active Directory.

### <a name="advanced-security-features"></a>Características de seguridad avanzada

**Autenticación multifactor.**  Azure AD proporciona la autenticación multifactor nativa.  Los administradores de TI pueden requerir autenticación multifactor para tener acceso a la aplicación, para que no tiene el código de esta compatibilidad usted mismo.  Más información sobre la [Autenticación multifactor](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Detección de inicio de sesión irregulares.**  Azure AD procesa inicios de sesión de más de mil millones día, mientras utiliza algoritmos de aprendizaje del equipo para detectar actividad sospechosa y notificar a los administradores de TI de posibles problemas.  Compatible con inicio de sesión de Azure AD, la aplicación obtiene el beneficio de esta protección. Más información sobre la [visualización de Azure Active Directory informe de access](../active-directory-view-access-usage-reports.md).

**Acceso condicional.**  Además de la autenticación multifactor, los administradores pueden solicitar cumplirse las condiciones específicas antes de que los usuarios puedan iniciar sesión en la aplicación.  Las condiciones que se pueden establecer incluir el intervalo de direcciones IP de dispositivos cliente, pertenencia a grupos especificados y el estado del dispositivo que se usa para el acceso.  Más información sobre el [acceso condicional de Azure Active Directory](../active-directory-conditional-access.md).

### <a name="easy-development"></a>Desarrollo fácil

**Protocolos estándar.**  Microsoft se compromete a estándares de apoyo.  Azure AD es compatible con los protocolos de autenticación SAML 2.0, OpenID Connect 1.0, OAuth 2.0 y WS-Federation 1.2.  La API de gráfico es OData 4.0 compatible.  Si su aplicación ya es compatible con los protocolos SAML 2.0 o OpenID Connect 1.0 para el inicio de sesión federado, agregar compatibilidad para Azure AD puede ser sencillo.  Más información sobre [los protocolos de autenticación compatible de Azure AD](../active-directory-authentication-protocols.md).

**Bibliotecas de código abierto.**  Microsoft proporciona bibliotecas de código abierto totalmente compatibles para idiomas populares y plataformas de desarrollo de velocidad.  El código fuente está bajo licencia de Apache 2.0, y es libre de horquilla y contribuir a los proyectos.  Más información acerca de [las bibliotecas de autenticación de Azure AD](../active-directory-authentication-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Alta disponibilidad y presencia en todo el mundo

**Azure AD se implementa en centros de datos de todo el mundo y está administrado y supervisado alrededor del reloj.**  Azure AD es el sistema de administración de identidades para Microsoft Azure y Office 365 y se implementa en 28 centros de datos de todo el mundo.  Datos del directorio se garantiza que se pueden replicar en al menos tres centros de datos.  Equilibradores de carga global garantizar el acceso a los usuarios la copia más cercana de Azure AD que contiene los datos y volver a enrutar automáticamente las convocatorias a otros centros de datos si se detecta un problema.

## <a name="next-steps"></a>Pasos siguientes

[Empezar a escribir el código](../active-directory-developers-guide.md#getting-started).

[Usuarios de inicio de sesión en el uso de Azure AD](../active-directory-authentication-scenarios.md)
