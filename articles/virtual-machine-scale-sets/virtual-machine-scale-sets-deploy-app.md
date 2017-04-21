<properties
    pageTitle="Implementar una aplicación en conjuntos de escala de máquina Virtual | Microsoft Azure"
    description="Implementar una aplicación en conjuntos de escala de máquina Virtual"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="guybo"/>

# <a name="deploy-an-app-on-virtual-machine-scale-sets"></a>Implementar una aplicación en conjuntos de escala de máquina Virtual

Normalmente se implementa una aplicación que se ejecuta en un conjunto de máquina virtual de escala de tres maneras:

- Instalar software nuevo en una imagen de la plataforma en tiempo de implementación. Una imagen de la plataforma en este contexto es una imagen de sistema operativo de Azure Marketplace, como Ubuntu 16.04, Windows Server 2012 R2, etcetera.

Puede instalar software nuevo en una imagen de la plataforma con una [Extensión de máquina virtual](../virtual-machines/virtual-machines-windows-extensions-features.md). Una extensión VM es software que se ejecuta cuando se implementa una máquina virtual. Puede ejecutar cualquier código que le guste en tiempo de implementación con una extensión de scripts personalizados. [Aquí](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) se muestra un ejemplo de la plantilla de administrador de recursos de Azure con dos extensiones VM: un extensión de Script personalizado de Linux para instalar Apache y PHP y una extensión de diagnóstico para emitir datos de rendimiento de Azure Autoescala.

Una ventaja de este enfoque es que tiene un nivel de separación entre el código de la aplicación y el sistema operativo y puede mantener su aplicación por separado. Por supuesto que significa que hay también es partes más móviles y tiempo de implementación de VM podrían ser más larga si hay mucho para que descargar y configurar la secuencia de comandos.

**Si pasa información confidencial en el comando de extensión de Script personalizado (por ejemplo, una contraseña), asegúrese de especificar el `commandToExecute` en la `protectedSettings` atributo de la extensión de Script personalizado en lugar de la `settings` atributo.**

- Crear una imagen de máquina virtual personalizada que incluya el sistema operativo y la aplicación en un disco duro virtual único. A continuación el conjunto de escala consta de un conjunto de máquinas virtuales de copiar de una imagen creada por el usuario, solo tiene que mantener. Este enfoque no requiere ninguna configuración adicional en tiempo de implementación de máquina virtual. Sin embargo, en la `2016-03-30` versión de conjuntos de escala de VM (y versiones anteriores), los discos de sistema operativo para las máquinas virtuales en el conjunto de escala están limitados a una cuenta de almacenamiento única. Por lo tanto, puede tener un máximo de 40 máquinas virtuales de un conjunto de escala, en lugar de la máquina virtual de 100 por escala establecer límite con imágenes de la plataforma. Para obtener más detalles, vea [Introducción al diseño de escala establecer](./virtual-machine-scale-sets-design-overview.md) .

- Implementar una plataforma o una imagen personalizada que básicamente es un host de contenedor e instale la aplicación como uno o varios de los contenedores que administre con una herramienta de administración de orchestrator o configuración. Lo mejor de este enfoque es que se extrae a la infraestructura de nube desde el nivel de aplicación y puede mantener por separado.

## <a name="what-happens-when-a-vm-scale-set-scales-out"></a>¿Qué ocurre cuando un conjunto de escala de VM escala?

Cuando se agrega uno o más máquinas virtuales a una escala de establecer aumentando la capacidad: si manualmente o a través de Autoescala: la aplicación se instala automáticamente. Para ejemplo si establece la escala tiene extensiones definidas, en una nueva máquina virtual ejecute cada vez que se crea. Si el conjunto de escala se basa en una imagen personalizada, cualquier nueva máquina virtual es una copia de la imagen personalizada de origen. Si establece la escala máquinas virtuales son hosts de contenedor, a continuación, es posible que tenga el código de inicio para cargar los contenedores en una extensión de Script personalizado o una extensión puede instalar a un agente que se registra con un orchestrator clúster (como servicio de contenedor de Azure).

## <a name="how-do-you-manage-application-updates-in-vm-scale-sets"></a>¿Cómo se administran las actualizaciones de la aplicación en conjuntos de escala de VM?

Actualizaciones de la aplicación en conjuntos de escala de VM, tres enfoques principales siga uno de los tres métodos de implementación aplicación anterior:

* Actualizar con extensiones de máquina virtual. Vuelve a cualquier extensión VM definida por un conjunto de escala de VM se ejecuta cada vez que se ha implementado una nueva máquina virtual, una máquina virtual existente o se actualiza una extensión de máquina virtual. Si necesita actualizar la aplicación, directamente la actualización de una aplicación a través de extensiones es viable el método: simplemente que actualizar la definición de extensión. Una forma sencilla de hacerlo es cambiando el fileUris para que apunten al nuevo software.

* El enfoque inmutable imagen personalizada. Cuando platos preparados la aplicación (o componentes de la aplicación) en una imagen de máquina virtual puede centrarse en la creación de una canalización confiable para automatizar la creación, prueba e implementación de las imágenes. Puede diseñar la arquitectura para facilitar el intercambio rápido de un conjunto de escala preconfigurada en producción. Un buen ejemplo de este enfoque es el [controlador de Azure Spinnaker funciona](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/).

Compresor y Terraform también son compatibles con el Administrador de recursos de Azure, por lo que también puede definir las imágenes "como código" y crearlos en Azure, use el disco duro virtual en el conjunto de escala. Sin embargo, si lo hace así que sería problemático para las imágenes de Marketplace, donde las secuencias de comandos de extensiones personalizada se vuelven más importantes ya que no manipular directamente los bits de Marketplace.

* Actualizar contenedores. Resumen la administración de ciclo de vida de aplicación a un nivel por encima de la infraestructura de nube, por ejemplo por aplicaciones encapsuladas y componentes de la aplicación en contenedores y administrar estos contenedores mediante orchestrators contenedor y administradores de la configuración como Chef/posición libre.

La escala de establece máquinas virtuales, a continuación, se convierten en un sustrato estable para los contenedores y solo se establece ocasional seguridad y actualizaciones relacionadas con el sistema operativo. Como se mencionó, el servicio de contenedor de Azure es un buen ejemplo de este enfoque y la creación de un servicio a su alrededor.

## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>¿Cómo dar una actualización de OS a través de dominios de actualización?

Suponga que desea actualizar la imagen de sistema operativo y mantener la ejecución VM escala configurada. Una forma de hacerlo es actualizar la máquina virtual de las imágenes de una VM a la vez. Puede hacerlo con PowerShell o CLI de Azure. Hay comandos independientes para actualizar el modelo de conjunto de escala de VM (¿cómo se define su configuración) y para enviar llamadas "actualización manual" en máquinas virtuales individuales.

[Aquí](https://github.com/gbowerman/vmsstools) se muestra un ejemplo de script de Python que automatizan el proceso de actualización de un dominio de una actualización de VM escala establecer a la vez. (Advertencia: es más de una prueba de concepto que una solución de producción lista consolidada, es recomendable agregar algunos etcetera de comprobación de errores.).
