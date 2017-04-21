<properties
   pageTitle="Configurar una conexión de VNet a VNet para el modelo de implementación clásica | Microsoft Azure"
   description="Cómo conectar Azure redes virtuales de manera conjunta con PowerShell y el portal de clásico de Azure."
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
   ms.date="08/31/2016"
   ms.author="cherylmc"/>


# <a name="configure-a-vnet-to-vnet-connection-for-the-classic-deployment-model"></a>Configurar una conexión de VNet a VNet para el modelo de implementación clásica

> [AZURE.SELECTOR]
- [Administrador de recursos - Portal de Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [Administrador de recursos - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [Clásico: Portal clásica](virtual-networks-configure-vnet-to-vnet-connection.md)



En este artículo le guiará por los pasos para crear y conectar redes virtuales de manera conjunta con el modelo de implementación clásica (también conocido como servicio de administración). Los pasos siguientes utilizan el portal clásico Azure para crear la VNets y puertas de enlace y PowerShell para configurar la conexión de VNet a VNet. No puede configurar la conexión en el portal.

![VNet a VNet conectividad diagrama](./media/virtual-networks-configure-vnet-to-vnet-connection/v2vclassic.png)


### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelos de implementación y métodos para las conexiones de VNet a VNet

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

La siguiente tabla muestra los métodos para las configuraciones de VNet a VNet y modelos de implementación disponible actualmente. Cuando esté disponible un artículo con pasos de configuración, nos vincularlo directamente desde esta tabla.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>Acerca de las conexiones de VNet a VNet

Conectarse a una red virtual a otra red virtual (VNet a VNet) es similar a una red virtual a conectarse a una ubicación del sitio local. Ambos tipos de conectividad usan una puerta de enlace VPN para proporcionar un túnel seguro mediante IPsec/IKE. 

Puede ser la VNets que conectar en diferentes suscripciones y de diferentes regiones. Puede combinar VNet VNet comunicación con configuraciones de varios sitios. Esto le permite establecer topologías de red que se combinan conectividad entre local con conectividad de red entre virtual.


### <a name="why-connect-virtual-networks"></a>¿Por qué conectar redes virtuales?

Desea conectar redes virtuales por los siguientes motivos:

- **Combinación cruzada redundancia de región geográfica y presencia geo**
    - Puede configurar su propio geo replicación o sincronización con conectividad segura sin saliendo extremos a través de Internet.
    - Equilibrador de carga de Azure y Microsoft o de tecnología de clústeres de terceros, puede configurar altamente disponible carga de trabajo con geo redundancia en diferentes regiones de Azure. Un ejemplo de importante es configurar SQL siempre en con grupos de disponibilidad reparte en varias áreas de Azure.

- **Aplicaciones de varios niveles regionales con el límite de aislamiento fuerte**
    - Dentro de la misma región, puede configurar las aplicaciones de varios niveles con varios VNets conectado junto con aislamiento sólido y una comunicación segura entre nivel.

- **Combinación cruzada suscripción, comunicación entre organizaciones en Azure**
    - Si tiene varias suscripciones de Azure, se pueden conectar las cargas de trabajo de distintas suscripciones segura entre redes virtuales.
    - Para empresas o proveedores de servicios, puede habilitar la comunicación de toda la organización con la tecnología VPN segura dentro de Azure.

### <a name="vnet-to-vnet-faq-for-classic-vnets"></a>Preguntas más frecuentes de VNet a VNet para VNets clásico

- Las redes virtuales pueden estar en la mismas u otra suscripciones.

- Las redes virtuales pueden estar en la mismas u otra Azure las regiones (ubicaciones).

- Un servicio de nube o un extremo de equilibrio de carga no puede abarcar redes virtuales, incluso si están conectados juntos.

- Conectar varias redes virtuales no requiere que todos los dispositivos VPN.

- VNet a VNet es compatible con la conexión de redes Virtual de Azure. No admite la conexión máquinas virtuales o servicios que no se implementan en una red virtual de nube.

- VNet a VNet requiere puertas de enlace enrutamiento dinámicos. Azure puertas de enlace enrutamiento estáticos no son compatibles.

- Conectividad de red virtual puede usarse simultáneamente con múltiples sitios VPN. Hay un máximo de 10 túneles VPN de una puerta de enlace de red virtual VPN conectarse a otras redes virtuales o sitios local.

- Los espacios de direcciones de los sitios de red local de redes y local virtuales no deben superponerse. Superposición de espacios de direcciones hará que la creación de redes virtuales o cargar archivos de configuración de netcfg errores.

- No se admiten túneles redundantes entre un par de redes virtuales.

- Todos los túneles VPN para la VNet, incluidos P2S VPN, compartan el ancho de banda de la puerta de enlace VPN y la misma actividad de puerta de enlace VPN SLA en Azure.

- VNet a VNet tráfico atraviesa la espina Azure.


## <a name="step1"></a>Paso 1: planear los intervalos de direcciones IP

Es importante decidir los rangos que se usará para configurar sus redes virtuales. Para esta configuración, debe asegurarse de que ninguno de los rangos de VNet se superponen entre sí o con cualquiera de las redes locales que se conectan a.

En la tabla siguiente se muestra un ejemplo de cómo definir los VNets. Usar los intervalos sólo como orientación. Anote los intervalos de las redes virtuales. Necesitará esta información para pasos posteriores.

**Configuración de ejemplo**

|Red virtual  |Espacio de direcciones               |Región      |Se conecta al sitio de la red local|
|:----------------|:---------------------------|:-----------|:-----------------------------|
|VNet1            |VNet1 (10.1.0.0/16)         |Oeste de Estados Unidos     |VNet2Local (10.2.0.0/16)      |
|VNet2            |VNet2 (10.2.0.0/16)         |Japón oriental  |VNet1Local (10.1.0.0/16)      |
  
## <a name="step-2---create-vnet1"></a>Paso 2: crear VNet1

En este paso, creamos VNet1. Cuando se utiliza cualquiera de los ejemplos, asegúrese de utilizar sus propios valores. Si ya existe la VNet, no es necesario realizar este paso. Pero necesita comprobar que los intervalos de direcciones IP no se superponen con los intervalos de la segunda VNet o con cualquiera de los otros VNets al que desea conectarse.

1. Inicie sesión en el [portal de clásico de Azure](https://manage.windowsazure.com). En este artículo, usamos el portal clásico porque algunas de las opciones de configuración necesarios todavía no están disponibles en el portal de Azure.

2. En la esquina inferior izquierda de la pantalla, haga clic en **nuevo** > **Servicios de red** > **Red Virtual** > **Crear personalizado** para iniciar el Asistente para configuración. Al navegar a través del asistente, agregar los valores especificados en cada página.

### <a name="virtual-network-details"></a>Detalles de la red virtual

En la página Detalles de la red Virtual, escriba la información siguiente:

  ![Detalles de la red virtual](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

  - **Nombre** : nombre de la red virtual. Por ejemplo, VNet1.
  - **Ubicación** : cuando se crea una red virtual, se asocia a una ubicación de Azure (región). Por ejemplo, si desea que sus máquinas virtuales que se implementan en su red virtual que estar ubicada en Estados Unidos occidental, seleccione esa ubicación. No puede cambiar la ubicación asociada a su red virtual después de crearlo.

### <a name="dns-servers-and-vpn-connectivity"></a>Los servidores DNS y conectividad VPN

En la página conectividad VPN y los servidores DNS, escriba la información siguiente y, a continuación, haga clic en la flecha siguiente en la esquina inferior derecha.

  ![Los servidores DNS y conectividad VPN](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg)  

- **Los servidores DNS** , escriba el nombre del servidor DNS y la dirección IP o seleccione un servidor DNS previamente registrado en la lista desplegable. Esta configuración no crear un servidor DNS. Permite especificar los servidores DNS que desea usar para la resolución de nombre para esta red virtual. Si desea tener la resolución de nombres entre sus redes virtuales, debe configurar su propio servidor DNS, en lugar de utilizar la resolución de nombres que se proporciona en Azure.
- No seleccione ninguna de las casillas de verificación para la conectividad P2S o S2S. Haga clic en la flecha situada en la esquina inferior derecha para desplazarse a la siguiente pantalla.

### <a name="virtual-network-address-spaces"></a>Espacios de direcciones de red virtual

En la página Virtual espacios de direcciones de red, especifique el intervalo de direcciones que desee usar para su red virtual. Son las direcciones IP dinámicas (DIP) que se asignará a las máquinas virtuales y otras instancias de la función se implementación en esta red virtual. 

Si está creando un VNet que también tendrá una conexión a su red local, es especialmente importante seleccionar un rango que no se superpone con cualquiera de los rangos que se usan para su red local. En ese caso, debe coordinar con el Administrador de red. El Administrador de red puede necesitar dividir fuera de un intervalo de direcciones IP de su espacio de direcciones de red local para su uso para su VNet.

  ![Página de espacios de direcciones de red virtual](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

  - **Espacio de direcciones** - incluidos IP inicial y el recuento de dirección Compruebe que los espacios de direcciones que especifique no se superponen con cualquiera de los espacios de direcciones que tiene en su red local. En este ejemplo, utilizamos 10.1.0.0/16 para VNet1.
  - **Agregar subred** - incluidos IP inicial y el recuento de dirección Subredes adicionales no son obligatorios, pero desea crear una subred independiente para máquinas virtuales que tendrán DIP estático. O bien, es posible que desee tener sus máquinas virtuales en una subred independiente de las instancias del rol.
 
**Haga clic en la marca de verificación** en la esquina inferior derecha de la página y su red virtual comenzará a crear. Cuando finalice, verá "Creado" que aparece en el estado en la página de redes.

## <a name="step-3---create-vnet2"></a>Paso 3: crear VNet2

A continuación, repita los pasos anteriores para crear otra VNet. En pasos posteriores, se conectará las dos VNets. Puede hacer referencia a la [configuración de ejemplo](#step1) en el paso 1. Si ya existe la VNet, no es necesario realizar este paso. Sin embargo, debe comprobar que los intervalos de direcciones IP no se superponen con cualquiera de las otras redes VNets o local que desea conectarse.

## <a name="step-4---add-the-local-network-sites"></a>Paso 4: agregue los sitios de la red local

Cuando se crea una configuración de VNet a VNet, debe configurar los sitios de la red local, que se muestran en la página de **Redes locales** del portal. Azure usa la configuración especificada en cada sitio de la red local para determinar cómo enrutar el tráfico entre el VNets. Determinar el nombre que desea utilizar para hacer referencia a cada sitio de la red local. Es mejor usar algo descriptivo, como seleccionar el valor de una lista desplegable en pasos posteriores.

Por ejemplo, VNet1 se conecta a un sitio de red local que cree con el nombre "VNet2Local". La configuración de VNet2Local contiene los prefijos de dirección para VNet2 y una dirección IP pública de la puerta de enlace VNet2. VNet2 se conecta a un sitio de red local que cree denominado "VNet1Local" que contiene los prefijos de dirección para VNet1 y la dirección IP pública de la puerta de enlace VNet1.

### <a name="localnet"></a>Agregar el sitio de red local VNet1Local

1. En la esquina inferior izquierda de la pantalla, haga clic en **nuevo** > **Servicios de red** > **Red Virtual** > **Agregar red Local**.

2. En la página **especificar los detalles de su red local** , en **nombre**, escriba un nombre que desea usar para representar la red de la desea conectarse. En este ejemplo, puede usar "VNet1Local" para hacer referencia a los intervalos de direcciones IP y la puerta de enlace para VNet1.

3. **Dirección IP del dispositivo VPN (opcional)**, especifique cualquier dirección IP pública válida. Normalmente, usaría la dirección IP externa real de un dispositivo VPN. Configuraciones de VNet a VNet, use la dirección IP pública que está asignada a la puerta de enlace para su VNet. Pero, dado que aún no ha creado la puerta de enlace, puede especificar cualquier dirección IP pública válida como un marcador de posición. No deje en blanco: no es opcional para esta configuración. En un paso posterior, vuelva a esta configuración y configurarlos con las direcciones IP de puerta de enlace correspondientes una vez Azure genera. Haga clic en la flecha para avanzar a la siguiente pantalla.

4. En la **página Especifique el dirección**, escriba el número de intervalo y dirección de la dirección IP de VNet1. Debe corresponder exactamente al rango que está configurado para VNet1. Azure usa los intervalos de direcciones IP que especifique para enrutar el tráfico destinado VNet1. Haga clic en la marca de verificación para crear la red local.

### <a name="add-the-local-network-site-vnet2local"></a>Agregar el sitio de red local VNet2Local

Siga los pasos anteriores para crear el sitio de la red local "VNet2Local". Puede hacer referencia a los valores de la [configuración de ejemplo](#step1) en el paso 1, si es necesario.

### <a name="configure-each-vnet-to-point-to-a-local-network"></a>Configurar cada VNet para que apunten a una red local

Cada VNet debe apuntar a la red local respectiva que desee para enrutar el tráfico a. 

#### <a name="for-vnet1"></a>Para VNet1

1. Vaya a la página **Configurar** una red virtual **VNet1**. 
2. En conectividad de sitio a sitio, seleccione "Conectarse a la red local" y, a continuación, seleccione **VNet2Local** como la red local en la lista desplegable. 
3. Guardar la configuración.

#### <a name="for-vnet2"></a>Para VNet2

1. Vaya a la página **Configurar** una red virtual **VNet2**. 
2. En conectividad de sitio a sitio, seleccione "Conectar a la red local", a continuación, seleccione **VNet1Local** en la lista desplegable como la red local. 
3. Guardar la configuración.

## <a name="step-5---configure-a-gateway-for-each-vnet"></a>Paso 5: configurar una puerta de enlace para cada VNet

Configurar una puerta de enlace enrutamiento dinámico para cada red virtual. Esta configuración no admite enrutamiento estático puertas de enlace. Si está utilizando VNets que se han configurado previamente y que dispone de puertas de enlace enrutamiento dinámico, no es necesario realizar este paso. Si las puertas de enlace son enrutamiento estático, debe elimínelas y créelas de nuevo como puertas de enlace enrutamiento dinámico. Si elimina una puerta de enlace, la dirección IP pública asignada obtiene publicada y necesita volver atrás y volver a configurar cualquiera de sus redes locales y dispositivos VPN con la nueva dirección IP pública para la nueva puerta de enlace.

1. En la página de **redes** , compruebe que la columna Estado para su red virtual es **creado**.

2. En la columna **nombre** , haga clic en el nombre de su red virtual. En este ejemplo, usamos "VNet1".

3. En la página de **panel** , tenga en cuenta que esta VNet no dispone de una puerta de enlace ha configurado. Verá este estado Cambiar a medida que vaya a través de los pasos para configurar la puerta de enlace.

4. En la parte inferior de la página, haga clic en **Crear la puerta de enlace** y **Enrutamiento dinámico**. Cuando el sistema le pida que confirme que desea que la puerta de enlace creado, haga clic en Sí.

    ![Tipo de puerta de enlace](./media/virtual-networks-configure-vnet-to-vnet-connection/IC717026.png)  

5. Cuando se cree la puerta de enlace, observe el gráfico de la puerta de enlace en la página cambia a amarillo y dice "Creación de puerta de enlace". Normalmente, se necesitan unos 30 minutos para crear la puerta de enlace.

6. Repita los mismos pasos para VNet2. No es necesario la primera puerta de enlace VNet para completar antes de empezar a crear la puerta de enlace para los demás VNet.

7. Cuando se cambia el estado de la puerta de enlace a "Conectando", la dirección IP pública para cada puerta de enlace está visible en el panel. Anote la dirección IP que corresponde a cada VNet, con cuidado de no para mezclar hacia arriba. Son las direcciones IP que se usan al editar las direcciones IP de marcador de posición para el dispositivo VPN para cada red local.

## <a name="step-6---edit-the-local-network"></a>Paso 6: modificar la red local

1. En la página **Redes locales** , haga clic en el nombre de la red Local que desea editar, haga clic en **Editar** en la parte inferior de la página. Para **dirección IP del dispositivo VPN**, introduzca la dirección IP de la puerta de enlace que corresponde a la VNet. Por ejemplo, para VNet1Local, coloque la dirección IP de puerta de enlace asignado a VNet1. A continuación, haga clic en la flecha situada en la parte inferior de la página.

2. En la página **especificar el espacio de direcciones** , haga clic en la marca de verificación en la esquina inferior derecha sin realizar cambios.

## <a name="step-7---create-the-vpn-connection"></a>Paso 7: crear la conexión VPN

Cuando se han completado todos los pasos anteriores, establezca las claves previamente compartidas de IPsec/IKE y crear la conexión. Este conjunto de pasos usa PowerShell y no se puede configurar en el portal. Vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener más información sobre la instalación de los cmdlets de PowerShell de Azure. Asegúrese de descargar la última versión de los cmdlets de administración de servicio (SM). 

1. Abrir Windows PowerShell e inicie sesión.

        Add-AzureAccount

2. Seleccione la suscripción que su VNets residen en.

        Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
        Select-AzureSubscription -SubscriptionName "<Subscription Name>"

3. Crear las conexiones. En los ejemplos, observe que la clave compartida es exactamente la misma. Siempre debe coincidir con la clave compartida.


    VNet1 a VNet2 conexión

        Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2Local -SharedKey A1b2C3D4

    VNet2 a VNet1 conexión

        Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1Local -SharedKey A1b2C3D4

4. Espere a que las conexiones iniciar. Una vez que ha inicializado la puerta de enlace, la puerta de enlace es similar a la ilustración siguiente.

    ![Estado de la puerta de enlace - conectado](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)  

    [AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)] 

## <a name="next-steps"></a>Pasos siguientes

Puede agregar máquinas virtuales con sus redes virtuales. Consulte la [documentación de máquinas virtuales de Windows](https://azure.microsoft.com/documentation/services/virtual-machines/) para obtener más información.



[1]: ../hdinsight-hbase-geo-replication-configure-vnets.md
[2]: http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Configure-the-VPN-connectivity-between-two-Azure-virtual-networks
 
