<properties
   pageTitle="Centro de seguridad precios | Microsoft Azure"
   description="En este artículo se proporciona información sobre precios para el centro de seguridad de Azure."
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
   ms.date="10/12/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-pricing"></a>Precios de centro de seguridad de Azure

Centro de seguridad de Azure le ayuda a evitar, detectar y responder a las amenazas con una mayor visibilidad y control sobre la seguridad de los recursos de Azure. Proporciona seguridad integrada supervisión y la directiva de administración de las suscripciones de Azure, ayuda a detecta amenazas que pueden incluirse en caso contrario, problemas y funciona con un amplio ecosistema de soluciones de seguridad.

## <a name="pricing-tiers"></a>Niveles de precios

Centro de seguridad se ofrece en dos niveles:

- El **nivel gratuita** se habilita automáticamente en todas las suscripciones de Azure. El nivel gratuito proporciona visibilidad sobre el estado de seguridad de los recursos de Azure, directiva de seguridad básica, recomendaciones de seguridad e integración con productos de seguridad y servicios de socios.
- El **nivel estándar** agrega capacidades de detección de amenazas avanzadas, incluidos sobre amenazas, análisis de comportamiento, detección de anomalías, incidencias de seguridad y los informes de evaluación de amenazas. Una **versión de prueba gratuita de 90 días** está disponible para la capa estándar.

Para obtener más información, consulte el centro de seguridad, [página de precios](https://azure.microsoft.com/pricing/details/security-center/).

> [AZURE.NOTE] Centro de seguridad se utiliza almacenamiento de Azure para guardar los datos de seguridad generados a partir de los nodos protegidos. Los costos asociados con este almacenamiento no están incluidos en el precio del servicio y se cargarán por separado a normal [tasas de almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Aplicarán cargos de almacenamiento incluso durante la prueba.

## <a name="try-standard-free-for-90-days"></a>Intente estándar gratuita de 90 días

Una prueba gratuita de 90 días está disponible para la capa estándar. Para obtener la versión de prueba gratuita de la capa estándar, seleccione el icono de **Directiva** en el módulo de **Centro de seguridad** . Seleccione la suscripción que desea actualizar a estándar. En el módulo de **directivas de seguridad** , seleccione el **nivel de precios**. En el módulo de **Elegir el nivel de precios** , seleccione **estándar: versión de prueba gratuita**.

![Versión de prueba gratuita][1]

Al final de 90 días, si decide seguir utilizando el servicio se iniciará automáticamente esté cargando para uso.

## <a name="upgrade-to-standard"></a>Actualizar a estándar

Actualizar a nivel estándar para agregar la detección de amenazas avanzadas. Para obtener el nivel estándar, seleccione el icono de **Directiva** en el módulo de **Centro de seguridad** . Seleccione la suscripción que desea actualizar a estándar. En el módulo de **directivas de seguridad** , seleccione el **nivel de precios**. En el módulo de **Elegir el nivel de precios** , seleccione **estándar**.

![Nivel estándar][2]

## <a name="why-upgrade-to-standard"></a>¿Por qué actualizar a estándar?

El nivel estándar del centro de seguridad proporciona todas las características de la capa gratuita más detección avanzadas de amenazas. Detección avanzadas de amenazas ayuda a identificar amenazas activas dirige a los recursos de Azure y le ofrece la información necesaria para responder rápidamente.

Centro de seguridad se emplea el análisis de seguridad avanzada, que van más allá de los enfoques basados en la firma. Aprovechan avances en big data y tecnologías de aprendizaje para evaluar eventos a través de la estructura de la nube todo: detección de amenazas que serían posible identificar utilizando enfoques manuales y las predicciones la evolución de ataques.

Análisis de seguridad que vienen con el nivel estándar son:

- **Información sobre amenazas** - busca conocidos actores malas usando inteligencia amenaza global de productos de Microsoft y servicios, la unidad de delitos Digital de Microsoft, Microsoft Security Response Center y fuentes externas
- **Análisis de comportamiento** - aplica conocidos modelos para descubrir comportamiento malintencionado
- **Detección de anomalías** - utiliza perfiles estadístico para generar una línea de base histórica. Alerta de las desviaciones de las referencias establecidas que se ajustan a un tipo de ataque posibles

En el módulo de **las alertas de seguridad** a continuación, el centro de seguridad ha detectado un **incidente**de seguridad. Un incidente de seguridad es una agregación de todas las alertas para un recurso que se alinean con patrones de cadena de interrupción. Seleccionar el incidente de seguridad revela información más detallada sobre el incidente y enumera las alertas relacionadas. Seleccionar una alerta proporciona más información sobre esa repetición.

![Incidente de seguridad][3]

La alerta de **comunicación de red** siguiente proporciona información detallada acerca de la alerta. Detalles de incluyen su descripción completa, su gravedad, su estado actual (que en este caso se descarta, lo que significa que el usuario realiza la acción para descartarla), los recursos han y los pasos de corrección. También es una lista de vínculos a los informes de inteligencia de amenaza de Microsoft. Estos informes se pueden usar para fines defensivas y correcciones de seguridad.

![Detalles de alerta de seguridad][4]

## <a name="enable-data-collection"></a>Habilitar la recopilación de datos

Para habilitar el análisis de comportamiento de máquina virtual, recopilación de datos debe estar activada. Debe habilitar la recopilación de datos cuando acceda centro de seguridad por primera vez o cuando se crea una directiva de seguridad.

Para validar que está habilitada la recopilación de datos, seleccione el icono de la **Directiva** . El módulo de **directivas de seguridad** abre listado las suscripciones de Azure. Seleccione una suscripción. Si la **recopilación de datos** está desactivado, cambie a activado y guardar el cambio. Vea [Habilitar la recopilación de datos en el centro de seguridad de Azure](security-center-enable-data-collection.md).

## <a name="next-steps"></a>Pasos siguientes

- En este documento, se han introducido precios para el centro de seguridad. Para obtener información adicional sobre precios, consulte el centro de seguridad, [página de precios](https://azure.microsoft.com/pricing/details/security-center/).
- Para obtener más información sobre las capacidades avanzadas de detección del centro de seguridad, consulte [capacidades de detección de centro de seguridad de Azure](security-center-detection-capabilities.md).
- Si tiene preguntas sobre cómo usar el centro de seguridad, consulte las [Preguntas más frecuentes sobre centro de seguridad de Azure](security-center-faq.md).
- Si aún tiene preguntas sobre cómo usar el centro de seguridad o Azure, visite los [Foros de Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureSecurityCenter&filter=alltypes&sort=lastpostdesc).

<!--Image references-->
[1]: ./media/security-center-pricing/free-trial.png
[2]: ./media/security-center-pricing/standard.png
[3]: ./media/security-center-pricing/incident.png
[4]: ./media/security-center-pricing/network-alert.png
