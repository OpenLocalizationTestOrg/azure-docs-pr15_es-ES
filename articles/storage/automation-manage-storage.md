<properties
    pageTitle="Administrar el almacenamiento de Azure mediante la automatización de Azure"
    description="Obtenga información acerca de cómo puede utilizar el servicio de automatización de Azure para administrar el almacenamiento de Azure a escala."
    services="storage, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="eamono"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-storage-using-azure-automation"></a>Administrar el almacenamiento de Azure mediante la automatización de Azure

Esta guía presentan el servicio de automatización de Azure y cómo puede utilizarse para simplificar la administración de almacenamiento de Azure BLOB, tablas y colas.


## <a name="what-is-azure-automation"></a>¿Qué es la automatización de Azure?

[Automatización de Azure](https://azure.microsoft.com/services/automation/) es un servicio de Azure para simplificar la administración de la nube mediante automatización de procesos. Mediante la automatización de Azure, tareas larga duración, manuales, errores y repiten con frecuencia se pueden automatizar para aumentar la confiabilidad y la eficiencia y reducen el tiempo de valor para su organización.

Automatización de Azure proporciona un motor de ejecución de flujo de trabajo altamente confiable y altamente disponibles escala para satisfacer sus necesidades a medida que crece su organización. En automatización de Azure, procesos pueden ser dio manualmente, parte 3 º sistemas o intervalos programados para que las tareas que se producen exactamente cuando sea necesario.

Reducir la sobrecarga operativa y liberar TI / DevOps personal para centrarse en el trabajo que agrega empresarial valor al mover las tareas de administración de la nube se ejecute automáticamente mediante la automatización de Azure.


## <a name="how-can-azure-automation-help-manage-azure-storage"></a>¿Cómo puede automatización de Azure ayudar a administrar el almacenamiento de Azure?

Almacenamiento de Azure puede administrarse en Azure automatización mediante los cmdlets de PowerShell que están disponibles en [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automatización de Azure tiene estos cmdlets de PowerShell de almacenamiento disponibles fuera del cuadro, para que pueda realizar sus blobs, tabla, y las tareas de administración de cola dentro del servicio. También puede emparejar estos cmdlets en automatización de Azure con los cmdlets de otros servicios de Azure, para automatizar tareas complejas en servicios de Azure y 3er sistemas de terceros.

La [automatización de Azure runbook Galería](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contiene una variedad de runbooks de comunidad y grupo de producto para empezar a automatizar la administración de almacenamiento de Azure, otros servicios de Azure y 3er sistemas de terceros. Galería runbooks incluyen:

 * [Quitar Azure almacenamiento de Blobs que son ciertos días antiguo usando automatización de servicio](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
 * [Descargue un Blob de almacenamiento de Azure](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
 * [Todos los discos de copia de seguridad para una única VM Azure o para todas las máquinas virtuales en un servicio de nube](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)


## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de automatización de Azure y cómo puede utilizarse para administrar el almacenamiento de Azure BLOB, tablas y colas, siga estos vínculos para obtener más información acerca de automatización de Azure.

Consulte el tutorial de automatización de Azure [crear o importar un runbook de automatización de Azure](../automation/automation-creating-importing-runbook.md).
 
