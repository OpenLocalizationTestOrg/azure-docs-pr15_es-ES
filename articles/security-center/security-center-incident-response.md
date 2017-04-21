<properties
   pageTitle="Usar el centro de seguridad de Azure para una respuesta a incidencias | Microsoft Azure"
   description="Este documento explica cómo usar el centro de seguridad de Azure para un escenario de respuesta a incidencias."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="yurid"/>

# <a name="using-azure-security-center-for-an-incident-response"></a>Usar el centro de seguridad de Azure para una respuesta a incidencias
Muchas organizaciones Obtenga información sobre cómo responder a las incidencias de seguridad únicamente después de sufrir un ataque. Para reducir los costes y daños, es importante tener una respuesta a incidencias plan en lugar de antes de que un ataque lleve a cabo. Puede usar el centro de seguridad de Azure en diferentes etapas de una incidente respuesta.

## <a name="incident-response-planning"></a>Planeación de respuesta a incidencias

Un plan eficaz depende de tres funciones principales: poder proteger, detectar y responder a amenazas. Protección consiste en evitar incidencias, detección es información sobre cómo identificar amenazas temprano y respuesta consiste en expulsar el intruso y restauración de sistemas para mitigar el impacto de una infracción de.

En este artículo se usan las etapas de respuesta a incidencias de seguridad en el artículo de la [Respuesta de seguridad de Microsoft Azure en la nube](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678) , como se muestra en el diagrama siguiente:

![Ciclo de vida de respuesta a incidencias](./media/security-center-incident-response/security-center-incident-response-fig1.png)

Puede usar el centro de seguridad durante las fases de detectar, evaluar y diagnosticar. Estos son algunos ejemplos de cómo el centro de seguridad pueden ser útil durante las tres fases de respuesta a incidencias inicial:

- **Detectar**: Revise la primera indicación de una investigación de evento.
    - Ejemplo: revisar la comprobación inicial que se ha generado una alerta de seguridad de alta prioridad en el panel Centro de seguridad.
- **Evaluación**: realizar la evaluación inicial para obtener más información sobre la actividad sospechosa.
    - Ejemplo: obtener más información acerca de la alerta de seguridad.
- **Diagnosticar**: realizar una investigación técnica e identificar estrategias de contenido, mitigación y solución.
    - Ejemplo: siga los pasos de corrección descritos por el centro de seguridad en esa alerta de seguridad determinada.

El siguiente escenario muestra cómo aprovechar el centro de seguridad durante las fases de detectar, evaluar y diagnosticar y responder de un incidente de seguridad. En el centro de seguridad, un [incidente de seguridad](security-center-incident.md) es una agregación de todas las alertas para un recurso que se alinean con patrones de [eliminar la cadena](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) . Incidencias aparecen en el mosaico de [las alertas de seguridad](security-center-managing-and-responding-alerts.md) y el módulo. Un incidente revela la lista de alertas relacionadas, que permite obtener más información sobre cada aparición. Centro de seguridad también presenta independiente las alertas de seguridad que también se pueden usar para realizar un seguimiento de una actividad sospechosa.

## <a name="scenario"></a>Escenario

Contoso migradas algunos de sus recursos locales a Azure, incluidos algunos basado en máquina virtual cargas de trabajo de línea de negocio y las bases de datos SQL. Actualmente, Contoso Core equipo seguridad incidente respuesta equipos (CSIRT) tiene un problema investigar problemas de seguridad debido a la inteligencia de seguridad no está integrado con las herramientas de respuesta a incidencias actual. Esta falta de integración presenta un problema durante la fase de detectar (demasiados falsos positivos), así como durante las fases de evaluación y diagnosticar. Como parte de la migración, decidió participar en el centro de seguridad para ayudarle a resolver este problema.

La primera fase de esta migración finaliza después de onboarded todos los recursos y tratado todas las recomendaciones de seguridad del centro de seguridad. CSIRT de Contoso es el punto central para abordar incidencias de seguridad del equipo. El grupo consta de un grupo de personas responsables de abordar cualquier incidente de seguridad. Los miembros del equipo han definido claramente sus tareas para asegurar que ningún área de respuesta sin cubierta.

En este escenario, vamos centrarse en los roles de los siguientes roles que forman parte de Contoso CSIRT:

![Ciclo de vida de respuesta a incidencias](./media/security-center-incident-response/security-center-incident-response-fig2.png)

Laura es en operaciones de seguridad. Sus responsabilidades se incluyen:

- Supervisar y responder a amenazas de seguridad de todo el día.
- Aumento al propietario de la carga de trabajo de nube o analista de seguridad según sea necesario.

SAM es analista de seguridad y entre sus responsabilidades se incluyen:

- Investigar los ataques.
- Alertas adecuadas.
- Trabajar con los propietarios de carga de trabajo para determinar y aplicar su eliminación.

Como puede ver, Vanessa y Sam tienen diferentes responsabilidades y debe trabajar conjuntamente para compartir información del centro de seguridad.

## <a name="recommended-solution"></a>Solución recomendada

Dado que corresponden a Vanessa y Sam tienen funciones diferentes, va a usar distintas áreas del centro de seguridad para obtener información relevante para sus actividades diarias. Laura usará **las alertas de seguridad** como parte de su supervisión diaria.

![Alertas de seguridad](./media/security-center-incident-response/security-center-incident-response-fig3.png)

Laura usarán las alertas de seguridad durante las fases de detectar y evaluar. Cuando termine de Vanessa la evaluación inicial, puede elevar el problema a Diego si se requiere una investigación adicional. En este momento, Sam usará la información provista por el centro de seguridad, a veces junto con otros orígenes de datos, para pasar a la fase de diagnóstico.


## <a name="how-to-implement-this-solution"></a>Cómo implementar esta solución

Para ver cómo debe usar el centro de seguridad de Azure en un escenario de respuesta a incidencias, se le siga los pasos de Vanessa en las fases de detectar y evaluar y, a continuación, consulte ¿Qué Sam para diagnosticar el problema.

### <a name="detect-and-assess-incident-response-stages"></a>Detectar y evaluar etapas de respuesta a incidencias

Laura ha iniciado sesión en el portal de Azure y está trabajando en la consola de centro de seguridad. Como parte de su diaria de las actividades de control, había iniciado revisar las alertas de seguridad de alta prioridad realizando los siguientes pasos:

1. Haga clic en el mosaico de **las alertas de seguridad** y el módulo de **las alertas de seguridad** de acceso.
    ![Módulo de alerta de seguridad](./media/security-center-incident-response/security-center-incident-response-fig4.png)

    > [AZURE.NOTE] En este escenario, Vanessa va a realizar una evaluación de la alerta de actividad malintencionado SQL, tal como se muestra en la figura anterior.
2. Haga clic en la alerta de **actividad malintencionado SQL** y revise los recursos han en el módulo de **actividad malintencionado SQL** :  ![detalles del incidente](./media/security-center-incident-response/security-center-incident-response-fig5.png)

    En este módulo, Vanessa puede tomar notas importantes sobre los recursos han, ¿cómo ha pasado muchas veces este ataque y al se ha detectado.
3. Haga clic en el **ataque recursos** para obtener más información acerca de este ataque.

Después de leer la descripción, Vanessa está seguro de que no es un falso positivo y que debe elevar este caso a Diego.

### <a name="diagnose-incident-response-stage"></a>Diagnosticar fase de respuesta a incidencias

SAM recibe el caso de Vanessa y revisar los pasos de corrección sugerido del centro de seguridad inicia.

![Ciclo de vida de respuesta a incidencias](./media/security-center-incident-response/security-center-incident-response-fig6.png)

### <a name="additional-resources"></a>Recursos adicionales

El grupo de respuesta a incidencias también puede sacar partido de la capacidad de [Power BI de centro de seguridad](security-center-powerbi.md) para ver diferentes tipos de informes. Estos informes permiten durante una investigación para visualizar, analizar y filtrar las alertas de seguridad y recomendaciones. Para empresas que usan su información de seguridad y la solución de administración (SIEM) evento durante el proceso de investigación, también pueden [integrar el centro de seguridad de su solución](security-center-integrating-alerts-with-log-integration.md). También puede integrar registros de auditoría de Azure y eventos de seguridad de máquina virtual (VM) mediante la [herramienta de integración de registro de Azure](https://blogs.msdn.microsoft.com/azuresecurity/2016/07/21/microsoft-azure-log-integration-preview/). Para investigar un ataque, puede usar esta información junto con la información que proporciona el centro de seguridad.


## <a name="conclusion"></a>Conclusión

Reunir un equipo antes de que se produzca un incidente es muy importante para su organización y se positiva influir en cómo se administran las incidencias. Con las herramientas correctas para supervisar los recursos puede ayudar a este grupo para tomar medidas precisas para corregir un incidente de seguridad. [Capacidades de detección](security-center-detection-capabilities.md) del centro de seguridad puede ayudar a TI para responder a las incidencias de seguridad y solucionar problemas de seguridad rápidamente.
