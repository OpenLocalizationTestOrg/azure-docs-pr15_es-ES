<properties
    pageTitle="Optimizar su entorno con la solución de evaluación de SQL de análisis de registro | Microsoft Azure"
    description="Puede usar la solución de evaluación de SQL para evaluar los riesgos y la salud de los entornos de servidor en intervalos regulares."
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
    ms.date="08/11/2016"
    ms.author="banders"/>

# <a name="optimize-your-environment-with-the-sql-assessment-solution-in-log-analytics"></a>Optimizar su entorno con la solución de evaluación de SQL de análisis de registro


Puede usar la solución de evaluación de SQL para evaluar los riesgos y el estado de sus entornos de servidor en intervalos regulares. En este artículo le ayudará a instalar la solución de modo que puede tomar medidas correctivas para posibles problemas.

Esta solución proporciona una lista de prioridades de recomendaciones específicas de su infraestructura de servidor implementado. Las recomendaciones están clasificadas por seis áreas de interés que le ayudarán a rápidamente comprende el riesgo y tomar medidas correctivas.

Las recomendaciones se basan en el conocimiento y experiencia con ingenieros de Microsoft de miles de visitas de clientes. Cada recomendación proporciona instrucciones sobre cómo implementar los cambios sugeridos y por qué un problema podría ser importante para usted.

Puede elegir áreas de interés más importantes para su organización y realizar el seguimiento de su progreso hacia ejecuta un entorno de riesgo gratuitas y correcto.

Una vez que haya agregado la solución y una evaluación haya finalizado, resumen se muestra información de áreas de interés en el panel de **Evaluación de SQL** para la infraestructura de su entorno. Las secciones siguientes describen cómo usar la información en el panel de **Evaluación de SQL** , donde puede ver y, a continuación, tomar acciones recomendadas para su infraestructura SQL server.

![imagen de mosaico de la evaluación de SQL](./media/log-analytics-sql-assessment/sql-assess-tile.png)

![imagen del panel de evaluación de SQL](./media/log-analytics-sql-assessment/sql-assess-dash.png)

## <a name="installing-and-configuring-the-solution"></a>Instalar y configurar la solución
Evaluación de SQL funciona con todas las versiones compatibles de SQL Server para las ediciones Standard, Developer y Enterprise.

Use la información siguiente para instalar y configurar la solución.

- Agentes deben estar instalados en los servidores que tienen instalado SQL Server.
- La solución de evaluación de SQL requiere .NET Framework 4 instalado en cada equipo que tiene un agente OMS.
- Cuando se utiliza al agente Operations Manager con la evaluación de SQL, debe usar una cuenta de Operations Manager Run-As. Para obtener más información, consulte [Operations Manager ejecutar como cuentas para OMS](#operations-manager-run-as-accounts-for-oms) debajo.

    >[AZURE.NOTE] El agente MMA no admite cuentas de Operations Manager Run-As.

- Agregar la solución de evaluación de SQL en el área de trabajo OMS mediante el proceso descrito en [soluciones de agregar el registro de análisis de la Galería de soluciones](log-analytics-add-solutions.md). No es necesaria realizar ninguna configuración adicional.

>[AZURE.NOTE] Una vez haya agregado la solución, el archivo AdvisorAssessment.exe se agrega a servidores con agentes. Datos de configuración se lea y, a continuación, se envía al servicio de OMS en la nube para el procesamiento. Lógica se aplica a los datos recibidos y el servicio de nube registra los datos.

## <a name="sql-assessment-data-collection-details"></a>Detalles del conjunto de datos de evaluación de SQL

Evaluación de SQL recopila datos WMI, los datos del registro, los datos de rendimiento y resultados de vista de administración dinámica de SQL Server con los agentes que ha habilitado.

La siguiente tabla muestra los métodos de conjunto de datos para los agentes, si es necesario Operations Manager (SCOM) y cómo se recopilan datos a menudo por un agente.

| plataforma | Agente directo | Agente SCOM | Almacenamiento de Azure | ¿SCOM obligatorio? | Datos de agente SCOM enviadas por grupo de administración | frecuencia de la colección |
|---|---|---|---|---|---|---|
|Windows|![Sí](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![Sí](./media/log-analytics-sql-assessment/oms-bullet-green.png)|![No](./media/log-analytics-sql-assessment/oms-bullet-red.png)|    ![No](./media/log-analytics-sql-assessment/oms-bullet-red.png)|![Sí](./media/log-analytics-sql-assessment/oms-bullet-green.png)|   7 días|

## <a name="operations-manager-run-as-accounts-for-oms"></a>Operations Manager ejecutar como cuentas para OMS

Análisis de registro en OMS utiliza el grupo de administración y el agente de Operations Manager para recopilar y enviar datos al servicio de OMS. OMS se basa en los paquetes de administración para cargas de trabajo proporcionar servicios de valor agregado. Cada carga de trabajo requiere privilegios de carga de trabajo específica para ejecutar los paquetes de administración en un contexto de seguridad diferente, por ejemplo, una cuenta de dominio. Debe proporcionar información de credenciales configurando una cuenta de Operations Manager ejecutar como.

Use la información siguiente para configurar la cuenta de ejecución Operations Manager de evaluación de SQL.

### <a name="set-the-run-as-account-for-sql-assessment"></a>Configurar la cuenta ejecutar como de evaluación de SQL

 Si ya está usando el módulo de administración de SQL Server, debe usar esa cuenta ejecutar como.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Para configurar la cuenta ejecutar como SQL en la consola de operaciones

>[AZURE.NOTE] Si está utilizando el agente OMS directo, en lugar del agente SCOM, el módulo de administración siempre se ejecuta en el contexto de seguridad de la cuenta del sistema Local. Omitir los pasos 1 a 5 siguiente y ejecutar el SQL T o una muestra de Powershell, que se especifica NT AUTHORITY\SYSTEM como el nombre de usuario.

1. En Operations Manager, abra la consola de operaciones y, a continuación, haga clic en **administración**.

2. En **Configuración de ejecución**, haga clic en **perfiles**y abra **OMS SQL evaluación ejecutar como perfil**.

3. En la página **Cuentas de ejecutar** , haga clic en **Agregar**.

4. Seleccione una cuenta de Windows ejecutar como que contiene las credenciales necesarias para SQL Server, o haga clic en **nuevo** para crear uno.
    >[AZURE.NOTE] El tipo de cuenta ejecutar como debe ser Windows. La cuenta ejecutar como también debe ser parte del grupo de administradores locales en todos los servidores de Windows de instancias de SQL Server.

5. Haga clic en **Guardar**.

6. Modificar y, a continuación, ejecute el siguiente ejemplo T-SQL en cada instancia de SQL Server para conceder permisos mínimos necesarios para ejecutar como cuenta para realizar la evaluación de SQL. Sin embargo, no necesita hacerlo si una cuenta ejecutar como ya es parte de la función de servidor sysadmin en instancias de SQL Server.

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```
#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Para configurar la cuenta ejecutar como SQL con Windows PowerShell

Abra una ventana de PowerShell y ejecute el siguiente script después de que ha actualizado con la información:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "OMS SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

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

**Actualizar, implementación y migración** - esta área de foco muestra recomendaciones para ayudarle a actualizar, migrar e implementar SQL Server a la infraestructura existente.

**Las operaciones y supervisión** - esta área de foco muestra recomendaciones para ayudar a simplificar las operaciones de TI, implementar mantenimiento preventivo y maximizar el rendimiento.

**Configuración de administración de cambios y** - esta área de foco muestra recomendaciones para ayudar a proteger operaciones día a día, asegúrese de que cambios no afectan a la infraestructura, establecer procedimientos de control de cambios, negativo y configuración del sistema y realizar el seguimiento.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>¿Debe intentar puntuación 100% en cada área de foco?

No necesariamente. Las recomendaciones se basan en los conocimientos y las experiencias adquiridas por ingenieros de Microsoft a través de miles de visitas de clientes. Sin embargo, no infraestructuras de dos servidores son los mismos y recomendaciones específicas pueden ser más o menos relevantes para usted. Por ejemplo, algunas recomendaciones de seguridad podrían ser menos relevantes si las máquinas virtuales no se exponen en Internet. Algunas recomendaciones de disponibilidad pueden ser menos importantes para los servicios que proporcionan informes y recopilación de datos ad hoc de prioridad baja. Problemas que son importantes para un negocio para adultos pueden ser menos importantes para un inicio. Es posible que desee identificar qué áreas de interés son sus prioridades y observe cómo cambian las calificaciones con el tiempo.

Cada recomendación incluye orientación sobre por qué es importante. Debe usar esta guía para evaluar si implementa la recomendación es apropiado, dada la naturaleza de los servicios de TI y las necesidades de su organización.

## <a name="use-assessment-focus-area-recommendations"></a>Utilizar recomendaciones de área de foco de evaluación

Antes de poder usar una solución de evaluación en OMS, debe tener la solución instalada. Para obtener más información sobre la instalación de soluciones, vea [soluciones de agregar el registro de análisis de la Galería de soluciones](log-analytics-add-solutions.md). Una vez instalado, puede ver el resumen de recomendaciones mediante el mosaico de evaluación de SQL en la página de información general de OMS.

Ver las evaluaciones de cumplimiento resumidos para la infraestructura y, a continuación, en Rastrear recomendaciones.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Para ver recomendaciones para un área de enfoque y tomar medidas correctivas

1. En la página **Introducción** , haga clic en el mosaico de **Evaluación de SQL** .
2. En la página de **Evaluación de SQL** , revise la información de resumen en uno de los módulos de área de foco y, a continuación, haga clic en para ver recomendaciones para dicha área de foco.
3. En cualquiera de las páginas de área de foco, puede ver las recomendaciones con prioridad para su entorno. Haga clic en una recomendación en **Objetos afectados** para ver los detalles sobre por qué se realiza la recomendación.  
    ![imagen de recomendaciones de la evaluación de SQL](./media/log-analytics-sql-assessment/sql-assess-focus.png)
4. Puede tomar medidas correctivas sugeridos en **Acciones sugeridas**. Cuando el elemento se ha resuelto, evaluaciones posteriores grabará que recomienda acciones realizadas y aumentará la puntuación de cumplimiento. Elementos corregidos aparecen como **Objetos pasado**.

## <a name="ignore-recommendations"></a>Omitir recomendaciones

Si tiene recomendaciones que desea omitir, puede crear un archivo de texto que usará OMS para evitar recomendaciones aparezcan en los resultados de la evaluación.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Identificar las recomendaciones que omitirá

1.  Inicie sesión en el área de trabajo y abrir búsqueda de registros. Use la siguiente consulta recomendaciones de lista que no han pasado para equipos en su entorno.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    Esto es una captura de pantalla que muestra la consulta de búsqueda de registro: ![error recomendaciones](./media/log-analytics-sql-assessment/sql-assess-failed-recommendations.png)

2.  Elija recomendaciones que desea omitir. En el siguiente procedimiento usará los valores para RecommendationId.


### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Crear y usar un archivo de texto IgnoreRecommendations.txt

1.  Cree un archivo llamado IgnoreRecommendations.txt.
2.  Pegue o escriba cada RecommendationId para cada recomendación que desee OMS para pasar por alto en una línea independiente y, a continuación, guarde y cierre el archivo.
3.  Coloque el archivo en la carpeta siguiente en cada equipo donde desee OMS omitir recomendaciones.
    - En equipos con el agente de supervisión de la Microsoft (conectado directamente o a través de Operations Manager) - *SystemDrive*: \Program Files\Microsoft Agent\Agent de supervisión
    - En el servidor de administración de Operations Manager - *SystemDrive*: \Program Files\Microsoft Manager\Server R2\Operations de System Center 2012

### <a name="to-verify-that-recommendations-are-ignored"></a>Para comprobar que se omiten las recomendaciones

1.  Después de la próxima programada evaluación se ejecute cada 7 días de forma predeterminada, las recomendaciones especificadas se marcan omitido y no aparecerá en el panel de evaluación.
2.  Puede usar las consultas de búsqueda de registro siguientes para obtener una lista de todas las recomendaciones ignoradas.

    ```
    Type=SQLAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
3.  Si más tarde decide que desea ver recomendaciones ignoradas, quite los archivos de IgnoreRecommendations.txt o puede quitar RecommendationIDs de ellas.

## <a name="sql-assessment-solution-faq"></a>Preguntas más frecuentes de solución de evaluación de SQL

*¿Con qué frecuencia se ejecuta una evaluación?*
- La evaluación se ejecute cada 7 días.

*¿Hay alguna forma para configurar la frecuencia de ejecución de la evaluación?*
- No en este momento.

*¿Si se detecta otro servidor después de haber agregado la solución de evaluación de SQL, se calculará?*
- Sí, una vez que se detectan se evalúa desde entonces, cada 7 días.

*¿Si un servidor de baja, cuándo se quitará la evaluación?*
- Si un servidor no enviar los datos de 3 semanas, se elimina.

*¿Qué es el nombre del proceso que realiza la recopilación de datos?*
- AdvisorAssessment.exe

*¿Cuánto tardará para que se van a recopilar datos?*
- La recopilación de datos reales en el servidor tarda aproximadamente 1 hora. Puede tardar más en servidores que tienen un gran número de bases de datos o instancias de SQL.

*¿Qué tipo de datos se recopila?*
- Se recopilan los siguientes tipos de datos:
    - WMI
    - Registro
    - Contadores de rendimiento
    - Vistas dinámicas de administración de SQL (DMV).

*¿Hay una forma de configurar cuando se recopilan datos?*
- No en este momento.

*¿Por qué es necesario configurar una cuenta ejecutar como?*
- Para SQL Server, se ejecuta un pequeño número de consultas SQL. En orden para que se ejecuten, debe utilizarse una cuenta ejecutar como con permisos de estado del servidor de la vista SQL.  Además, con el fin de la consulta WMI, se necesitan credenciales de administrador local.

*¿Por qué mostrar solo las recomendaciones de 10 mejores?*
- En lugar de que le da una lista de gran exhaustiva de tareas, se recomienda centrarse en abordar las recomendaciones con prioridad en primer lugar. Después de solucionarlos, recomendaciones adicionales estarán disponibles. Si prefiere ver la lista detallada, puede ver todas las recomendaciones mediante la búsqueda de registros OMS.

*¿Hay alguna forma omitir una recomendación?*
- Sí, vea [recomendaciones de ignorar](#ignore-recommendations) sección anterior.



## <a name="next-steps"></a>Pasos siguientes

- [Registros de búsqueda](log-analytics-log-searches.md) para ver datos detallados de evaluación de SQL y recomendaciones.
