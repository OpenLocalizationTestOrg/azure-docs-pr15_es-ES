<properties
   pageTitle="MATLAB clústeres en máquinas virtuales | Microsoft Azure"
   description="Usar máquinas virtuales de Microsoft Azure para crear clústeres MATLAB distribuido informática de servidor para ejecutar las cargas de trabajo MATLAB paralelo de descarga intensiva"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mscurrell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Windows"
   ms.workload="infrastructure-services"
   ms.date="05/09/2016"
   ms.author="markscu"/>

# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>Crear clústeres MATLAB distribuido informática Server en máquinas virtuales de Azure 

Use máquinas virtuales de Microsoft Azure para crear uno o más clústeres MATLAB distribuido informática de servidor para ejecutar las cargas de trabajo MATLAB paralelo de descarga intensiva. Instale el software de servidor de informática distribuida MATLAB en una máquina virtual para usar como una imagen de base y usar una plantilla de tutorial Azure o una secuencia de comandos de PowerShell de Azure (disponible en [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster)) para implementar y administrar el clúster. Después de la implementación, conecte el clúster para ejecutar las cargas de trabajo. 

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>Acerca de MATLAB y MATLAB distribuido informática Server 

La plataforma [MATLAB](http://www.mathworks.com/products/matlab/) está optimizada para solucionar problemas de ingeniería y científicos. Los usuarios MATLAB con simulaciones a gran escala y las tareas de procesamiento de datos pueden usar paralelo MathWorks informática productos para acelerar sus cargas de trabajo de descarga intensiva aprovechando clústeres y servicios de cuadrícula. [Herramientas de informática en paralelo](http://www.mathworks.com/products/parallel-computing/) permite a los usuarios MATLAB paralelo aplicaciones y aprovechar las ventajas de los procesadores multinúcleo, ofrece y clústeres de cálculo. [Servidor de informática distribuida de MATLAB](http://www.mathworks.com/products/distriben/) permite a los usuarios MATLAB utilizar varios equipos en un clúster de cálculo. 


Mediante máquinas virtuales de Windows Azure, puede crear MATLAB distribuido informática clústeres que tengan los mismos mecanismos disponibles para enviar el trabajo paralelo como clústeres local, como trabajos interactivos, trabajos por lotes, tareas independientes y comunicar tareas. Usar tradicional local hardware y con Azure junto con la plataforma MATLAB tiene muchas ventajas en comparación con aprovisionamiento: tamaños de un rango de máquina virtual, la creación de clústeres a petición por lo que paga solo para los recursos de cálculo uso y la capacidad de probar modelos a escala.  

## <a name="prerequisites"></a>Requisitos previos

* **Equipo cliente** - tendrá un equipo cliente basado en Windows para comunicarse con Azure y el clúster de servidor de informática distribuida MATLAB después de la implementación. 

* **Azure PowerShell** : vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) instalarlo en el equipo cliente. 

* **Azure suscripción** : si no tiene una suscripción, puede crear un [libre cuenta](https://azure.microsoft.com/free/) de dos minutos. Para clústeres más grandes, considere la posibilidad de una suscripción de pago u otras opciones de compra. 

* **Cuota de núcleos** - es posible que necesite aumentar la cuota de principales para implementar un clúster grande o más de un servidor de informática distribuida MATLAB. Para aumentar una cuota, [Abra una solicitud de soporte técnico en línea para clientes](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sin cargo. 

* **MATLAB, herramientas de informática en paralelo y licencias de servidor de informática distribuida MATLAB** - las secuencias de comandos, se suponen que el [Administrador de licencias de MathWorks hospedado](http://www.mathworks.com/products/parallel-computing/mathworks-hosted-license-manager/) se usa para todas las licencias.  

* **Software de servidor informática distribuida de MATLAB** - se instalará en una máquina virtual que se utilizará como la imagen VM base para el clúster máquinas virtuales. 


## <a name="high-level-steps"></a>Pasos de nivel alto

Para usar su MATLAB distribuido informática clústeres máquinas virtuales de Windows Azure, se requiere los pasos siguientes. Las instrucciones detalladas están en la documentación que acompaña a la plantilla de tutorial rápido y secuencias de comandos en [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster).

1. **Crear una imagen de máquina virtual base**  
    * Descargue e instale el software de servidor de informática distribuida MATLAB en esta máquina virtual. 

    >[AZURE.NOTE]Este proceso puede tardar un par de horas, pero solo tiene que hacerlo una vez para cada versión de MATLAB utilizado.   
    
2. **Crear uno o más clústeres**  
    * Utilice la secuencia de comandos de PowerShell suministrada o la plantilla de tutorial rápido para crear un clúster de la imagen VM base.   
    * Administrar los clústeres mediante la secuencia de comandos de PowerShell suministrado que permite a la lista, pausar, reanudar y eliminar clústeres. 
 
## <a name="cluster-configurations"></a>Configuraciones de clúster 

Actualmente, la plantilla y la secuencia de comandos de creación de clúster le permiten crear una topología de servidor de informática distribuida MATLAB. Si lo desea, cree uno o más clústeres adicionales con cada clúster de un número distinto de trabajo VM, con distintos tamaños VM y así sucesivamente. 

### <a name="matlab-client-and-cluster-in-azure"></a>Cliente MATLAB y clúster de Azure 

El nodo de cliente MATLAB, nodo de MATLAB planificador y nodos de "Trabajo" de MATLAB distribuido informática Server se configuran todos como máquinas virtuales de Azure en una red virtual, tal como se muestra en la siguiente ilustración. 

![Topología de clúster](./media/virtual-machines-windows-matlab-mdcs-cluster/mdcs_cluster.png)

* Para utilizar el clúster, conectar escritorio remoto para el nodo de cliente. El nodo cliente ejecuta al cliente MATLAB. 

* El nodo de cliente tiene un archivo compartido que puede tener acceso a todos los trabajadores.

* MathWorks alojado License Manager se usa para las comprobaciones de licencia para todo el software MATLAB. 

* De forma predeterminada, se crea un trabajo de servidor informática distribuida de MATLAB por núcleo en el trabajo máquinas virtuales, pero puede especificar cualquier número. 


## <a name="use-an-azure-based-cluster"></a>Usar un clúster de Azure 

Como con otros tipos de MATLAB distribuido informática clústeres, debe usar el Administrador de perfiles de clúster en el cliente MATLAB (en el cliente VM) para crear un perfil de clúster de planificador de MATLAB.

![Administrador de perfiles de clúster](./media/virtual-machines-windows-matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>Pasos siguientes

* Para que obtener instrucciones detalladas implementar y administrar clústeres de servidor de informática distribuida MATLAB en Azure, consulte el repositorio de [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) que contiene las plantillas y secuencias de comandos. 

* Vaya al [sitio de MathWorks](http://www.mathworks.com/) documentación detallada de MATLAB y MATLAB distribuido informática Server.
