<properties
   pageTitle="Guía de inicio rápido de centro de seguridad de Azure | Microsoft Azure"
   description="Este artículo le ayudará a empezar rápidamente con el centro de seguridad de Azure guiarle a través de los componentes de administración de supervisión y directivas de seguridad y vínculos a los pasos siguientes."
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
   ms.date="10/28/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-quick-start-guide"></a>Guía de inicio rápido de centro de seguridad de Azure

Este artículo le ayudará a comenzar rápidamente con el centro de seguridad de Azure al guiarle a través de los componentes de administración de supervisión y la directiva de seguridad del centro de seguridad.

> [AZURE.NOTE] En este artículo se presenta el servicio mediante el uso de una implementación de ejemplo. En este artículo no es una guía paso a paso.

## <a name="prerequisites"></a>Requisitos previos

Para empezar con el centro de seguridad, debe tener una suscripción a Microsoft Azure. Si no tiene una suscripción, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).

El nivel gratuito del centro de seguridad se habilita automáticamente con la suscripción y proporciona visibilidad del estado de seguridad de los recursos de Azure. Proporciona administración de directivas de seguridad básica, recomendaciones de seguridad y la integración con productos de seguridad y servicios de asociados de Azure.

Obtener acceso a centro de seguridad desde el [portal de Azure](https://azure.microsoft.com/features/azure-portal/). Para obtener más información sobre el portal de Azure, consulte la [documentación del portal](https://azure.microsoft.com/documentation/services/azure-portal/).

## <a name="data-collection"></a>Recopilación de datos

Centro de seguridad recopila los datos de sus máquinas virtuales (VM) para evaluar su estado de seguridad, proporcionar recomendaciones de seguridad y alertarle sobre amenazas. Cuando acceda por primera vez el centro de seguridad, recopilación de datos está habilitada en todas las máquinas virtuales en su suscripción. Se recomienda la recopilación de datos, pero puede cambiar de opinión desactivando la recopilación de datos en la directiva de centro de seguridad.

Los pasos siguientes describen cómo acceder y utilizar los componentes del centro de seguridad. En estos pasos, le mostraremos cómo desactivar la recopilación de datos si decide cambiar de opinión.

## <a name="access-security-center"></a>Centro de seguridad de Access

En el portal, siga estos pasos para tener acceso al centro de seguridad:

1. En el menú de **Microsoft Azure** , seleccione **Centro de seguridad**.
![Menú de Azure][1]

2. Si tiene acceso a centro de seguridad por primera vez, se abrirá el módulo de **bienvenida** . ¡Seleccione Sí **! Deseo inicio centro de seguridad de Azure** para abrir el módulo de **Centro de seguridad** y para habilitar la recopilación de datos.
![Pantalla de bienvenida][10]

3. Después de iniciar el centro de seguridad desde el módulo de bienvenida o seleccione Centro de seguridad en el menú de Microsoft Azure, se abre el módulo de **Centro de seguridad** . Para facilitar el acceso a los módulos del **Centro de seguridad** en el futuro, seleccione la opción de **módulo Pin paneles** (parte superior derecha).
![Módulo de PIN para la opción de paneles][2]

## <a name="use-security-center"></a>Use el centro de seguridad

Puede configurar directivas de seguridad para suscripciones de Azure y grupos de recursos. Vamos a configurar una directiva de seguridad para su suscripción:

1. En el módulo de **Centro de seguridad** , seleccione el mosaico de la **Directiva** .
![Directiva de seguridad][3]

2. En el módulo de **Directiva de seguridad: definir la directiva de grupo de recursos o de suscripción** , seleccione una suscripción.
3. En el módulo de **directivas de seguridad** , **recopilación de datos** está habilitada para recopilar automáticamente los registros. La extensión de supervisión se aprovisiona en todas las máquinas virtuales actuales y la nuevas en la suscripción. (Puede anular la recopilación de datos mediante la configuración de **recopilación de datos** para **desactivar**, pero esto impide que el centro de seguridad que le da recomendaciones y las alertas de seguridad).
4. En el módulo de **directivas de seguridad** , seleccione **Elegir una cuenta de almacenamiento por región**. Para cada región en la que tiene máquinas virtuales con, elija la cuenta de almacenamiento donde se almacenan los datos recopilados de esas máquinas virtuales. Si elige una cuenta de almacenamiento para cada región, se crea por usted. Los datos que se recopilan están lógicamente aislados de los datos de otros clientes por motivos de seguridad.

     > [AZURE.NOTE] Le recomendamos que habilite la recopilación de datos y elija una cuenta de almacenamiento en el nivel de la suscripción en primer lugar. Se pueden establecer directivas de seguridad en el nivel de suscripción de Azure y un nivel de grupo de recursos, pero la configuración de cuenta de almacenamiento y recopilación de datos se produce en el nivel de suscripción solo.

5. En el módulo de **directivas de seguridad** , seleccione la **Directiva de prevención**. Se abrirá el módulo de **directivas de prevención** .
![Directiva de prevención][4]

6. En el módulo de **directivas de prevención** , active las recomendaciones que desea ver como parte de la directiva de seguridad. Ejemplos:

 - Configurar **las actualizaciones del sistema** **en** analiza todos los equipos virtuales compatibles para las actualizaciones de sistema operativo que faltan.
 - Configurar **vulnerabilidad OS** **en** analiza todos los equipos virtuales compatibles para identificar las configuraciones de sistema operativo que pueden hacer que la máquina virtual más vulnerables.

### <a name="view-recommendations"></a>Recomendaciones para la vista

1. Volver a la hoja de **Centro de seguridad** y seleccione el icono de **recomendaciones** . Centro de seguridad periódicamente analiza el estado de seguridad de los recursos de Azure. Cuando el centro de seguridad se identifica posibles vulnerabilidad de seguridad, muestra recomendaciones en el módulo de **recomendaciones** .
![Recomendaciones en Centro de seguridad de Azure][5]

2.  Seleccione una recomendación en el módulo de **recomendaciones** para obtener más información o tomar medidas para resolver el problema.

### <a name="view-the-health-and-security-state-of-your-resources"></a>Ver el estado de salud y seguridad de los recursos

1.  Volver a la hoja de **Centro de seguridad** . El icono de **estado de seguridad de recursos** contiene los indicadores de estado de seguridad de máquinas virtuales, redes, datos y aplicaciones.
2.  Seleccione **máquinas virtuales de Windows** para obtener más información. El módulo de **máquinas virtuales de Windows** se abre y muestra un resumen de estado de los programas antimalware, actualizaciones del sistema, se reinicia y vulnerabilidad OS de sus máquinas virtuales.
![El icono de estado de recursos en el centro de seguridad de Azure][6]

3.  Seleccione una recomendación en **Máquina VIRTUAL recomendaciones** para obtener más información o tomar medidas para configurar los controles necesarios.
4.  Seleccione una máquina virtual en **máquinas virtuales de Windows** para ver detalles adicionales.

### <a name="view-security-alerts"></a>Ver las alertas de seguridad

1.  Volver a la hoja de **Centro de seguridad** y seleccione el icono de **las alertas de seguridad** . El módulo de **las alertas de seguridad** se abre y muestra una lista de las alertas. El análisis del centro de seguridad de los registros de seguridad y la actividad de la red genera estas alertas. Se incluyen las alertas de soluciones integradas de socios.
![Alertas de seguridad en el centro de seguridad de Azure][7]

    > [AZURE.NOTE] Solo están disponibles si está habilitada la capa estándar del centro de seguridad de las alertas de seguridad. Una prueba gratuita de 90 días de la capa estándar está disponible. Vea los [pasos siguientes](#next-steps) para obtener información sobre cómo obtener el nivel estándar.

2.  Seleccione una alerta para ver información adicional. En este ejemplo, seleccionaremos **detectó modificado sistema binario**. Esto abrirá aspas que proporcionan información adicional acerca de la alerta.
![Detalles de alerta de seguridad en el centro de seguridad de Azure][8]

### <a name="view-the-health-of-your-partner-solutions"></a>Ver el estado de las soluciones de socios

1. Volver a la hoja de **Centro de seguridad** . El mosaico de **soluciones de socios** le permite controlar de un vistazo, el estado de las soluciones de socios integrado con su suscripción de Azure.
2. Seleccione el icono de **soluciones de socios** . Un módulo se abre y muestra una lista de las soluciones de socios conectado al centro de seguridad.
![Soluciones de asociados][9]

3. Seleccione una solución asociado. En este ejemplo, seleccionaremos la solución **WAFS F5** .  Un módulo se abre y muestra el estado de la solución de partner y recursos asociados de la solución. Seleccione **consola de soluciones** para abrir la experiencia de administración de partners para esta solución.

## <a name="next-steps"></a>Pasos siguientes
En este artículo había presentado los componentes de administración de supervisión y la directiva de seguridad del centro de seguridad. Ahora que ya está familiarizado con el centro de seguridad, intente los siguientes pasos:

- Configurar una directiva de seguridad para su suscripción de Azure. Para obtener más información, vea [configuración de directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md).
- Utilice las recomendaciones en Centro de seguridad para proteger los recursos de Azure. Para obtener más información, vea [administrar las recomendaciones de seguridad en el centro de seguridad de Azure](security-center-recommendations.md).
- Revisar y administrar las alertas de seguridad actual. Para obtener más información, consulte [administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md).
- Obtenga más información sobre la [amenaza detección características avanzadas](security-center-detection-capabilities.md) que vienen con el [nivel estándar](security-center-pricing.md) del centro de seguridad. Una prueba gratuita de 90 días de la capa estándar está disponible.
- Si tiene preguntas sobre cómo usar el centro de seguridad, consulte las [Preguntas más frecuentes sobre centro de seguridad de Azure](security-center-faq.md).

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/prevention-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png
