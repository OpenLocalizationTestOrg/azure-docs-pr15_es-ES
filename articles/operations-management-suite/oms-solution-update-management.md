<properties
    pageTitle="Actualizar la solución de administración de OMS | Microsoft Azure"
    description="Este artículo está pensado para ayudarle a entender cómo usar esta solución para administrar las actualizaciones de los equipos de Windows y Linux."
    services="operations-management-suite"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""
    />
<tags
    ms.service="operations-management-suite"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="magoedte"/>

# <a name="update-management-solution-in-omsmediaoms-solution-update-managementupdate-management-solution-iconpng-update-management-solution-in-oms"></a>![Solución de administración de actualización de OMS](./media/oms-solution-update-management/update-management-solution-icon.png) Solución de administración de actualización de OMS

La solución de administración de actualización de OMS le permite administrar las actualizaciones de los equipos de Windows y Linux.  Rápidamente puede evaluar el estado de las actualizaciones disponibles en todos los equipos agente e iniciar el proceso de instalar las actualizaciones necesarias para servidores. 

## <a name="prerequisites"></a>Requisitos previos

-   Agentes de Windows deben estar configurados para comunicarse con un servidor de Windows Server Update Services (WSUS) o tener acceso a Microsoft Update.  

    >[AZURE.NOTE] El agente de Windows no se puede administrar simultáneamente por el Administrador de configuración de System Center.  
  
-   Agentes Linux deben tener acceso a un repositorio de actualización.  El agente de OMS para Linux puede descargarse desde [GitHub](https://github.com/microsoft/oms-agent-for-linux). 

## <a name="configuration"></a>Configuración

Siga estos pasos para agregar la solución de administración de actualizaciones al área de trabajo OMS y agregar a agentes Linux.  Agentes Windows se agregan automáticamente con ninguna configuración adicional.

1.  Agregar la solución de administración de actualizaciones al área de trabajo OMS mediante el proceso descrito en [Agregar OMS soluciones](../log-analytics/log-analytics-add-solutions.md) de la Galería de soluciones.  
2.  En el portal OMS, seleccione **configuración** y, a continuación, **Orígenes conectados**.  Nota el **identificador del área de trabajo** y la **clave principal** o **clave secundaria**.
3.  Siga estos pasos para cada equipo Linux.

    una.  Instalar la última versión del agente OMS para Linux, ejecute los comandos siguientes.  Reemplazar <Workspace ID> con el identificador de área de trabajo y <Key> con la clave principal o secundaria.

        cd ~
        wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/v1.2.0-75/omsagent-1.2.0-75.universal.x64.sh
        sudo bash omsagent-1.2.0-75.universal.x64.sh --upgrade -w <Workspace ID> -s <Key>

     b. Para quitar al agente, ejecute el siguiente comando.

        sudo bash omsagent-1.2.0-75.universal.x64.sh --purge

## <a name="management-packs"></a>Paquetes de administración

Si su grupo de administración de System Center Operations Manager está conectado a su área de trabajo OMS, los paquetes de administración siguientes se instalará en Operations Manager cuando agregue esta solución. No hay ninguna configuración o el mantenimiento de estos paquetes de administración necesarios. 

-   Evaluación de actualización de Microsoft System Center Advisor inteligencia Pack (Microsoft.IntelligencePacks.UpdateAssessment)
-   Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
-   Actualizar el panel de administración de implementación

Para obtener más información sobre cómo se actualizan los paquetes de administración de la solución, consulte [Conectar Operations Manager para el análisis de registro](../log-analytics/log-analytics-om-agents.md).

## <a name="data-collection"></a>Recopilación de datos

### <a name="supported-agents"></a>Agentes compatibles

La siguiente tabla describe las fuentes conectadas que son compatibles con esta solución.

Origen conectada | Compatibles | Descripción|
----------|----------|----------|
Agentes de Windows | Sí | La solución recopila información sobre las actualizaciones del sistema de agentes de Windows e inicia la instalación de las actualizaciones necesarias.|
Agentes Linux | Sí | La solución recopila información sobre las actualizaciones del sistema de agentes Linux.|
Grupo de administración de Operations Manager | Sí | La solución recopila información sobre las actualizaciones del sistema de agentes de un grupo de administración conectados.<br>No se requiere una conexión directa desde el agente de Operations Manager para el análisis de registro. En el repositorio OMS, los datos se reenvían desde el grupo de administración.|
Cuenta de almacenamiento de Azure | No | Almacenamiento de Azure no incluye información sobre las actualizaciones del sistema.|  

### <a name="collection-frequency"></a>Frecuencia de la colección

Para cada equipo de Windows administrado, se realiza un análisis dos veces al día.  Cuando se instala una actualización, la información se actualice en 15 minutos.  

Para cada equipo Linux administrado, un análisis se realiza cada tres horas.  

## <a name="using-the-solution"></a>Con la solución

Al agregar la solución de administración de actualizaciones en el área de trabajo OMS, el mosaico de **La administración de actualizaciones** se agregará a su panel OMS. Este mosaico muestra un recuento y una representación gráfica de la cantidad de equipos en su entorno actualmente que requieren actualizaciones del sistema.<br><br>
![Mosaico de resumen de administración de actualización](media/oms-solution-update-management/update-management-summary-tile.png)  

## <a name="viewing-update-assessments"></a>Evaluación de actualización de visualización

Haga clic en el mosaico de la **Administración de actualización** para abrir el panel de **Administración de la actualización** . El panel incluye las columnas de la tabla siguiente. Cada columna contiene hasta diez elementos que coincidan con los criterios de la columna para el ámbito especificado y el intervalo de tiempo. Puede ejecutar una búsqueda de registro que devuelve todos los registros haciendo clic en **Ver todo** en la parte inferior de la columna o haciendo clic en el encabezado de columna.

Columna | Descripción|
----------|----------|
**Las actualizaciones que faltan los equipos** ||
Tareas críticas o actualizaciones de seguridad | La parte superior actualiza diez equipos que no se encuentran ordenada por el número de actualizaciones de listas estén que falta. Haga clic en el nombre del equipo para ejecutar una búsqueda de registro devuelve que todos los registros de ese equipo de actualización.|
Tareas críticas o actualizaciones de seguridad más de 30 días| Identifica el número de equipos que falta críticas o actualizaciones de seguridad, agrupadas por el período de tiempo, puesto que la actualización se ha publicado. Haga clic en una de las entradas para ejecutar una búsqueda de registro que devuelve todas las actualizaciones que faltan y críticas.|
**Falta la actualización necesaria**||
Tareas críticas o actualizaciones de seguridad | Enumera las clasificaciones de actualizaciones que equipos faltan ordenados por el número de equipos que faltan actualizaciones en la categoría. Haga clic en una clasificación para ejecutar una búsqueda de registros que devuelve que todos los registros para esa clasificación de actualización.|
**Implementaciones de actualización**||
Implementaciones de actualización | Número de implementaciones de actualización programada y la duración de la siguiente ejecución programada.  Haga clic en el icono para ver programaciones, actualmente en ejecución y actualizaciones completadas o para programar una implementación nueva.|  
<br>  
![Panel Resumen de la administración de actualización](./media/oms-solution-update-management/update-management-deployment-dashboard.png)<br>  
<br>
![Actualizar vista de equipo del panel de administración](./media/oms-solution-update-management/update-management-assessment-computer-view.png)<br>  
<br>
![Actualizar vista de paquete de administración de paneles](./media/oms-solution-update-management/update-management-assessment-package-view.png)<br>  

## <a name="installing-updates"></a>Instalar actualizaciones

Una vez que se han evaluado actualizaciones para todos los equipos de Windows en su entorno, puede tienen requeridos actualizaciones instaladas mediante la creación de una *Implementación de la actualización*.  Una implementación de actualización es una instalación programada de actualizaciones necesarias para uno o varios equipos de Windows.  Especifique la fecha y hora para la implementación además de un equipo o un grupo de equipos que se deben incluir.  

Las actualizaciones se instalan runbooks en automatización de Azure.  Actualmente no puede ver estos runbooks y no requieren ninguna configuración.  Cuando se crea una implementación de actualización, crea una programación en que empieza un runbook de actualización principales a la hora especificada en los equipos incluidos.  Este patrón runbook empieza un runbook secundario en cada agente de Windows que realiza la instalación de las actualizaciones necesarias.  

### <a name="viewing-update-deployments"></a>Implementaciones de actualización de visualización

Haga clic en el mosaico de **Implementación de actualización** para ver la lista de implementaciones de actualización existentes.  Se agrupan por estado: **programada**, **Ejecutar**y **completadas**.<br><br> ![Página de programación de actualización implementaciones](./media/oms-solution-update-management/update-updatedeployment-schedule-page.png)<br>  

En la siguiente tabla, se describen las propiedades que se muestran para cada implementación de la actualización.

(Propiedad) | Descripción|
----------|----------|
Nombre | Nombre de la implementación de la actualización.|
Programación | Tipo de programación.  *OneTime* actualmente es el único valor posible.|
Hora de inicio|Fecha y hora en que la implementación de la actualización se ha programado para comenzar.|
Duración | Número de minutos que se permite la ejecución de la implementación de la actualización.  Si todas las actualizaciones no se instalan dentro de esta duración, el resto de las actualizaciones debe esperar hasta la siguiente implementación de actualización.|
Servidores | Número de equipos afectados por la implementación de la actualización.|
Estado | Estado actual de la implementación de la actualización.<br><br>Los valores posibles son:<br>-No comenzada<br>-Ejecutar<br>-Terminado|  

Haga clic en una implementación de actualización para ver su pantalla de detalle que incluye las columnas de la tabla siguiente.  Estas columnas no se rellenará si aún no ha comenzado la implementación de la actualización.<br>

Columna | Descripción|
----------|----------|
**Resultados de equipo**||
Se ha completado correctamente | Muestra el número de equipos de la implementación de actualización por estado.  Haga clic en un estado para ejecutar una búsqueda de registro devolver que todas actualización registros con ese estado para la implementación de actualización.|
Estado de la instalación de equipo| Enumera los equipos implicados en la implementación de la actualización y el porcentaje de las actualizaciones que ha instalado correctamente. Haga clic en una de las entradas para ejecutar una búsqueda de registro que devuelve todas las actualizaciones que faltan y críticas.|
**Resultados de la instancia de actualización**||
Estado de la instalación de instancia | Enumera las clasificaciones de actualizaciones que equipos faltan ordenados por el número de equipos que faltan actualizaciones en la categoría. Haga clic en un equipo para ejecutar una búsqueda de registros que devuelve que todos los registros de ese equipo de actualización.|  
<br><br> ![Información general de los resultados de la implementación de actualización](./media/oms-solution-update-management/update-la-updaterunresults-page.png)

### <a name="creating-an-update-deployment"></a>Creación de una implementación de actualización

Crear una nueva implementación de la actualización haciendo clic en el botón **Agregar** en la parte superior de la pantalla para abrir la página de **Implementación de la actualización de nuevo** .  Debe proporcionar los valores de las propiedades de la tabla siguiente.

(Propiedad) | Descripción|
----------|----------|
Nombre | Nombre único para identificar la implementación de la actualización.|
Zona horaria | Zona horaria para la hora de inicio.|
Hora de inicio | Fecha y hora para iniciar la implementación de la actualización.|
Duración | Número de minutos que se permite la ejecución de la implementación de la actualización.  Si todas las actualizaciones no se instalan dentro de esta duración, el resto de las actualizaciones debe esperar hasta la siguiente implementación de actualización.|
Equipos | Nombres de equipos o grupos que se incluirán en la implementación de la actualización.  Seleccione una o más entradas de la lista desplegable.|
<br><br> ![Nueva página de implementación de actualización](./media/oms-solution-update-management/update-newupdaterun-page.png)

### <a name="time-range"></a>Intervalo de tiempo

De forma predeterminada, es el ámbito de los datos analizados en la solución de administración de actualizaciones de todos los grupos de administración conectados generados dentro del último día 1. 

Para cambiar el intervalo de tiempo de los datos, seleccione **datos basados en** la parte superior del panel. Puede seleccionar registros creado o actualizado dentro de los últimos 7 días, 1 día o 6 horas. O bien, puede seleccionar **personalizada** y especificar un intervalo de fechas personalizado.<br><br> ![Opción de intervalo de tiempo personalizado](./media/oms-solution-update-management/update-la-time-range-scope-databasedon.png)  

## <a name="log-analytics-records"></a>Análisis de registros

La solución de administración de actualización crea dos tipos de registros en el repositorio OMS.

### <a name="update-records"></a>Actualizar los registros

Se crea un registro con un tipo de **actualización** para cada actualización que está instalada o es necesario en cada equipo. Actualizar los registros tienen las propiedades de la tabla siguiente.

(Propiedad) | Descripción|
----------|----------|
Tipo | *Actualización*|
SourceSystem | El origen que aprobado la instalación de la actualización.<br>Los valores posibles son:<br>-Microsoft Update<br>-Windows Update<br>-SCCM<br>-Servidores Linux (que se obtienen los jefes de paquete de)|
Aprobado | Especifica si se ha aprobado la actualización para la instalación.<br> Para servidores Linux esto es actualmente opcional como revisión no administrada por OMS.|
Clasificación para Windows | Clasificación de la actualización.<br>Los valores posibles son:<br>-Aplicaciones<br>-Actualizaciones críticas<br>: Actualizaciones de definición de<br>-Feature Pack<br>: Actualizaciones de seguridad<br>-Service Packs<br>-Actualizar resúmenes<br>: Actualizaciones|
Clasificación para Linux | Cassification de la actualización.<br>Los valores posibles son:<br>-Actualizaciones críticas<br>: Actualizaciones de seguridad<br>-Otras actualizaciones|
Equipo | Nombre del equipo.|
InstallTimeAvailable | Especifica si el tiempo de instalación está disponible en otros agentes que instalar la actualización de la misma.|
InstallTimePredictionSeconds | Tiempo de instalación estimado en segundos en función de otros agentes que instalar la actualización de la misma.|
KBID | Id. del artículo KB que describa la actualización.|
ManagementGroupName | Nombre del grupo de administración de agentes SCOM.  Para otros agentes, esto es AOI -<workspace ID>.|
MSRCBulletinID | Id. del boletín de seguridad de Microsoft que describe la actualización.|
MSRCSeverity | Gravedad del boletín de seguridad de Microsoft.<br>Los valores posibles son:<br>-Crítica<br>-Importante<br>-Moderado|
Opcional | Especifica si la actualización es opcional.|
Producto | Nombre de producto a la actualización.  Haga clic en **Ver** para abrir el artículo en un explorador.|
PackageSeverity | La gravedad de la vulnerabilidad fijada en esta actualización notificado por los proveedores de distro Linux. | 
Fecha de publicación | Fecha y hora en que se ha instalado la actualización.|
RebootBehavior | Especifica si la actualización obliga a reiniciar el equipo.<br>Los valores posibles son:<br>-canrequestreboot<br>-neverreboots|
RevisionNumber | Número de revisión de la actualización.|
SourceComputerId | GUID para identificar el equipo.|
TimeGenerated | Fecha y hora en que se actualizó por última vez el registro.|
Título | Título de la actualización.|
UpdateID | GUID para identificar la actualización.|
UpdateState | Especifica si la actualización está instalada en este equipo.<br>Los valores posibles son:<br>-Instalado, la actualización está instalada en este equipo.<br>-Es necesario - la actualización no está instalada y es necesaria en este equipo.|  

<br>
Cuando se realiza cualquier búsqueda de registros que devuelve registros con un tipo de **actualización** puede seleccionar la vista de **las actualizaciones** que muestra un conjunto de mosaicos resumir las actualizaciones devueltas por la búsqueda. Puede hacer clic en las entradas en el **que falta y actualizaciones** y **actualizaciones obligatorios y opcionales** mosaicos para definir el ámbito de la vista a ese conjunto de actualizaciones. Seleccione la vista de **lista** o **tabla** para devolver los registros individuales.<br> 

![Actualizar vista de búsqueda de registro con la actualización de tipo de registro](./media/oms-solution-update-management/update-la-view-updates.png)  

En la vista de **tabla** , puede hacer clic en **KBID** para cualquier registro para abrir un explorador con el artículo de KB. Esto le permite leer rápidamente sobre los detalles de la actualización específica.<br> 

![Vista de tabla de búsqueda de registro con las actualizaciones de tipo de registro de mosaicos](./media/oms-solution-update-management/update-la-view-table.png)

En la vista de **lista** , haga clic en el vínculo **Ver** junto a la KBID para abrir el artículo de KB.<br>

![Vista de lista de búsqueda de registro con las actualizaciones de tipo de registro de mosaicos](./media/oms-solution-update-management/update-la-view-list.png)


###<a name="updatesummary-records"></a>Registros de UpdateSummary

Se crea un registro con un tipo de **UpdateSummary** para cada equipo de agente de Windows. Este registro se actualiza cada vez que el equipo se analiza actualizaciones. Registros de **UpdateSummary** tienen las propiedades de la tabla siguiente.

(Propiedad) | Descripción|
----------|----------|
Tipo | UpdateSummary|
SourceSystem | OpsManager |
Equipo | Nombre del equipo.|
CriticalUpdatesMissing | Número de actualizaciones críticas que faltan en el equipo.|
ManagementGroupName | Nombre del grupo de administración de agentes SCOM. Para otros agentes, esto es AOI -<workspace ID>.|
NETRuntimeVersion | Versión de .NET runtime instalado en el equipo.|
OldestMissingSecurityUpdateBucket | Se publicó depósito para clasificar la hora desde el más antiguo que faltan actualización de seguridad en este equipo.<br>Los valores posibles son:<br>-Anteriores<br>-180 días<br>-150 días<br>-120 días<br>-90 días<br>-60 días<br>-vaya 30 días.<br>-Recientes|
OldestMissingSecurityUpdateInDays | Se publicó el número de días desde el más antiguo que faltan actualización de seguridad en este equipo.|
OsVersion | Versión del sistema operativo instalado en el equipo.|
OtherUpdatesMissing | Número de otras actualizaciones que faltan en el equipo.|
SecurityUpdatesMissing | Número de actualizaciones de seguridad que faltan en el equipo.|
SourceComputerId | GUID para identificar el equipo.|
TimeGenerated | Fecha y hora en que se actualizó por última vez el registro.|
TotalUpdatesMissing |Número total de actualizaciones que faltan en el equipo.|
WindowsUpdateAgentVersion | Número de versión del agente de Windows Update en el equipo.|
WindowsUpdateSetting | Configuración para el modo en que el equipo instalará actualizaciones importantes.<br>Los valores posibles son:<br>-Deshabilitado<br>-Notificar antes de la instalación<br>-Instalación programada|
WSUSServer | Dirección URL de desconectado si el equipo está configurado para usar una.|  

## <a name="sample-log-searches"></a>Búsquedas de registro de ejemplo

La tabla siguiente proporciona búsquedas de registro de ejemplo para actualizar los registros recopilados por esta solución. 

Consulta | Descripción|
----------|----------|
Todos los equipos con las actualizaciones que faltan | Tipo = actualización UpdateState = es necesario opcional = false & #124; Seleccione el equipo, título, KBID, clasificación, UpdateSeverity, PublishedDate|
Actualizaciones que faltan para equipo "COMPUTER01.contoso.com" (reemplazar con su propio nombre de equipo) | Tipo = actualización UpdateState = es necesario opcional = equipo falso = "COMPUTER01.contoso.com" & #124; Seleccione el equipo, título, KBID, producto, UpdateSeverity, PublishedDate|
Todos los equipos con falta críticos o actualizaciones de seguridad | Tipo = actualización UpdateState = es necesario opcional = false (clasificación = clasificación o de "Actualizaciones de seguridad" = "Actualizaciones críticas")|
Tareas críticas o actualizaciones de seguridad necesarias para equipos donde se aplican manualmente actualizaciones | Tipo = UpdateState de actualización = es necesario opcional = false (clasificación = clasificación o de "Actualizaciones de seguridad" = "Actualizaciones críticas") en equipo {tipo = UpdateSummary WindowsUpdateSetting = #124; & Manual Equipos distintos} & #124; KBID DISTINCT|
Eventos de error para equipos que les faltan crítica o seguridad requerían actualizaciones | Tipo = EventLevelName evento = error en equipo {tipo = actualización (clasificación = clasificación o de "Actualizaciones de seguridad" = "Actualizaciones críticas") UpdateState = es necesario opcional = false & #124; Equipos distintos}|
Todos los equipos con la falta de resúmenes de actualización | Tipo = actualización opcional = false clasificación = "Resúmenes de actualización" UpdateState = es necesario & #124; Seleccione el equipo, título, KBID, clasificación, UpdateSeverity, PublishedDate|
Distintas actualizaciones que faltan en todos los equipos | Tipo = actualización UpdateState = es necesario opcional = false & #124; Título distinto|
Ser miembro del equipo WSUS | Tipo = UpdateSummary & #124; medir count() por WSUSServer|
Configuración de actualización automática | Tipo = UpdateSummary & #124; medir count() por WindowsUpdateSetting|
Equipos con la actualización automática deshabilitada | Tipo = UpdateSummary WindowsUpdateSetting = Manual|  
Lista de todos los equipos Linux que tengan una actualización de paquete disponible | Tipo = actualización y OSType = Linux y UpdateState! = "No necesario" & #124; medir count() por equipo|
Lista de todos los equipos Linux que tengan una actualización de paquete disponible que trata tareas críticas o vulnerabilidad de seguridad | Tipo = actualización y OSType = Linux y UpdateState! = "no se necesitan" y (clasificación = clasificación o de "Actualizaciones críticas" = "Actualizaciones de seguridad") & #124; medir count() por equipo|
Lista de todos los paquetes que tienen una actualización disponible | Tipo = actualización y OSType = Linux y UpdateState! = "No necesario"|
Lista de todos los paquetes que tienen una actualización disponible que trata tareas críticas o vulnerabilidad de seguridad | Tipo = actualización y OSType = Linux y UpdateState! = "no se necesitan" y (clasificación = clasificación o de "Actualizaciones críticas" = "Actualizaciones de seguridad")|
Lista de todos los equipos "Ubuntu" con cualquier actualización disponible | Tipo = actualización y OSType = Linux y OSName = Ubuntu & #124; medir count() por equipo|

## <a name="next-steps"></a>Pasos siguientes

- Usar búsquedas de registro de [Análisis de registro](../log-analytics/log-analytics-log-searches.md) para ver datos detallados de actualización.

- [Crear sus propio paneles](../log-analytics/log-analytics-dashboards.md) que muestra compatibilidad con las actualizaciones de los equipos administrados.

- [Crear alertas](../log-analytics/log-analytics-alerts.md) cuando se detectan actualizaciones críticas como falta de equipos o un equipo que tiene las actualizaciones automáticas deshabilitadas.  




