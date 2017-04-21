<properties
   pageTitle="Se recomienda convenciones de nomenclatura para los recursos de Azure | Orientación | Microsoft Azure"
   description="Se recomienda convenciones de nomenclatura para los recursos de Azure. Cómo asignar un nombre a máquinas virtuales, cuentas de almacenamiento, redes, redes virtuales, subredes y otras entidades de Azure"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/27/2016"
   ms.author="christb"/>
   
# <a name="recommended-naming-conventions-for-azure-resources"></a>Convenciones de nomenclatura recomendadas para recursos de Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

La elección de un nombre para los recursos en Microsoft Azure es importante porque:

- Es difícil cambiar un nombre más adelante.
- Nombres deben cumplir los requisitos de su tipo de recurso específico.

Convenciones de nomenclatura poner los recursos sea más fácil encontrarlas. También pueden indicar el rol de un recurso en una solución.

En este artículo es un resumen de las reglas de nomenclatura y restricciones para recursos de Azure y un conjunto de línea base de recomendaciones para convenciones de nomenclatura.  Puede usar estas recomendaciones como punto de partida para sus propio convenciones específicas para sus necesidades.  

La clave para el éxito con convenciones de nomenclatura es establecer y seguir ellos a través de las organizaciones y aplicaciones. 

## <a name="naming-subscriptions"></a>Suscripciones de nomenclatura

Al asignar nombres a Azure suscripciones, nombres detallados Asegúrese de comprender el contexto y la finalidad de cada suscripción borrar.  Cuando se trabaja en un entorno con muchas de las suscripciones, siguiendo una convención de nomenclatura compartida puede mejorar claridad.

Un modelo recomendado para suscripciones de nomenclatura es:

`<Company> <Department (optional)> <Product Line (optional)> <Environment>`

- Compañía normalmente sería el mismo para cada suscripción. Sin embargo, es podrán que algunas compañías tenga compañías secundario dentro de la estructura organizativa. Estas empresas pueden administrarse por un grupo central de TI. En estos casos, podría ser diferenciados haciendo que el nombre de compañía (*Contoso*) y el nombre de la compañía secundarios (*North Wind*).

- Departamento es un nombre de la organización donde funciona un grupo de personas. Este elemento del espacio de nombres como opcional.

- Línea de producto es un nombre específico de un producto o la función que se realiza desde dentro del departamento.
Esto es opcional, en general, para aplicaciones y servicios interna. Sin embargo, se recomienda para servicios de público que requieren separación fácil y la identificación (por ejemplo, separación clara de los registros de facturación).

- Entorno es el nombre que describa el ciclo de vida de implementación de las aplicaciones o servicios, como desarrollo, control de calidad u ord.

| Compañía | Departamento | Línea de producto o servicio | Entorno | Nombre completo  |
----------| ---------- | ----------------------- | ----------- | ---------- |
| Contoso | SocialGaming | AwesomeService | Producción | Contoso SocialGaming AwesomeService producción |
| Contoso | SocialGaming | AwesomeService | Desarrollo | Desarrollo de Contoso SocialGaming AwesomeService |
| Contoso | SE | InternalApps | Producción | Contoso TI InternalApps producción |
| Contoso | SE | InternalApps | Desarrollo | Contoso TI InternalApps desarrollo |

<!-- TODO; include more information about organizing subscriptions for application deployment, pods, etc. -->

## <a name="use-affixes-to-avoid-ambiguity"></a>Usar pondrá para evitar la ambigüedad

Al asignar recursos en Azure, se recomienda utilizar comunes prefijos o sufijos para identificar el tipo y el contexto del recurso.  Durante toda la información sobre el tipo de metadatos, contexto, está disponible mediante programación, aplicar pondrá comunes simplifica la identificación visual.  Cuando incorpora colocará la convención de nomenclatura, es importante especificar claramente si la conviene poner está al principio del nombre (prefijo) o al final (sufijo).  

Por ejemplo, hay dos nombres posibles para un servicio de hospedaje un motor de cálculo:

- SvcCalculationEngine (prefijo)
- CalculationEngineSvc (sufijo)

Pondrá hacer referencia a distintos aspectos que describen los recursos determinados. La siguiente tabla muestran algunos ejemplos de uso.

| Aspecto | Ejemplo | Notas |
| ------ | ------- | ----- |
| Entorno | desarrollo de producción, q & a | Identifica el entorno para el recurso |
| Ubicación | UW (nos oeste), ue (nos Oriente) | Identifica el área en que se implementa el recurso |
| Instancia | 01, 02 | Para los recursos que tienen más de una instancia con nombre (servidores web, etcetera). |
| Producto o servicio | servicio | Identifica el producto, aplicación o servicio que admita el recurso |
| Función | SQL, web, mensajería | Identifica la función de los recursos asociados |

Al desarrollar una convención de nomenclatura específica para su empresa o proyectos, es importante que elija un conjunto común de pondrá y su posición (prefijo o sufijo).

## <a name="naming-rules-and-restrictions"></a>Convenciones de nomenclatura y restricciones

Cada tipo de recurso o servicio de Azure aplica un conjunto de restricciones de nomenclatura ámbito; cualquier convención de nomenclatura o trama debe cumplir las normas de nomenclatura necesarias y el ámbito.  Por ejemplo, mientras que el nombre de una máquina virtual se asigna a un nombre DNS (y, por tanto, debe ser único en todos de Azure), el nombre de un VNET ámbito es el grupo de recursos que se creó en.

En general, evitar que los caracteres especiales (`-` o `_`) como el primer o último carácter en cualquier nombre. Estos caracteres hará que la mayoría de las reglas de validación a un error.

| Categoría | Entidad o servicio | Ámbito | Longitud | Mayúsculas y minúsculas | Caracteres válidos | Modelo sugerido | Ejemplo |
| ------------- | ----------------- | ----- | ------ | ------ | ---------------- | ----------------- | ------- |
| Grupo de recursos | Grupo de recursos | Global | 1-64 | Mayúsculas y minúsculas | Alfanumérico, subrayado y guión | `<service short name>-<environment>-rg` | `profx-prod-rg` |
| Grupo de recursos | Conjunto de disponibilidad | Grupo de recursos | 1-80 | Mayúsculas y minúsculas | Alfanumérico, subrayado y guión | `<service-short-name>-<context>-as` | `profx-sql-as` |
| General | Etiqueta | Entidad asociada | 512 (nombre), 256 (valor) | Mayúsculas y minúsculas | Alfanumérico | `"key" : "value"` | `"department" : "Central IT"` |
| Calcular | Máquina virtual | Grupo de recursos | 1-15 | Mayúsculas y minúsculas | Alfanumérico, subrayado y guión | `<name>-<role>-<instance>` | `profx-sql-001` |
| Almacenamiento de información | Nombre de la cuenta de almacenamiento (datos) | Global | 3-24 | Minúsculas | Alfanumérico | `<service short name><type><number>` | `profxdata001` |
| Almacenamiento de información | Nombre de la cuenta de almacenamiento (discos) | Global | 3-24 | Minúsculas | Alfanumérico | `<vm name without dashes>st<number>` | `profxsql001st0` |
| Almacenamiento de información | Nombre del contenedor | Cuenta de almacenamiento | 3-63 |   Minúsculas | Alfanumérico y guión | `<context>` | `logs` |
| Almacenamiento de información | Nombre de BLOB | Contenedor | 1-1024 | Mayúsculas y minúsculas | Cualquier carácter de dirección URL | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Almacenamiento de información | Nombre de la cola | Cuenta de almacenamiento | 3-63 | Minúsculas | Alfanumérico y guión | `<service short name>-<context>-<num>` | `awesomeservice-messages-001` |
| Almacenamiento de información | Nombre de tabla | Cuenta de almacenamiento | 3-63 |Mayúsculas y minúsculas | Alfanumérico | `<service short name>-<context>` | `awesomeservice-logs` |
| Almacenamiento de información | Nombre de archivo | Cuenta de almacenamiento | 3-63 | Minúsculas | Alfanumérico | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Redes | Red virtual (VNet) | Grupo de recursos | 2-64 | Entre mayúsculas y minúsculas | Alfanumérico, guión, subrayado y período | `<service short name>-[section]-vnet` | `profx-vnet` |
| Redes | Subred | VNet primario | 2-80 | Entre mayúsculas y minúsculas | Alfanumérico, subrayado, guión y período | `<role>-subnet` | `gateway-subnet` |
| Redes | Interfaz de red | Grupo de recursos | 1-80 | Entre mayúsculas y minúsculas | Alfanumérico, guión, subrayado y período | `<vmname>-<num>nic` | `profx-sql1-1nic` |
| Redes | Grupo de seguridad de red | Grupo de recursos | 1-80 | Entre mayúsculas y minúsculas | Alfanumérico, guión, subrayado y período | `<service short name>-<context>-nsg` | `profx-app-nsg` |
| Redes | Regla de grupo de seguridad de red | Grupo de recursos | 1-80 | Entre mayúsculas y minúsculas | Alfanumérico, guión, subrayado y período | `<descriptive context>` | `sql-allow` |
| Redes | Dirección IP pública | Grupo de recursos | 1-80 | Entre mayúsculas y minúsculas | Alfanumérico, guión, subrayado y período | `<vm or service name>-pip` | `profx-sql1-pip` |
| Redes | Equilibrador de carga | Grupo de recursos | 1-80 | Entre mayúsculas y minúsculas | Alfanumérico, guión, subrayado y período | `<service or role>-lb` | `profx-lb` |
| Redes | Cargar la configuración de reglas equilibrada | Equilibrador de carga | 1-80 | Entre mayúsculas y minúsculas | Alfanumérico, guión, subrayado y período | `descriptive context` | `http` |

<!-- TODO fill in the rest of these resources
| Networking | Azure Application Gateway | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | `<service or role>-aag` | `profx-aag`
| Networking | Azure Application Gateway Connection | Azure Application Gateway | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | `` | TODO
| Networking | Traffic Manager Profile | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | TODO | TODO
-->

## <a name="organizing-resources-with-tags"></a>Organizar los recursos con etiquetas

El Administrador de recursos de Azure admite entidades etiquetas con las cadenas de texto arbitrario para identificar el contexto y simplificar la automatización.  Por ejemplo, la etiqueta `"sqlVersion: "sql2014ee"` identifique máquinas virtuales en una implementación que se ejecuta SQL Server 2014 Enterprise Edition para ejecutar una secuencia de comandos automatizada en ellos.  Las etiquetas deben usarse para aumentar y mejorar el contexto a lo largo de las convenciones de nomenclatura elegido.

> [AZURE.TIP]Una de las otra ventajas de etiquetas es que etiquetas abarcan grupos de recursos, lo que le permite vincular y relacionar entidades a través de implementaciones diferentes.

Cada grupo de recursos o puede tener un máximo de **15** etiquetas. El nombre de etiqueta se limita a 512 caracteres y el valor de la etiqueta está limitado a 256 caracteres.

Para obtener más información sobre el etiquetado de recursos, consulte [mediante etiquetas para organizar los recursos de Azure](../resource-group-using-tags.md).

Algunos de los casos de uso de etiquetas temáticas comunes son:

- **Facturación**; Agrupación de recursos y asociarlos con la facturación o cargo nuevamente códigos.
- **Identificación de contexto de servicio**; Identificar los grupos de recursos a través de grupos de recursos para operaciones comunes agrupación
- **Control de acceso y contexto de seguridad**; Identificación de funciones administrativas en función de la cartera, sistema, servicio, aplicación, instancia, etcetera.

> [AZURE.TIP]Etiquetar temprano - etiqueta con frecuencia.  Mejor tener una línea de base combinación en lugar de etiquetado y ajustar a través del tiempo, en lugar de tener que plantear después del hecho.  

Un ejemplo de algunos métodos comunes de etiquetas temáticas:

| Nombre de etiqueta | Clave | Ejemplo | Comentario |
| -------- | --- | ------- | ------- |
| Factura / interno ID de cargo al usuario | Facturación  | `IT-Chargeback-1234` | Un código de facturación o interno i/OS |
| Operador o directamente responsable individuales (DRI) | managedBy | `joe@contoso.com`  | Dirección de correo electrónico o alias |
| Nombre del proyecto | nombre del proyecto | `myproject`  | Nombre de la línea de producto o de proyecto |
| Versión del proyecto | versión del proyecto | `3.4`  | Versión de la línea de producto o de proyecto |
| Entorno | entorno | `<Production, Staging, QA >` | Identificador ambiental | 
| Nivel | nivel | `Front End, Back End, Data` | Identificación de nivel o función y contexto |
| Perfil de datos | dataProfile | `Public, Confidential, Restricted, Internal` | Confidencialidad de los datos almacenados en el recurso |
 
## <a name="tips-and-tricks"></a>Sugerencias y trucos

Algunos tipos de recursos pueden requerir precauciones adicionales sobre nomenclatura y convenciones.

### <a name="virtual-machines"></a>Máquinas virtuales de Windows

Especialmente en topologías más grandes, máquinas virtuales de nomenclatura con cuidado simplifica la identificación de la función y el propósito de cada equipo y a continuación, activar secuencias de comandos más predecible.

> [AZURE.WARNING]Cada máquina virtual en Azure tiene un nombre de recurso de Azure y un nombre de host de sistema operativo.  
> Administrar las máquinas virtuales puede ser difícil si el nombre del recurso y el nombre de host son diferentes y se debe evitar.
> Por ejemplo, si se crea una máquina virtual desde un .vhd que ya contiene un sistema operativo configurado con un nombre de host.

- [Convenciones de nomenclatura para máquinas virtuales de Windows Server](https://support.microsoft.com/en-us/kb/188997)

<!-- TODO - recommendations on naming VMs. -->

### <a name="storage-accounts-and-storage-entities"></a>Cuentas de almacenamiento y entidades de almacenamiento

Hay dos casos de uso principal para las cuentas de almacenamiento - una copia de discos para máquinas virtuales y almacenar los datos en BLOB, colas y tablas.  Cuentas de almacenamiento utilizadas para discos de VM deben seguir la convención de nomenclatura de asociarlos con el nombre de la máquina virtual primario (y con la necesidad potencial de varias cuentas de almacenamiento de gama alta SKU VM, también puede aplicar un sufijo de número).

> [AZURE.TIP]Cuentas de almacenamiento, ya sea para datos o discos: deben seguir una convención de nomenclatura que permite varias cuentas de almacenamiento aprovechar (es decir, siempre con un sufijo numérico).

Es posible configurar un nombre de dominio personalizado para obtener acceso a datos blob en su cuenta de almacenamiento de Azure.
El extremo predeterminado para el servicio de Blob es `https://mystorage.blob.core.windows.net`.

Pero si asignar un dominio personalizado (por ejemplo, www.contoso.com) al extremo blob para su cuenta de almacenamiento, también puede acceder blob datos en su cuenta de almacenamiento mediante el uso de ese dominio. Por ejemplo, con un nombre de dominio personalizado, `http://mystorage.blob.core.windows.net/mycontainer/myblob` podría tener acceso como `http://www.contoso.com/mycontainer/myblob`.

Para obtener más información acerca de cómo configurar esta característica, consulte [configurar un nombre de dominio personalizado para el extremo de almacenamiento de blobs de Windows](../storage/storage-custom-domain-name.md).

Para obtener más información acerca de los nombres de tablas, contenedores y BLOB:

- [Los nombres y hacer referencia a contenedores, BLOB y metadatos](https://msdn.microsoft.com/library/dd135715.aspx)
- [Colas de nomenclatura y metadatos](https://msdn.microsoft.com/library/dd179349.aspx)
- [Nombres de tablas](https://msdn.microsoft.com/library/azure/dd179338.aspx)

Un nombre de blobs puede contener cualquier combinación de caracteres, pero deben ser de escape correctamente caracteres reservados de dirección URL. Evite los nombres de blobs terminan en punto (.), una barra diagonal (/), o una secuencia o una combinación de ambos. Por convención, la barra diagonal es el separador de directorio **virtual** . No utilice una barra diagonal inversa (\) en un nombre de blobs de Windows. El cliente de API pueden habilitarlo, pero no hash correctamente y las firmas no coincidirán.

No es posible modificar el nombre de una cuenta de almacenamiento o contenedor después de que se ha creado.
Si desea utilizar un nombre nuevo, debe eliminarlo y crear uno nuevo.

> [AZURE.TIP] Se recomienda establecer una convención de nomenclatura para todas las cuentas de almacenamiento y tipos antes de embarcarse en el desarrollo de un nuevo servicio o la aplicación.

## <a name="example---deploying-an-n-tier-service"></a>Ejemplo: implementar un servicio de capas

En este ejemplo, se define una configuración de servicio de capas, que consta de servidores front-end IIS (hospedados en máquinas virtuales de Windows Server), con SQL Server (alojado en dos máquinas virtuales de servidor de Windows), un clúster de ElasticSearch (hospedado en máquinas virtuales de Linux 6) y las cuentas de almacenamiento asociado, redes virtuales, recursos del grupo y equilibrador de carga.

Comenzaremos definiendo las convenciones contextuales para esta aplicación:

| Entidad | Convención | Descripción  |
| ------ | ---------- | ------------ |  
| Nombre de servicio | `profx` | El nombre de la aplicación o servicio que está implementando corto |
| Entorno | `prod` | Este es para la implementación de producción (en contraposición a QA, prueba, etcetera.) |

De esa instantánea, a continuación, podemos asignar a las convenciones para cada uno de los tipos de recursos:

| Tipo de recurso | Base de la convención | Ejemplo |
| ------------- | --------------- | ------- |
| Suscripción | `<Company> <Department (optional)> <Product Line (optional)> <Environment>` | `Contoso IT InternalApps Profx Production` |
| Grupo de recursos | `servicename-rg` | `profx-rg` |
| Red virtual | `servicename-vnet` | `profx-vnet` |
| Subred | `role-subnet` | `sql-vnet` |
| Equilibrador de carga | `servicename-lb` | `profx-lb` |
| Máquina virtual | `servicename-role[number]` | `profx-sql0` |
| Cuenta de almacenamiento | `<vmnamenodashes>st<num>` | `profxsql0st0` |

Tal como se muestra en el siguiente diagrama:

![diagrama de la topología de aplicación] (media/guidance-naming-conventions/guidance-naming-convention-example.png "Topología de aplicación de ejemplo")

## <a name="sample---azure-cli-script-for-deploying-the-sample-above"></a>Ejemplo - script de Azure CLI para implementar el ejemplo anterior

```bash
#!/bin/sh

#####################################################################
# Sample script using the Azure CLI to build out an application 
# demonstrating naming conventions.  
#
# Note; this script is not intended for production deployment, as it does 
# not create availability sets, configure network security rules, etc.
#####################################################################

# Set up variables to build out the naming conventions for deploying
# the cluster  
LOCATION=eastus2
APP_NAME=profx
ENVIRONMENT=prod
USERNAME=testuser
PASSWORD="testpass"

# Set up the tags to associate with items in the application
TAG_BILLTO="InternalApp-ProFX-12345"
TAGS="billTo=${TAG_BILLTO}"

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
SUBSCRIPTION=3e9c25fc-55b3-4837-9bba-02b6eb204331

# Set up the names of things using recommended conventions
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
VNET_NAME="${APP_NAME}-vnet"

# Set up the postfix variables attached to most CLI commands
POSTFIX="--resource-group ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}"

##########################################################################################
# Set up the VM conventions for Linux and Windows images

# For Windows, get the list of URN's via 
# azure vm image list ${LOCATION} MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

# For Linux, get the list or URN's via 
# azure vm image list ${LOCATION} canonical ubuntuserver
LINUX_BASE_IMAGE=canonical:ubuntuserver:16.04.0-DAILY-LTS:16.04.201602130

#########################################################################################
## Define functions 
create_vm ()
{
    vm_name=$1
    vnet_name=$2
    subnet_name=$3
    os_type=$4
    vhd_path=$5
    vm_size=$6
    diagnostics_storage=$7

    # Create the network interface card for this VM
    azure network nic create --name "${vm_name}-0nic" --subnet-name ${subnet_name} --subnet-vnet-name ${vnet_name} \
        --tags="${TAGS}" ${POSTFIX}

    # Create the storage account for this vm's disks (premium locally redundant storage -> PLRS)
    # Note the ${var//-/} syntax to remove dashes from the vm name
    storage_account_name=${vm_name//-/}st01
    azure storage account create --type=PLRS --tags "${TAGS}" ${POSTFIX} "${storage_account_name}"

    # Map the name of the diagnostics storage account to a blob URI for boot diagnostics
    # This is (currently) required when deploying with a named premium storage account 
    diag_blob="https://${diagnostics_storage}.blob.core.windows.net/"

    # Create the VM
    azure vm create --name ${vm_name} --nic-name "${vm_name}-0nic" --os-type ${os_type} \
        --image-urn ${vhd_path} --vm-size ${vm_size} --vnet-name ${vnet_name} --vnet-subnet-name ${subnet_name} \
        --storage-account-name "${storage_account_name}" --storage-account-container-name vhds --os-disk-vhd "${vm_name}-osdisk.vhd" \
        --admin-username "${USERNAME}" --admin-password "${PASSWORD}" \
        --boot-diagnostics-storage-uri "${diag_blob}" \
        --tags="${TAGS}" ${POSTFIX} 
}

###################################################################################################
# Create resources

# Step 1 - create the enclosing resource group
azure group create --name "${RESOURCE_GROUP}" --location "${LOCATION}" --tags "${TAGS}" --subscription "${SUBSCRIPTION}"

# Step 2 - create the network security groups

# Step 3 - create the networks (VNet and subnets)
azure network vnet create --name "${VNET_NAME}" --address-prefixes="10.0.0.0/8" --tags "${TAGS}" ${POSTFIX}
# TODO - does subnet support tagging?
azure network vnet subnet create --name gateway-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.1.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-master-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.2.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-data-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.3.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name sql-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.4.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}

# Step 4 - define the load balancer and network security rules
azure network lb create --name "${APP_NAME}-lb" ${POSTFIX}
# In a production deployment script, we'd create load balancer rules and 
# network security groups here

# Step 5 - create a diagnostics storage account
diagnostics_storage_account=${APP_NAME//-/}diag
azure storage account create --type=LRS --tags "${TAGS}" ${POSTFIX} "${diagnostics_storage_account}"

# Step 6.1 - Create the gateway VMs
for i in `seq 1 4`;
do
    create_vm "${APP_NAME}-gw${i}" "${APP_NAME}-vnet" "gateway-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}" 
done    

# Step 6.2 - Create the ElasticSearch master and data VMs
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-master${i}" "${APP_NAME}-vnet" "es-master-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-data${i}" "${APP_NAME}-vnet" "es-data-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done

# Step 6.3 - Create the SQL VMs
create_vm "${APP_NAME}-sql0" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
create_vm "${APP_NAME}-sql1" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
```
