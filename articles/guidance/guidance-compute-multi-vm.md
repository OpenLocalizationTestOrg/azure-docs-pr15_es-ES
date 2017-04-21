<properties
   pageTitle="Ejecución de máquinas virtuales múltiples | Arquitectura de referencia | Microsoft Azure"
   description="Cómo ejecutar varias instancias VM en Azure para escalabilidad, resistencia, administración y seguridad."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/19/2016"
   ms.author="mwasson"/>

# <a name="running-multiple-vms-on-azure-for-scalability-and-availability"></a>Ejecución de varios VM en Azure para escalabilidad y disponibilidad 

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

En este artículo se describe un conjunto de prácticas para ejecutar varias instancias de máquinas virtuales (VM) para mejorar la escalabilidad, disponibilidad, administración y seguridad.   

En esta arquitectura, la carga de trabajo se distribuye entre las instancias de máquina virtual. Hay una sola dirección IP pública y se distribuye el tráfico de Internet a las VM mediante un equilibrador de carga. Esta arquitectura de puede usarse para una aplicación de un solo nivel, como un clúster de aplicación o almacenamiento de web sin estado. También es un bloque de creación de aplicaciones de N niveles. 

En este artículo se basa en que se [ejecuta una única VM en Azure][single vm]. Las recomendaciones en este artículo también se aplicarán a esta arquitectura.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

Máquinas virtuales en Azure requieren los recursos de redes y almacenamiento de soporte.

> Un documento de Visio que incluye este diagrama de arquitectura está disponible para su descarga en el [Centro de descarga de Microsoft][visio-download]. Este diagrama se encuentra en la "cálculo - pestaña VM multi." 

![[0]][0]

La arquitectura tiene los siguientes componentes: 

- **Establece la disponibilidad.** La [disponibilidad de establece] [ availability set] contiene las máquinas virtuales y es necesario para admitir la [disponibilidad SLA para máquinas virtuales de Azure][vm-sla].

- **VNet**. Cada VM en Azure se implementa en una red virtual (VNet) que se divide en **subredes**.

- **Equilibrador de carga de Azure.** El [equilibrador de carga] distribuye las solicitudes entrantes de Internet a las instancias VM en un conjunto de disponibilidad. El equilibrador de carga incluye algunos recursos relacionados:

  - **Dirección IP pública.** Se necesita una dirección IP pública para que el equilibrador de carga recibir el tráfico de Internet.

  - **Configuración de front-end.** Asocia la dirección IP pública del equilibrador de carga.

  - **Grupo de direcciones de back-end.** Contiene las interfaces de red (NIC) para las VM que recibirán el tráfico entrante.

- **Reglas del equilibrador de carga.** Se usa para distribuir el tráfico de red entre las máquinas virtuales en el grupo de direcciones de back-end. 

- **Reglas NAT.** Se usa para enrutar el tráfico a una máquina virtual específica. Por ejemplo, para habilitar el protocolo de escritorio remoto (RDP) a las máquinas virtuales, crear una regla de traducción (NAT) de la dirección de red independiente para cada máquina virtual. 

- **Interfaces de red (NIC)**. Cada máquina virtual tiene una NIC para conectarse a la red.

- **Almacenamiento de información.** Cuentas de almacenamiento mantienen las imágenes de máquina virtual y otros recursos relacionados con el archivo, como los datos de diagnóstico VM capturados, Azure.

## <a name="recommendations"></a>Recomendaciones

Azure ofrece numerosos recursos distintos y tipos de recursos, por lo que puede ser esta arquitectura de referencia se aprovisione varias formas diferentes. Proporcionamos una plantilla de administrador de recursos de Azure para instalar la arquitectura de referencia que sigue las recomendaciones que se describen a continuación. Si elige crear su propia arquitectura de referencia debería seguir estas recomendaciones a menos que tenga un requisito específico que no admite una recomendación. 

### <a name="availability-set-recommendations"></a>Disponibilidad de establece recomendaciones

Debe crear al menos dos máquinas virtuales de la disponibilidad configurada para admitir la [disponibilidad SLA para máquinas virtuales de Azure][vm-sla]. Observe que el equilibrador de carga de Azure también requiere que pertenecen máquinas virtuales de equilibrio de carga en el mismo conjunto de disponibilidad.

### <a name="network-recommendations"></a>Recomendaciones de red

Las VM detrás del equilibrador de carga deben colocarse dentro de la misma subred. No exponen las VM directamente a Internet, pero en su lugar, asigne a cada máquina virtual una dirección IP privada. Se conectan los clientes con la dirección IP pública del equilibrador de carga.

### <a name="load-balancer-recommendations"></a>Recomendaciones de equilibrador de carga

Agregar todas máquinas virtuales de la disponibilidad establecida en el grupo de direcciones de back-end del equilibrador de carga.

Definir reglas de equilibrador de carga para dirigir el tráfico de red a las máquinas virtuales. Por ejemplo, para permitir el tráfico HTTP, cree una regla que asigne el puerto 80 de la configuración de front-end al puerto 80 en el grupo de direcciones de back-end. Cuando el equilibrador de carga, recibe una solicitud en el puerto 80 de la dirección IP pública, enrutamiento la solicitud al puerto 80 en una de las NIC en el grupo de direcciones de back-end.

Definir reglas NAT para enrutar el tráfico a una máquina virtual específica. Por ejemplo, para habilitar RDP a las VM crear una regla NAT independiente para cada máquina virtual. Cada regla debe asignar un número de puerto distinto al puerto 3389, el puerto predeterminado para RDP. (Por ejemplo, utilizan el puerto 50001 para "VM1," puerto 50002 "VM2" y así sucesivamente). Asignar las reglas NAT a las NIC en las máquinas virtuales. 

### <a name="storage-account-recommendations"></a>Recomendaciones de la cuenta de almacenamiento

Crear cuentas de almacenamiento de Azure independiente para cada máquina virtual que contenga el disco duro virtual (VHD), con el fin de evitar el alcance de las operaciones de entrada y salida por segundo [(IOPS) límites] [ vm-disk-limits] para las cuentas de almacenamiento. 

Crear una cuenta de almacenamiento para los registros de diagnóstico. Todas las máquinas virtuales se puede compartir esta cuenta de almacenamiento.

## <a name="scalability-considerations"></a>Consideraciones de escalabilidad

Existen dos opciones para el escalado de máquinas virtuales en Azure: 

- Use un equilibrador de carga para distribuir el tráfico de red entre un conjunto de máquinas virtuales. Para cambiar la escala, aprovisionar máquinas virtuales adicionales y colocarlos detrás del equilibrador de carga. 

- Usar [conjuntos de escala de máquina Virtual][vmss]. Un conjunto de escala contiene un número válido de VM idénticos detrás de un equilibrador de carga. Escala VM establece el ajuste automático de soporte técnico basado en mediciones de rendimiento. A medida que aumenta la carga en las máquinas virtuales, máquinas virtuales adicionales se agregan automáticamente al equilibrador de carga. 

Las siguientes secciones comparan estas dos opciones.

### <a name="load-balancer-without-vm-scale-sets"></a>Equilibrador de carga sin conjuntos de escala VM

Un equilibrador de carga toma las solicitudes entrantes de red y las distribuye entre las NIC en el grupo de direcciones de back-end. Para cambiar la escala horizontalmente, añadir más instancias de máquina virtual para el conjunto de disponibilidad (o desasignar máquinas virtuales para reducir el tamaño). 

Por ejemplo, suponga que está ejecutando un servidor web. Puede agregar una regla de equilibrador de carga para el puerto 80 o puerto 443 (para SSL). Cuando un cliente envía una solicitud HTTP, el equilibrador de carga selecciona una dirección IP de back-end mediante el [algoritmo de hash] [ load balancer hashing] que incluya la dirección IP de origen. En este modo, las solicitudes de cliente se distribuyen en todas las máquinas virtuales. 

> [AZURE.TIP] Cuando se agrega una nueva máquina virtual para una disponibilidad establecer, asegúrese de crear una NIC para la máquina virtual y agregar la NIC al grupo de direcciones de back-end en el equilibrador de carga. En caso contrario, no enrutar el tráfico de Internet a la nueva máquina virtual.

Cada suscripción Azure tiene límites predeterminados en su lugar, incluidos el número máximo de máquinas virtuales por región. Puede aumentar el límite presentando una solicitud de soporte. Para obtener más información, consulte [suscripción Azure y límites de servicio, cuotas y restricciones][subscription-limits].  

### <a name="vm-scale-sets"></a>Conjuntos de escala VM 

Conjuntos de escala VM le ayudan a implementar y administrar un conjunto de máquinas virtuales idénticos. Con todas las máquinas virtuales de la misma configuración, conjuntos de escala VM admiten Autoescala true, sin previo aprovisionamiento VM, lo que facilita la creación de servicios a gran escala destino cálculo grande, datos grandes y contenedores de cargas de trabajo. 

Para obtener más información acerca de los conjuntos de escala VM, vea [Información general conjuntos de escala de máquina Virtual][vmss].

Consideraciones sobre el uso de conjuntos de escala VM:

- Considere la posibilidad de conjuntos de escala si necesita rápidamente escalar máquinas virtuales o necesita Autoescala. 

- Actualmente, los conjuntos de escala no admiten discos de datos. Las opciones para almacenar los datos son Azure almacenamiento de archivos, la unidad de sistema operativo, la unidad Temp o un almacén externo, como el almacenamiento de Azure. 

- Todas las instancias de máquina virtual dentro de una escala de establecer automáticamente pertenecen el mismo conjunto de disponibilidad, con los dominios de error 5 y 5 actualización.

- De forma predeterminada, los conjuntos de escala usan "para otros fines," lo que significa que el conjunto de escala inicialmente disposiciones más máquinas virtuales de solicita y luego elimina las VM adicionales. Esto mejora la velocidad de éxito global cuando las máquinas virtuales de aprovisionamiento. 

- Se recomienda no más después de 20 máquinas virtuales por almacenamiento cuenta con para otros fines habilitadas o no más de 40 máquinas virtuales con para otros fines deshabilitada.  

- Puede encontrar plantillas de administrador de recursos para implementar escala se establece en las [Plantillas de Azure tutorial][vmss-quickstart].

- Hay dos formas básicas para configurar máquinas virtuales implementadas en un conjunto de escala: crear una imagen personalizada o usar extensiones para configurar la máquina virtual después de se aprovisiona.

    - Un conjunto de escala integrado en una imagen personalizada debe crear todos los VHD de disco de sistema operativo dentro de una cuenta de almacenamiento. 

    - Con imágenes personalizadas, debe mantener actualizada la imagen.

    - Con las extensiones, puede tardar más de una máquina virtual recientemente creada poner en marcha.

Para otras consideraciones, vea [Diseñar VM escala conjuntos de escala][vmss-design].

> [AZURE.TIP]  Cuando se utiliza cualquier solución escala automática, pruebe con cargas de trabajo de nivel de producción con antelación. 

## <a name="availability-considerations"></a>Consideraciones de disponibilidad

La disponibilidad conjunto hace que la aplicación más resistentes a planificado y eventos de mantenimiento.

- _Mantenimiento planeado_ se produce cuando Microsoft actualiza la plataforma subyacente, a veces causando VM reiniciar. Azure hace que las máquinas virtuales de un conjunto de disponibilidad no están todos reinicia al mismo tiempo, que se mantiene al menos una ejecución mientras otras personas se reinicia.

- _Mantenimiento planificado_ sucede si hay un error de hardware. Azure se asegura de que las máquinas virtuales de un conjunto de disponibilidad aprovisionadas en bastidor de más de un servidor. Esto ayuda a reducir el impacto de los errores de hardware, interrupciones de red, interrupciones de energía y así sucesivamente.

Para obtener más información, vea [Administrar la disponibilidad de máquinas virtuales][availability set]. El siguiente vídeo también tiene una buena introducción de conjuntos de disponibilidad: [Realice ¿cómo configurar un conjunto de disponibilidad a máquinas virtuales de escala][availability set ch9]. 

> [AZURE.WARNING]  Asegúrese de configurar la disponibilidad establecer al aprovisionar la máquina virtual. Actualmente, no hay ninguna manera de agregar un administrador de recursos de VM a una disponibilidad establecer después de la máquina virtual se aprovisiona.

Equilibrador de carga usa [sondeos de estado] para supervisar la disponibilidad de instancias de máquina virtual. Si un sondeo no puede conectar con una instancia dentro de un período de tiempo de espera, el equilibrador de carga deja de enviar tráfico a máquina. Sin embargo, seguirá el equilibrador de carga de sondeo y si la máquina virtual vuelve a estar disponible, el equilibrador de carga reanuda enviar tráfico a máquina.

Estas son algunas recomendaciones sobre sondeos de estado del equilibrador de carga:

- Pueden probar sondeos HTTP o TCP. Si la ejecución de máquinas virtuales de un servidor HTTP (IIS, nginx, Node.js aplicación y así sucesivamente), crea un sondeo HTTP. En caso contrario, cree un sondeo TCP.

- Para un sondeo HTTP, especifique la ruta de acceso de extremo HTTP. El sondeo comprueba si hay una respuesta HTTP 200 desde esta ruta de acceso. Esto puede ser la ruta de acceso raíz ("/") o un punto final de la supervisión de estado que implementa algunas lógica personalizada para comprobar el estado de la aplicación. El extremo debe permitir las solicitudes HTTP anónimas.

- El sondeo se envía desde un [conocidos] [ health-probe-ip] dirección IP, 168.63.129.16. Asegúrese de que no bloquear el tráfico a o desde esta dirección IP en las directivas de firewall o reglas (GSN) del grupo de seguridad de red.

- Usar [registros de sondeo de estado] [ health probe log] para ver el estado de los sondeos de estado. Habilitar el registro en el portal de Azure para cada equilibrador de carga. Los registros se escriben en el almacenamiento de blobs de Windows Azure. Los registros muestran cuántas VM de fondo no recibe tráfico de red debido a las respuestas de error de sondeo.

## <a name="manageability-considerations"></a>Consideraciones de capacidad de administración

Con varias VM, es importante automatizar procesos, por lo que son predecible y confiable. Puede utilizar la [Automatización de Azure] [ azure-automation] para automatizar la implementación, revisión de sistema operativo y otras tareas. Automatización de Azure es un servicio de automatización que se ejecuta en Azure y se basa en Windows PowerShell. Las secuencias de comandos de automatización de ejemplo están disponibles en la [Galería de Runbook] en TechNet.

## <a name="security-considerations"></a>Consideraciones de seguridad

Redes virtuales son un límite de aislamiento de tráfico en Azure. Máquinas virtuales de una que vnet no se puede comunicar directamente a máquinas virtuales en un VNet diferentes. Máquinas virtuales dentro de la misma VNet puede comunicarse, a menos que cree [grupos de seguridad de red] [ nsg] (NSGs) para restringir el tráfico. Para obtener más información, vea [Servicios de nube de Microsoft y seguridad de la red][network-security].

Para el tráfico de Internet entrante, las reglas de equilibrador de carga definen qué tráfico puede ponerse en contacto el back-end. Sin embargo, no admiten reglas del equilibrador de carga lista blanca IP, por lo que si desea blanca determinada IP pública direcciones, agregar un GSN a la subred.

## <a name="solution-deployment"></a>Implementación de soluciones

Una implementación de una arquitectura de referencia que implementa estas recomendaciones está disponible en GitHub. Esta arquitectura de referencia incluye una red virtual (VNet), grupo de seguridad de la red (GSN), equilibrador de carga y dos máquinas virtuales (VM).

La arquitectura de referencia se puede implementar con Windows o Linux VM siguiendo las instrucciones siguientes: 

1. Haga clic en el botón siguiente y seleccione uno "Abrir vínculo en una nueva pestaña" o "Abrir vínculo en ventana nueva":  
[![Implementar en Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-multi-vm%2Fazuredeploy.json)

2. Una vez que ha abierto el vínculo en el portal de Azure, debe especificar valores para algunas de las opciones: 
    - El nombre del **grupo de recursos** ya está definido en el archivo de parámetros, así que seleccione **Usar existente** y escriba `ra-multi-vm-rg` en el cuadro de texto.
    - Seleccione la región en el cuadro desplegable de **ubicación** .
    - No modifique la **Plantilla raíz Uri** o los cuadros de texto de **Uri raíz del parámetro** .
    - Seleccione el **Tipo de sistema operativo** en la lista desplegable cuadro, **windows** o **linux**. 
    - Revise los términos y condiciones y luego haga clic en la casilla de verificación **que acepto los términos y condiciones indicadas arriba** .
    - Haga clic en el botón de **compra** .

3. Espere a que la implementación completar.

4. Los archivos de parámetro incluyen una contraseña y el nombre de usuario administrador modificable y se recomienda cambiar inmediatamente ambas. Haga clic en la máquina virtual denominada `ra-multi-vm1` en el portal de Azure. A continuación, haga clic en **Restablecer la contraseña** en el módulo de **compatibilidad + solución de problemas** . Seleccione **Restablecer la contraseña** en el cuadro de lista desplegable de **modo** , seleccione un nuevo **nombre de usuario** y **contraseña**. Haga clic en el botón **Actualizar** para guardar el nuevo nombre de usuario y la contraseña. Repita los pasos en la máquina virtual denominada `ra-multi-vm2`.

Para obtener información sobre maneras adicionales para implementar la arquitectura de referencia, vea el archivo Léame de la [orientación de la vm solo] [ github-folder] GitHub carpeta. 

## <a name="next-steps"></a>Pasos siguientes

Colocar varias máquinas virtuales detrás de un equilibrador de carga es un bloque de creación para crear arquitecturas de varios niveles. Para obtener más información, vea [Ejecutar máquinas virtuales de Windows para una arquitectura de N niveles en Azure] [ n-tier-windows] y [Ejecuta máquinas virtuales de Linux para una arquitectura de N niveles en Azure][n-tier-linux]

<!-- Links -->
[availability set]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[availability set ch9]: https://channel9.msdn.com/Series/Microsoft-Azure-Fundamentals-Virtual-Machines/08
[azure-automation]: https://azure.microsoft.com/en-us/documentation/services/automation/
[azure-cli]: ../virtual-machines-command-line-tools.md
[bastion host]: https://en.wikipedia.org/wiki/Bastion_host
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-multi-vm
[health probe log]: ../load-balancer/load-balancer-monitor-log.md
[comprobaciones de estado]: ../load-balancer/load-balancer-overview.md#service-monitoring
[health-probe-ip]: ../virtual-network/virtual-networks-nsg.md#special-rules
[equilibrador de carga]: ../load-balancer/load-balancer-get-started-internet-arm-cli.md
[load balancer hashing]: ../load-balancer/load-balancer-overview.md#hash-based-distribution
[n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[n-tier-windows]: guidance-compute-n-tier-vm.md
[naming conventions]: guidance-naming-conventions.md
[network-security]: ../best-practices-network-security.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md 
[Galería de runbook]: ../automation/automation-runbook-gallery.md#runbooks-in-runbook-gallery
[single vm]: guidance-compute-single-vm.md
[subscription-limits]: ../azure-subscription-service-limits.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_2/
[vmss]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md
[vmss-design]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md
[vmss-quickstart]: https://azure.microsoft.com/documentation/templates/?term=scale+set
[VM-sizes]: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
[0]: ./media/blueprints/compute-multi-vm.png "Arquitectura de una solución entre varios VM en Azure que incluya una disponibilidad configurado con dos máquinas virtuales y un equilibrador de carga"
