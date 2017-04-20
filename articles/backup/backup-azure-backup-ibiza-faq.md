<properties
   pageTitle="Servicios de recuperación de depósito preguntas más frecuentes | Microsoft Azure"
   description="Esta versión de las preguntas más frecuentes es compatible con la versión preliminar pública de servicio de copia de seguridad de Azure. Respuestas a las preguntas más frecuentes sobre el agente de copia de seguridad, copia de seguridad y retención, recuperación, seguridad y otras preguntas comunes sobre la solución de copia de seguridad de Azure."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="solución de copia de seguridad. servicio de copia de seguridad"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="10/21/2016"
     ms.author="trinadhk; markgal; jimpark;"/>

# <a name="recovery-services-vault---faq"></a>Depósito de servicios de recuperación - preguntas más frecuentes


En este artículo se proporciona información específica en depósito de servicios de recuperación y lo complementos las [Preguntas más frecuentes de copia de seguridad de Azure](backup-azure-backup-faq.md). Las preguntas más frecuentes de copia de seguridad de Azure proporciona el conjunto completo de preguntas y respuestas acerca del servicio de copia de seguridad de Azure.  

Puede formular preguntas acerca de la copia de seguridad de Azure en la sección Disqus de este artículo o un artículo relacionado. También puede publicar preguntas sobre el servicio de copia de seguridad de Azure en el [foro de discusión](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>Depósitos de servicios de recuperación están en función de administrador de recursos. ¿Depósitos de copia de seguridad (modo clásico) aún se admiten? <br/>
Sí, todavía se admiten depósitos de copia de seguridad. Crear depósitos de copia de seguridad en el [portal de clásico](https://manage.windowsazure.com). Crear los servicios de recuperación depósitos en el [portal de Azure](https://portal.azure.com). Sin embargo, le recomendamos encarecidamente crear depósito de servicios de recuperación como todas las futuras mejoras estará disponible sólo en depósito de servicios de recuperación.

## <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>¿Puedo migrar un depósito de copia de seguridad a un depósito de servicios de recuperación? <br/>
Desgraciadamente, no, en este momento no puede migrar el contenido de un depósito de copia de seguridad a un depósito de servicios de recuperación. Estamos trabajando en Agregar esta funcionalidad, pero no está disponible como parte de la versión preliminar pública.

## <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>¿Servicios de recuperación depósitos admiten VM clásicas o administrador de recursos en máquinas virtuales? <br/>
Depósitos de servicios de recuperación admiten ambos modelos.  Hacer copia de seguridad una máquina virtual creada en el portal de clásico (que son máquinas virtuales de modo clásico) o una máquina virtual creado en el portal de Azure (que se basa el Administrador de recursos) a un depósito de servicios de recuperación.

## <a name="i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode--how-can-i-backup-them-in-recovery-services-vault"></a>He copia de mi VM clásicas en depósito de copia de seguridad. Ahora desea migrar mi máquinas virtuales de modo clásico al modo de administrador de recursos.  ¿Cómo puedan copia de seguridad de ellos en depósito de servicios de recuperación?
Copias de seguridad de máquinas virtuales clásicas en depósito de copia de seguridad no se migran automáticamente a depósito de servicios de recuperación al migrar las máquinas virtuales de clásico a modo de administrador de recursos. Siga estos pasos para la migración de las copias de seguridad de la máquina virtual:

1. En depósito de copia de seguridad, vaya a la pestaña **Elementos protegidos** y seleccione la máquina virtual. Haga clic en [Detener protección](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines). Deje de *Eliminar datos de copia de seguridad asociados* opción **desactivada**.
2. Migrar la máquina virtual de modo clásico al modo de administrador de recursos. Asegúrese de que almacenamiento y red correspondiente a la máquina virtual también se migran a modo de administrador de recursos.
3. Crear un depósito de servicios de recuperación y configurar la copia de seguridad en la máquina virtual migrada con la acción de **copia de seguridad** sobre los paneles de la cámara. Más información sobre cómo [Habilitar la copia de seguridad en depósito de servicios de recuperación](backup-azure-vms-first-look-arm.md)
