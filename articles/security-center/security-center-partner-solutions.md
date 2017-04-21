<properties
   pageTitle="Administrar soluciones de socios en el centro de seguridad de Azure | Microsoft Azure"
   description="Este documento le guiará por el centro de seguridad de Azure le permite a monitor de un vistazo el estado de las soluciones de socios integrado con su suscripción de Azure."
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
   ms.date="10/26/2016"
   ms.author="terrylan"/>

# <a name="monitoring-partner-solutions-with-azure-security-center"></a>Supervisar las soluciones de asociados con el centro de seguridad de Azure

Este documento le guiará a través de cómo supervisar el estado de las soluciones de socios en el centro de seguridad de Azure.

> [AZURE.NOTE] Este documento presenta el servicio mediante el uso de una implementación de ejemplo. No es una guía paso a paso.

## <a name="monitoring-partner-solutions"></a>Supervisar las soluciones de asociados

El mosaico de **soluciones de socios** en el módulo de **Centro de seguridad** le permite a monitor de un vistazo el estado de las soluciones de socios integrado con su suscripción de Azure.
![Las soluciones de asociados de mosaico][1]

El mosaico de **soluciones de socios** muestra el número de soluciones de asociados y un resumen de las soluciones de estado.

El **estado** de una solución de un partner puede ser:

- Protegido (verde): no hay ningún problema de salud.
- Negativa (rojo): hay un problema de salud que necesite atención inmediata.
- Detener reporting (naranja): la solución ha dejado de informar de su estado.
- Protección desconocido (naranja) - el mantenimiento de la solución se desconoce el estado en este momento debido a un error proceso de agregar un nuevo recurso para la solución existente.
- No informe (gris) - la solución no notificado nada todavía, estado de una solución posible que no notificado si acaba de conectarse y aún está implementando.

Si no hay ningún soluciones integradas con su suscripción en el mosaico indicará que no hay ninguna solución. Seleccionar el mosaico de **soluciones de socios** le permitirá abrir el módulo de **recomendaciones** para implementar soluciones de seguridad de socios.
![Sin soluciones de socios][2]

Para ver el estado de las soluciones de socios:

1. Seleccione el icono de **soluciones de socios** . Un módulo abrirá una lista de las soluciones de socios conectado al centro de seguridad.
![Soluciones de asociados][3]

2. Seleccione una solución asociado. En este ejemplo, le permite seleccionar la solución **WAF2 F5** .  Se abre un módulo que muestra que el estado de la solución de partner y la solución los recursos asociados. Seleccione **consola de soluciones** para abrir la experiencia de administración de partners para esta solución.
![Detalle de soluciones de socios][4]

3. Volver a la hoja **WAF2 F5** y seleccione **aplicación de vínculo**. Se abre el módulo de **Aplicaciones de vínculo** . Aquí puede conectar recursos para la solución de un partner.
![Recursos de vínculo para soluciones de socios][5]

## <a name="see-also"></a>Vea también
En este documento, se han introducido en el mosaico de **Soluciones de socios** en el centro de seguridad. Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md) : Obtenga información sobre cómo configurar directivas de seguridad para suscripciones de Azure y grupos de recursos.
- [Administrar las recomendaciones de seguridad en el centro de seguridad de Azure](security-center-recommendations.md) : Descubra cómo recomendaciones ayudar a proteger los recursos de Azure.
- [Supervisión en el centro de seguridad de Azure de estado de seguridad](security-center-monitoring.md) : Obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Preguntas más frecuentes sobre centro de seguridad de Azure](security-center-faq.md) : preguntas más frecuentes sobre el uso del servicio de búsqueda.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : obtener las últimas noticias de seguridad de Azure y la información.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png
