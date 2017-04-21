<properties 
   pageTitle="Configurar una puerta de enlace VPN en el Portal de clásico Azure | Microsoft Azure"
   description="En este artículo le guiará a través de la configuración de la puerta de enlace VPN de red virtual y cambiar un tipo de enrutamiento VPN de puerta de enlace."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/11/2016"
   ms.author="cherylmc" />

# <a name="configure-a-vpn-gateway-for-the-classic-deployment-model"></a>Configurar una puerta de enlace VPN para el modelo de implementación clásica


Si desea crear una conexión segura entre local entre Azure y su ubicación en local, debe configurar una conexión VPN de puerta de enlace. En el modelo de implementación clásica, una puerta de enlace puede ser uno de los dos tipos de enrutamiento de VPN: estática o dinámica. El tipo que elija depende de su plan de diseño de red tanto el dispositivo VPN local que desea usar. 

Por ejemplo, algunas opciones de conectividad, como una conexión punto a sitios, requieren una puerta de enlace enrutamiento dinámico. Si desea configurar la puerta de enlace para admitir conexiones de punto a sitio (P2S) y una conexión de sitio a sitio (S2S), debe configurar una puerta de enlace enrutamiento dinámico aunque también se pueden configurar para sitios con cualquier tipo de enrutamiento VPN de puerta de enlace. 

Además, debe asegurarse de que el dispositivo que desea usar para la conexión admite el tipo de ruta de VPN que desea crear. Consulte [acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md).


**Acerca de este artículo** 

En este artículo se escribió para el modelo de implementación clásica con el [portal clásica](https://manage.windowsazure.com) (no el portal de Azure). 

**Acerca de los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-overview"></a>Introducción a la configuración

Los siguientes pasos le guíe durante la configuración de la puerta de enlace VPN en el portal de clásico de Azure. Estos pasos se aplican a las puertas de enlace para redes virtuales que se crearon usando el modelo de implementación clásico. Actualmente, no todos los valores de configuración de puertas de enlace están disponibles en el portal de Azure. Cuando se les, se creará un nuevo conjunto de instrucciones que se aplican al portal de Azure.


1. [Crear una puerta de enlace VPN para su VNet](#create-a-vpn-gateway)

1. [Recopilar información para la configuración del dispositivo VPN](#gather-information-for-your-vpn-device-configuration)

1. [Configurar el dispositivo VPN](#configure-your-vpn-device)

1. [Compruebe que la rangos de red local y la dirección IP de puerta de enlace VPN](#verify-your-local-network-ranges-and-vpn-gateway-ip-address)

### <a name="before-you-begin"></a>Antes de empezar

Antes de configurar la puerta de enlace, primero debe crear la red virtual. Para conocer los pasos crear una red virtual para conectividad entre local, consulte [Configurar una red virtual con una conexión VPN de sitio a sitio](vpn-gateway-site-to-site-create.md)o [Configurar una red virtual con una conexión VPN punto al sitio](vpn-gateway-point-to-site-create.md). A continuación, realice los pasos siguientes para configurar la puerta de enlace VPN y recopilar la información que necesita configurar el dispositivo VPN. 

Si ya tiene una puerta de enlace VPN y desea cambiar el tipo de ruta de VPN, consulte [cómo cambiar el tipo de enrutamiento VPN de la puerta de enlace](#how-to-change-the-vpn-routing-type-for-your-gateway).

## <a name="create-a-vpn-gateway"></a>Crear una puerta de enlace VPN

1. En el [portal de clásica Azure](https://manage.windowsazure.com), en la página de **redes** , compruebe que la columna Estado para su red virtual es **creado**.

1. En la columna **nombre** , haga clic en el nombre de su red virtual.

1. En la página de **panel** , tenga en cuenta que esta VNet no dispone de una puerta de enlace ha configurado. Verá este estado pasar por los pasos para configurar la puerta de enlace.

![Puerta de enlace no creado](./media/vpn-gateway-configure-vpn-gateway-mp/IC717025.png)


A continuación, en la parte inferior de la página, haga clic en **Crear la puerta de enlace**. Puede seleccionar *Enrutamiento estático* o *Enrutamiento dinámico*. Seleccione el tipo de ruta de VPN depende de algunos factores. Por ejemplo, ¿qué es compatible con el dispositivo VPN y si necesita admitir conexiones de punto al sitio. Consulte [Acerca de los dispositivos VPN para la conectividad de red Virtual](vpn-gateway-about-vpn-devices.md) para comprobar el tipo de enrutamiento VPN que necesita. Una vez creada la puerta de enlace, no puede cambiar entre tipos enrutamiento de puerta de enlace VPN sin eliminar y volver a crear la puerta de enlace. Cuando el sistema le pida que confirme que desea que la puerta de enlace creado, haga clic en **Sí**.

![Tipo de enrutamiento de puerta de enlace VPN](./media/vpn-gateway-configure-vpn-gateway-mp/IC717026.png)

Cuando se cree la puerta de enlace, observe el gráfico de la puerta de enlace en la página cambia a amarillo y muestra la *Creación de puerta de enlace*. Puede tardar hasta 45 minutos para crear la puerta de enlace. Espere hasta que se complete la puerta de enlace para poder mover hacia delante con otras opciones de configuración.

![Creación de la puerta de enlace](./media/vpn-gateway-configure-vpn-gateway-mp/IC717027.png)

Cuando se cambia la puerta de enlace para *Conectar*, puede recopilar la información que necesitará para el dispositivo VPN.

![Conexión de la puerta de enlace](./media/vpn-gateway-configure-vpn-gateway-mp/IC717028.png)

## <a name="gather-information-for-your-vpn-device-configuration"></a>Recopilar información para la configuración del dispositivo VPN

Después de crear la puerta de enlace, recopilar información para la configuración del dispositivo VPN. Esta información se encuentra en la página de **panel** para su red virtual:

1. **Dirección IP de puerta de enlace:** La dirección IP se puede encontrar en la página de **panel** . No podrá verla hasta después de la puerta de enlace ha terminado de crear.

1. **Clave compartida:** Haga clic en **Administrar la clave de** la parte inferior de la pantalla. Haga clic en el icono situado junto a la tecla para copiar al Portapapeles y, a continuación, pegar y guarde la clave. Este botón solo funciona cuando hay un único túnel VPN S2S. Si tiene varios túneles VPN S2S, use la *Obtener Virtual red puerta de enlace clave compartida* API o el cmdlet de PowerShell.

![Administrar clave](./media/vpn-gateway-configure-vpn-gateway-mp/IC717029.png)


## <a name="configure-your-vpn-device"></a>Configurar el dispositivo VPN

Después de completar los pasos anteriores, usted o su administrador de red tendrán que configurar el dispositivo VPN con el fin de crear la conexión. Para obtener más información acerca de los dispositivos VPN, consulte [Acerca de los dispositivos VPN para la conectividad de red Virtual](vpn-gateway-about-vpn-devices.md) .

Después de configurar el dispositivo VPN, puede ver la información de conexión actualizada en la página de panel para su VNet.

También puede ejecutar uno de los siguientes comandos para probar la conexión:

|                      | ASA de Cisco             | Cisco ISR/ASR         | Juniper SSG/ISG | Juniper SRX/J                            |
|----------------------|-----------------------|-----------------------|-----------------|------------------------------------------|
| **Comprobar SA de modo principal**  | Mostrar crypto isakmp sa | Mostrar crypto isakmp sa | Obtener cookie ike  | Mostrar seguridad asociación de seguridad ike   |
| **Comprobar SA de modo rápido** | Mostrar crypto ipsec sa  | Mostrar crypto ipsec sa  | obtener sa          | Mostrar la asociación de seguridad de seguridad ipsec |


## <a name="verify-your-local-network-ranges-and-vpn-gateway-ip-address"></a>Compruebe que la rangos de red local y la dirección IP de puerta de enlace VPN

### <a name="verify-your-vpn-gateway-ip-address"></a>Comprobar la dirección IP de puerta de enlace VPN

De puerta de enlace para conectarse correctamente, la dirección IP de su dispositivo VPN debe estar configurada correctamente para la red Local especificada para la configuración de la cruz local. Normalmente, esto se configura durante el proceso de configuración de sitio a otro. Sin embargo, si se ha usado previamente esta red local con un dispositivo diferente, o ha cambiado la dirección IP de esta red local, modifique la configuración para especificar la dirección IP de puerta de enlace.

1. Para comprobar la dirección IP de puerta de enlace, haga clic en **redes** en el panel izquierdo de portal y, a continuación, seleccione **Redes locales** en la parte superior de la página. Verá la dirección de la puerta de enlace VPN para cada red local que ha creado. Para editar la dirección IP, seleccione la VNet y haga clic en **Editar** en la parte inferior de la página.

1. En la página **especificar los detalles de su red local** , edite la dirección IP y, a continuación, haga clic en la flecha en la parte inferior de la página siguiente.

1. En la página **especificar el espacio de direcciones** , haga clic en la marca de verificación en la parte inferior derecha para guardar la configuración.

### <a name="verify-the-address-ranges-for-your-local-networks"></a>Comprobar los intervalos de direcciones para sus redes locales

Para que el tráfico correcto fluya a través de la puerta de enlace a la ubicación local, debe comprobar que cada rango de la dirección IP se ha especificado. Cada rango tiene que aparecer en la configuración de Azure **Redes locales** . Según la configuración de red de su ubicación en local, puede ser una tarea bastante grande. Tráfico enlazado para una dirección IP que está dentro de los intervalos de la lista se enviarán a través de la puerta de enlace de red virtual VPN. Los intervalos de lista no tienen que estar intervalos privados, aunque es posible que desee comprobar que la configuración local puede recibir el tráfico entrante.

Para agregar o modificar los intervalos de una red Local, realice los siguientes pasos.

1. Para editar los intervalos de direcciones IP de una red local, haga clic en **redes** en el panel izquierdo de portal y, a continuación, seleccione **Redes locales** en la parte superior de la página. En el portal, la manera más sencilla de ver los rangos que ha incluido está en la página **Editar** . Para ver los intervalos, seleccione la VNet y haga clic en **Editar** en la parte inferior de la página.

1. En la página **especificar los detalles de su red local** , no realice los cambios. Haga clic en la flecha en la parte inferior de la página siguiente.

1. En la página **especificar el espacio de direcciones** , realice los cambios de espacio de dirección de red. A continuación, haga clic en la marca de verificación para guardar la configuración.

## <a name="how-to-view-gateway-traffic"></a>Cómo ver el tráfico de la puerta de enlace

Puede ver la puerta de enlace y el tráfico de la puerta de enlace desde su página de una red Virtual **paneles** .

En la página de **panel** puede ver lo siguiente:

- La cantidad de datos que fluyen a través de la puerta de enlace, datos y salida de datos.

- Los nombres de los servidores DNS que se especifican para su red virtual.

- La conexión entre la puerta de enlace y el dispositivo VPN.

- La clave compartida que se utiliza para configurar la conexión de la puerta de enlace en el dispositivo VPN.


## <a name="how-to-change-the-vpn-routing-type-for-your-gateway"></a>Cómo cambiar el tipo de enrutamiento VPN de la puerta de enlace

Porque algunas configuraciones de conectividad solo están disponibles para ciertos tipos de enrutamiento de puerta de enlace, es posible que necesite cambiar el tipo de enrutamiento VPN de una puerta de enlace VPN existente de puerta de enlace. Por ejemplo, desea agregar sitio de punto de conexión para una conexión de sitio a sitio ya existente que tenga una puerta de enlace estática. Conexiones de punto a sitios requieren una puerta de enlace dinámico. Esto significa para configurar una conexión P2S, tendrá que cambiar el tipo de enrutamiento VPN de puerta de enlace de estático a dinámico.

Si necesita cambiar un tipo de enrutamiento VPN de puerta de enlace, deberá eliminar la puerta de enlace existente y, a continuación, cree una nueva puerta de enlace con el nuevo tipo de enrutamiento. No es necesario eliminar toda la red virtual para cambiar el tipo de enrutamiento de puerta de enlace.

Antes de cambiar el tipo de enrutamiento VPN de puerta de enlace, asegúrese de comprobar que el dispositivo VPN es compatible con el tipo de enrutamiento que desea usar. Para descargar los nuevos ejemplos de configuración de enrutamiento y comprobar los requisitos del dispositivo VPN, consulte [Acerca de los dispositivos VPN Virtual conectividad de red](vpn-gateway-about-vpn-devices.md).

>[AZURE.IMPORTANT] Cuando se elimina una puerta de enlace de red virtual VPN, se libera la dirección VIP asignada a la puerta de enlace. Cuando vuelva a crear la puerta de enlace, VIP nueva se asigna a él.

1. **Eliminar la puerta de enlace VPN existente.**

    En la página de **panel** para su red virtual, desplácese hasta la parte inferior de la página y haga clic en **Eliminar la puerta de enlace**. Espere a que la notificación de que la puerta de enlace se ha eliminado. Una vez que recibe la notificación en la pantalla que se ha eliminado la puerta de enlace, puede crear una nueva puerta de enlace.

1. **Crear una nueva puerta de enlace VPN.**

    Use el procedimiento en la parte superior de la página para crear una nueva puerta de enlace: [crear una puerta de enlace VPN](#create-a-vpn-gateway).


## <a name="next-steps"></a>Pasos siguientes

Puede agregar máquinas virtuales a su red virtual. Aprenda [a crear una máquina virtual personalizada](../virtual-machines/virtual-machines-windows-classic-createportal.md).

Si va a configurar una conexión VPN punto al sitio, vea [Configurar una conexión VPN punto al sitio](vpn-gateway-point-to-site-create.md).

 
