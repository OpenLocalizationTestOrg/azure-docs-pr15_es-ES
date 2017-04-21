<properties
    pageTitle="Tutorial de infraestructura de ejemplo | Microsoft Azure"
    description="Obtenga información sobre las directrices de diseño e implementación claves para implementar una infraestructura de ejemplo en Azure."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="example-azure-infrastructure-walkthrough"></a>Tutorial de Azure infraestructura de ejemplo

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

En este artículo le guía a través de la construcción de una infraestructura de la aplicación de ejemplo. Se detallan diseñar una infraestructura de una tienda en línea simple que reúne todas las directrices y decisiones alrededor de convenciones de nomenclatura, conjuntos de disponibilidad, redes virtuales y equilibradores de carga e implementar realmente sus máquinas virtuales (VM).


## <a name="example-workload"></a>Carga de trabajo de ejemplo

Ciclos de Adventure Works desea crear una aplicación de tienda en línea en Azure que consta de:

- Dos servidores de nginx ejecuta al cliente front-end de un nivel de web
- Dos servidores de nginx procesar pedidos en un nivel de aplicación y datos
- Parte de los servidores de MongoDB dos de un clúster sharded para almacenar datos de productos y pedidos en un nivel de base de datos
- Dos controladores de dominio de Active Directory para las cuentas de clientes y proveedores en un nivel de autenticación
- Todos los servidores se encuentran en dos subredes:
    - una subred para los servidores web front-end 
    - una subred de back-end para los servidores de aplicaciones, MongoDB clúster y los controladores de dominio

![Diagrama de diferentes niveles de infraestructura de aplicaciones](./media/virtual-machines-common-infrastructure-service-guidelines/example-tiers.png)

Entrada seguro tráfico web debe ser equilibrio de carga entre los servidores web como los clientes examinar la tienda en línea. Orden de procesamiento de tráfico en forma de HTTP solicita desde la web servidores deben ser equilibrio de carga entre los servidores de aplicaciones. Además, la infraestructura debe diseñarse para alta disponibilidad.

El diseño resultante incluirá:

- Una suscripción de Azure y cuenta
- Un único grupo de recursos
- Cuentas de almacenamiento
- Una red virtual con dos subredes
- Conjuntos de disponibilidad para las máquinas virtuales con una función similar
- Máquinas virtuales de Windows

Todas las respuestas anteriores siguen estas convenciones de nomenclatura:

- Aventura ciclos de Works usa **[carga de trabajo de TI]-[ubicación]-[Azure recurso]** como prefijo
    - En este ejemplo, "**azos**" (Store en línea de Azure) es el nombre de la carga de trabajo de TI y "**usar**" (Estados Unidos oriental 2) es la ubicación
- Cuentas de almacenamiento usan adventureazosusesa**[descripción]**
    - 'adventure' se agregó al prefijo para proporcionar exclusividad y nombres de cuenta de almacenamiento no admite el uso de guiones.
- Redes virtuales utilizan VN de uso de AZOS**[número]**
- Conjuntos de disponibilidad usar azos-use-como-**[rol]**
- Nombres de máquina virtual usan azos-use-vm -**[vmname]**


## <a name="azure-subscriptions-and-accounts"></a>Cuentas y suscripciones de azure

Ciclos de Adventure Works está utilizando su suscripción de empresa, con el nombre de suscripción de empresa de Adventure Works, proporcione facturación para esta carga de trabajo de TI.


## <a name="storage-accounts"></a>Cuentas de almacenamiento

Ciclos de Adventure Works determina que necesitan dos cuentas de almacenamiento:

- **adventureazosusesawebapp** para el almacenamiento estándar de los servidores web, servidores de aplicaciones y los controladores de dominio y los discos de datos.
- **adventureazosusesadbclust** para el almacenamiento de prima de los servidores de clúster sharded MongoDB y los discos de datos.


## <a name="virtual-network-and-subnets"></a>Subredes y una red virtual

Debido a la red virtual no necesita en curso conectividad a la red local de Adventure ciclos de trabajo, decidido en una red virtual solo nube.

Crea una red virtual solo nube con la configuración siguiente con el portal de Azure:

- Nombre: AZOS-USE-VN01
- Ubicación: Oriental US 2
- Espacio de direcciones de red virtual: 10.0.0.0/8
- Primera subred:
    - Nombre: front-end
    - Espacio de direcciones: 10.0.1.0/24
- Segunda subred:
    - Nombre: back-end
    - Espacio de direcciones: 10.0.2.0/24


## <a name="availability-sets"></a>Conjuntos de disponibilidad

Para mantener la alta disponibilidad de los cuatro niveles de su tienda en línea, ciclos de Adventure Works decidido en cuatro conjuntos de disponibilidad:

- **usar azos como web** para los servidores web
- **use azos como aplicación** para los servidores de aplicaciones
- **usar azos como db** para los servidores de clúster sharded MongoDB
- **usar azos como dc** para los controladores de dominio


## <a name="virtual-machines"></a>Máquinas virtuales de Windows

Ciclos de Adventure Works decidido en los siguientes nombres para sus máquinas virtuales de Azure:

- **azos-uso-vm-web01** para el primer servidor web
- **azos-uso-vm-web02** para el segundo servidor web
- **azos-uso-vm-app01** del primer servidor de aplicaciones
- **app02-azos-uso-vm** del segundo servidor de aplicaciones
- **azos-uso-vm-db01** para el primer servidor de MongoDB en el clúster
- **azos-uso-vm-db02** para el segundo servidor de MongoDB en el clúster
- **azos-uso-vm-dc01** para el primer controlador de dominio
- **azos-uso-vm-dc02** para el segundo controlador de dominio

Aquí tiene la configuración resultante.

![Infraestructura de aplicación final implementada en Azure](./media/virtual-machines-common-infrastructure-service-guidelines/example-config.png)

Incorpora esta configuración:

- Una red virtual solo nube con dos subredes (front-end y back-end)
- Dos cuentas de almacenamiento
- Cuatro conjuntos de disponibilidad, uno para cada nivel de la tienda en línea
- Las máquinas virtuales de cuatro niveles
- Un conjunto de equilibrio de carga externo para el tráfico web basadas en HTTPS de Internet a los servidores web
- Una carga interno equilibrada conjunto para el tráfico de web sin cifrar de los servidores web a los servidores de aplicaciones
- Un único grupo de recursos


## <a name="next-steps"></a>Pasos siguientes

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 