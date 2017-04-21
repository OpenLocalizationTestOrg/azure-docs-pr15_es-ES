<properties
   pageTitle="Proporcionar información de contacto de seguridad en el centro de seguridad de Azure | Microsoft Azure"
   description="Este documento muestra cómo proporcionar detalles de contacto de seguridad en el centro de seguridad de Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/17/2016"
   ms.author="terrylan"/>

# <a name="provide-security-contact-details-in-azure-security-center"></a>Proporcionar información de contacto de seguridad en el centro de seguridad de Azure

Centro de seguridad de Azure se recomienda proporcionar detalles de contacto de seguridad para su suscripción de Azure, si todavía no lo ha hecho. Microsoft utilizará esta información en contacto con usted si el centro de respuesta de seguridad de Microsoft (MSRC) descubre que se tiene acceso a los datos del cliente por parte de un ilegal o no autorizada. MSRC realiza la supervisión de la red de Azure y la infraestructura de seguridad select y recibe quejas de inteligencia y abuso amenaza de terceros.

En la primera aparición diaria de una alerta y solo para las alertas de gravedad alta, se envía una notificación de correo electrónico. Preferencias de correo electrónico sólo se puede configurar para las directivas de suscripción. Grupos de recursos dentro de una suscripción heredan esta configuración.

> [AZURE.NOTE] Este documento presenta el servicio mediante el uso de una implementación de ejemplo.  No es una guía paso a paso.

## <a name="implement-the-recommendation"></a>Implementar la recomendación

1. En el módulo de **recomendaciones** , seleccione **proporcionar seguridad detalles de contacto**.
![Proporciona contacto de seguridad][1]

2. Se abrirá el módulo de **seguridad de proporcionar detalles de contacto**. Seleccione la suscripción para proporcionar información de contacto en Azure.
![Proporcionar información de contacto de seguridad][2]

3. Se abre un segundo módulo **proporcionar detalles de contacto de seguridad** .

  - Escriba la dirección de correo electrónico de contacto de seguridad o direcciones separadas por comas. No hay un límite de direcciones de correo electrónico que se pueden escribir.
  - Escriba un número de teléfono internacional de seguridad.
  - Para recibir mensajes de correo electrónico acerca de las alertas de gravedad alta, active la opción **correos electrónicos Enviarme alertas**.
  - En el futuro, tendrá la opción de enviar notificaciones de correo electrónico a los propietarios de la suscripción. Esta opción está atenuada.
  - Seleccione **Aceptar** para aplicar la información de contacto de seguridad a su suscripción.

## <a name="see-also"></a>Vea también

Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md) : Obtenga información sobre cómo configurar directivas de seguridad para suscripciones de Azure y grupos de recursos.
- [Recomendaciones de seguridad de la administración de centro de seguridad de Azure](security-center-recommendations.md) : Descubra cómo recomendaciones ayudar a proteger los recursos de Azure.
- [Supervisión del estado de seguridad del centro de seguridad de Azure](security-center-monitoring.md) : Obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Las soluciones de asociados de supervisión con el centro de seguridad de Azure](security-center-partner-solutions.md) : Obtenga información sobre cómo supervisar el estado de las soluciones de socios.
- [Preguntas más frecuentes sobre centro de seguridad de azure](security-center-faq.md) --buscar con frecuencia preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de azure](http://blogs.msdn.com/b/azuresecurity/) : obtener las últimas noticias de seguridad de Azure y la información.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
