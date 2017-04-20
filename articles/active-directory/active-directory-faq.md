<properties
    pageTitle="Preguntas más frecuentes de Azure Active Directory | Microsoft Azure"
    description="Azure Active Directory preguntas más frecuentes que proporcionan respuestas a preguntas junto con el acceso a Azure y Azure Active Directory, acceso de administración y aplicación de la contraseña."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="markusvi"/>

# <a name="azure-active-directory-faq"></a>Preguntas más frecuentes de Azure Active Directory

Azure Active Directory es una identidad completa como una solución de servicio (IDaaS) que abarca todos los aspectos de seguridad, administración de acceso e identidad.


Para obtener más detalles, consulte [¿Qué es Azure Active Directory?](active-directory-whatis.md).



## <a name="accessing-azure-and-azure-active-directory"></a>Obtener acceso a Azure y Azure Active Directory


**P: ¿por qué obtengo "suscripciones no encontraron" al intentar obtener acceso a Azure AD en el portal de Azure clásico (https://manage.windowsazure.com)?**

**A:** Acceder al portal clásico Azure requiere que cada usuario con permisos en una suscripción de Azure. En caso contrario, si tiene un pago de Office 365 o desplácese hasta [http://aka.ms/accessAAD](http://aka.ms/accessAAD) de un paso de una única activación de Azure AD, deberá activar una completa [evaluación de Azure](https://azure.microsoft.com/pricing/free-trial/) o una suscripción de pago. 

Para obtener más detalles, consulte:

- [Cómo se suscripciones Azure están asociadas a Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [Administrar el directorio de suscripción de Office 365 en Azure](active-directory-manage-o365-subscription.md)

---

**P: ¿cuál es la relación entre Azure AD, Office 365 y Azure?**

**A:** Azure Active Directory le ofrece capacidades comunes de identidad y acceso a todos los servicios en línea de Microsoft. Si está utilizando Office 365, Microsoft Azure, Intune u otros usuarios, ya se usa un Azure AD habilitar inicio de sesión y obtener acceso a la administración de todos estos servicios de. 

De hecho, todos los usuarios que se ha habilitado para Microsoft Online services se definen como cuentas de usuario en una o varias instancias de Azure AD. Puede habilitar estas capacidades de cuentas de forma gratuita Azure AD como acceso a la aplicación de nube.
 
Además, Azure AD pagado servicios (por ejemplo: Azure AD basic, Premium, EMS, etc.) complemento a otro en línea servicios como Office 365 y Microsoft Azure con las soluciones de administración y la seguridad de escala empresariales.


---



## <a name="getting-started-with-hybrid-azure-ad"></a>Introducción a híbrido Azure AD


**P: ¿Cómo puedo conectar mi directorio local a Azure AD?**

**A:** Puede conectar su directorio local a Azure AD con **Azure AD Connect**. 

Para obtener más detalles, consulte [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).


---

**P: ¿cómo configurar SSO entre mi directorio local y mis aplicaciones de nube?**

**A:** Solo debe configurar SSO entre su directorio local y Azure AD. Como obtener acceso a sus aplicaciones de nube a través Azure AD, el servicio de unidades automáticamente los usuarios para autenticar correctamente con sus credenciales local.

Implementar SSO locales puede conseguirse de fácilmente con las soluciones de federación como ADFS o mediante la configuración de sincronización de hash de la contraseña. Puede implementar fácilmente ambas opciones con el Asistente para configuración de Azure AD Connect.
  

Para obtener más detalles, consulte [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
  

---

**P: ¿Azure Active Directory ofrece un portal de autoservicio para los usuarios de mi organización?**

**A:** Sí, Azure Active Directory proporciona el [Panel de Azure AD acceso](http://myapps.microsoft.com) de autoservicio de usuario y acceso a la aplicación. Si es un cliente de Office 365, puede encontrar muchas de las mismas capacidades en el portal de Office 365. 

Para obtener más información, vea la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 



---

**P: ¿Azure AD ayudarme a administrar mi infraestructura local?**

**A:** Sí, tiene. La edición de Azure AD Premium proporciona con su **Estado de conexión**. Estado de conexión de Azure AD le ayuda a supervisar y obtenga información sobre la infraestructura de identidad local y los servicios de sincronización.  

Para obtener más detalles, consulte [los servicios de sincronización y la infraestructura de identidad Monitor su local en la nube](active-directory-aadconnect-health.md).  

---

## <a name="password-management"></a>Administración de contraseñas

**P: ¿puedo usar la contraseña de Azure AD reescritura sin sincronización de la contraseña? (También conocido como, deseo usar Azure AD SSPR con reescritura de contraseña, pero no quiero que mis contraseñas almacenadas en el cloud?)**

**A:** No es necesario sincronizar las contraseñas de AD a Azure AD con el fin de habilitar reescritura. En un entorno federado, Azure AD SSO se basa en el directorio local para autenticar al usuario. Este escenario no requiere la contraseña local para su seguimiento en Azure AD.

---

**P: ¿cuánto tiempo tarda una contraseña que se escriban volver a AD local?**

**A:** Contraseña de escritura no funciona en tiempo real. 

Para obtener más detalles, consulte [Introducción a la administración de contraseñas](active-directory-passwords-getting-started.md) 


---

**P: ¿puedo usar reescritura de contraseña con contraseñas administradas por un administrador?**

**A:** Sí, si tiene habilitada la reescritura de contraseña, las operaciones de contraseña realizadas por un administrador se vuelven a escribir su entorno local.  

Más respuestas a contraseña preguntas relacionadas, consulte [Preguntas más frecuentes de contraseña de administración](active-directory-passwords-faq.md).

---

## <a name="application-access"></a>Acceso a la aplicación


**P: ¿dónde puedo encontrar una lista de aplicaciones integradas previamente con Azure AD y sus capacidades?**

**A:** Azure AD tiene más 2600 aplicaciones previamente integradas de Microsoft, proveedores de servicios de aplicación o socios. Todas las aplicaciones integradas previamente admiten SSO. SSO permite utilice sus credenciales de la organización para tener acceso a sus aplicaciones. Algunas de las aplicaciones también admiten aprovisionamiento automatizado y eliminar

Para obtener una lista completa de las aplicaciones integradas previamente, vea el [Catálogo de soluciones de Active Directory](https://azure.microsoft.com/marketplace/active-directory/).


---

**P: ¿qué debo hacer si la aplicación que necesita no está en el catálogo de soluciones de Azure AD?**

**A:** Con Azure AD Premium, puede agregar y configurar cualquier aplicación que desee. Dependiendo de las capacidades de la aplicación y sus preferencias, puede configurar SSO y aprovisionamiento automatizado.  

Para obtener más detalles, consulte:

- [Configuración de inicio de sesión único en aplicaciones que no están en la Galería de la aplicación de Azure Active Directory](active-directory-saas-custom-apps.md)
- [Con SCIM para habilitar el aprovisionamiento automático de usuarios y grupos de Azure Active Directory para las aplicaciones](active-directory-scim-provisioning.md) 


---

**P: ¿cómo firmar los usuarios en las aplicaciones con Azure Active Directory?**
 
**A:** Azure Active directory proporciona varias formas para que los usuarios ver y tener acceso a sus aplicaciones como:

- El panel de acceso de Azure AD

- El iniciador de aplicaciones de Office 365

- Inicio de sesión directamente en aplicaciones federadas

- Profundidad vínculos federados, basado en contraseña o aplicaciones existentes

Para obtener más información, vea [implementar Azure AD integrado aplicaciones a los usuarios](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).


---

**P: ¿Cuáles son las diferentes formas de Azure Active Directory habilita la autenticación y el inicio de sesión único para las aplicaciones?**
 
**A:** Azure Active Directory es compatible con muchos protocolos estándares para la autenticación y la autorización como SAML 2.0, OpenID conectarse, OAuth 2.0 y WS-Federation. Azure AD también admite la protección de contraseña y capacidades de inicio de sesión automáticas para las aplicaciones que solo admiten la autenticación basada en formularios.  

Para obtener más información, consulte:

- [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md)

- [Protocolos de autenticación de Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)

- [¿Cómo un solo inicio de sesión con el trabajo de Azure Active Directory?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)


---

**P: ¿puedo agregar aplicaciones ejecuto local?**

**A:** Proxy de aplicación de Azure AD le proporciona acceso seguro y sencillo a las aplicaciones web de local que elija. Puede acceder a estas aplicaciones de la misma manera que se tiene acceso a sus aplicaciones de SaaS en Azure Active Directory. No es necesario para una VPN o cambiando su infraestructura de red.  

Para obtener más detalles, consulte [cómo proporcionar acceso remoto seguro a aplicaciones locales](active-directory-application-proxy-get-started.md).


--- 

**P: ¿cómo requiere AMF para usuarios que tienen acceso a una aplicación en particular?**

**A:** Con el acceso condicional Azure AD, puede asignar una directiva de acceso único para cada aplicación. En la directiva, puede requerir AMF en todo momento, o cuando los usuarios no están conectados a la red local.  

Para obtener más detalles, vea [proteger el acceso a Office 365 y otras aplicaciones conectados a Azure Active Directory](active-directory-conditional-access.md).


---

**P: ¿cuál es el aprovisionamiento de usuario automática para las aplicaciones SaaS?**

**A:** Azure Active Directory le permite automatizar la creación, el mantenimiento y la eliminación de identidades de usuario en muchas aplicaciones de nube populares (SaaS). 

Para obtener más información, vea [automatizar el aprovisionamiento de usuario y Deprovisioning a aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)

---



