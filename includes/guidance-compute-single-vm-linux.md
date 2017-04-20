En este artículo se describe un conjunto de prácticas para ejecutar una máquina virtual (VM) de Linux en Azure, preste atención a escalabilidad, disponibilidad, administración y seguridad. Azure permite realizar varias distribuciones Linux populares, incluidos CentOS, Debian, rojo sombrero Enterprise, Ubuntu y FreeBSD. Para obtener más información, consulte [Azure y Linux][azure-linux].

> [AZURE.NOTE] Azure tiene dos diferentes modelos de implementación: [El Administrador de recursos] [ resource-manager-overview] y clásica. Este artículo, se utiliza el Administrador de recursos, que Microsoft recomienda para implementaciones nuevas.

No se recomienda usar una máquina virtual única para cargas de trabajo de producción, porque no hay ningún contrato de nivel de tiempo de servicio (SLA) para máquinas virtuales solo en Azure. Para obtener el SLA, debe implementar varias máquinas virtuales de un [conjunto de disponibilidad][availability-set]. Para obtener más información, consulte [ejecutar varios VM en Azure][multi-vm]. 

## <a name="architecture-diagram"></a>Diagrama de arquitectura

Aprovisionamiento de una máquina virtual en Azure implica partes móviles más que simplemente la VM propiamente dicho. Hay un proceso, redes y almacenamiento de los elementos que debe tener en cuenta.

> Un documento de Visio que incluye este diagrama de arquitectura está disponible para su descarga en el [Centro de descarga de Microsoft][visio-download]. Este diagrama se encuentra en la "- solo máquina virtual de cálculo" página.

![[0]][0]

- **Grupo de recursos.** Un [_grupo de recursos_] [ resource-manager-overview] es un contenedor que contiene los recursos relacionados. Crear un grupo de recursos para mantener los recursos para esta máquina virtual.

- **VM**. Puede proporcionar una máquina virtual de una lista de imágenes publicadas o desde un archivo de disco duro virtual (VHD) que cargar en el almacenamiento de blobs de Windows Azure.

- **Disco de sistema operativo.** El disco de sistema operativo es un disco duro virtual almacenado en el [almacenamiento de Azure][azure-storage]. Esto significa que persiste aunque el equipo host deja de funcionar. El disco de sistema operativo es `/dev/sda1`.

- **Disco temporal.** Se crea la máquina virtual con un disco temporal. Este disco se almacena en una unidad física en el equipo host. _No_ es guardado en el almacenamiento de Azure y pueden desaparecer durante reiniciar el equipo y otros eventos de ciclo de vida de máquina virtual. Use este disco sólo para datos temporales, como archivos de página o intercambiar. El disco temporal es `/dev/sdb1` y montaje en `/mnt/resource` o `/mnt`.

- **Discos de datos.** Un [disco de datos] [ data-disk] es un disco duro de virtual persistente utilizada para los datos de aplicación. Discos de datos se almacenan en el almacenamiento de Azure, como el disco de sistema operativo.

- **Red virtual (VNet) y subred.** Cada VM en Azure se implementa en (VNet), que tiene más dividido en subredes.

- **Dirección IP pública.** Se necesita una dirección IP pública para comunicarse con la máquina virtual&mdash;por ejemplo sobre SSH.

- **Interfaz de red (NIC)**. La NIC permite la máquina virtual para comunicarse con la red virtual.

- **Grupo de seguridad de la red (GSN)**. La [GSN] [ nsg] se usa para permitir o denegar el tráfico de red a la subred. Puede asociar una GSN con una NIC individual o con una subred. Si se asocia a una subred, se aplican las reglas de GSN a todas las máquinas virtuales de esa subred.
 
- **Diagnósticos.** Registro de diagnóstico es fundamental para administrar y solucionar problemas de la máquina virtual.

## <a name="recommendations"></a>Recomendaciones

Azure ofrece numerosos recursos distintos y tipos de recursos, por lo que puede ser esta arquitectura de referencia se aprovisione varias formas diferentes. Proporcionamos una plantilla de administrador de recursos de Azure para instalar la arquitectura de referencia que sigue estas recomendaciones. Si elige crear su propia arquitectura de referencia debería seguir estas recomendaciones a menos que tenga un requisito específico que no se ajusta una recomendación. 

### <a name="vm-recommendations"></a>Recomendaciones de VM

Se recomienda la serie DS y GS porque estos tamaños de máquina admiten el [Almacenamiento de Premium][premium-storage]. Seleccione uno de estos tamaños de equipo a menos que tenga una carga de trabajo especializada como informática de alto rendimiento. Para obtener más información, vea [tamaños de máquina Virtual][virtual-machine-sizes]. Al mover una carga de trabajo existente a Azure, comience con el tamaño de la memoria virtual que se parezca a los servidores local. A continuación, medir el rendimiento de la carga de trabajo real sobre la CPU, memoria y disco entrada y salida de operaciones por segundo (IOPS) y ajustar el tamaño, si es necesario. Además, si necesita varias NIC, tenga en cuenta el límite de NIC para cada tamaño.  

Cuando se aprovisiona la máquina virtual y otros recursos, debe especificar una ubicación. En general, elija una ubicación más cercana a los usuarios internos o clientes. Sin embargo, no todos los tamaños VM esté disponibles en todas las ubicaciones. Para obtener más información, vea [servicios por región][services-by-region]. Para mostrar los tamaños VM disponibles en una ubicación concreta, ejecute el siguiente comando de Azure de línea de comandos interfaz:

```
    azure vm sizes --location <location>
```

Para obtener información sobre cómo elegir una imagen de máquina virtual publicada, vea [navegar y seleccionar máquina virtual Azure imágenes][select-vm-image].

### <a name="disk-and-storage-recommendations"></a>Recomendaciones de disco y almacenamiento

Para mejorar el rendimiento de i/OS de disco, se recomienda [Premium almacenamiento][premium-storage], que almacena los datos en unidades de estado sólido (SSD). Costo se basa en el tamaño del disco aprovisiona. IOPS y rendimiento (es decir, velocidad de transferencia) también dependen en tamaño de disco, así que cuando se aprovisiona un disco, considere la posibilidad de tres factores (rendimiento, IOPS y capacidad). 

Una cuenta de almacenamiento puede admitir máquinas virtuales de 1 a 20.

Agregar uno o más discos de datos. Cuando se crea un disco duro virtual, no tiene formato. Inicie sesión en la máquina virtual para formatearlo. Mostrar los discos de datos como `/dev/sdc`, `/dev/sdd`y así sucesivamente. Puede ejecutar `lsblk` a los dispositivos de bloque, incluidos los discos de la lista. Para usar un disco de datos, cree un partición y sistema de archivos y montaje el disco. Por ejemplo:

```bat
    # Create a partition.
    sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     
    
    # Create a file system.
    sudo mkfs -t ext3 /dev/sdc1
    
    # Mount the drive.
    sudo mkdir /data1
    sudo mount /dev/sdc1 /data1
```

Si tiene muchos de los discos de datos, tenga en cuenta los límites de i/OS totales de la cuenta de almacenamiento. Para obtener más información, consulte [Límites de máquina Virtual disco][vm-disk-limits].

Cuando agrega un disco de datos, se asigna un ID (LUN) número de unidad lógica en el disco. Opcionalmente, puede especificar el identificador de LUN &mdash; por ejemplo, si está reemplazando un disco y desea conservar el mismo ID de LUN, o si tiene una aplicación que busca un identificador de LUN específico. Sin embargo, recuerde que los identificadores de LUN debe ser únicas para cada disco.

Desea cambiar el programador i/OS para optimizar el rendimiento en unidades de estado sólido (SSD) (usa almacenamiento Premium). Una recomendación común es usar al programador NOOP para SSDs, pero debe usar una herramienta como [iostat] para supervisar el rendimiento de disco para la carga de trabajo determinado.

- Para mejorar el rendimiento, cree una cuenta de almacenamiento independiente para mantener registros de diagnóstico. Una cuenta de almacenamiento redundante local (LRS) estándar es suficiente para registros de diagnóstico.


### <a name="network-recommendations"></a>Recomendaciones de red

La dirección IP pública puede ser dinámico o estático. El valor predeterminado es dinámico.

- Reservar una [dirección IP estática] [ static-ip] si necesita una dirección IP fija que no cambia &mdash; por ejemplo, si tiene que crear un registro en DNS o necesita la dirección IP que para estar en la lista blanca.

- También puede crear un nombre de dominio completo (FQDN) para la dirección IP. A continuación, puede registrar un [registro CNAME] [ cname-record] en DNS que apunte al FQDN. Para obtener más información, vea [crear un nombre de dominio completo en el portal de Azure][fqdn].

Todos los NSGs contienen un conjunto de [reglas predeterminadas][nsg-default-rules], incluyendo una regla que bloquea todo el tráfico de Internet entrante. No se puede eliminar las reglas de forma predeterminada, pero pueden reemplazar otras reglas. Para habilitar el tráfico de Internet, crear reglas para permitir el tráfico entrante a puertos específicos &mdash; por ejemplo, el puerto 80 para HTTP.  

Para habilitar SSH, agregar una regla a la GSN que permite el tráfico entrante al puerto TCP 22.

## <a name="scalability-considerations"></a>Consideraciones de escalabilidad

Puede escalar hacia arriba o hacia abajo una máquina virtual cambiando [el tamaño de la máquina virtual][vm-resize]. 

Para ampliar horizontalmente, coloque dos o más máquinas virtuales en una disponibilidad establecer detrás de un equilibrador de carga. Para obtener información detallada, vea [ejecutar varios VM en Azure][multi-vm].

## <a name="availability-considerations"></a>Consideraciones de disponibilidad

Como se mencionó anteriormente, no hay ningún SLA para una única VM. Para obtener el SLA, debe implementar varias VM en un conjunto de disponibilidad.

La máquina virtual puede verse afectada por [mantenimiento planeado] [ planned-maintenance] o [mantenimiento planificado][manage-vm-availability]. Puede usar [VM reinicie registros] [ reboot-logs] para determinar si se debe reiniciar VM por mantenimiento planeado.

VHD se copia por [El almacenamiento de Azure][azure-storage], que se duplica para disponibilidad y la duración.

Para proteger contra la pérdida de datos accidental durante las operaciones normales (por ejemplo, debido a errores de los usuarios), también debe implementar copias de seguridad en un momento, mediante [instantáneas blob] [ blob-snapshot] u otra herramienta.

## <a name="manageability-considerations"></a>Consideraciones de capacidad de administración

**Grupos de recursos.** Poner acoplados recursos que comparten el mismo ciclo de vida en el mismo [grupo de recursos][resource-manager-overview]. Grupos de recursos permiten implementar y supervisar los recursos como un grupo y resumir facturación costos por grupo de recursos. También puede eliminar recursos como un conjunto, que es muy útil para implementaciones de prueba. Asignar recursos a un nombre descriptivo. Que facilita encontrar un recurso específico y entender su función. Vea [recomendados convenciones de nomenclatura para los recursos de Azure][naming conventions].

**SSH**. Antes de crear una VM Linux, generar un par de claves de 2048 bits RSA pública y privada. Use el archivo de clave pública cuando se crea la máquina virtual. Para obtener más información, vea [cómo usar SSH con Linux y Mac en Azure][ssh-linux].

**Diagnósticos de máquina virtual.** Habilitar supervisión y diagnóstico, incluidos métricas básicas de mantenimiento, registros de diagnósticos de infraestructura y [Diagnósticos de arranque][boot-diagnostics]. Diagnósticos de arranque pueden ayudarle a diagnosticar los errores de inicio si obtiene su máquina virtual en un estado no sea de inicio. Para obtener más información, vea [Habilitar la supervisión y diagnósticos de][enable-monitoring].  

El siguiente comando CLI habilita los diagnósticos:

```
    azure vm enable-diag <resource-group> <vm-name>
```

**Detener una máquina virtual.** Azure distingue entre Estados "Detenido" y "Deallocated". Se cargan cuando el estado de la máquina virtual es "detenido". No se cargan cuando se cancela la asignación de la máquina virtual.

Use el siguiente comando CLI desasignar una máquina virtual:

```
    azure vm deallocate <resource-group> <vm-name>
```

- El botón **Detener** en el portal de Azure también cancela la asignación de la máquina virtual. Sin embargo, si apaga mediante el sistema operativo mientras ha iniciado sesión, se detiene la máquina virtual pero _no_ cancela la asignación, así que aún se cargará.

**Eliminar una máquina virtual.** Si elimina una máquina virtual, no se eliminan los VHD. Esto significa que puede eliminar la máquina virtual sin perder los datos. Sin embargo, se siguen cargará para el almacenamiento. Para eliminar el disco duro virtual, elimine el archivo de [almacenamiento de blobs][blob-storage].

Para evitar el borrado accidental, use un [bloqueo de recurso] [ resource-lock] bloquear el recurso completo grupo o bloquear recursos individuales, como la máquina virtual. 

## <a name="security-considerations"></a>Consideraciones de seguridad

Automatizar actualizaciones del sistema operativo mediante la extensión VM [OSPatching] . Instalar esta extensión cuando aprovisionar la máquina virtual. Puede especificar la frecuencia con que instalar revisiones y si desea reiniciar después de la revisión.

Usar el [control de acceso basado en roles] [ rbac] (RBAC) para controlar el acceso a los recursos de Azure que implementa. RBAC le permite asignar roles de autorización a los miembros del equipo DevOps. Por ejemplo, la función lector puede ver los recursos de Azure pero no crear, administrar o eliminarlos. Algunas funciones son específicas de tipos de recursos de Azure determinado. Por ejemplo, el rol de colaborador de máquina Virtual puede reiniciar o desasignar una máquina virtual, restablecer la contraseña de administrador, crear una máquina virtual y así sucesivamente. Otras [funciones integradas de RBAC] [ rbac-roles] que puede resultar útil para esta arquitectura de referencia incluyen [DevTest prácticas usuario] [ rbac-devtest] y [Colaborador de red][rbac-network]. 

Un usuario se puede asignar a varias funciones y puede crear funciones personalizadas para aún más los permisos específicos.

> [AZURE.NOTE] RBAC no limita las acciones que puede realizar un usuario conectado a una máquina virtual. Los permisos se determinan mediante el tipo de cuenta en el sistema operativo de invitado.   

Usar [registros de auditoría] [ audit-logs] para ver el aprovisionamiento de acciones y otros eventos de máquina virtual.

- Considere la posibilidad de [Cifrado de disco de Azure] [ disk-encryption] si necesita cifrar los discos de sistema operativo y los datos. 

## <a name="solution-deployment"></a>Implementación de soluciones

Una [implementación] [ github-folder] para una referencia de arquitectura que muestra estos procedimientos recomendados está disponible. Esta arquitectura de referencia incluye una red virtual (VNet), grupo de seguridad de la red (GSN) y una sola máquina virtual (VM).

Existen varias formas de implementar la arquitectura de referencia. La manera más sencilla es seguir los pasos siguientes: 

1. Haga clic con el botón secundario del mouse en el botón de abajo y seleccione uno "Abrir vínculo en una nueva pestaña" o "Abrir vínculo en ventana nueva".
[![Implementar en Azure](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)

2. Una vez que ha abierto el vínculo en el portal de Azure, debe especificar valores para algunas de las opciones: 
    - El nombre del **grupo de recursos** ya está definido en el archivo de parámetros, así que seleccione **Crear nuevo** y escriba `ra-single-vm-rg` en el cuadro de texto.
    - Seleccione la región en el cuadro desplegable de **ubicación** .
    - No modifique la **Plantilla raíz Uri** o los cuadros de texto de **Uri raíz del parámetro** .
    - Seleccione el **Tipo de sistema operativo** en el cuadro de lista desplegable, **linux**.
    - Revise los términos y condiciones y luego haga clic en la casilla de verificación **que acepto los términos y condiciones indicadas arriba** .
    - Haga clic en el botón de **compra** .

3. Espere a que la implementación completar.

4. Los archivos de parámetros incluyen un nombre de usuario administrador modificable y una contraseña, y se recomienda cambiar inmediatamente ambas. Haga clic en la máquina virtual denominada `ra-single-vm0 `en el Portal de Azure. Haga clic en **Restablecer la contraseña** en la sección de **soporte técnico + solución de problemas** . Seleccione **Restablecer la contraseña** en el cuadro de lista desplegable de **modo** , seleccione un nuevo **nombre de usuario** y **contraseña**. Haga clic en el botón **Actualizar** para conservar el nuevo nombre de usuario y la contraseña.

Para obtener información sobre maneras adicionales para implementar la arquitectura de referencia, vea el archivo Léame de la [orientación de la vm solo] [ github-folder] Github carpeta.

## <a name="customize-the-deployment"></a>Personalizar la implementación

Para cambiar la implementación para satisfacer sus necesidades, siga las instrucciones de la [Guía de la vm solo] [ github-folder] página.

## <a name="next-steps"></a>Pasos siguientes

En orden para el [SLA para máquinas virtuales] [ vm-sla] para aplicar, debe implementar dos o más instancias de un conjunto de disponibilidad. Para obtener más información, consulte [ejecutar varios VM en Azure][multi-vm].

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: ../articles/virtual-machines/virtual-machines-windows-create-availability-set.md
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-linux]: ../articles/virtual-machines/virtual-machines-linux-azure-overview.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-linux-portal-create-fqdn.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[select-vm-image]: ../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssh-linux]: ../articles/virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-linux-sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_0/
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[components]: #Solution-components
[blocks]: https://github.com/mspnp/template-building-blocks
[0]: ./media/guidance-blueprints/compute-single-vm.png "Una sola arquitectura de Linux VM en Azure"

