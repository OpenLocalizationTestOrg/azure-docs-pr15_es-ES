<properties
    pageTitle="Azure Active Directory B2C: Información general | Microsoft Azure"
    description="Desarrollar aplicaciones para consumidores con Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-sign-up-and-sign-in-consumers-in-your-applications"></a>Azure Active B2C de directorio: Inicio de sesión y los consumidores en las aplicaciones de inicio de sesión

Azure Active B2C de directorio es una solución de administración de identidades de la nube integral para los consumidores orientación web y aplicaciones móviles. Es un servicio global altamente disponible que escala a cientos de millones de identidades de consumidores. Basado en una plataforma segura de nivel empresarial, Azure Active Directory B2C mantiene las aplicaciones de su empresa y los consumidores protegidos.

En el pasado, los desarrolladores de aplicación que deseaban suscribirse y los consumidores en sus aplicaciones de inicio de sesión hubiera escrito su propio código. Y habría usan sistemas o bases de datos local para almacenar los nombres de usuario y contraseñas. Azure Active B2C de directorio ofrece a los desarrolladores una mejor manera de integrar la administración de identidades consumidor en sus aplicaciones con la Ayuda de una plataforma segura y basada en estándares y un amplio conjunto de directivas extensibles. Cuando utiliza Azure Active Directory B2C, los consumidores pueden registrarse para las aplicaciones con sus cuentas sociales existentes (Facebook, Google, Amazon, LinkedIn) o mediante la creación de nuevas credenciales (dirección de correo electrónico y contraseña, o nombre de usuario y contraseña); llamamos a este últimas "cuentas locales".

## <a name="get-started"></a>Introducción

Crear una aplicación que acepta consumidor registrarse e iniciar sesión en, deberá primero tiene que registrar la aplicación con una B2C de Azure Active Directory inquilinos. Obtener a su propio inquilino mediante los pasos descritos en [crear un inquilino de Azure AD B2C](active-directory-b2c-get-started.md).

Puede escribir la aplicación con el servicio de Azure Active Directory B2C eligiendo enviar mensajes de protocolo directamente, usando [OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-authorization-code-flow) o [Abrir conectar de ID](active-directory-b2c-reference-protocols.md#openid-connect-sign-in-flow), o utilizando nuestras bibliotecas para hacer el trabajo para usted. Elegir la plataforma de favorito en la tabla siguiente y empezar a trabajar.

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## <a name="whats-new"></a>¿Qué novedades

Consulte este sitio con frecuencia para obtener información sobre los futuros cambios en el B2C de Azure Active Directory. También se le tweet sobre las actualizaciones usando @AzureAD.

- Obtenga información acerca de nuestro [marco de directivas extensible](active-directory-b2c-reference-policies.md) y los tipos de directivas que se pueden crear y usar en las aplicaciones.
- Marcador nuestro [blog de servicio](https://blogs.msdn.microsoft.com/azureadb2c/) para las notificaciones de problemas de servicio secundarias, actualizaciones, estado y su eliminación. Continuar supervisar también el [panel de estado de Azure](https://azure.microsoft.com/status/) .
- Actual [restricciones, restricciones y limitaciones de servicio](active-directory-b2c-limitations.md).
- Por último, un [código de ejemplo](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-b2c) con Azure AD B2C & principales de ASP.NET.

## <a name="how-to-articles"></a>Artículos de procedimientos

Obtenga información sobre cómo utilizar las características específicas de Azure Active Directory B2C:

- Configurar cuentas de [Facebook](active-directory-b2c-setup-fb-app.md), [Google +](active-directory-b2c-setup-goog-app.md), [cuenta de Microsoft](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md)y [LinkedIn](active-directory-b2c-setup-li-app.md) para su uso en las aplicaciones de consumidor orientación.
- [Utilizar atributos personalizados para recopilar información acerca de los consumidores](active-directory-b2c-reference-custom-attr.md).
- [Habilitar la autenticación multifactor de Azure en las aplicaciones de consumidor orientación](active-directory-b2c-reference-mfa.md).
- [Configurar el autoservicio restablecimiento de contraseña para los consumidores](active-directory-b2c-reference-sspr.md).
- [Personalizar la apariencia de inicio de sesión de inicio de sesión y otras páginas de cara al consumidor](active-directory-b2c-reference-ui-customization.md) que sirve Azure Active Directory B2C.
- [Usar la API de gráfico de Azure Active Directory para mediante programación crear, leer, actualizar y eliminar los consumidores](active-directory-b2c-devquickstarts-graph-dotnet.md) de su inquilino de Azure Active Directory B2C.

## <a name="next-steps"></a>Pasos siguientes

Estos vínculos le será de utilidad para explorar en profundidad el servicio de:

- Ver la [información de precios de Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
- Obtener ayuda sobre desbordamiento de pila utilizando el [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) o [adal](http://stackoverflow.com/questions/tagged/adal) etiquetas.
- Envíenos sus ideas utilizando [Voz de usuario](https://feedback.azure.com/forums/169401-azure-active-directory/): Queremos escuchar sus comentarios ellos! Utilice la frase "AzureADB2C:" en el título de la publicación para que podamos encontrarla.
- Revise la [referencia de protocolo de Azure AD B2C](active-directory-b2c-reference-protocols.md).
- Revise la [referencia de Token de Azure AD B2C](active-directory-b2c-reference-tokens.md).
- Lea las [preguntas más frecuentes de Azure Active Directory B2C](active-directory-b2c-faqs.md).
- [Archivo admite las solicitudes de Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Obtener actualizaciones de seguridad de nuestros productos

Le recomendamos que obtener notificaciones de cuándo se producen incidencias de seguridad, visite [esta página](https://technet.microsoft.com/security/dd252948) y suscribirse a las alertas de aviso de seguridad.
