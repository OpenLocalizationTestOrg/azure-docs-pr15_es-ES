<properties
    pageTitle="Diseño de escala de la máquina Virtual establece para escala | Microsoft Azure"
    description="Obtenga más información sobre cómo diseñar los conjuntos de escala de máquina Virtual de escala"
    keywords="conjuntos de máquina virtual Linux, escala de máquina virtual" 
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="gatneil"/>

# <a name="designing-vm-scale-sets-for-scale"></a>Diseño de escala VM conjuntos de escala

Este tema describe las consideraciones de diseño para los conjuntos de escala de máquina Virtual. Para obtener información acerca de los conjuntos de escala de máquina Virtual que son, consulte [Información general de conjuntos de escala de máquina Virtual](virtual-machine-scale-sets-overview.md).


## <a name="storage"></a>Almacenamiento de información

Un conjunto de escala usa cuentas de almacenamiento para almacenar los discos de sistema operativo de la VM en el conjunto. Se recomienda una relación de 20 máquinas virtuales por cuenta de almacenamiento o menos. También se recomienda que se propagan a través del alfabeto los caracteres iniciales de los nombres de cuenta de almacenamiento. Hacerlo le ayuda a distribuir la carga entre distintos sistemas internos. Por ejemplo, en la plantilla siguiente, usamos la función de administrador de recursos plantilla uniqueString para generar hash prefijo que llevan a los nombres de cuenta de almacenamiento: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).


## <a name="overprovisioning"></a>Para otros fines

A partir de la versión de la API de "2016-03-30", conjuntos de escala de VM predeterminado máquinas virtuales de "para otros"(fines". Con para otros fines activado, la escala de establece realmente gira el más máquinas virtuales de frecuentes para y luego elimina las VM adicionales que girado la última. Para otros fines mejora tasas de éxito de aprovisionamiento. No se cargarán para estas máquinas virtuales adicionales y no se contabilizan hacia los límites de cuota.

Aunque para otros fines mejorar aprovisionamiento tasas de éxito, puede provocar un comportamiento confuso para una aplicación que no está diseñado para administrar máquinas virtuales desaparezca sin previo aviso. Para activar para otros fines desactivar, asegúrese de que tiene la cadena siguiente en la plantilla: "overprovision": "false". Encontrará más detalles en la [documentación de la API de REST de Configurar escala de máquina virtual](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Si desactiva para otros fines, puede obtener ausente con una relación mayor de máquinas virtuales por cuenta de almacenamiento, pero no lo recomendamos ir por encima de 40.


## <a name="limits"></a>Límites
Un conjunto de escala integrado en una imagen personalizada (una creada por el usuario) debe crear todos los VHD de disco de sistema operativo dentro de una cuenta de almacenamiento. Como resultado, el máximo número de máquinas virtuales de un conjunto de escala integrado en una imagen personalizada recomendado es 20. Si desactiva para otros fines, puede ir hasta 40.

Un conjunto de escala integrado en una imagen de la plataforma actualmente está limitado a 100 VM (se recomienda 5 cuentas de almacenamiento para esta escala).

Para obtener más de máquinas virtuales de permitir que estos límites, necesita implementar varios conjuntos de escala, como se muestra en [esta plantilla](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).