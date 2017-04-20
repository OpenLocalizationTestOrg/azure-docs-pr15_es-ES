<properties
    pageTitle="Automatizado en Azure AD de aprovisionamiento de usuarios de aplicación de SaaS | Microsoft Azure"
    description="Una introducción sobre cómo usar Azure AD suministrar automáticamente, anule la aprovisionar y actualizar continuamente las cuentas de usuario en varias aplicaciones de terceros SaaS."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatizar usuario de aprovisionamiento y baja para las aplicaciones SaaS con Azure Active Directory

##<a name="what-is-automated-user-provisioning-for-saas-apps"></a>¿Qué es automatizado aprovisionamiento de usuario para las aplicaciones SaaS?

Azure Active Directory (AD Azure) le permite automatizar la creación, el mantenimiento y la eliminación de identidades de usuario en aplicaciones de nube ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) como Dropbox, Salesforce, ServiceNow y mucho más.

**A continuación se muestran algunos ejemplos de lo que esta característica le permite hacer:**

- Crear automáticamente nuevas cuentas en las aplicaciones de SaaS derecha para nuevos usuarios cuando se unan a su equipo.
- Desactivar cuentas desde aplicaciones de SaaS automáticamente cuando personas inevitable dejan el equipo.
- Asegúrese de que las identidades en las aplicaciones de SaaS están actualizadas según los cambios en el directorio.
- Aprovisionar objetos no de usuario, como grupos de aplicaciones de SaaS que son compatibles con ellos.

**Aprovisionamiento automatizado usuario, también incluye las siguientes funciones:**

- La capacidad para que coincida con las identidades existentes entre Azure AD y aplicaciones de SaaS.
- Opciones de personalización ayuda Azure AD ajustan las configuraciones actuales de las aplicaciones de SaaS que su organización utiliza actualmente.
- Alertas de correo electrónico opcionales para errores de aprovisionamiento.
- Registros de actividad y generación de informes para ayudar a supervisar y solucionar problemas.

##<a name="why-use-automated-provisioning"></a>¿Por qué usar aprovisionamiento automatizado?

Algunas razones comunes para usar esta característica se incluyen:

- Para evitar los costos, eficacia y errores humanos asociado manual de procesos de aprovisionamiento.
- Para proteger su organización quitando instantáneamente las identidades de los usuarios de aplicaciones de SaaS claves cuando sale de la organización.
- Para importar fácilmente un número de forma masiva de usuarios en una aplicación de SaaS determinada.
- Para disfrutar de la comodidad de que su solución de aprovisionamiento ejecutar fuera de las mismas directivas de acceso de aplicación que haya definido para Azure AD inicio de sesión único.

##<a name="frequently-asked-questions"></a>Preguntas más frecuentes

**¿La frecuencia con escribir Azure AD los cambios de directorio en la aplicación de SaaS?**

Azure AD comprueba si hay cambios cada cinco a diez minutos. Si la aplicación de SaaS devuelve varios errores (como en el caso de credenciales de administrador no válido), Azure AD disminuya gradualmente su frecuencia a hasta una vez al día hasta que se corrigen los errores.

**¿Cuánto tiempo tardará proporcionando Mis usuarios?**

Cambios incrementales ocurrir casi al instante, pero si está intentando aprovisionar la mayor parte de su directorio, a continuación, depende del número de usuarios y grupos que tiene. Directorios pequeños tomar solo unos minutos, mediana directorios pueden tardar varios minutos y directorios muy grande pueden tardar varias horas.

**¿Cómo se puede controlar el progreso de la tarea actual de aprovisionamiento?**

Puede revisar el informe de aprovisionamiento de cuenta en la sección informes de su directorio. Otra opción es visitar la pestaña panel para la aplicación de SaaS que son aprovisionamiento a y busque en la sección "Estado de integración" cerca de la parte inferior de la página.

**¿Cómo puedo saber si los usuarios no podrán obtener aprovisionado correctamente?**

Al final de la configuración de aprovisionamiento Asistente allí es una opción para suscribirse a las notificaciones de correo electrónico para el aprovisionamiento de errores. También puede comprobar el informe de errores de aprovisionamiento para ver los usuarios que no se pudo aprovisionar y por qué.

**¿Puede Azure AD escribir los cambios desde la aplicación de SaaS en el directorio?**

Para la mayoría de las aplicaciones de SaaS, aprovisionamiento es sólo saliente, lo que significa que los usuarios se escriben desde el directorio de la aplicación y los cambios de la aplicación no pueden escribirse en el directorio. Para [los días laborables](https://msdn.microsoft.com/library/azure/dn762434.aspx), sin embargo, aprovisionamiento es sólo de entrada, lo que significa que lo que se importan los usuarios en el directorio de la jornada laboral y, a continuación, del mismo modo, cambios en el directorio no se escribieron volver a jornada laboral.

**¿Cómo puedo enviar comentarios al equipo de ingeniería?**

Póngase en contacto con nosotros en el [foro de comentarios de Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

##<a name="how-does-automated-provisioning-work"></a>¿Cómo trabajo de aprovisionamiento automatizado?

Azure AD aprovisiona usuarios a aplicaciones de SaaS conectándose a aprovisionamiento extremos proporcionados por cada proveedor de aplicación. Estos extremos permiten Azure AD crear, actualizar y eliminar usuarios mediante programación. A continuación, encontrará una breve introducción a los diferentes pasos que se lleve a Azure AD automatizar aprovisionamiento.

1. Cuando se habilita el aprovisionamiento de una aplicación por primera vez, se realizan las siguientes acciones:
 - Azure AD intentará hacer coincidir todos los usuarios existentes en la aplicación de SaaS con sus identidades correspondientes en el directorio. Cuando se asocia a un usuario, que son *no* habilita automáticamente para el inicio de sesión único. Fin de un usuario tenga acceso a la aplicación, deben explícitamente asignarse a la aplicación en Azure AD directa o a través de la pertenencia a grupos.
 - Si ya ha especificado qué usuarios deben asignarse a la aplicación y se produce un error de Azure AD buscar cuentas existentes para esos usuarios, Azure AD aprovisionar nuevas cuentas para ellos en la aplicación.
2. Cuando se haya completado la sincronización inicial, como se describió anteriormente, Azure AD deberá comprobar cada 10 minutos para que los cambios siguientes:
 - Si se han asignado nuevos usuarios a la aplicación (directamente o a través de la pertenencia a grupos), a continuación, estarán aprovisiona una nueva cuenta en la aplicación de SaaS.
 - Si se ha quitado el acceso de un usuario, su cuenta en la aplicación de SaaS se marcará como deshabilitada (los usuarios son totalmente nunca eliminados, que protege contra la pérdida de datos si se produce un error de configuración).
 - Si un usuario recientemente se ha asignado a la aplicación y ya tenían una cuenta en la aplicación de SaaS, esa cuenta se marcará como habilitado y algunas propiedades de usuario pueden actualizarse si están actualizadas en comparación con el directorio.
 - Si se ha cambiado la información de un usuario (por ejemplo, el número de teléfono, ubicación de la oficina, etcetera) en el directorio, esta información también se actualizarán en la aplicación de SaaS.

Para obtener más información sobre cómo se asignan los atributos entre Azure AD y la aplicación de SaaS, consulte el artículo sobre la [Personalización de asignaciones de atributos](active-directory-saas-customizing-attribute-mappings.md).

##<a name="list-of-apps-that-support-automated-user-provisioning"></a>Lista de aplicaciones que son compatibles con aprovisionamiento automatizado usuario

Haga clic en una aplicación para ver un tutorial acerca de cómo configurar aprovisionamiento automatizado para ello:

- [Cuadro](http://go.microsoft.com/fwlink/?LinkId=286016)
- [Citrix GoToMeeting](http://go.microsoft.com/fwlink/?LinkId=309580)
- [Contribuyan](http://go.microsoft.com/fwlink/?LinkId=309575)
- [Docusign](http://go.microsoft.com/fwlink/?LinkId=403254)
- [Dropbox para empresas](http://go.microsoft.com/fwlink/?LinkId=309581)
- [Aplicaciones de Google](http://go.microsoft.com/fwlink/?LinkId=309577)
- [Jive](http://go.microsoft.com/fwlink/?LinkId=309591)
- [Salesforce](http://go.microsoft.com/fwlink/?LinkId=286017)
- [Espacio aislado de Salesforce](http://go.microsoft.com/fwlink/?LinkId=327869)
- [ServiceNow](http://go.microsoft.com/fwlink/?LinkId=309587)
- [Día laborable](http://go.microsoft.com/fwlink/?LinkId=690250) (aprovisionamiento entrante)

En el orden de una aplicación admitir aprovisionamiento automatizado usuario, primero debe proporcionar los extremos es necesarios que permiten a los programas externos automatizar la creación, el mantenimiento y la eliminación de los usuarios. Por lo tanto, no todas las aplicaciones de SaaS son compatibles con esta característica. Para las aplicaciones que son compatibles con esto, el equipo de ingeniería de Azure AD podrá crear un conector de aprovisionamiento a las aplicaciones y este trabajo tiene prioridad por las necesidades de los clientes potenciales y las actuales.

Para ponerse en contacto con el equipo de ingeniería de Azure AD para solicitar soporte de aprovisionamiento de aplicaciones adicionales, envíe un mensaje en el [foro de comentarios de Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

##<a name="related-articles"></a>Artículos relacionados

- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
- [Personalizar las asignaciones de atributo para el aprovisionamiento de usuario](active-directory-saas-customizing-attribute-mappings.md)
- [Escritura de expresiones para asignaciones de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Especificar el ámbito de filtros para el aprovisionamiento de usuario](active-directory-saas-scoping-filters.md)
- [Con SCIM para habilitar el aprovisionamiento automático de usuarios y grupos de Azure Active Directory para las aplicaciones](active-directory-scim-provisioning.md)
- [Notificaciones de aprovisionamiento de la cuenta](active-directory-saas-account-provisioning-notifications.md)
- [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS](active-directory-saas-tutorial-list.md)
