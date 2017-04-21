<properties
   pageTitle="Corregir la vulnerabilidad OS en Centro de seguridad de Azure | Microsoft Azure"
   description="Este documento muestra cómo implementar la recomendación del centro de seguridad de Azure **corregir OS vulnerabilidad**."
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

# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>Corregir la vulnerabilidad OS en Centro de seguridad de Azure

Centro de seguridad de Azure diaria analiza su sistema operativo de máquina virtual (VM) (SO) para las configuraciones que pueden hacer que la máquina virtual más vulnerables y recomienda cambios de configuración para abordar estas vulnerabilidad. Para obtener más información acerca de las configuraciones específicas supervisado, consulte la [lista de reglas de la configuración recomendada](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Centro de seguridad, se recomienda resolver vulnerabilidad cuando la configuración del sistema operativo de la VM no coinciden con las reglas de la configuración recomendada.

> [AZURE.NOTE] Este documento presenta el servicio mediante el uso de una implementación de ejemplo.  No es una guía paso a paso.

## <a name="implement-the-recommendation"></a>Implementar la recomendación

1. En el módulo de **recomendaciones** , seleccione **corregir OS vulnerabilidad**.
![Corregir la vulnerabilidad OS][1]

    El módulo de **corregir OS vulnerabilidad** se abre y muestra sus máquinas virtuales con las configuraciones de sistema operativo que no coinciden con las reglas de la configuración recomendada.  Para cada VM e identifica el módulo:

   - **Reglas de error** : el número de reglas de error de configuración del sistema operativo de la máquina virtual.
   - **Análisis de última hora** , la fecha y hora que el centro de seguridad había capturado última configuración del sistema operativo de la máquina virtual.
   - **Estado** : el estado actual de la vulnerabilidad:

        - Abrir: La vulnerabilidad no se resuelve todavía
        - En curso: La vulnerabilidad aplicada actualmente, se requiere ninguna acción por el usuario
        - Resolver: La vulnerabilidad ya se ha tratado (cuando se haya resuelto el problema, la entrada aparece atenuada)
  - **Gravedad** --vulnerabilidad todos está establecidos gravedad baja, lo que significa que debe solucionarse una vulnerabilidad, pero no requiere atención inmediata.

Seleccione una máquina virtual. Un módulo para dicha máquina virtual se abre y muestra las reglas que no han pasado.
   ![Reglas de configuración que han fallado][2]

Seleccione una regla. En este ejemplo, permite seleccionar **contraseña debe cumplir los requisitos de complejidad**. Abre un módulo que describa la regla con errores y el impacto. Revise los detalles y tener en cuenta cómo se aplicarán las configuraciones de sistema operativo.
  ![Descripción de la regla con errores][3]

  Centro de seguridad se utiliza enumeración de configuración común (CCE) para asignar identificadores únicos para las reglas de configuración. En este módulo se proporciona la información siguiente:

  - NOMBRE: Nombre de regla
  - GRAVEDAD--Valor CCE gravedad de crítica, importante o de advertencia
  - CCIED--Identificador único CCE para la regla
  - Descripción: Una descripción de regla
  - VULNERABILIDAD--Explicación de vulnerabilidad o riesgo si no se aplica la regla
  - IMPACTO, Impacto en el negocio cuando se aplica la regla
  - VALOR esperado: Valor que se esperaba al centro de seguridad analiza la configuración del sistema operativo de VM con la regla
  - OPERACIÓN de regla: Operación utilizada por el centro de seguridad durante el análisis de la configuración del sistema operativo de VM con la regla de la regla
  - El valor real, Valor devuelto después de análisis de la configuración del sistema operativo de VM con la regla
  - RESULTADO de la evaluación:-resultado del análisis: pasar éxito


## <a name="see-also"></a>Vea también

En este artículo le mostramos cómo implementar la recomendación del centro de seguridad "Remediate OS vulnerabilidad". Puede revisar el conjunto de reglas de configuración [aquí](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Centro de seguridad se utiliza CCE (enumeración de configuración común) para asignar identificadores únicos para las reglas de configuración. Visite el sitio [CCE](http://cce.mitre.org) para obtener más información.

Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md) : Obtenga información sobre cómo configurar directivas de seguridad para suscripciones de Azure y grupos de recursos.
- [Recomendaciones de seguridad de la administración de centro de seguridad de Azure](security-center-recommendations.md) : Descubra cómo recomendaciones ayudar a proteger los recursos de Azure.
- [Supervisión del estado de seguridad del centro de seguridad de Azure](security-center-monitoring.md) : Obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Las soluciones de asociados de supervisión con el centro de seguridad de Azure](security-center-partner-solutions.md) : Obtenga información sobre cómo supervisar el estado de las soluciones de socios.
- [Preguntas más frecuentes sobre centro de seguridad de azure](security-center-faq.md) --buscar con frecuencia preguntas más frecuentes sobre el uso del servicio.
- Entradas de [blog de seguridad de azure](http://blogs.msdn.com/b/azuresecurity/) --buscar blog sobre cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/recommendation.png
[2]:./media/security-center-remediate-os-vulnerabilities/vm-remediate-os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
