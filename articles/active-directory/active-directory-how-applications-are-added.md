<properties
   pageTitle="¿Cómo se agregan aplicaciones para Azure Active Directory."
   description="Este artículo describe cómo se agregan las aplicaciones a una instancia de Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="shoatman"
   manager="kbrint"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="02/09/2016"
      ms.author="shoatman"/>

# <a name="how-and-why-applications-are-added-to-azure-ad"></a>¿Cómo y por qué aplicaciones se agregan a Azure AD

Una de las cosas inicialmente desconcertantes al ver una lista de aplicaciones en la instancia de Azure Active Directory es comprender dónde proceden las aplicaciones y por qué ¿hay.  En este artículo le proporcionará una visión general de alto nivel de cómo las aplicaciones están representadas en el directorio y proporcionarán un contexto que le ayudarán a comprender cómo se ha suministrado una aplicación en el directorio.

## <a name="what-services-does-azure-ad-provide-to-applications"></a>¿Qué servicios proporciona Azure AD a las aplicaciones?

Aplicaciones se agregan a Azure AD aprovechar uno o varios de los servicios que proporciona.  Estos servicios incluyen:

* Autorización y la autenticación de la aplicación
* Autorización y autenticación de usuario
* Inicio de sesión único (SSO) mediante contraseña o la federación
* Aprovisionamiento de usuario y la sincronización
* Control de acceso basado en roles; Utilizar el directorio para definir las funciones de la aplicación para realizar funciones según comprobaciones de autorización en una aplicación.
* Servicios de autorización de oAuth (usados por Office 365 y otras aplicaciones de Microsoft para autorizar el acceso a la API o recursos).
* Publicación de aplicaciones y proxy. Publicar una aplicación desde una red privada a internet

## <a name="how-are-applications-represented-in-the-directory"></a>¿Cómo se representan las aplicaciones en el directorio?

Aplicaciones están representadas en Azure AD con 2 objetos: un objeto application y un objeto principal de servicio.  Hay un objeto de aplicación, registrado en una "casa" o "propietario" o "publicación" directorio y, a continuación, uno o más objetos principales que representa la aplicación en cada directorio en el que actúa de servicio.  

El objeto de aplicación describe la aplicación de Azure AD (el servicio de múltiples arrendatario) y puede incluir cualquiera de las siguientes acciones: (*Nota*: no es una lista exhaustiva.)

* Nombre, logotipo y Publisher
* Información confidencial (teclas simétricas y asimétricas, utilizadas para autenticar la aplicación)
* Dependencias de API (oAuth)
* API/recursos/ámbitos publicados (oAuth)
* Roles (RBAC) de aplicación
* Metadatos SSO y configuración (SSO)
* Aprovisionamiento de metadatos y la configuración de usuario
* Configuración y metadatos de proxy

Servicio principal es un registro de la aplicación en todos los directorios, donde la aplicación actúa incluidos su directorio de inicio.  Servicio principal:

* Hace referencia a un objeto de la aplicación a través de la propiedad de id de aplicación
* Usuario local de registros y agrupar las asignaciones de roles de la aplicación
* Registros de concedido permisos de usuario y administrador locales a la aplicación
    * Por ejemplo: permisos para la aplicación tener acceso a un correo electrónico de los usuarios determinada
* Directivas de registros locales incluida la directiva de acceso condicional
* Configuración local alternativa de local de registros para una aplicación
    * Reglas de transformación de notificaciones
    * Asignaciones de atributo (aprovisionamiento de usuario)
    * Funciones de aplicación concreta de inquilinos (si la aplicación admite funciones personalizadas)
    * Nombre o logotipo

### <a name="a-diagram-of-application-objects-and-service-principals-across-directories"></a>Un diagrama de objetos de la aplicación y principales de servicio en directorios

![Un diagrama que ilustra cómo objetos de aplicación y principales existentes con instancias de Azure AD del servicio.][apps_service_principals_directory]

Como puede ver en el diagrama anterior.  Microsoft mantiene dos directorios internamente (a la izquierda) usa para publicar aplicaciones.

* Uno para Microsoft Apps (directorio de servicios de Microsoft)
* Uno para previamente 3ª parte las aplicaciones integradas (directorio de la Galería de aplicaciones)

Editores y proveedores de aplicaciones que se integre con Azure AD deben tener un directorio de publicación.  (Algunos directorio SAAS).

Las aplicaciones que usted agregue incluyen:

* Aplicaciones que ha desarrollado (integradas con AAD)
* Aplicaciones conectado de sesión único
* Aplicaciones que ha publicado con el proxy de aplicación de Azure AD.

### <a name="a-couple-of-notes-and-exceptions"></a>Un par de notas y excepciones

* No todas las identidades de servicio señalen a objetos de la aplicación.  ¿No? Cuando se creó inicialmente Azure AD los servicios que proporciona a las aplicaciones ha sido mucho más limitados y servicio principal era suficiente para establecer una identidad de aplicación.  El servicio original principal era más cerca de la forma a la cuenta de servicio de Active Directory de Windows Server.  Por este motivo está siendo posible crear a principales de servicio con Azure AD PowerShell sin crear primero un objeto de la aplicación.  La API de Graph requiere un objeto de la aplicación antes de crear un servicio principal.
* Actualmente no toda la información que se indica más arriba se expone mediante programación.  La siguiente solo está disponible en la interfaz de usuario:
    * Reglas de transformación de notificaciones
    * Asignaciones de atributo (aprovisionamiento de usuario)
* Para obtener más información detallada sobre el capital de servicio y objetos de la aplicación, consulte la documentación de referencia de la API de REST de Azure AD Graph.  *Sugerencia*: documentación de la API de gráfico de la Azure AD es lo más cercano a una referencia de esquema para Azure AD está disponible actualmente.  
    * [Aplicación](https://msdn.microsoft.com/library/azure/dn151677.aspx)
    * [Servicio Principal](https://msdn.microsoft.com/library/azure/dn194452.aspx)


## <a name="how-are-apps-added-to-my-azure-ad-instance"></a>¿Cómo se agregan las aplicaciones a la instancia de Azure AD?
Hay muchas formas que se puede agregar una aplicación a Azure AD:

* Agregar una aplicación de la [Galería de aplicación de Azure Active Directory](https://azure.microsoft.com/updates/azure-active-directory-over-1000-apps/)
* Inicio de sesión arriba o en un 3 º fiesta aplicación integrada con Azure Active Directory (por ejemplo: [Smartsheet](https://app.smartsheet.com/b/home) o [DocuSign](https://www.docusign.net/member/MemberLogin.aspx))
    * Durante el inicio de sesión arriba o en los usuarios le pedirá que conceder permiso a la aplicación para tener acceso a su perfil y otros permisos.  La primera persona que dar consentimiento provoca un principal de servicio que representa la aplicación se agregue al directorio.
* Inicie sesión arriba o en los servicios en línea de Microsoft como [Office 365](http://products.office.com/)
    * Al suscribirse a Office 365 o comenzar una prueba una o varias entidades de servicio se crean en el directorio de los distintos servicios que se usan para ofrecer toda la funcionalidad asociada a Office 365.
    * Algunos servicios de Office 365 como SharePoint crear a principales de servicio de forma continua para permitir la comunicación segura entre los componentes de los flujos de trabajo.
* Agregar una aplicación que está creando en el Portal de administración de Azure, consulte: https://msdn.microsoft.com/library/azure/dn132599.aspx
* Agregar una aplicación que desarrolla utilizando Visual Studio Vea:
    * [Métodos de autenticación de ASP.Net](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)
    * [Servicios conectados](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)
* Agregar una aplicación para utilizar el [Proxy de aplicación de Azure AD](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Conectar una aplicación de inicio de sesión único sobre el uso de SAML o SSO de contraseña
* Muchas otras incluye diversas experiencias de desarrollador de Azure y o en API explorer experimenta a través de los centros de desarrollo

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>¿Quién tiene permiso para agregar aplicaciones a la instancia de Azure AD?

Solo los administradores globales pueden:

* Agregar aplicaciones desde la Galería de aplicación de Azure AD (aplicaciones previamente integradas de parte 3)
* Publicar una aplicación mediante el Proxy de aplicación de Azure AD

Todos los usuarios en el directorio tienen derechos para agregar las aplicaciones que desarrollan y discreción qué aplicaciones compartir y dar acceso a sus datos de la organización.  *Recuerde arriba o en el inicio de sesión de usuario a una aplicación y concesión de permisos puede traducirse en una entidad de seguridad de servicio se ha creado.*

Este es posible que inicialmente sonido relativas, pero mantener los siguientes en cuenta:

* Aplicaciones han sido capaces aprovechar Active Directory de Windows Server para la autenticación de usuario para muchos años sin necesidad de la aplicación esté registrado o se registra en el directorio.  Ahora la organización habrá mejorado exactamente cuántas aplicaciones están usando el directorio y para qué la visibilidad.
* Sin necesidad de administración condicionada por el proceso de publicación o registro de la aplicación.  Con los servicios de federación de Active Directory era probable que tenía un administrador agregar una aplicación como un usuario de confianza en nombre de los desarrolladores.  Ahora los desarrolladores pueden autoservicio.
* Los usuarios iniciar sesión en/hasta aplicaciones con sus cuentas de organización fines empresariales es bueno.  Si posteriormente abandonan la organización se pierde el acceso a su cuenta en la aplicación que estaban utilizando.
* Tener un registro de los datos que se ha compartido con la aplicación está bueno.  Los datos son más transportables que nunca y tener un registro de quién comparte los datos con las aplicaciones que borrar es útil.
* Aplicaciones que usan Azure AD para oAuth decidir exactamente qué permisos que los usuarios pueden conceder a aplicaciones y que requieren de permisos de administrador a Aceptar.  Debería ir sin que le indica que solo los administradores pueden acepta ámbitos mayores y permisos más importantes.
* Los usuarios agregar y permitir aplicaciones acceso a sus datos son auditarán eventos para que pueda ver los informes de auditoría en el portal de administración de Azure para determinar cómo se haya agregado una aplicación en el directorio.

**Nota:** *Microsoft ha estado funcionando con la configuración predeterminada para el número de meses ahora.*

Con todo lo dice es posible evitar que los usuarios en el directorio agregar aplicaciones y de ejercer discreción sobre qué información comparten con aplicaciones modificando la configuración del directorio en el portal de administración de Azure.  El portal de administración de Azure en la pestaña de "Configurar" de su directorio puede tener acceso a la siguiente configuración.

![Captura de pantalla de la interfaz de usuario para configurar las opciones de la aplicación integrada][app_settings]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo agregar aplicaciones a Azure AD y cómo configurar los servicios de aplicaciones.

* Los desarrolladores: [Obtenga información sobre cómo integrar una aplicación con AAD](https://msdn.microsoft.com/library/azure/dn151122.aspx)
* Desarrolladores: el [código de ejemplo de revisión para las aplicaciones integradas con Azure Active Directory en Github](https://github.com/AzureADSamples)
* Los programadores y los profesionales de TI: [Revisar la documentación de la API de REST de la API de gráfico de Azure Active Directory](https://msdn.microsoft.com/library/azure/hh974478.aspx)
* Los profesionales de TI: [Aprenda a usar aplicaciones previamente integradas de Azure Active Directory de la Galería de aplicación](https://msdn.microsoft.com/library/azure/dn308590.aspx)
* Los profesionales de TI: [Buscar tutoriales para configurar las aplicaciones previamente integradas específicas](https://msdn.microsoft.com/library/azure/dn893637.aspx)
* Los profesionales de TI: [Obtenga información sobre cómo publicar una aplicación mediante el Proxy de aplicación de Azure Active Directory](https://msdn.microsoft.com/library/azure/dn768219.aspx)

## <a name="see-also"></a>Vea también

- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)

<!--Image references-->
[apps_service_principals_directory]:media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
[app_settings]:media/active-directory-how-applications-are-added/IntegratedAppSettings.jpg
