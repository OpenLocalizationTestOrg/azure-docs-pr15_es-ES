<properties
   pageTitle="Información general de seguridad de máquinas virtuales de Windows Azure | Microsoft Azure"
   description=" Máquinas virtuales de Windows Azure le ofrecen la flexibilidad de la virtualización sin necesidad de comprar y mantener el hardware físico que ejecuta la máquina virtual.  Este artículo proporciona una descripción general de las principales características de seguridad de Azure que pueden utilizarse con Azure máquinas virtuales de Windows. "
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
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="azure-virtual-machines-security-overview"></a>Información general de seguridad de máquinas virtuales de Windows Azure

Máquinas virtuales de Windows Azure le permite implementar una amplia gama de la informática soluciones en forma ágil. Compatible con Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP y servicios de BizTalk de Azure, puede implementar cualquier carga de trabajo y cualquier idioma en casi cualquier sistema operativo.

Una máquina virtual Azure proporciona la flexibilidad de la virtualización sin necesidad de comprar y mantener el hardware físico que ejecuta la máquina virtual.  Puede crear e implementar aplicaciones con la seguridad que sus datos están seguros en nuestros centros de datos muy seguro y protegido.

Con Azure, puede crear soluciones compatibles con seguridad mejorada:

- Proteger las máquinas virtuales de virus y malware
- Cifrar la información confidencial
- Proteger el tráfico de red
- Identificar y detectar amenazas
- Cumple los requisitos de cumplimiento

El objetivo de este artículo es proporcionar una descripción general de las principales características de seguridad de Azure que se pueden usar con máquinas virtuales. También le proporcionamos los vínculos a artículos que proporcionan detalles de cada característica, por lo que puede obtener más información.  

Las capacidades de seguridad de máquina Virtual de Azure core a en este artículo se tratan:

- Antimalware
- Módulo de seguridad de hardware
- Cifrado de disco de máquina virtual
- Copia de seguridad de máquina virtual
- Recuperación de sitio de Azure
- Red virtual
- Informes y administración de directivas de seguridad
- Cumplimiento

## <a name="antimalware"></a>Antimalware

Con Azure, puede usar software antimalware de proveedores de seguridad, como Microsoft, Symantec, tendencia Micro, McAfee y Kaspersky para proteger sus máquinas virtuales de archivos malintencionados, publicidad y otras amenazas. Consulte la sección más información para buscar artículos en partner soluciones.

Microsoft Antimalware para servicios de nube de Azure y máquinas virtuales es una función de protección en tiempo real que le ayuda a identificar y eliminar virus, spyware y otro software malintencionado.  Microsoft Antimalware proporciona configurables alertas cuando conoce software malintencionado o no deseado intenta instalarse o ejecutarse en los sistemas de Azure.

Microsoft Antimalware es una solución de agente único para aplicaciones y entornos de inquilinos, diseñados para ejecutarse en segundo plano sin intervención humana. Puede implementar protección según las necesidades de las cargas de trabajo de la aplicación, con ambos básica seguro de forma predeterminada o avanzadas de configuración personalizada, incluida la supervisión de antimalware.

Al implementar y habilitar Microsoft Antimalware, están disponibles las siguientes características principales:

- Protección en tiempo real - supervisa la actividad de servicios en la nube y en máquinas virtuales de Windows para detectar y bloquear la ejecución de malware.
- Análisis programados - periódicamente realiza un análisis de destino para detectar malware, incluida la ejecución de forma activa programas.
- Corrección de malware - automáticamente realiza una acción sobre malware detectado, como eliminar o poner en cuarentena archivos malintencionados y limpiar las entradas del registro malintencionado.
- Actualizaciones de firmas: automáticamente las firmas de protección más recientes (definiciones de virus) para garantizar la protección de instalaciones está actualizado en una frecuencia predeterminada.
- Actualizaciones del motor de antimalware – automáticamente actualiza el motor de Antimalware de Microsoft.
- Plataforma antimalware automática de actualizaciones: actualiza la plataforma de Microsoft Antimalware.
- Protección activa - notifica a los metadatos de telemetría Azure sobre amenazas detectadas y recursos sospechosos para garantizar una respuesta rápida y permite la entrega de firma sincrónico en tiempo real a través de Microsoft Active Protection System (MAPS).
- Ejemplos de informes: proporciona informes y ejemplos con el servicio de Microsoft Antimalware para mejorar el servicio y habilitar la solución de problemas.
- Exclusiones: permite la aplicación y los administradores del servicio para configurar determinados archivos, procesos y unidades para impedir que protección y análisis de rendimiento y otras razones.
- Recopilación de eventos de antimalware - registra el estado del servicio antimalware, actividades sospechosas y acciones de corrección realizadas en el registro de eventos del sistema operativo y ellos se recogen en la cuenta del cliente el almacenamiento de Azure.

Más información: para obtener más información sobre el software antimalware a proteger sus máquinas virtuales, consulte:

- [Antimalware de Microsoft para servicios de nube de Azure y máquinas virtuales](../security/azure-security-antimalware.md)
- [Implementar soluciones de Antimalware en máquinas virtuales de Windows Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
- [Cómo instalar y configurar la seguridad de profundidad de tendencia Micro como un servicio en una máquina virtual de Windows](../virtual-machines/virtual-machines-windows-classic-install-trend.md)
- [Cómo instalar y configurar Symantec Endpoint Protection en una máquina virtual de Windows](../virtual-machines/virtual-machines-windows-classic-install-symantec.md)
- [Nuevas opciones de Antimalware para proteger máquinas virtuales de Azure – McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)
- [Soluciones de seguridad de Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>Módulo de seguridad de hardware

Autenticación y cifrado no mejorar la seguridad a menos que las claves están protegidas. Puede simplificar la administración y la seguridad de la información confidencial crítica y claves almacenarlos en depósito de clave de Azure. Depósito de clave proporciona la opción para almacenar las claves en los módulos de seguridad de hardware (HSM) certificadas para FIPS 140-2 nivel 2 estándares. Las claves de cifrado de SQL Server para copia de seguridad o [cifrado de datos transparente](https://msdn.microsoft.com/library/bb934049.aspx) puede almacenar en depósito de clave con las teclas o información confidencial de sus aplicaciones. Permisos y acceso a los elementos protegidos se administran a través de [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Aprende más:

- [¿Qué es depósito de clave de Azure?](../key-vault/key-vault-whatis.md)
- [Introducción a Azure clave depósito](../key-vault/key-vault-get-started.md)
- [Blog de Azure depósito de clave](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Cifrado de disco de máquina virtual

Cifrado de disco Azure es una nueva funcionalidad que le permite cifrar los discos de Windows y Máquina Virtual de Azure de Linux. Cifrado de disco Azure usa la característica [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) estándar de la industria de Windows y la característica de [cifrado dm](https://en.wikipedia.org/wiki/Dm-crypt) de Linux para proporcionar cifrado de volumen para el sistema operativo y los discos de datos.

La solución se integra con Azure clave depósito para ayudarle a controlar y administrar las claves de cifrado de disco y la información confidencial en su suscripción de depósito clave, mientras le garantiza que todos los datos en los discos de la máquina virtual se cifran almacenados en el almacenamiento de Azure.

Aprende más:

- [Cifrado de disco Azure para Windows y máquinas virtuales de Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
- [Cifrado de disco Azure para Linux y máquinas virtuales de Windows](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
- [Cifrar una máquina virtual](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>Copia de seguridad de máquina virtual

Copia de seguridad de Azure es una solución scalable que protege los datos de la aplicación con cero inversión de capital y costos operativos mínimos. Errores de aplicación pueden alterar los datos y errores humanos pueden introducir errores en las aplicaciones. Con copia de seguridad de Azure están protegidos sus máquinas virtuales que ejecutan Windows y Linux.

Aprende más:

- [¿Qué es la copia de seguridad de Azure?](../backup/backup-introduction-to-azure-backup.md)
- [Ruta de aprendizaje de copia de seguridad de Azure](https://azure.microsoft.com/documentation/learning-paths/backup/)
- [Servicio de copia de seguridad Azure - preguntas más frecuentes](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Recuperación de sitio de Azure

Una parte importante de la estrategia BCDR de su organización es averiguar cómo mantenerse cargas de trabajo corporativos y aplicaciones y ejecuta cuando se producen interrupciones planificadas y. Recuperación del sitio de Azure le ayuda a organizar replicación, migración tras error y recuperación de aplicaciones y cargas de trabajo para que estén disponibles desde una ubicación secundaria si su ubicación principal deja de funcionar.

Recuperación de sitio:

- **Simplifica la estrategia BCDR** : sitio recuperación hace que sea fácil manejar replicación, migración tras error y recuperación de múltiples cargas de trabajo de empresa y aplicaciones desde una única ubicación. Recuperación de sitio organiza replicación y migración tras error pero no interceptar los datos de aplicación o tiene cualquier información acerca de él.
- **Proporciona la replicación flexible** : uso de recuperación de sitios pueden replicar cargas de trabajo que se ejecuta en máquinas virtuales Hyper-V, máquinas virtuales de VMware y servidores físicos de Windows o Linux.
- **Es compatible con la migración tras error y recuperación** : recuperación de sitio proporciona migraciones tras error de prueba para admitir la recuperación de desastres sin que ello afecte entornos de producción. También puede ejecutar planeadas migraciones tras error con una pérdida de datos para interrupciones esperadas o no planeado migraciones tras error con pérdida mínima de datos (dependiendo de la frecuencia de replicación) para desastres inesperados. Después de la migración, puede recuperación a los sitios principales. Recuperación de sitio proporciona los planes de recuperación que pueden incluir secuencias de comandos y libros de Azure automatización para que pueda personalizar migración y recuperación de aplicaciones de varios niveles.
- **Centro de datos secundario elimina** : puede replicar en un sitio secundario local o en Azure. Uso de Azure como un destino de recuperación elimina el costo y la complejidad de mantener un sitio secundario. Los datos duplicados se almacenan en el almacenamiento de Azure.
- **Se integra con las tecnologías BCDR existentes** : recuperación de sitio se asocia con otras características BCDR de aplicación. Por ejemplo, puede usar recuperación de sitio para proteger el servidor SQL Server de cargas de trabajo corporativos. Esto incluye compatibilidad nativa para SQL Server AlwaysOn administrar la migración tras error de los grupos de disponibilidad.

Aprende más:

- [¿Qué es la recuperación de sitios de Azure?](../site-recovery/site-recovery-overview.md)
- [¿Cómo funciona la recuperación de sitio de Azure?](../site-recovery/site-recovery-components.md)
- [¿Qué cargas de trabajo están protegidos por la recuperación de sitios de Azure?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Red virtual

Máquinas virtuales necesita conectividad de red. Para admitir este requisito, Azure requiere máquinas virtuales que estar conectado a una red Virtual de Azure. Una red Virtual de Azure es una construcción lógica creada a partir de la estructura de red física de Azure. Cada red Virtual de Azure lógica está aislado de todas las demás redes Virtual de Azure. Este aislamiento ayuda a garantizar que el tráfico de red en las implementaciones no es accesible para otros clientes de Microsoft Azure.

Aprende más:

- [Información general de seguridad de red de Azure](security-network-overview.md)
- [Información general de una red virtual](../virtual-network/virtual-networks-overview.md)
- [Características de redes y asociaciones para escenarios empresariales](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Informes y administración de directivas de seguridad

Centro de seguridad de Azure le ayuda a evitar, detectar y responder a amenazas y proporciona que mayor visibilidad y control, la seguridad de los recursos de Azure. Proporciona seguridad integrada supervisión y la directiva de administración de las suscripciones de Azure, ayuda a detecta amenazas que pueden incluirse en caso contrario, problemas y funciona con un amplio ecosistema de soluciones de seguridad.

Centro de seguridad de Azure le ayuda a optimizar y supervisar la seguridad de la máquina virtual por:

- Proporcionar [recomendaciones de seguridad](../security-center/security-center-recommendations.md) de máquina virtual como aplicar actualizaciones del sistema, configurar ACL extremos, habilitar antimalware, habilitar a grupos de seguridad de la red y aplicar el cifrado de disco.
- Supervisar el estado de sus máquinas virtuales

Aprende más:

- [Introducción al centro de seguridad de Azure](../security-center/security-center-intro.md)
- [Preguntas más frecuentes sobre el centro de seguridad de Azure](../security-center/security-center-faq.md)
- [Planificación del centro de seguridad de Azure y operaciones](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Cumplimiento

Máquinas virtuales de Windows Azure está certificada para FISMA, FedRAMP, HIPAA, PCI DSS nivel 1 y otros programas de cumplimiento clave. Esta certificación es más fácil para sus propias aplicaciones de Azure para satisfacer requisitos de cumplimiento y para su empresa a una amplia gama de requisitos normativos nacionales e internacionales de direcciones.

Aprende más:

- [Centro de confianza de Microsoft: cumplimiento](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
- [Nube de confianza: Seguridad de Microsoft Azure, privacidad y cumplimiento](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)
