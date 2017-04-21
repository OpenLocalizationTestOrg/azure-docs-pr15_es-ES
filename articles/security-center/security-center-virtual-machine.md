<properties
   pageTitle="Centro de seguridad de Azure y máquinas virtuales de Windows Azure | Microsoft Azure"
   description="Este documento le ayuda a comprender cómo el centro de seguridad de Azure puede protegerle máquinas virtuales de Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-and-azure-virtual-machines"></a>Centro de seguridad de Azure y máquinas virtuales de Windows Azure

[Centro de seguridad de Azure](https://azure.microsoft.com/services/security-center/) le ayuda a evitar, detectar y responder a las amenazas. Proporciona seguridad integrada supervisión y la directiva de administración de las suscripciones de Azure, ayuda a detecta amenazas que pueden incluirse en caso contrario, problemas y funciona con un amplio ecosistema de soluciones de seguridad.

Este artículo le muestra cómo el centro de seguridad puede ayudar a proteger su máquinas virtuales de Windows Azure (VM).

## <a name="why-use-security-center"></a>¿Por qué usar el centro de seguridad?

Centro de seguridad le ayuda a proteger los datos de máquina virtual en Azure proporcionando visibilidad en configuración de seguridad de su máquina virtual. Al centro de seguridad protege sus máquinas virtuales, las siguientes capacidades estarán disponibles:

- Configuración de seguridad del sistema operativo (SO) con las reglas de la configuración recomendada
- Seguridad del sistema y actualizaciones críticas que faltan
- Recomendaciones de protección de extremo
- Disco de validación de cifrado
- Corrección y evaluación de vulnerabilidad
- Detección de amenazas

Además de proteger sus máquinas virtuales de Azure, centro de seguridad proporciona la supervisión de seguridad y administración de servicios en la nube, servicios de aplicación, redes virtuales y mucho más. 

>[AZURE.NOTE] Vea [Introducción al centro de seguridad de Azure](security-center-intro.md) para obtener más información sobre el centro de seguridad de Azure.

## <a name="prerequisites"></a>Requisitos previos

Para empezar con el centro de seguridad de Azure, debe saber y considere lo siguiente:

- Debe tener una suscripción a Microsoft Azure. Para obtener más información sobre niveles gratuitas y estándares del centro de seguridad, consulte [Precios de centro de seguridad](https://azure.microsoft.com/pricing/details/security-center/) .
- Planear la adopción de centro de seguridad, consulte la [guían de planificación de centro de seguridad de Azure y operaciones](security-center-planning-and-operations-guide.md) para obtener más información sobre consideraciones de planeación y operaciones.
- Para obtener información sobre la compatibilidad del sistema operativo, consulte [Preguntas más frecuentes (p+f sobre) el centro de seguridad de Azure](security-center-faq.md). 

## <a name="set-security-policy"></a>Establecer la directiva de seguridad

Recopilación de datos debe estar habilitado para que ese centro de seguridad de Azure puede recopilar la información que necesita para proporcionar recomendaciones y alertas que se generan en función de la directiva de seguridad que configure. En la siguiente ilustración, puede ver que **la recopilación de datos** ha sido **activado**.

Una directiva de seguridad define el conjunto de controles que se recomiendan para recursos dentro de la suscripción especificada o grupo de recursos. Antes de habilitar la directiva de seguridad, debe tener habilitada la recopilación de datos, centro de seguridad recopila los datos de sus máquinas virtuales para evaluar su estado de seguridad, proporcionar recomendaciones de seguridad y alertarle sobre amenazas. En el centro de seguridad, defina las directivas de sus suscripciones Azure o los grupos de recursos según las necesidades de seguridad de su empresa y el tipo de aplicaciones o confidencialidad de los datos de cada suscripción. 

![Directiva de seguridad](./media/security-center-virtual-machine/security-center-virtual-machine-fig1.png)

>[AZURE.NOTE] Para obtener más información acerca de cada artículo [Configurar directivas de seguridad](security-center-policies.md) de **Directiva de prevención** disponible, consulte.

## <a name="manage-security-recommendations"></a>Administrar las recomendaciones de seguridad

Centro de seguridad analiza el estado de seguridad de los recursos de Azure. Cuando el centro de seguridad se identifica posibles vulnerabilidad de seguridad, crea recomendaciones. Las recomendaciones le guiará a través del proceso de configuración de los controles necesarios.

Después de establecer una directiva de seguridad, el centro de seguridad analiza el estado de seguridad de los recursos para identificar los puntos débiles potenciales. Las recomendaciones se muestran en un formato de tabla, donde cada línea representa una recomendación determinada. La siguiente tabla proporciona algunos ejemplos de recomendaciones para máquinas virtuales de Azure y lo que cada uno de ellos hará si se aplica. Cuando se selecciona una recomendación, se le proporcionará información que muestra cómo implementar la recomendación en el centro de seguridad.

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
| [Evaluación de vulnerabilidad no instalada](security-center-vulnerability-assessment-recommendations.md) | Recomienda instalar una solución de evaluación de vulnerabilidad en la máquina virtual. |
| [Corregir la vulnerabilidad](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Permite ver vulnerabilidad de sistema y la aplicación detecta la solución de evaluación de vulnerabilidad instalada en su máquina virtual. |

>[AZURE.NOTE] Para obtener más información sobre las recomendaciones, consulte el artículo de [recomendaciones de seguridad de administración](security-center-recommendations.md) .

## <a name="monitor-security-health"></a>Supervisar el mantenimiento de seguridad

Después de habilitar [las directivas de seguridad](security-center-policies.md) para los recursos de la suscripción, el centro de seguridad analizará la seguridad de los recursos para identificar los puntos débiles potenciales.  Puede ver el estado de seguridad de los recursos, junto con cualquier problema en el módulo de **estado de seguridad de recursos** . Al hacer clic en **máquinas virtuales de Windows** en el icono de estado de **seguridad del recurso** , se abrirá el módulo de **máquinas virtuales** con recomendaciones para sus máquinas virtuales. 

![Estado de seguridad](./media/security-center-virtual-machine/security-center-virtual-machine-fig2.png)

## <a name="manage-and-respond-to-security-alerts"></a>Administrar y responder a las alertas de seguridad

Centro de seguridad automáticamente recopila, analiza e integra datos de registro de los recursos de Azure, la red y las soluciones de asociados conectada (como firewall extremo soluciones de protección y), para detectar amenazas reales y reducir los falsos positivos. Aprovechando una agregación diversa de [funciones de detección](security-center-detection-capabilities.md), el centro de seguridad es capaz de generar las alertas de seguridad con prioridad para ayudarle a investigar el problema rápidamente y proporcionar recomendaciones acerca de cómo solucionar posibles ataques.

![Alertas de seguridad](./media/security-center-virtual-machine/security-center-virtual-machine-fig3.png)

Seleccione una alerta de seguridad para obtener más información sobre los eventos que activó la alerta y qué, si existe, pasos que debe seguir para corregir un ataque. Las alertas de seguridad se agrupan por [tipo](security-center-alerts-type.md) y la fecha.


## <a name="see-also"></a>Vea también

Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md) : Obtenga información sobre cómo configurar directivas de seguridad para suscripciones de Azure y grupos de recursos.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Preguntas más frecuentes sobre centro de seguridad de azure](security-center-faq.md) --buscar con frecuencia preguntas más frecuentes sobre el uso del servicio.
