<properties
   pageTitle="Aplicar el cifrado de disco en el centro de seguridad de Azure | Microsoft Azure"
   description="Este documento muestra cómo implementar la recomendación del centro de seguridad de Azure **aplicar cifrado de disco**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="apply-disk-encryption-in-azure-security-center"></a>Aplicar el cifrado de disco en el centro de seguridad de Azure

Centro de seguridad de Azure se recomienda aplicar cifrado de disco si tiene Windows o Linux VM discos que no se cifran mediante el cifrado de disco de Azure. Cifrado de disco le permite cifrar los discos de Windows y Linux IaaS VM.  Cifrado se recomienda para volúmenes de sistema operativo y datos en la máquina virtual.


Cifrado de disco aprovecha la característica [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) estándar de la industria de Windows y la característica de [Cifrado DM](https://en.wikipedia.org/wiki/Dm-crypt) de Linux para proporcionar OS y cifrado de datos para ayudar a proteger y proteger sus datos y cumplir con los compromisos de seguridad y cumplimiento organizativas. Cifrado de disco se integra con [Azure clave depósito](https://azure.microsoft.com/documentation/services/key-vault/) para ayudarle a controlar y administrar las claves de cifrado de disco y la información confidencial en su suscripción de depósito tecla mientras le garantiza que todos los datos en los discos VM se cifran almacenados en el [Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/).

> [AZURE.NOTE] Cifrado de disco Azure es compatible con los siguientes sistemas operativos Windows server - Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2. Cifrado de disco es compatible con el siguiente servidor sistemas operativos Linux - Ubuntu, CentOS, SUSE y SUSE Linux Enterprise Server (SLES).

## <a name="implement-the-recommendation"></a>Implementar la recomendación

1. En el módulo de **recomendaciones** , seleccione **aplicar cifrado de disco**.
2. En el módulo de **aplicar cifrado de disco** , verá una lista de VM para el que se recomienda disco cifrado.
3. Siga las instrucciones para aplicar el cifrado a estas máquinas virtuales.

![][1]

Para cifrar máquinas virtuales de Azure que se hayan identificado como necesidad de cifrado por el centro de seguridad, le recomendamos lo siguiente:

- Instalar y configurar Azure PowerShell. Esto le permitirá ejecutar los comandos de PowerShell necesarios para configurar los requisitos previos necesarios para cifrar máquinas virtuales de Azure.
- Obtenga y ejecute la secuencia de comandos de PowerShell de Azure los requisitos previos de Azure disco cifrado.
- Cifrar las máquinas virtuales.

[Cifrar una máquina Virtual de Azure](security-center-disk-encryption.md) le guiará a través de estos pasos.  En este tema se supone que usa Windows 10 como el equipo cliente desde el que va a configurar cifrado de disco.

Hay muchos enfoques que se pueden usar para los requisitos previos de instalación y configurar el cifrado de Azure máquinas virtuales de Windows. Si ya están muchos conocimientos en Azure PowerShell o CLI de Azure, es posible que prefiera usar enfoques alternativos. Para obtener información acerca de estos otros enfoques vea [cifrado de disco Azure](../security/azure-security-disk-encryption.md).



## <a name="see-also"></a>Vea también

Este documento le mostramos cómo implementar la recomendación del centro de seguridad "Aplicar cifrado de disco". Para obtener más información sobre el cifrado de disco, consulte lo siguiente:

- [Cifrado y administración de claves con depósito de clave de Azure](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (vídeo, 36 39 min.): Obtenga información sobre cómo usar la administración de cifrado de disco de máquinas virtuales de IaaS y depósito de Azure clave para ayudar a proteger y proteger sus datos.
- [Cifrar una máquina Virtual de Azure](security-center-disk-encryption.md) (documento): Obtenga información sobre cómo cifrar máquinas virtuales de Azure.
- [Cifrado de disco de Azure](../security/azure-security-disk-encryption.md) (documento): Obtenga información sobre cómo habilitar el cifrado de disco para Windows y máquinas virtuales de Linux.

Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md) : Obtenga información sobre cómo configurar directivas de seguridad.
- [Supervisión del estado de seguridad del centro de seguridad de Azure](security-center-monitoring.md) : Obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Recomendaciones de seguridad de la administración de centro de seguridad de Azure](security-center-recommendations.md) : Descubra cómo recomendaciones ayudar a proteger los recursos de Azure.
- [Preguntas más frecuentes sobre centro de seguridad de azure](security-center-faq.md) --buscar con frecuencia preguntas más frecuentes sobre el uso del servicio.
- Entradas de [blog de seguridad de azure](http://blogs.msdn.com/b/azuresecurity/) --buscar blog sobre cumplimiento y la seguridad de Azure.



<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
