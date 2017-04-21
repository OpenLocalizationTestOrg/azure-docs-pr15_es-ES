<properties
   pageTitle="Crear clústeres de tela de servicio de Azure en Windows Server y Linux | Microsoft Azure"
   description="Clústeres de servicio tela ejecutar en Windows Server y Linux, lo que significa que podrá implementar y aplicaciones de servicio tela host en cualquier lugar puede ejecutar Windows Server o Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="Chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="chackdan"/>

# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Crear clústeres de tela de servicio en Windows Server o Linux

Azure tela de servicio permite la creación de clústeres de servicio tela cualquier máquinas virtuales o equipos que ejecutan Windows Server o Linux. Esto significa que puede implementar y ejecutar aplicaciones de servicio tela en cualquier entorno donde tiene un conjunto de equipos de Windows Server o Linux interconectados, ya sea local, Microsoft Azure o con cualquier proveedor de nube.

##<a name="create-service-fabric-clusters-on-azure"></a>Crear clústeres de servicio tela en Azure

Crear un clúster en Azure se realiza a través de una plantilla de modelo de recursos o el portal de Azure. Para obtener más información, lea [crear un clúster de servicio tela mediante una plantilla de administrador de recursos](service-fabric-cluster-creation-via-arm.md) o [crear un clúster de tela de servicio desde el portal de Azure](service-fabric-cluster-creation-via-portal.md) .

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Sistemas operativos admitidos para clústeres en Azure

Es posible crear clústeres en máquinas virtuales con estos sistemas operativos:

* Windows Server 2012 R2
* Windows Server 2016 (después de que se anuncia generalmente disponible)
* Linux Ubuntu 16.04 (en la versión preliminar pública) 


##<a name="create-service-fabric-standalone-clusters-on-premise-or-with-any-cloud-provider"></a>Crear servicio tela independiente clústeres local o con cualquier proveedor de servicios de nube

Servicio tela proporciona un paquete de instalación para crear independiente servicio tela clústeres local o en cualquier proveedor de servicios de nube

Para obtener más información acerca de cómo configurar clústeres de tela de servicio independiente en Windows Server, lea [tela de servicio de creación de un clúster de Windows Server](service-fabric-cluster-creation-for-windows-server.md)

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>Las implementaciones de nube frente en las implementaciones locales
El proceso de creación de un clúster de servicio tela local es similar al proceso de creación de un clúster en cualquier nube de su elección con un conjunto de máquinas virtuales. Los pasos iniciales proporcionando las máquinas virtuales se rigen por el proveedor de servicios de nube o entorno local que está utilizando. Una vez que tenga un conjunto de máquinas virtuales con la conectividad de red habilitada entre ellas, a continuación, los pasos para configurar el paquete de servicio tela, editar la configuración de clúster, la creación de un clúster y ejecutar secuencias de comandos de administración son idénticos. Esto garantiza que sus conocimientos y experiencia de funcionamiento y administrar clústeres tela de servicio se pueden transferir cuando decide nuevos entornos de hospedaje de destino.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>Ventajas de la creación de clústeres de tela de servicio independiente
* Son gratuitas elija cualquier proveedor de servicios de nube para hospedar el clúster.
* Aplicaciones de servicio tela, una vez escritas, se pueden ejecutar en entornos de hospedaje varias con mínimas sin cambios.
* Conocimiento de la creación de aplicaciones de servicio tela transfiere desde un entorno de hospedaje a otro.
* Experiencia de funcionamiento de ejecutar y administrar el servicio tela clústeres lleva sobre desde un entorno a otro.
* Cliente amplio alcance es ilimitado por las restricciones del entorno de hospedaje.
* Una capa adicional de seguridad y protección contra interrupciones generalizadas existe porque puede mover los servicios sobre otro entorno de implementación si un proveedor de nube o de centro de datos tiene un en negro.

## <a name="supported-operating-systems-for-standalone-clusters"></a>Sistemas operativos admitidos para clústeres independiente
Es posible crear clústeres en máquinas virtuales o equipos con estos sistemas operativos:

* Windows Server 2012 R2
* Windows Server 2016 (después de que se anuncia generalmente disponible)
* Linux (próximamente)

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>Ventajas de servicio tela clústeres en Azure sobre independiente que servicio tela clústeres crean local

Servicio tela clústeres se ejecuta en Azure proporciona ventajas sobre las instalaciones de opción, por lo que si no tiene necesidades específicas para el que se ejecutan los clústeres, a continuación, le recomendamos que se ejecuta en Azure. En Azure, le proporcionamos integración con otras características de Azure y servicios, lo que facilita la administración del clúster y operaciones y más fiable.

* **Portal azure:** Portal de Azure facilita la crear y administrar clústeres.

* **Azure Administrador de recursos:** Uso del Administrador de recursos de Azure permite la fácil administración de todos los recursos utilizados por el clúster como una unidad y simplifica el seguimiento del costo y facturación.
* **Servicio tela clúster como un recurso de Azure** Un clúster de servicio tela es un recurso ARM, para poder modelar igual que otros recursos ARM en Azure.
* **Integración con la infraestructura de Azure** Servicio tela coordenadas con la infraestructura de Azure subyacente para el sistema operativo, red y otras actualizaciones mejorar la disponibilidad y la confiabilidad de las aplicaciones.  
* **Diagnósticos:** En Azure, le proporcionamos integración con el análisis de registro y diagnóstico de Azure.
* **-Autoescala:** Para clústeres en Azure, le proporcionamos funcionalidad integrada-Autoescala debido a conjuntos de escala de máquina Virtual. En local y otros entornos de nube, debe crear su propia característica o escala manualmente usando las API que expone el servicio tela para clústeres de ajuste de escala de escala automático.

## <a name="next-steps"></a>Pasos siguientes
Crear un clúster en máquinas virtuales o equipos que ejecutan Windows Server: [creación de un clúster tela de servicio de Windows Server](service-fabric-cluster-creation-for-windows-server.md)

Crear un clúster en máquinas virtuales o equipos que ejecutan Linux: [Tela de servicio en Linux](service-fabric-linux-overview.md)
