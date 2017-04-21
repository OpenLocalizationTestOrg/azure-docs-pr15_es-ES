<properties
   pageTitle="Implementar un clúster de servicio del contenedor de Azure | Microsoft Azure"
   description="Implementar un clúster de servicio del contenedor de Azure con el portal de Azure, Azure CLI o PowerShell."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contenedores, Micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>

# <a name="deploy-an-azure-container-service-cluster"></a>Implementar un clúster de servicio del contenedor de Azure

Servicio de contenedor Azure proporciona implementación rápida del contenedor de código abierto popular soluciones de clústeres y orquestación. Mediante el servicio de contenedor de Azure, puede implementar DC/OS y Docker enjambre clústeres con las plantillas de administrador de recursos de Azure o el portal de Azure. Implementar estos clústeres mediante conjuntos de escala de máquina Virtual de Azure y los clústeres aprovechar las ventajas de ofertas de redes y el almacenamiento de Azure. Para obtener acceso al servicio de contenedor de Azure, necesita una suscripción de Azure. Si no tiene una, a continuación, puede registrarse para una [prueba gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

Este documento le guiará a través de la implementación de un clúster de servicio del contenedor de Azure con el [portal de Azure](#creating-a-service-using-the-azure-portal), la [interfaz de línea de comandos de Azure (CLI)](#creating-a-service-using-the-azure-cli)y el [módulo de PowerShell de Azure](#creating-a-service-using-powershell).  

## <a name="create-a-service-by-using-the-azure-portal"></a>Crear un servicio a través del portal de Azure

Inicie sesión en el portal de Azure, seleccione **nuevo**y busque Azure Marketplace **Servicio del contenedor de Azure**.

![Crear implementación 1](media/acs-portal1.png)  <br />

Seleccione el **Servicio de contenedor de Azure**y haga clic en **crear**.

![Crear implementación 2](media/acs-portal2.png)  <br />

Escriba la información siguiente:

- **Nombre de usuario**: este es el nombre de usuario que se usará para una cuenta en cada uno de los equipos virtuales y conjuntos de escala de la máquina virtual en el clúster de servicio del contenedor de Azure.
- **Suscripción**: seleccione una suscripción de Azure.
- **Grupo de recursos**: seleccione un grupo de recursos existente o cree uno nuevo.
- **Ubicación**: seleccione una región Azure para la implementación de servicio del contenedor de Azure.
- **Clave pública de SSH**: agregar la clave pública que se usará para la autenticación contra máquinas virtuales de servicio del contenedor de Azure. Es muy importante que esta clave no contiene saltos de línea y, a continuación, que incluye el prefijo 'ssh-rsa' y la 'username@domain' sufijo. Debe ser algo parecido al siguiente: **ssh rsa AAAAB3Nz... <> …... UcyupgH azureuser@linuxvm **. Para obtener instrucciones sobre la creación de claves de Shell seguro (SSH), consulte los artículos de [Linux]( https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-linux/) y [Windows]( https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-windows/) .

Cuando esté listo para continuar, haga clic en **Aceptar** .

![Crear implementación 3](media/acs-portal3.png)  <br />

Seleccione un tipo de orquestación. Las opciones son:

- **DC/OS**: implementa un clúster DC/OS.
- **Enjambre**: implementa un clúster Docker enjambre.

Cuando esté listo para continuar, haga clic en **Aceptar** .

![Crear implementación 4](media/acs-portal4.png)  <br />

Escriba la información siguiente:

- **Contar patrón**: el número de páginas maestras en el clúster.
- **Recuento de agente**: para Docker enjambre, será el número inicial de agentes en el conjunto de escala de agente. Para DC/OS, será el número inicial de agentes de un conjunto de escala privado. Además, se crea un conjunto de escala público, que contiene un número predeterminado de agentes. El número de agentes de este conjunto de escala público se determina cuántos maestros se han creado en el clúster: un agente público para un patrón y dos agentes públicos para tres o cinco patrones.
- **Tamaño de la máquina virtual de agente**: el tamaño de las máquinas virtuales de agente.
- **Prefijo DNS**: un nombre único de llamadas internacionales que se utilizará como prefijo partes principales de los nombres de dominio completo para el servicio.

Cuando esté listo para continuar, haga clic en **Aceptar** .

![Crear implementación 5](media/acs-portal5.png)  <br />

Una vez finalizada la validación de servicio, haga clic en **Aceptar** .

![Crear implementación 6](media/acs-portal6.png)  <br />

Haga clic en **crear** para iniciar el proceso de implementación.

![Crear implementación 7](media/acs-portal7.png)  <br />

Si ha decidido anclar la implementación al portal de Azure, puede ver el estado de implementación.

![Crear implementación 8](media/acs-portal8.png)  <br />

Una vez finalizada la implementación, el clúster de servicio del contenedor de Azure está listo para su uso.

## <a name="create-a-service-by-using-the-azure-cli"></a>Crear un servicio mediante la CLI de Azure

Para crear una instancia de servicio del contenedor de Azure mediante el uso de la línea de comandos, necesita una suscripción de Azure. Si no tiene una, a continuación, puede registrarse para una [prueba gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). También debe tener [instalado](../xplat-cli-install.md) y [configurado](../xplat-cli-connect.md) CLI Azure.

Para implementar un clúster de Docker enjambre o DC/OS, seleccione una de las siguientes plantillas de GitHub. Tenga en cuenta que ambas plantillas son iguales, con la excepción de la selección de orchestrator predeterminada.

* [Plantilla de DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
* [Plantilla de conjunto](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

A continuación, asegúrese de que la CLI Azure se ha conectado a una suscripción de Azure. Puede hacerlo mediante el siguiente comando:

```bash
azure account show
```
Si no se devuelve una cuenta de Azure, use el comando siguiente para iniciar sesión CLI en Azure.

```bash
azure login -u user@domain.com
```

A continuación, configure las herramientas de Azure CLI para usar el Administrador de recursos de Azure.

```bash
azure config mode arm
```

Crear un grupo de recursos de Azure y clúster de servicio de contenedor con el comando siguiente, donde:

- **RESOURCE_GROUP** es el nombre del grupo de recursos que desea usar para este servicio.
- **Ubicación** es la región de Azure donde se creará el grupo de recursos y de implementación de servicio del contenedor de Azure.
- **TEMPLATE_URI** es la ubicación del archivo de implementación. Tenga en cuenta que debe ser el archivo original, no un puntero a la UI GitHub. Para buscar esta dirección URL, seleccione el archivo de azuredeploy.json en GitHub y haga clic en el botón de **materia** .

> [AZURE.NOTE] Cuando ejecuta este comando, la consola le pedirá los valores de parámetro de implementación.

```bash
azure group create -n RESOURCE_GROUP DEPLOYMENT_NAME -l LOCATION --template-uri TEMPLATE_URI
```

### <a name="provide-template-parameters"></a>Proporcionar parámetros de plantilla

Esta versión del comando requiere definir parámetros de forma interactiva. Si desea proporcionar parámetros, como una cadena con formato JSON, puede hacerlo mediante la `-p` cambiar. Por ejemplo:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
```

Como alternativa, puede proporcionar un archivo con formato JSON parámetros mediante la `-e` cambiar:

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
```

Para ver un archivo de parámetros de ejemplo denominado `azuredeploy.parameters.json`, busque con las plantillas de servicio del contenedor de Azure en GitHub.

## <a name="create-a-service-by-using-powershell"></a>Crear un servicio con PowerShell

También puede implementar un clúster de servicio del contenedor de Azure con PowerShell. Este documento se basa en la versión 1.0 de [módulo de PowerShell de Azure](https://azure.microsoft.com/blog/azps-1-0/).

Para implementar un clúster de Docker enjambre o DC/OS, seleccione una de las siguientes plantillas. Tenga en cuenta que ambas plantillas son iguales, con la excepción de la selección de orchestrator predeterminada.

* [Plantilla de DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
* [Plantilla de conjunto](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Antes de crear un clúster de su suscripción de Azure, compruebe que se ha firmado la sesión de PowerShell a Azure. Puede hacerlo con la `Get-AzureRMSubscription` comando:

```powershell
Get-AzureRmSubscription
```

Si necesita iniciar sesión en Azure, use la `Login-AzureRMAccount` comando:

```powershell
Login-AzureRmAccount
```

Si está implementando a un nuevo grupo de recursos, primero debe crear el grupo de recursos. Para crear un nuevo grupo de recursos, utilice el `New-AzureRmResourceGroup` comando y especifique una región nombre y el destino del grupo de recursos:

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Después de crear un grupo de recursos, puede crear el clúster con el siguiente comando. Se especificará el URI de la plantilla que desee para la `-TemplateUri` parámetro. Cuando ejecuta este comando, PowerShell le pedirá los valores de parámetro de implementación.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
```

### <a name="provide-template-parameters"></a>Proporcionar parámetros de plantilla

Si ya está familiarizado con PowerShell, sabrá que puede recorrer los parámetros disponibles para un cmdlet, escriba un signo menos (-) y, a continuación, presionar la tecla TAB. Esta misma funcionalidad también funciona con los parámetros que se definen en la plantilla. Tan pronto como escriba el nombre de plantilla, el cmdlet recupera la plantilla, analiza los parámetros y los parámetros de la plantilla agrega al comando dinámicamente. Esto facilita mucho especificar los valores de parámetro de plantilla. Y si olvida un valor de parámetro necesario, PowerShell solicita el valor.

A continuación, encontrará el comando completo con parámetros incluidos. Puede proporcionar sus propios valores de los nombres de los recursos.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ya tiene un clúster de funcionamiento, vea estos documentos para la conexión y detalles de administración:

- [Conectarse a un clúster de servicio del contenedor de Azure](container-service-connect.md)
- [Trabajar con el servicio de contenedor Azure y DC/OS](container-service-mesos-marathon-rest.md)
- [Trabajar con el servicio de contenedor Azure y conjunto de Docker](container-service-docker-swarm.md)
