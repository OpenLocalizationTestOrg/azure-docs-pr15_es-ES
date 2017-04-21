<properties
    pageTitle="Crear planes de recuperación | Microsoft Azure" 
    description="Crear planes de recuperación con la recuperación de sitio de Azure conmutar y recuperar grupos de máquinas virtuales y servidores físicos." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="create-recovery-plans"></a>Crear planes de recuperación

El servicio de recuperación de sitios de Azure contribuye a su estrategia de recuperación (BCDR) de desastres y continuidad empresarial por coordinar replicación, migración tras error y recuperación de máquinas virtuales y servidores físicos. Máquinas pueden replicarse en Azure o a un centro de datos secundario en local. Para obtener una introducción rápida leer [¿Qué es la recuperación de sitio de Azure?](site-recovery-overview.md).


## <a name="overview"></a>Información general

En este artículo se proporciona información sobre la creación y personalización de los planes de recuperación. 

Planes de recuperación constan de uno o varios grupos ordenados que contienen máquinas virtuales o servidores físicos que desea conmutar entre sí. Planes de recuperación haga lo siguiente:

- Definir grupos de equipos que conmutarán y a continuación inicie juntas.
- Modelo dependencias entre equipos agrupándolas en un grupo de plan de recuperación. Por ejemplo si desea conmutar y aparezca una aplicación específica desea agrupar las máquinas virtuales de esa aplicación en el mismo grupo de plan de recuperación.
- Automatizar y ampliar la migración tras error. Puede ejecutar una prueba, planeado, o no planeado migración tras error en un plan de recuperación. Puede personalizar los planes de recuperación con las secuencias de comandos, automatización de Azure y acciones manuales.

Planes de recuperación se muestran en los **Planes de recuperación** en el portal de recuperación del sitio.


Publicar comentarios o preguntas en la parte inferior de este artículo o en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Antes de empezar

Tenga en cuenta lo siguiente:

- Un plan de recuperación no mezcla máquinas virtuales con adaptadores de red únicos y múltiples. Esto es porque no se permite mezclar estas máquinas virtuales en un servicio de nube de Azure.
- Puede extender los planes de recuperación con las secuencias de comandos y las acciones manuales. Tenga en cuenta lo siguiente:
    - Escribir secuencias de comandos con Windows PowerShell.
    - Asegúrese de que las secuencias de comandos utilizan bloques try capturas para que las excepciones se administran correctamente. Si hay una excepción en la secuencia de comandos dejará de ejecutarse y la tarea muestra un error.  Si se produce un error, no se ejecutará cualquier parte de la secuencia de comandos restante. Si esto ocurre cuando se ejecuta una migración tras error no planeado, el plan de recuperación seguirá. Si esto ocurre cuando se ejecuta un error planeado, el plan de recuperación se detendrá. Si es así, corrija la secuencia de comandos, asegúrese de que se ejecuta como se esperaba y ejecute de nuevo el plan de recuperación.
    - El comando de escritura Host no funciona en una secuencia de comandos del plan de recuperación y, a continuación, se producirá un error en la secuencia de comandos. Si desea crear el resultado, crear un script de proxy que se ejecuta por separado a la secuencia de comandos principal y asegurarse de que todos los resultados se por departamento utilizando la >> comando.
    - La secuencia de comandos agota el tiempo si no se devuelve dentro de 600 segundos.
    - Si nada se escribe en STDERR, la secuencia de comandos se clasifica como error. Esta información se mostrará en los detalles de la ejecución de scripts.
    - Si usa VMM en la implementación tenga en cuenta que:

        - Secuencias de comandos en un plan de recuperación se ejecutan en el contexto de la cuenta de servicio de VMM. Asegúrese de que esta cuenta tiene permisos de lectura en el recurso compartido remoto donde se encuentra la secuencia de comandos y probar la secuencia de comandos para ejecutar en el nivel de privilegios de cuenta de servicio VMM.
        - Cmdlets de VMM se entregan en un módulo de Windows PowerShell. Está instalado el módulo de VMM Windows PowerShell cuando se instala la consola VMM. Puede cargar el módulo VMM en la secuencia de comandos con el comando siguiente en la secuencia de comandos: módulo de importación-virtualmachinemanager nombre. [Obtener más detalles](hhttps://technet.microsoft.com/library/hh875013.aspx).
        - Asegúrese de que tiene al menos un servidor de biblioteca en la implementación de VMM. De forma predeterminada la ruta de acceso del recurso compartido de biblioteca para un servidor de VMM se encuentra localmente en el servidor VMM con el nombre de carpeta MSCVMMLibrary.
        - Si la ruta de acceso del recurso compartido de biblioteca es remoto (o local pero no se comparte con MSCVMMLibrary, configurar el recurso compartido de manera (con \\libserver2.contoso.com\share\ como ejemplo):
            - Abra el Editor del registro y navegue hasta HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure sitio Recovery\Registration.
            -  Editar el valor ScriptLibraryPath y coloque el valor como \\libserver2.contoso.com\share\. especificar el FQDN completo. Proporcionar permisos a la ubicación compartida.
            -  Asegúrese de probar la secuencia de comandos con una cuenta de usuario que tiene los mismos permisos que la cuenta de servicio VMM, para asegurarse de que se ejecuten scripts probados independientes de la misma manera que aparecerán en los planes de recuperación. En el servidor VMM, establezca la directiva de ejecución omitir como sigue:
                -  Abra la consola de Windows PowerShell de 64 bits con privilegios elevados.
                -  Tipo: **Set-executionpolicy omitir**. [Obtener más detalles](https://technet.microsoft.com/library/ee176961.aspx).

## <a name="create-a-recovery-plan"></a>Crear un plan de recuperación

La manera en que se crea un plan de recuperación depende de la implementación de recuperación de sitios.

- **Replicación de máquinas virtuales de VMware y servidores físicos**: crear un plan y agregar grupos de replicación que contienen máquinas virtuales y servidores físicos a un plan de recuperación.
- **Replicación de máquinas virtuales de Hyper-V (en nubes de VMM)**: crear un plan y agregar protegidas máquinas virtuales de Hyper-V de una nube VMM a un plan de recuperación.
- **Replicación de máquinas virtuales de Hyper-V (no en nubes de VMM)**: crear un plan y agregar protegidas máquinas virtuales de Hyper-V de un grupo de protección a un plan de recuperación.
- **Replicación de SAN**: crear un plan y agregar un grupo de replicación que contiene el plan de recuperación de máquinas virtuales. Seleccione un grupo de replicación en lugar de equipos virtuales específicos porque todos los equipos virtuales en un grupo de replicación debe conmutar entre sí (se produce error en el nivel de almacenamiento en primer lugar).


Crear un plan de recuperación de la siguiente manera:

1. Haga clic en la pestaña de **Planes de recuperación** > **Crear el Plan de recuperación**.
Especifique un nombre para el plan de recuperación de un origen y destino. El servidor de origen debe tener máquinas virtuales que están habilitados para la migración y recuperación.

    - Si va a duplicar desde VMM en VMM seleccionar **Tipo de origen** > **VMM**y los servidores de origen y destino VMM. Haga clic en **Hyper-V** para ver nubes que se haya configurado para usar Hyper-V réplica. 
    - Si está duplicar desde VMM en VMM utilizando SAN seleccione el **Tipo de origen** > **VMM**y los servidores de origen y destino VMM. Haga clic en **SAN** para ver nubes que se han configurado para la replicación de SAN.
    - Si va a duplicar desde VMM en Azure seleccionar **Tipo de origen** > **VMM**.  Seleccione el servidor de origen VMM y **Azure** como destino.
    - Si va a replicar desde un sitio de Hyper-V seleccionar **Tipo de origen** > **Hyper-V sitio**. Seleccione el sitio como el origen y **Azure **como destino.
    - Si está replicar de VMware o en un servidor local físico a Azure, seleccione un servidor de configuración como el origen y **Azure** como destino

2. En **Seleccionar máquinas virtuales** seleccione máquinas virtuales de Windows (o grupo de replicación) que desea agregar al grupo predeterminado (grupo 1) en el plan de recuperación.

## <a name="customize-recovery-plans"></a>Personalizar los planes de recuperación

Una vez haya agregado protegido máquinas virtuales o grupos de replicación en el grupo de plan de recuperación de forma predeterminada y creado el plan que puede personalizar:

- **Agregar nuevos grupos**, puede agregar grupos de plan de recuperación adicional. Los grupos que agregue se numeran en el orden en que se agregan. Puede agregar hasta siete grupos. Puede agregar más equipos o grupos de replicación a estos grupos nuevos. Tenga en cuenta que una máquina virtual o un grupo de replicación sólo puede incluirse en el grupo de recuperación de un plan.
- **Agregar una secuencia de comandos **, puede agregar secuencias de comandos que antes o después de la recuperación de una plan de grupo. Cuando agrega una secuencia de comandos agrega un nuevo conjunto de acciones para el grupo. Por ejemplo se creará con el nombre de un conjunto de pasos anteriores para el grupo 1: 1 de grupo: los pasos anteriores. Todos los pasos anteriores se mostrará dentro de este conjunto. Tenga en cuenta que solo puede agregar una secuencia de comandos en el sitio primario si tiene un servidor VMM implementado.
- **Agregar una acción manual**, puede agregar acciones manuales que se ejecutan antes o después de un grupo de plan de recuperación. Cuando se ejecuta el plan de recuperación, se detiene en el punto en el que se ha insertado la acción manual y un cuadro de diálogo le pide que especifique que se realizó la acción manual.

## <a name="extend-recovery-plans-with-scripts"></a>Extender los planes de recuperación con secuencias de comandos

Puede agregar una secuencia de comandos para su plan de recuperación:

- Si tiene un sitio de origen VMM puede crear una secuencia de comandos en el servidor VMM e incluirlo en su plan de recuperación.
- Si va a duplicar en Azure puede integrar runbooks automatización Azure en el plan de recuperación

### <a name="create-a-vmm-script"></a>Crear un script VMM


Crear la secuencia de comandos de la siguiente manera:

1. Crear una nueva carpeta en el recurso compartido de biblioteca, por ejemplo \<VMMServerName > \MSSCVMMLibrary\RPScripts. Colocarlo en el origen y destino en servidores VMM.
2. Crear la secuencia de comandos (por ejemplo, RPScript) y comprobar que funciona según lo esperado.
3. Colocar la secuencia de comandos en la ubicación \<VMMServerName > \MSSCVMMLibrary en los servidores de origen y destino VMM.

### <a name="create-an-azure-automation-runbook"></a>Crear un runbook de automatización de Azure

Puede ampliar su plan de recuperación ejecutando un runbook de automatización Azure como parte del plan. [Obtenga más información](site-recovery-runbook-automation.md).


### <a name="add-custom-settings-to-a-recovery-plan"></a>Agregar una configuración personalizada a un plan de recuperación

1. Abra el plan de recuperación que desea personalizar.
2. Haga clic para agregar un máquinas virtuales o nuevo grupo.
3. Para agregar una secuencia de comandos o manual de acción haga clic en cualquier elemento de la lista **paso** y, a continuación, haga clic en **secuencia de comandos** o **Acción Manual**. Especifique si desea agregar la secuencia de comandos o acción antes o después del elemento seleccionado. Use los botones de comando **Subir** y **Bajar** para mover la posición de la secuencia de comandos hacia arriba o hacia abajo.
4. Si va a agregar una secuencia de comandos VMM, seleccione **migración tras error para scripts VMM**y en la **Ruta de acceso de secuencia de comandos** , escriba la ruta de acceso relativa para el recurso compartido. Es así, en nuestro ejemplo donde se encuentra en el recurso compartido \\ <VMMServerName>\MSSCVMMLibrary\RPScripts, especifique la ruta de acceso: \RPScripts\RPScript.PS1.
5. Si está agregando una automatización Azure ejecutar libro, especifique la **Cuenta de automatización de Azure** en la que se encuentra runbook y seleccionar el adecuado **Azure Runbook Script**.
5. Realizar una migración tras error del plan de recuperación para asegurarse de que la secuencia de comandos funciona según lo esperado.


## <a name="next-steps"></a>Pasos siguientes

Puede ejecutar diferentes tipos de migraciones tras error en el plan de recuperación, incluyendo una prueba para comprobar su entorno y planificado o de migraciones tras error. [Más información](site-recovery-failover.md).


 
