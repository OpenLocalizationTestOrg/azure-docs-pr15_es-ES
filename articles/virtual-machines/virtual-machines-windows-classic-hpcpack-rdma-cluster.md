<properties
 pageTitle="Configurar un clúster de Windows RDMA para ejecutar aplicaciones de MPI | Microsoft Azure"
 description="Obtenga información sobre cómo crear un clúster de Windows HPC Pack con tamaño H16r, H16mr, A8 o máquinas virtuales A9 usar la red de Azure RDMA para ejecutar aplicaciones MPI."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/20/2016"
 ms.author="danlep"/>

# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Configurar un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones de MPI

Configurar un clúster RDMA de Windows Azure con [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) y las [instancias de una serie de serie de H o de cálculo](virtual-machines-windows-a8-a9-a10-a11-specs.md) para ejecutar aplicaciones de interfaz de paso de mensajes (MPI) paralelas. Al configurar RDMA capaz, basado en Windows Server nodos en un clúster de HPC Pack, aplicaciones de MPI eficazmente comunican a través de un nivel de latencia baja de red de alto rendimiento en Azure que se basa en la tecnología de access (RDMA) remoto directa de memoria.

Si desea ejecutar cargas de trabajo MPI en máquinas virtuales de Linux que tienen acceso a la red de Azure RDMA, consulte [configurar un clúster de Linux RDMA para ejecutar aplicaciones de MPI](virtual-machines-linux-classic-rdma-cluster.md).


## <a name="hpc-pack-cluster-deployment-options"></a>Opciones de implementación de clúster de HPC Pack
Microsoft HPC Pack es una herramienta siempre sin costo adicional para crear HPC clústeres local o en Azure para ejecutar Windows o Linux HPC aplicaciones. Paquete de HPC incluye un entorno de tiempo de ejecución de la implementación de Microsoft del mensaje pasando interfaz para Windows (MS-MPI). Cuando se utiliza con instancias RDMA capaz ejecuta un sistema operativo compatible de Windows Server, HPC Pack proporciona una opción eficaz para ejecutar aplicaciones de Windows MPI que tienen acceso a la red de Azure RDMA. 

Este artículo presenta dos escenarios y vínculos a instrucciones detalladas para configurar un clúster RDMA Winodws con Microsoft HPC Pack. 

* Escenario 1. Implementar instancias de la función de trabajo de descarga intensiva (PaaS)

* Escenario 2. Implementar los nodos de cálculo en máquinas virtuales de descarga intensiva (IaaS)

Para que los requisitos previos usar instancias de descarga intensiva con Windows, consulte [acerca de la serie de H y descarga intensiva máquinas virtuales de serie](virtual-machines-windows-a8-a9-a10-a11-specs.md) .



## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>Escenario 1. Implementar instancias de la función de trabajo de descarga intensiva (PaaS)

Desde un clúster de HPC Pack existente, agregar recursos de cálculo adicional en instancias de la función de trabajo Azure (nodos de Azure) ejecuta en un servicio de nube (PaaS). Esta característica, también llamada "ráfaga en Azure" de HPC Pack, es compatible con una gama de tamaños de las instancias de la función de trabajo. Al agregar los nodos de Azure, solo tiene que especificar uno de los tamaños RDMA capaz.

Siguientes son consideraciones y pasos para a RDMA capaz de ráfaga Azure instancias de una existente (normalmente local) clúster. Usar procedimientos similares para agregar instancias de la función de trabajo a un nodo principal de HPC Pack se implementa en una máquina virtual de Azure.

>[AZURE.NOTE] Para que obtener un tutorial de ráfaga en Azure con HPC Pack, consulte [configurar un clúster híbrido con HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Tenga en cuenta las consideraciones de los pasos que se aplican específicamente a RDMA capaz nodos de Azure.

![Ráfaga en Azure][burst]

### <a name="steps"></a>Pasos

4. **Implementar y configurar un nodo principal de HPC Pack 2012 R2**

    Descargar el paquete de instalación de HPC Pack más reciente desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=49922). Para que requisitos e instrucciones para prepararse para una implementación de Azure ráfaga, consulte [HPC Pack Guía de introducción](https://technet.microsoft.com/library/jj884144.aspx) y [ráfaga a Azure instancias de trabajo con Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

5. **Configurar un certificado de administración de la suscripción de Azure**

    Configurar un certificado para proteger la conexión entre el nodo principal y Azure. Procedimientos y las opciones, consulte [escenarios para configurar el certificado de administración de Azure HPC Pack](http://technet.microsoft.com/library/gg481759.aspx). Para implementaciones de prueba, HPC Pack instala un predeterminado Microsoft HPC Azure administración certificado puede cargar rápidamente a su suscripción de Azure.

6. **Crear un nuevo servicio de nube y una cuenta de almacenamiento**

    Usar el portal de clásico Azure para crear un servicio de nube y una cuenta de almacenamiento para la implementación en una región donde están disponibles las instancias RDMA capaz.

7. **Crear una plantilla de nodo de Azure**

    Usar el nodo Asistente Crear plantilla en el Administrador de clúster de HPC. Para conocer los pasos, consulte [crear una plantilla de nodo de Azure](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) en "Pasos para implementar Azure nodos con Microsoft HPC Pack".

    Para las pruebas iniciales, le recomendamos que la configuración de una directiva de disponibilidad manual en la plantilla.

8. **Agregar nodos al clúster**

    Usar el Asistente para agregar nodos en el Administrador de clúster de HPC. Para obtener más información, vea [Agregar nodos de Azure en el clúster de HPC de Windows](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).

    Al especificar el tamaño de los nodos, seleccione uno de los tamaños de instancia RDMA capaz.
    
    >[AZURE.NOTE]En cada ráfaga a la implementación de Azure con las instancias de descarga intensiva, HPC Pack implementa automáticamente un mínimo de instancias de RDMA capaz de 2 (como A8) como nodos de proxy, además de las instancias de la función de trabajador de Azure que especifique. Los nodos de proxy utilizan núcleos que están asignados a la suscripción y provocar cargos junto con las instancias de la función de trabajador de Azure.

9. **Iniciar (aprovisionar) los nodos y llevarlos en línea para ejecutar trabajos**

    Seleccione los nodos y usar la acción **Iniciar** en el Administrador de clúster de HPC. Cuando termine de aprovisionamiento, seleccione los nodos y usar la acción de **Poner en conexión** en el Administrador de clúster de HPC. Los nodos están listos para ejecutar trabajos.

10. **Enviar trabajos al clúster**

    Usar herramientas de presentación de trabajo de paquete HPC para ejecutar trabajos de clúster. Consulte [Microsoft HPC Pack: gestión](http://technet.microsoft.com/library/jj899585.aspx).

11. **Detener (deprovision) los nodos**

    Cuando haya terminado de tareas en curso, poner los nodos sin conexión y usar la acción **Detener** en el Administrador de clúster de HPC.





## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>Escenario 2. Implementar los nodos de cálculo en máquinas virtuales de descarga intensiva (IaaS)

En este escenario, implementar el nodo principal de HPC Pack y clúster de cálculo nodos en máquinas virtuales de unirse a un dominio de Active Directory en una red virtual Azure. Paquete de HPC proporciona varias [Opciones de implementación en máquinas virtuales de Azure](virtual-machines-linux-hpcpack-cluster-options.md), incluidas las secuencias de comandos de implementación automatizada y tutorial Azure. Por ejemplo, las consideraciones y los pasos siguientes guían utilizar el [script de implementación de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para automatizar la mayor parte de este proceso.

![Clúster en máquinas virtuales de Azure][iaas]



### <a name="steps"></a>Pasos

1. **Crear un nodo de clúster principal y calcular máquinas virtuales de nodo ejecutando el script de implementación de HPC Pack IaaS en un equipo cliente**

    Descargar el paquete de Script de implementación de HPC Pack IaaS desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=49922).

    Para preparar el equipo cliente, crear el archivo de configuración de secuencia de comandos y ejecute la secuencia de comandos, consulte [crear un clúster de HPC con el script de implementación de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md). 
    
    Para implementar los nodos RDMA capaz de cálculo, tenga en cuenta las consideraciones adicionales siguientes:
    
    * **Red Virtual** - especificar una nueva red virtual en una región en la que está disponible el tamaño RDMA capaz de instancia que desea usar.

    * **Sistema operativo Windows Server** - soporte RDMA conectividad, especifique un sistema operativo Windows Server 2012 R2 o Windows Server 2012 para el nodo de cálculo máquinas virtuales.

    * **Servicios de nube** : se recomienda implementar el nodo principal en servicio de uno nube y los nodos de cálculo en un servicio de nube diferente.

    * **Tamaño de nodo de cabeza** - en este escenario, considere la posibilidad de un tamaño de al menos A4 (Extra grande) para el nodo principal.

    * **Extensión de HpcVmDrivers** - el script de implementación se instala el agente de Azure VM y la extensión HpcVmDrivers automáticamente cuando se implementa tamaño A8 o A9 nodos con un sistema operativo Windows Server de cálculo. HpcVmDrivers instala a controladores en el nodo de cálculo máquinas virtuales para que pueden conectarse a la red RDMA.

    * **Configuración de la red de clúster** : el script de implementación configurará automáticamente el clúster HPC Pack topología 5 (todos los nodos en la red empresarial). Esta topología es necesaria para las implementaciones de clúster HPC Pack en máquinas virtuales. No cambiar la topología de red de clúster más adelante.

2. **Poner en línea los nodos de cálculo para ejecutar trabajos**

    Seleccione los nodos y usar la acción de **Poner en conexión** en el Administrador de clúster de HPC. Los nodos están listos para ejecutar trabajos.

3. **Enviar trabajos al clúster**

    Conectar con el nodo principal enviar trabajos o configurar un equipo local para realizar esta acción. Para obtener más información, vea [Enviar trabajos a un clúster HPC en Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).

4. **Los nodos se desconecta y dejar (desasignar) ellos**

    Cuando haya terminado la ejecución de trabajos, tomar los nodos sin conexión en el Administrador de clúster de HPC. A continuación, use las herramientas de administración de Azure ellos apagar.



## <a name="run-mpi-applications-on-the-cluster"></a>Ejecutar aplicaciones de MPI en el clúster

### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Ejemplo: Ejecutar mpipingpong en un clúster de HPC Pack

Para comprobar una implementación de paquete HPC de las instancias RDMA capaz, ejecute el comando de **mpipingpong** HPC Pack en el clúster. **MPIPingPong** envía paquetes de datos entre los nodos parejas varias veces para calcular la latencia y medidas de rendimiento y las estadísticas de la red de la aplicación RDMA habilitada. Este ejemplo muestra un patrón típico para ejecutar un trabajo MPI (en este caso, **mpipingpong**) mediante el comando **mpiexec** de clúster.

Este ejemplo se supone que agregó Azure nodos en una configuración de "ráfaga en Azure" ([escenario 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-(PaaS) in this article). Si implementa HPC Pack en un clúster de máquinas virtuales de Azure, debe modificar la sintaxis de comando para especificar un grupo de nodo diferente y establecer variables de entorno adicionales para dirigir el tráfico de red a la red RDMA.


Para ejecutar mpipingpong en el clúster:


1. En el nodo principal o en un equipo cliente configurada correctamente, abra un símbolo del sistema.

2. Para calcular la latencia entre pares de nodos en una implementación de Azure ráfaga de 4 nodos, escriba el comando siguiente para enviar un trabajo para ejecutar mpipingpong con un tamaño de paquete pequeño y un gran número de iteraciones:

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```

    El comando devuelve el identificador de la tarea que se ha enviado.

    Si implementa el clúster HPC Pack implementado en máquinas virtuales de Azure, especifique un grupo de nodo que contiene máquinas virtuales implementadas en un servicio de nube solo el nodo de cálculo y modifique el comando **mpiexec** como sigue:

    ```
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```

3. Cuando finalice el trabajo, escriba lo siguiente para ver el resultado (en este caso, el resultado de la tarea 1 del trabajo)

    ```
    task view <JobID>.1
    ```

    donde &lt; *ID* &gt; es el identificador de la tarea que se ha enviado.

    El resultado incluirá resultados de latencia similares al siguiente.

    ![Latencia de pong ping][pingpong1]

4. Para calcular el rendimiento entre pares de nodos de ráfaga Azure, escriba el comando siguiente para enviar un trabajo para ejecutar **mpipingpong** con un tamaño de paquete grande y un pequeño número de iteraciones:

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```

    El comando devuelve el identificador de la tarea que se ha enviado.

    En un clúster de HPC Pack implementado en máquinas virtuales de Azure, modifique el comando como se indica en el paso 2.

5. Cuando finalice el trabajo, para ver el resultado (en este caso, el resultado de la tarea 1 del trabajo), escriba lo siguiente:

    ```
    task view <JobID>.1
    ```

  El resultado incluirá resultados de rendimiento similares al siguiente.

  ![Rendimiento de pong ping][pingpong2]


### <a name="mpi-application-considerations"></a>Consideraciones de la aplicación de MPI


Siguientes son consideraciones para ejecutar aplicaciones de MPI con HPC Pack en Azure. Algunas sólo se aplican a las implementaciones de Azure nodos (instancias de rol de trabajo agregados en una configuración de "ráfaga en Azure").

* Instancias de la función de trabajo en un servicio de nube periódicamente se reaprovisionarla sin previo aviso por Azure (por ejemplo, para el mantenimiento del sistema, o en caso de que errores en una instancia). Si una instancia se reaprovisionarla mientras se ejecuta un trabajo MPI, la instancia pierde sus datos y devuelve el estado al que se implementa por primera vez, lo que puede hacer el trabajo MPI errores. Los nodos de más que use para un único trabajo MPI y más el trabajo se ejecuta, más probable es que una de las instancias se reaprovisionarla mientras un trabajo se está ejecutando. También debe considerar si se designa un único nodo de la implementación como un servidor de archivos.


* Para ejecutar trabajos MPI en Azure, no debe usar las instancias RDMA capaz. Puede usar cualquier tamaño de la instancia es compatible con el paquete de HPC. Sin embargo, se recomiendan las instancias RDMA capaz para ejecutar trabajos MPI relativamente a gran escala que dependen de la latencia y el ancho de banda de la red que conecta los nodos. Si utiliza otros tamaños para ejecutar trabajos MPI dependiente de la latencia y el ancho de banda, se recomienda ejecutar trabajos pequeños, en el que se ejecuta una sola tarea en sólo algunos nodos.

* Aplicaciones que se implementa en Azure instancias son sujeto a los términos de licencia asociados a la aplicación. Póngase en contacto con el proveedor de cualquier aplicación comercial de restricciones de licencia o de otras para ejecutar en la nube. No todos los proveedores ofrecen licencias de pago.


* Instancias de Azure necesitan una configuración más nodos de acceso local, recursos compartidos y servidores de licencias. Por ejemplo, para habilitar los nodos Azure tener acceso a un servidor de licencias local, puede configurar una red virtual de sitio a sitio Azure.


* Para ejecutar aplicaciones de MPI en instancias de Azure, registrar cada aplicación MPI con Firewall de Windows en las instancias de ejecutar el comando **hpcfwutil** . Esto permite MPI comunicaciones tenga lugar en un puerto asignado dinámicamente por el firewall.

    >[AZURE.NOTE] Para ráfaga para implementaciones Azure, también puede configurar un comando de la excepción de firewall para que se ejecute automáticamente en todos los nodos de Azure nuevas que se agregan al clúster. Después de ejecutar el comando **hpcfwutil** y compruebe que funciona la aplicación, agregue el comando a un script de inicio para los nodos de Azure. Para obtener más información, consulte [usar un Script de inicio para los nodos de Azure](https://technet.microsoft.com/library/jj899632.aspx).



* Paquete de HPC usa la variable de entorno de clúster CCP_MPI_NETMASK para especificar un intervalo de direcciones aceptables para la comunicación de MPI. A partir de HPC Pack 2012 R2, la variable de entorno CCP_MPI_NETMASK clúster sólo afecta a la comunicación de MPI entre los nodos de cálculo de clúster de dominio (en modo local o en máquinas virtuales de Azure). La variable se omite los nodos agregado en una ráfaga a configuración de Azure.


* No pueden ejecutar trabajos MPI en varias instancias de Azure que se ha implementado en servicios de nube diferente (por ejemplo, en ráfaga para implementaciones Azure con plantillas de nodo distinto o nodos de cálculo de Azure VM implementados en varios servicios de nube). Si tiene varias implementaciones de nodo Azure que se inician con plantillas de nodo diferente, debe ejecutar el trabajo MPI en solo un conjunto de nodos de Azure.


* Cuando agregue nodos Azure al clúster y conectarlos, el servicio Programador de trabajos de HPC inmediatamente intenta iniciar trabajos en los nodos. Si solo una parte de su carga de trabajo puede ejecutarse en Azure, asegúrese de que actualizar o crear plantillas de trabajo para definir qué tipos de trabajo que se pueden ejecutar en Azure. Por ejemplo, para garantizar que los trabajos enviados con una plantilla de trabajo solo se ejecutan en los nodos de Azure, agregue la propiedad de grupos de nodo a la plantilla de trabajo y seleccione AzureNodes como el valor requerido. Para crear grupos personalizados para los nodos de Azure, use el cmdlet de PowerShell de agregar HpcGroup HPC.


## <a name="next-steps"></a>Pasos siguientes

* Como alternativa a HPC Pack, desarrollar con el servicio de Azure lote para ejecutar aplicaciones de MPI en agrupaciones administrados de nodos de cálculo en Azure. Vea [usar instancias múltiples tareas para ejecutar aplicaciones de la interfaz de paso de mensajes (MPI) en el lote de Azure](../batch/batch-mpi.md).

* Si desea ejecutar Linux MPI aplicaciones que tienen acceso a la red de Azure RDMA, vea [configurar un clúster de Linux RDMA para ejecutar aplicaciones de MPI](virtual-machines-linux-classic-rdma-cluster.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/burst.png
[iaas]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/iaas.png
[pingpong1]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong1.png
[pingpong2]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong2.png