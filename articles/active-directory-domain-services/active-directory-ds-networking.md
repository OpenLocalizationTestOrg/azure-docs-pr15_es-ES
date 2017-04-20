<properties
    pageTitle="Servicios de dominio de Azure AD: Directrices de las redes | Microsoft Azure"
    description="Consideraciones de red para servicios de dominio de Azure Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="maheshu"/>

# <a name="networking-considerations-for-azure-ad-domain-services"></a>Consideraciones de red para los servicios de dominio de Active Directory de Azure

## <a name="how-to-select-an-azure-virtual-network"></a>Cómo seleccionar una red virtual de Azure
Las siguientes directrices ayudarle a seleccionar una red virtual para usar con servicios de dominio de Active Directory de Azure.

### <a name="type-of-azure-virtual-network"></a>Tipo de red virtual Azure

- Puede habilitar servicios de dominio de Active Directory de Azure en una red virtual Azure clásico.

- Azure AD servicios de dominio **no se puede habilitar en redes virtuales creadas con el Administrador de recursos de Azure**.

- Puede conectar una red virtual basado en el Administrador de recursos a una red virtual clásica que los servicios de dominio de Azure AD está habilitado. Por lo tanto, puede usar servicios de dominio de Active Directory de Azure en la red virtual basado en el Administrador de recursos. Para obtener más información, vea la sección de [conectividad de red](active-directory-ds-networking.md#network-connectivity) .

- **Redes virtuales regional**: si planea usar una red virtual existente, asegúrese de que es una red virtual regional.

    - Redes virtuales que utilizan el mecanismo de grupos de afinidad heredados no se pueden usar con los servicios de dominio de Active Directory de Azure.

    - Uso de servicios de dominio de Active Directory de Azure, [migrar redes virtuales heredadas a redes virtuales regionales](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).


### <a name="azure-region-for-the-virtual-network"></a>Azure región de la red virtual

- Los servicios de dominio de Active Directory de Azure dominio administrado se implementa en la misma región Azure como la red virtual elija Habilitar el servicio en.

- Seleccione una red virtual en un área de Azure compatible con servicios de dominio de Active Directory de Azure.

- Consulte la página de [Servicios de Azure por región](https://azure.microsoft.com/regions/#services/) saber las regiones de Azure en la que los servicios de dominio de Azure AD está disponible.


### <a name="requirements-for-the-virtual-network"></a>Requisitos de la red virtual

- **Cerca de las cargas de trabajo de Azure**: seleccione la red virtual que hospeda actualmente/hospedará máquinas virtuales que necesiten acceder a los servicios de dominio de Active Directory de Azure.

- **Los servidores DNS personalizada o traer sus propias**: asegúrese de que no existen servidores DNS configurados para la red virtual personalizados.

- **Dominios existente con el mismo nombre de dominio**: asegúrese de que no tiene un dominio existente con el mismo nombre de dominio disponible en una red virtual. Por ejemplo, suponga que tiene un dominio llamado 'contoso.com' ya está disponible en la red virtual seleccionada. Más adelante, pruebe a habilitar un dominio de servicios de dominio de Active Directory de Azure administrado con el mismo nombre de dominio (es decir, 'contoso.com') en una red virtual. Producir un error al intentar activar servicios de dominio de Active Directory de Azure. Este error es debido a los conflictos de nombres para el nombre de dominio en una red virtual. En esta situación, debe usar un nombre diferente para configurar el dominio de servicios de dominio de Active Directory de Azure administrado. Como alternativa, puede anular aprovisionar el dominio existente y luego proceda para habilitar los servicios de dominio de Active Directory de Azure.

> [AZURE.WARNING] No se puede mover los servicios de dominio a una red virtual diferente después de habilitar el servicio.


## <a name="network-security-groups-and-subnet-design"></a>Grupos de seguridad de la red y diseño de subred
Un [Grupo de seguridad de red (GSN)](../virtual-network/virtual-networks-nsg.md) contiene una lista de reglas de la lista de Control de acceso (ACL) que permitir o denegar el tráfico de red de las instancias de máquina virtual en una red Virtual. NSGs se pueden asociadas con subredes o instancias VM individuales en esa subred. Cuando un GSN está asociada a una subred, se aplican las reglas ACL a todas las instancias de máquina virtual de esa subred. Además, se puede restringir el tráfico a una máquina virtual individual más asociando una GSN directamente a esa máquina virtual.

![Diseño de subred recomendada](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)


### <a name="best-practices-for-choosing-a-subnet"></a>Procedimientos recomendados para elegir una subred
- Implementar los servicios de dominio de Active Directory de Azure en una **subred dedicada independiente** dentro de su red virtual Azure.

- NSGs no se aplican a la subred dedicada de su dominio administrado. Si debe aplicar NSGs a la subred dedicada, asegurarse de que **no se bloquean los puertos necesarios para el servicio y administrar su dominio**.

- No restringir el número de direcciones IP disponibles dentro de la subred dedicada de su dominio administrado demasiado. Esta restricción impide que el servicio de disponibilidad de los dos controladores de dominio de su dominio administrado.

- **Habilita los servicios de dominio de Active Directory de Azure en la subred de puerta de enlace** de la red virtual.


> [AZURE.WARNING] Cuando se asocia un GSN con una subred en la que Servicios de dominio de Active Directory de Azure está habilitado, puede afectar a la capacidad de Microsoft de servicio y administrar el dominio. Además, se interrumpe la sincronización entre el inquilino de Azure AD y su dominio administrado. **El SLA no se aplica a implementaciones donde un GSN se ha aplicado que bloquea los servicios de dominio de Active Directory de Azure de actualizar y administrar el dominio.**


### <a name="ports-required-for-azure-ad-domain-services"></a>Puertos necesarios para los servicios de dominio de Active Directory de Azure
Los puertos siguientes son necesarios para servicios de dominio de Active Directory de Azure al servicio y mantienen su dominio administrado. Asegúrese de que estos puertos no están bloqueados para la subred en la que ha habilitado el dominio administrado.

| Número de puerto | Propósito |
|---|---|
| 443 | Sincronización con el inquilino de Azure AD |
| 3389 | Administración de su dominio |
| 5986 | Administración de su dominio |
| 636 | LDAP (LDAPS) acceso seguro a su dominio administrado |



## <a name="network-connectivity"></a>Conectividad de red
Un dominio de servicios de dominio de Active Directory de Azure administrado puede habilitarse sólo dentro de una única red virtual clásica en Azure. Redes virtuales creadas con el Administrador de recursos de Azure no son compatibles.


### <a name="scenarios-for-connecting-azure-networks"></a>Escenarios para conectar redes de Azure
Conectar redes virtuales Azure para usar el dominio administrado en cualquiera de los siguientes escenarios de implementación:

#### <a name="use-the-managed-domain-in-more-than-one-azure-classic-virtual-network"></a>Usar el dominio administrado en más de una red virtual de Azure clásica
Otras redes virtuales clásicas Azure puede conectarse a la red virtual clásica Azure en el que ha habilitado Servicios de dominio de Active Directory de Azure. Esta conexión VPN le permite usar el dominio administrado con las cargas de trabajo implementados en otras redes virtuales.

![Conectividad de red virtual clásica](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Usar el dominio administrado en una red virtual basado en el Administrador de recursos
Puede conectar una red virtual basado en el Administrador de recursos a la red virtual clásica Azure en el que ha habilitado Servicios de dominio de Active Directory de Azure. Esta conexión le permite usar el dominio administrado con las cargas de trabajo implementados en la red virtual basado en el Administrador de recursos.

![Administrador de recursos de conectividad de red virtual clásica](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)


### <a name="network-connection-options"></a>Opciones de conexión de red

- **Conexiones de VNet a VNet con conexiones de sitio a sitio VPN**: conectarse a una red virtual a otra red virtual (VNet a VNet) es similar a una red virtual a conectarse a una ubicación del sitio local. Ambos tipos de conectividad usan una puerta de enlace VPN para proporcionar un túnel seguro mediante IPsec/IKE.

    ![Conectividad de red virtual mediante puerta de enlace VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Más información: conectar redes virtuales con la puerta de enlace VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)


- **Conexiones de VNet a VNet mediante interconexión de red virtual**: red Virtual interconexión es un mecanismo que conecta dos redes virtuales de la misma región a través de la red troncal Azure. Una vez peered, las dos redes virtuales aparecen como uno para todos los fines de conectividad. Se administran como recursos independientes, pero máquinas virtuales de estas redes virtuales puede comunicarse entre sí directamente mediante direcciones IP.

    ![Conectividad de red virtual mediante interconexión](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Más información: virtual interconexión de red](../virtual-network/virtual-network-peering-overview.md)



<br>

## <a name="related-content"></a>Contenido relacionado

- [Una red virtual Azure interconexión](../virtual-network/virtual-network-peering-overview.md)

- [Configurar una conexión de VNet a VNet para el modelo de implementación clásica](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

- [Grupos de seguridad de la red de Azure](../virtual-network/virtual-networks-nsg.md)
