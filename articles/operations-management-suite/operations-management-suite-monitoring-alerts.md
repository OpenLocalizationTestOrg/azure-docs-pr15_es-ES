<properties 
   pageTitle="Alerta de administración de productos de supervisión de Microsoft | Microsoft Azure"
   description="Una alerta indica algún problema que necesita atención del administrador.  Este artículo describe las diferencias en cómo se crean y se administran en System Center Operations Manager (SCOM) y el análisis de registro de alertas y proporciona mejores prácticas para aprovechar los dos productos para una estrategia de administración de alertas híbrido." 
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="bwren" />

# <a name="managing-alerts-with-microsoft-monitoring"></a>Administrar alertas con la supervisión de Microsoft 

Una alerta indica algún problema que necesita atención del administrador.  Hay diferencias inequívocas entre System Center Operations Manager (SCOM) y el análisis de registro en conjunto de aplicaciones de administración de operaciones (OMS) en función de cómo se crean alertas, ¿cómo se administran y se analizan y cómo se le notifica que se ha detectado un problema crítico.

## <a name="alerts-in-operations-manager"></a>Alertas de Operations Manager
Alertas en SCOM generan reglas individuales o monitores para indicar un problema específico.  Un monitor puede generar una alerta cuando entra en un estado de error cuando una regla puede generar una alerta para indicar algún problema crítico que no está directamente relacionado con el estado de un objeto administrado.  Paquetes de administración incluyen una variedad de flujos de trabajo que crear alertas para la aplicación o servicio que administran.  Parte del proceso de configurar un nuevo módulo de administración ajuste para asegurarse de que no recibe excesivo alertas para los problemas que no considere crítico.

![Vista de alerta de SCOM](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

SCOM proporciona administración de alerta completa con las alertas de estado que se pueden cambiar por los administradores a medida que trabajan para resolver el problema.  Cuando se ha resuelto el problema, el administrador configura la alerta que cerrar momento en que ya no aparecerá en las vistas Mostrar alertas activas.  Alertas que se generan desde monitores se pueden resolver automáticamente cuando el monitor vuelve a un estado correcto.

![Estado de alerta](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>Alertas de análisis de registro
Se crea una alerta de análisis de registro de una consulta de registro que se ejecute automáticamente a intervalos regulares.  Puede crear una regla de alerta desde cualquier consulta del registro.  Si la consulta devuelve los resultados que cumplen los criterios que especifique, se crea una alerta.  Esto puede deberse a una consulta específica que crea una alerta si se detecta un evento concreto o podría usar una consulta más general que busca cualquier evento de error relacionado con una aplicación en particular.

Alertas de análisis de registro se escriben en el repositorio OMS como un evento y se pueden recuperar con una consulta de registro.  No tiene un estado como SCOM eventos por lo que puede indicar cuando se ha resuelto el problema.

![Alerta OMS](media/operations-management-suite-monitoring-alerts/oms-alert.png)

Cuando SCOM se usa como origen de datos para el análisis de registro, alertas SCOM se escriben en el repositorio OMS como creación y modificación.  

![Alerta SCOM](media/operations-management-suite-monitoring-alerts/scom-alert.png)

La [solución de administración de alertas](http://technet.microsoft.com/library/mt484092.aspx) proporciona un resumen de alertas activas y varias consultas comunes para recuperar diferentes conjuntos de alertas.  Esto le ofrece más eficaces análisis de las alertas de un informe en SCOM.  Puede rastrear desagrupando datos hasta de los resúmenes a datos detallados y crear consultas ad hoc para recuperar diferentes conjuntos de alertas.

![Solución de administración de alertas](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>Notificaciones
Notificaciones en SCOM enviar un correo o el texto en respuesta a las alertas que coinciden con un criterio determinado.  Puede crear las suscripciones de notificación diferentes que tienen diferentes personas una notificación según criterios como el objeto supervisado, la gravedad de la alerta, el tipo de problema detectado o la hora del día.

Algunas suscripciones pueden usarse para implementar una estrategia de notificación completa para una gran cantidad de paquetes de administración.

![Alertas SCOM](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

Análisis de registro puede notificar por correo electrónico que se ha creado una alerta cuando se establece una acción de notificación de correo electrónico en cada [regla de alerta](http://technet.microsoft.com/library/mt614775.aspx).  No tiene la capacidad de SCOM la suscribirse a varias alertas con una sola regla.  También debe crear sus propias reglas de alerta desde OMS no proporciona ninguna preconfigurado.

![Alertas de análisis de registro](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

Completamente no puede administrar las alertas de SCOM de análisis de registro aunque ya que sólo se puede modificar en la consola de operaciones.  Análisis de registro es útil como parte de una alerta de administración de proceso aunque para proporcionar herramientas de análisis que SCOM solo no tiene.

## <a name="alert-remediation"></a>Corrección de alerta
[Corrección](http://technet.microsoft.com/library/mt614775.aspx) se refiere a un intento de corregir automáticamente el problema identificado mediante una alerta.
  
SCOM permite ejecutar diagnósticos y recuperación en respuesta a un monitor escribir mal estado.  Esto ocurre simultánea al monitor de creación de la alerta.  Diagnósticos y recuperación se implementa normalmente como una secuencia de comandos que se ejecuta en el agente.  Diagnóstico intenta recopilar más información sobre el problema detectado mientras intenta una recuperación corregir el problema.

Análisis de registro le permite iniciar un [runbook de automatización de Azure](https://azure.microsoft.com/documentation/services/automation/) o llamar a un webhook en respuesta a una alerta de análisis de registro.  Runbooks puede contener lógica compleja que se ha implementado en PowerShell.  La secuencia de comandos se ejecuta en Azure y puede tener acceso a cualquier recursos Azure o recursos externos disponibles de la nube.  Automatización de Azure tienen la capacidad de ejecutar runbooks en un servidor en el centro de datos local, pero esta característica no está disponible actualmente al iniciar runbook en respuesta a las alertas de análisis de registro.

Recuperación de SCOM tanto runbooks en OMS puede contener secuencias de comandos de PowerShell, pero es más difícil de crear y administrar porque debe estar dentro de un módulo de administración de recuperación.  Runbooks se almacenan en automatización de Azure que proporciona características para crear, probar y administrar runbooks.

Si utiliza SCOM como origen de datos para el análisis de registro, puede crear una alerta de análisis de registro mediante una consulta de registro para recuperar las alertas SCOM almacenadas en el repositorio OMS.  Esto le permitirá ejecutar un runbook de automatización de Azure en respuesta a una alerta SCOM.  Por supuesto, ya que se ejecutará runbook en Azure, no sería una estrategia viable de recuperación de problemas local.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre los detalles de [las alertas de System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh212913.aspx).