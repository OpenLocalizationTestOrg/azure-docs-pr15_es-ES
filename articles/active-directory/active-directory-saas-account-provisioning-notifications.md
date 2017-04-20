<properties
    pageTitle="Notificaciones de aprovisionamiento de cuenta | Microsoft Azure"
    description="Obtenga información sobre cómo asegurarse de que se le notifica de problemas relacionados con el aprovisionamiento de usuario que requieren la atención al habilitar notificaciones de aprovisionamiento de cuenta."
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
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="account-provisioning-notifications"></a>Notificaciones de aprovisionamiento de la cuenta

Con el aprovisionamiento de usuario, puede automatizar el proceso de administración de usuarios en aplicaciones de terceros SaaS. <br>
Aunque se trata de un proceso automatizado, la interacción con este proceso de vez en cuando es necesaria. <br>
Esto ocurre, por ejemplo, cuando la contraseña de la cuenta que ha configurado para intercambiar datos con un tercero SaaS ha expirado la aplicación de terceros. 

Habilitar las notificaciones de aprovisionamiento de cuenta, puede asegurarse de que se le notifica de problemas relacionados con el aprovisionamiento de usuario que requieren su atención.

Activar o desactivar las notificaciones de aprovisionamiento como parte de la configuración para una aplicación de SaaS de terceros de aprovisionamiento de usuarios de cuenta.

![Aprovisionamiento de usuario][1] 



Para activar las notificaciones de aprovisionamiento de cuenta, seleccione la casilla de verificación relacionada en la página de diálogo de **confirmación** y, a continuación, escriba el alias de correo electrónico del destinatario.

![Notificaciones de aprovisionamiento de la cuenta][2]
 


Puede especificar una lista de distribución como destinatario; Sin embargo, es importante que tenga en cuenta que el correo electrónico de notificación contiene vínculos a los informes que solo tienen acceso a los administradores de Azure AD.

Si tiene habilitadas las notificaciones de aprovisionamiento de cuenta, recibirá mensajes sobre problemas relacionados con el aprovisionamiento de usuario. Sin embargo, para evitar una sobrecarga de correo electrónico, sólo recibirá una notificación por correo electrónico al día para cada aplicación de SaaS para que se habilita el correo electrónico de notificación.


##<a name="related-articles"></a>Artículos relacionados

- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
- [Automatizar usuario aprovisionamiento y baja a aplicaciones de SaaS](active-directory-saas-app-provisioning.md)
- [Personalizar las asignaciones de atributo para el aprovisionamiento de usuario](active-directory-saas-customizing-attribute-mappings.md)
- [Escritura de expresiones para asignaciones de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Especificar el ámbito de filtros para el aprovisionamiento de usuario](active-directory-saas-scoping-filters.md)
- [Con SCIM para habilitar el aprovisionamiento automático de usuarios y grupos de Azure Active Directory para las aplicaciones](active-directory-scim-provisioning.md)
- [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS](active-directory-saas-tutorial-list.md)



<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png