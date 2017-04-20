<properties
    pageTitle="Glosario de Azure - diccionario Azure | Microsoft Azure"
    description="Use el glosario Azure para comprender la terminología de la nube en la plataforma Windows Azure. Este breve diccionario Azure proporciona definiciones de los términos comunes de nube de Azure."
    keywords="Diccionario de Azure, terminología de nube, Glosario de Azure, definiciones de terminología, términos de la nube"
    services="na"
    documentationCenter="na"
    authors="MonicaRush"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="monicar"/>


# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Glosario de Microsoft Azure: un diccionario de terminología de nube en la plataforma Windows Azure

Glosario de Microsoft Azure es un breve diccionario de terminología de nube de la plataforma Windows Azure.

## <a name="find-service-definitions-and-other-cloud-terms"></a>Buscar definiciones de servicio y otros términos de nube

* Para obtener definiciones de los servicios de Azure y sus AWS homólogos vea [Microsoft Azure y servicios Web de Amazon](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).

* Para el sector general términos de nube vea [términos de la informática en nube](https://azure.microsoft.com/overview/cloud-computing-dictionary/).

Glosario de Azure con dos referencias anteriores proporciona una taxonomía de llevar a cabo para Azure y el sector de la nube.  

## <a name="azure-glossary-list"></a>Lista de Azure glosario


### <a name="account"></a>cuenta  
Un trabajo o escuela o cuenta personal de Microsoft que se usa para acceder y administrar una suscripción de Azure.  
Vea también [cómo Azure suscripciones están asociadas a Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md)


### <a name="availability-set"></a>conjunto de disponibilidad  
Una colección de máquinas virtuales que se administran conjuntamente para proporcionar confiabilidad y redundancia de la aplicación. El uso de un conjunto de disponibilidad garantiza que durante un evento de mantenimiento planificado o al menos una máquina virtual está disponible.  
Vea también [Administrar la disponibilidad de máquinas virtuales de Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) o [Administrar la disponibilidad de máquinas virtuales de Linux](./virtual-machines/virtual-machines-linux-manage-availability.md)


### <a name="classic-model"></a>Modelo de implementación de clásico de Azure  
Uno de los dos [modelos de implementación](resource-manager-deployment-model.md) que se utilizan para implementar recursos en Azure (el nuevo modelo es el Administrador de recursos de Azure). Algunos recursos Azure se pueden implementar en dos, mientras que otros usuarios pueden implementados en ambos modelos. Guía para los detalles de los recursos de Azure individuales que model(s) un recurso se puede implementar con.


### <a name="cli"></a>Azure línea de comandos de interfaz  
[Interfaz de línea de comandos](xplat-cli-install.md) que se pueden usar para administrar los servicios de Azure de Windows, OSX y equipos Linux.


### <a name="powershell"></a>Azure PowerShell  
[Interfaz de línea de comandos](powershell-install-configure.md) para administrar los servicios de Azure a través de una línea de comandos de equipos con Windows. Algunos servicios o características de servicio pueden administrarse a través de PowerShell o la CLI. Pautas para cada detalles del recurso de Azure individuales que model(s) un recurso se pueden implementar con.   
Vea también [cómo instalar y configurar PowerShell de Azure](powershell-install-configure.md)


### <a name="arm-model"></a>Modelo de implementación de Azure Administrador de recursos  
Uno de los dos [modelos de implementación](resource-manager-deployment-model.md) usado para implementar recursos en Microsoft Azure (la otra es el modelo de implementación clásico). Algunos recursos Azure se pueden implementar en dos, mientras que otros usuarios pueden implementados en ambos modelos. Guía para los detalles de los recursos de Azure individuales que model(s) un recurso se puede implementar con.


### <a name="fault-domain"></a>dominio de error  
La colección de máquinas virtuales de un conjunto de disponibilidad que posiblemente puede fallar al mismo tiempo. Un ejemplo es un grupo de equipos en un bastidor que comparten un modificador de origen y de red power comunes. En Azure, las máquinas virtuales de un conjunto de disponibilidad se separan automáticamente en varios dominios de errores.  
Vea también [Administrar la disponibilidad de máquinas virtuales de Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) o [Administrar la disponibilidad de máquinas virtuales de Linux](./virtual-machines/virtual-machines-linux-manage-availability.md)  


### <a name="geo"></a>geo  
Límite definido para residencia de datos que normalmente contiene dos o más regiones. Los límites pueden estar dentro o más allá de los bordes nacionales e influenciados por la normativa de impuestos. Cada geo tiene al menos una región. Ejemplos de zonas son Asia Pacífico y Japón. También se denomina *geografía*.  
Vea también [regiones de Azure](best-practices-availability-paired-regions.md)


### <a name="geo-replication"></a>replicación de geo  
El proceso de replicar automáticamente el contenido como blobs, tablas y colas de dentro de un par regional.  
Vea también [La replicación de Active Geo para base de datos SQL Azure](./sql-database/sql-database-geo-replication-overview.md)


### <a name="image"></a>imagen  
Un archivo que contiene el sistema operativo y la configuración de la aplicación que se pueden usar para crear cualquier número de máquinas virtuales de Windows. En Azure existen dos tipos de imágenes: VM imagen y OS. Una imagen de máquina virtual incluye un sistema operativo y todos los discos conectados a una máquina virtual cuando se crea la imagen. Una imagen de sistema operativo contiene solo un sistema operativo generalizado sin configuraciones de discos de datos.  
Consulte también [navegar y seleccionadas imágenes de máquina virtual de Windows Azure con PowerShell o CLI](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md)


### <a name="limits"></a>límites  
El número de recursos que se pueden crear o la referencia de rendimiento que se puede conseguir. Límites se asocian normalmente a ofertas, servicios y suscripciones.  
Consulte también [suscripción Azure y límites de servicio, cuotas y las restricciones](azure-subscription-service-limits.md)


### <a name="load-balancer"></a>equilibrador de carga  
Un recurso que se distribuye el tráfico entrante entre equipos en una red. En Azure, un equilibrador de carga distribuye el tráfico a máquinas virtuales definidos en un conjunto de equilibrador de carga. Un [equilibrador de carga](./load-balancer/load-balancer-overview.md) puede ser a través de internet, o pueden ser interno.  


### <a name="offer"></a>oferta  
Los precios, créditos y términos relacionados aplicables a una suscripción de Azure.  
Consulte la [página de detalles de la oferta de Azure](https://azure.microsoft.com/support/legal/offer-details/)


### <a name="portal"></a>Portal  
El portal de Web seguro usado para implementar y administrar servicios de Azure.  Existen dos portales: el [portal de Azure](http://portal.azure.com/) y el [portal de clásico](http://manage.windowsazure.com/). Algunos servicios están disponibles en ambos portales, mientras que otros solo están disponibles en uno de ellos. El [gráfico de disponibilidad de portal Azure](https://azure.microsoft.com/features/azure-portal/availability/) enumera los servicios que están disponibles en qué portal.  


### <a name="region"></a>región  
Área dentro de una geo que no bordes no cruzada nacionales y contiene uno o más centros de datos. Precios, servicios regionales y tipos de oferta se exponen en el nivel de región. Una región normalmente se corresponde con otra región, que puede tener hasta varios cientos millas, para formar una pareja regional. Pares regionales pueden usarse como mecanismo para escenarios de alta disponibilidad y la recuperación. También se denomina generalmente *ubicación*.  
Vea también [regiones de Azure](best-practices-availability-paired-regions.md)


### <a name="resource"></a>recursos  
Un elemento que forme parte de su solución de Azure. Cada servicio de Azure le permiten implementar diferentes tipos de recursos, como bases de datos o máquinas virtuales de Windows.   
Vea también [información general del Administrador de recursos de Azure](azure-resource-manager/resource-group-overview.md)


### <a name="resource-group"></a>grupo de recursos  
Un contenedor en el Administrador de recursos que contiene los recursos relacionados para una aplicación. El grupo de recursos puede incluir todos los recursos para una aplicación o sólo aquellos recursos que lógicamente se agrupan. Puede decidir cómo desea asignar recursos a grupos de recursos basándose en lo que más sentido tenga para su organización.  
Vea también [información general del Administrador de recursos de Azure](azure-resource-manager/resource-group-overview.md)


### <a name="arm-template"></a>Plantilla de administrador de recursos  
Un archivo JSON que define mediante declaración uno o más recursos de Azure y que define las dependencias entre los recursos implementadas. La plantilla se puede utilizar para implementar los recursos de forma coherente y varias veces.  
Vea también [plantillas de administrador de recursos de Azure de creación](resource-group-authoring-templates.md)


### <a name="resource-provider"></a>proveedor de recursos  
Un servicio que proporciona los recursos que puede implementar y administrar mediante el Administrador de recursos. Cada proveedor de recursos ofrece operaciones para trabajar con los recursos que se implementan. Proveedores de recursos se pueden acceder a través del portal de Azure, Azure PowerShell y varios SDK de programación.  
Vea también [información general del Administrador de recursos de Azure](azure-resource-manager/resource-group-overview.md)


### <a name="role"></a>función  
Un medio para controlar el acceso que se puede asignar a los usuarios, grupos y servicios. Las funciones son pueden realizar acciones como crear, administrar y leer en Azure recursos.  
Vea también [RBAC: funciones integradas](./active-directory/role-based-access-built-in-roles.md)


### <a name="sla"></a>contrato de nivel de servicio (SLA)  
El acuerdo que describe los compromisos de Microsoft y conectividad. Cada servicio de Azure tiene un SLA específico.  
Vea también [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/)


### <a name="storage-account"></a>cuenta de almacenamiento  
Una cuenta de almacenamiento que proporciona acceso a los servicios de blobs de Windows Azure, cola, tabla y archivo de almacenamiento de Azure. Su cuenta de almacenamiento proporciona el espacio de nombres único para los objetos de datos de almacenamiento de Azure.  
Consulte también [cuentas de almacenamiento acerca de Azure](./storage/storage-create-storage-account.md)


### <a name="subscription"></a>suscripción  
Contrato de un cliente con Microsoft que les permite obtener servicios de Azure. Los precios de suscripción y términos relacionados se rigen por la oferta elegida para la suscripción. Consulte el [contrato de suscripción en línea de Microsoft](https://azure.microsoft.com/support/legal/subscription-agreement/).  
Vea también [cómo Azure suscripciones están asociadas a Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md)


### <a name="tag"></a>etiqueta  
Un término de indización que le permite clasificar los recursos de acuerdo con los requisitos para administrar o de facturación. Puede utilizar etiquetas cuando tiene una colección de grupos de recursos y recursos compleja y desea visualizar los activos de la manera que más sentido. Por ejemplo, puede etiquetar recursos que tienen una función similar en su organización o que pertenecen al mismo departamento.  
Vea también [mediante etiquetas para organizar los recursos de Azure](resource-group-using-tags.md)


### <a name="update-domain"></a>Actualizar dominio  
La colección de máquinas virtuales de un conjunto de disponibilidad que se actualizan al mismo tiempo. Máquinas virtuales en el mismo dominio de actualización se reinicia juntos durante el mantenimiento planeado. Azure no reinicia nunca más de un dominio de actualización en un momento. También conocida como un dominio de actualización.  
Vea también [Administrar la disponibilidad de máquinas virtuales de Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) o [Administrar la disponibilidad de máquinas virtuales de Linux](./virtual-machines/virtual-machines-linux-manage-availability.md)  


### <a name="vm"></a>máquina virtual  
La implementación de software de un equipo físico que ejecuta un sistema operativo. Varias máquinas virtuales puede ejecutar simultáneamente en el mismo hardware. En Azure, máquinas virtuales están disponibles en una gran variedad de tamaños.  
Vea también [documentación máquinas virtuales de Windows](https://azure.microsoft.com/documentation/services/virtual-machines/)


### <a name="vm-extension"></a>extensión de máquina virtual  
Un recurso que implemente comportamientos o características que ya sea ayudar a otros programas de trabajo o proporcionar la capacidad para interactuar con un equipo con. Por ejemplo, podría usar la extensión de acceso de VM restablecer o modifique los valores de acceso remoto en una máquina virtual Azure.  
Consulte también [acerca de extensiones de máquina virtual y características (Windows)](./virtual-machines/virtual-machines-windows-extensions-features.md) o [extensiones de máquina virtual y características (Linux)](./virtual-machines/virtual-machines-linux-extensions-features.md)


### <a name="vnet"></a>red virtual  
Una red que proporciona la conectividad entre los recursos de Azure está aislado de todos los inquilinos de Azure. Puede conectarse a otras redes virtuales Azure a través de una [Puerta de enlace de Azure VPN](./vpn-gateway/vpn-gateway-about-vpngateways.md) y a su red local con [varias opciones](./vpn-gateway/vpn-gateway-cross-premises-options.md). Puede controlar completamente el bloques de direcciones IP, configuración de DNS, directivas de seguridad y tablas de rutas dentro de esta red.  
Vea también [información general de una red Virtual](./virtual-network/virtual-networks-overview.md)  

###<a name="see-also"></a>**Vea también**  
- [Introducción a Azure](https://azure.microsoft.com/get-started/)
- [Centro de recursos de la nube](https://azure.microsoft.com/resources/)  
- [Azure para su aplicación de empresa](https://azure.microsoft.com/overview/business-apps-on-azure/)
- [Azure en el centro de datos](https://azure.microsoft.com/overview/business-apps-on-azure/) 





