<properties
   pageTitle="Resolver las alertas de estado de protección de extremo en el centro de seguridad de Azure | Microsoft Azure"
   description="Este documento muestra cómo implementar la recomendación del centro de seguridad de Azure **resolver Endpoint Protection alertas de estado**."
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
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="resolve-endpoint-protection-health-alerts-in-azure-security-center"></a>Resolver las alertas de estado de protección de extremo en el centro de seguridad de Azure

Centro de seguridad de Azure se recomienda que resolver alertas de estado de protección de extremo detectado.  Centro de seguridad le permite ver qué máquinas virtuales (VM) tienen errores de protección de extremo y cuántas.

> [AZURE.NOTE] Este documento presenta el servicio mediante el uso de una implementación de ejemplo. No es una guía paso a paso.

## <a name="implement-the-recommendation"></a>Implementar la recomendación

1. En el **módulo de recomendaciones**, seleccione **alertas de estado de resolver Endpoint Protection**.
![Resolver las alertas de estado de protección de extremo][1]

2. Se abrirá el módulo **Endpoint Protection error** que muestra máquinas virtuales con errores y el número de errores para cada máquina virtual. Seleccione una máquina virtual de la lista.
![Error de protección de extremo][2]

3. Se abre un módulo de la **Lista de errores** para la máquina virtual seleccionada, que muestra una lista de errores. Seleccione un error de la lista para obtener más información. Se abrirá un módulo con información sobre el error seleccionado.
![Lista de errores][3]
  ![eventos de error][4]

## <a name="see-also"></a>Vea también

Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md): Obtenga información sobre cómo configurar directivas de seguridad para suscripciones de Azure y grupos de recursos.
- [Administrar las recomendaciones de seguridad en el centro de seguridad de Azure](security-center-recommendations.md): Descubra cómo recomendaciones ayudar a proteger los recursos de Azure.
- [Supervisión en el centro de seguridad de Azure de estado de seguridad](security-center-monitoring.md): Obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Las soluciones de asociados de supervisión con el centro de seguridad de Azure](security-center-partner-solutions.md) : Obtenga información sobre cómo supervisar el estado de las soluciones de socios.
- [Preguntas más frecuentes sobre centro de seguridad de azure](security-center-faq.md)--buscar con frecuencia preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de azure](http://blogs.msdn.com/b/azuresecurity/): obtener las últimas noticias de seguridad de Azure y la información.

<!--Image references-->
[1]: ./media/security-center-resolve-endpoint-protection/resolve-endpoint-protection.png
[2]: ./media/security-center-resolve-endpoint-protection/endpoint-protection-failure.png
[3]: ./media/security-center-resolve-endpoint-protection/failure-list.png
[4]: ./media/security-center-resolve-endpoint-protection/failure-event.png
