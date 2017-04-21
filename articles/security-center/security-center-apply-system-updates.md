<properties
   pageTitle="Aplicar actualizaciones del sistema del centro de seguridad de Azure | Microsoft Azure"
   description="Este documento muestra cómo implementar las recomendaciones de centro de seguridad de Azure **aplicar actualizaciones del sistema** y **reiniciar después de las actualizaciones del sistema**."
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

# <a name="apply-system-updates-in-azure-security-center"></a>Aplicar actualizaciones del sistema del centro de seguridad de Azure

Centro de seguridad de Azure diariamente supervisa Windows y Linux máquinas virtuales (VM) si faltan actualizaciones de sistema operativo. Centro de seguridad recupera una lista de seguridad disponibles y actualizaciones críticas de Windows Update o Windows Server Update Services (WSUS), dependiendo de lo que el servicio está configurado en una máquina virtual de Windows.  Centro de seguridad también busca las actualizaciones más recientes en sistemas Linux. Si su máquina virtual falta una actualización del sistema, centro de seguridad se recomienda aplicar actualizaciones del sistema

> [AZURE.NOTE] Este documento presenta el servicio mediante el uso de una implementación de ejemplo.  No es una guía paso a paso.

## <a name="implement-the-recommendation"></a>Implementar la recomendación

1. En el módulo de **recomendaciones** , seleccione **aplicar actualizaciones del sistema**.
![Aplicar actualizaciones del sistema][1]

2. El módulo de **aplicar actualizaciones del sistema** abrirá una lista de máquinas virtuales de las actualizaciones de sistema que faltan. Seleccione una máquina virtual.
![Seleccione una máquina virtual][2]

3. Un módulo abrirá una lista de las actualizaciones que faltan para dicha máquina virtual. Seleccione una actualización del sistema. En este ejemplo, seleccionaremos KB3156016.
![Actualizaciones de seguridad que faltan][3]

4. Siga los pasos descritos en el módulo de **Actualización de seguridad** para aplicar la actualización que falta.
![Actualización de seguridad][4]

## <a name="reboot-after-system-updates"></a>Reiniciar después de las actualizaciones del sistema

5. Volver a la hoja de **recomendaciones** . Después de aplicar actualizaciones del sistema, denominadas **reiniciar después de las actualizaciones del sistema**, se genera una nueva entrada. Esta entrada permite saber que necesite reiniciar la máquina virtual para completar el proceso de aplicar actualizaciones del sistema.
![Reiniciar después de las actualizaciones del sistema][5]

6. Seleccione **reiniciar después de las actualizaciones del sistema**. Se abrirá el módulo **que reiniciar está pendiente para completar las actualizaciones del sistema** que muestra una lista de las VM que tenga que reiniciar para completar el proceso de actualizaciones del sistema de aplicación.
![Reinicie pendiente][6]

Reinicie la máquina virtual de Azure para completar el proceso.

## <a name="see-also"></a>Vea también

Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md) : Obtenga información sobre cómo configurar directivas de seguridad para suscripciones de Azure y grupos de recursos.
- [Recomendaciones de seguridad de la administración de centro de seguridad de Azure](security-center-recommendations.md) : Descubra cómo recomendaciones ayudar a proteger los recursos de Azure.
- [Supervisión del estado de seguridad del centro de seguridad de Azure](security-center-monitoring.md) : Obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Las soluciones de asociados de supervisión con el centro de seguridad de Azure](security-center-partner-solutions.md) : Obtenga información sobre cómo supervisar el estado de las soluciones de socios.
- [Preguntas más frecuentes sobre centro de seguridad de azure](security-center-faq.md) --buscar con frecuencia preguntas más frecuentes sobre el uso del servicio.
- Entradas de [blog de seguridad de azure](http://blogs.msdn.com/b/azuresecurity/) --buscar blog sobre cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/recommendation.png
[2]:./media/security-center-apply-system-updates/select-vm.png
[3]: ./media/security-center-apply-system-updates/missing-security-updates.png
[4]: ./media/security-center-apply-system-updates/security-update.png
[5]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[6]: ./media/security-center-apply-system-updates/restart-pending.png
