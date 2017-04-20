<properties
   pageTitle="Integración de aplicaciones con Azure Active Directory | Microsoft Azure"
   description="Obtener más información sobre cómo agregar, actualizar o quitar una aplicación en Azure Active Directory (AD Azure)."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/29/2016"
   ms.author="mbaldwin;bryanla" />

# <a name="integrating-applications-with-azure-active-directory"></a>Integración de aplicaciones con Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Los desarrolladores empresariales y proveedores de software como servicio (SaaS) pueden desarrollar servicios de nube comercial o línea de aplicaciones empresariales que se pueden integrar con Azure Active Directory (AD Azure) para proporcionar el inicio de sesión seguro y autorización para sus servicios. Para integrar una aplicación o servicio con Azure AD, un desarrollador debe registrar los detalles sobre su aplicación con Azure AD a través del portal clásico Azure.

En este artículo se muestra cómo agregar, actualizar o quitar una aplicación en Azure AD. Obtendrá información sobre los distintos tipos de aplicaciones que se pueden integrar con Azure AD, cómo configurar las aplicaciones para tener acceso a otros recursos como web API y mucho más.

Para obtener más información acerca de los dos objetos de Azure AD que representan una aplicación registrada y la relación entre ellas, consulte [objetos de la aplicación y principales de servicio](active-directory-application-objects.md); Para obtener más información sobre las directrices de personalización de marca que debe usar al desarrollar aplicaciones con Azure Active Directory, vea [Personalización de marca las instrucciones para las aplicaciones integradas.](active-directory-branding-guidelines.md)

## <a name="adding-an-application"></a>Agregar una aplicación

Cualquier aplicación que desee usar las funciones de Azure AD primero debe registrarse en un inquilino de Azure AD. Este proceso de registro implica con Azure AD detalles sobre la aplicación, como la dirección URL de dónde se encuentra, la dirección URL para enviar respuestas después de que un usuario se autentica, el URI que identifica la aplicación y así sucesivamente.

Si desea crear una aplicación web que solo debe admitir el inicio de sesión para usuarios de Azure AD, simplemente puede seguir las instrucciones a continuación. Si su aplicación necesita credenciales o permisos para tener acceso a un sitio web API o debe permitir a los usuarios de otros inquilinos de Azure AD para tener acceso, vea la sección [actualizar una aplicación](#updating-an-application) para continuar la configuración de la aplicación.

### <a name="to-register-a-new-application-in-the-azure-classic-portal"></a>Para registrar una nueva aplicación en el portal de clásico de Azure

1. Inicie sesión en el [portal de clásico de Azure](https://manage.windowsazure.com).

1. Haga clic en el icono de Active Directory en el menú de la izquierda y, a continuación, haga clic en el directorio deseado.

1. En el menú superior, haga clic en **aplicaciones**. Si no hay aplicaciones se han agregado al directorio, esta página solo mostrará el agregar un vínculo de aplicación. Haga clic en el vínculo o, como alternativa, puede hacer clic en el botón **Agregar** en la barra de comandos.

1. En el cuadro ¿Qué desea de página, haga clic en el vínculo Agregar **una aplicación se desarrolla mi organización**.

1. En la información nos acerca de la página de la aplicación, debe especificar un nombre para la aplicación así como indicar el tipo de aplicación que se va a registrar con Azure AD.  Puede elegir desde un [cliente de la aplicación web](active-directory-dev-glossary.md#client-application) / aplicación[API de recursos de web](active-directory-dev-glossary.md#resource-server) (también podría funcionar como ambos) o una aplicación de [cliente nativa](active-directory-dev-glossary.md#native-client) . Una vez que haya terminado, haga clic en el icono de flecha en la esquina inferior derecha de la página.

1. En la página de propiedades de la aplicación, proporcionar la dirección URL de inicio de sesión y aplicación identificador URI si va a registrar una aplicación web o simplemente la redirección URI para una aplicación nativa de cliente y luego haga clic en la casilla de verificación en la esquina inferior derecha de la página.

1. Se ha agregado la aplicación y, a continuación, se le dirigirá a la página de inicio rápido de la aplicación. Dependiendo de si la aplicación es un sitio web o aplicación nativa, verá opciones diferentes sobre cómo agregar funciones adicionales a la aplicación. Una vez agregada la aplicación, puede empezar la actualización de la aplicación para que los usuarios puedan iniciar sesión en web de access API en otras aplicaciones, configurar aplicación múltiples arrendatario (que permite a otras organizaciones tener acceso a la aplicación).

>[AZURE.NOTE] De forma predeterminada, el registro de aplicación recién creado está configurado para permitir que los usuarios desde el directorio de iniciar sesión en la aplicación.

## <a name="updating-an-application"></a>Actualizar una aplicación

Una vez que la aplicación se ha registrado con Azure AD, puede que necesite actualizar para proporcionar acceso a web API, están disponibles en otras organizaciones y mucho más. Esta sección describen varias maneras en que tendrá que configurar la aplicación más. En primer lugar se iniciará con una descripción general de Framework consentimiento, que es importante conocer si se crean aplicaciones de recursos y de la API que se utiliza en aplicaciones cliente creadas por los desarrolladores de su organización u otra organización.

Para obtener más información sobre la forma de autenticación en Azure AD, consulte [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md).

### <a name="overview-of-the-consent-framework"></a>Información general sobre el marco de trabajo de consentimiento

Marco de consentimiento de Azure AD facilita la desarrollar varios inquilino web y aplicaciones cliente nativo que necesitan tener acceso a web API protegidas por un inquilino de Azure AD diferente del que se registra la aplicación cliente. Estas web API incluyen la API de gráfico, Office 365 y otros servicios de Microsoft, además de su propia API de web. El marco de trabajo se basa en un usuario o un administrador dar su consentimiento a la aplicación que le pregunta registrar en su directorio, que puede implicar el acceso a los datos de directorio.

Por ejemplo, si una aplicación de cliente de web necesita llamar a una aplicación API o recurso para leer la información de calendario sobre el usuario web de Office 365, ese usuario deberán consentimiento a la aplicación cliente. Después de dar su consentimiento, la aplicación cliente podrá llamar a la API de web de Office 365 en nombre del usuario y use la información de calendario según sea necesario.

El marco de trabajo de consentimiento se basa en OAuth 2.0 y sus flujos de diversos, como concesión autorización código cliente y conceder las credenciales, con clientes públicos o confidenciales. Usando OAuth 2.0, Azure AD permite crear muchos tipos diferentes de aplicaciones cliente, como en un teléfono, tableta, servidor o una aplicación web, y obtener acceso a los recursos necesarios.

Para obtener información más detallada sobre el marco de trabajo de consentimiento, vea [OAuth 2.0 en Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx), [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md)y tema de Office 365 [Descripción de la autenticación con la API de Office 365](https://msdn.microsoft.com/office/office365/howto/common-app-authentication-tasks).

#### <a name="example-of-the-consent-experience"></a>Ejemplo de la experiencia de consentimiento

Los siguientes pasos le mostrará cómo el consentimiento experimentar funciona para el desarrollador de la aplicación y el usuario.

1. En la página de configuración de su cliente de la aplicación web en el portal de clásica Azure, establezca los permisos que requiere la aplicación mediante el uso de los menús desplegables en los permisos de control de otras aplicaciones.

    ![Permisos a otras aplicaciones](./media/active-directory-integrating-applications/permissions.png)

1. Considere la posibilidad de que se han actualizado los permisos de la aplicación, la aplicación se está ejecutando y es un usuario va a utilizar por primera vez. Si la aplicación no se ha adquirido una acceso o actualizar token, la aplicación que necesita ir al extremo de autorización de Azure AD para obtener un código de autorización que se pueden usar para adquirir un nuevo acceso y actualizar token.

1. Si el usuario ya no está autenticado, le pedirá que inicie sesión en Azure AD.

    ![Usuario o administrador de iniciar sesión en Azure AD](./media/active-directory-integrating-applications/useradminsignin.png)

1. Una vez el usuario ha iniciado sesión, Azure AD determinará si el usuario debe mostrarse en una página de su consentimiento. Esta determinación se basa en si el usuario (o el Administrador de su organización) ya ha concedido la autorización de la aplicación. Si ya no se ha concedido consentimiento, Azure AD le solicitará al usuario su consentimiento y mostrará los permisos necesarios que necesite para funcionar. El conjunto de permisos que se muestra en el cuadro de diálogo de consentimiento es la misma que la selección realizada en los permisos a otro control de aplicaciones en el portal de clásico de Azure.

    ![Experiencia de usuario de consentimiento](./media/active-directory-integrating-applications/userconsent.png)

1. Después de que el usuario concede consentimiento, se devuelve un código de autorización para la aplicación, que puede canjear para adquirir un símbolo de acceso y actualizar el token. Para obtener más información sobre este flujo, consulte la sección de [aplicación web de la sección de la API de web](active-directory-authentication-scenarios.md#web-application-to-web-api) en [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md).

### <a name="configuring-a-client-application-to-access-web-apis"></a>Configurar una aplicación de cliente para tener acceso a las API de web

En orden para una aplicación web o confidencial de cliente poder participar en un flujo de concesión de autorización que requiere autenticación (y obtener un token de acceso), deben establecer las credenciales de seguridad. El método de autenticación predeterminado compatible con el portal de Azure es el identificador de cliente + clave simétrica. Esta sección cubrirá los pasos de configuración necesarios para proporcionar la clave de credenciales de su cliente.

Además, antes un cliente acceso una API web expuestas por una aplicación de recursos (ie: API de Azure AD Graph), el marco de trabajo de consentimiento asegurará que el cliente obtiene la concesión de permisos necesaria, en función de los permisos solicitados. De forma predeterminada, todas las aplicaciones pueden elegir permisos de Azure Active Directory (Graph API) y las API de administración de servicio de Azure, con el permiso de Azure AD "Habilitar inicio de sesión y perfil de usuario de lectura" ya está seleccionado de forma predeterminada. Si la aplicación cliente registrada en un inquilino de Azure AD de Office 365, web API y los permisos de SharePoint y Exchange Online también estará disponibles para su selección. Puede seleccionar entre [dos tipos de permisos](active-directory-dev-glossary.md#permissions) en los menús de lista desplegable situada junto a la API de web que desee:

- Permisos de aplicación: La aplicación cliente debe tener acceso a la API de web directamente como Sí (sin contexto de usuario). Este tipo de permiso requiere consentimiento del administrador y no está disponible para las aplicaciones cliente nativo también.

- Permisos de delegado: La aplicación cliente debe acceder a la API de web como el usuario que ha iniciado sesión, pero con acceso limitado el permiso seleccionado. Un usuario puede conceder este tipo de permiso a menos que el permiso está configurado como que requieren consentimiento del administrador.

#### <a name="to-add-credentials-or-permissions-to-access-web-apis"></a>Para agregar credenciales o permisos de acceso a web API

1. Inicie sesión en la [Azure portal clásico.](https://manage.windowsazure.com)

1. Haga clic en el icono de Active Directory en el menú de la izquierda y luego haga clic en el directorio deseado.

1. En el menú superior, haga clic en **aplicaciones**y, a continuación, haga clic en la aplicación que desea configurar. Aparecerá la página de inicio rápido con el inicio de sesión único y otra información de configuración. Haga clic en el vínculo **Configurar** en la parte superior de la página, que le llevará a la página de configuración de la aplicación.

1. Para agregar una clave secreta las credenciales de la aplicación web, desplácese hacia abajo hasta la sección "Claves".  

    - Primero haga clic en "Seleccionar duración" lista desplegable y seleccione un 1 o 2 años de duración. 
    - A continuación, se muestra una nueva fila, con las fechas de "Caduca en" que se rellena y "Válida desde".
    - La columna del extremo derecho contendrá el valor de clave después de guardar los cambios de configuración (abajo). Asegúrese de que vuelva a esta sección y copie después de presionar guardar, por lo que tendrá para su uso en la aplicación cliente durante la autenticación en tiempo de ejecución.

2. Para agregar permisos para tener acceso a la API de recursos de cliente, desplácese hacia abajo hasta la sección "Permisos a otras aplicaciones". 
    - En primer lugar, haga clic en el botón "Agregar aplicación".
    - Use la lista desplegable "Mostrar" para seleccionar el tipo de recursos que desea elegir de.
    - La primera columna permite seleccionar desde las aplicaciones de recursos disponibles en el directorio que exponen una API de web. Haga clic en el recurso que le interese, haga clic en la marca de verificación en la esquina inferior derecha.
    - Una vez seleccionado, verá el recurso agregado a la lista "Permisos a otras aplicaciones".
    - Con las listas desplegables "Permisos" y "Delegar permisos", seleccione los permisos que desee para la aplicación de cliente.

1. Cuando haya terminado, haga clic en el botón **Guardar** en la barra de comandos. Si especifica una clave de la aplicación, también genera después de hacer clic en Guardar.

>[AZURE.NOTE] Haciendo clic en el botón Guardar automáticamente establece los permisos para la aplicación en el directorio según los permisos a otras aplicaciones que ha configurado.  Puede ver estos permisos de aplicación consultando la ficha de propiedades de la aplicación.

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configurar una aplicación de recursos para exponer web API

Puede desarrollar un web API y que esté disponible para las aplicaciones cliente al exponer acceso [ámbitos](active-directory-dev-glossary.md#scopes) y [funciones](active-directory-dev-glossary.md#roles). API de web correctamente configurado estará disponible como otro web Microsoft API, incluidas las API de gráfico y las API de Office 365. Ámbitos de access y funciones se exponen a través de su [manifiesto de la aplicación](active-directory-dev-glossary.md#application-manifest), que es un archivo JSON que representa la configuración de identidad de la aplicación.  

En la sección siguiente le mostrará cómo exponer ámbitos de access, modificando manifiesto de la aplicación de recursos.

#### <a name="adding-access-scopes-to-your-resource-application"></a>Agregar ámbitos de acceso a la aplicación de recursos

1. Inicie sesión en el [portal de clásico de Azure](https://manage.windowsazure.com).

1. Haga clic en el icono de Active Directory en el menú de la izquierda y luego haga clic en el directorio deseado.

1. En el menú superior, haga clic en **aplicaciones**y, a continuación, haga clic en la aplicación de recursos que desea configurar. Aparecerá la página de inicio rápido con el inicio de sesión único y otra información de configuración.

1. Haga clic en el botón **Administrar manifiestos** en la barra de comandos y seleccione **Descargar manifiesto**.

1. Abra el archivo de manifiestos de aplicación de JSON y reemplace el nodo "oauth2Permissions" con el siguiente fragmento JSON. Este fragmento de código es un ejemplo de cómo exponer un ámbito conocido como "suplantación de usuario", que permite que el propietario de un recurso dar a una aplicación cliente de un tipo de acceso delegado a un recurso. Asegúrese de cambiar el texto y los valores para su propia aplicación:

        "oauth2Permissions": [
        {
            "adminConsentDescription": "Allow the application full access to the Todo List service on behalf of the signed-in   user",
            "adminConsentDisplayName": "Have full access to the Todo List service",
            "id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
            "isEnabled": true,
            "type": "User",
            "userConsentDescription": "Allow the application full access to the todo service on your behalf",
            "userConsentDisplayName": "Have full access to the todo service",
            "value": "user_impersonation"
            }
        ],

    El valor del identificador debe ser un GUID generado nuevos que cree mediante una [herramienta de generación de GUID](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) o mediante programación. Representa un identificador único para el permiso que se expone por la API de web. Una vez que el cliente está configurado correctamente para solicitar acceso a su web API y llamadas de la API de web, presentará un símbolo de OAuth 2.0 JWT que tiene la notificación de ámbito (scp) anteriores, el valor que en este caso es user_impersonation.

    >[AZURE.NOTE] Puede exponer los ámbitos adicionales más tarde según sea necesarios. Considere la posibilidad de que su web API puede exponer varios ámbitos asociados a una gran variedad de funciones diferentes. Ahora puede controlar el acceso a la API de web mediante el uso de la notificación de ámbito (scp) en el token de OAuth 2.0 JWT recibido.

1. Guarde el archivo actualizado de JSON y cargue, haciendo clic en el botón **Administrar manifiestos** en la barra de comandos, seleccione **cargar manifiesto**, buscando el archivo actualizado manifiesto y, a continuación, selecciónela. Una vez cargado, la API de web ahora está configurada para usarlo con otras aplicaciones en el directorio.

#### <a name="to-verify-the-web-api-is-exposed-to-other-applications-in-your-directory"></a>Para comprobar la web API se expone a otras aplicaciones en el directorio

1. En el menú superior, haga clic en **aplicaciones**, seleccione la aplicación de cliente que desee que desea configurar el acceso a la API de web y, a continuación, haga clic en **Configurar**.

1. Desplácese hacia abajo hasta la sección permisos a otra aplicaciones. Haga clic en el menú de lista desplegable Seleccione aplicación y podrá seleccionar la API que solo exponen un permiso para web. En el menú desplegable de permisos de delegado, seleccione el permiso nuevo.

![Se muestran los permisos de lista de tareas pendientes](./media/active-directory-integrating-applications/listpermissions.png)

#### <a name="more-on-the-application-manifest"></a>Obtener más información sobre el manifiesto de aplicación
El manifiesto de aplicación realmente sirve como mecanismo de actualización de la entidad de aplicación, que define todos los atributos de configuración de identidad de la aplicación de Azure AD, incluidos los ámbitos de access API tratamos. Para obtener más información sobre la entidad de aplicación, consulte la [documentación de entidad de aplicación de la API de gráfico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). En él, encontrará información de referencia completa de los miembros de la entidad de aplicación permite especificar los permisos de la API:  

- el miembro appRoles, que es una colección de entidades de [función de aplicación](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) que se pueden usar para definir los **Permisos de aplicación** para una API de web  
- el miembro oauth2Permissions, que es una colección de entidades [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) que se pueden usar para definir los **Permisos de delegado** para una API de web

Para obtener más información sobre la aplicación de conceptos manifiestos en general, consulte [Descripción de manifiesto de aplicación de Azure Active Directory](active-directory-application-manifest.md).

### <a name="accessing-the-azure-ad-graph-and-office-365-apis"></a>Obtener acceso a las API de Office 365 y gráfico de Azure AD

Como se mencionó anteriormente, además de exponer/acceso API en sus propias aplicaciones de recursos, también puede actualizar la aplicación cliente para tener acceso a la API expuestas por los recursos de Microsoft.  La API de Azure AD gráfico, que se denomina "Azure Active Directory" en la lista de permisos a otras aplicaciones, está disponible de forma predeterminada para todas las aplicaciones que se registran con Azure AD. Si va a registrar la aplicación de cliente en un inquilino de Azure AD aprovisionada por Office 365, puede tener acceso todos los permisos expuestos por las API de diversos recursos de Office 365.

Para obtener más información sobre los ámbitos de access expuestas por:  

- Gráfico de Azure AD API, consulte los ámbitos [permiso | Gráfico de los conceptos de API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) artículo.
- API de Office 365, vea el artículo de [autenticación y la autorización mediante el marco de consentimiento común](https://msdn.microsoft.com/office/office365/howto/application-manifest) . Consulte [Configurar el entorno de desarrollo de Office 365](https://msdn.microsoft.com/office/office365/HowTo/setup-development-environment) para la discusión mayor acerca de cómo crear una aplicación de cliente que se integra con la API de Office 365.

>[AZURE.NOTE] Debido a una limitación actual, aplicaciones cliente nativo sólo pueden llamar a la API de Azure AD gráfico si utilizan el permiso "Acceso de directorio de su organización".  Esta restricción no se aplica para las aplicaciones web.

### <a name="configuring-multi-tenant-applications"></a>Configurar aplicaciones de múltiples arrendatarios

Al agregar una aplicación para Azure AD, puede que desee su aplicación para tener acceso a los usuarios de su organización. Como alternativa, puede que prefiera la aplicación para tener acceso a los usuarios de las organizaciones externas. Estos tipos de dos aplicación se denominan inquilino único y aplicaciones de múltiples usuarios. Puede modificar la configuración de una aplicación de inquilinos único para que sea una aplicación de múltiples arrendatario, esta sección se describen a continuación.

Es importante que tenga en cuenta las diferencias entre una aplicación de múltiples inquilino y solo inquilino:  

- Una aplicación de inquilinos solo está pensada para su uso en una organización. Normalmente son una aplicación (LoB) de línea de negocio escrita por un desarrollador de la empresa. Una aplicación de inquilinos único solo necesita tener acceso a los usuarios en un directorio y por tanto, solo necesita estén configurados en un directorio.
- Una aplicación de múltiples arrendatario está pensada para su uso en muchas organizaciones. Son una aplicación de software como servicio (SaaS) web suele escrita un proveedor de software independiente (ISV). Aplicaciones de múltiples arrendatarios necesitan estén configurados en cada directorio donde se van a utilizar, lo que requiere el usuario o el administrador consentimiento para registrar ellos compatible a través del marco de consentimiento de Azure AD. Tenga en cuenta que todas las aplicaciones cliente nativo varios inquilinos de forma predeterminada como están instalados en el dispositivo del propietario de recursos. Vea la información general de la sección de consentimiento marco anterior para obtener más detalles en el marco de trabajo de consentimiento.

#### <a name="enabling-external-users-to-grant-your-application-access-to-their-resources"></a>Permitir a los usuarios externos conceder a su aplicación de acceso a sus recursos

Si está escribiendo una aplicación que desee que esté disponible para los clientes o socios externos a su organización, debe actualizar la definición de aplicación en el portal de clásico de Azure.

>[AZURE.NOTE] Al habilitar a varios inquilinos, debe asegurarse de que pertenece URI de identificador de aplicación de la aplicación en un dominio comprobado. Además, la URL devuelta debe comenzar con https://. Para obtener más información, vea [objetos de la aplicación y Principal del servicio](active-directory-application-objects.md).

Para habilitar el acceso a la aplicación para los usuarios externos: 

1. Inicie sesión en la [Azure portal clásico.](https://manage.windowsazure.com)

1. Haga clic en el icono de Active Directory en el menú de la izquierda y luego haga clic en el directorio deseado.

1. En el menú superior, haga clic en **aplicaciones**y, a continuación, haga clic en la aplicación que desea configurar. Aparecerá la página de inicio rápido con opciones de configuración.

1. Expanda la sección **Configurar la aplicación de varios inquilinos** de la barra de inicio rápido, haga clic en el vínculo **Configurar ahora** en la sección Habilitar el acceso. Aparecerá la página de propiedades de la aplicación.

1. Haga clic en el botón **Sí** junto a la aplicación es múltiple inquilino y luego haga clic en el botón **Guardar** en la barra de comandos.

Una vez haya realizado el cambio anterior, los usuarios y administradores de otras organizaciones podrán conceder a su aplicación de acceso a su directorio y otros datos.

#### <a name="triggering-the-azure-ad-consent-framework-at-runtime"></a>Activar el marco de trabajo de Azure AD consentimiento en tiempo de ejecución

Para usar el marco de trabajo de consentimiento, las aplicaciones cliente de múltiples arrendatarios deben solicitar autorización mediante OAuth 2.0. [Ejemplos de código](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant) están disponibles para mostrar cómo una aplicación web, la aplicación nativa o la aplicación de servidor o daemon solicita códigos de autorización y símbolos de acceso a web API de llamadas.

La aplicación web también puede ofrecer una experiencia de suscripción para los usuarios. Si ofrece una experiencia de suscripción, se espera que el usuario se haga clic en un botón suscribirme que se redirigir el explorador a la OAuth2.0 de Azure AD autorizar extremo o un extremo de información del usuario OpenID conectarse. Estos extremos permiten la aplicación obtener información sobre el nuevo usuario mediante la inspección de la id_token. Después de la fase de suscripción se presentará al usuario con la indicación consentimiento similar a la que se muestra en la visión general de la sección consentimiento Framework.

Como alternativa, la aplicación web también puede ofrecer una experiencia que permite a los administradores "registrarse mi compañía". Este proceso también redirija al usuario a la OAuth de Azure AD 2.0 autorizar extremo. En este caso, se pasa una solicitud de confirmación = parámetro admin_consent al extremo autorizar obligar a la experiencia de consentimiento del administrador, donde el administrador concederá consentimiento en nombre de su organización. Un usuario se autentica con una cuenta que pertenezca a la función de administrador Global puede proporcionar consentimiento; otros usuarios recibirán un error. En el consentimiento correcta, la respuesta contendrá admin_consent = true. Cuando se canjean un token de acceso, también recibirá un id_token que le proporcionará información sobre la organización y el administrador que dispone de la aplicación.

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Habilitar OAuth 2.0 implícitos conceder solo aplicaciones de página

Solo página aplicación (SPAs) normalmente están estructurada con un front-end visible de JavaScript que se ejecuta en el explorador, que devuelve la llamada web de la aplicación API final para llevar a cabo su lógica empresarial. Para SPAs alojados en Azure AD, use OAuth 2.0 implícitos conceder para autenticar al usuario con Azure AD y obtener un símbolo que puede usar para llamadas seguras desde el cliente de JavaScript de la aplicación a su web API de back-end. Después de que el usuario ha concedido el consentimiento, este mismo protocolo de autenticación puede utilizarse para obtener tokens para proteger llamadas entre el cliente y otro web recursos API configurados para la aplicación. Para obtener más información acerca de la autorización implícita conceder y le ayudará a decidir si es adecuado para la aplicación, vea la [Descripción del flujo de conceder implícitos OAuth2 de Azure Active Directory ](active-directory-dev-understanding-oauth2-implicit-grant.md).

De forma predeterminada, OAuth 2.0 implícito conceder está deshabilitado para las aplicaciones. Puede habilitar OAuth 2.0 implícitos conceder para su aplicación estableciendo la `oauth2AllowImplicitFlow`"' valor en su [aplicación de manifiestos](active-directory-application-manifest.md), que es un archivo JSON que representa la configuración de identidad de la aplicación.

#### <a name="to-enable-oauth-20-implicit-grant"></a>Para habilitar conceder implícito de OAuth 2.0 

1. Inicie sesión en la [Azure portal clásico.](https://manage.windowsazure.com)
1. Haga clic en el icono de **Active Directory** en el menú de la izquierda y luego haga clic en el directorio deseado.
1. En el menú superior, haga clic en **aplicaciones**y, a continuación, haga clic en la aplicación que desea configurar. Aparecerá la página de inicio rápido con el inicio de sesión único y otra información de configuración.
1. Haga clic en el botón **Administrar manifiestos** en la barra de comandos y seleccione **Descargar manifiesto**.
Abra el archivo de manifiestos de aplicación de JSON y establezca el valor de "oauth2AllowImplicitFlow" a "true". De forma predeterminada, es "false".

    `"oauth2AllowImplicitFlow": true,`

1. Guarde el archivo actualizado de JSON y cargue, haciendo clic en el botón **Administrar manifiestos** en la barra de comandos, seleccione **cargar manifiesto**, buscando el archivo actualizado manifiesto y, a continuación, selecciónela. Una vez cargado, su web API ahora está configurado para usar OAuth 2.0 implícitos conceder autenticar a los usuarios.


### <a name="legacy-experiences-for-granting-access"></a>Experiencias heredados para conceder acceso

Esta sección describen la experiencia de consentimiento heredados antes del 12 de marzo de 2014. Esta experiencia aún se admite y se describe a continuación. Antes de la nueva funcionalidad, solo puede conceder los siguientes permisos:

- Inicie sesión en los usuarios de su organización

- Inicie sesión en usuarios y leer los datos del directorio de la organización (como la aplicación solo)

- Inicie sesión en usuarios y leer y escribir datos del directorio de la organización (como la aplicación solo)

Puede seguir los pasos en el [Desarrollo de aplicaciones Web de múltiples arrendatarios con Azure AD](https://msdn.microsoft.com/library/azure/dn151789.aspx) para conceder acceso para las nuevas aplicaciones registrado en Azure AD. Es importante que tenga en cuenta que el nuevo marco consentimiento permite aplicaciones mucho más eficaces y también permite a los usuarios consentimiento a estas aplicaciones en lugar de los administradores solo.

#### <a name="building-the-link-that-grants-access-for-external-users-legacy"></a>Crear el vínculo que conceda acceso a usuarios externos (heredado)

En orden para usuarios externos a iniciar sesión en su aplicación con sus cuentas de la organización, debe actualizar su aplicación para mostrar un botón que se vincula a la página en Azure AD que les permite conceder acceso. Personalización de marca pautas para este botón suscribirme se describe en el tema de [Personalización de marca directrices para aplicaciones integradas](active-directory-branding-guidelines.md) . Después de que el usuario concede o rechaza el acceso, la página de Azure AD conceder acceso le redirigirá el explorador vuelva a la aplicación con una respuesta. Para obtener más información sobre las propiedades de la aplicación, consulte [principios de servicio y objetos de la aplicación](active-directory-application-objects.md).

Se crea la página de acceso a conceder Azure AD y puede encontrar un vínculo a él en la página de configuración de la aplicación en el portal de clásico de Azure. Para ir a la página de configuración, haga clic en el vínculo de **aplicaciones** en el menú de su inquilino de Azure AD superior, haga clic en la aplicación que desea configurar, a continuación, haga clic en **Configurar** en el menú superior de la página de inicio rápido.

El vínculo de la aplicación tendrá este aspecto: `http://account.activedirectory.windowsazure.com/Consent.aspx?ClientID=058eb9b2-4f49-4850-9b78-469e3176e247&RequestedPermissions=DirectoryReaders&ConsentReturnURL=https%3A%2F%2Fadatum.com%2FExpenseReport.aspx%3FContextId%3D123456`. La siguiente tabla describe las partes del vínculo:

|Parámetro|Descripción|
|---|---|
|ClientId|Obligatorio. Identificador de cliente obtenidos como parte de la adición de la aplicación.|
|RequestedPermissions|Opcional. Nivel de acceso que solicita la aplicación, que se mostrará al usuario conceder el acceso de la aplicación. Si no se especifica el nivel de acceso solicitado se predeterminado para el inicio de sesión único solo. Las otras opciones son DirectoryReaders y DirectoryWriters. Para obtener más detalles sobre estos niveles de acceso, vea niveles de acceso de la aplicación.|
|ConsentReturnUrl|Opcional. La dirección URL que desea que la respuesta de acceso conceder devuelta al. Este valor debe ser la URL codificada y debe estar en el mismo dominio como la dirección URL de respuesta configurado en la definición de una aplicación. Si no se proporciona la respuesta de conceder acceso le redirigirá a la dirección URL de respuesta configurada.|

Especificar un ConsentReturnUrl independiente de la dirección URL de respuesta le permitirá su aplicación implementar la lógica independiente que puede procesar la respuesta en una dirección URL diferente de la dirección URL de respuesta (que se procesa normalmente tokens SAML para iniciar sesión en). También puede especificar parámetros adicionales en el ConsentReturnURL codificación URL; estos se pasará como parámetros de cadena de consulta a su aplicación tras la redirección.  Este mecanismo puede utilizarse para conservar información adicional o para asociar la solicitud de la aplicación para una concesión de acceso a la respuesta de Azure AD.

#### <a name="grant-access-user-experience-and-response-legacy"></a>Conceder acceso a experiencia del usuario y la respuesta (heredado)

Cuando una aplicación se redirige al vínculo de conceder acceso, las siguientes imágenes muestran lo que experimentará el usuario.

Si el usuario no se ha iniciado sesión, se le pedirá que lo haga:

![Inicie sesión en AAD](./media/active-directory-integrating-applications/signintoaad.png)

Una vez que el usuario se autentica, Azure AD redirigirá al usuario a la página de acceso conceder:

![Conceder acceso](./media/active-directory-integrating-applications/grantaccess.png)

>[AZURE.NOTE] Solo el Administrador de la compañía de la organización externa puede conceder acceso a la aplicación. Si el usuario no es un administrador de la compañía, se le dará la opción para enviar correo a su administrador de la compañía para solicitar que esta aplicación se conceda acceso.

Después de que el cliente ha concedido el acceso de la aplicación haciendo clic en conceder acceso, o si ha denegado el acceso, haga clic en Cancelar, Azure AD envía una respuesta a la ConsentReturnUrl o a la dirección URL de respuesta configurada. Esta respuesta contiene los parámetros siguientes:

|Parámetro|Descripción|
|---|---|
|TenantId|Identificador exclusivo de la organización en Azure AD que ha concedido acceso de la aplicación. Este parámetro solo podrá especificar si el cliente le ha concedido acceso.|
|Consentimiento|El valor se establecerá a concedido si la aplicación se ha concedido acceso o denegado si se rechaza la solicitud.|

Si se especificaron como parte de la ConsentReturnUrl codificado dirección URL, se devolverá parámetros adicionales a la aplicación. La siguiente es una respuesta de ejemplo para una solicitud de acceso conceder que indica que se ha autorizado la aplicación e incluye un ID de contexto que se ha proporcionado en la solicitud de acceso conceder: `https://adatum.com/ExpenseReport.aspx?ContextID=123456&Consent=Granted&TenantId=f03dcba3-d693-47ad-9983-011650e64134`.

>[AZURE.NOTE] La respuesta de conceder acceso no contendrá un token de seguridad para el usuario; la aplicación debe iniciar sesión el usuario por separado.

La siguiente es una respuesta de ejemplo a una solicitud de concesión de acceso denegada.`https://adatum.com/ExpenseReport.aspx?ContextID=123456&Consent=Denied`

#### <a name="rolling-app-keys-for-uninterrupted-access-to-the-graph-api-legacy"></a>Desplazamiento de las teclas de aplicación para el acceso sin interrupciones a la API de gráfico (heredado)

Durante la duración de la aplicación, debe cambiar las teclas que utiliza al llamar a Azure AD para adquirir un token de acceso para llamar a la API de gráfico.  Cambiar, en general, las claves se divide en dos categorías: rollo de emergencia sobre dónde se ha comprometido la clave o un rollo sobre cuando la clave actual está a punto de expirar. El siguiente procedimiento para proporcionar acceso sin interrupciones a la aplicación mientras actualizar las claves (principalmente para el segundo caso).

1. En el portal de Azure clásico, haga clic en el inquilino de directorio, haga clic en **aplicaciones** en el menú superior, haga clic en la aplicación que desea configurar. Aparecerá la página de inicio rápido con el inicio de sesión único y otra información de configuración.

1. Haga clic en **Configurar** en el menú superior para ver una lista de propiedades de la aplicación y verá una lista de las claves.

1. En teclas, haga clic en la lista desplegable dice **Seleccione duración** y elija 1 o 2 años y, a continuación, haga clic en **Guardar** en la barra de comandos. Esto genera una nueva clave de contraseña para la aplicación. Copie esta nueva clave de contraseña. En este momento tanto la clave nueva y existente puede ser usada por la aplicación para obtener un token de acceso de Azure AD.

1. Vuelva a la aplicación y actualizar la configuración para empezar a usar la nueva clave de contraseña. Para obtener un ejemplo de dónde debe ocurrir esta actualización, consulte [uso de la API de gráfico para consultar Azure AD](https://msdn.microsoft.com/library/azure/dn151791.aspx) .

1. Ahora debe dar este cambio a conocer a través de su entorno de producción comprobando primero en el nodo de un servicio, antes de implementar por el resto.

1. Una vez que se completa la actualización a través de la implementación de producción, está libre para volver al portal clásico Azure y quitar la clave antigua.

#### <a name="changing-app-properties-after-enabling-access-legacy"></a>Cambiar las propiedades de la aplicación después de habilitar el acceso (heredado)

Después de habilitar el acceso de usuarios externos a su aplicación, puede seguir realizar cambios en las propiedades de la aplicación en el portal de clásico de Azure. Sin embargo, los clientes que ya se han concedido acceso a la aplicación antes de que se han realizado cambios de aplicación no podrá ver los cambios reflejados al ver los detalles sobre la aplicación en el portal de clásico de Azure. Una vez que la aplicación estará disponible para los clientes, debe ser muy cuidado cuando se realicen cambios. Por ejemplo, si actualiza el URI de ID de aplicación, a continuación, los clientes existentes que le ha concedido acceso antes de este cambio será no se puede iniciar sesión en su aplicación con sus cuentas de empresa o escuela.

Si realiza un cambio en el RequestedPermissions para solicitar un nivel de acceso mayor, los clientes existentes mediante la funcionalidad de la aplicación que ahora aprovecha llamadas API nuevas con este aumento nivel de acceso puede obtener una respuesta de acceso denegado de la API de gráfico.  La aplicación debería controlar estos casos y preguntar al cliente para conceder acceso a la aplicación con la solicitud de un nivel de acceso mayor.

## <a name="removing-an-application"></a>Quitar una aplicación

Esta sección describe cómo quitar una aplicación de su inquilino de Azure AD.

### <a name="removing-an-application-authored-by-your-organization"></a>Quitar una aplicación creada por su organización
Estas son las aplicaciones que se muestran en el filtro "Posee aplicaciones de mi compañía" en la página principal de "Aplicaciones" de su inquilino de Azure AD. En términos técnicos, son aplicaciones registradas manualmente a través de Azure clásica, o mediante programación a través de PowerShell o la API de gráfico. Más concretamente, se representan mediante un objeto de aplicación y servicio Principal de su inquilino. Para obtener más información, vea [objetos de la aplicación y Principal del servicio](active-directory-application-objects.md) .

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Para quitar una aplicación de inquilinos solo desde el directorio

1. Inicie sesión en la [Azure portal clásico.](https://manage.windowsazure.com)

1. Haga clic en el icono de Active Directory en el menú de la izquierda y, a continuación, haga clic en el directorio deseado.

1. En el menú superior, haga clic en **aplicaciones**y, a continuación, haga clic en la aplicación que desea configurar. Aparecerá la página de inicio rápido con el inicio de sesión único y otra información de configuración.

1. Haga clic en el botón **Eliminar** en la barra de comandos.

1. Haga clic en **Sí** en el mensaje de confirmación.

#### <a name="to-remove-a-multi-tenant-application-from-your-directory"></a>Para quitar una aplicación de múltiples arrendatario desde el directorio

1. Inicie sesión en la [Azure portal clásico.](https://manage.windowsazure.com)

1. Haga clic en el icono de Active Directory en el menú de la izquierda y, a continuación, haga clic en el directorio deseado.

1. En el menú superior, haga clic en **aplicaciones**y, a continuación, haga clic en la aplicación que desea configurar. Aparecerá la página de inicio rápido con el inicio de sesión único y otra información de configuración.

1. En la aplicación es la sección de varios inquilino, haga clic en **No**. Esto convierte la aplicación sea único inquilino, pero la aplicación permanecerá en una organización que ya ha aceptado.

1. Haga clic en el botón **Eliminar** en la barra de comandos.

1. Haga clic en **Sí** en el mensaje de confirmación.

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Quitar una aplicación de múltiples arrendatario autorizada por otra organización
Se trata de un subconjunto de las aplicaciones que se muestran en el filtro "Aplicaciones de mi compañía utiliza" en la página principal "aplicaciones" de su inquilino de Azure AD específicamente los que no se muestran en la lista "Aplicaciones de mi compañía posee". En términos técnicos, son aplicaciones de múltiples arrendatarios registradas durante el proceso de consentimiento. Más concretamente, se representan mediante un objeto de Principal de servicio de su inquilino. Para obtener más información, vea [objetos de la aplicación y Principal del servicio](active-directory-application-objects.md) .

Para quitar el acceso de la aplicación de múltiples arrendatarios al directorio (después de que haya concedido consentimiento), el Administrador de la compañía debe tener una suscripción de Azure para quitar el acceso a través del portal clásico Azure. Simplemente vaya a la página de configuración de la aplicación y haga clic en el botón "Administrar el acceso" en la parte inferior. Como alternativa, el Administrador de la compañía puede usar los [Cmdlets de PowerShell de Azure AD](http://go.microsoft.com/fwlink/?LinkId=294151) para quitar el acceso.

## <a name="next-steps"></a>Pasos siguientes

- Vea las [Instrucciones de personalización de marca para las aplicaciones integradas](active-directory-branding-guidelines.md) para obtener sugerencias sobre guía visual de la aplicación.

- Para obtener más detalles sobre la relación entre objetos de aplicación y servicio Principal de la aplicación, vea [objetos de la aplicación y Principal del servicio](active-directory-application-objects.md).

- Para obtener más información acerca de la función reproduce manifiestos de la aplicación, vea [comprender el manifiesto de aplicación de Azure Active Directory](active-directory-application-manifest.md)

- Consulte el [Glosario de desarrollador de Azure AD](active-directory-dev-glossary.md) para las definiciones de algunos de los conceptos de desarrollador de Azure Active Directory (AD) principales.

- Visite la [Guía del desarrollador de Active Directory](active-directory-developers-guide.md) para obtener información general de todos los desarrolladores de contenido relacionado.
