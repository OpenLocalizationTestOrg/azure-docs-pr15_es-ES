<properties 
    pageTitle="Cómo proteger los servicios de back-end mediante el cliente de autenticación de certificado de la administración de la API de Azure" 
    description="Obtenga información sobre cómo proteger los servicios de back-end mediante la autenticación de certificado de cliente en la administración de la API de Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Cómo proteger los servicios de back-end mediante el cliente de autenticación de certificado de la administración de la API de Azure

Administración de la API proporciona la capacidad para proteger el acceso al servicio de back-end de una API con certificados de cliente. Esta guía muestra cómo administrar los certificados en el portal de publisher API y cómo configurar una API para usar un certificado para tener acceso a su servicio de back-end.

Para obtener información sobre la administración de certificados mediante la API de REST API administración, vea [certificado de API de REST de Azure API administración entidad][].

## <a name="prerequisites"> </a>Los requisitos previos

Esta guía le muestra cómo configurar la instancia de servicio de administración de la API para usar autenticación de certificado de cliente para tener acceso al servicio de back-end para una API. Antes de seguir los pasos de este tema, debe tener el servicio de back-end configurado para la autenticación de certificado de cliente ([para configurar la autenticación de certificado de sitios Web de Azure, consulte este artículo][]) y tener acceso al certificado y la contraseña para el certificado para cargar en el portal de administración de la API de publisher.

## <a name="step1"> </a>Cargar un certificado de cliente

Para empezar, haga clic en **Administrar** en el Portal de Azure clásico de su servicio de administración de la API. Se abrirá el portal de administración de la API de publisher.

![Portal de API Publisher][api-management-management-console]

>Si aún no ha creado una instancia de servicio de administración de la API, vea [crear una instancia de servicio de administración de la API][] en el tutorial de [Introducción a la administración de la API de Azure][] .

Haga clic en **seguridad** en el menú de **Administración de la API** de la izquierda y haga clic en **certificados de cliente**.

![Certificados de cliente][api-management-security-client-certificates]

Para cargar un nuevo certificado, haga clic en **cargar el certificado**.

![Cargar el certificado][api-management-upload-certificate]

Busque el certificado y, a continuación, escriba la contraseña para el certificado.

>El certificado debe estar en formato **.pfx** . Se permiten los certificados de firma automática.

![Cargar el certificado][api-management-upload-certificate-form]

Haga clic en **cargar** para cargar el certificado.

>En este momento, se valida la contraseña de certificado. Si no es correcta, se muestra un mensaje de error.

![Certificado cargado][api-management-certificate-uploaded]

Una vez cargado el certificado, aparece en la ficha **certificados de cliente** . Si tiene varios certificados, asegúrese de una nota del asunto o los últimos cuatro caracteres de la huella digital, que se utilizan para seleccionar el certificado al configurar una API para usar certificados, como se explica en la siguiente sección para [Configurar una API para usar un certificado de cliente para la autenticación de puerta de enlace][] .

>Para desactivar la validación de la cadena de certificados cuando se utiliza, por ejemplo, un certificado autofirmado, siga los pasos descritos en este [artículo](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)de preguntas más frecuentes.

## <a name="step1a"> </a>Eliminar un certificado de cliente

Para eliminar un certificado, haga clic en **Eliminar** junto al certificado que desee.

![Eliminar certificado][api-management-certificate-delete]

Haga clic en **Sí, elimínelo** para confirmar.

![Confirmar la eliminación][api-management-confirm-delete]

Si está utilizando una API el certificado, se muestra una pantalla de advertencia. Para eliminar el certificado primero debe quitar el certificado de cualquier API que se haya configurado para usarlo.

![Confirmar la eliminación][api-management-confirm-delete-policy]

## <a name="step2"> </a>Configurar una API para usar un certificado de cliente para la autenticación de puerta de enlace

Haga clic en **las API de** desde el menú de **Administración de la API** de la izquierda, haga clic en el nombre de la API que desee y haga clic en la pestaña **seguridad** .

![Seguridad de la API][api-management-api-security]

Seleccione **los certificados de cliente** de la lista de lista desplegable **con credenciales** .

![Certificados de cliente][api-management-mutual-certificates]

Seleccione el certificado que desee de la lista desplegable de **certificado de cliente** . Si hay varios certificados puede mirar el asunto o los últimos cuatro caracteres de la huella digital tal como se indica en la sección anterior para determinar el certificado correcto.

![Seleccionar certificado][api-management-select-certificate]

Haga clic en **Guardar** para guardar el cambio de configuración en la API.

>Este cambio entra en vigor inmediatamente y llamadas a las operaciones de esa API utilizará el certificado para autenticar en el servidor back-end.

![Guardar los cambios de la API][api-management-save-api]

>Cuando se especifica un certificado para la autenticación de puerta de enlace para el servicio de fondo de una API, pase a formar parte de la directiva para la API y se pueden visualizar en el editor de directivas.

![Directiva de certificado][api-management-certificate-policy]

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre otras formas de proteger el servicio de back-end, como HTTP autenticación básica o compartido secreto, vea el siguiente vídeo.

> [AZURE.VIDEO last-mile-security]

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Introducción a la administración de la API de Azure]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Crear una instancia de servicio de administración de la API]: api-management-get-started.md#create-service-instance

[Entidad de certificado de API de REST de administración de API de Azure]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Para configurar la autenticación de certificado de sitios Web de Azure, consulte este artículo]: https://azure.microsoft.com/en-us/documentation/articles/app-service-web-configure-tls-mutual-auth/

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configurar una API para usar un certificado de cliente para la autenticación de puerta de enlace]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps


 
