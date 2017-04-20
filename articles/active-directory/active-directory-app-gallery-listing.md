<properties
   pageTitle="Listado de la aplicación en la Galería de la aplicación de Azure Active Directory"
   description="Cómo mostrar una aplicación que admita el inicio de sesión único en la Galería de Azure Active Directory | Microsoft Azure"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>


# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Listado de la aplicación en la Galería de la aplicación de Azure Active Directory

Para mostrar una aplicación que admita el inicio de sesión único con Azure Active Directory en la [Galería de Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/), la aplicación primero debe implementar uno de los siguientes modos de integración:

* **Conectar OpenID** - integración directa con Azure AD usando OpenID conectarse para la autenticación y la API de Azure AD consentimiento para la configuración. Si es la primera vez una integración y su aplicación no es compatible con SAML, es recomendable modo.

* **SAML** : la aplicación ya tiene la capacidad de configurar los proveedores de identidades de terceros mediante el protocolo SAML.

Requisitos de entrada para cada modo están por debajo.

##<a name="openid-connect-integration"></a>OpenID conectar integración

Integrar su aplicación con Azure AD, siguiendo las [instrucciones del desarrollador](active-directory-authentication-scenarios.md). A continuación, complete las siguientes preguntas y enviar a waadpartners@microsoft.com.

* Proporcionar credenciales para una cuenta o el inquilino de prueba con la aplicación que puede ser usada por el equipo de Azure AD para probar la integración.  

* Proporcionar instrucciones sobre cómo puede iniciar sesión y conectar una instancia de Azure AD a la aplicación mediante el [marco de Azure AD consentimiento](active-directory-integrating-applications.md#overview-of-the-consent-framework)del equipo de Azure AD. 

* Proporcione el resto de instrucciones necesaria para que el equipo de Azure AD para probar el inicio de sesión único con la aplicación. 

* Proporcione la información siguiente:

> Nombre de la empresa:
> 
> Sitio Web de la empresa:
> 
> Nombre de la aplicación:
> 
> Descripción de la aplicación (límite de 256 caracteres):
> 
> Sitio Web de aplicación (informativo):
> 
> Sitio Web de soporte de técnico de aplicación o información de contacto:
> 
> Identificador de cliente de la aplicación, como se muestra en los detalles de la aplicación en https://manage.windowsazure.com:
> 
> Dirección URL de suscripción a la aplicación dónde suscribirse a los clientes y o comprar la aplicación:
> 
> Elija hasta tres categorías para su aplicación en la lista (para categorías disponibles, consulte el catálogo de soluciones Azure Active Directory):
> 
> Adjuntar icono pequeño de aplicación (archivo PNG, 45px por 45px, color de fondo sólido):
> 
> Adjuntar icono grande de aplicación (archivo PNG, 215px por 215px, color de fondo sólido):
> 
> Adjuntar el logotipo de la aplicación (archivo PNG, 150px por 122px, color de fondo transparente):

##<a name="saml-integration"></a>Integración de SAML

Cualquier aplicación que admita SAML 2.0 puede integrarse directamente con un inquilino de Azure AD con [estas instrucciones para agregar una aplicación personalizada](active-directory-saas-custom-apps.md). Una vez haya probado que la integración de aplicaciones funciona con Azure AD, envíe la información siguiente para <waadpartners@microsoft.com>.

* Proporcionar credenciales para una cuenta o el inquilino de prueba con la aplicación que puede ser usada por el equipo de Azure AD para probar la integración.  

* Proporcionar los valores de (servicio de consumidor aserción) SAML inicio de sesión en la dirección URL, URL de emisor (Id. de entidad) y dirección URL de respuesta de la aplicación, como se describe [a continuación](active-directory-saas-custom-apps.md). Si suele proporcionar estos valores como parte de un archivo de metadatos SAML, a continuación, envíe también.

* Proporcione una breve descripción de cómo configurar Azure AD como un proveedor de identidades en su aplicación mediante SAML 2.0. Si la aplicación admite configuración Azure AD como un proveedor de identidades mediante un portal de administración de autoservicio, a continuación, asegúrese de las credenciales proporcionadas por encima incluyen la capacidad para realizar esta configuración.

* Proporcione la información siguiente:

> Nombre de la empresa:
> 
> Sitio Web de la empresa:
> 
> Nombre de la aplicación:
> 
> Descripción de la aplicación (límite de 256 caracteres):
> 
> Sitio Web de aplicación (informativo):
> 
> Sitio Web de soporte de técnico de aplicación o información de contacto:
> 
> Dirección URL de suscripción a la aplicación dónde suscribirse a los clientes y o comprar la aplicación:
> 
> Elija hasta tres categorías para su aplicación en la lista (para categorías disponibles, consulte el [Catálogo de soluciones de Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/))):
> 
> Adjuntar icono pequeño de aplicación (archivo PNG, 45px por 45px, color de fondo sólido):
> 
> Adjuntar icono grande de aplicación (archivo PNG, 215px por 215px, color de fondo sólido):
> 
> Adjuntar el logotipo de la aplicación (archivo PNG, 150px por 122px, color de fondo transparente):
