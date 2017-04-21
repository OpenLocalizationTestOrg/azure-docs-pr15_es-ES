<properties
   pageTitle="Información general sobre la seguridad de administración de identidades Azure | Microsoft Azure"
   description=" Identidad y Microsoft access administración soluciones ayudan a TI proteger el acceso a aplicaciones y recursos en el centro de datos corporativo y en la nube, lo que permite niveles adicionales de validación, como la autenticación multifactor y directivas de acceso condicional. Este artículo proporciona una descripción general de las principales características de seguridad de Azure que ayuda con la administración de identidades. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="azure-identity-management-security-overview"></a>Información general sobre la seguridad de administración de identidades de Azure

Identidad y Microsoft access administración soluciones ayudan a TI proteger el acceso a aplicaciones y recursos en el centro de datos corporativo y en la nube, lo que permite niveles adicionales de validación, como la autenticación multifactor y directivas de acceso condicional. Supervisar la actividad sospechosa a través de seguridad avanzada, auditoría de alerta y reporting ayuda a mitigar posibles problemas de seguridad. [Azure Active Directory Premium](../active-directory/active-directory-editions.md) proporciona un inicio de sesión único a miles de nube (SaaS) aplicaciones y obtener acceso a aplicaciones web que ejecute local.

Ventajas de seguridad de Azure Active Directory (AD) incluyen la posibilidad de:

- Crear y administrar una identidad única para cada usuario de la empresa híbrido, mantener sincronizados los usuarios, grupos y dispositivos
- Proporcionar acceso de inicio de sesión único a sus aplicaciones incluidas miles de aplicaciones de SaaS previamente integradas
- Habilitar la seguridad de acceso de aplicación aplicando basado en las reglas de autenticación multifactor para ambos local y aplicaciones en la nube
- Proporcionando acceso remoto seguro a aplicaciones web de local a través de Proxy de aplicación de Azure AD

El objetivo de este artículo es proporcionar una descripción general de las principales características de seguridad de Azure que ayuda con la administración de identidades. También le proporcionamos los vínculos a artículos que encontrará detalles de cada característica, por lo que puede obtener más información.  

El artículo se centra en las capacidades de administración de identidades de Azure core siguientes:

- Inicio de sesión único
- Proxy inverso
- Autenticación multifactor
- Supervisión de seguridad, alertas e informes de basados en aprendizaje de equipo
- Administración de identidades y acceso de consumidor
- Registro de dispositivo
- Administración de identidades con privilegios
- Protección de identidad
- Administración de identidades híbrido

## <a name="single-sign-on"></a>Inicio de sesión único

Solo inicio de sesión (SSO) significa poder tener acceso a todas las aplicaciones y los recursos que necesita hacer negocios, al iniciar sesión en una sola vez usando una sola cuenta de usuario. Una vez que haya iniciado sesión, puede acceder a todas las aplicaciones que necesita sin que sea necesario para autenticar (por ejemplo, escriba una contraseña) una segunda vez.

Muchas organizaciones dependen de software como un servicio (SaaS) aplicaciones como Office 365, cuadro de Salesforce para la productividad del usuario final. Tradicionalmente, personal de TI necesarios para crear y actualizar cuentas de usuario en cada aplicación de SaaS de forma individual y, a continuación, los usuarios tenían recordar una contraseña para cada aplicación de SaaS.

Azure AD extiende local de Active Directory en la nube, que los usuarios puedan usar su cuenta principal de la organización no sólo iniciar sesión en sus dispositivos de dominio y recursos de la compañía, pero también todos los web y aplicaciones SaaS necesitan para su trabajo.

No solo los usuarios no es necesario administrar varios conjuntos de nombres de usuario y contraseñas, acceso a la aplicación puede ser aprovisionados o anule la preparación automáticamente en función de grupos de la organización y su estado de un empleado. Azure AD presenta seguridad y acceder a los controles de gobierno que le permiten administrar de forma centralizada el acceso de los usuarios en todas las aplicaciones de SaaS.

Aprende más:

- [Descripción general de inicio de sesión único](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
- [¿Qué es el acceso a la aplicación e inicio de sesión único con Azure Active Directory?](../active-directory/active-directory-appssoaccess-whatis.md)
- [Integrar el inicio de sesión único Azure Active Directory con aplicaciones de SaaS](../active-directory/active-directory-sso-integrate-saas-apps.md)

## <a name="reverse-proxy"></a>Proxy inverso

Proxy de aplicación de Azure AD le permite publicar aplicaciones locales, como sitios de [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) , [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx)y [IIS](http://www.iis.net/)-según aplicaciones dentro de la red privada y proporciona acceso seguro a usuarios externos a su red. Proxy de aplicación proporciona acceso remoto y el inicio de sesión único (SSO) para muchos tipos de aplicaciones web de local con las miles de aplicaciones de SaaS compatible con Azure AD. Empleados pueden iniciar sesión en las aplicaciones de inicio en sus propios dispositivos y autenticar a través de este proxy basada en la nube.

Aprende más:

- [Habilitar a Proxy de la aplicación de Azure AD](../active-directory/active-directory-application-proxy-enable.md)
- [Publicar aplicaciones mediante Proxy de aplicación de Azure AD](../active-directory/active-directory-application-proxy-publish.md)
- [Solo sesión con Proxy de aplicación](../active-directory/active-directory-application-proxy-sso-using-kcd.md)
- [Trabajar con acceso condicional](../active-directory/active-directory-application-proxy-conditional-access.md)

## <a name="multi-factor-authentication"></a>Autenticación multifactor
Azure con autenticación multifactor (AMF) es un método de autenticación que requiere el uso de más de un método de verificación y se agrega una segunda capa crítica de seguridad para inicios de sesión de usuario y las transacciones. AMF ayuda a proteger el acceso a datos y aplicaciones cumpliendo demanda de usuario de un proceso de inicio de sesión simple. Ofrece autenticación segura a través de un rango de opciones de comprobación, llamada de teléfono, mensajes de texto o aplicación móvil notificación o una comprobación de código y 3 º fiesta OAuth tokens.

Aprende más:

- [Autenticación multifactor](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
- [¿Qué es la autenticación multifactor de Azure?](../multi-factor-authentication/multi-factor-authentication.md)
- [Cómo funciona la autenticación multifactor de Azure](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Supervisión de seguridad, alertas e informes de basados en aprendizaje de equipo

Supervisión de seguridad y alertas e informes basados en aprendizaje de máquina que identifican patrones de acceso incoherente pueden ayudar a proteger su negocio. Puede usar informes de uso y acceso de Azure Active Directory para obtener visibilidad de la integridad y seguridad de directorio de su organización. Con esta información, un administrador de directorio puede determinar mejor donde puede encontrarse posibles riesgos de seguridad para que puedan planear adecuadamente a mitigar estos riesgos.

En el portal de Azure clásico, los informes se clasifican en las siguientes maneras:

- Informes de anomalías – contienen eventos que hemos encontrado a ser incorrectos de inicio de sesión. Nuestro objetivo es que tenga en cuenta este tipo de actividad y le permiten podrá determinar si un evento es sospechoso.
- Informes de la aplicación integrados: proporcionan información sobre cómo se utilizan aplicaciones de nube de su organización. Azure Active Directory ofrece integración con miles de aplicaciones de la nube.
- Informes de errores: indicar errores que pueden producirse al hacer el aprovisionamiento de cuentas de aplicaciones externas.
- Los informes específicos del usuario: mostrar dispositivo/inicio de sesión de los datos de actividad de un usuario específico.
- Registros de actividad: contienen un registro de todos los eventos de auditoría dentro de las últimas 24 horas últimos 7 días o últimos 30 días, así como cambios de la actividad de grupo y actividad de registro y restablecer contraseña.

Aprende más:

- [Ver los informes de access y el uso](../active-directory/active-directory-view-access-usage-reports.md)
- [Introducción a informes de Azure Active Directory](../active-directory/active-directory-reporting-getting-started.md)
- [Guía de informes de Azure Active Directory](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Administración de identidades y acceso de consumidor

Azure Active B2C de directorio es un servicio de administración de identidad global, altamente disponible para las aplicaciones de consumidor orientación que escala a cientos de millones de identidades. Se puede integrar a través de mobile y web plataformas. Los consumidores pueden iniciar sesión en todas las aplicaciones a través de experiencias personalizables usando sus cuentas sociales existentes o crear nuevas credenciales.

En el pasado, los desarrolladores de aplicación que deseaban suscribirse y los consumidores en sus aplicaciones de inicio de sesión hubiera escrito su propio código. Y habría usan sistemas o bases de datos local para almacenar los nombres de usuario y contraseñas. Azure Active B2C de directorio ofrece a su organización una mejor manera de integrar la administración de identidades de consumidor en las aplicaciones con la Ayuda de una plataforma segura y basada en estándares y un amplio conjunto de directivas extensibles.

Cuando utiliza Azure Active Directory B2C, los consumidores pueden registrarse para las aplicaciones con sus cuentas sociales existentes (Facebook, Google, Amazon, LinkedIn) o mediante la creación de nuevas credenciales (dirección de correo electrónico y contraseña, o nombre de usuario y contraseña).

Aprende más:

- [¿Qué es B2C de Azure Active Directory?](https://azure.microsoft.com/services/active-directory-b2c/)
- [Vista previa de B2C de Active Directory Azure: inicie sesión arriba y los consumidores en las aplicaciones de inicio de sesión](../active-directory-b2c/active-directory-b2c-overview.md)
- [Vista previa de Azure Active Directory B2C: Tipos de aplicaciones](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Registro de dispositivo

Registro de dispositivo de Azure AD es la base para escenarios de dispositivo de [acceso condicional](../active-directory/active-directory-conditional-access-on-premises-setup.md) . Cuando se registra un dispositivo, registro de dispositivo de Azure Active Directory proporciona el dispositivo con una identidad que se utiliza para autenticar el dispositivo cuando el usuario inicia sesión. El dispositivo autenticado y los atributos del dispositivo, pueden utilizarse para aplicar directivas de acceso condicional para las aplicaciones que se hospedan en la nube y locales.

Cuando se combina con una solución de administración (MDM) de un dispositivo móvil como Intune, se actualizan los atributos del dispositivo de Azure Active Directory con información adicional sobre el dispositivo. Esto le permite crear reglas de acceso condicional exigir acceso desde dispositivos para cumplir con los estándares de seguridad y cumplimiento.

Aprende más:

- [Empezar a trabajar con el registro de dispositivo de Azure Active Directory](../active-directory/active-directory-conditional-access-device-registration-overview.md)
- [Configurar el acceso condicional locales con Azure Active Directory dispositivo de registro](../active-directory/active-directory-conditional-access-on-premises-setup.md)
- [Registro de dispositivo automática con dispositivos de dominio de Azure Active Directory para Windows](../active-directory/active-directory-conditional-access-automatic-device-registration.md)

## <a name="privileged-identity-management"></a>Administración de identidades con privilegios
Administración de identidades con privilegios de Active Directory (AD) de Azure le permite administrar, controlar y supervisar las identidades con privilegios y acceso a los recursos en Azure AD, así como otros servicios en línea de Microsoft como Office 365 o Microsoft Intune.

A veces, los usuarios necesitan para llevar a cabo operaciones con privilegios en recursos de Azure u Office 365, o en otras aplicaciones de SaaS. Esto suele significar que las organizaciones tiene para darle acceso con privilegios permanente en Azure AD. Esto es aumentando el riesgo de seguridad para recursos hospedada en la nube porque las organizaciones suficientemente no pueden supervisar qué hacen los usuarios con los privilegios de administrador. Además, si se compromete a una cuenta de usuario con acceso con privilegios, que una infracción puede afectar su seguridad general de la nube. Administración de identidades de AD privilegios Azure ayuda a resolver este riesgo.

Administración de identidades de AD privilegios Azure le permite:

- Vea qué usuarios son administradores de Azure AD
- Habilitar a petición "en el momento" acceso administrativo a Microsoft Online Services, como Office 365 y Intune
- Obtener informes sobre el historial de acceso de administrador y los cambios en las asignaciones de administrador
- Recibir alertas sobre el acceso a una función privilegios

Aprende más:

- [Administración de identidades de Azure con privilegios de AD](../active-directory/active-directory-privileged-identity-management-configure.md)
- [Funciones de Azure AD con privilegios de administración de identidades](../active-directory/active-directory-privileged-identity-management-roles.md)
- [Azure AD con privilegios la administración de identidades: Cómo agregar o quitar una función de usuario](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Protección de identidad
Protección de identidades de AD Azure es un servicio de seguridad que ofrece una vista consolidada en eventos de riesgos y posibles vulnerabilidad que afectan a las identidades de su organización. Protección de identidad aprovecha las capacidades de detección de Azure Active Directory existente anomalías (disponibles a través de informes de actividad anómala de Azure AD) e introduce a nuevos tipos de evento de riesgo que pueden detectar anomalías en tiempo real.

Aprende más:

- [Protección de la identidad de Azure Active Directory](../active-directory/active-directory-identityprotection.md)
- [Canal 9: Azure AD y mostrar identidad: vista previa de protección de identidad](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>Administración de identidades híbrido

Enfoque de Microsoft para identidad abarca local y la nube, la creación de una identidad de usuario único para la autenticación y autorización para todos los recursos, independientemente de la ubicación.

Aprende más:

- [Híbrido identidad notas del producto](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
- [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
- [Blog del equipo de Active Directory](https://blogs.technet.microsoft.com/ad/)
