<properties
   pageTitle="Guía de introducción de integración de Azure Active Directory con aplicaciones de introducción |  Microsoft Azure"
   description="En este artículo es una guía de introducción para la integración con aplicaciones locales y aplicaciones de nube de Azure Active Directory (AD)."
   services="active-directory"
   documentationCenter=""
   authors="ihenkel"
   manager="femila"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="02/09/2016"
      ms.author="inhenk"/>

# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Guía de introducción de integración de Azure Active Directory con aplicaciones de introducción
## <a name="overview"></a>Información general
Este tema está pensado para dar a un plan de integración de aplicaciones con Azure Active Directory (AD). Cada una de las secciones siguientes contienen un breve resumen de un tema más detallado para que pueda identificar qué partes de esta guía de introducción son relevantes para usted.  Siga los vínculos para un análisis más profundo de cada asunto.

## <a name="before-you-begin-take-inventory"></a>Antes de comenzar, realizar un inventario
Antes de saltar a la integración de las aplicaciones con Azure AD, es importante saber dónde se encuentra y en la que desee ir.  Las preguntas siguientes están diseñadas para ayudarle a pensar en su proyecto de integración de aplicación de Azure AD.

### <a name="application-inventory"></a>Inventario de aplicaciones
- ¿Dónde están todos sus aplicaciones? ¿Quién es el propietario?
- ¿Qué tipo de autenticación requieren las aplicaciones?
- ¿Quién tiene acceso a las aplicaciones?
- ¿Desea distribuir una nueva aplicación?
  - ¿Se crea en la empresa e implementarlo en una instancia de cálculo Azure?
  - ¿Va a utilizar una que está disponible en la Galería de la aplicación de Azure?

### <a name="user-and-group-inventory"></a>Inventario de usuario y de grupo
- ¿Dónde se encuentran sus cuentas de usuario?
 - Local de Active Directory
 - Azure AD
 - Dentro de una base de datos de una aplicación independiente que es el propietario
 - En las aplicaciones no sancionadas
 - Todo lo anterior
- ¿Qué permisos y las asignaciones de roles usuarios individuales tienen actualmente? ¿Necesita revisar su acceso o está seguro de que las asignaciones de acceso y el rol de usuario son las más apropiadas ahora?
- ¿Grupos ya establecidos en Active Directory local?
 - ¿Cómo se organizan los grupos?
 - ¿Quiénes son los miembros del grupo?
 - ¿Qué asignaciones de permisos y funciones tienen actualmente los grupos?
- ¿Se debe limpiar las bases de datos de usuario o grupo antes de integrar?  (Esta es una pregunta bastante importante. Basura en basura fuera).

### <a name="access-management-inventory"></a>Inventario de administración de acceso
- ¿Cómo administrar actualmente acceso de usuario a las aplicaciones? ¿Necesita cambiar?  ¿Ha tenido en cuenta otras formas de administrar el acceso, como por ejemplo con [RBAC](role-based-access-control-configure.md) por ejemplo?
- ¿Quién tiene acceso a qué?

¿No tiene las respuestas a todas estas preguntas adelantado, pero que está bien.  Esta guía puede ayudarle a responder algunas de estas preguntas y decisiones informadas.

## <a name="prerequisites"></a>Requisitos previos
- Una suscripción de Azure y un directorio de Azure Active Directory.  Si todavía no tiene una suscripción de Azure, puede intentar Azure gratis durante 30 días. [¡Pruébelo!](https://azure.microsoft.com/trial/get-started-active-directory/)

## <a name="application-integration-with-azure-ad"></a>Integración de las aplicaciones con Azure AD
### <a name="finding-unsanctioned-cloud-applications-with-cloud-app-discovery"></a>Aplicaciones de nube de buscar no sancionada con detección de aplicación de nube
Como se mencionó anteriormente, es posible que las aplicaciones que todavía no lo ha sido administradas por la organización hasta ahora.  Como parte del proceso de inventario, es posible buscar aplicaciones de nube no sancionada. Consulte [Buscar aplicaciones de nube no sancionada con detección de aplicación de nube](active-directory-cloudappdiscovery-whatis.md).

### <a name="authentication-types"></a>Tipos de autenticación
Cada una de las aplicaciones puede tener requisitos de autenticación diferente. Con Azure AD, pueden usar certificados de firma con aplicaciones que utilizan SAML 2.0, WS-Federation o OpenID conectar protocolos así como contraseña de inicio de sesión único. Para obtener más información sobre la aplicación de los tipos de autenticación para su uso con Azure AD consulte [Administración de certificados para federado inicio de sesión único de Azure Active Directory](active-directory-sso-certs.md) y [contraseña de inicio de sesión único por](active-directory-appssoaccess-whatis.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Habilitar SSO con Proxy de aplicación de Azure AD
Con Microsoft Azure AD aplicación Proxy, puede proporcionar acceso a las aplicaciones que se encuentra dentro de la red privada de forma segura, desde cualquier lugar y en cualquier dispositivo. Después de haber instalado un conector de proxy de aplicación en su entorno, se puede configurar fácilmente con Azure AD.

### <a name="integrating-applications-with-azure-ad"></a>Integración de aplicaciones con Azure AD
Los siguientes artículos describen las distintas formas aplicaciones integran con Azure AD y proporcionan instrucciones.

- [Determinar qué Active Directory para utilizar](active-directory-administer.md)
- [Uso de aplicaciones en la Galería de aplicación de Azure](active-directory-appssoaccess-whatis.md)
- [Integración de la lista de tutoriales de aplicaciones de SaaS](active-directory-saas-tutorial-list.md)

## <a name="managing-access-to-applications"></a>Administrar el acceso a las aplicaciones
Los artículos siguientes describen formas puede administrar el acceso a las aplicaciones una vez que se han integrado con Azure AD con conectores de AD Azure y Azure AD.

- [Administrar el acceso a aplicaciones mediante Azure AD](active-directory-managing-access-to-apps.md)
- [Automatización de conectores de Azure AD](active-directory-saas-app-provisioning.md)
- [Asignar usuarios a una aplicación](active-directory-applications-guiding-developers-assigning-users.md)
- [Asignación de grupos a una aplicación](active-directory-applications-guiding-developers-assigning-groups.md)
- [Uso compartido de cuentas](active-directory-sharing-accounts.md)

## <a name="integrating-custom-applications"></a>Integración de aplicaciones personalizadas
Si está escribiendo una nueva aplicación y desea ayudar a los desarrolladores aprovechar la potencia de Azure AD, vea [directrices programadores](active-directory-applications-guiding-developers-for-lob-applications.md).

Si desea agregar la aplicación personalizada en la Galería de la aplicación de Azure, vea ["Llevarse su propios aplicación" configuración de Azure AD autoservicio SAML](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

## <a name="see-also"></a>Vea también

- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
