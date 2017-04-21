<properties
    pageTitle="Infraestructura de las directrices de nomenclatura | Microsoft Azure"
    description="Obtenga información sobre las instrucciones de diseño e implementación de clave de nomenclatura en servicios de infraestructura de Azure."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="infrastructure-naming-guidelines"></a>Instrucciones de nomenclatura de infraestructura

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

En este artículo se centra en comprender cómo enfocar convenciones de nomenclatura para todos los recursos de Azure distintos crear un conjunto lógico y fácil de identificar recursos en su entorno.

## <a name="implementation-guidelines-for-naming-conventions"></a>Directrices de implementación para convenciones de nomenclatura

Decisiones:

- ¿Cuáles son las convenciones de nomenclatura para los recursos de Azure?

Tareas:

- Definir la pondrá usar a través de los recursos para mantener la coherencia.
- Definir los nombres de cuenta de almacenamiento dado el requisito de que sean únicos.
- La convención de nomenclatura para utilizar y distribuir a todas las partes interesadas garantizar la coherencia en implementaciones de documentos.

## <a name="naming-conventions"></a>Convenciones de nomenclatura

Antes de crear nada en Azure que debería tener una buena convención de nomenclatura en su lugar. Una convención de nomenclatura garantiza que todos los recursos tengan un nombre predecible, lo que ayuda a reduce la carga administrativa asociada con la administración de los recursos.

Puede seguir un conjunto concreto de convenciones de nomenclatura definido para toda la organización o para una cuenta o suscripción de Azure específica. Aunque es fácil para personas dentro de las organizaciones establecer reglas implícitas cuando se trabaja con recursos Azure, debe poder ajustar para equipos que trabajan juntas en Azure.

Acordar un conjunto de convenciones de nomenclatura adelantado. Existen algunas consideraciones sobre convenciones de nomenclatura que cortar en conjuntos de reglas.

## <a name="affixes"></a>Pondrá

Mientras ve para definir una convención de nomenclatura, una decisión es si la conviene poner en:

- El principio del nombre (prefijo)
- Al final del nombre (sufijo)

Por ejemplo, hay dos nombres posibles para un grupo de recursos con el `rg` colocar:

- C-Web App (prefijo)
- Aplicación Web-c (sufijo)

Pondrá hacer referencia a distintos aspectos que describen los recursos determinados. La siguiente tabla muestran algunos ejemplos de uso.

| Aspecto                               | Ejemplos                                                               | Notas                                                                                                      |
|:-------------------------------------|:-----------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------|
| Entorno                          | producto de desarrollo, stg,                                                         | Dependiendo de la finalidad y el nombre de cada entorno.                                                     |
| Ubicación                             | Usw (US occidental), use (Estados Unidos oriental 2)                                         | Según la región del centro de datos o la región de la organización.                               |
| Componente de Azure, servicio o producto | C para el grupo de recursos, VNet de red virtual                        | Dependiendo del producto que proporcionan los recursos de soporte técnico.                                          |
| Función                                 | DB, la aplicación, la web                                                           | Dependiendo de la función de la máquina virtual.                                                              |
| Instancia                             | 01, 02, 03, etcetera.                                                       | Para los recursos que tengan más de una instancia. Por ejemplo, servidores web en un servicio de nube con equilibrio de carga. |


Al establecer las convenciones de nomenclatura, asegúrese de que claramente indiquen qué pondrá para cada tipo de recurso y en qué posición (sufijo de vs prefijo).

## <a name="dates"></a>Fechas

A menudo es importante determinar la fecha de creación del nombre de un recurso. Se recomienda el formato de fecha AAAAMMDD. Este formato garantiza que no solo es completo se registra la fecha, pero también que dos recursos cuyos nombres difieren únicamente en la fecha se ordenan alfabéticamente y cronológico.

## <a name="naming-resources"></a>Nombres recursos

Definir cada tipo de recurso de la convención de nomenclatura, que debería tener reglas que definen cómo asignar nombres a cada recurso que se ha creado. Estas reglas deben aplicarse a todos los tipos de recursos, por ejemplo:

- Suscripciones
- Cuentas
- Cuentas de almacenamiento
- Redes virtuales
- Subredes
- Conjuntos de disponibilidad
- Grupos de recursos
- Máquinas virtuales de Windows
- Extremos
- Grupos de seguridad de red
- Roles

Para asegurarse de que el nombre proporciona suficiente información para determinar qué recurso que se hace referencia, debe usar nombres descriptivos.

## <a name="computer-names"></a>Nombres de equipo

Cuando se crea una máquina virtual (VM), Azure requiere un nombre de la máquina virtual de hasta 64 caracteres que se usa para el nombre del recurso. Azure usa el mismo nombre para el sistema operativo instalado en la máquina virtual. Sin embargo, estos nombres no siempre sea la misma.

Si se crea una máquina virtual desde un archivo de imagen .vhd que ya contiene un sistema operativo, el nombre de VM en Azure puede diferir de nombre del equipo de sistema operativo de la máquina virtual. Esta situación puede agregar un nivel de dificultad a la administración de VM, que, por tanto, no se recomienda. Asignar el recurso de máquina virtual de Azure en el mismo nombre que el nombre del equipo que asigne al sistema operativo de la máquina.

Se recomienda que el nombre de la máquina virtual de Azure es el mismo que el nombre del equipo de sistema operativo subyacente.

## <a name="storage-account-names"></a>Nombres de cuenta de almacenamiento

Cuentas de almacenamiento tienen las reglas especiales de sus nombres. Solo puede usar letras minúsculas y números. Para obtener más información, vea [crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account) . Además, el nombre de la cuenta de almacenamiento, con Core.Windows.NET., debe ser un nombre DNS globalmente válido y único. Por ejemplo, si la cuenta de almacenamiento se llama mystorageaccount, los siguientes nombres DNS resultantes deben ser únicos:

- mystorageaccount.BLOB.Core.Windows.NET
- mystorageaccount.Table.Core.Windows.NET
- mystorageaccount.Queue.Core.Windows.NET


## <a name="next-steps"></a>Pasos siguientes
[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 