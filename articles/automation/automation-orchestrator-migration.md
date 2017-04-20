<properties
   pageTitle="Migrar desde Orchestrator automatización de Azure | Microsoft Azure"
   description="Describe cómo migrar paquetes runbooks e integración de System Center Orchestrator automatización de Azure."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="bwren" />


# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrar desde Orchestrator Azure automatización (Beta)

Runbooks en [System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) se basan en las actividades de paquetes de integración que se escriben específicamente para Orchestrator mientras runbooks en Azure automatización se basan en Windows PowerShell.  [Runbooks gráfica](automation-runbook-types.md#graphical-runbooks) en automatización de Azure tiene un aspecto similar al Orchestrator runbooks con sus actividades que representan cmdlets de PowerShell, runbooks secundarios y activos.

El [Kit de herramientas de migración de System Center Orchestrator](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) incluye herramientas para ayudarle a convertir runbooks Orchestrator en automatización de Azure.  Además de convertir el runbooks propiamente dichas, debe convertir los paquetes de integración con las actividades que usan el runbooks a los módulos de integración con los cmdlets de Windows PowerShell.  

A continuación se muestra el proceso básico para convertir Orchestrator runbooks automatización de Azure.  Cada uno de estos pasos se describe en detalle en las secciones siguientes.

1.  Descargar el [Kit de herramientas de migración de System Center Orchestrator](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) que contiene las herramientas y los módulos analizados en este artículo.
2.  Importar [las actividades estándar módulo](#standard-activities-module) Azure automatización.  Esto incluye convertir versiones de actividades Orchestrator estándar que se pueden utilizar runbooks convertida.
3.  Importar [Módulos de integración de System Center Orchestrator](#system-center-orchestrator-integration-modules) a Azure automatización para los paquetes de integración usados por sus runbooks que tener acceso al centro de sistema.
4.  Convertir personalizadas y terceros paquetes de integración con el [Conversor de paquete de integración](#integration-pack-converter) e importar en automatización de Azure.
5.  Convertir runbooks Orchestrator utilizando el [Convertidor Runbook](#runbook-converter) e instale en automatización de Azure.
6.  Crear manualmente los activos de Orchestrator necesarios en la automatización de Azure desde Runbook no convertir estos recursos.
7.  Configurar un [Trabajo de Runbook híbrido](#hybrid-runbook-worker) en el centro de datos local para ejecutar convertir runbooks que tenga acceso a los recursos locales.

## <a name="service-management-automation"></a>Automatización de la administración de servicio

[Automatización de la administración de servicio](http://technet.microsoft.com/library/dn469260.aspx) (SMA) almacena runbooks en el centro de datos local como Orchestrator y ejecuta usa los mismos módulos de integración como automatización de Azure. El [Convertidor Runbook](#runbook-converter) convierte Orchestrator runbooks runbooks gráfica aunque no son compatibles en SMA.  Puede instalar el [Módulo de actividades estándar](#standard-activities-module) y [Módulos de integración de System Center Orchestrator](#system-center-orchestrator-integration-modules) en SMA pero manualmente debe [escribir sus runbooks](http://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Trabajador de Runbook híbrido

Runbooks en Orchestrator se almacenan en un servidor de base de datos y ejecutar en servidores de runbook, tanto en el centro de datos local.  Runbooks en Azure automatización se almacenan en la nube de Azure y se pueden ejecutar en el centro de datos locales con un [Híbrido Runbook trabajo](automation-hybrid-runbook-worker.md).  Esto es cómo normalmente se ejecutará runbooks convertida de Orchestrator ya están diseñados para ejecutar en servidores locales.

## <a name="integration-pack-converter"></a>Convertidor de paquete de integración

El convertidor de paquete de integración convierte los paquetes de integración que se crearon usando el [Kit de herramientas de integración de Orchestrator (OIT)](http://technet.microsoft.com/library/hh855853.aspx) para módulos de integración basados en Windows PowerShell que se pueden importar a Azure automatización o automatización de la administración de servicio.  

Cuando se ejecuta el convertidor de paquete de integración, se presentan con un asistente que le permitirá seleccionar un archivo de paquete (.oip) de integración.  El asistente, a continuación, enumera las actividades que se incluyen en dicho paquete de integración y le permite seleccionar que se migrará.  Cuando finalice el asistente, crea un módulo de integración que incluye un cmdlet correspondiente para cada una de las actividades en el módulo de integración original.


### <a name="parameters"></a>Parámetros

Las propiedades de una actividad en el módulo de integración se convierten en los parámetros del cmdlet correspondiente en el módulo de integración.  Cmdlets de Windows PowerShell tiene un conjunto de [parámetros comunes](http://technet.microsoft.com/library/hh847884.aspx) que se pueden usar con todos los cmdlets.  Por ejemplo,-parámetro detallado provoca un cmdlet para obtener información detallada sobre el funcionamiento de salida.  Cmdlet no puede tener un parámetro con el mismo nombre que un parámetro común.  Si una actividad tiene una propiedad con el mismo nombre que un parámetro común, el asistente le pedirá que proporcione otro nombre para el parámetro.

### <a name="monitor-activities"></a>Supervisar las actividades

Monitor runbooks en Orchestrator empezar con una [supervisar la actividad](http://technet.microsoft.com/library/hh403827.aspx) y ejecutar continuamente esperando para invocar un evento determinado.  Automatización de Azure no admite runbooks monitor, por lo que no se convertirán las actividades de monitor en el módulo de integración.  En su lugar, se crea un cmdlet de marcador de posición en el módulo de integración para supervisar la actividad.  Este cmdlet no tiene ninguna funcionalidad, pero permite cualquier runbook convertida que usa para instalarse.  Este runbook no podrá ejecutar en la automatización de Azure, pero se puede instalar para que se puede modificar.

### <a name="integration-packs-that-cannot-be-converted"></a>Paquetes de integración no se puede convertir

No se puede convertir paquetes de integración que no se crearon con OIT con el conversor de paquete de integración. También hay algunos paquetes de integración proporcionados por Microsoft que actualmente no se puede convertir con esta herramienta.  Convertir versiones de estos paquetes de integración han sido [proporcionado para su descarga](#system-center-orchestrator-integration-modules) para que se pueden instalar en Azure automatización o automatización de la administración de servicio.


## <a name="standard-activities-module"></a>Módulo de actividades estándar

Orchestrator incluye un conjunto de [actividades estándar](http://technet.microsoft.com/library/hh403832.aspx) que no se incluirán en un paquete de integración pero se utilizan en muchos runbooks.  El módulo de actividades estándar es un módulo de integración que incluye un cmdlet equivalente para cada una de estas actividades.  Debe instalar este módulo de integración de Azure automatización antes de importar cualquier runbooks convertida que use una actividad estándar.

Además de admitir runbooks convertidos, los cmdlets en el módulo de actividades estándar puede ser usados por el familiarizado con Orchestrator crear runbooks nuevo en automatización de Azure.  Mientras que la funcionalidad de todas las actividades estándares se puede realizar con los cmdlets, pueden funcionar de forma diferente.  Los cmdlets en el módulo de actividades estándar convertida se funcionan igual que sus actividades correspondientes y los mismos parámetros.  Esto puede ayudar al autor del runbook Orchestrator existente en la transición a runbooks automatización de Azure.

## <a name="system-center-orchestrator-integration-modules"></a>Módulos de integración de System Center Orchestrator

Microsoft proporciona [paquetes de integración](http://technet.microsoft.com/library/hh295851.aspx) para generar runbooks para automatizar otros productos y componentes de System Center.  Algunos de estos paquetes de integración actualmente se basan en OIT pero actualmente no se puede convertir a los módulos de integración debido a problemas conocidos.  [Módulos de integración de System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) incluye convertir versiones de estos paquetes de integración que se pueden importar a la automatización de Azure y automatización de administración de servicio.  

La versión RTM de esta herramienta, se publicará versiones actualizadas de los paquetes de integración basadas en OIT que se puede convertir con el conversor de paquete de integración.  También se proporcionará instrucciones para ayudarle a convertir runbooks con las actividades de los paquetes de integración no basados en OIT.

## <a name="runbook-converter"></a>Convertidor de runbook

Convertidor de Runbook convierte Orchestrator runbooks en [runbooks gráficos](automation-runbook-types.md#graph-runbooks) que se pueden importar a automatización de Azure.  

Convertidor de runbook se implementa como un módulo de PowerShell con un cmdlet denominado **SCORunbook ConvertFrom** que realiza la conversión.  Al instalar la herramienta, creará un acceso directo a una sesión de PowerShell que carga el cmdlet.   

A continuación se muestra el proceso básico para convertir un runbook Orchestrator e impórtelo automatización de Azure.  Las siguientes secciones proporcionan más detalles sobre el uso de la herramienta y trabajar con runbooks convertida.

1. Exportar runbooks uno o más de Orchestrator.
2. Obtener módulos de integración para todas las actividades en runbook.
3. Convertir la runbooks Orchestrator en el archivo exportado.
4. Revise la información en los registros para validar la conversión y determinar las tareas manuales necesarias.
4. Importar runbooks convertida en automatización de Azure.
5. Crear los activos necesarios en automatización de Azure.
6. Editar runbook de automatización de Azure para modificar las actividades necesarias.

### <a name="using-runbook-converter"></a>Usar Runbook convertidor

La sintaxis de **ConvertFrom SCORunbook** es la siguiente:

    ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string> 

- RunbookPath - ruta de acceso al archivo de exportación que contiene la runbooks a convertir.
- Módulo: lista separada por comas de módulos de integración que contiene las actividades de la runbooks.
- OutputFolder - ruta de acceso a la carpeta para crear convierte runbooks gráfica. 


El siguiente comando de ejemplo convierte la runbooks en un archivo de exportación denominado **MyRunbooks.ois_export**.  Estos runbooks usar los paquetes de integración de Active Directory y el Administrador de protección de datos.

    ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks" 


### <a name="log-files"></a>Archivos de registro

Convertidor de Runbook creará los archivos de registro siguiente en la misma ubicación que runbook convertida.  Si los archivos que ya existen, se sobrescribirán con la información de la última conversión.

| Archivo | Contenido |
|:---|:---|
| Convertidor de runbook - Progress.log | Pasos detallados de la conversión con información para cada actividad convertido correctamente y advertencia para cada actividad no convertir. |
| Convertidor de runbook - Summary.log  | Resumen de la última conversión incluidas algunas advertencias y tareas que se deben llevar a cabo como la creación de una variable necesaria para convertir runbook de seguimiento.  |

### <a name="exporting-runbooks-from-orchestrator"></a>Exportar runbooks de Orchestrator

Convertidor de Runbook funciona con un archivo de exportación de Orchestrator que contiene una o más runbooks.  Crea un runbook de automatización de Azure correspondiente para cada runbook Orchestrator en el archivo de exportación.  

Para exportar un runbook de Orchestrator, haga clic en el nombre del runbook en Runbook Designer y seleccione **Exportar**.  Para exportar todos los runbooks en una carpeta, haga clic en el nombre de la carpeta y seleccione **Exportar**.


### <a name="runbook-activities"></a>Actividades de runbook

Convertidor de Runbook convierte cada actividad en runbook Orchestrator a una actividad correspondiente en la automatización de Azure.  Para las actividades que no se puede convertir, se crea una actividad de marcador de posición en runbook con texto de advertencia.  Después de importar runbook convertida en automatización de Azure, debe reemplazar cualquiera de estas actividades con actividades válidas que lleve a cabo la funcionalidad necesaria.

Se convertirán las actividades de Orchestrator en el [Módulo estándar de actividades](#standard-activities-module) .  Existen algunas actividades de Orchestrator estándares que no están en este módulo aunque y no se convierten.  Por ejemplo, **Enviar evento de plataforma** no tiene equivalente de automatización de Azure ya que el evento es específico de Orchestrator.

[Supervisar las actividades](https://technet.microsoft.com/library/hh403827.aspx) no se convierten porque no hay ningún equivalente a ellos en la automatización de Azure.  La excepción son las actividades de monitor en [convertir paquetes de integración](#integration-pack-converter) que se convertirá en la actividad de marcador de posición.

Se convierten cualquier actividad desde un [paquete de integración de convertir](#integration-pack-converter) si proporciona la ruta de acceso para el módulo de integración con el parámetro de **módulos** .  Para paquetes de integración de centro de sistema, puede utilizar los [Módulos de integración de System Center Orchestrator](#system-center-orchestrator-integration-modules).


### <a name="orchestrator-resources"></a>Recursos de Orchestrator

Convertidor de Runbook solo convierte runbooks, no los otros recursos Orchestrator como contadores, variables o conexiones.  Contadores no son compatibles con la automatización de Azure.  Variables y conexiones son compatibles, pero debe crear manualmente.  Los archivos de registro se le informará si runbook requiere estos recursos y especificar recursos correspondientes que necesita para crear en la automatización de Azure para convertir runbook funcione correctamente.

Por ejemplo, un runbook puede utilizar una variable para rellenar un valor concreto de una actividad.  Convertir runbook convertirá la actividad y especificar un variable activo en automatización de Azure con el mismo nombre que la variable Orchestrator.  Esto se se indica en el archivo del **Convertidor de Runbook - Summary.log** creadas después de la conversión.  Debe crear manualmente este activo variable en Azure automatización antes de utilizar runbook. 

 
### <a name="input-parameters"></a>Parámetros de entrada

Runbooks en Orchestrator aceptar parámetros de entrada con la actividad de **Inicializar datos** .  Si runbook convertirse incluye esta actividad, se crea un [parámetro de entrada](automation-graphical-authoring-intro.md#runbook-input-and-output) en Azure automatización runbook para cada parámetro de la actividad.  Se crea una actividad de [control de secuencia de comandos de flujo de trabajo](automation-graphical-authoring-intro.md#activities) en convertir runbook que recupera y devuelve cada parámetro.  Las actividades en runbook que utilizan un parámetro de entrada hacen referencia a la salida de esta actividad.

El motivo por el que se usa esta estrategia es mejor reflejan la funcionalidad de Orchestrator runbook.  Actividades de nuevo runbooks gráfica deben consultar directamente para usar un origen de datos de entrada Runbook de parámetros de entrada.


### <a name="invoke-runbook-activity"></a>Invocar Runbook actividad

Runbooks en Orchestrator iniciar otros runbooks con la actividad **Invocar Runbook** . Si runbook convertirse incluye esta actividad y se establece la opción **esperar a que finalice** , una actividad runbook se crea para él en runbook convertida.  Si no se establece la opción **esperar a que finalice** , se crea una actividad de secuencia de comandos de flujo de trabajo que usa **AzureAutomationRunbook de inicio** para iniciar runbook.  Después de importar runbook convertida en automatización de Azure, debe modificar esta actividad con la información especificada en la actividad.




## <a name="related-articles"></a>Artículos relacionados

- [System Center 2012 - Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
- [Automatización de la administración de servicio](https://technet.microsoft.com/library/dn469260.aspx)
- [Trabajador de Runbook híbrido](automation-hybrid-runbook-worker.md)
- [Actividades de Orchestrator estándar](http://technet.microsoft.com/library/hh403832.aspx)
- [Kit de herramientas de migración de descargar System Center Orchestrator](https://www.microsoft.com/en-us/download/details.aspx?id=47323)
 
