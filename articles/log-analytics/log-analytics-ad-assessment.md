<properties
    pageTitle="Optimizar su entorno con la solución de evaluación de Active Directory en el análisis de registro | Microsoft Azure"
    description="Puede usar la solución de evaluación de Active Directory para evaluar los riesgos y la salud de los entornos de servidor en intervalos regulares."
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

# <a name="optimize-your-environment-with-the-active-directory-assessment-solution-in-log-analytics"></a>Optimizar su entorno con la solución de evaluación de Active Directory en el análisis de registro

Puede usar la solución de evaluación de Active Directory para evaluar los riesgos y la salud de los entornos de servidor en intervalos regulares. En este artículo le ayudará a instalar y usar la solución de modo que puede tomar medidas correctivas para posibles problemas.

Esta solución proporciona una lista de prioridades de recomendaciones específicas de su infraestructura de servidor implementado. Las recomendaciones se clasifican en cuatro áreas de interés que le ayudarán a rápidamente comprende el riesgo y ejecute una acción.

Las recomendaciones se basan en el conocimiento y experiencia con ingenieros de Microsoft de miles de visitas de clientes. Cada recomendación proporciona instrucciones sobre cómo implementar los cambios sugeridos y por qué un problema podría ser importante para usted.

Puede elegir áreas de interés más importantes para su organización y realizar el seguimiento de su progreso hacia ejecuta un entorno de riesgo gratuitas y correcto.

Una vez que haya agregado la solución y una evaluación haya finalizado, resumen se muestra información de áreas de interés en el panel de la **evaluación de AD** para la infraestructura de su entorno. Las secciones siguientes describen cómo usar la información en el panel de la **evaluación de AD** , donde puede ver y, a continuación, tomar acciones recomendadas para la infraestructura de servidor de Active Directory.

![imagen de mosaico de la evaluación de SQL](./media/log-analytics-ad-assessment/ad-tile.png)

![imagen del panel de evaluación de SQL](./media/log-analytics-ad-assessment/ad-assessment.png)


## <a name="installing-and-configuring-the-solution"></a>Instalar y configurar la solución
Use la información siguiente para instalar y configurar las soluciones.

- Agentes deben estar instalados en los controladores de dominio que sean miembros del dominio que se debe evaluar.
- La solución de evaluación de Active Directory requiere .NET Framework 4 instalado en cada equipo que tiene un agente OMS.
- Agregar la solución de evaluación de Active Directory para el área de trabajo OMS mediante el proceso descrito en [soluciones de agregar el registro de análisis de la Galería de soluciones](log-analytics-add-solutions.md).  No es necesaria realizar ninguna configuración adicional.

    >[AZURE.NOTE] Una vez haya agregado la solución, el archivo AdvisorAssessment.exe se agrega a servidores con agentes. Datos de configuración se lea y, a continuación, se envía al servicio de OMS en la nube para el procesamiento. Lógica se aplica a los datos recibidos y el servicio de nube registra los datos.

## <a name="active-directory-assessment-data-collection-details"></a>Detalles de colección de datos de Active Directory evaluación

Evaluación de Active Directory recopila datos WMI, los datos del registro y datos de rendimiento con los agentes que ha habilitado.

La siguiente tabla muestra los métodos de conjunto de datos para los agentes, si es necesario Operations Manager (SCOM) y cómo se recopilan datos a menudo por un agente.

| plataforma | Agente directo | Agente SCOM | Almacenamiento de Azure | ¿SCOM obligatorio? | Datos de agente SCOM enviadas por grupo de administración | frecuencia de la colección |
|---|---|---|---|---|---|---|
|Windows|![Sí](./media/log-analytics-ad-assessment/oms-bullet-green.png)|![Sí](./media/log-analytics-ad-assessment/oms-bullet-green.png)|![No](./media/log-analytics-ad-assessment/oms-bullet-red.png)|   ![No](./media/log-analytics-ad-assessment/oms-bullet-red.png)|![Sí](./media/log-analytics-ad-assessment/oms-bullet-green.png)| 7 días|


## <a name="understanding-how-recommendations-are-prioritized"></a>Comprender cómo se asignan prioridades recomendaciones

Cada recomendación realizado se expresa un valor de ponderación que identifica la importancia relativa de la recomendación. Se muestran solo las diez recomendaciones más importantes.

### <a name="how-weights-are-calculated"></a>¿Cómo se calcula su peso

Ponderaciones son valores agregados en función de tres factores clave:

- La *probabilidad* de que se detectó un problema provocará problemas. Una mayor probabilidad es igual a un mayor puntuación para la recomendación.

- El *impacto* del problema en la organización si hace que un problema. Un mayor impacto equivale a una puntuación más grande para la recomendación.

- El *esfuerzo* necesario para implementar la recomendación. Un mayor esfuerzo equivale a una puntuación más pequeño de la recomendación.

Ponderación de cada recomendación se expresa como un porcentaje de la puntuación total disponible para cada área de foco. Por ejemplo, si una recomendación en el área de foco de seguridad y cumplimiento tiene una calificación de 5%, implementar dicha recomendación aumentará su % de puntuación por 5 general de seguridad y cumplimiento.

### <a name="focus-areas"></a>Áreas de interés

**Seguridad y cumplimiento** - esta área de foco muestra recomendaciones para amenazas de seguridad y violaciones, directivas corporativas y los requisitos de cumplimiento técnico, legal y normativo.

**Disponibilidad y continuidad empresarial** - esta área de foco muestra recomendaciones para la disponibilidad de los servicios, la resistencia de la infraestructura y la protección de la empresa.

Recomendaciones para ayudar a su organización de muestra de **rendimiento y escalabilidad** - esta área de foco infraestructura de TI crecen, asegúrese de que su entorno de TI cumple con los requisitos de rendimiento actuales y puede responder a las cambiantes necesidades de infraestructura.

**Actualizar, implementación y migración** - esta área de foco muestra recomendaciones para ayudarle a actualizar, migrar e implementar Active Directory en la infraestructura existente.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>¿Debe intentar puntuación 100% en cada área de foco?

No necesariamente. Las recomendaciones se basan en los conocimientos y las experiencias adquiridas por ingenieros de Microsoft a través de miles de visitas de clientes. Sin embargo, no infraestructuras de dos servidores son los mismos y recomendaciones específicas pueden ser más o menos relevantes para usted. Por ejemplo, algunas recomendaciones de seguridad podrían ser menos relevantes si las máquinas virtuales no se exponen en Internet. Algunas recomendaciones de disponibilidad pueden ser menos importantes para los servicios que proporcionan informes y recopilación de datos ad hoc de prioridad baja. Problemas que son importantes para un negocio para adultos pueden ser menos importantes para un inicio. Es posible que desee identificar qué áreas de interés son sus prioridades y observe cómo cambian las calificaciones con el tiempo.

Cada recomendación incluye orientación sobre por qué es importante. Debe usar esta guía para evaluar si implementa la recomendación es apropiado, dada la naturaleza de los servicios de TI y las necesidades de su organización.

## <a name="use-assessment-focus-area-recommendations"></a>Utilizar recomendaciones de área de foco de evaluación

Antes de poder usar una solución de evaluación en OMS, debe tener la solución instalada. Para obtener más información sobre la instalación de soluciones, vea [soluciones de agregar el registro de análisis de la Galería de soluciones](log-analytics-add-solutions.md). Una vez instalado, puede ver el resumen de recomendaciones mediante el mosaico de evaluación en la página de información general de OMS.

Ver las evaluaciones de cumplimiento resumidos para la infraestructura y, a continuación, en Rastrear recomendaciones.


### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para ver recomendaciones para un área de enfoque y tomar medidas correctivas

1. En la página **Introducción** , haga clic en el mosaico de la **evaluación** de la infraestructura de servidores.
2. En la página de **evaluación** , revise la información de resumen en uno de los módulos de área de foco y, a continuación, haga clic en para ver recomendaciones para dicha área de foco.
3. En cualquiera de las páginas de área de foco, puede ver las recomendaciones con prioridad para su entorno. Haga clic en una recomendación en **Objetos afectados** para ver los detalles sobre por qué se realiza la recomendación.  
    ![imagen de recomendaciones de la evaluación](./media/log-analytics-ad-assessment/ad-focus.png)
4. Puede tomar medidas correctivas sugeridos en **Acciones sugeridas**. Cuando el elemento se ha resuelto, evaluaciones posteriores grabará que recomienda acciones realizadas y aumentará la puntuación de cumplimiento. Elementos corregidos aparecen como **Objetos pasado**.

## <a name="ignore-recommendations"></a>Omitir recomendaciones

Si tiene recomendaciones que desea omitir, puede crear un archivo de texto que usará OMS para evitar recomendaciones aparezcan en los resultados de la evaluación.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Identificar las recomendaciones que omitirá

1.  Inicie sesión en el área de trabajo y abrir búsqueda de registros. Use la siguiente consulta recomendaciones de lista que no han pasado para equipos en su entorno.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Esto es una captura de pantalla que muestra la consulta de búsqueda de registro: ![error recomendaciones](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)

2.  Elija recomendaciones que desea omitir. En el siguiente procedimiento usará los valores para RecommendationId.


### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Crear y usar un archivo de texto IgnoreRecommendations.txt

1.  Cree un archivo llamado IgnoreRecommendations.txt.
2.  Pegue o escriba cada RecommendationId para cada recomendación que desea que el análisis de registro para pasar por alto en una línea independiente y, a continuación, guarde y cierre el archivo.
3.  Coloque el archivo en la carpeta siguiente en cada equipo donde desee OMS omitir recomendaciones.
    - En equipos con el agente de supervisión de la Microsoft (conectado directamente o a través de Operations Manager) - *SystemDrive*: \Program Files\Microsoft Agent\Agent de supervisión
    - En el servidor de administración de Operations Manager - *SystemDrive*: \Program Files\Microsoft Manager\Server R2\Operations de System Center 2012

### <a name="to-verify-that-recommendations-are-ignored"></a>Para comprobar que se omiten las recomendaciones

Después de la próxima programada evaluación se ejecute cada 7 días de forma predeterminada, las recomendaciones especificadas se marcan *omitido* y no aparecerá en el panel de evaluación.

1. Puede usar las consultas de búsqueda de registro siguientes para obtener una lista de todas las recomendaciones ignoradas.

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

2.  Si más tarde decide que desea ver recomendaciones ignoradas, quite los archivos de IgnoreRecommendations.txt o puede quitar RecommendationIDs de ellas.

## <a name="ad-assessment-solutions-faq"></a>Soluciones de evaluación de AD preguntas más frecuentes

*¿Con qué frecuencia se ejecuta una evaluación?*
- La evaluación se ejecute cada 7 días.

*¿Hay alguna forma para configurar la frecuencia de ejecución de la evaluación?*
- No en este momento.

*¿Si se detecta otro servidor después de haber agregado una solución de evaluación, se calculará?*
- Sí, una vez que se detectan se evalúa desde entonces, cada 7 días.

*¿Si un servidor de baja, cuándo se quitará la evaluación?*
- Si un servidor no enviar los datos de 3 semanas, se elimina.

*¿Qué es el nombre del proceso que realiza la recopilación de datos?*
- AdvisorAssessment.exe

*¿Cuánto tardará para que se van a recopilar datos?*
- La recopilación de datos reales en el servidor tarda aproximadamente 1 hora. Puede tardar más en servidores que tienen un gran número de servidores de Active Directory.

*¿Qué tipo de datos se recopila?*
- Se recopilan los siguientes tipos de datos:
    - WMI
    - Registro
    - Contadores de rendimiento

*¿Hay una forma de configurar cuando se recopilan datos?*
- No en este momento.

*¿Por qué mostrar solo las recomendaciones de 10 mejores?*
- En lugar de que le da una lista de gran exhaustiva de tareas, se recomienda centrarse en abordar las recomendaciones con prioridad en primer lugar. Después de solucionarlos, recomendaciones adicionales estarán disponibles. Si prefiere ver la lista detallada, puede ver todas las recomendaciones usando la búsqueda de registro.

*¿Hay alguna forma omitir una recomendación?*
- Sí, vea [recomendaciones de ignorar](#ignore-recommendations) sección anterior.


## <a name="next-steps"></a>Pasos siguientes

- Usar [búsquedas de registro de análisis de registro](log-analytics-log-searches.md) para ver datos detallados de evaluación de AD y recomendaciones.
