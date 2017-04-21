<properties
   pageTitle="Proteger los equipos virtuales en el centro de seguridad de Azure | Microsoft Azure"
   description="Las direcciones de este documento recomendaciones en Centro de seguridad de Azure que ayudan a protección sus máquinas virtuales y mantenerse cumple con las directivas de seguridad."
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
   ms.date="09/25/2016"
   ms.author="terrylan"/>

# <a name="protecting-your-virtual-machines-in-azure-security-center"></a>Proteger los equipos virtuales en el centro de seguridad de Azure

Centro de seguridad de Azure analiza el estado de seguridad de los recursos de Azure. Cuando el centro de seguridad se identifica posibles vulnerabilidad de seguridad, crea recomendaciones que le guiarán a lo largo del proceso de configuración de los controles necesarios.  Recomendaciones se aplican a los tipos de recursos de Azure: máquinas virtuales (VM), redes, aplicaciones y SQL.

Este artículo tratan las recomendaciones que se aplican a máquinas virtuales.  Centro de recomendaciones VM alrededor de recopilación de datos, aplicar actualizaciones del sistema, aprovisionamiento antimalware, cifrar los discos VM y mucho más.  Utilice la siguiente tabla como referencia para ayudarle a entender las recomendaciones de VM disponibles y lo que cada uno de ellos hará si se aplica.

## <a name="available-vm-recommendations"></a>Recomendaciones de VM disponibles

|Recomendación|Descripción|
|-----|-----|
|[Habilitar la recopilación de datos para suscripciones](security-center-enable-data-collection.md)|Se recomienda activar la recopilación de datos en la directiva de seguridad para cada una de las suscripciones y todos los equipos virtuales (VM) en las suscripciones.|
|[Corregir la vulnerabilidad OS](security-center-remediate-os-vulnerabilities.md)|Recomienda alinear las configuraciones de sistema operativo con las reglas de la configuración recomendada, por ejemplo, no permitir guardar contraseñas.|
|[Aplicar actualizaciones del sistema](security-center-apply-system-updates.md)|Se recomienda implementar la seguridad del sistema que faltan y las actualizaciones críticas en máquinas virtuales.|
|[Reiniciar después de las actualizaciones del sistema](security-center-apply-system-updates.md#reboot-after-system-updates)|Se recomienda reiniciar una máquina virtual para completar el proceso de aplicar actualizaciones del sistema.|
|[Instalar Endpoint Protection](security-center-install-endpoint-protection.md)|Se recomienda que incluya los programas antimalware VM (sólo VM de Windows).|
|[Resolver las alertas de estado de Endpoint Protection](security-center-resolve-endpoint-protection-health-alerts.md)|Se recomienda que resolver errores de protección de extremo.|
|[Habilitar el agente VM](security-center-enable-vm-agent.md)|Le permite ver qué máquinas virtuales requieren el agente de máquina virtual. El agente de VM debe instalarse en máquinas virtuales para aprovisionar de detección, análisis de línea base y programas antimalware de revisiones. El agente de VM se instala de forma predeterminada para máquinas virtuales que se implementan de Azure Marketplace. El artículo [VM agente y extensiones: parte 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) proporciona información sobre cómo instalar al agente de máquina virtual.|
| [Aplicar el cifrado de disco](security-center-apply-disk-encryption.md) |Se recomienda que cifre los discos VM mediante el cifrado de disco de Azure (Windows y máquinas virtuales de Linux). Cifrado se recomienda para volúmenes de sistema operativo y datos en la máquina virtual.|
| [Actualizar la versión del SO](security-center-update-os-version.md) | Se recomienda que actualice la versión del sistema operativo (SO) de su servicio de nube a la versión más reciente disponible para su familia OS.  Para obtener más información acerca de los servicios en la nube, vea la [información general sobre servicios de nube](../cloud-services/cloud-services-choose-me.md). |
| [Evaluación de vulnerabilidad no instalada](security-center-vulnerability-assessment-recommendations.md) | Recomienda instalar una solución de evaluación de vulnerabilidad en la máquina virtual. |
| [Corregir la vulnerabilidad](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Permite ver vulnerabilidad de sistema y la aplicación detecta la solución de evaluación de vulnerabilidad instalada en su máquina virtual. |

## <a name="see-also"></a>Vea también

Para obtener más información acerca de las recomendaciones que se aplican a otros tipos de recursos de Azure, consulte lo siguiente:

- [Proteger las aplicaciones en el centro de seguridad de Azure](security-center-application-recommendations.md)
- [Protección de su red en Centro de seguridad de Azure](security-center-network-recommendations.md)
- [Proteger el servicio SQL Azure en el centro de seguridad de Azure](security-center-sql-service-recommendations.md)

Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md) : Obtenga información sobre cómo configurar directivas de seguridad para suscripciones de Azure y grupos de recursos.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Preguntas más frecuentes sobre centro de seguridad de azure](security-center-faq.md) --buscar con frecuencia preguntas más frecuentes sobre el uso del servicio.
