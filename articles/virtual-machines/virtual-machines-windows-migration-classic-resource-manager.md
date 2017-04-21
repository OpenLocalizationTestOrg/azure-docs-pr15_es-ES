<properties
    pageTitle="Migración de plataforma compatible de recursos de IaaS de clásico para el Administrador de recursos de Azure | Microsoft Azure"
    description="En este artículo se recorre la migración compatibles con la plataforma de recursos de clásico para el Administrador de recursos de Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kasing"/>

# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Migración de plataforma compatible de recursos de IaaS de clásico para el Administrador de recursos de Azure

En este artículo se describe cómo estamos permitiendo migración de infraestructura como un recursos de servicio (IaaS) desde el clásico para modelos de implementación de administrador de recursos. Puede obtener más información sobre las [ventajas y las características del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md). Se explican cómo conectar los recursos de los modelos de implementación de dos coexisten en su suscripción mediante el uso de las puertas de enlace a sitios de una red virtual. 

## <a name="goal-for-migration"></a>Objetivo de migración

Administrador de recursos permite distribuir aplicaciones complejas a través de plantillas, configura máquinas virtuales de Windows mediante extensiones VM e incorpora etiquetado y administración de acceso. Administrador de recursos de Azure incluye implementación scalable, paralelo para máquinas virtuales en conjuntos de disponibilidad. El nuevo modelo de implementación también proporciona administración del ciclo de vida del cálculo, red y almacenamiento por separado. Por último, hay un enfoque en habilitar la seguridad con el cumplimiento de máquinas virtuales de una red virtual de forma predeterminada.

Casi todas las características del modelo de implementación clásica son compatibles para proceso, red y almacenamiento en el Administrador de recursos de Azure. Para beneficiarse de las capacidades nuevas en el Administrador de recursos de Azure, puede migrar implementaciones existentes desde el modelo de implementación de clásico.

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Cambios en la automatización y herramientas después de la migración

Como parte de migrar los recursos desde el modelo de implementación estándar para el modelo de implementación de administrador de recursos, debe actualizar su automatización existente o herramientas para asegurarse de que continúa funcionando después de la migración.

## <a name="meaning-of-migration-of-iaas-resources-from-classic-to-resource-manager"></a>Significado de migración de recursos de IaaS de clásico para el Administrador de recursos

Antes de que se explorar en profundidad los detalles, echemos un vistazo a la diferencia entre las operaciones de datos plano y plano de la administración de los recursos de IaaS.

- *Plano de administración* , se describen las llamadas que entran en el plano de administración o la API para modificar los recursos. Por ejemplo, las operaciones como la creación de una máquina virtual, reiniciar una máquina virtual y actualizar una red virtual con una nueva subred administración los recursos de ejecución. No afectan directamente a conectarse a las instancias.
- *Plano de datos* (aplicación) describe el tiempo de ejecución de la aplicación e implica la interacción con instancias que no vaya a través de la API de Azure. Obtener acceso a su sitio Web o extraer datos desde una instancia de SQL Server en ejecución o un servidor de MongoDB consideraría datos interacción plano o de la aplicación. Copiar un blob desde una cuenta de almacenamiento y tener acceso a una dirección IP pública RDP o SSH en la máquina virtual también son plano de datos. Estas operaciones tenga la aplicación que se ejecuta en proceso, redes y almacenamiento.

>[AZURE.NOTE] En algunos escenarios de migración, la plataforma de Windows Azure se detiene, cancela la asignación y reinicia las máquinas virtuales. Esto supone un breve tiempo de inactividad de plano de datos.

## <a name="supported-scopes-of-migration"></a>Ámbitos admitidos de migración

Existen tres ámbitos de migración principalmente bienes proceso, red y almacenamiento. 

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migración de máquinas virtuales (no en una red virtual)

En el modelo de implementación de administrador de recursos, se exige seguridad para las aplicaciones de forma predeterminada. Todas las máquinas virtuales deben estar en una red virtual en el modelo de administrador de recursos. El reinicio de la plataforma Windows Azure (`Stop`, `Deallocate`, y `Start`) las máquinas virtuales como parte de la migración. Tiene dos opciones para las redes virtuales:

- Puede solicitar la plataforma para crear una nueva red virtual y migrar la máquina virtual a la nueva red virtual.
- Puede migrar la máquina virtual en una red virtual existente en el Administrador de recursos.

>[AZURE.NOTE] En este ámbito de migración, las operaciones de plano de administración y las operaciones de datos plano puedan durante un período de tiempo durante la migración.

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migración de máquinas virtuales (en una red virtual)

Para la mayoría de las configuraciones de máquina virtual solo los metadatos está migrando entre los modelos de implementación estándar y el Administrador de recursos. Las VM subyacentes se ejecutan en el mismo hardware, en la misma red y con el mismo almacenamiento. Las operaciones de plano de administración no podrán para un período de tiempo durante la migración. Sin embargo, el plano de datos continúa funcionando. Es decir, las aplicaciones que se ejecutan en máquinas virtuales (clásicas) no provocan el tiempo de inactividad durante la migración.

Las configuraciones siguientes no se admiten actualmente. Si se agrega compatibilidad con en el futuro, algunas máquinas virtuales en esta configuración podría provocar el tiempo de inactividad (Ir a través de tabulación, Cancelar y reiniciar operaciones de VM).

-   Tiene más de una disponibilidad establecer en un servicio de nube solo.
-   Tiene uno o varios conjuntos de disponibilidad y máquinas virtuales que no están en una conjunto en un servicio de nube solo la disponibilidad.

>[AZURE.NOTE] En este ámbito de la migración, el plano de administración no se permite durante un período de tiempo durante la migración. Para ciertas configuraciones como se describió anteriormente, se produce el tiempo de inactividad de plano de datos.

### <a name="storage-accounts-migration"></a>Migración de cuentas de almacenamiento

Para permitir la migración transparente, puede implementar máquinas virtuales de administrador de recursos en una cuenta de almacenamiento clásica. Con esta función, recursos de red y cálculo pueden y deben migrarse independientemente de cuentas de almacenamiento. Una vez que se migrarán las máquinas virtuales y una red Virtual, necesita migrar sobre sus cuentas de almacenamiento para completar el proceso de migración. 

>[AZURE.NOTE] El modelo de implementación de administrador de recursos no tiene el concepto de imágenes clásica y discos. Cuando la cuenta de almacenamiento es imágenes migradas clásicas y discos no son visibles en la pila de administrador de recursos, pero la copia de seguridad VHD permanecen en la cuenta de almacenamiento. 

## <a name="unsupported-features-and-configurations"></a>Las configuraciones y características no compatibles

No actualmente se admiten algunas características y configuraciones. Las secciones siguientes describen nuestras recomendaciones sobre ellos.

### <a name="unsupported-features"></a>Características no compatibles

Las siguientes características no se admiten actualmente. Puede quitar opcionalmente esta configuración, migrar las máquinas virtuales y, a continuación, volver a habilitar a la configuración en el modelo de implementación de administrador de recursos.

Proveedor de recursos | Característica
---------- | ------------
Calcular | Discos no asociados máquina virtual.
Calcular | Imágenes de máquina virtual.
Red | ACL de extremo.
Red | Puertas de enlace de red virtual (para cada sitio, Azure ExpressRoute, puerta de enlace de aplicaciones, seleccione sitio).
Red | Redes virtuales con interconexión VNet. (Migrar VNet a ARM después del mismo nivel) Más información sobre [VNet interconexión] (.. /Virtual-Network/Virtual-Network-Peering-Overview.MD).
Red | Perfiles de administrador de tráfico.

### <a name="unsupported-configurations"></a>Configuraciones no admitidas

Las configuraciones siguientes no se admiten actualmente.

Servicio | Configuración | Recomendación
---------- | ------------ | ------------
Administrador de recursos | Control de acceso basado (RBAC) de rol para recursos clásicos | Porque el URI de los recursos se modifica después de la migración, se recomienda que planee las actualizaciones de directiva RBAC que deben ocurrir después de la migración.
Calcular | Varias subredes asociadas a una máquina virtual | Actualizar la configuración de subred para hacer referencia a solo subredes.
Calcular | Máquinas virtuales que pertenecen a una red virtual, pero no tiene una subred explícita asignada | Si lo desea, puede eliminar la máquina virtual.
Calcular | Máquinas virtuales de Windows que tienen alertas, Autoescala directivas | La migración atraviesa y se eliminan esta configuración. Se recomienda evaluar su entorno antes de realizar la migración. Como alternativa, puede volver a configurar las opciones de alertas una vez completada la migración.
Calcular | Extensiones de XML VM (1.* no BGInfo, depurador de Visual Studio, Web implementar y depuración remota) | Esto no es compatible. Se recomienda que quite estas extensiones de la máquina virtual para continuar la migración o se eliminará automáticamente durante el proceso de migración.
Calcular | Diagnósticos de inicio con almacenamiento Premium | Deshabilitar la característica de diagnósticos de inicio para las máquinas virtuales antes de continuar con la migración. Puede volver a habilitar diagnósticos de inicio en la pila de administrador de recursos, una vez completada la migración. Además, debe eliminarse BLOB que se utilizan para la captura de pantalla y los registros de la serie por lo que ya no se aplican a esos BLOB.
Calcular | Servicios de nube que contienen funciones web/trabajo | Esto no es compatible actualmente.
Red | Redes virtuales que contienen máquinas virtuales y funciones web/trabajo |  Esto no es compatible actualmente.
Servicio de aplicaciones de Azure | Redes virtuales que contengan entornos de servicio de aplicaciones | Esto no es compatible actualmente.
HDInsight de Azure | Redes virtuales que contienen servicios HDInsight | Esto no es compatible actualmente.
Servicios de ciclo de vida de Microsoft Dynamics | Redes virtuales que contienen máquinas virtuales administrados por servicios de ciclo de vida de Dynamics | Esto no es compatible actualmente.
Calcular | Extensiones de centro de seguridad de Azure con un VNET que tenga una puerta de enlace VPN o puerta de enlace de recuperación de emergencia con el servidor DNS local | Centro de seguridad de Azure instala automáticamente las extensiones en sus máquinas virtuales para supervisar su seguridad y generar alertas. Estas extensiones normalmente se instalan automáticamente si está habilitada la directiva de centro de seguridad de Azure en la suscripción. No se admite actualmente la migración de puerta de enlace y la puerta de enlace debe eliminarse antes de continuar con la confirmación de la migración, el acceso a internet a la cuenta de almacenamiento VM se pierden cuando se elimina la puerta de enlace. La migración no continuará cuando esto ocurre como no se puede llenar el blob de estado de agente de invitado. Se recomienda deshabilitar 3 horas antes de continuar con la migración de directiva de centro de seguridad de Azure en la suscripción.

## <a name="the-migration-experience"></a>La experiencia de migración

Antes de iniciar la experiencia de migración, se recomienda lo siguiente:

- Asegúrese de que los recursos que desea migrar no usar las características no compatibles o configuraciones. Normalmente la plataforma detecta estos problemas y genera un error.
- Si tiene máquinas virtuales que no están en una red virtual, se detiene y desasignar como parte de la operación de preparación. Si no desea perder la dirección IP pública, mire en reservar la dirección IP antes de activar la operación de preparación. Sin embargo, si las máquinas virtuales están en una red virtual, no se detiene y cancela la asignación.
- Planificar la migración no horario laboral para acomodar para cualquier error inesperado que esto suceda durante la migración.
- Descargar la configuración actual de sus máquinas virtuales mediante PowerShell, los comandos de la línea de comandos de interfaz o las API de REST para que sea más fácil de validación después de completar el paso Preparar.
- Actualice las secuencias de comandos de automatización/operationalization para administrar el modelo de implementación de administrador de recursos antes de comenzar la migración. Opcionalmente, puede realizar operaciones de GET cuando los recursos están en estado preparada.
- Evaluar las directivas RBAC que están configuradas en los recursos de IaaS clásicos y plan una vez completada la migración.

El flujo de trabajo de migración es la siguiente

![Captura de pantalla que muestra el flujo de trabajo de migración](./media/virtual-machines-windows-migration-classic-resource-manager/migration-workflow.png)

>[AZURE.NOTE] Todas las operaciones que se describe en las secciones siguientes son idempotentes. Si tiene un problema distinto de una característica no admitida o un error de configuración, se recomienda que vuelva a intentar preparar, anular o confirmar la operación. La plataforma de Windows Azure intenta la acción de nuevo.

### <a name="validate"></a>Validar

La operación de validar es el primer paso del proceso de migración. El objetivo de este paso es analizar datos en el fondo para los recursos en proceso de migración y devolver éxito o falla si los recursos son capaces de migración.

Selecciona una red virtual o el servicio hospedado (si no es una red virtual) que desee validar para la migración.

* Si el recurso no es capaz de migración, la plataforma de Windows Azure enumera todos los motivos de por qué no se admiten para la migración.

### <a name="prepare"></a>Preparar

La operación de preparación es el segundo paso del proceso de migración. El objetivo de este paso es simular la transformación de los recursos de IaaS de clásico a recursos de administrador de recursos y presentar esto en paralelo para que pueda visualizar.

Selecciona una red virtual o el servicio hospedado (si no es una red virtual) que va a prepararse para la migración.

* Si el recurso no es capaz de migración, la plataforma de Windows Azure detiene el proceso de migración y muestra el motivo por qué ha fallado la operación de preparación.
* Si el recurso es capaz de migración, los bloqueos primera plataforma Windows Azure abajo las operaciones de plano de la administración de los recursos en proceso de migración. Por ejemplo, no es posible agregar un disco de datos a una máquina virtual en proceso de migración.

La plataforma de Windows Azure, a continuación, inicia la migración de metadatos de clásico para el Administrador de recursos para los recursos de migración.

Una vez completada la operación de preparación, tiene la opción de visualización de los recursos en ambos clásico y el Administrador de recursos. Para cada servicio de nube en el modelo de implementación clásica, la plataforma de Windows Azure crea un nombre de grupo de recursos que tenga el diseño `cloud-service-name>-migrated`.

>[AZURE.NOTE] Máquinas virtuales de Windows que no están en una red Virtual clásica se detienen manera en esta fase de migración.

### <a name="check-manual-or-scripted"></a>Comprobar (manual o mediante secuencias de comandos)

En el paso de verificación, si lo desea puede usar la configuración que ha descargado anteriormente para validar que la migración es correcta. Como alternativa, se pueden iniciar sesión en el portal y la verificación del lugar las propiedades y los recursos para validar que la migración de metadatos es satisfactorio.

Si va a migrar una red virtual, no se reinicia la configuración de la mayoría de máquinas virtuales. Para las aplicaciones en máquinas virtuales de esas, puede validar que la aplicación está todavía en funcionamiento.

Puede probar la supervisión y automatización y secuencias de comandos operativas para ver si las máquinas virtuales son funciona como se espera y las secuencias de comandos actualizados funcionan correctamente. Operaciones de GET sola son compatibles cuando los recursos están en estado preparada.

No hay ninguna ventana de tiempo de conjunto antes de que necesita confirmar la migración. Puede tardar tanto tiempo como desee en este estado. Sin embargo, el plano de administración está bloqueado para estos recursos hasta que anula o confirma.

Si ve los problemas, siempre puede anular la migración y volver al modelo de implementación clásico. Después de volver atrás, la plataforma de Windows Azure abrirá las operaciones de plano de la administración de los recursos para que puede reanudar las operaciones normales en esas máquinas virtuales en el modelo de implementación clásico.

### <a name="abort"></a>Anular

Anulación es un paso opcional que puede utilizar para revertir los cambios en el modelo de implementación clásica y detener la migración.

>[AZURE.NOTE] No se puede ejecutar esta operación después de que ha activado la operación de confirmación.  

### <a name="commit"></a>Confirmar

Cuando termine la validación, puede confirmar la migración. Recursos no aparecen ya en clásico y sólo están disponibles en el modelo de implementación de administrador de recursos. Los recursos migrados pueden administrarse solo en el nuevo portal.

>[AZURE.NOTE] Se trata de una operación idempotente. Si se produce un error, se recomienda que vuelva a intentar la operación. Si sigue un error, cree una incidencia de soporte técnico o crear una publicación de foro con una etiqueta de ClassicIaaSMigration en nuestro [foro de la máquina virtual](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows).

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**¿Este plan de migración afecta a cualquiera de Mis servicios existentes o las aplicaciones que se ejecutan en máquinas virtuales de Windows Azure?**

No. Las VM (clásicas) son totalmente compatibles servicios de disponibilidad general. Aún puede usar estos recursos para expandir su huella en Microsoft Azure.

**¿Qué sucede con mi máquinas virtuales si no va a migrar en un futuro próximo?**

Nos estamos no dejar obsoletos la API clásicas existentes y el modelo de recursos. Queremos facilitar la migración, teniendo en cuenta las características avanzadas que están disponibles en el modelo de implementación de administrador de recursos. Recomendamos que revise [algunas de las mejoras](virtual-machines-windows-compare-deployment-models.md) que forman parte de IaaS en el Administrador de recursos.

**¿Qué significa este plan de migración para mi herramientas existente?**

Actualizar el conjunto de herramientas para el modelo de implementación de administrador de recursos es uno de los cambios más importantes que tenga en cuenta en sus planes de migración.

**¿Cuánto tiempo estará el tiempo de inactividad de plano de administración?**

Depende del número de recursos que se migran. Para implementaciones más pequeñas (unas decenas de VM), la migración completo debe tener menos de una hora. Para implementaciones a gran escala (cientos de máquinas virtuales), la migración puede tardar unas horas.

**¿Puedo dar después de la migración de recursos asignadas en el Administrador de recursos?**

Puede cancelar la migración, siempre y cuando los recursos están en estado preparada. No se permite deshacer después de que los recursos se han migrado correctamente a través de la operación de confirmación.

**¿Puedo deshacer la migración si se produce un error en la operación de confirmación?**

No se puede anular la migración si se produce un error en la operación de confirmación. Todas las operaciones de migración, incluida la operación de confirmación, sean idempotentes. Por lo tanto, se recomienda que vuelva a intentar la operación poco tiempo después. Si aún se enfrenta a un error, cree una incidencia de soporte técnico o crear una publicación de foro con la etiqueta ClassicIaaSMigration en nuestro [foro de la máquina virtual](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows).

**¿Tengo que comprar otro circuito ruta express si tengo que usar en el Administrador de recursos de IaaS?**

No. Hemos habilitado recientemente [mover circuitos ExpressRoute desde el estándar para el modelo de implementación de administrador de recursos](../expressroute/expressroute-move.md). No tienes que comprar un nuevo circuito ExpressRoute si ya tiene una.

**¿Qué ocurre si había configurado las directivas de Control de acceso basado en roles para Mis recursos de IaaS clásicas?**

Durante la migración, los recursos transforman de clásico para el Administrador de recursos. Por lo tanto, se recomienda que tiene previsto de las actualizaciones de directiva RBAC que deben ocurrir después de la migración.

**¿Qué pasa si estoy usando Azure sitio recuperación o copia de seguridad de Azure hoy?**

Para migrar su máquina Virtual que están habilitados para la copia de seguridad, consulte [he copia de mi VM clásicas en depósito de copia de seguridad. Ahora desea migrar mi máquinas virtuales de modo clásico al modo de administrador de recursos. ¿Cómo puedan copia de seguridad de ellos en depósito de servicios de recuperación?](../backup/backup-azure-backup-ibiza-faq.md#i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode-how-can-i-backup-them-in-recovery-services-vault)

**¿Validar mi suscripción o recursos para ver si es capaces de migración?**

Sí. En la opción compatibles con la plataforma de migración, el primer paso en la preparación para la migración es validar que los recursos son capaces de migración. En caso de que se produce un error en la operación de validación, recibirá mensajes para todas las razones que no se puede completar la migración.

**¿Qué ocurre si se produzcan un error de cuota al preparar los recursos de IaaS para la migración?**

Le recomendamos que anular la migración y, a continuación, inicie una solicitud de soporte para aumentar las cuotas de la región que migra las máquinas virtuales. Después de aprobar la solicitud de cuota, puede empezar a ejecutar los pasos de la migración de nuevo.

**¿Cómo informo de un problema?**

Publique sus problemas y preguntas sobre la migración a nuestro [foro de la máquina virtual](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WAVirtualMachinesforWindows), con la palabra clave ClassicIaaSMigration. Se recomienda exponer una de las preguntas en este foro. Si tiene un contrato de soporte técnico, está Bienvenido a iniciar también una incidencia de soporte técnico.

**¿Qué sucede si no le gusta a los nombres de los recursos de la plataforma elegida durante la migración?**

Todos los recursos que proporcionar explícitamente los nombres en el modelo de implementación clásica se conservan durante la migración. En algunos casos, se crean nuevos recursos. Por ejemplo: se crea una interfaz de red para cada VM. Actualmente no se admite la capacidad de controlar los nombres de estos recursos nuevos creados durante la migración. Inicie sesión en su votos para esta característica en el [foro de comentarios de Azure](http://feedback.azure.com).

* *aparece el mensaje *"VM informa el estado de agente global como no está listo. Por lo tanto, no se pueden migrar la máquina virtual. Asegúrese de que el agente de VM son los informes de estado de agente global como listo"* o *"VM contiene extensión no se notifica cuyo estado de la máquina virtual. Por lo tanto, este VM no se migrará."***

Este mensaje aparece cuando la máquina virtual no tiene conectividad saliente a internet. El agente VM utiliza conectividad saliente para llegar a la cuenta de almacenamiento de Azure para actualizar el estado de un agente cada cinco minutos.


## <a name="next-steps"></a>Pasos siguientes
Ahora que conoce la migración de recursos de IaaS clásicos para el Administrador de recursos, puede comenzar a migrar recursos.

- [Análisis técnico detallado de migración compatibles con la plataforma de clásico para el Administrador de recursos de Azure](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Usar PowerShell para migrar los recursos de IaaS de clásico para el Administrador de recursos de Azure](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Utilizar CLI para migrar recursos de IaaS de clásico para el Administrador de recursos de Azure](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Clonar una máquina virtual clásica para el Administrador de recursos de Azure mediante secuencias de comandos de PowerShell de comunidad](virtual-machines-windows-migration-scripts.md)
