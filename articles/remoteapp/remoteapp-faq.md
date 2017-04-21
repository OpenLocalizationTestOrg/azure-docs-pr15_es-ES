<properties 
    pageTitle="Preguntas más frecuentes de RemoteApp Azure | Microsoft Azure" 
    description="Obtenga respuestas a la mayoría de la preguntas más frecuentes sobre RemoteApp de Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="swadhwa" 
    editor=""/>

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="azure-remoteapp-faq"></a>Preguntas más frecuentes de RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Las siguientes preguntas acerca de RemoteApp de Azure nuestros usuarios. ¿Tener otros usuarios? Visite los [foros de RemoteApp](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp) y háganoslo saber lo que debe saber o desplegable un comentario a continuación.

## <a name="cant-find-what-youre-looking-for-have-a-question-we-didnt-answer"></a>¿No encuentra lo está buscando? ¿Tiene alguna pregunta, no responde?
Si no puede encontrar la información que necesita, o tenga una pregunta adicional que nos estamos no cubierta por aquí, vaya al [foro de RemoteApp de Azure](http://aka.ms/araforum) y pida a su pregunta. Siempre podemos agregar más respuestas aquí.

## <a name="what-is-azure-remoteapp"></a>¿Qué es RemoteApp de Azure? ##


- **¿Qué es RemoteApp de Azure?** RemoteApp es un servicio de Azure le ayuda a proporcionar acceso remoto seguro a aplicaciones desde muchos dispositivos de otro usuario. Obtenga más información acerca de [RemoteApp de Azure](remoteapp-whatis.md).
- **¿Cuáles son las opciones de implementación?** Existen dos tipos de colecciones de RemoteApp: nube y híbrido. Qué uno que necesita depende de varios factores, como si debe unirse a un dominio. Hablar de todas las decisiones de [aquí](remoteapp-collections.md).

## <a name="quick-tips-on-using-azure-remoteapp"></a>Sugerencias sobre el uso de RemoteApp de Azure ##
- **¿Cuánto tiempo hasta pierdo la conexión? ¿Cuánto tiempo puedo estar inactivo antes de que me ofrece el inicio?** 4 horas. Si usted o uno de los usuarios está inactivo durante 4 horas, se iniciará sesión automáticamente de RemoteApp de Azure. Consulte la otra configuración predeterminada en la [suscripción de Azure y los límites del servicio, cuotas y las restricciones](../azure-subscription-service-limits.md).
- **¿Puedo probar este servicio de forma gratuita?** Sí. Hay una prueba gratuita de 30 días. Cuando la prueba termine, puede hacer la transición a una cuenta de pagada (que puede usar en producción) o dejar de usar el servicio. Iniciar la versión de prueba gratuita, vaya a [portal.azure.com](http://portal.azure.com) : crear una nueva instancia de RemoteApp. Con la versión de prueba gratuita que puede crear 2 instancias de RemoteApp con 10 usuarios por cada instancia. Recuerde que sólo reside esta versión de prueba de 30 días.
## <a name="azure-remoteapp-subscription-details"></a>Detalles de la suscripción de RemoteApp Azure ##

- **¿Cuáles son los límites de servicio?** Puede obtener información sobre los límites de servicio de RemoteApp de Azure en la [suscripción de Azure y los límites del servicio, cuotas y las restricciones](../azure-subscription-service-limits.md)y la configuración predeterminada. Díganos si tiene más preguntas.
- **¿Cuántos usuarios puedo tener tener?** Hay un mínimo de 20 usuarios. Que es lo mismo para ser muy clara: el valor mínimo es 20. Se le facturará para 20. 
- **¿Cuánto cuesta RemoteApp costo?** Consulte [Detalles de precios de RemoteApp de Azure ](https://azure.microsoft.com/pricing/details/remoteapp/).
- **¿Un tipo de colección cuesta más que otra?** Sí, puede, dependiendo de los requisitos de la colección. Una colección de híbrido requiere una conexión de Azure RemoteApp a su red local. Si utiliza una ruta VNET/Express existente, no hay ningún costo adicional. Pero, si utiliza un nuevo VNET de Azure y una puerta de enlace o distribuir Express, se cobrará por la [puerta de enlace VPN](https://azure.microsoft.com/pricing/details/vpn-gateway) o la [Ruta de Express](https://azure.microsoft.com/pricing/details/expressroute/). Este costo (detallado en los vínculos) es sobre su RemoteApp de Azure mensual de costo.

## <a name="collections---whats-supported-which-should-you-use-and-others"></a>Colecciones - ¿qué es compatible, que debe utilizar y otros usuarios
- **¿Son compatibles con las aplicaciones personalizadas línea de negocio (LOB)?** Sí. Para usar una aplicación personalizada en Azure RemoteApp, crear una [imagen de la plantilla personalizada](remoteapp-create-custom-image.md)y luego cárguelo en la colección de RemoteApp.
- **¿Funcionarán mis aplicaciones LOB personalizadas en RemoteApp de Azure?** La mejor manera de figura que fuera probarlo. Consulte el [Centro de compatibilidad de escritorio remoto](http://www.rdcompatibility.com/compatibility/default.aspx).
- **¿Qué método de distribución (nube o híbrido) es mejor para mi organización?** Colecciones híbrido proporcionan la experiencia más completa si desea que la integración completa con inicio de sesión único (SSO) y conectividad de red seguro en local. Colecciones de nube proporcionan una forma fácil y ágil aislar la implementación mediante varios métodos de autenticación. Obtenga más información acerca de las [Opciones de implementación](remoteapp-whatis.md).
- **Tenemos SQL u otra base de datos en modo local o en Azure. ¿Tipo de implementación debemos utilizar?** Que depende de dónde está la base de datos SQL o back-end. Si la base de datos está en una red privada, utilice la colección de híbrido. Si la base de datos se expone a Internet y permite a cliente conexiones para conectarse a ella, puede usar la colección de la nube.
- **¿Qué información sobre la asignación de unidad USB y puerto serie, uso compartido del Portapapeles y redirección de impresora?** Todas las características son compatibles con RemoteApp de Azure. Redirección de uso compartido y la impresora está habilitada de forma predeterminada. Puede obtener más información sobre redirección [aquí](remoteapp-redirection.md). 


## <a name="template-images"></a>Imágenes de plantilla
- **¿Puedo usar una nube o una máquina virtual existente como plantilla para Mi colección RemoteApp?** ¡Sí! Puede crear una imagen basada en una máquina virtual de Azure, utilice una de las imágenes que se incluye con la suscripción o crear una imagen personalizada. Consulte las [Opciones de imagen de RemoteApp](remoteapp-imageoptions.md).


## <a name="network-options"></a>Opciones de red
- **La colección híbrido requiere un VNET. ¿Podemos usar nuestro VNET existente?** Puede hacerlo si la VNET existente es un VNET de Azure. Consulte "paso 1: configurar una red virtual" en las [instrucciones de la colección de híbrido](remoteapp-create-hybrid-deployment.md) para obtener más información.
- **¿Puedo usar un VNET con una colección de nube?** De hecho, puede. Consulte [crear una colección de nube](remoteapp-create-cloud-deployment.md), especialmente paso 1, para obtener más información.

## <a name="authentication-options"></a>Opciones de autenticación



- **¿Qué autenticación? ¿Los métodos son compatibles?** La colección de nube admite cuentas de Microsoft y cuentas de Azure Active Directory, que son también cuentas de Office 365. La colección de híbrido admite únicamente las cuentas de Azure Active Directory que se han sincronizado (mediante una herramienta como [Azure Active Directory Sync](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)) de una implementación de Active Directory de Windows Server; más concretamente, ya sea sincronizado con la opción de sincronización de contraseñas o sincronizado con la federación de servicios de federación de Active Directory (AD FS) configurada. También puede configurar la [Autenticación multifactor (AMF)](https://azure.microsoft.com/services/multi-factor-authentication/).

>[AZURE.NOTE]Los usuarios de Azure Active Directory deben estar entre el inquilino que está asociada a su suscripción. (Puede ver y modificar su suscripción en la pestaña **configuración** en el portal. Consulte [cambiar el inquilino de Azure Active Directory usa RemoteApp](remoteapp-changetenant.md) para obtener más información).

- **¿Por qué no puedo dar a mi acceso a la cuenta de Azure Active Directory?** Los usuarios de Azure Active Directory deben ser desde el directorio que está asociada a su suscripción. Puede ver o modificar dicho directorio en la pestaña Configuración en el portal. Para obtener más información, vea [cambiar el inquilino de Azure Active Directory usa RemoteApp](remoteapp-changetenant.md) .

## <a name="clients---what-device-can-i-use-to-access-azure-remoteapp"></a>Clientes: ¿qué dispositivo puede usar para tener acceso a RemoteApp de Azure?
Puede encontrar información de cliente, incluidos los pasos para instalar a los clientes de [acceder a sus aplicaciones de Azure RemoteApp](remoteapp-clients.md)diferentes.

- **¿Qué dispositivos y sistemas operativos admiten las aplicaciones de cliente?**
Primero, los equipos y tabletas: 
    - Windows 10 (versión preview del cliente)
    - Windows 8.1 y Windows 8
    - Windows 7 Service Pack 1
    - Mac OS X
    - Windows RT
    - Tabletas Android
    - iPads y los teléfonos:
    - iPhone
    - Teléfono Android
    - Windows Phone
 
    [Descargar](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) un cliente de RemoteApp ahora.
- **¿Se admite a clientes ligeros de RemoteApp de Azure?** Sí, se admiten los siguientes clientes finos incrustado de Windows:
    - Windows incrustado estándar 7
    - Windows incrustado estándar 8
    - Windows incrustado sector 8.1 Pro
    - Windows 10 IoT Enterprise

- **¿Qué versión de Windows Server es compatible para el Host de sesión de escritorio remoto (RDSH)?** Windows Server 2012 R2.

## <a name="support-and-feedback"></a>Soporte técnico y comentarios


- **¿Qué es el plan de soporte técnico de RemoteApp?** Se proporciona soporte técnico para la administración de facturación y suscripción sin costo. Soporte técnico está disponible a través de los [planes de servicio de Azure](https://azure.microsoft.com/support/plans/). También puede obtener soporte técnico de la Comunidad gratuita a través de nuestro [foro de discusión de Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp). 
- **¿Cómo enviar comentarios?** Visite el [foro de comentarios](https://feedback.azure.com/forums/247748-azure-remoteapp/).
- **¿Quién puedo hablar para obtener más información acerca de RemoteApp de Azure?** Además de nuestro [foro de discusión](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp), que es un lugar ideal para publicar preguntas, puede unirse a la semanal [Ask seminario de expertos](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html), donde hablar sobre todo RemoteApp.
- **¿Qué documentación de RemoteApp?** Nos alegra que se le solicite. Además del contenido de Ayuda del cajón de Ayuda de portal (haga clic en **?** en cualquier página en el portal), los artículos siguientes están disponibles para mostrarle todo sobre RemoteApp:
    - **Introducción:**
        - [¿Qué es RemoteApp?](remoteapp-whatis.md)
        - [¿Cuáles son las imágenes de plantilla de RemoteApp?](remoteapp-images.md)
        - [¿Cómo funciona licencias?](remoteapp-licensing.md)
        - [¿Cómo funcionan conjuntamente RemoteApp y Office?](remoteapp-o365.md)
        - [¿Cómo funciona la redirección en RemoteApp](remoteapp-redirection.md)?
    - **Implementar:**
        - [Crear una imagen de la plantilla personalizada](remoteapp-create-custom-image.md)
        - [Crear una colección de híbrido](remoteapp-create-hybrid-deployment.md)
        - [Crear una colección de nube](remoteapp-create-cloud-deployment.md)
        - [Configure Azure Active Directory de RemoteApp](remoteapp-ad.md)
        - [Publicar una aplicación de RemoteApp](remoteapp-publish.md)
    - **Administrar:**
        - [Agregar usuarios](remoteapp-user.md)
        - [Prácticas recomendadas para configurar y utilizar RemoteApp](remoteapp-bestpractices.md)  

    ¡Vídeos! También tenemos una serie de vídeos acerca de RemoteApp. Algunos proporcionan Introducción ([Introducción a Azure RemoteApp](https://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)) mientras otras personas le guían durante la implementación ([implementación de la nube](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) e [implementación híbrida](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). ¡Desprotegerlos!

 
### <a name="help-us-help-you"></a>Ayúdenos a ayudarle 
¿Sabía que, además de valoración de este artículo y realizar comentarios hacia abajo, a continuación, puede realizar cambios en el artículo? ¿Falta algo? ¿Algún error? ¿Escribir algo que es simplemente confuso? Desplazarse hacia arriba y haga clic en **Editar en GitHub** para realizar cambios: los vaya a nosotros para revisión y, a continuación, una vez se aprobar en ellas, verá los cambios y mejoras aquí.
