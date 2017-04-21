<properties
   pageTitle="Solucionar errores comunes de implementación de Azure | Microsoft Azure"
   description="Describe cómo resolver errores comunes al implementar recursos en Azure con el Administrador de recursos de Azure."
   services="azure-resource-manager"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"
   keywords="error de implementación, implementación de azure, implementar en azure"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="tomfitz"/>

# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Solucionar errores comunes de implementación de Azure con el Administrador de recursos de Azure

Este tema describe cómo puede resolver algunos comunes que puede encontrarse de errores de implementación de Azure. Si necesita más información sobre qué ha fallado con la implementación, consulte en primer lugar [las operaciones de implementación de vista](resource-manager-troubleshoot-deployments-portal.md) y, a continuación, vuelva a este artículo para que le ayude a resolver el error. Las secciones en este tema enumeran ve el código de error.

## <a name="invalid-template"></a>Plantilla no válido

Este error puede producirse de diferentes tipos de errores. 

### <a name="syntax-error"></a>Error de sintaxis

Si recibe un mensaje de error que indica la validación de la plantilla no se pudo, puede tener un problema de sintaxis en la plantilla. 

    Code=InvalidTemplate 
    Message=Deployment template validation failed

Este error es fácil de solucionar porque las expresiones de plantilla pueden ser complicadas. Por ejemplo, la siguiente asignación de nombre de una cuenta de almacenamiento contiene un conjunto de corchetes, tres funciones, tres conjuntos de paréntesis, un conjunto de comillas simples y una propiedad:

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

Si no se proporciona la sintaxis de coincidencia, la plantilla genera un valor que es diferente a su intención.

Si recibe este tipo de error, detenidamente la sintaxis de la expresión. Tenga en cuenta con un editor de JSON como [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) o [Código de Visual Studio](resource-manager-vs-code.md), que puede avisar sobre errores de sintaxis. 

### <a name="incorrect-segment-lengths"></a>Longitud de segmento incorrecto

Cuando el nombre del recurso no está en el formato correcto, se produce otro error de plantilla no válido.

    Code=InvalidTemplate
    Message=Deployment template validation failed: 'The template resource {resource-name}' 
    for type {resource-type} has incorrect segment lengths.

Un recurso de nivel raíz debe tener un segmento de menos en el nombre que en el tipo de recurso. Cada segmento se diferencia por una barra diagonal. En el ejemplo siguiente, el tipo tiene dos segmentos y el nombre tiene un segmento, por lo que es un **nombre válido**.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "myHostingPlanName",
      ...
    }

Pero el ejemplo siguiente **no es un nombre válido** porque tiene el mismo número de segmentos como tipo.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "appPlan/myHostingPlanName",
      ...
    }

Para los recursos secundarios, el tipo y el nombre tienen el mismo número de segmentos. Este número de segmentos sentido porque el nombre completo y el tipo secundario incluye el nombre de elemento primario y tipo. Por lo tanto, el nombre completo aún tiene un segmento de menor que el tipo de total. 

    "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "contosokeyvault",
            ...
            "resources": [
                {
                    "type": "secrets",
                    "name": "appPassword",
                    ...
                }
            ]
        }
    ]

Obtener los segmentos derecha puede ser difícil con los tipos de administrador de recursos que se aplican a través de proveedores de recursos. Por ejemplo, al aplicar un bloqueo de recursos a un sitio web, requiere un tipo de cuatro segmentos. Por lo tanto, el nombre es tres segmentos:

    {
        "type": "Microsoft.Web/sites/providers/locks",
        "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
        ...
    }

### <a name="copy-index-is-not-expected"></a>Índice de copia no se espera

Aparece este error **InvalidTemplate** cuando el elemento de la **copia** que haya aplicado a una parte de la plantilla que no admite este elemento. El elemento copiar solo se aplican a un tipo de recurso. No se puede aplicar copia a una propiedad dentro de un tipo de recurso. Por ejemplo, aplicar copia a una máquina virtual, pero no puede aplicar a los discos de sistema operativo para una máquina virtual. En algunos casos, puede convertir un recurso secundario a un recurso principal para crear un bucle de copia. Para obtener más información sobre el uso de la copia, vea [crear varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md).

### <a name="parameter-is-not-valid"></a>Parámetro no es válido

Si la plantilla especifica los valores permitidos para un parámetro y proporciona un valor que no es uno de esos valores, recibirá un mensaje similar al siguiente error:

    Code=InvalidTemplate; 
    Message=Deployment template validation failed: 'The provided value {parameter vaule} 
    for the template parameter {parameter name} is not valid. The parameter value is not 
    part of the allowed value(s)

Doble Compruebe los valores permitidos en la plantilla y proporcione una durante la implementación.

## <a name="not-found-or-resource-not-found"></a>No se encontró (o no se encontró el recurso)

Cuando la plantilla incluye el nombre de un recurso que no se puede resolver, recibe un error similar al:

    Code=NotFound;
    Message=Cannot find ServerFarm with name exampleplan.

Si está intentando implementar el recurso que falta en la plantilla, compruebe si necesita agregar una dependencia. Administrador de recursos optimiza implementación mediante la creación de recursos en paralelo, cuando sea posible. Si un recurso se debe implementar después otro recurso, debe usar el elemento **dependsOn** en la plantilla para crear una dependencia en otro recurso. Por ejemplo, al implementar una aplicación web, debe existir el plan de servicios de aplicación. Si no ha especificado depende de la aplicación web en el plan de servicio de aplicaciones, Administrador de recursos crea dos recursos al mismo tiempo. Recibe un error que indica que no se encuentra el recurso de plan de servicio de aplicaciones, porque no existe aún al intentar establecer una propiedad en la aplicación web. Evitar este error estableciendo la dependencia en la aplicación web.

    {
      "apiVersion": "2015-08-01",
      "type": "Microsoft.Web/sites",
      ...
      "dependsOn": [
        "[variables('hostingPlanName')]"
      ],
      ...
    }
 
También puede ver este error cuando el recurso existe en otro grupo de recursos a la que se implementa en. En ese caso, use la [función resourceId](resource-group-template-functions.md#resourceid) para obtener el nombre completo del recurso.

    "properties": {
        "name": "[parameters('siteName')]",
        "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
    } 

Si intenta utilizar las funciones de [referencia](resource-group-template-functions.md#referenc) o [listKeys](resource-group-template-functions.md#listkeys) con un recurso que no se puede resolver, recibirá el siguiente error:

    Code=ResourceNotFound;
    Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource 
    group {resource group name} was not found.

Busque una expresión que incluye la función de **referencia** . Compruebe que los valores de parámetro están correctos.

## <a name="storage-account-already-exists-or-already-taken"></a>Cuenta de almacenamiento ya existe (o ya está hecho)

Para las cuentas de almacenamiento, debe proporcionar un nombre para el recurso que es único en Azure. Si no se proporciona un nombre único, recibirá un error como:

    Code=StorageAccountAlreadyTaken 
    Message=The storage account named mystorage is already taken.

Puede crear un nombre único concatenando la convención de nomenclatura con el resultado de la función [uniqueString](resource-group-template-functions.md#uniquestring) .

    "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]",
    "type": "Microsoft.Storage/storageAccounts",

Si implementar una cuenta de almacenamiento con el mismo nombre que una cuenta existente de almacenamiento de su suscripción, pero proporcionar una ubicación diferente, recibirá un error que indica que ya existe la cuenta de almacenamiento en una ubicación diferente. Elimine la cuenta de almacenamiento existente, o proporcione la misma ubicación que la cuenta de almacenamiento existente.

## <a name="account-name-invalid"></a>Nombre de cuenta no válido

Consulte el error **AccountNameInvalid** cuando intenta asigne un nombre que incluye a una cuenta de almacenamiento prohibido caracteres. Nombres de cuenta de almacenamiento deben estar entre 3 y 24 caracteres de longitud y utilizar solo letras minúsculas y números.

## <a name="no-registered-provider-found"></a>No se encuentra el proveedor registrado

Al implementar recursos, puede recibir el siguiente código de error y el mensaje:

    Code: NoRegisteredProviderFound
    Message: No registered resource provider found for location {ocation} 
    and API version {api-version} for type {resource-type}.

Recibe este error de uno de los tres razones:

1. No se admite para el tipo de recurso de ubicación
2. API versión no compatible con el tipo de recurso
3. No se ha registrado el proveedor de recursos para la suscripción

El mensaje de error debe dar sugerencias para las versiones de API y ubicaciones compatibles. Puede cambiar la plantilla a uno de los valores sugeridos. La mayoría de los proveedores son registrados automáticamente por el portal de Azure o la interfaz de línea de comandos que está usando, aunque no todos. Si no ha utilizado un proveedor de recursos determinado antes, necesitará registrar ese proveedor. Encontrará más información acerca de los proveedores de recursos a través de PowerShell o CLI de Azure.

### <a name="powershell"></a>PowerShell

Para ver el estado de registro, use **Get-AzureRmResourceProvider**.

    Get-AzureRmResourceProvider -ListAvailable

Para registrar un proveedor, use **AzureRmResourceProvider registrar** y proporcionar el nombre del proveedor del recurso que desea registrar.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

Para obtener las ubicaciones compatibles para un determinado tipo de recurso, use:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

Para obtener las versiones de API compatibles para un determinado tipo de recurso, use:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### <a name="azure-cli"></a>CLI de Azure

Para ver si está registrado el proveedor, use la `azure provider list` comando.

    azure provider list

Para registrar un proveedor de recursos, utilice el `azure provider register` comando y especifique el *espacio de nombres* para registrar.

    azure provider register Microsoft.Cdn

Para ver las ubicaciones compatibles y las versiones API para un proveedor de recursos, use:

    azure provider show -n Microsoft.Compute --json > compute.json

## <a name="operation-not-allowed"></a>Operación no permitida

Es posible que tenga problemas cuando implementación supera una cuota, que podría ser por grupo de recursos, suscripciones, cuentas y otros ámbitos. Por ejemplo, la suscripción puede estar configurada para limitar el número de núcleos de una región. Si intenta implementar una máquina virtual con más núcleos que la cantidad permitida, recibirá un error para indicar que se ha superado la cuota.
Para obtener información completa de cuota, vea [suscripción Azure y límites de servicio, cuotas y las restricciones](azure-subscription-service-limits.md).

Para examinar las cuotas de su suscripción de núcleos, puede usar el `azure vm list-usage` comando en CLI Azure. En el ejemplo siguiente se muestra que la cuota de núcleo para una cuenta de prueba gratuita es 4:

    azure vm list-usage

Que devuelve:

    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

Si implementa una plantilla que crea más de cuatro núcleos en la región Oeste US, recibe un error de implementación es similar:

    Code=OperationNotAllowed
    Message=Operation results in exceeding quota limits of Core. 
    Maximum allowed: 4, Current in use: 4, Additional requested: 2.

O bien, en PowerShell, puede usar el cmdlet **Get-AzureRmVMUsage** .

    Get-AzureRmVMUsage

Que devuelve:

    ...
    CurrentValue : 0
    Limit        : 4
    Name         : {
                     "value": "cores",
                     "localizedValue": "Total Regional Cores"
                   }
    Unit         : null
    ...

En estos casos, debe ir al portal y archivo un problema de soporte técnico para elevar su cuota de la región en la que desea implementar.

> [AZURE.NOTE] Recuerde que para los grupos de recursos, es la cuota para cada región individual, no para toda la suscripción. Si necesita implementar 30 núcleos en oeste de Estados Unidos, debe pedir 30 núcleos del Administrador de recursos en Estados Unidos oeste. Si necesita implementar 30 núcleos en cualquiera de las regiones a los que tiene acceso, debe pedir 30 núcleos del Administrador de recursos en todas las regiones.

## <a name="invalid-content-link"></a>Vínculo de contenido no válido

Cuando reciba el mensaje de error:

    Code=InvalidContentLink
    Message=Unable to download deployment content from ...

Es muy probable que ha intentado vincular a una plantilla anidada que no está disponible. Compruebe el URI proporcionado para la plantilla anidada. Si la plantilla se encuentra en una cuenta de almacenamiento, asegúrese de que el URI es accesible. Debe pasar un token SA. Para obtener más información, consulte [uso de plantillas vinculadas con el Administrador de recursos de Azure](resource-group-linked-templates.md).

## <a name="authorization-failed"></a>Error de autorización

Puede recibir un error durante la implementación porque no tiene acceso a realizar las acciones que la cuenta o el servicio principal al intentar implementar los recursos. Permite Azure Active Directory o el administrador para controlar qué identidades puede tener acceso a los recursos con un alto grado de precisión. Por ejemplo, si su cuenta se asigna a la función Lector, no es puede crear recursos. En este caso, verá un mensaje de error que indica que no se pudo autorización.

Para obtener más información acerca de control de acceso basado en roles, vea [Control de acceso de Azure Role-Based](./active-directory/role-based-access-control-configure.md).

Además de control de acceso basado en roles, las acciones de implementación pueden estar limitadas por las directivas de la suscripción. A través de directivas, el administrador puede forzar convenciones en todos los recursos que se implementa en la suscripción. Por ejemplo, un administrador puede requerir que se proporciona un valor de etiqueta específica para un tipo de recurso. Si no se cumplen los requisitos de la directiva, recibirá un error durante la implementación. Para obtener más información acerca de las directivas, vea [Utilizar una directiva para administrar los recursos y controlar el acceso](resource-manager-policy.md).

## <a name="troubleshooting-virtual-machines"></a>Máquinas virtuales de solución de problemas

| Error | Artículos |
| -------- | ----------- |
| Errores de script personalizado de extensión | [Errores de extensión de máquina virtual de Windows](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md)<br />o<br />[Errores de extensión Linux VM](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md) |
| Imagen del sistema operativo errores de aprovisionamiento | [Nuevos errores de máquina virtual de Windows](./virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md)<br />o<br />[Nuevos errores Linux VM](./virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md ) |
| Errores de asignación | [Errores de asignación de máquina virtual de Windows](./virtual-machines/virtual-machines-windows-allocation-failure.md)<br />o<br />[Errores de asignación de Linux VM](./virtual-machines/virtual-machines-linux-allocation-failure.md) |
| Secure Shell (SSH) errores al intentar conectarse | [Conexiones de Shell seguras Linux VM](./virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md) |
| Errores al conectarse a la aplicación que se ejecuta en máquina virtual | [Aplicación que se ejecuta en máquina virtual de Windows](./virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md)<br />o<br />[Aplicación que se ejecuta en una VM Linux](./virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md) |
| Errores de conexión a Escritorio remoto | [Conexiones a Escritorio remoto para máquina virtual de Windows](./virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md) |
| Errores de conexión resueltos reinstalación | [Volver a máquina Virtual en nuevo nodo de Azure](./virtual-machines/virtual-machines-windows-redeploy-to-new-node.md) |
| Errores del servicio de nube | [Problemas de implementación de servicio de nube](./cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

## <a name="troubleshooting-other-services"></a>Solución de problemas de otros servicios

En la tabla siguiente no es una lista completa de temas de solución de problemas de Azure. En su lugar, se centra en problemas relacionados con la implementación o configuración de los recursos. Si necesita ayuda para solucionar problemas de tiempo de ejecución con un recurso, consulte la documentación de ese servicio Azure.

| Servicio | Artículo |
| -------- | -------- |
| Automatización | [Consejos para solucionar errores comunes en la automatización de Azure](./automation/automation-troubleshooting-automation-errors.md) |
| Pila de Azure | [Solución de problemas de pila de Microsoft Azure](./azure-stack/azure-stack-troubleshooting.md) |
| Pila de Azure | [Aplicaciones Web y pila de Azure](./azure-stack/azure-stack-webapps-troubleshoot-known-issues.md ) |
| Generador de datos | [Solucionar problemas del generador de datos](./data-factory/data-factory-troubleshoot.md) |
| Tela de servicio | [Solucionar problemas comunes al implementar servicios en tela de servicio de Azure](./service-fabric/service-fabric-diagnostics-troubleshoot-common-scenarios.md) |
| Recuperación de sitio | [Supervisar y solucionar problemas de protección para máquinas virtuales y servidores físicos](./site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| Almacenamiento de información | [Supervisar, diagnosticar y solucionar problemas de Microsoft Azure almacenamiento](./storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple | [Solucionar problemas de implementación de dispositivo StorSimple](./storsimple/storsimple-troubleshoot-deployment.md) |
| Base de datos SQL | [Solucionar problemas de conexión a la base de datos de SQL Azure](./sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| Almacén de datos SQL | [Solución de problemas de almacén de datos SQL Azure](./sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## <a name="understand-when-a-deployment-is-ready"></a>Comprender cuándo está lista una implementación

Administrador de recursos de Azure informes de éxito en una implementación cuando termina de todos los proveedores de implementación correctamente. Sin embargo, este mensaje no necesariamente significa que el grupo de recursos es "activa y listo para los usuarios". Por ejemplo, una implementación necesite descargar actualizaciones, espere en recursos de plantilla no o instalar alfabetos complejos. Administrador de recursos no sabe cuando se completan estas tareas porque no son actividades que realiza un seguimiento de un proveedor. En estos casos, puede ser algún tiempo antes de que los recursos están listos para uso del mundo real. Como resultado, debe esperar a que el estado de la implementación se realiza correctamente algún tiempo antes de poder usar su implementación.

Puede impedir que Azure e informar del éxito de la implementación, sin embargo, mediante la creación de un script personalizado de la plantilla personalizada. La secuencia de comandos sepa cómo supervisar la implementación completa para todo el sistema de disponibilidad y devuelve correctamente solo cuando los usuarios pueden interactuar con la implementación completa. Si desea asegurarse de que la extensión es la última para ejecutar, use la propiedad **dependsOn** en la plantilla.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información acerca de acciones de auditoría, consulte [con el Administrador de recursos de operaciones de auditoría](resource-group-audit.md).
- Para obtener información acerca de las acciones para determinar los errores durante la implementación, vea [operaciones de implementación de vista](resource-manager-troubleshoot-deployments-portal.md).
