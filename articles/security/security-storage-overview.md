<properties
   pageTitle="Información general de seguridad de almacenamiento de Azure | Microsoft Azure"
   description=" Almacenamiento de Azure es la solución de almacenamiento de la nube para aplicaciones modernas que se basan en la duración, disponibilidad y escalabilidad para satisfacer las necesidades de sus clientes. Este artículo proporciona una descripción general de las principales características de seguridad de Azure que se pueden usar con el almacenamiento de Azure. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/16/2016"
   ms.author="terrylan"/>

# <a name="azure-storage-security-overview"></a>Información general de seguridad de almacenamiento de Azure

Almacenamiento de Azure es la solución de almacenamiento de la nube para aplicaciones modernas que se basan en la duración, disponibilidad y escalabilidad para satisfacer las necesidades de sus clientes. Almacenamiento de Azure proporciona un conjunto completo de características de seguridad:

- La cuenta de almacenamiento se puede proteger mediante el Control de acceso basado en roles y Azure Active Directory.
- Pueden proteger los datos al transmitirlos entre una aplicación y Azure mediante el cifrado de cliente, HTTPS o SMB 3.0.
- Pueden establecer el datos para cifrar automáticamente al escribir en el almacenamiento de Azure usa cifrado de servicio de almacenamiento.
- Discos de sistema operativo y datos usados por máquinas virtuales se pueden establecer para ser cifrada con cifrado de disco de Azure.
- Se pueden conceder acceso delegado a los objetos de datos en el almacenamiento de Azure usar firmas de acceso compartido.
- Método de autenticación usado por una persona cuando tengan acceso a almacenamiento se puede controlar mediante el análisis de almacenamiento.

Para más detalles sobre la seguridad en el almacenamiento de Azure, consulte la [Guía de seguridad de almacenamiento de Azure](../storage/storage-security-guide.md). Esta guía proporciona un análisis detallado las características de seguridad de almacenamiento de Azure como claves de la cuenta de almacenamiento, el cifrado de datos al transmitirlos y al resto y el análisis de almacenamiento.

Este artículo proporciona una descripción general de las características de seguridad de Azure que se pueden usar con el almacenamiento de Azure. Los vínculos son siempre a artículos que ofrecen detalles de cada característica, puede obtener más.

Estas son las características principales que se tratan en este artículo:

- Control de acceso basado en roles
- Acceso delegado a los objetos de almacenamiento
- Cifrado en tránsito
- Cifrado al resto y cifrado de servicio de almacenamiento de información
- Cifrado de disco de Azure
- Depósito de clave de Azure

## <a name="role-based-access-control-rbac"></a>Control de acceso basado en roles (RBAC)

Puede proteger su cuenta de almacenamiento con Control de acceso basado en roles (RBAC). Restringir el acceso basándose en los principios de seguridad de [menor privilegio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) y [necesita saber](https://en.wikipedia.org/wiki/Need_to_know) es fundamental para las organizaciones que desea aplicar directivas de seguridad para el acceso a los datos. Estos derechos de acceso se le concede al asignar el rol RBAC correspondiente a grupos y aplicaciones en un determinado ámbito. Puede usar [funciones RBAC integradas](../active-directory/role-based-access-built-in-roles.md), como colaborador de la cuenta de almacenamiento, para asignar privilegios a los usuarios.

Aprende más:

- [Control de acceso basado en roles de Azure Active Directory](../active-directory/role-based-access-control-configure.md)

## <a name="delegated-access-to-storage-objects"></a>Acceso delegado a los objetos de almacenamiento

Una firma de acceso compartido (SA) proporciona acceso delegado a los recursos en su cuenta de almacenamiento. Las asociaciones de seguridad significa que puede conceder que permisos a los objetos de su cuenta de almacenamiento limitados de un cliente durante un período específico de tiempo y con un conjunto de permisos especificado. Puede conceder estos permisos limitados sin tener que compartir su teclas de acceso de la cuenta. Las asociaciones de seguridad es un URI que abarca en sus parámetros de consulta toda la información necesaria para el acceso autenticado a un recurso de almacenamiento. Para obtener acceso a recursos de almacenamiento con las asociaciones de seguridad, el cliente sólo necesita pasar las asociaciones de seguridad para el método o constructor adecuado.

Aprende más:

- [Descripción del modelo de SA](../storage/storage-dotnet-shared-access-signature-part-1.md)
- [Crear y usar un SA con el almacenamiento de blobs](../storage/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Cifrado en tránsito
Cifrado en tránsito es un mecanismo de protección de datos cuando se transmite a través de redes. Con el almacenamiento de Azure puede proteger datos mediante:

- [Cifrado de nivel de transporte](../storage/storage-security-guide.md#encryption-in-transit), como HTTPS al transferir datos dentro o fuera de almacenamiento de Azure.
- [Cifrado comunicaciones](../storage/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), como el cifrado de SMB 3.0 Azure recursos compartidos de archivos.
- [Cifrado de cliente](../storage/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage)para cifrar los datos antes de que se transfieran en el almacenamiento y descifrar los datos después de que se transfieran sin espacio de almacenamiento.

Más información sobre el cifrado de cliente:

- [Cifrado de cliente para el almacenamiento de Microsoft Azure](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
- [Serie de controles de seguridad de la nube: cifrar datos al transmitirlos](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Cifrado al resto

Para muchas organizaciones, el [cifrado de datos en el resto](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) es un paso obligatorio hacia la soberanía de datos, el cumplimiento y privacidad de los datos. Hay tres características Azure que proporcionan cifrado de los datos "en descanso":

- [Cifrado de servicio de almacenamiento](../storage/storage-security-guide.md#encryption-at-rest) le permite solicitar que el servicio de almacenamiento cifrar automáticamente los datos al escribir en el almacenamiento de Azure.
- [Cifrado de cliente](../storage/storage-security-guide.md#client-side-encryption) también proporciona la característica de cifrado al resto.
- [Cifrado de disco de Azure](../storage/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) le permite cifrar los discos de sistema operativo y datos usado una máquina virtual de IaaS.

Más información sobre el cifrado de servicio de almacenamiento:

- [Cifrado de servicio de almacenamiento de Azure](https://azure.microsoft.com/services/storage/) está disponible para [El almacenamiento de blobs de Windows Azure](https://azure.microsoft.com/services/storage/blobs/). Para obtener más información sobre otros tipos de almacenamiento de Azure, consulte [archivo](https://azure.microsoft.com/services/storage/files/), [disco (almacenamiento Premium)](https://azure.microsoft.com/services/storage/premium-storage/), [tabla](https://azure.microsoft.com/services/storage/tables/)y [cola](https://azure.microsoft.com/services/storage/queues/).
- [Cifrado de servicio de almacenamiento de Azure para datos almacenados](../storage/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Cifrado de disco de Azure

Cifrado de disco Azure para máquinas virtuales (VM) le ayuda a requisitos de cumplimiento y seguridad de la organización dirección mediante el cifrado de los discos VM (incluidos los discos de inicio y de datos) con claves y directivas de que controlar en [Depósito de clave de Azure](https://azure.microsoft.com/services/key-vault/).

Cifrado de disco para máquinas virtuales funciona para sistemas operativos Linux y Windows. También se usa depósito clave para ayudarle a proteger, administrar y auditar el uso de las claves de cifrado de disco. Todos los datos de los discos VM está cifrado al resto mediante la tecnología de cifrado estándar en sus cuentas de almacenamiento de Azure. La solución de cifrado de disco para Windows se basa en [Unidad Microsoft BitLocker](https://technet.microsoft.com/library/cc732774.aspx)y la solución Linux se basa en [dm cifrado](https://en.wikipedia.org/wiki/Dm-crypt).

Aprende más:

- [Cifrado de disco Azure para Windows y máquinas virtuales de Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-key-vault"></a>Depósito de clave de Azure

Cifrado de disco Azure usa [Depósito de Azure clave](https://azure.microsoft.com/services/key-vault/) para ayudarle a controlar y administrar las claves de cifrado del disco y la información confidencial en su suscripción de depósito clave, mientras le garantiza que todos los datos en los discos de la máquina virtual se cifran almacenados en el almacenamiento de Azure. Debe utilizar depósito de clave para las claves y el uso de la directiva de auditoría.

Aprende más:

- [¿Qué es depósito de clave de Azure?](../key-vault/key-vault-whatis.md)
- [Introducción a Azure clave depósito](../key-vault/key-vault-get-started.md)
