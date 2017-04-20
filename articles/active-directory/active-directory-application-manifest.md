<properties
   pageTitle="Comprender el manifiesto de aplicación de Azure Active Directory | Microsoft Azure"
   description="Cobertura detallada del manifiesto de aplicación de Azure Active Directory, que representa la configuración de identidad de la aplicación en un inquilino de Azure AD y se usa para facilitar la autorización de OAuth, consentimiento experiencia y mucho más."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="dkershaw;bryanla"/>

# <a name="understanding-the-azure-active-directory-application-manifest"></a>Comprender el manifiesto de aplicación de Azure Active Directory

Aplicaciones que se integran con Azure Active Directory (AD) deben estar registradas con un inquilino de Azure AD, lo que proporciona una configuración de identidad persistente para la aplicación. Esta configuración se consulta en tiempo de ejecución permite escenarios que permiten una aplicación subcontratación e intermediario autenticación/autorización a través de Azure AD. Para obtener más información acerca del modelo de aplicación de Azure AD, vea [Agregar, actualizar y quitar una aplicación] [ ADD-UPD-RMV-APP] artículo.

## <a name="updating-an-applications-identity-configuration"></a>Actualizar la configuración de identidad de la aplicación

Hay varias opciones realmente disponibles para actualizar las propiedades de configuración de identidad de la aplicación, que varían en funciones y los grados de dificultad, incluidos los siguientes:

- La ** [Azure portal clásica] [ AZURE-CLASSIC-PORTAL] interfaz de usuario Web** permite actualizar las propiedades más comunes de una aplicación. La forma más rápida y menos error susceptible de actualización de propiedades de la aplicación, pero no le ofrecen acceso completo a todas las propiedades, como los siguientes dos métodos.
- Escenarios más avanzados donde deberá actualizar propiedades que no se exponen en el portal de clásico Azure, puede modificar la **aplicación de manifiestos**. Este es el enfoque de este artículo y se explica con más detalle a partir de la siguiente sección.
- También es posible **escribir una aplicación que utiliza la [API de gráfico] [ GRAPH-API] ** actualizar la aplicación, que requiere más esfuerzo. Esto puede ser una opción atractiva aunque, si escribe el software de administración, o necesita actualizar propiedades de la aplicación de forma regular de forma automática.

## <a name="using-the-application-manifest-to-update-an-applications-identity-configuration"></a>Usar el manifiesto de aplicación para actualizar la configuración de identidad de la aplicación
A través del [portal clásica Azure][AZURE-CLASSIC-PORTAL], puede administrar la configuración de identidad de la aplicación, descargar y cargar un JSON archivo representación, que se denomina un manifiesto de aplicación. Ningún archivo real se almacena en el directorio. El manifiesto de aplicación es simplemente una operación HTTP GET en la entidad de aplicación de la API de Azure AD Graph y la carga es una operación de revisión de HTTP en la entidad de aplicación.

Por tanto, para comprender el formato y las propiedades del manifiesto de aplicación, debe hacer referencia a la API de Graph [entidad de aplicación] [ APPLICATION-ENTITY] documentación. Ejemplos de las actualizaciones que se pueden realizar aunque carga de manifiestos de aplicación:

- **Ámbitos de permiso Declare (oauth2Permissions)** expuestas por la API de web. Vea el tema "Exponer API a otras aplicaciones Web" en [Aplicaciones de integración con Azure Active Directory] [ INTEGRATING-APPLICATIONS-AAD] para obtener información sobre cómo implementar la suplantación de usuarios utilizando la oauth2Permissions delegados ámbito de permisos. Como se mencionó anteriormente, propiedades de la entidad de aplicación se describen en la API de Graph la [entidad y tipo complejo] [ APPLICATION-ENTITY] artículo de referencia, incluyendo la propiedad oauth2Permissions que es una colección de tipo [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION].
- **Funciones de aplicación de Declare (appRoles) expuestas por la aplicación**. Propiedad de appRoles de la entidad de aplicación es una colección de tipo de [función de aplicación][APPLICATION-ENTITY-APP-ROLE]. Ver el [control de acceso en aplicaciones de nube con Azure AD basado en roles] [ RBAC-CLOUD-APPS-AZUREAD] artículo para obtener un ejemplo de implementación.
- **Declare conocidos a cliente aplicaciones (knownClientApplications)**, que le permiten lógicamente el consentimiento de las aplicaciones de cliente especificado en la API de recursos o web.
- **Solicitar Azure AD emitir pertenencias a grupos de notificación de grupo** para el usuario de sesión (groupMembershipClaims).  También puede configurarse para emitir notificaciones sobre los miembros del usuario directorio roles. Vea la [autorización en las aplicaciones de nube con grupos de AD] [ AAD-GROUPS-FOR-AUTHORIZATION] artículo para obtener un ejemplo de implementación.
- **Permitir que su aplicación admita OAuth 2.0 implícitos conceder** flujos (oauth2AllowImplicitFlow). Este tipo de flujo de conceder se utiliza con páginas web de JavaScript incrustadas o aplicaciones de página único (SPA). Para obtener más información sobre la concesión de autorización implícita, vea [comprender el OAuth2 implícito conceder el flujo de Azure Active Directory][IMPLICIT-GRANT].
- **Habilitar el uso de X509 certificados como clave secreta** (keyCredentials). Consulte [crear aplicaciones de servicio y daemon en Office 365] [ O365-SERVICE-DAEMON-APPS] y [Guía del desarrollador de auth con la API del Administrador de recursos de Azure] [ DEV-GUIDE-TO-AUTH-WITH-ARM] artículos para obtener ejemplos de implementación.
- **Agregar un nuevo URI de identificador de aplicación** de la aplicación (identifierURIs[]). URI de ID de aplicación se utilizan para identificar una aplicación dentro de su inquilino de Azure AD (o a través de varios inquilinos de Azure AD, para escenarios de múltiples arrendatarios cuando calificado a través de dominio personalizado comprobado). Se utilizan cuando se solicitan permisos para una aplicación de recursos o para adquirir un token de acceso para una aplicación de recursos. Al actualizar este elemento, la actualización se realiza a la colección de [] servicePrincipalNames de capital de servicio correspondiente, que se encuentra en el inquilino de inicio de la aplicación.

El manifiesto de aplicación también proporciona una buena manera de seguimiento del estado de su registro de aplicación. Ya está disponible en formato JSON, la representación de archivo se puede incorporar el control de código fuente, junto con el código fuente de la aplicación.

## <a name="step-by-step-example"></a>Ejemplo paso a paso
Ahora le permite seguir los pasos necesarios para actualizar la configuración de identidad de la aplicación a través de manifiesto de aplicación. Nos centraremos en uno de los ejemplos anteriores, que muestra cómo declarar un nuevo ámbito de permisos en una aplicación de recursos:

1. Desplácese hasta el [portal clásica Azure] [ AZURE-CLASSIC-PORTAL] e inicie sesión con una cuenta que tiene privilegios de administrador o compañeros de servicio.

2. Una vez que haya autenticado, desplácese hacia abajo y seleccione la extensión de Azure "Active Directory" en el panel de navegación izquierdo (1), haga clic en el inquilino de Azure AD donde la aplicación está había registrado (2).

    ![Seleccione al inquilino de Azure AD][SELECT-AZURE-AD-TENANT]

3. Cuando aparezca la página de directorio, haga clic en "Aplicaciones" (1) en la parte superior de la página para ver una lista de aplicaciones registradas en el inquilino. A continuación, busque la aplicación que desea actualizar en la lista y haga clic en ella (2).

    ![Seleccione al inquilino de Azure AD][SELECT-AZURE-AD-APP]

4. Ahora que ha seleccionado la página principal de la aplicación, observe que la característica "Administrar manifiestos" en la parte inferior de la página (1). Si hace clic en este vínculo, le pedirá que descargue o cargue el archivo de manifiestos JSON. Haga clic en "Descargar manifiesto" (2). Esto seguirán inmediatamente con el cuadro de diálogo de confirmación de descarga que le pide que confirme, haga clic en "Descargar manifiestos" (3), a continuación, puede abrir o guardar el archivo localmente (4).

    ![Administrar el manifiesto, opción de descarga][MANAGE-MANIFEST-DOWNLOAD]

    ![Descargar el manifiesto][DOWNLOAD-MANIFEST]

5. En este ejemplo, hemos guardado el archivo localmente, lo que nos permite abrir en un editor, realizar cambios en el JSON y guarde de nuevo. Aquí es el aspecto de la estructura JSON dentro del editor de JSON de Visual Studio. Observe que sigue el esquema de la [entidad de aplicación] [ APPLICATION-ENTITY] como se mencionó anteriormente:

    ![Actualizar el JSON manifiesto][UPDATE-MANIFEST]

    Por ejemplo, suponiendo que queremos implementar o exponer un permiso nuevo llamado "Employees.Read.All" en nuestra aplicación de recursos (API), puede simplemente agregar un elemento nuevo por segundo a la colección de oauth2Permissions ie:

        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],

    La entrada debe ser única y, por tanto, debe generar una nueva globalmente único identificador (GUID) para la `"id"` propiedad. En este caso, porque se especificó `"type": "User"`, este permiso puede ser aceptado por cualquier cuenta autenticada por Azure AD inquilinos en que se registra la aplicación de recursos y de la API. De este modo, el cliente de aplicación permiso para obtener acceso a él en nombre de la cuenta. Las cadenas de nombre de presentación y descripción se utilizan durante consentimiento y para que se muestre en el portal de clásico de Azure.

6. Cuando haya terminado de actualizar el manifiesto, volver a la página de la aplicación de Azure AD en el portal de clásica Azure y haga clic en la "Administrar manifiestos" característica nuevo (1). Pero esta vez, seleccione la opción "Cargar manifiestos" (2). Similar a la descarga, que se le recordará nuevo con un segundo cuadro de diálogo le pide la ubicación del archivo JSON. Haga clic en "Buscar archivo..." (3), a continuación, use el cuadro de diálogo "Elegir archivo para cargar" para seleccionar el archivo JSON (4) y haga clic en "Abrir". Una vez que el cuadro de diálogo desaparece, seleccione la marca de verificación "Aceptar" (5) y su manifiesto se cargarán.  

    ![Administrar el manifiesto, opciones de carga][MANAGE-MANIFEST-UPLOAD]

    ![Cargar el JSON manifiesto][UPLOAD-MANIFEST]

    ![Cargar el JSON manifiesto - confirmación][UPLOAD-MANIFEST-CONFIRM]

Ahora que se guardó el manifiesto, puede dar un registrados acceso a las aplicaciones cliente para el nuevo permiso se agrega encima. En este momento puede usar Web UI del portal clásico Azure en lugar de editar el manifiesto de la aplicación cliente:  

1. En primer lugar vaya a la página "Configurar" de la aplicación cliente a la que desea agregar a la nueva API de acceso y haga clic en el botón "Agregar aplicación".
2. A continuación, aparecerá la lista de aplicaciones de recursos registrado (API) en el inquilino. Haga clic en el signo más o símbolo + junto a nombre de la aplicación de recursos para seleccionarla.  
3. A continuación, haga clic en la marca de verificación en la esquina inferior derecha.
4. Cuando vuelva a la sección "Agregar aplicación" de la página de configuración de su cliente, verá la nueva aplicación de recursos en la lista. Si desplace el puntero sobre la sección "Permisos de delegado" a la derecha de dicha fila, verá una lista desplegable mostrarse. Haga clic en la lista, seleccione el nuevo permiso para agregar a la lista del cliente solicitado de permisos. Nota: este permiso nuevo se almacenarán en configuración de identidad de la aplicación cliente, en la propiedad de la colección de "requiredResourceAccess".

![Permisos a otras aplicaciones][PERMS-TO-OTHER-APPS]

![Permisos a otras aplicaciones][PERMS-SELECT-APP]

![Permisos a otras aplicaciones][PERMS-SELECT-PERMS]

Eso es todo. Ahora las aplicaciones se ejecutarán utilizando su nueva configuración de identidad.

## <a name="next-steps"></a>Pasos siguientes
- Para obtener más detalles sobre la relación entre objetos de aplicación y servicio Principal de la aplicación, vea [aplicación y objetos principales de servicio en Azure AD][AAD-APP-OBJECTS].
- Consulte el [Glosario de desarrollador de Azure AD] [ AAD-DEVELOPER-GLOSSARY] para las definiciones de algunos de los conceptos de desarrollador de Azure Active Directory (AD) principales.

Utilice la sección de comentarios DISQUS siguiente para proporcionar comentarios y ayudar a perfeccionar y dar forma a nuestro contenido.

<!--Image references-->
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[PERMS-SELECT-APP]: ./media/active-directory-application-manifest/portal-perms-select-app.png
[PERMS-SELECT-PERMS]: ./media/active-directory-application-manifest/portal-perms-select-perms.png
[PERMS-TO-OTHER-APPS]: ./media/active-directory-application-manifest/portal-perms-to-other-apps.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

