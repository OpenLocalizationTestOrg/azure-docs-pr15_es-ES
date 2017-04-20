
<properties
    pageTitle="Almacenamiento de Azure coherente: diferencias y consideraciones | Microsoft Azure"
    description="Comprender las diferencias entre el almacenamiento de Azure y otras consideraciones de implementación de almacenamiento Azure coherentes."
    services="azure-stack"
    documentationCenter=""
    authors="MChadalapaka"
    manager="siroy"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="mchad"/>

# <a name="azure-consistent-storage-differences-and-considerations"></a>Almacenamiento de Azure coherente: diferencias y consideraciones

Almacenamiento de Azure coherente es el conjunto de servicios de nube de almacenamiento en la pila de Microsoft Azure. Almacenamiento de Azure coherente proporciona blobs, tabla, cola y funcionalidad de administración de cuenta con una semántica coherente de Azure. En este artículo se resume las diferencias de almacenamiento de Azure coherente conocidas con el almacenamiento de Azure. También resume otras consideraciones a tener en cuenta cuando se implementa la versión preview esté disponible públicamente actualmente de la pila de Microsoft Azure.

<span id="Concepts" class="anchor"><span id="_Toc386544169" class="anchor"><span id="_Toc389466742" class="anchor"><span id="_Ref428966996" class="anchor"><span id="_Toc433223853" class="anchor"></span></span></span></span></span>
## <a name="known-differences"></a>Diferencias conocidas

Esta versión de vista previa técnica de almacenamiento de Azure coherentes no tiene paridad de características de 100% para las versiones de API que son compatibles con el almacenamiento de Azure. Conocidos diferencias de características se incluyen las siguientes:

-   Ciertos tipos de cuenta de almacenamiento no están disponibles, por ejemplo, estándar\_estándar y RAGRS\_GRS.

-   Premium\_pueden aprovisionar cuentas de almacenamiento LRS. Sin embargo, actualmente existen garantías ni límites de rendimiento.

-   Funcionalidad de Azure archivos aún no está disponible.

-   Obtener página rangos API no admite la recuperación de páginas diferentes instantáneas de blobs de página.

-   Obtener página rangos API devuelve las páginas que tengan 4 KB de detalle.

-   Claves de partición y fila en la implementación de tabla de almacenamiento Azure coherentes son cada limitado a 400 caracteres (es decir, 800 bytes).

-   Nombres de BLOB en la implementación del servicio de blobs de Windows Azure coherente almacenamiento están limitados a 880 caracteres (es decir, 1760 bytes).

-   La implementación de almacenamiento de Azure coherentes de datos de uso de almacenamiento de inquilinos informes proporciona medidores de uso de almacenamiento que son idénticos a los de Azure, con la misma semántica y unidades. Sin embargo, actualmente, el indicador de uso de las transacciones de almacenamiento no incluye las transacciones de IaaS y el indicador de uso de transferencia de datos no distingue el uso de ancho de banda por el tráfico de red interna o externa a un área de la pila de Azure.

-   Existen ciertas diferencias en el ámbito de la funcionalidad de capacidad de almacenamiento. Por ejemplo, no puede cambiar el tipo de cuenta o tiene dominios personalizados. Además, se ofrece solo coherencia de nivel de la API de prima\_LRS tipo de cuenta de almacenamiento.

## <a name="deployment-considerations"></a>Consideraciones de implementación

-   **Sólo prueba.** Implemente almacenamiento Azure coherente en entornos de producción que usan la versión actual de Microsoft Azure pila Technical Preview. Esta versión está pensada solo con fines de evaluación en un entorno de prueba.

-   **Rendimiento**. La versión de vista previa técnica de Microsoft Azure pila de almacenamiento de Azure coherentes no está totalmente optimizado de rendimiento.

-   **Escalabilidad.** La versión de vista previa técnica de Microsoft Azure pila de almacenamiento de Azure coherentes no está optimizada para escalabilidad.

## <a name="version-support-considerations"></a>Consideraciones de soporte de versión

Las versiones siguientes son compatibles con esta versión preliminar de almacenamiento Azure coherente:

> Biblioteca de cliente de almacenamiento de Azure: [Microsoft Azure almacenamiento 6.x .NET SDK](http://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)
>
> Servicios de datos de Azure almacenamiento: [versión de la API de REST de 2015-04-05](https://msdn.microsoft.com/library/azure/mt705637.aspx)
>
> Servicios de administración de almacenamiento de Azure: [vista previa de 2015-05-01](https://msdn.microsoft.com/library/azure/mt163683.aspx)
> [2015-06-15](https://msdn.microsoft.com/library/azure/mt163683.aspx)
## <a name="next-steps"></a>Pasos siguientes

-   [Introducción al almacenamiento de Azure coherente](azure-stack-storage-overview.md)
