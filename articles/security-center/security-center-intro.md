<properties
   pageTitle="Introducción al centro de seguridad de Azure | Microsoft Azure"
   description="Obtenga información sobre el centro de seguridad de Azure, sus capacidades claves y cómo funciona."
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
   ms.date="07/21/2016"
   ms.author="terrylan"/>

# <a name="introduction-to-azure-security-center"></a>Introducción al centro de seguridad de Azure

Obtenga información sobre el centro de seguridad de Azure, sus capacidades claves y cómo funciona.

> [AZURE.NOTE] Este documento presenta el servicio mediante el uso de una implementación de ejemplo.

## <a name="what-is-azure-security-center"></a>¿Qué es el centro de seguridad de Azure?
 Centro de seguridad le ayuda a evitar, detectar y responder a las amenazas con una mayor visibilidad y control sobre la seguridad de los recursos de Azure. Proporciona seguridad integrada supervisión y la directiva de administración de las suscripciones de Azure, ayuda a detecta amenazas que pueden incluirse en caso contrario, problemas y funciona con un amplio ecosistema de soluciones de seguridad.

##  <a name="key-capabilities"></a>Capacidades clave
 Centro de seguridad ofrece capacidades de prevención, detección y respuesta que están integradas en amenaza fácil de usar y eficaz en Azure. Capacidades clave son:

| | |
|----- |-----|
| Evitar | Supervisa el estado de seguridad de los recursos de Azure |
| | Defina las directivas para suscripciones de Azure y grupos de recursos en función de los requisitos de seguridad de su empresa, los tipos de aplicaciones que usa y la sensibilidad de los datos |
| | Recomendaciones de seguridad de usos basado en directivas para guiar a los propietarios de servicio a través del proceso de implementación es necesario controles |
| | Implementa rápidamente los servicios de seguridad y dispositivos de Microsoft y socios |
| Detectar |Recopila y analiza los datos de seguridad de los recursos de Azure, la red y soluciones de socios como programas antimalware y los firewalls automáticamente |
| | Aprovecha global amenazas inteligencia de productos de Microsoft y servicios, Digital delitos unidad (DCU) de Microsoft, el centro de respuesta de seguridad de Microsoft (MSRC) y fuentes externas |
| | Se aplica a funciones analíticas avanzadas, incluidos el aprendizaje y análisis de comportamiento |
| Responder | Proporciona incidencias y alertas de seguridad con prioridad |
| | Ofrece recomendaciones sobre el origen del ataque y los recursos afectados |
| | Sugiere formas de detener el ataque actual y ayudar a evitar ataques futuros |

## <a name="introductory-walkthrough"></a>Tutorial introductorio
 Obtener acceso a centro de seguridad desde el [portal de Azure](https://azure.microsoft.com/features/azure-portal/). [Inicie sesión en el portal](https://portal.azure.com), seleccione **Examinar**y, a continuación, vaya a la opción de **Centro de seguridad** o seleccione el icono de **Centro de seguridad** previamente anclados a panel portal.

![Mosaico de seguridad en el portal de Azure][1]

Desde el centro de seguridad, puede establecer directivas de seguridad, supervisar las configuraciones de seguridad y ver las alertas de seguridad.

### <a name="security-policies"></a>Directivas de seguridad

Puede definir directivas para suscripciones de Azure y grupos de recursos según los requisitos de seguridad de su empresa. También puede personalizar los tipos de aplicaciones que está usando o de la sensibilidad de los datos de cada suscripción. Por ejemplo, recursos utilizados para desarrollo o pruebas pueden tener distintos requisitos de seguridad de los empleados para aplicaciones de producción. Del mismo modo, las aplicaciones con datos regulados como PII pueden requerir un nivel superior de seguridad.

> [AZURE.NOTE] Para modificar una directiva de seguridad en el nivel de la suscripción o el nivel de grupo de recursos, debe ser el propietario de la suscripción o un colaborador en él.

En el módulo de **Centro de seguridad** , seleccione el mosaico de la **Directiva** de una lista de las suscripciones y grupos de recursos.   

![Módulo de centro de seguridad][2]

En el módulo de **directivas de seguridad** , seleccione una suscripción para ver los detalles de la directiva.

![Suscripción de módulo de directivas de seguridad][3]

**Recopilación de datos** (vea lo anterior) permite la recopilación de datos de una directiva de seguridad. Habilitar proporciona:

- Análisis diaria de todos los compatibles máquinas virtuales de supervisión de seguridad y recomendaciones.
- Colección de eventos de seguridad para la detección de análisis y amenazas.

**Elegir una cuenta de almacenamiento por región** (vea lo anterior) le permite elegir, para cada región en la que tiene máquinas virtuales que ejecutan, la cuenta de almacenamiento donde se almacenan los datos recopilados de esas máquinas virtuales. Si elige una cuenta de almacenamiento para cada región, se creará para usted. Los datos que se recopilan están lógicamente aislados de los datos de otros clientes por motivos de seguridad.

> [AZURE.NOTE] Elegir una cuenta de almacenamiento por región y recopilación de datos está configurado en el nivel de suscripción.

Seleccione la **Directiva de prevención** (vea lo anterior) para abrir el módulo de **directivas de prevención** . **Mostrar recomendaciones para** le permite elegir los controles de seguridad que desea supervisar y recomendamos según las necesidades de seguridad de los recursos de la suscripción.

A continuación, seleccione un grupo de recursos para ver los detalles de la directiva.

![Grupo de recursos de módulo de directivas de seguridad][4]

**Herencia** (vea lo anterior) le permite definir el grupo de recursos como:

- Heredado (predeterminado) lo que significa que todas las directivas de seguridad para este grupo de recursos se heredan desde el nivel de suscripción.
- Único lo que significa que el grupo de recursos tendrá una directiva de seguridad personalizado. Debe realizar cambios en **Mostrar recomendaciones para**.

> [AZURE.NOTE] Si hay un conflicto entre la directiva de nivel de suscripción y la directiva de nivel de grupo de recursos, la directiva de nivel de grupo de recursos tiene prioridad.

### <a name="security-recommendations"></a>Recomendaciones de seguridad

 Centro de seguridad analiza el estado de seguridad de los recursos de Azure para identificar los posibles puntos débiles de seguridad. Una lista de recomendaciones le guiará a lo largo del proceso de configuración de controles necesarios. Algunos ejemplos:

- Aprovisionamiento antimalware para ayudar a identificar y quitar software malintencionado
- Configuración de grupos de seguridad de red y reglas para controlar el tráfico a máquinas virtuales
- Aprovisionamiento de servidores de aplicaciones web para ayudar a defender contra ataques destinados a las aplicaciones web
- Implementar las actualizaciones de sistema que faltan
- Escribir direcciones en las configuraciones de sistema operativo que no coinciden con las previsiones recomendadas

Haga clic en el mosaico de **recomendaciones** para obtener una lista de recomendaciones. Haga clic en cada recomendación para ver información adicional o tomar medidas para resolver el problema.

![Recomendaciones de seguridad en el centro de seguridad de Azure][5]

### <a name="resource-health"></a>Estado de los recursos

El icono de **estado de seguridad de recursos** muestra la posición de seguridad general del entorno por tipo de recurso, incluidos equipos virtuales, aplicaciones web y otros recursos.   

Seleccione un tipo de recurso en el mosaico de **Mantenimiento de seguridad de recursos** para ver más información, incluida una lista de cualquier vulnerabilidad de seguridad potenciales que se han identificado. (**Máquinas virtuales** está seleccionada en el ejemplo siguiente).

![Icono de estado de recursos][6]

### <a name="security-alerts"></a>Alertas de seguridad

 Centro de seguridad automáticamente recopila, analiza e integra datos de registro de los recursos de Azure, la red y soluciones de socios como programas antimalware y los firewalls. Cuando se detectan amenazas, se crea una alerta de seguridad. Detección de algunos ejemplos:

- Comprometido máquinas virtuales de Windows comunicarse con las direcciones IP malintencionadas conocidas
- Avanzadas malware detectado utilizando el informe de errores de Windows
- Ataques contra máquinas virtuales de Windows
- Alertas de seguridad de los firewalls y programas de antimalware integrado

Haga clic en el mosaico de **las alertas de seguridad** , se muestra una lista de alertas con prioridad.

![Alertas de seguridad][7]

Seleccionar una alerta, muestra más información sobre el ataque y sugerencias sobre cómo corregirlo.

![Detalles de alerta de seguridad][8]

### <a name="partner-solutions"></a>Soluciones de asociados

El mosaico de **soluciones de socios** le permite a monitor de un vistazo el estado de las soluciones de socios integrado con su suscripción de Azure. Centro de seguridad muestra alertas procedentes de las soluciones.

Seleccione el icono de **soluciones de socios** . Un módulo abrirá una lista de todas las soluciones de socios conectados.

![Soluciones de asociados][9]

## <a name="get-started"></a>Introducción
Para empezar con el centro de seguridad, necesita una suscripción a Microsoft Azure. Centro de seguridad está habilitado con su suscripción de Azure. Si no tiene una suscripción, puede registrarse para una [prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).

 Obtener acceso a centro de seguridad desde el [portal de Azure](https://azure.microsoft.com/features/azure-portal/). Consulte la [documentación del portal](https://azure.microsoft.com/documentation/services/azure-portal/) para obtener más información.

[Introducción a centro de seguridad de Azure](security-center-get-started.md) guiará rápidamente a través de la supervisión de seguridad y componentes de administración de directivas de centro de seguridad.

## <a name="see-also"></a>Vea también
En este documento, se han introducido al centro de seguridad, sus capacidades claves y cómo empezar a trabajar. Para obtener más información, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md) : Obtenga información sobre cómo configurar directivas de seguridad para suscripciones de Azure y grupos de recursos.
- [Administrar las recomendaciones de seguridad en el centro de seguridad de Azure](security-center-recommendations.md) : Descubra cómo recomendaciones ayudar a proteger los recursos de Azure.
- [Supervisión en el centro de seguridad de Azure de estado de seguridad](security-center-monitoring.md) : Obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Supervisar las soluciones de asociados con el centro de seguridad de Azure](security-center-partner-solutions.md) : Obtenga información sobre cómo supervisar el estado de las soluciones de socios.
- [Preguntas más frecuentes sobre centro de seguridad de Azure](security-center-faq.md) : preguntas más frecuentes sobre el uso del servicio de búsqueda.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : obtener las últimas noticias de seguridad de Azure y la información.

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.png
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png
