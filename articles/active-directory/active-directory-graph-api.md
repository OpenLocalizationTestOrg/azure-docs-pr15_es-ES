<properties
   pageTitle="Gráfico de Azure Active Directory API | Microsoft Azure"
   description="Una guía de introducción y tutorial rápido para la API de gráfico que permite acceso mediante programación a Azure AD a través de extremos de la API de REST."
   services="active-directory"
   documentationCenter=""
   authors="PatAltimore"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin" />

# <a name="azure-active-directory-graph-api"></a>Gráfico de Azure Active Directory API

> [AZURE.IMPORTANT] Funcionalidad de Azure AD Graph API también está disponible a través de [Microsoft Graph](https://graph.microsoft.io/), una API unificada que incluye las API de otros servicios de Microsoft, como Outlook, OneDrive, OneNote, organizador y Office Graph, accesible a través de un solo extremo y con un token de acceso único.

La API de gráfico de Azure Active Directory proporciona acceso mediante programación a Azure AD a través de extremos de la API de REST. Aplicaciones pueden utilizar la API de gráfico para realizar crear, leer, actualizar y eliminar operaciones (CRUD) en los datos de directorio y objetos. Por ejemplo, la API de Graph admite las siguientes operaciones comunes de un objeto de usuario:

- Crear un nuevo usuario en un directorio

- Obtener propiedades detallada de un usuario, como sus grupos

- Actualizar las propiedades de un usuario, como su ubicación y el número de teléfono, o cambiar su contraseña

- Comprobar la pertenencia a grupos de un usuario para el acceso basado en roles

- Deshabilitar una cuenta de usuario o eliminarlo por completo

Además de objetos de usuario, puede realizar operaciones similares en otros objetos, como los grupos y las aplicaciones. Para llamar a la API de gráfico en un directorio, la aplicación debe estar registrada con Azure AD y estar configurada para permitir el acceso al directorio. Normalmente, esto se logra a través de un flujo de consentimiento de usuario o de administrador.

Para empezar a usar la API de gráfico de Azure Active Directory, consulte la [Guía de inicio rápido de API de gráfico](active-directory-graph-api-quickstart.md)o ver la [documentación de referencia de las API de gráfico interactiva](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).


## <a name="features"></a>Características

La API de Graph proporciona las siguientes características:

- **Extremos de la API de REST**: la API de gráfico es un servicio REST consta de los extremos que se accede mediante solicitudes HTTP estándar. La API de gráfico es compatible con los tipos de contenido XML o notación de objetos Javascript (JSON) para las convocatorias y respuestas. Para obtener más información, vea [referencia de API de REST de Azure AD gráfico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- **Autenticación con Azure AD**: cada solicitud a la API del gráfico se debe autenticar agregando un Token para la Web de JSON (JWT) en el encabezado de autorización de la solicitud. Este token se adquiere al realizar una solicitud al extremo de token de Azure AD y proporcionar credenciales válidas. Puede usar el flujo de credenciales de cliente de OAuth 2.0 o el código de autorización conceder flujo para adquirir un símbolo para el gráfico de llamadas. Para obtener más información, [OAuth 2.0 en Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

- **Autorización basado en roles (RBAC)**: los grupos de seguridad se utilizan para realizar RBAC en la API de gráfico. Por ejemplo, si desea determinar si un usuario tiene acceso a un recurso específico, la aplicación puede llamar a la operación de [Revisar la pertenencia a grupos (transitivos)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#FunctionsandactionsongroupsCheckmembershipinaspecificgrouptransitive) , que devuelve true o false.

- **Consulta diferencial**: si desea comprobar los cambios en un directorio entre dos períodos de tiempo sin tener que realizar consultas frecuentes en la API de gráfico, puede realizar una solicitud de consulta diferencial. Este tipo de solicitud devolverá solo los cambios realizados entre la solicitud de consulta diferencial anterior y la solicitud actual. Para obtener más información, vea [Azure AD Graph API diferencial consulta](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).

- **Extensiones de directorio**: si va a desarrollar una aplicación que necesita para leer o escribir propiedades únicas para objetos de directorio, puede registrar y utilizar los valores de extensión mediante la API de gráfico. Por ejemplo, si la aplicación requiere una propiedad identificador de Skype para cada usuario, puede registrar la nueva propiedad en el directorio y estará disponible en cada objeto de usuario. Para obtener más información, vea [extensiones de esquema de directorio de Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).

- **Segura por los ámbitos de permiso**: AAD Graph API expone ámbitos de permiso que habilitar el acceso seguro de aceptado a los datos AAD y admiten una gran variedad de tipos de aplicación de cliente, incluidos:
 - aquellos con una interfaz de usuario que reciben delegan acceso a datos a través de la autorización del usuario ha iniciado sesión en (delegados)
  - las que utilizan aplicación-definen control de acceso basado en roles como los clientes de servicio o daemon (funciones de aplicación)

    Los delegados y ámbitos de permiso de rol de aplicación representan privilegio expuesto por la API de gráfico y aplicaciones cliente a través de permisos de registro de aplicación [características en el portal de clásica Azure](https://manage.windowsazure.com)pueden solicitar. Los clientes pueden comprobar los ámbitos de permiso concedido a ellas mediante la inspección de la notificación de ámbito ("scp") recibida en el token de acceso para permisos de delegado y reclamación la funciones ("") para permisos de la función de aplicación. Más información sobre los [ámbitos de permiso de la API de Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes).


## <a name="scenarios"></a>Escenarios

La API de Graph permite muchos escenarios de aplicación. Las situaciones siguientes son las más comunes:

- **Aplicación de línea de negocio (solo inquilino)**: en este escenario, un desarrollador de la empresa funciona para una organización que disponga de una suscripción de Office 365. El desarrollador está creando una aplicación web que interactúa con Azure AD para realizar tareas como asignar una licencia a un usuario. Esta tarea requiere acceso a la API de gráfico, por lo que los registros de programador la única aplicación en Azure AD de inquilinos y configura los permisos lectura y escritura para la API de gráfico. A continuación, la aplicación está configurada para usar sus propias credenciales o las del usuario actualmente sesión para adquirir un símbolo para llamar a la API de gráfico.

- **Software como una aplicación de servicio (múltiples inquilino)**: en este escenario, un proveedor de software independiente (ISV) es desarrollo de aplicación web de múltiples usuarios alojados que proporciona características de administración de usuario de otras organizaciones que usan Azure AD. Estas características requieren acceso a objetos de directorio y, por tanto, debe llamar a la API de gráfico de la aplicación. El programador registra la aplicación en Azure AD, configura para requerir la lectura y permisos de escritura para la API de gráfico y, a continuación, permite el acceso externo para que otras organizaciones pueden consentimiento para utilizar la aplicación en su directorio. Cuando un usuario de otra organización autentica a la aplicación por primera vez, se muestran un cuadro de diálogo de consentimiento con los permisos que solicita la aplicación.  Otorgar consentimiento luego dará la aplicación solicitan los permisos a la API de gráfico en el directorio del usuario. Para obtener más información sobre el marco de trabajo de consentimiento, vea [información general sobre el marco consentimiento](active-directory-integrating-applications.md).

## <a name="see-also"></a>Vea también

[Guía de inicio rápido de Azure AD Graph API](active-directory-graph-api-quickstart.md)

[Documentación del resto del gráfico de AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Guía del desarrollador de Azure Active Directory](active-directory-developers-guide.md)
