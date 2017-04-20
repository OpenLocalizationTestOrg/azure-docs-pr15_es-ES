<properties
    pageTitle="Introducción a la copia de seguridad de Azure DPM | Microsoft Azure"
    description="Introducción a la copia de seguridad de los servidores DPM mediante el servicio de copia de seguridad de Azure"
    services="backup"
    documentationCenter=""
    authors="Nkolli1"
    manager="shreeshd"
    editor=""
    keywords="Administrador de protección de datos de centro de sistema, Administrador de protección de datos, la copia de seguridad de dpm"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/21/2016"
    ms.author="trinadhk;giridham;jimpark;markgal"/>

# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Preparar la copia de seguridad de las cargas de trabajo en Azure con DPM

> [AZURE.SELECTOR]
- [Servidor de copia de seguridad de Azure](backup-azure-microsoft-azure-backup.md)
- [SCDPM](backup-azure-dpm-introduction.md)
- [Servidor de copia de seguridad de Azure (clásico)](backup-azure-microsoft-azure-backup-classic.md)
- [SCDPM (clásico)](backup-azure-dpm-introduction-classic.md)


Este artículo proporciona una introducción al uso de copia de seguridad de Microsoft Azure para proteger los servidores de System Center Data Protection Manager (DPM) y las cargas de trabajo. Leerlo, podrá comprender:

- Funcionamiento de la copia de seguridad de servidor DPM de Azure
- Los requisitos previos para lograr una experiencia de copia de seguridad suave
- Los errores típicos encontrados y cómo tratar con ellos
- Escenarios compatibles

System Center DPM copia los datos de aplicaciones y archivos. Los datos copiados a DPM pueden ser almacenados en la cinta, en el disco, o una copia en Azure con copia de seguridad de Microsoft Azure. DPM interactúa con copia de seguridad de Azure como sigue:

- **DPM implementado como una máquina virtual de servidor o local física** : DPM si se implementa como un servidor físico o como una máquina virtual Hyper-V en local que puede realizar una copia de datos a una copia de seguridad de Azure depósito además de disco y la cinta una copia de seguridad.
- **DPM implementado como una máquina virtual Azure** — de System Center 2012 R2 con 3 de actualización, se puede implementar DPM como una máquina virtual Azure. Si DPM se implementa como una máquina virtual Azure que puede crear una copia de datos de Azure discos adjuntos a la máquina virtual de Azure DPM o puede descargar el almacenamiento de datos realizando hasta un depósito de copia de seguridad de Azure.

## <a name="why-backup-your-dpm-servers"></a>¿Por qué copia de seguridad de los servidores DPM?

Las ventajas de la empresa del uso de copia de seguridad de Azure para la copia de seguridad de servidores DPM incluyen:

- Para la implementación local de DPM, puede usar la copia de seguridad de Azure como alternativa a la implementación a largo plazo en cinta.
- Para implementaciones de DPM en Azure, copia de seguridad de Azure le permite descargar almacenamiento desde el disco de Azure, lo que le permite escalar al almacenar datos antiguos en copia de seguridad de Azure y datos nuevos en disco.

## <a name="how-does-dpm-server-backup-work"></a>¿Cómo funciona la copia de seguridad DPM server?
Para la copia de seguridad una máquina virtual, primero se necesita una instantánea de punto en el tiempo de los datos. El servicio de copia de seguridad de Azure inicia el trabajo de copia de seguridad en el tiempo programado y activa la extensión de copia de seguridad para tomar una instantánea. La extensión de copia de seguridad coordenadas con el servicio VSS en invitado para lograr la coherencia y llama a la API de instantánea blob del servicio de almacenamiento de Azure una vez que se alcanzó la coherencia. Para ello, para obtener una instantánea coherente de los discos de la máquina virtual, sin tener que salir de ella.

Después de que se haya tomado la instantánea, los datos se transfieren por el servicio de copia de seguridad de Azure en el depósito de copia de seguridad. El servicio se encarga de identificar y transferir sólo los bloques que han cambiado desde la última copia de seguridad, mejorar la eficacia de la red y el almacenamiento de las copias de seguridad. Cuando se complete la transferencia de datos, se elimina la instantánea y se crea un punto de recuperación. En este punto de recuperación se puede ver en el portal de clásico de Azure.

>[AZURE.NOTE] Máquinas virtuales de Linux, es posible solo coherente del archivo de copia de seguridad.

## <a name="prerequisites"></a>Requisitos previos
Preparar la copia de seguridad de Azure para realizar copias de seguridad de datos DPM como sigue:

1. **Crear un depósito de copia de seguridad** : crear un depósito en la consola de copia de seguridad de Azure.
2. **Credenciales de la cámara de descarga** : en copia de seguridad de Azure, cargar el certificado de administración que creó en el depósito.
3. **Instalar el agente de copia de seguridad de Azure y registrar el servidor** , de Azure copia de seguridad, instale el agente en cada servidor DPM y registrar el servidor DPM en depósito de copia de seguridad.

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]


## <a name="requirements-and-limitations"></a>Requisitos (y limitaciones)

- DPM se puede ejecutar como un servidor físico o una máquina virtual de Hyper-V instalado en System Center 2012 SP1 o System Center 2012 R2. También se puede ejecutar como una máquina virtual Azure ejecuta en System Center 2012 R2 con al menos DPM 2012 R2 Update Rollup 3 o una máquina virtual de Windows en VMWare ejecutando en System Center 2012 R2 con al menos paquete acumulativo 5.
- Si está ejecutando DPM con System Center 2012 SP1, debe instalar Update Rollup 2 de System Center Data Protection Manager SP1. Esto es necesario antes de poder instalar al agente de copia de seguridad de Azure.
- El servidor DPM debe tener Windows PowerShell y .net Framework 4.5 instalado.
- DPM puede respaldar la mayoría de las cargas de trabajo para la copia de seguridad de Azure. Para obtener una lista completa de lo que ha compatible Véase la copia de seguridad de Azure admite elementos siguientes.
- No se puede recuperar los datos almacenados en la copia de seguridad de Azure con la opción "copiar en cinta".
- Tendrá una cuenta de Azure con la característica de copia de seguridad de Azure habilitada. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Obtenga información sobre [precios de copia de seguridad de Azure](https://azure.microsoft.com/pricing/details/backup/).
- Usar copias de seguridad de Azure requiere el agente de copia de seguridad de Azure esté instalado en los servidores que desea hacer copia de seguridad. Cada servidor debe tener al menos un 10% del tamaño de los datos que la copia de seguridad, disponible como espacio de almacenamiento local. Por ejemplo, la copia de seguridad de 100 GB de datos, requiere un mínimo de 10 GB de espacio libre en la ubicación de borrador. Mientras que el valor mínimo es 10%, se recomienda 15% de espacio local que se usará para la ubicación en caché.
- Datos se almacenarán en el almacenamiento de Azure depósito. No hay ningún límite en la cantidad de datos que puede hacer copia de seguridad una copia de seguridad de Azure depósito pero el tamaño de un origen de datos (por ejemplo una máquina virtual o una base de datos) no debe superar 54,400 GB.

Estos tipos de archivos son compatibles con copia de seguridad en Azure:

- Cifrado (sólo copias de seguridad completas)
- Comprimir (incrementales compatibles)
- Sparse (incrementales compatibles)
- Comprimido y disperso (considera Sparse)

Y estos son compatibles:

- No se admiten los servidores de sistemas de archivos entre mayúsculas y minúsculas.
- Vínculos de disco duro (omitido)
- Puntos (omitido) de análisis
- Cifrado y comprimido (omitido)
- Cifrado y disperso (omitir)
- Secuencia comprimida
- Secuencia de dispersión

>[AZURE.NOTE] Desde en System Center 2012 DPM con SP1 o posterior, hacer una copia de seguridad protegidos por DPM a Azure con copia de seguridad de Microsoft Azure de cargas de trabajo.
