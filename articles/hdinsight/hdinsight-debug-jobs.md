<properties
    pageTitle="Depurar Hadoop en HDInsight: ver registros e interpretar los mensajes de error | Microsoft Azure"
    description="Obtenga información sobre los mensajes de error que puedes recibir al administrar HDInsight con PowerShell y pasos que puede seguir para recuperar."
    services="hdinsight"
    tags="azure-portal"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="analyze-hdinsight-logs"></a>Analizar los registros de HDInsight

Cada clúster de Hadoop en HDInsight de Azure tiene una cuenta de almacenamiento de Azure usa como el sistema de archivos de forma predeterminada. La cuenta de almacenamiento se conoce como la cuenta de almacenamiento predeterminada. Clúster usa el almacenamiento de tablas de Azure y el almacenamiento de blobs de Windows en la cuenta de almacenamiento predeterminada para almacenar sus registros.  Para la cuenta de almacenamiento predeterminada para el clúster, consulte [Administrar Hadoop clústeres en HDInsight](hdinsight-administer-use-management-portal.md#find-the-default-storage-account). Los registros se conservan en la cuenta de almacenamiento, incluso después de que se elimina el clúster.

##<a name="logs-written-to-azure-tables"></a>Registros que se escriben en las tablas de Azure

Los registros que se escriben en las tablas de Azure proporcionan un nivel de conocimiento de lo que ocurre con un clúster de HDInsight.

Cuando se crea un clúster HDInsight, 6 tablas se crean automáticamente para clústeres basados en Linux en el almacenamiento de tabla predeterminado:

- hdinsightagentlog
- registro del sistema
- daemonlog
- hadoopservicelog
- ambariserverlog
- ambariagentlog

3 tablas se crean para clústeres basados en Windows:

- Setuplog: registro de eventos y excepciones encontrados en aprovisionamiento o configurar clústeres HDInsight.
- hadoopinstalllog: registro de eventos y excepciones al instalar Hadoop en el clúster. Esta tabla puede ser útil para depurar problemas relacionados con clústeres creados con parámetros personalizados.
- hadoopservicelog: registro de eventos y excepciones grabado por todos los servicios de Hadoop. Esta tabla puede ser útil para depurar problemas relacionados con errores de trabajo en clústeres HDInsight.

Los nombres de archivo de la tabla son **u<ClusterName>DDMonYYYYatHHMMSSsss<TableName>**.

Estas tablas contiene los campos siguientes:

- ClusterDnsName
- ComponentName
- Marca horaria del evento
- Host
- MALoggingHash
- Mensaje
- N
- PreciseTimeStamp
- Función
- RowIndex
- Inquilino
- MARCA DE TIEMPO
- TraceLevel

### <a name="tools-for-accessing-the-logs"></a>Herramientas para obtener acceso a los registros

Hay muchas herramientas disponibles para tener acceso a datos en estas tablas:

-  Visual Studio
-  Explorador de almacenamiento de Azure
-  Power Query para Excel

#### <a name="use-power-query-for-excel"></a>Usar Power Query para Excel

Power Query puede instalarse desde [www.microsoft.com/en-us/download/details.aspx?id=39379]( http://www.microsoft.com/en-us/download/details.aspx?id=39379). Consulte la página de descarga de los requisitos del sistema

**Usar Power Query para abrir y analizar el registro del servicio**

1. Abra **Microsoft Excel**.
2. En el menú **Power Query** , haga clic en **De Azure**y, a continuación, haga clic en **almacenamiento de tablas de Azure de Microsoft**.
 
    ![HDInsight Hadoop Excel PowerQuery abrir almacenamiento de tablas de Azure](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Escriba el nombre de la cuenta de almacenamiento. Esto puede ser el nombre corto o el nombre completo.
4. Escriba la clave de cuenta de almacenamiento. Deberá ver una lista de tablas:

    ![Registros de HDInsight Hadoop almacenados en el almacenamiento de tablas de Azure](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Haga clic en la tabla hadoopservicelog en el panel **navegador** y seleccione **Editar**. Deberá ver 4 columnas. Opcionalmente, puede eliminar las columnas de **Clave de partición**, **Fila**y **marca de tiempo** , selecciónelos y haciendo clic en **Quitar columnas** en las opciones de la cinta de opciones.
6. Haga clic en el icono de expandir en la columna contenido para elegir las columnas que desea importar en la hoja de cálculo de Excel. Para esta demostración, se ha elegido TraceLevel y ComponentName: puede me ofrece información básica en la que componentes tenían problemas.

    ![Registros de HDInsight Hadoop Elegir columnas](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png)
7. Haga clic en **Aceptar** para importar los datos.
8. Seleccione las columnas **TraceLevel**, función y **ComponentName** y, a continuación, haga clic en **Agrupar por** control en la cinta de opciones.
9. Haga clic en **Aceptar** en el cuadro de diálogo Agrupar por
10. Haga clic en** Aplicar y cerrar**.
 
Ahora puede usar Excel para filtrar y ordenar según sea necesario. Evidentemente, desea incluir otras columnas (por ejemplo, mensaje) para profundizar en problemas cuando se producen, pero seleccionando y agrupando las columnas que se indica más arriba proporcionan una imagen aceptable de lo que ocurre con los servicios de Hadoop. La misma idea se puede aplicar a las tablas setuplog y hadoopinstalllog.

#### <a name="use-visual-studio"></a>Use Visual Studio

**Usar Visual Studio**

1. Abra Visual Studio.
2. En el menú **Ver** , haga clic en **Explorador de la nube**. O simplemente haga clic en **CTRL +\, CTRL+X**.
3. Desde el **Explorador de la nube**, seleccione **Tipos de recursos**.  La otra opción disponible es **Grupos de recursos**.
4. Expandir **Cuentas de almacenamiento**, la cuenta de almacenamiento predeterminada para el clúster y, a continuación, en **tablas**.
5. Haga doble clic en **hadoopservicelog**.
6. Agregar un filtro. Por ejemplo:
    
        TraceLevel eq 'ERROR'

    ![Registros de HDInsight Hadoop Elegir columnas](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png)

    Para obtener más información sobre cómo crear filtros, consulte [Construir cadenas de filtro para el Diseñador de tablas](../vs-azure-tools-table-designer-construct-filter-strings.md).
 
##<a name="logs-written-to-azure-blob-storage"></a>Registros de escribir en el almacenamiento de blobs de Windows Azure

[Los registros que se escriben en las tablas de Azure](#log-written-to-azure-tables) proporcionan un nivel de conocimiento de lo que ocurre con un clúster de HDInsight. Sin embargo, estas tablas proporcionan registros de nivel de tarea, que pueden ser útil para profundizar más en problemas cuando se producen. Para proporcionar este siguiente nivel de detalle, HDInsight clústeres están configurados para escribir los registros de tareas a su cuenta de almacenamiento de blobs para cualquier tarea que se envía a través de Templeton. En la práctica, esto significa tareas enviadas mediante los cmdlets de PowerShell de Microsoft Azure o las API de envío de trabajo .NET, no las tareas enviadas a través de RDP/comandos-de línea de acceso al clúster. 

Para ver los registros, consulte la [aplicación de Access hilo inicia sesión HDInsight de Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md).

Para obtener más información acerca de los registros de aplicación, vea [administración de registros de usuario Simplifying y acceso en hilo](http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/).
 
 
## <a name="view-cluster-health-and-job-logs"></a>Ver registros de estado y del trabajo de clúster

###<a name="access-hadoop-ui"></a>Interfaz de usuario de Access Hadoop

Desde el Portal de Azure, haga clic en un nombre de clúster HDInsight para abrir el módulo de clúster. En el módulo de clúster, haga clic en **panel**.

![Iniciar el panel de diseño](./media/hdinsight-debug-jobs/hdi-debug-launch-dashboard.png)

Cuando se le pida, escriba las credenciales de administrador de clúster. En la consola de consulta que se abre, haga clic en la **Interfaz de usuario de Hadoop**.

![Interfaz de usuario de inicio Hadoop](./media/hdinsight-debug-jobs/hdi-debug-launch-dashboard-hadoop-ui.png)


###<a name="access-the-yarn-ui"></a>Obtener acceso a la interfaz de usuario del hilo

Desde el Portal de Azure, haga clic en un nombre de clúster HDInsight para abrir el módulo de clúster. En el módulo de clúster, haga clic en **panel**. Cuando se le pida, escriba las credenciales de administrador de clúster. En la consola de consulta que se abre, haga clic en la **Interfaz de usuario del hilo**.

Puede usar la interfaz de usuario del hilo hacer lo siguiente:

* **Obtener el estado de clúster**. En el panel izquierdo, expanda **clúster**y haga clic en **acerca de**. Detalles de estado como total asignado memoria, núcleos usados, estado del Administrador de recursos de clúster de clúster de esta presentación, clúster versión etcetera.

    ![Iniciar el panel de diseño](./media/hdinsight-debug-jobs/hdi-debug-yarn-cluster-state.png)

* **Obtener el estado de nodo**. En el panel izquierdo, expanda **clúster**y haga clic en **los nodos**. Enumera todos los nodos en la dirección HTTP de cada nodo del clúster, los recursos asignados a cada nodo, etcetera.

* **Supervisar el estado de trabajo**. Desde el panel izquierdo, expanda **clúster**y, a continuación, haga clic en **aplicaciones** para obtener una lista de todos los trabajos en el clúster. Si desea ver los trabajos en un estado específico (por ejemplo, la ejecución nuevo, enviado, etc.), haga clic en el vínculo apropiado en **aplicaciones**. Además, puede hacer clic en el nombre del trabajo para obtener más información sobre el trabajo por ejemplo, incluidos los resultados, registros, etcetera.

###<a name="access-the-hbase-ui"></a>Obtener acceso a la interfaz de usuario de HBase

Desde el Portal de Azure, haga clic en un nombre de clúster HDInsight HBase para abrir el módulo de clúster. En el módulo de clúster, haga clic en **panel**. Cuando se le pida, escriba las credenciales de administrador de clúster. En la consola de consulta que se abre, haga clic en la **Interfaz de usuario de HBase**.

## <a name="hdinsight-error-codes"></a>Códigos de error de HDInsight

Los mensajes de error detallados de esta sección se proporcionan para ayudar a los usuarios de Hadoop en HDInsight de Azure entender posibles condiciones de error que pueden encontrar al administrar el servicio con PowerShell de Azure y para informarles de que en los pasos que se pueden tomar para recuperarse del error.

También se podrían ver algunos de estos mensajes de error en el Portal de Azure cuando se usa para administrar los clústeres HDInsight. Otros mensajes de error que pueden surgir, pero hay menos detallado debido a las restricciones en las acciones correctivas posibles en este contexto. Otros mensajes de error se proporcionan en los contextos donde la mitigación es obvia. 

### <a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided
- **Descripción**: proporcione los detalles de la base de datos de SQL Azure para al menos un componente para poder usar una configuración personalizada para la sección y Oozie metastores.
- **Mitigación**: el usuario debe proporcionar un metastore de SQL Azure válida y vuelva a intentar la solicitud.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
- **Descripción**: no se pudo crear un clúster de región *nameOfYourRegion*. Usar una región HDInsight válida y vuelva a intentar la solicitud.
- **Mitigación**: cliente debe crear el área de clúster que admite actualmente: sureste Asia, Europa occidental, Europa del Norte, oriental de Estados Unidos u oeste de Estados Unidos.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
- **Descripción**: el servidor no puede encontrar el registro de clúster solicitado.  
- **Mitigación**: vuelva a intentar la operación.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
- **Descripción**: clúster DNS nombre *yourDnsName* no es válido. Asegúrese de nombre comienza y termina con alfanumérico y sólo puede contener '-' carácter especial  
- **Mitigación**: asegúrese de que ha utilizado un nombre de DNS para el clúster que comienza y termina con alfanuméricos y no contiene especial caracteres excepto el guión '-' y, a continuación, vuelva a intentar la operación.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
- **Descripción**: clúster nombre *yourClusterName* no está disponible. Elija otro nombre.  
- **Mitigación**: el usuario debe especificar un nombreDeClúster que sea único y no existe y vuelva a intentarlo. Si el usuario está utilizando el Portal, la interfaz de usuario les notificará si ya se utiliza durante los pasos de crear un nombre de clúster.


### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
- **Descripción**: clúster contraseña no es válida. Contraseña debe tener al menos 10 caracteres y debe contener al menos un número, mayúscula, minúscula y un carácter especial sin espacios y no debe contener el nombre de usuario como parte de ella.  
- **Mitigación**: proporcione una contraseña de clúster válido y vuelva a intentar la operación.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
- **Descripción**: el nombre de usuario de clúster no es válido. Asegúrese de que nombre de usuario no contiene caracteres especiales o espacios.  
- **Mitigación**: proporcione un nombre de usuario de clúster válido y vuelva a intentar la operación.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
- **Descripción**: clúster DNS nombre *yourDnsClusterName* no es válido. Asegúrese de nombre comienza y termina con alfanumérico y sólo puede contener '-' carácter especial  
- **Mitigación**: proporcione un nombre de usuario de clúster DNS válido y vuelva a intentar la operación.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
- **Descripción**: nombre del contenedor de URI *yourcontainerURI* y DNS nombre *yourDnsName* en el cuerpo de la solicitud deben ser el mismo.  
- **Mitigación**: asegúrese de que el nombre del contenedor y el nombre de DNS son los mismos y vuelva a intentar la operación.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
- **Descripción**: configuración de clúster no válido. No se puede encontrar las definiciones de nodo de datos de tamaño de nodo.  
- **Mitigación**: vuelva a intentar la operación.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
- **Descripción**: error al eliminar de implementación para el clúster  
- **Mitigación**: vuelva a intentar la operación de eliminación.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
- **Descripción**: error de configuración del servicio. Información de asignación DNS requerida que no se encuentra.  
- **Mitigación**: Eliminar clúster y crear un nuevo clúster.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
- **Descripción**: duplicar el intento de creación del contenedor de clúster. No hay registro para *nameOfYourContainer* pero Etags no coinciden.
- **Mitigación**: proporcione un nombre único para el contenedor y vuelva a intentar la operación de creación.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
- **Descripción**: servicio hospedado *nameOfYourHostedService* ya contiene un clúster. Un servicio hospedado no puede contener varios clústeres  
- **Mitigación**: hospedar el clúster en otro servicio hospedado.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
- **Descripción**: el servidor no puede actualizar el estado de la implementación de clúster.  
- **Mitigación**: vuelva a intentar la operación. Si esto sucede varias veces, póngase en contacto con CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
- **Descripción**: se ha eliminado de clúster *yourClusterName* como parte del mantenimiento. Vuelva a crear el clúster.
- **Mitigación**: volver a crear el clúster.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
- **Descripción**: configuración de clúster no válido. Configuración de nodo de cabeza requerido que no se encuentra en tamaños de nodo.
- **Mitigación**: vuelva a intentar la operación.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
- **Descripción**: no se puede crear servicio hospedado *nameOfYourHostedService*. Vuelva a intentar solicitud.  
- **Mitigación**: vuelva a intentar la solicitud.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
- **Descripción**: servicio hospedado *nameOfYourHostedService* ya tiene una implementación de producción. Un servicio hospedado no puede contener varias implementaciones de producción. Vuelva a intentar la solicitud con un nombre de clúster diferente.
- **Mitigación**: usar un nombre de clúster diferente y vuelva a intentar la solicitud.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
- **Descripción**: hospedado servicio *nameOfYourHostedService* para que no se encontró el clúster.  
- **Mitigación**: si el clúster está en estado de error, elimine y vuelva a intentarlo.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
- **Descripción**: servicio hospedado *nameOfYourHostedService* no tiene ninguna implementación asociado.  
- **Mitigación**: si el clúster está en estado de error, elimine y vuelva a intentarlo.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
- **Descripción**: SubscriptionId la *yourSubscriptionId* no tiene núcleos izquierda para crear clúster *yourClusterName*. Requerido: *resourcesRequired*, disponible: *resourcesAvailable*.  
- **Mitigación**: liberar los recursos en la suscripción o aumentar los recursos disponibles para la suscripción e intente volver a crear el clúster.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
- **Descripción**: identificador de suscripción *yourSubscriptionId* carece de cuota para un nuevo HostedService crear clúster *yourClusterName*.  
- **Mitigación**: liberar los recursos en la suscripción o aumentar los recursos disponibles para la suscripción e intente volver a crear el clúster.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
- **Descripción**: el servidor ha encontrado un error interno. Vuelva a intentar solicitud.  
- **Mitigación**: vuelva a intentar la solicitud.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
- **Descripción**: ubicación de almacenamiento de Azure *dataRegionName* no es válida. Asegúrese de que la región sea correcta y vuelva a intentar la solicitud.
- **Mitigación**: seleccione una ubicación de almacenamiento que admita HDInsight, compruebe que el clúster se encuentran y vuelva a intentar la operación.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
- **Descripción**: tamaño VM no válidos para los nodos de datos. Tamaño de 'VM grandes' solo es compatible con todos los nodos de datos.  
- **Mitigación**: especifique el tamaño de nodo compatible para el nodo de datos y vuelva a intentar la operación.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
- **Descripción**: tamaño VM no válido para el nodo principal. Tamaño de 'VM ExtraLarge' solo es compatible con el nodo principal.  
- **Mitigación**: especifique el tamaño de nodo compatible para el nodo principal y vuelva a intentar la operación

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
- **Descripción**: identificador de suscripción *yourSubscriptionId* que se usa no tiene permisos suficientes para ejecutar la operación de eliminación de clúster *yourClusterName*.  
- **Mitigación**: si el clúster está en estado de error, colóquela y vuelva a intentarlo.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
- **Descripción**: almacenamiento externo cuenta blob contenedor nombre *yourContainerName* no es válido. Asegúrese de que nombre empiece por una letra y contiene solo letras minúsculas, números y guiones.  
- **Mitigación**: especifique un nombre de contenedor de almacenamiento válido cuenta blob y vuelva a intentar la operación.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
- **Descripción**: configuración de cuenta de almacenamiento externo *yourStorageAccountName* debe tener detalles de la clave secretas se almacenen.  
- **Mitigación**: especifique una clave secreta válida para la cuenta de almacenamiento y vuelva a intentar la operación.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
- **Descripción**: versión encabezado *yourVersionHeader* formato no es válida de aaaa-mm-dd.  
- **Mitigación**: especifique un formato válido para el encabezado de versión y vuelva a intentar la solicitud.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
- **Descripción**: configuración de clúster no válido. Encuentra más de una configuración de nodo principal.  
- **Mitigación**: editar la configuración para que se especifica que onloy un nodo central.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
- **Descripción**: no se pudo completar la operación en el tiempo permitido o el número máximo de reintentos posibles. Vuelva a intentar solicitud.  
- **Mitigación**: vuelva a intentar la solicitud.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
- **Descripción**: parámetro *yourParameterName* no puede ser nula o vacía.  
- **Mitigación**: especifique un valor válido para el parámetro.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
- **Descripción**: una o varias de las entradas de solicitud de creación de clúster no son válido. Asegúrese de que los valores de entrada son correctos y vuelva a intentar la solicitud.  
- **Mitigación**: asegúrese de que los valores de entrada son correctos y vuelva a intentar la solicitud.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
- **Descripción**: capacidad de región no está disponible para la región *yourRegionName* y *yourSubscriptionId*de Id. de suscripción.  
- **Mitigación**: especifique una región que admite clústeres HDInsight. Las regiones públicamente compatibles son: sureste Asia, Europa occidental, Europa del Norte, oriental de Estados Unidos u oeste de Estados Unidos.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
- **Descripción**: almacenamiento cuenta *yourStorageAccountName* está en la región *currentRegionName*. Debe ser igual que en la región de clúster *yourClusterRegionName*.  
- **Mitigación**: especifique una cuenta de almacenamiento en la misma región que en el clúster o si los datos ya están en la cuenta de almacenamiento, crear un nuevo clúster en la misma región como la cuenta de almacenamiento existente. Si está utilizando el Portal, la interfaz de usuario le notificará de este problema con anticipación.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
- **Descripción**: *yourSubscriptionId* especificado identificador de suscripción no está activa.  
- **Mitigación**: volver a activar la suscripción u obtenga una nueva suscripción válida.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
- **Descripción**: no se encontró el identificador de suscripción *yourSubscriptionId* .  
- **Mitigación**: Compruebe que el identificador de suscripción es válido y vuelva a intentar la operación.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
- **Descripción**: no ha podido resolver DNS *yourDnsUrl*. Asegúrese de que se proporciona la dirección URL completa del extremo de blobs de Windows.  
- **Mitigación**: proporcione una dirección URL de blobs válida. La dirección URL debe ser totalmente válido, incluidos comenzando por *http://* y acabando en *.com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
- **Descripción**: no se puede comprobar la ubicación del recurso *yourDnsUrl*. Asegúrese de que se proporciona la dirección URL completa del extremo de blobs de Windows.  
- **Mitigación**: proporcione una dirección URL de blobs válida. La dirección URL debe ser totalmente válido, incluidos comenzando por *http://* y acabando en *.com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
- **Descripción**: capacidad de versión no está disponible para la versión *specifiedVersion* y *yourSubscriptionId*de Id. de suscripción.  
- **Mitigación**: elegir una versión disponible y vuelva a intentar la operación.

### <a id="VersionNotSupported"></a>VersionNotSupported
- **Descripción**: versión *specifiedVersion* incompatibles.
- **Mitigación**: elegir una versión compatible y vuelva a intentar la operación.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
- **Descripción**: versión *specifiedVersion* no está disponible en la región de Azure *specifiedRegion*.  
- **Mitigación**: elegir una versión que sea compatible con la región especificada y vuelva a intentar la operación.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
- **Descripción**: configuración de clúster no válido. Configuración de cuenta WASB necesaria que no se encuentra en cuentas externas.  
- **Mitigación**: Compruebe que la cuenta existe y se ha especificado correctamente en configuración y vuelva a intentar la operación.

## <a name="next-steps"></a>Pasos siguientes

- [Usar vistas de Ambari depurar Tez trabajos en HDInsight](hdinsight-debug-ambari-tez-view.md)
- [Activar montón volcados Hadoop los servicios de HDInsight de Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
- [Administrar clústeres HDInsight mediante la interfaz de usuario de Web Ambari](hdinsight-hadoop-manage-ambari.md)
