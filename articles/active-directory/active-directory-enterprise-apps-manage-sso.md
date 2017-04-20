<properties
    pageTitle="Un solo inicio de sesión de administración de aplicaciones empresariales en la vista previa de Azure Active Directory | Microsoft Azure"
    description="Obtenga información sobre cómo administrar el inicio de sesión único en de aplicaciones empresariales con Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="asmalser"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/30/2016"
    ms.author="asmalser"/>

# <a name="preview-managing-single-sign-on-for-enterprise-apps-in-the-new-azure-portal"></a>Vista previa: Administración de inicio de sesión único para las aplicaciones de la empresa en el nuevo portal de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](active-directory-enterprise-apps-manage-sso.md)
- [Portal de clásico de Azure](active-directory-sso-integrate-saas-apps.md)

Este artículo describe cómo usar el [portal de Azure](https://portal.azure.com) para administrar la configuración de inicio de sesión único para las aplicaciones, especialmente en los que se han agregado desde la [Galería de la aplicación de Azure Active Directory (AD Azure)](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). La experiencia de administración de Azure AD para el inicio de sesión único está en vista previa público y, en este artículo se describe las características nuevas, así como algunas limitaciones temporales que estarán en su lugar sólo durante el período de vista previa. [¿Qué es la vista previa?](active-directory-preview-explainer.md)

##<a name="finding-your-apps-in-the-new-portal"></a>Buscar sus aplicaciones en el nuevo portal

A partir de septiembre de 2016, todas las aplicaciones que se han configurado para un solo inicio de sesión en un directorio, un administrador del directorio utilizando la [Galería de la aplicación de Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) dentro del [portal clásica Azure](https://manage.windowsazure.com), pueden ahora ver y administrar en el portal de Azure.

Estas aplicaciones se pueden encontrar en la sección **Aplicaciones empresariales** del portal de Azure, un vínculo a la que se puede encontrar en la lista de **Servicios más** en el [portal](https://portal.azure.com). Aplicaciones empresariales son aplicaciones que se han implementado y se utiliza por los usuarios de su organización.

![Módulo de aplicaciones empresariales][1]

Seleccione **todas las aplicaciones** para ver una lista de todas las aplicaciones que se han configurado, incluidas las aplicaciones que se han añadido desde la galería. Seleccionar una aplicación de carga del módulo de recursos para esa aplicación, donde pueden ver los informes de esa aplicación y pueden administrarse diversas opciones de configuración.

Para administrar la configuración de inicio de sesión único, seleccione **Inicio de sesión único**.

![Módulo de recursos de aplicación][2]


##<a name="single-sign-on-modes"></a>Modos de inicio de sesión único

El módulo de **Inicio de sesión único** comienza con un menú de **modo** que permite el modo de inicio de sesión único estén configurados. Las opciones disponibles son:

* **Basada en SAML inicio de sesión** : esta opción está disponible si la aplicación admite completa federado inicio de sesión único con Azure Active Directory mediante el protocolo de SAML 2.0.

* **Basado en la contraseña de inicio de sesión** : esta opción está disponible si Azure AD admite rellenar para esta aplicación de formulario de contraseña.

* **Inicio de sesión vinculado** - anteriormente conocida como "Existente inicio de sesión único", esta opción permite a los administradores colocar un vínculo a esta aplicación en el iniciador de aplicaciones de Panel de Azure AD acceso u Office 365 de su usuario.

Para obtener más información acerca de estos modos, vea [cómo un solo inicio de sesión con el trabajo de Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).


##<a name="saml-based-sign-on"></a>Basada en SAML inicio de sesión

La opción de **Inicio de sesión basada en SAML en** muestra un módulo que se divide en cuatro secciones:

###<a name="domains-and-urls"></a>Dominios y direcciones URL
Esto es donde todos los detalles sobre el dominio y las direcciones URL de la aplicación se agregan al directorio de Azure AD. Se muestran todas las entradas necesarias para realizar el trabajo de inicio de sesión único aplicación directamente en la pantalla, mientras que se pueden ver todas las entradas opcionales activando la casilla de verificación **Mostrar avanzadas de configuración de la dirección URL** . La lista completa de entradas admitidos incluye:

* **Inicio de sesión en la dirección URL** : la donde el usuario va a iniciar sesión en esta aplicación. Si la aplicación está configurada para llevar a cabo servicios iniciados por el proveedor solo inicio de sesión, a continuación, cuando un usuario se desplaza a esta dirección URL, el proveedor de servicios no la redirección necesaria para Azure AD para autenticar y el inicio de sesión del usuario. Si este campo se rellena, Azure AD usará esta dirección URL para iniciar la aplicación de Office 365 y el Panel de Azure AD acceso. Si se omite este campo, Azure AD realiza en su lugar el proveedor de identidades-inicio de sesión iniciada cuando se inicia la aplicación de Office 365, el Panel de Azure AD Access o de Azure AD un solo inicio de sesión en la dirección URL.

* **Identificador** - este URI debe identificar la aplicación para que solo inicio de sesión se está configurando. Este es el valor que Azure AD se envía a la aplicación como el parámetro de la audiencia del token SAML y la aplicación se espera para validarla. Este valor también aparece como el ID de entidad en los metadatos SAML proporcionado por la aplicación.

* **Dirección URL de respuesta** : la dirección URL de respuesta es donde la aplicación espera recibir el token de SAML. También se conoce como la dirección URL del servicio de consumidores de aserción (ACS). Después de que se han introducido, haga clic en siguiente para pasar a la siguiente pantalla. Esta pantalla proporciona información sobre lo que debe estar configurado en el lado de la aplicación para que pueda aceptar un símbolo SAML de Azure AD.

* **Estado de retransmisión** - el estado de retransmisión es un parámetro opcional que puede ayudar a indicar dónde puede redirigir al usuario una vez completada la autenticación de la aplicación. Normalmente, el valor es una dirección URL válida en la aplicación, pero algunas aplicaciones utilizan este campo de forma diferente (consulte inicio de sesión único de la aplicación en la documentación para obtener información detallada). La capacidad para establecer el estado de retransmisión es una nueva característica exclusiva para el nuevo portal de Azure.

###<a name="user-attributes"></a>Atributos de usuario
Esto es donde los administradores pueden ver y editar los atributos que se envían en el token SAML Azure AD problemas con la aplicación los usuarios de tiempo iniciar sesión.

En la primera versión de vista previa, el atributo editable solo admitido es el atributo de **Identificador de usuario** . El valor de este atributo es el campo en Azure AD que identifica inequívocamente cada usuario dentro de la aplicación. Por ejemplo, si la aplicación se ha implementado usando la dirección de correo electrónico de"" como el nombre de usuario y el identificador único, se podría establecer el valor en el campo "user.mail" en Azure AD.

Edición de atributos adicionales se admitirá en una vista previa de las siguientes.

###<a name="saml-signing-certificate"></a>Certificado de firma de SAML
Esta sección muestra los detalles del certificado que Azure AD se usa para firmar los tokens SAML que se emiten a la aplicación cada vez que el usuario se autentica. Permite que las propiedades del certificado actual que se inspeccionen, incluida la fecha de expiración.

La capacidad de sustitución el certificado y administrar certificado adicionales se admitirá en una versión preliminar de las siguientes opciones. Tenga en cuenta que total de la administración de certificados aún se puede realizar en el [portal de clásico de Azure](active-directory-sso-certs.md).

###<a name="application-configuration"></a>Configuración de la aplicación
La sección final proporciona la documentación y controles necesarios para configurar la aplicación para usar Azure Active Directory como un proveedor de identidades.

El menú emergente **Configurar aplicación** proporciona nuevas concisas, incrustadas las instrucciones para configurar la aplicación. Esta es otra característica nueva único para el nuevo portal de Azure.

> [AZURE.NOTE] Para obtener un ejemplo completo de documentación incrustado, vea la aplicación de Salesforce.com. Documentación de otras aplicaciones se va a agregar continuamente durante la vista previa.

![Documentos incrustados][3]

##<a name="password-based-sign-on"></a>Basado en la contraseña de inicio de sesión
Si se admiten para la aplicación, seleccionando el modo SSO basada en contraseñas y **Guardar** al instante configura para hacer SSO basado en contraseña. Para obtener más información sobre cómo implementar SSO basado en contraseña, vea [cómo un solo inicio de sesión con el trabajo de Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Basado en la contraseña de inicio de sesión][4]


##<a name="linked-sign-on"></a>Inicio de sesión vinculado
Si se admiten para la aplicación, seleccionar el modo SSO vinculado le permite escriba la dirección URL que desee en el Panel de acceso de Azure AD u Office 365 para redirigir a cuando los usuarios haga clic en esta aplicación. Para obtener más información acerca de SSO vinculado (anteriormente conocida como "SSO existente"), vea [cómo un solo inicio de sesión con el trabajo de Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![El inicio de sesión vinculado][5]

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG
