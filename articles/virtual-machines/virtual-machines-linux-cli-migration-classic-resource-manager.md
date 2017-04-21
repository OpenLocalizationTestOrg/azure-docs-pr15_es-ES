<properties
    pageTitle="Migrar los recursos de IaaS de clásico para el Administrador de recursos de Azure mediante CLI de Azure | Microsoft Azure"
    description="En este artículo se recorre la migración compatibles con la plataforma de recursos de clásico para el Administrador de recursos de Azure usando CLI de Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="cynthn"/>

# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Migrar los recursos de IaaS de clásico para el Administrador de recursos de Azure mediante CLI de Azure

Estos pasos le muestran cómo utilizar los comandos de Azure de línea de comandos interfaz migrar infraestructura como un recursos de servicio (IaaS) del modelo de implementación clásico para el modelo de implementación de administrador de recursos de Azure. El artículo requiere [CLI de Azure](../xplat-cli-install.md).

>[AZURE.NOTE] Todas las operaciones que se describen aquí son idempotentes. Si tiene un problema distinto de una característica no admitida o un error de configuración, le recomendamos que vuelva a intentar preparar, anular o confirmar la operación. La plataforma se, a continuación, vuelva a intentarlo.

## <a name="step-1-prepare-for-migration"></a>Paso 1: Preparar para la migración

Estas son algunas prácticas recomendadas que se recomienda evaluar los recursos IaaS migrar desde clásico para el Administrador de recursos:

- Lea la [lista de características o configuraciones no compatibles](virtual-machines-windows-migration-classic-resource-manager.md). Si tiene máquinas virtuales que usan configuraciones no admitidas o características, le recomendamos que espere a que la compatibilidad de características y configuración a anunciará. Como alternativa, puede quitar dicha característica o mover fuera de que la configuración para permitir migración que mejor se adapte a sus necesidades.
-   Si automatizada de secuencias de comandos que implementación la infraestructura y las aplicaciones de hoy, intente crear un programa de instalación de prueba similar mediante esas secuencias de comandos para la migración. Como alternativa, puede configurar los entornos de ejemplo mediante el portal de Azure.

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Paso 2: Configurar la suscripción y registrar el proveedor

Escenarios de migración, debe configurar el entorno para ambos clásico y el Administrador de recursos. [Instalar CLI de Azure](../xplat-cli-install.md) y [Seleccione la suscripción](../xplat-cli-connect.md).

Inicie sesión en su cuenta.
    
    azure login

Seleccione la suscripción Azure mediante el comando siguiente.

    azure account set "<azure-subscription-name>"

>[AZURE.NOTE] Registro es un paso de tiempo, pero debe hacerse una vez antes de intentar la migración. Sin registrar verá el siguiente mensaje de error 

>   *BadRequest: Suscripción no está registrada para la migración.* 

Registrar con el proveedor de recursos de migración, con el siguiente comando. Tenga en cuenta que en algunos casos, este comando agota el tiempo. Sin embargo, se realizará el registro.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Espere cinco minutos para el registro Finalizar. Puede comprobar el estado de la aprobación mediante el comando siguiente. Asegúrese de que es RegistrationState `Registered` antes de continuar.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Ahora cambie CLI a la `asm` modo.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Paso 3: Asegúrese de que tiene suficiente núcleos de máquina Virtual de administrador de recursos de Azure en la región de Azure de su implementación actual o VNET

Para este paso deberá cambiar a `arm` modo. Haga esto con el siguiente comando.

```
azure config mode arm
```

Puede usar el siguiente comando CLI para comprobar la cantidad de núcleos que tiene en el Administrador de recursos de Azure. Para obtener más información acerca de las cuotas de núcleo, vea [los límites y el Administrador de recursos de Azure](../articles/azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Cuando haya terminado de comprobar este paso, puede cambiar a `asm` modo.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Paso 4: Opción 1 - migrar máquinas virtuales de un servicio de nube 

Obtener la lista de servicios de nube mediante el comando siguiente y, a continuación, seleccione el servicio de nube que desea migrar. Tenga en cuenta que si las máquinas virtuales en el servicio de nube que se encuentran en una red virtual o si tienen funciones web o trabajo, obtendrá un mensaje de error.

    azure service list

Ejecute el comando siguiente para obtener el nombre de la implementación del servicio de nube desde la salida detallada. En la mayoría de los casos, el nombre de la implementación es el mismo que el nombre de servicio de nube.

    azure service show <serviceName> -vv

Preparar las máquinas virtuales en el servicio de nube para la migración. Tiene dos opciones para elegir.

Si desea migrar las máquinas virtuales a una red virtual creado plataforma, use el siguiente comando.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Si desea migrar a una red virtual existente en el modelo de implementación de administrador de recursos, use el siguiente comando.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> subnetName <vnetName>

Después de la operación de preparación es correcta, puede consultar la salida detallada para obtener el estado de la migración de las máquinas virtuales y asegurarse de que están en la `Prepared` estado.

    azure vm show <vmName> -vv

Compruebe la configuración de los recursos preparados mediante CLI o el portal de Azure. Si no está listo para la migración y desea volver al estado anterior, utilice el siguiente comando.

    azure service deployment abort-migration <serviceName> <deploymentName>

Si la configuración preparados es satisfactorio, puede desplazarse hacia delante y confirmar los recursos con el siguiente comando.

    azure service deployment commit-migration <serviceName> <deploymentName>


    
## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Paso 4: Opción 2: migrar máquinas virtuales de una red virtual

Elija la red virtual que desea migrar. Tenga en cuenta que si la red virtual contiene funciones web o trabajador o máquinas virtuales con configuraciones no compatibles, obtendrá un mensaje de error de validación.

Obtener todas las redes virtuales en la suscripción mediante el siguiente comando.

    azure network vnet list
    
El resultado tendrá un aspecto parecido al siguiente:

![Captura de pantalla de la línea de comandos con el nombre de red virtual completo resaltado.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

En el ejemplo anterior, la **virtualNetworkName** es el nombre completo **"Grupo classicubuntu16 classicubuntu16"**.

Preparar la red virtual de su elección para la migración mediante el comando siguiente.

    azure network vnet prepare-migration <virtualNetworkName>

Compruebe la configuración de los equipos virtuales preparados mediante CLI o el portal de Azure. Si no está listo para la migración y desea volver al estado anterior, utilice el siguiente comando.

    azure network vnet abort-migration <virtualNetworkName>

Si la configuración preparados es satisfactorio, puede desplazarse hacia delante y confirmar los recursos con el siguiente comando.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Paso 5: Migrar una cuenta de almacenamiento

Cuando haya terminado de migrar los equipos virtuales, se recomienda migrar la cuenta de almacenamiento.

Preparar la cuenta de almacenamiento para la migración mediante el comando siguiente

    azure storage account prepare-migration <storageAccountName>

Compruebe la configuración de la cuenta de almacenamiento preparado mediante CLI o el portal de Azure. Si no está listo para la migración y desea volver al estado anterior, utilice el siguiente comando.

    azure storage account abort-migration <storageAccountName>

Si la configuración preparados es satisfactorio, puede desplazarse hacia delante y confirmar los recursos con el siguiente comando.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Pasos siguientes

- [Migración de plataforma compatible de recursos de IaaS de clásico para el Administrador de recursos](virtual-machines-windows-migration-classic-resource-manager.md)
- [Análisis técnico detallado de migración compatibles con la plataforma de clásico para el Administrador de recursos](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
