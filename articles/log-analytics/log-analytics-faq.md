<properties
    pageTitle="Iniciar el análisis de las preguntas más frecuentes | Microsoft Azure"
    description="Respuestas a las preguntas más frecuentes sobre el servicio de análisis de registro."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="log-analytics-faq"></a>Preguntas más frecuentes sobre el análisis de registro

Este FAQ Microsoft es una lista de preguntas más frecuentes sobre el análisis de registro en Microsoft operaciones Management Suite (OMS). Si tiene preguntas adicionales sobre el análisis de registro, vaya al [foro de discusión](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) y publique sus preguntas. Alguien de nuestra comunidad le ayudarán a sus respuestas. Si una pregunta frecuentes, se agregará a este artículo para que se puede encontrar rápida y fácilmente.

## <a name="general"></a>General

**P. ¿qué comprobaciones realizadas por AD y soluciones de evaluación de SQL?**

A. La consulta siguiente muestra una descripción de todas las comprobaciones que llevan a cabo actualmente:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Los resultados se pueden exportar a Excel para revisión posterior.

* *P: ¿por qué veo algo diferente a *OMS* en SCOM Administration? **

R: dependiendo de qué Update Rollup de SCOM que esté usando, puede ver un nodo para *Asesor de centro de sistema*, *Perspectivas operativas*o *El análisis de registro*.

La actualización de la cadena de texto a *OMS* se incluye en un módulo de administración, que es necesario importar manualmente. Siga las instrucciones en el artículo de KB de SCOM Update Rollup más reciente y actualizar la consola OMS para ver las actualizaciones más recientes en el nodo *OMS* .

* *P: ¿hay un *local* versión de OMS? **

R: no. Análisis de registro se procesa y almacena muy grandes cantidades de datos. Como un servicio de nube, es posible escalado si es necesario y evitar cualquier impacto de rendimiento en su entorno de análisis de registro.

Además, un medio de servicio de nube no es necesario mantener la infraestructura de análisis de registro y ejecutando puede recibir actualizaciones de características frecuentes y soluciones.

## <a name="configuration"></a>Configuración
**P. ¿puedo cambiar el nombre del contenedor de blob/tabla utilizado para leer de Azure diagnósticos (TORUNDA)?**  

A.  No, esto no es posible actualmente, pero se han planificado para una versión futura.

**P. ¿qué direcciones hacer el uso de servicios OMS? ¿Cómo me aseguro de que mi servidor de seguridad sólo permite el tráfico a los servicios de OMS?**  

A. El servicio de análisis de registro se basa en Azure y los extremos reciban direcciones IP que están en los [Intervalos de IP de centro de datos de Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=41653).

Cuando se realizan implementaciones de servicio, cambian las direcciones IP reales de los servicios OMS. Los nombres DNS para permitir a través del firewall se describen en [establecer la configuración del servidor proxy y el firewall de análisis de registro](log-analytics-proxy-firewall.md).

**P. ¿puedo usar ExpressRoute para conectarse a Azure. ¿Mi tráfico de análisis de registro usará mi conexión de ExpressRoute?**  

A. Los diferentes tipos de tráfico de ExpressRoute se describen en la [documentación de ExpressRoute](./expressroute/expressroute-faqs.md#supported-services).

Tráfico de análisis de registro utiliza el circuito de ExpressRoute interconexión público.

**P. ¿hay una forma sencilla y fácil para mover un área de trabajo de análisis de registro existente a otra suscripción de Azure o área de trabajo de análisis de registro?**  En nuestro suscripción Azure tenemos áreas de trabajo OMS de varios clientes que se nos probando y someter a prueba y están moviendo a producción por lo que deseamos moverlos a su propia suscripción Azure/OMS.  

A. La `Move-AzureRmResource` cmdlet le permitirá mover un área de trabajo de análisis de registro y también una cuenta de automatización de una suscripción de Azure a otro. Para obtener más información, vea [Mover AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

También se puede realizar este cambio en el portal de Azure.

No puede mover datos de un área de trabajo de análisis de registro a otro, o cambiar la región de datos de análisis de registro se almacenan en.

**P: ¿Cómo puedo agregar OMS a SCOM?**

R: actualizar a la última actualización de rollup e importar módulos de administración le permitirá conectarse SCOM a análisis de registro.

Observe que la conexión de SCOM al análisis de registro solo está disponible para SCOM 2012 SP1 o superior.

**P: ¿Cómo puedo confirmar que un agente es capaz de comunicarse con el análisis de registro?**

R: para asegurarse de que el agente puede comunicarse con OMS, vaya a: Panel de Control, seguridad y configuración, **Agente de supervisión de Microsoft**.

En la pestaña de **Análisis de registro de Azure (OMS)** , busque una marca de verificación verde. Un icono de marca de verificación verde confirma que el agente está disponible para comunicarse con el servicio OMS.

Un icono de advertencia indica que el agente está teniendo problemas de comunicación con OMS. Una razón común es el servicio Agente de supervisión de Microsoft se ha detenido y es necesario reiniciar.

**P: ¿Cómo puedo dejar un agente se comunique con el análisis de registro?**

R: en SCOM, quite el equipo de la lista OMS administrada. Esto detiene todas las comunicaciones a través de SCOM para que el agente. Para los agentes conectados a OMS directamente, puede dejar se comunique con OMS a través de: Panel de Control, seguridad y configuración, **Agente de supervisión de Microsoft**.
En **Análisis de registro de Azure (OMS)**, quite todas las áreas de trabajo de lista.

## <a name="agent-data"></a>Datos de agente

**P. ¿cuántos datos puedo enviar a través del agente al análisis de registro? ¿Hay una cantidad máxima de datos por cliente?**  
A. El plan libre establece un extremo diario de 500 MB por área de trabajo. Los planes estándar y premium no tienen límite en la cantidad de datos que se cargan. Como un servicio de nube análisis de registro en OMS diseñado para escalar automáticamente el volumen del controlador procedentes de un cliente, aunque es terabytes por día.

El agente de análisis de registro se ha diseñado para garantizar que tiene un diseño compacto y hace alguna compresión de datos básico. Uno de nuestros clientes escribió realmente un blog en las pruebas que realiza en nuestro agente y cómo impresionado fueran. El volumen de datos varían en función de las soluciones permite a los clientes. Puede encontrar información detallada sobre el volumen de datos y ver la división por solución en el **uso** de mosaico en la página de información general OMS.

Para obtener más información, puede leer un [cliente blog](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) sobre la huella bajo del agente OMS.

**P. ¿cuánto ancho de banda de red se utiliza el agente de administración de Microsoft (MMA) al enviar datos al análisis de registro?**

A. Ancho de banda es una función de la cantidad de datos enviados. Los datos se comprimen cuando se envían a través de la red.

**P. ¿cuántos datos se envían por agente?**

A. Esto depende en gran medida:

- las soluciones que se ha habilitado
- el número de registros y que se recopilan los contadores de rendimiento
- el volumen de datos en los registros

El nivel de precio libre es una buena manera incorporado varios servidores y el volumen de datos típico de indicadores. Total uso se muestra en la página de **uso** .
Para equipos que se pueden ejecutar el agente de WireData, puede ver la cantidad de datos se envía con la siguiente consulta:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```



## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de registro](log-analytics-get-started.md) para obtener más información sobre el análisis de registro y póngase en marcha en minutos.
