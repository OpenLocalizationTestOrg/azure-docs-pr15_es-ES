<properties
    pageTitle="¿Qué es la recuperación de sitio? | Microsoft Azure"
    description="Proporciona información general sobre el servicio de recuperación de sitios de Azure y un resumen de escenarios de implementación."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/13/2016"
    ms.author="raynew"/>

#  <a name="what-is-site-recovery"></a>¿Qué es la recuperación de sitio?

Bienvenido a la recuperación de Azure sitio! Este artículo proporciona una descripción general de servicio de recuperación de sitios y cómo contribuye a su empresa.

Su organización necesita una continuidad empresarial y la estrategia de recuperación (BCDR) que mantener aplicaciones, cargas de trabajo y datos seguros y disponibles durante el tiempo de inactividad planificado y y recupera tan pronto como sea posible a las condiciones de trabajo normal. Recuperación de sitio es un servicio de Azure que contribuye a esta estrategia.

Recuperación de sitio organiza la replicación de cargas de trabajo servidores físicos de local y máquinas virtuales. Puede replicar servidores y máquinas virtuales de un centro de datos principal en la nube (Azure) o a un centro de datos secundario. Cuando se produzcan interrupciones en el sitio primario, conmutar al sitio secundario para mantener las aplicaciones y cargas de trabajo accesibles y disponibles. No volver a su ubicación primaria cuando devuelve las operaciones normales.

## <a name="site-recovery-in-the-azure-portal"></a>Recuperación de sitio en el portal de Azure

Azure tiene dos distintos [modelos de implementación](../resource-manager-deployment-model.md) para crear y trabajar con recursos. El modelo de administrador de recursos de Azure y el modelo de administración de servicios clásico. Azure también tiene dos portales – el [portal clásica Azure](https://manage.windowsazure.com/) que admita el modelo de implementación clásico y el [portal de Azure](https://portal.azure.com) con soporte para el estándar y modelos de administrador de recursos.

- Recuperación de sitio está disponible en el portal de clásico y el portal de Azure.
- En el portal de clásico Azure, se puede admitir recuperación de sitios con el modelo de administración de servicios clásico.
- En el portal de Azure, se pueden admitir el modelo clásico o implementaciones del Administrador de recursos. 

La información en este artículo se aplica a implementaciones portal clásicas y Azure. Diferencias se indican donde sea aplicable.


## <a name="why-deploy-site-recovery"></a>¿Por qué implementar recuperación de sitio?

Esto es lo que puede hacer recuperación de sitio para su empresa:

- **Simplificar BCDR**, puede controlar la replicación, migración tras error y recuperación de múltiples cargas de trabajo en una única ubicación en el portal de Azure. Recuperación de sitio organiza replicación y migración tras error pero no interceptar los datos de aplicación o tiene cualquier información acerca de él.
- **Proporcionan replicación flexible**: uso de recuperación de sitios, puede replicar las cargas de trabajo que se ejecuta en máquinas virtuales de Hyper-V compatibles, máquinas virtuales de VMware y servidores físicos de Windows o Linux.
- **Replicación fácil de realizar pruebas**: recuperación de sitio proporciona migraciones tras error de prueba para admitir la recuperación de desastres, sin que ello afecte entornos de producción.
- **Recuperar y conmutar**: puede ejecutar failovers planificados para interrupciones esperados con una pérdida de datos o no planeado migraciones tras error con pérdida mínima de datos (dependiendo de la frecuencia de replicación) para desastres inesperados. Después de la migración, puede recuperación a los sitios principales. Recuperación de sitio proporciona planes de recuperación que pueden incluir secuencias de comandos y libros de automatización Azure, que puede personalizar migración y recuperación de aplicaciones de varios niveles.
- **Eliminar un centro de datos secundario**, puede replicar las cargas de trabajo a Azure, en lugar de en un sitio secundario. Esto elimina el costo y la complejidad de mantener un centro de datos secundario. Los datos duplicados se almacenan en el almacenamiento de Azure, con todos los la resistencia que proporciona. Cuando se produce error, se crean máquinas virtuales con los datos duplicados.
- **Integrar con las tecnologías BCDR existentes**: recuperación de sitio se integra con otras características BCDR. Por ejemplo, puede usar recuperación de sitio para proteger el servidor de SQL Server de cargas de trabajo corporativos, incluido el soporte técnico nativo de SQL Server AlwaysOn, para administrar la migración tras error de los grupos de disponibilidad.

## <a name="what-can-i-replicate"></a>¿Qué puedo replicar?

Este es un resumen de lo que puede replicar con el sitio de recuperación.

![Local a local](./media/site-recovery-overview/asr-overview-graphic.png)

**REPLICAR** | **REPLICAR EN** 
---|---
Cargas de trabajo que se ejecutan en máquinas virtuales de VMware local | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Sitio secundario](site-recovery-vmware-to-vmware.md)
Administran las cargas de trabajo que se ejecutan en máquinas virtuales de Hyper-V local en nubes VMM  | [Azure](site-recovery-vmm-to-azure.md)<br/><br/> [Sitio secundario](site-recovery-vmm-to-vmm.md) 
Carga de trabajo que se ejecutan en máquinas virtuales de Hyper-V local administrado en nubes VMM con almacenamiento SAN|  [Sitio secundario](site-recovery-vmm-san.md)
Cargas de trabajo que se ejecutan en máquinas virtuales de Hyper-V local, sin VMM | [Azure](site-recovery-hyper-v-site-to-azure.md)
Cargas de trabajo que se ejecutan en servidores de Windows o Linux físicos local | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Sitio secundario](site-recovery-vmware-to-vmware.md)


## <a name="what-workloads-can-i-protect"></a>¿Qué cargas de trabajo se debe proteger?

Recuperación de un sitio permite BCDR tener en cuenta la aplicación, para que las cargas de trabajo y aplicaciones continuarán para ejecutarse de forma coherente cuando se produzcan interrupciones. Recuperación de sitio proporciona:

- **Dr**: máquinas replican con Dr, para uno o varios niveles de aplicaciones. Además de capturar los datos de disco, Dr captura captura todos los datos en memoria y todas las transacciones en proceso.
- **Replicación cerca sincrónico**: recuperación de sitio proporciona la frecuencia de replicación es tan baja como 30 segundos para Hyper-V y la replicación continua para VMware.
- **Planes de recuperación flexibles**, puede crear y personalizar los planes de recuperación con scripts externos y acciones manuales. Integración con la automatización de Azure runbooks le permiten recuperar una pila de toda la aplicación con un solo clic.
- **Integración con SQL Server AlwaysOn**, puede administrar la migración tras error de los grupos de disponibilidad de los planes de recuperación de recuperación del sitio.
- **Biblioteca de automatización**, una biblioteca de automatización de Azure enriquecida proporciona las secuencias de comandos de producción: preparados, específicos de la aplicación que pueden descargar e integrados con el sitio de recuperación.
- **Administración de red simple**: requisitos de la red de aplicaciones, incluidos reservar direcciones IP, configurar equilibradores de carga y la integración de administrador de tráfico de Azure para switchovers de uso eficiente de la red simplifica la administración de red avanzada en Azure y recuperación de sitio.


## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información en [Qué cargas de trabajo de recuperación de sitios puede proteger?](site-recovery-workload.md)
- Obtenga más información sobre la arquitectura de recuperación de sitio en [¿cómo funciona el sitio recuperación?](site-recovery-components.md)
 
