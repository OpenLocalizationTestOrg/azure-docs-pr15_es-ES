<properties
    pageTitle="Agregar soluciones de análisis de registro de la Galería de soluciones | Microsoft Azure"
    description="Las soluciones de análisis de registro son que reglas de una colección de lógica, visualización y adquisición de datos que ofrecen métricas girados alrededor de un área del problema en particular."
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

# <a name="add-log-analytics-solutions-from-the-solutions-gallery"></a>Agregar soluciones de análisis de registro de la Galería de soluciones

Soluciones de análisis de registro son una colección de **lógica**, **visualización** y **reglas de adquisición de datos** que ofrecen métricas girados alrededor de un área del problema en particular. Este artículo las soluciones de listas compatibles de análisis de registro y se explica cómo agregar y quitar mediante la Galería de soluciones.

Las soluciones permiten más profundos perspectivas para:

- ayudar a investigar y resolver problemas de funcionamiento más rápido
- recopilar y relacionar varios tipos de datos de equipo
- le permiten ser proactiva con actividades como la planificación de capacidad, informes de estado de revisión y auditoría de seguridad.


>[AZURE.NOTE] Análisis de registro incluye funcionalidad de búsqueda de registros, por lo que no es necesario instalar una solución para habilitarlo. Sin embargo, puede obtener sugerencias de búsqueda, impresiones y visualizaciones de datos mediante la adición de soluciones de la Galería de soluciones.

Una vez haya agregado una solución, los datos se recopilan de los servidores de la infraestructura y enviados al servicio de OMS. Procesamiento de la OMS servicio normalmente tarda unos minutos a una hora. Después de que el servicio procesa los datos, puede ver en OMS.

Puede quitar con facilidad una solución cuando ya no sea necesario. Cuando quita una solución, sus datos no se envían a OMS, lo que puede reducir la cantidad de datos utilizados la cuota diaria, si tiene una.


## <a name="solutions-supported-by-the-microsoft-monitoring-agent"></a>Soluciones compatibles con el agente de supervisión de Microsoft

En este momento, servidores que están conectados a OMS con el agente de supervisión de Microsoft pueden utilizar la mayoría de las soluciones disponibles, incluidos:

- Evaluación de Active Directory
- Administración de alertas (sin alertas SCOM)
- Antimalware
- Seguimiento de cambios
- Seguridad
- Evaluación de SQL
- Actualizaciones del sistema

Sin embargo, las siguientes soluciones son *no* compatibles con el agente de supervisión de Microsoft y requerir a agente de System Center Operations Manager (SCOM).

- Administración de alertas (incluidas las alertas SCOM)
- Administración de la capacidad
- Evaluación de configuración

Para obtener información sobre cómo conectar al agente SCOM a análisis de registro, consulte [Conectar Operations Manager para el análisis de registro](log-analytics-om-agents.md) .

### <a name="to-add-a-solution-using-the-solutions-gallery"></a>Para agregar una solución utilizando la Galería de soluciones

1. En la página información general de OMS, haga clic en el mosaico de la **Galería de soluciones** .    
    ![Galería de soluciones](./media/log-analytics-add-solutions/sol-gallery.png)
2. En la página de la Galería de soluciones de OMS, obtenga información sobre cada solución disponible. Haga clic en el nombre de la solución que desea agregar a OMS.
3. En la página para la solución que elija, se muestra información detallada sobre la solución. Haga clic en **Agregar**.
4. Un nuevo mosaico para la solución que ha agregado aparece en la información general de página de OMS y puede empezar a usar después de que el servicio OMS procesa los datos.

## <a name="to-configure-solutions"></a>Para configurar soluciones
1. Deberá configurar algunas soluciones. Por ejemplo, deberá configurar automatización, recuperación de sitio de Azure y copia de seguridad antes de que puede usarlos.
2. Para cualquiera de estas soluciones, haga clic en el mosaico en la página información general.  
    ![configurar la solución](./media/log-analytics-add-solutions/configure-additional.png)
3. A continuación, configure la solución con la información necesaria y, a continuación, haga clic en **Guardar**.  
    ![configurar la solución](./media/log-analytics-add-solutions/configure.png)

### <a name="to-remove-a-solution-using-the-solutions-gallery"></a>Para quitar una solución utilizando la Galería de soluciones

1. En la página información general de OMS, haga clic en el icono **configuración** .
2. En la página Configuración, en la pestaña soluciones, haga clic en **Quitar** para la solución que desea quitar.
3. En el cuadro de diálogo de confirmación, haga clic en **Sí** para quitar la solución.

## <a name="data-collection-details-for-oms-features-and-solutions"></a>Detalles del conjunto de datos para características OMS y soluciones

La siguiente tabla muestra los métodos de conjunto de datos y otros detalles acerca de cómo se recopilan los datos de soluciones y características OMS. Agentes directos y SCOM son esencialmente los mismos, aunque el agente directo incluye funciones adicionales para permitir que se conecte al área de trabajo OMS y redirige a través de un servidor proxy. Si usa a un agente SCOM, debe estar dirigido como agente OMS comunicarse con OMS. Agentes SCOM en esta tabla son agentes OMS que están conectados a SCOM. Para obtener información sobre cómo conectar su entorno existente de SCOM a OMS, vea [Conectar Operations Manager para el análisis de registro](log-analytics-om-agents.md) .

>[AZURE.NOTE] El tipo de agente que use determina cómo se envían datos a OMS, con las siguientes condiciones:

- Utiliza el agente directo o un adjunto SCOM OMS.
- Cuando se requiere SCOM, SCOM agente datos para la solución siempre se envían a OMS mediante el grupo de administración de SCOM. Además, cuando se requiere SCOM, solo el agente SCOM se utiliza la solución.
- Cuando SCOM no es obligatorio y se muestra la tabla que se envían datos de agente SCOM a OMS del grupo de administración, a continuación, datos de agente SCOM siempre se envían a OMS usando grupos de administración. Agentes directos omitir el grupo de administración y enviar sus datos directamente a OMS.
- Cuando los datos de agente SCOM no se envía con un grupo de administración, los datos se envían directamente a OMS: omitir el grupo de administración.


|tipo de datos| plataforma | Agente directo | Agente SCOM | Almacenamiento de Azure | ¿SCOM obligatorio? | Datos de agente SCOM enviadas por grupo de administración | frecuencia de la colección |
|---|---|---|---|---|---|---|---|
|Evaluación de AD|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|  7 días|
|Estado de la replicación de AD|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 días|
|Alertas (Nagios)|Linux|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|en llegada|
|Alertas (Zabbix)|Linux|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|1 minuto|
|Alertas (Operations Manager)|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|3 minutos|
|Antimalware|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)| cada hora|
|Administración de la capacidad|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)| cada hora|
|Seguimiento de cambios|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)| cada hora|
|Seguimiento de cambios|Linux|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|cada hora|
|Configuración de evaluación (Advisor heredado)|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)| dos veces por día|
|ETW|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutos|
|Registros de IIS|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutos|
|Depósitos claves|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutos|
|Puertas de enlace de aplicación de red|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutos|
|Grupos de seguridad de red|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutos|
|Office 365|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|en la notificación|
|Contadores de rendimiento|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|como mínimo de 10 segundos programada|
|Contadores de rendimiento|Linux|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|como mínimo de 10 segundos programada|
|Tela de servicio|Windows|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutos|
|Evaluación de SQL|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)| 7 días|
|SurfaceHub|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|en llegada|
|Registro del sistema|Linux|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|desde el almacenamiento de Azure: 10 minutos; agente de: en llegada|
|Actualizaciones del sistema|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)| al menos de 2 horas por día y 15 minutos después de instalar una actualización|
|Registros de eventos de seguridad de Windows|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)| para el almacenamiento de Azure: 10 minutos; para el agente: llegada|
|Registros de firewall de Windows|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)| en llegada|
|Registros de eventos de Windows|Windows|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)| para el almacenamiento de Azure: 1 min; para el agente: llegada|
|Datos de alambre|Windows (2012 R2 / 8.1 o posterior)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Sí](./media/log-analytics-add-solutions/oms-bullet-green.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)|![No](./media/log-analytics-add-solutions/oms-bullet-red.png)| cada minuto|

## <a name="log-analytics-preview-solutions-and-features"></a>Características y soluciones de vista previa de análisis de registro

Si ejecuta un servicio y sigue devops prácticas estamos puedas colaborar con los clientes para desarrollar soluciones y características.

Durante la vista previa privada ofrecemos un pequeño grupo de los clientes a acceder a una implementación inicial de la función o una solución para obtener comentarios y realizar mejoras. Esta implementación inicial posee mínimas funciones operativas.

Nuestro objetivo es que puede probar cosas rápidamente para que podamos encontrarlos qué funciona y qué no funciona. Este proceso se recorra hasta que los comentarios de los clientes de vista previa privada nos informa de que se está listos para una vista previa pública.

Durante la public preview, realizamos la característica o solución disponible para todos los usuarios para obtener más información y validar nuestro escala y eficacia. Esta fase:

- Características de vista previa se mostrarán en la pestaña Configuración y se pueden habilitar por cualquier usuario
- Pueden agregarse a través de la Galería de soluciones de vista previa o mediante una secuencia de comandos publicado

### <a name="what-should-i-know-about-preview-features-and-solutions"></a>¿Qué debo saber sobre las características de vista previa y soluciones?

Estamos satisfechos sobre las nuevas características y soluciones y trabajar con ellos desarrollar de Domino.

Características de vista previa y soluciones no están adecuadas para todos los usuarios no obstante, por lo que antes de unirse a una vista previa privada o habilitar una vista previa pública Asegúrese de que aceptar está trabajando con un elemento que está en desarrollo.

Al habilitar una característica de vista previa a través del portal que será muestra una advertencia que le recuerda que la característica está en vista previa.

#### <a name="for-both-private-and-public-preview"></a>Para preview *públicas* y *privadas*

La siguiente se aplica a las vistas previas de públicas y privadas:

- Cosas no siempre funcionen correctamente.
  - Rango de problemas de ser un mensaje secundaria molesto mediante a algo que no funciona en absoluto
- Es posible que la vista previa para tener un impacto negativo en los sistemas / entorno
  - Intentamos evitar negativas cosas que se usa con OMS pero cosas inesperadas a veces se producen a ocurrir con los sistemas
- Pérdida de datos y pueden producirse daños
- Le podemos pedimos que nos recopilar registros de diagnóstico u otros datos para ayudar a solucionar problemas
- La característica o solución puede quitarse (temporal o permanentemente)
  - En función de nuestros conocimientos durante la vista previa que nos podemos decide no suelte la característica o solución
- Vistas previas no funcionen o no se ha probado con todas las configuraciones y podemos limitar:
  - Los sistemas operativos que se pueden usar (por ejemplo, una característica sólo se puede aplicar a Linux en vista preliminar)
  - El tipo de agente (MMA, SCOM) que se pueden usar (por ejemplo, una característica no funcionen con SCOM en vista preliminar)  
- Soluciones de vista previa y características no están cubiertas por el contrato de nivel de servicio
- Uso de características de vista previa producirá cargos de uso
- Características o funciones que necesita para la característica / solución útil puede ser que faltan o incompletas
- Características o soluciones no esté disponibles en todas las regiones
- Características o soluciones pueden no estén localizadas
- Características de soluciones pueden tener un límite del número de clientes o dispositivos que pueden utilizar
- Debe utilizar secuencias de comandos para realizar la configuración y para habilitar la característica de solución
- La interfaz de usuario (UI) será incompleta y puede cambiar de un día a día
- Las vistas previas de públicas no sean apropiadas para su producción / crítica sistemas

#### <a name="for-private-preview"></a>Para *privado* preview

Además de los elementos anteriores es específica de las vistas previas de privada lo siguiente:

- Esperamos que nos proporcione comentarios sobre la experiencia de modo que podemos hacer que la característica o solución mejor
- Nos pongamos en contacto con encuestas, llamadas de teléfono o correo electrónico de comentarios
- Cosas siempre no funcionan correctamente
- Se puede requerir un contrato de confidencialidad (confidencialidad) para participar o pueden incluir contenido confidencial
  - Antes de crear blogs, realizar TWEETS o en caso contrario, se comunique con proveedores de terceros, consulte con el Administrador de programas responsable de la vista previa para conocer las restricciones sobre divulgación
- No se ejecutan en producción / crítica sistemas


### <a name="how-do-i-get-access-to-private-preview-features-and-solutions"></a>¿Cómo se puede obtener acceso a las características de vista previa privado y soluciones?

Nos invitar a los clientes a las vistas previas de privada a través de varias maneras diferentes dependiendo de la vista previa.

- Responder a la encuesta mensual del cliente y darnos permiso para su seguimiento con usted mejoran sus posibilidades de receptor de la invitación a una vista previa privada.
- Su equipo de cuentas de Microsoft puede designado.
- Puede registrarse basándose en los detalles que se publicó en twitter [msopsmgmt](https://twitter.com/msopsmgmt)
- Puede registrarse en función de eventos de la comunidad compartida de detalles: mirar nos reunirse nivel de conferencias y las Comunidades en línea.


## <a name="next-steps"></a>Pasos siguientes

- [Registros de búsqueda](log-analytics-log-searches.md) para ver información detallada recopilada por soluciones.
