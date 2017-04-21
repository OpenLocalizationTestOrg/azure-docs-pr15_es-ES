<properties
   pageTitle="Habilitar el agente VM en Centro de seguridad de Azure | Microsoft Azure"
   description="Este documento muestra cómo implementar la recomendación del centro de seguridad de Azure **Habilitar agente de máquina virtual**."
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

# <a name="enable-vm-agent-in-azure-security-center"></a>Habilitar el agente VM en Centro de seguridad de Azure

El agente de VM debe instalarse en máquinas virtuales (VM) en orden para [Habilitar la recopilación de datos](security-center-enable-data-collection.md).  Centro de seguridad de Azure le permite ver qué máquinas virtuales requieren al agente de VM y se recomendarán que habilite al agente de VM en esas máquinas virtuales.

El agente de VM se instala de forma predeterminada para máquinas virtuales que se implementan de Azure Marketplace. El artículo [VM agente y extensiones: parte 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) proporciona información sobre cómo instalar al agente de máquina virtual.


> [AZURE.NOTE] Este documento presenta el servicio mediante el uso de una implementación de ejemplo. No es una guía paso a paso.

## <a name="implement-the-recommendation"></a>Implementar la recomendación

1. En el **módulo de recomendaciones**, seleccione **Habilitar el agente de máquina virtual**.
![Habilitar el agente VM][1]

2. Se abrirá el módulo **VM agente que faltan o no responde**. Este módulo enumera las VM que requieren al agente de máquina virtual. Siga las instrucciones en el módulo para instalar al agente de máquina virtual.
![Falta el agente de VM][2]

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
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png
