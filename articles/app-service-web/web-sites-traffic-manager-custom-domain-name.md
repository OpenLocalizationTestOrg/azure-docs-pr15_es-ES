<properties
    pageTitle="Configurar un nombre de dominio personalizado para una aplicación web de servicio de la aplicación de Azure que usa el Administrador de tráfico de equilibrio de carga."
    description="Usar un nombre de dominio personalizado para un una aplicación web de servicio de aplicación de Azure que incluye Administrador de tráfico de equilibrio de carga."
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Configurar un nombre de dominio personalizado para una aplicación web de servicio de aplicación de Azure con el Administrador de tráfico

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Este artículo ofrece instrucciones genéricas para usar un nombre de dominio personalizado con la aplicación de servicio de Azure que use el Administrador de tráfico de equilibrio de carga.

[AZURE.INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
## <a name="understanding-dns-records"></a>Descripción de los registros DNS

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## <a name="configure-your-web-apps-for-standard-mode"></a>Configurar aplicaciones web para modo estándar

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## <a name="add-a-dns-record-for-your-custom-domain"></a>Agregar un registro DNS de su dominio personalizado

> [AZURE.NOTE] Si ha adquirido dominio a través de la aplicación de servicio Web Apps de Azure omitir pasos y consulte el paso final del artículo [Comprar dominio para las aplicaciones Web](custom-dns-web-site-buydomains-web-app.md) .

Para asociar el dominio personalizado con una aplicación web en la aplicación de servicio de Azure, debe agregar una nueva entrada en la tabla DNS de su dominio personalizado mediante herramientas proporcionadas por el registrador de dominios que compró su nombre de dominio desde. Realice los pasos siguientes para buscar y usar las herramientas DNS.

1. Inicie sesión en su cuenta en el registrador de dominios y busque una página de administración de registros DNS. Buscar vínculos o áreas del sitio con la etiqueta como **Nombre de dominio**, **DNS**o un **Nombre de servidor de administración**. A menudo se puede encontrar un vínculo a esta página se vean su información de cuenta y, a continuación, buscando un vínculo como **My domains**.

1. Una vez haya encontrado la página de administración para el nombre de dominio, busque un vínculo que le permite editar los registros DNS. Esto puede aparecer como un **archivo de zona**, **Los registros DNS**, o como un vínculo de configuración **avanzada** .

    * La página probablemente tendrá unos registros que ya ha creado, por ejemplo, una asociación de entrada '**@**'o'\*' con una página 'estacionamiento dominio'. También puede contener registros para subdominios comunes como **www**.
    * La página se mencione **registros CNAME**o proporcionar una lista desplegable para seleccionar un tipo de registro. También pueden mencionar otros registros como **registros** y los **registros MX**. En algunos casos, otros nombres como un **Registro de Alias**llamará registros CNAME.
    * La página también tendrá campos que le permitan **mapa** de un **nombre de Host** o el **nombre de dominio** a otro nombre de dominio.

1. Aunque los detalles de cada registrador varían, en general asignar *de* nombre de dominio personalizado (por ejemplo, **contoso.com**) *para* el nombre de dominio de administrador de tráfico (**contoso.trafficmanager.net**) que se usa para la aplicación web.

    > [AZURE.NOTE] Como alternativa, si un registro ya está en uso y necesita IntruShield enlazar las aplicaciones, puede crear un registro CNAME adicional. Por ejemplo, para enlazar IntruShield **www.contoso.com** a la aplicación web, cree un registro CNAME de **awverify.www** a **contoso.trafficmanager.net**. A continuación, puede agregar "www.contoso.com" a su aplicación Web sin cambiar el registro CNAME de "www". Para obtener más información, vea [crear registros DNS para una aplicación web en un dominio personalizado][CREATEDNS].

1. Cuando haya terminado de agregar o modificar los registros DNS en el registrador, guardar los cambios.

<a name="enabledomain"></a>
## <a name="enable-traffic-manager"></a>Habilitar el Administrador de tráfico

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de Node.js](/develop/nodejs/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
