<properties
    pageTitle="Cifrado de servicio de almacenamiento de Azure para datos almacenados | Microsoft Azure"
    description="Usar la característica de cifrado de servicio de almacenamiento de Azure para cifrar el almacenamiento de blobs de Windows Azure en el lado de servicio para almacenar los datos y descifrar al recuperar los datos."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="robinsh"/>

# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Cifrado de servicio de almacenamiento de Azure para datos almacenados

Cifrado de servicio de almacenamiento de Azure (SSE) para datos almacenados le ayuda a proteger y proteger sus datos para cumplir con la seguridad de la organización y los compromisos de cumplimiento. Con esta característica, el almacenamiento de Azure automáticamente cifra los datos antes de guardar al almacenamiento y descifra antes de recuperación. El cifrado, descifrado y administración de claves son totalmente transparentes a los usuarios.

Las siguientes secciones proporcionan instrucciones detalladas sobre cómo usar las características de cifrado de servicio de almacenamiento, así como los escenarios compatibles y usuario experiencias.

## <a name="overview"></a>Información general

Almacenamiento de Azure proporciona un conjunto completo de características de seguridad que juntas permiten a los desarrolladores generar aplicaciones seguras. Pueden proteger los datos al transmitirlos entre una aplicación y Azure mediante [El cifrado de cliente](storage-client-side-encryption.md), HTTPs o SMB 3.0. Cifrado de servicio de almacenamiento proporciona cifrado al resto, control de cifrado, descifrado y administración de claves de manera completamente transparente. Todos los datos se cifra con 256 [cifrado AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), uno de lo más potente cifrado de bloques disponible.

SSE funciona mediante el cifrado de los datos cuando se escribe en el almacenamiento de Azure y puede utilizarse para bloquear blobs, blobs de página y anexar BLOB. Funciona para lo siguiente:

-   Cuentas de almacenamiento de propósito general y cuentas de almacenamiento de blobs
-   Almacenamiento de estándar y Premium 
-   Redundancia de todos los niveles (LRS, ZRS, GRS, GRS RA)
-   Cuentas de Azure almacenamiento de administrador de recursos (pero no clásico) 
-   Todas las regiones

Para habilitar o deshabilitar el cifrado de servicio de almacenamiento para una cuenta de almacenamiento, inicie sesión en el [portal de Azure](https://azure.portal.com) y seleccione una cuenta de almacenamiento. En el módulo de configuración, busque la sección servicio de blobs tal como se muestra en esta captura de pantalla y haga clic en cifrado.

![Opción de cifrado que muestra portal de captura de pantalla](./media/storage-service-encryption/image1.png)

Después de hacer clic en la configuración de cifrado, puede habilitar o deshabilitar el cifrado de servicio de almacenamiento.

![Propiedades de cifrado que muestra portal de captura de pantalla](./media/storage-service-encryption/image2.png)

##<a name="encryption-scenarios"></a>Escenarios de cifrado

Cifrado de servicio de almacenamiento de información pueden habilitarse en un nivel de la cuenta de almacenamiento. Admite los siguientes escenarios de cliente:

-   Cifrado de blobs de bloque, anexar BLOB y blobs de página.

-   Cifrado de archivado VHD y plantillas de poner en Azure locales.

-   Cifrado de discos de sistema operativo y los datos subyacentes para máquinas virtuales de IaaS creados con el VHD.

SSE tiene las limitaciones siguientes:

-   No se admite el cifrado de cuentas de almacenamiento clásico.

-   No se admite el cifrado de almacenamiento clásica cuentas migradas a cuentas de almacenamiento de administrador de recursos.

-   Los datos existentes - SSE sólo cifran datos recién creado cuando se habilita el cifrado. Si, por ejemplo crear una nueva cuenta de almacenamiento de administrador de recursos pero no activar el cifrado y, a continuación, cargar BLOB o archivados VHD a esa cuenta de almacenamiento y, a continuación, activar SSE, esos BLOB no se cifran a menos que se escribirse o se copian.

-   El soporte de catálogo de soluciones - Activar cifrado de máquinas virtuales creado desde el catálogo de soluciones con el [portal de Azure](https://portal.azure.com), PowerShell y CLI de Azure. La imagen de disco duro virtual base permanecerán sin cifrar; Sin embargo, cualquier escritura listo cuando haya terminado la máquina virtual estará cifrada.

-   Tabla, colas, y datos de archivos no se cifran.

##<a name="getting-started"></a>Introducción

###<a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>Paso 1: [crear una nueva cuenta de almacenamiento](storage-create-storage-account.md).

###<a name="step-2-enable-encryption"></a>Paso 2: Habilitar el cifrado.

Puede habilitar el cifrado con el [portal de Azure](https://portal.azure.com).

> [AZURE.NOTE] Si desea habilitar o deshabilitar el cifrado de servicio de almacenamiento en una cuenta de almacenamiento mediante programación, puede usar la [API de REST de proveedor de recursos de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx), la [Biblioteca de cliente de proveedor de recursos de almacenamiento para .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx), [Azure PowerShell](../powershell-install-configure.md)o [CLI de Azure](storage-azure-cli.md).

###<a name="step-3-copy-data-to-storage-account"></a>Paso 3: Copiar datos a la cuenta de almacenamiento

Si habilita SSE en una cuenta de almacenamiento y, a continuación, escribir BLOB a esa cuenta de almacenamiento, los BLOB estará cifrados. Cualquier ya se encuentra en esa cuenta de almacenamiento de blobs no estará cifrados hasta que se vuelven a escribir. Puede copiar los datos de cuenta de almacenamiento de uno a uno con SSE cifrado, o incluso habilitar SSE y copie el BLOB de un contenedor a otro a seguro que se cifran los datos anteriores. Puede usar cualquiera de las siguientes herramientas para ello.

#### <a name="using-azcopy"></a>Usar AzCopy

AzCopy es una herramienta de línea de comandos de Windows diseñada para copiar datos a y desde el almacenamiento de blobs de Microsoft Azure, el archivo y la tabla con los comandos simples con un rendimiento óptimo. Puede utilizarla para copiar el BLOB de una cuenta de almacenamiento a otra que tiene SSE habilitado. 

Para obtener más información, visite la [transferencia de datos con la herramienta de línea de comandos de AzCopy](storage-use-azcopy.md).

#### <a name="using-the-storage-client-libraries"></a>Uso de las bibliotecas de cliente de almacenamiento

Puede copiar datos blob a y desde el almacenamiento de blobs o entre cuentas de almacenamiento mediante nuestro amplio conjunto de bibliotecas de cliente de almacenamiento incluidos. NET, C++, Java, Android, Node.js, PHP, Python y Ruby.

Para obtener más información, visite nuestra [Introducción a almacenamiento de blobs de Windows Azure con .NET](storage-dotnet-how-to-use-blobs.md).

#### <a name="using-a-storage-explorer"></a>Con el Explorador de almacenamiento

Puede usar el Explorador de almacenamiento para crear cuentas de almacenamiento, cargar y descargar los datos, ver contenido de BLOB y desplazarse a través de directorios. Puede usar uno de estos para cargar BLOB en su cuenta de almacenamiento con cifrado habilitado. Con algunos exploradores de almacenamiento, también puede copiar datos de almacenamiento de blobs existente a otro contenedor de la cuenta de almacenamiento o una nueva cuenta de almacenamiento que tiene SSE habilitado.

Para obtener más información, visite [Exploradores de almacenamiento de Azure](storage-explorers.md).

###<a name="step-4-query-the-status-of-the-encrypted-data"></a>Paso 4: Consultar el estado de los datos cifrados

Se ha implementado una versión actualizada de las bibliotecas de cliente de almacenamiento que le permite consultar el estado de un objeto para determinar si están cifrado o no. Ejemplos se agregará a este documento en un futuro próximo.

Mientras tanto, puede llamar a [Obtener propiedades de la cuenta](https://msdn.microsoft.com/library/azure/mt163553.aspx) para comprobar que la cuenta de almacenamiento tiene cifrado habilitado o ver las propiedades de la cuenta de almacenamiento en el portal de Azure.

##<a name="encryption-and-decryption-workflow"></a>Flujo de trabajo de descifrado y cifrado

Esta es una breve descripción del flujo de trabajo de cifrado/descifrado:

-   El cliente habilita el cifrado en la cuenta de almacenamiento.

-   Cuando el cliente escribe nuevos datos (poner blobs, poner bloque, página colocar, etc.) para el almacenamiento de blobs; cada escritura se cifra con 256 [cifrado AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), uno de lo más potente cifrado de bloques disponible.

-   Cuando el cliente necesita tener acceso a datos (obtener blobs, etc.), los datos se descifra automáticamente antes de devolver al usuario.

-   Si se deshabilita el cifrado, escribe nuevos ya no se cifra y los datos cifrados permanecen cifrados hasta que volver a escribir el usuario. Mientras está habilitado el cifrado, escribe al almacenamiento de blobs estará cifrada. No cambia el estado de datos con el usuario alternar entre habilitar o deshabilitar el cifrado para la cuenta de almacenamiento.

-   Todas las claves de cifrado están almacenadas, cifradas y administradas por Microsoft.

##<a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Preguntas más frecuentes sobre el cifrado de servicio de almacenamiento para datos almacenados

**P: tengo una cuenta de almacenamiento clásico existente. ¿Habilitar SSE en él?**

R: no, SSE solo es compatible con cuentas de almacenamiento de administrador de recursos.

**P: ¿¿Cómo puedo cifrar datos en mi cuenta de almacenamiento clásica?**

R: puede crear una nueva cuenta de almacenamiento de administrador de recursos y copiar los datos con [AzCopy](storage-use-azcopy.md) desde su cuenta de almacenamiento clásico existente a su cuenta de almacenamiento de administrador de recursos recién creado. 

Otra opción es migrar su cuenta de almacenamiento clásico a una cuenta de almacenamiento administrar recursos. Para obtener más información, vea [Plataformas compatibles de IaaS recursos de migración de clásico para el Administrador de recursos](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/).

**P: tengo una cuenta existente de almacenamiento de administrador de recursos. ¿Habilitar SSE en él?**

R: Sí, pero solo recién escrito BLOB estará cifrada. No volver atrás y cifrar los datos que ya estaba presentes. 

**P: ¿deseo cifrar la información actual de una cuenta de almacenamiento de administrador de recursos existente?**

R: puede habilitar SSE en una cuenta de administrador de recursos de almacenamiento en cualquier momento. Sin embargo, los BLOB que ya no había se cifran. Para cifrar esas BLOB, puede copiarlos a otro nombre u otro contenedor y, a continuación, quite las versiones sin cifrar.

**P: ¿Estoy usando almacenamiento Premium; ¿Puedo usar SSE?**

R: Sí, SSE es compatible con almacenamiento estándar y almacenamiento Premium.

**P: si crear una nueva cuenta de almacenamiento y habilitar SSE, crear una nueva máquina virtual con esa cuenta de almacenamiento, ¿significa que está cifrado mi VM?**

R: Sí. Se cifra los discos creados que use la nueva cuenta de almacenamiento, siempre y cuando se crean cuando se habilita SSE. Si la máquina virtual se creó con Azure mercado lugar, la imagen de disco duro virtual base permanecerán sin cifrar; Sin embargo, cualquier escritura listo cuando haya terminado la máquina virtual estará cifrada.

**P: ¿puedo crear nuevas cuentas de almacenamiento con SSE habilitado mediante PowerShell de Azure y Azure CLI?**

R: Sí.

**P: ¿cómo mucho más cuesta el almacenamiento de Azure si SSE está habilitado?**

R: no hay sin costo adicional.

**P: ¿quién administra las claves de cifrado?**

A: Microsoft administra las claves.

**P: ¿puedo usar Mis propio claves de cifrado?**

R: estamos trabajando en proporcionar las capacidades de los clientes llevar sus propias claves de cifrado.

**P: ¿revocar el acceso a las claves de cifrado?**

R: no en este momento; las claves son totalmente administradas por Microsoft.

**P: ¿es SSE habilitado de forma predeterminada al crear una nueva cuenta de almacenamiento?**

R: SSE no está habilitado de forma predeterminada; Puede usar el portal de Azure habilitarlo. Mediante programación, puede habilitar esta característica con la API de REST de proveedor de recursos de almacenamiento.

**P: ¿cómo es diferente de cifrado de unidad de Azure?**

R: esta característica se usa para cifrar datos en el almacenamiento de blobs de Windows Azure. El cifrado de disco Azure se usa para cifrar discos OS y datos en máquinas virtuales de IaaS. Para obtener más detalles, visite nuestra [Guía de seguridad de almacenamiento](storage-security-guide.md).

**P: ¿qué debo hacer si habilitar SSE y, a continuación, vaya y habilitar el cifrado de disco de Azure en los discos?**

R: Esto funciona sin problemas. Los datos se cifran por ambos métodos.

**P: ¿Mi cuenta de almacenamiento está configurado para replicarse forma geo redundante. ¿Si habilita el SSE, mi copia redundante también estarán cifrada?**

R: Sí, se cifran todas las copias de la cuenta de almacenamiento, y son compatibles con todas las opciones de redundancia: almacenamiento redundantes localmente (LRS), redundantes de la zona de almacenamiento (ZRS), almacenamiento Geo redundantes (GRS) y almacenamiento de acceso de lectura Geo redundantes (RA GRS):.

**P: ¿no se puede habilitar el cifrado en mi cuenta de almacenamiento.**

¿R: es una cuenta de administrador de recursos de almacenamiento? Cuentas de almacenamiento clásica no son compatibles. 

**P: ¿SSE sólo se permite en regiones específicos?**

R: el SSE está disponible en todas las regiones. 

**P: ¿cómo contacto alguien si tengo problemas o desea proporcionar comentarios?**

R: póngase en contacto con [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) para los problemas relacionados con el cifrado de servicio de almacenamiento.

##<a name="next-steps"></a>Pasos siguientes

Almacenamiento de Azure proporciona un conjunto completo de características de seguridad que juntas permiten a los desarrolladores generar aplicaciones seguras. Para obtener más detalles, visite la [Guía de seguridad de almacenamiento](storage-security-guide.md).
