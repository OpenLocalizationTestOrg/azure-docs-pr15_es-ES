
<properties
    pageTitle="Administrar recursos con CLI Azure | Microsoft Azure"
    description="Usar la interfaz de línea de comandos de Azure (CLI) para administrar grupos y recursos de Azure"
    editor=""
    manager="timlt"
    documentationCenter=""
    authors="dlepow"
    services="azure-resource-manager"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="danlep"/>

# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Utilizar la CLI de Azure para administrar Azure y grupos de recursos


> [AZURE.SELECTOR]
- [Portal](azure-portal/resource-group-portal.md) 
- [CLI de Azure](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [API DE REST](resource-manager-rest-api.md)


La interfaz de línea de comandos de Azure (Azure CLI) es una de las diversas herramientas que puede usar para implementar y administrar recursos con el Administrador de recursos. Este artículo presenta formas comunes para administrar Azure y grupos de recursos mediante la CLI de Azure en modo de administrador de recursos. Para obtener información sobre el uso de la CLI para implementar recursos, vea [implementar recursos a las plantillas de administrador de recursos y CLI de Azure](resource-group-template-deploy-cli.md). Para obtener información acerca de los recursos de Azure y Administrador de recursos, visite la [Información general del Administrador de recursos de Azure](azure-resource-manager/resource-group-overview.md).

>[AZURE.NOTE] Para administrar recursos Azure con CLI Azure, debe [instalar la CLI de Azure](xplat-cli-install.md)y a continuación, [inicie sesión en Azure](xplat-cli-connect.md) utilizando la `azure login` comando. Asegúrese de que la CLI está en modo de administrador de recursos (ejecutar `azure config mode arm`). Si lo ha hecho, está listo.



## <a name="get-resource-groups-and-resources"></a>Obtener y grupos de recursos

### <a name="resource-groups"></a>Grupos de recursos

Para obtener una lista de todos los grupos de recursos de la suscripción y las ubicaciones, ejecutar este comando.

    azure group list
    

### <a name="resources"></a>Recursos
 Para obtener una lista de todos los recursos en un grupo, por ejemplo, una con el nombre *testRG*, utilice el siguiente comando.

    azure resource list testRG

Para ver un recurso individual dentro del grupo, como, por ejemplo, una máquina virtual había denominada *MyUbuntuVM*, use un comando similar al siguiente.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"
    
Observe el parámetro **Microsoft.Compute/virtualMachines** . Este parámetro indica el tipo de recurso que solicita información.
    
>[AZURE.NOTE]Al usar los comandos de **azure recursos** que no sea el comando de la **lista** , debe especificar la versión de la API del recurso con el parámetro **-o** . Si no está seguro sobre la versión de la API, consulte el archivo de plantilla y busque el campo apiVersion para el recurso. Para obtener más información acerca de las versiones de la API en el Administrador de recursos, consulte [proveedores de administrador de recursos, regiones, versiones API y los esquemas](resource-manager-supported-services.md).

Al ver detalles de un recurso, a menudo es muy útil para usar el `--json` parámetro. Este parámetro hace que el resultado más legible porque algunos valores son estructuras anidadas o colecciones. En el ejemplo siguiente se muestra cómo devolver los resultados del comando **Mostrar** como un documento JSON.

    azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json

>[AZURE.NOTE] Si lo desea, guardar los datos JSON al archivo mediante el &gt; carácter para dirigir la salida a un archivo. Por ejemplo:
>
> `azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

### <a name="tags"></a>Etiquetas

[AZURE.INCLUDE [resource-manager-tag-resources-cli](../includes/resource-manager-tag-resources-cli.md)]

## <a name="manage-resources"></a>Administrar recursos


Para agregar un recurso como una cuenta de almacenamiento a un grupo de recursos, ejecutar un comando similar al:

    azure resource create testRG MyStorageAccount "Microsoft.Storage/storageAccounts" "westus" -o "2015-06-15" -p "{\"accountType\": \"Standard_LRS\"}"
    
Además de especificar la versión de la API del recurso con el parámetro **-o** , use el parámetro **-p** para pasar de una cadena con formato JSON con las propiedades necesarias o adicionales.
    
    
Para eliminar un recurso existente como un recurso de máquina virtual, use un comando similar al siguiente.

    azure resource delete testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

Para mover los recursos existentes a otro grupo de recursos o suscripción, utilice el comando **mover recursos azure** . En el ejemplo siguiente se muestra cómo mover una caché Redis a un nuevo grupo de recursos. En el parámetro **-i** , proporcione que una lista de valores separados por comas de los identificadores de recursos está mover.


    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"

## <a name="control-access-to-resources"></a>Controlar el acceso a los recursos

Puede utilizar la CLI Azure para crear y administrar las directivas para controlar el acceso a los recursos de Azure. Para obtener información acerca de definiciones de directivas y asignar directivas a los recursos, vea [Usar directiva para administrar recursos y controlar el acceso](resource-manager-policy.md).

Por ejemplo, definir la directiva siguiente para denegar todas las solicitudes donde ubicación no es occidental de Estados Unidos o Norte Central de EE y guardarlo en el policy.json de archivo de definición de directiva:

    {
    "if" : {
        "not" : {
        "field" : "location",
        "in" : ["westus" ,  "northcentralus"]
        }
    },
    "then" : {
        "effect" : "deny"
    }
    }

A continuación, ejecute el comando **Crear definición de la directiva** :

    azure policy definition create MyPolicy -p c:\temp\policy.json
    
Este comando muestra resultado similar al siguiente.

    + Creación de definición de directiva MiDirectiva datos: directiva: MiDirectiva datos: PolicyDefinitionId: /subscriptions/###-###-###-###-###/providers/Microsoft.Authorization/policyDefinitions/MyPolicy

    datos: PolicyType: datos personalizados: DisplayName: sin definir datos: Descripción: sin definir datos: PolicyRule: campo = ubicación, en = [westus northcentralus], efecto = denegar

 Para asignar una directiva en el ámbito que desee, use la **PolicyDefinitionId** obtenidos mediante el comando anterior. En el ejemplo siguiente, este ámbito es la suscripción, pero puede definir el ámbito para los recursos individuales o grupos de recursos:

    asignación de directiva Azure crear MyPolicyAssignment -p /subscriptions/###-###-###-###-###/providers/Microsoft.Authorization/policyDefinitions/MyPolicy -s /subscriptions/###-###-###-###-### /

Puede obtener, cambiar o quitar definiciones de directiva mediante los comandos **Mostrar de la definición de la directiva**, **definición de directiva**y **eliminar la definición de la directiva** .

Asimismo, puede obtener, cambiar o quitar las asignaciones de directivas, use los comandos **Mostrar de la asignación de directiva**, **asignación de directiva**y **eliminar la asignación de la directiva** .


## <a name="export-a-resource-group-as-a-template"></a>Exportar un grupo de recursos como una plantilla

Para un grupo de recursos existente, puede ver la plantilla de administrador de recursos del grupo de recursos. Exportar la plantilla ofrece dos ventajas:

1. Puede automatizar fácilmente las futuras implementaciones de la solución porque toda la infraestructura está definida en la plantilla.

2. Puede familiarizarse con la sintaxis de la plantilla consultando el JSON que representa la solución.

Con la CLI de Azure, puede exportar una plantilla que representa el estado actual de su grupo de recursos, o descargar la plantilla que se usó para una implementación concreta.

* **Exportar la plantilla para un grupo de recursos** - esto es útil cuando realizado cambios en un grupo de recursos y necesita recuperar la representación JSON de su estado actual. Sin embargo, la plantilla generada contiene solo un número mínimo de parámetros y no variables. La mayoría de los valores de la plantilla son modificables. Antes de implementar la plantilla generada, puede convertir varios valores en parámetros para que pueda personalizar la implementación para distintos entornos.

    Para exportar la plantilla para un grupo de recursos en un directorio local, ejecute el `azure group export` comando tal como se muestra en el ejemplo siguiente. (Sustituya un directorio local adecuado para su entorno de sistema operativo).

        azure group export testRG ~/azure/templates/

* **Descargar la plantilla para una implementación concreta** , esto es útil cuando necesite ver la plantilla real que se usó para implementar recursos. La plantilla incluye todos los parámetros y variables definidas para la implementación original. Sin embargo, si alguien de su organización realizado cambios en el grupo de recursos fuera de la definición de la plantilla, esta plantilla no representa el estado actual del grupo de recursos.

    Para descargar la plantilla utilizada para una implementación concreta en un directorio local, ejecute el `azure group deployment template download` comando. Por ejemplo:

        azure group deployment template download TestRG testRGDeploy ~/azure/templates/downloads/
 
>[AZURE.NOTE] Exportación de la plantilla está en vista previa y no todos los tipos de recursos actualmente admiten la exportación de una plantilla. Al intentar exportar una plantilla, es posible que vea un error que indica que no se exportan algunos recursos. Si es necesario, definir manualmente estos recursos en la plantilla después de descargarlo.



## <a name="next-steps"></a>Pasos siguientes

* Para obtener detalles de las operaciones de implementación y solucionar problemas de implementación con la CLI de Azure, vea [operaciones de implementación de vista con CLI de Azure](resource-manager-troubleshoot-deployments-cli.md).
* Si desea utilizar la CLI para configurar una aplicación o la secuencia de comandos para tener acceso a los recursos, vea [Usar Azure CLI para crear un servicio principal para tener acceso a los recursos](resource-group-authenticate-service-principal-cli.md).


