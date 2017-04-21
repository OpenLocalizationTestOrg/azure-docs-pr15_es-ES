<properties
    pageTitle="Mover datos a y desde el almacenamiento de Azure | Microsoft Azure"
    description="Este artículo proporciona una descripción general de los diferentes métodos para mover datos hacia y desde el almacenamiento de Azure."
    services="storage"
    documentationCenter=""
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="micurd"/>

# <a name="moving-data-to-and-from-azure-storage"></a>Mover datos a y desde el almacenamiento de Azure

Si desea mover los datos locales con Azure almacenamiento (o viceversa), hay varias maneras para realizar esta acción. El método que mejor le convenga dependerá de su situación. En este artículo le proporcionará una introducción rápida de escenarios y ofertas apropiadas para cada uno.

## <a name="building-applications"></a>Creación de aplicaciones

Si desea crear una aplicación, desarrollo de la API de REST o uno de nuestras muchas bibliotecas de cliente es una excelente forma de mover los datos hacia y desde el almacenamiento de Azure.

Almacenamiento de Azure proporciona bibliotecas de cliente enriquecido para .NET, iOS, Java, Android, plataforma de Windows Universal (UWP), Xamarin, C++, Node.JS, PHP, Ruby y Python. Las bibliotecas de cliente ofrecen capacidades avanzadas, como la lógica de reintento, registro y cargas paralelas. También puede desarrollar directamente con la API de REST, puede llamar a cualquier idioma que realiza las solicitudes de HTTP/HTTPS.

Vea [Empezar a trabajar con el almacenamiento de blobs de Windows Azure](storage-dotnet-how-to-use-blobs.md) para más información.

Además, también le ofrecemos la [Biblioteca de movimiento de datos de almacenamiento de Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) que es una biblioteca diseñada para alto rendimiento copiar datos de Azure. Consulte nuestra [documentación](https://github.com/Azure/azure-storage-net-data-movement) de la biblioteca de movimiento de datos para obtener más información. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Rápidamente la visualización o interacción con los datos

Si desea una forma sencilla de ver los datos de almacenamiento de Azure y también la capacidad para cargar y descargar los datos, considere la posibilidad de usar el Explorador de almacenamiento de Azure.

Consulte nuestra lista de [Exploradores de almacenamiento de Azure](storage-explorers.md) para más información.

## <a name="system-administration"></a>Administración del sistema

Si necesita o más cómodo con una herramienta de línea de comandos (por ejemplo, los administradores del sistema), estas son algunas opciones para tener en cuenta:

### <a name="azcopy"></a>AzCopy

AzCopy es una herramienta de línea de comandos de Windows diseñada para la copia de alto rendimiento de datos hacia y desde el almacenamiento de Azure. También puede copiar datos dentro de una cuenta de almacenamiento, o entre cuentas de almacenamiento diferente.

Para obtener más información, consulte [transferir datos con la herramienta de línea de comandos de AzCopy](storage-use-azcopy.md) .

### <a name="azure-powershell"></a>Azure PowerShell

PowerShell Azure es un módulo que proporciona los cmdlets de administración de servicios en Azure. Es un lenguaje de secuencias de comandos diseñadas especialmente para la administración del sistema y shell de línea de comandos basado en tareas.

Consulte [Usar PowerShell de Azure con el almacenamiento de Azure](storage-powershell-guide-full.md) para obtener más información.

### <a name="azure-cli"></a>CLI de Azure

Azure CLI proporciona un conjunto de código abierto, entre plataformas comandos para trabajar con los servicios de Azure. CLI Azure está disponible en Windows, OSX y Linux.

Vea [usar CLI Azure con el almacenamiento de Azure](storage-azure-cli.md) para obtener más información.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Mover grandes cantidades de datos con una red lenta

Uno de los desafíos más grandes asociados a mover grandes cantidades de datos es el tiempo de transferencia. Si desea obtener datos de Azure almacenamiento sin preocuparse por los costos de redes o escribir el código, importar o exportar de Azure es una solución adecuada.

Consulte [Importar o exportar de Azure](storage-import-export-service.md) para más información.

## <a name="backing-up-your-data"></a>Copia de seguridad de los datos

Si simplemente necesita hacer copia de seguridad de los datos y el almacenamiento de Azure, copia de seguridad de Azure es la mejor opción. Esta es una solución eficaz para realizar copias de seguridad de los datos locales y máquinas virtuales de Azure.

Consulte [Copia de seguridad de Azure](../backup/backup-introduction-to-azure-backup.md) para más información.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Obtener acceso a los datos locales y de la nube

Si necesita una solución para tener acceso a los datos locales y de la nube, debe considerar usar la solución de almacenamiento de nube de Azure híbrido, StorSimple. Esta solución consta de un dispositivo StorSimple físico que inteligente con frecuencia almacena datos utilizados SSDs, utiliza ocasionalmente datos en el disco duro e inactivo o copia de seguridad o archivar datos en el almacenamiento de Azure.

Consulte [StorSimple](../storsimple/storsimple-overview.md) para obtener más información.

## <a name="recovering-your-data"></a>Recuperar los datos

Cuando tenga las cargas de trabajo local y aplicaciones, tendrá una solución que permite a su empresa siga funcionando en caso de desastre. Recuperación de sitio de Azure controladores replicación, migración tras error y recuperación de máquinas virtuales y servidores físicos. Los datos duplicados se almacenan en el almacenamiento de Azure, lo que permite eliminar la necesidad de un centro de datos en el sitio secundario.

Consulte [Recuperación de sitio de Azure](../site-recovery/site-recovery-overview.md) para más información.
