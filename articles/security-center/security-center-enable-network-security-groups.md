<properties
   pageTitle="Habilitar grupos de seguridad de red en Centro de seguridad de Azure | Microsoft Azure"
   description="Este documento muestra cómo implementar la recomendación del centro de seguridad de Azure **Habilitar grupos de seguridad de red**."
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

# <a name="enable-network-security-groups-in-azure-security-center"></a>Habilitar grupos de seguridad de red en Centro de seguridad de Azure

Centro de seguridad de Azure se recomienda habilitar a un grupo de seguridad de la red (GSN) si una no está habilitada. NSGs contiene una lista de reglas de la lista de Control de acceso (ACL) que permitir o denegar el tráfico de red de las instancias de máquina virtual en una red Virtual. NSGs se pueden asociadas con subredes o instancias VM individuales en esa subred. Cuando un GSN está asociada a una subred, se aplican las reglas ACL a todas las instancias de máquina virtual de esa subred. Además, se puede restringir el tráfico a una máquina virtual individual más asociando una GSN directamente a esa máquina virtual. Para obtener más, vea [¿Qué es un grupo de seguridad de la red (GSN)?](../virtual-network/virtual-networks-nsg.md)

Si no tiene NSGs habilitados, el centro de seguridad se presentará dos recomendaciones a: habilitar grupos de seguridad de red en subredes y habilitar a los grupos de seguridad de red en máquinas virtuales. Elija qué nivel, subred o VM para aplicar NSGs.


> [AZURE.NOTE] Este documento presenta el servicio mediante el uso de una implementación de ejemplo.  No es una guía paso a paso.

## <a name="implement-the-recommendation"></a>Implementar la recomendación

1. En el módulo de **recomendaciones** , seleccione **Habilitar grupos de seguridad de red** en subredes o en máquinas virtuales de Windows.
![Habilitar grupos de seguridad de red][1]

2. Se abrirá el módulo **Configurar grupos de seguridad de red que faltan** para subredes o para máquinas virtuales de Windows, dependiendo de la recomendación que haya seleccionado. Seleccione una subred o una máquina virtual para configurar un GSN en.

  ![Configurar GSN de subred][2]

  ![Configurar GSN para VM][3]
3. En el **grupo de seguridad de red de elegir** el módulo seleccione un GSN existente o crear un nuevo GSN.

  ![Elija el grupo de seguridad de red][4]

Si crea un nuevo GSN, siga los pasos de [cómo administrar NSGs con el portal de Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) crear un GSN y establecer reglas de seguridad.

## <a name="see-also"></a>Vea también

En este artículo le mostramos cómo implementar la recomendación del centro de seguridad "Habilitar grupos de seguridad de red" subredes o máquinas virtuales de Windows. Para obtener más información acerca de cómo habilitar NSGs, consulte lo siguiente:

- [¿Qué es un grupo de seguridad de la red (GSN)?](../virtual-network/virtual-networks-nsg.md)
- [Cómo administrar NSGs con el portal de Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md) : Obtenga información sobre cómo configurar directivas de seguridad para suscripciones de Azure y grupos de recursos.
- [Recomendaciones de seguridad de la administración de centro de seguridad de Azure](security-center-recommendations.md) : Descubra cómo recomendaciones ayudar a proteger los recursos de Azure.
- [Supervisión del estado de seguridad del centro de seguridad de Azure](security-center-monitoring.md) : Obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Las soluciones de asociados de supervisión con el centro de seguridad de Azure](security-center-partner-solutions.md) : Obtenga información sobre cómo supervisar el estado de las soluciones de socios.
- [Preguntas más frecuentes sobre centro de seguridad de azure](security-center-faq.md) --buscar con frecuencia preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de azure](http://blogs.msdn.com/b/azuresecurity/) : obtener las últimas noticias de seguridad de Azure y la información.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
