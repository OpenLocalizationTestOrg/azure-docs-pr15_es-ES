<properties 
   pageTitle="Configurar una conexión de puerta de enlace VPN de sitio a punto a una red virtual mediante el modelo de implementación de administrador de recursos y el portal de Azure | Microsoft Azure"
   description="Conectar forma segura a su red Virtual de Azure mediante la creación de una conexión de puerta de enlace VPN de sitio a punto con el Administrador de recursos y el portal de Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="cherylmc" />

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Configurar una conexión punto a sitio para un VNet con el Portal de Azure

> [AZURE.SELECTOR]
- [Administrador de recursos - Portal de Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Administrador de recursos - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Clásico: Portal de Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Configuración de un punto de sitio (P2S) le permite crear una conexión segura desde un equipo cliente individuales a una red virtual. Una conexión de P2S es útil cuando desea conectarse a su VNet desde una ubicación remota, como desde casa o de una conferencia, o solo tiene unos pocos clientes que necesitan conectarse a una red virtual. 

Conexiones de punto a sitio no requieren un dispositivo VPN o una dirección IP de público para que funcione. Establecer una conexión VPN iniciando la conexión desde el equipo cliente. Para obtener más información acerca de las conexiones de punto a sitios, consulte las [P+F de puerta de enlace de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) y la [planificación y diseño](vpn-gateway-plan-design.md).

En este artículo le guiará a través de la creación de un VNet con una conexión punto a sitio en el modelo de implementación de administrador de recursos con el portal de Azure.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos de implementación y métodos para las conexiones P2S

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

La siguiente tabla muestra los métodos de implementación disponibles para las configuraciones de P2S y dos modelos de implementación. Cuando esté disponible un artículo con pasos de configuración, nos vincularlo directamente desde esta tabla.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Flujo de trabajo básico 

![Punto-al-sitio-diagrama] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "sitio de punto")

### <a name="example"></a>Valores de ejemplo

- **Nombre: VNet1**
- **Espacio de direcciones: 192.168.0.0/16**<br>En este ejemplo, usamos un solo espacio de direcciones. Puede tener más de un espacio de direcciones para su VNet.
- **Nombre de subred: front-end**
- **Intervalo de direcciones de subred: 192.168.1.0/24**
- **Suscripción:** Si tiene más de una suscripción, compruebe que está usando correcto.
- **Grupo de recursos: TestRG**
- **Ubicación: Estados Unidos oriental**
- **GatewaySubnet: 192.168.200.0/24**
- **Nombre de puerta de enlace de red virtual: VNet1GW**
- **Tipo de puerta de enlace: VPN**
- **Tipo de VPN: basado en la ruta**
- **Dirección IP pública: VNet1GWpip**
- **Tipo de conexión: punto al sitio**
- **Grupo de direcciones de cliente: 172.16.201.0/24**<br>Clientes VPN que se conecten a la VNet usando una conexión punto a sitio recibir una dirección IP del grupo de direcciones de cliente.

## <a name="before-beginning"></a>Antes de comenzar

- Compruebe que tiene una suscripción de Azure. Si todavía no tiene una suscripción de Azure, puede activar los [beneficios de suscriptor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o signo hacia arriba para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).
    
## <a name="createvnet"></a>Parte 1: crear una red virtual

Si va a crear esta configuración como ejercicio, puede hacer referencia a los [valores de ejemplo](#example).

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

### <a name="2-add-additional-address-space-and-subnets"></a>2. agregar subredes y el espacio de direcciones adicionales

Puede agregar espacio adicional de direcciones y subredes a su VNet una vez que se ha creado.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)] 

### <a name="3-create-a-gateway-subnet"></a>3. crear una subred de puerta de enlace

Antes de conectar su red virtual a una puerta de enlace, debe crear la subred de puerta de enlace de la red virtual al que desea conectarse. Si es posible, es mejor crear una subred de puerta de enlace con un bloque CIDR de /28 o /27 para proporcionar suficientes direcciones IP para adaptarse a requisitos adicionales configuración futura.

Las capturas de pantalla en esta sección se proporcionan como un ejemplo de referencia. Asegúrese de usar el intervalo de direcciones de GatewaySubnet que se corresponda con los valores requeridos para su configuración.

#### <a name="to-create-a-gateway-subnet"></a>Para crear una subred de puerta de enlace

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="dns"></a>4. Especifique un servidor DNS (opcional)

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>Parte 2: crear una puerta de enlace de red virtual

Conexiones de punto a sitio requieren la siguiente configuración:

- Tipo de puerta de enlace: VPN
- Tipo de VPN: basado en la ruta

### <a name="to-create-a-virtual-network-gateway"></a>Para crear una puerta de enlace de red virtual

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generatecert"></a>Parte 3: generar certificados

Azure utilizan certificados para autenticar a clientes VPN de sitio a punto VPN. Exportar datos de certificado público (no la clave privada) como Base 64 codificado X.509 .cer archivo desde un certificado de raíz generado por una solución de certificado de empresa o un certificado autofirmado raíz. A continuación, importar los datos del certificado público desde el certificado raíz a Azure. Además, debe generar un certificado de cliente desde el certificado raíz para clientes. Cada cliente que quiere conectarse a la red virtual mediante una conexión de P2S debe tener un certificado de cliente generado desde el certificado raíz.

### <a name="getcer"></a>1. obtener el archivo .cer del certificado raíz

Si está utilizando una solución empresarial, puede usar la cadena de certificados existente. Si no está usando una solución de entidad emisora de certificados de empresa, puede crear un certificado autofirmado raíz. Un método para crear un certificado autofirmado es makecert.

- Si está utilizando un sistema de certificados de empresa, obtenga el archivo .cer del certificado raíz que desea usar. 

- Si no está utilizando una solución de certificado de empresa, debe generar un certificado autofirmado raíz. Para conocer los pasos de Windows 10, puede hacer referencia a [trabajar con los certificados raíz firmados para las configuraciones de sitio de punto](vpn-gateway-certificates-point-to-site.md).

1. Para obtener un certificado de un archivo .cer, abra **certmgr.msc** y busque el certificado raíz. Haga clic en el certificado autofirmado raíz, haga clic en **todas las tareas**y, a continuación, haga clic en **Exportar**. Se abre el **Asistente para exportación de certificados**.

2. En el asistente, haga clic en **siguiente**, seleccione **No, no exportar la clave privada**y, a continuación, haga clic en **siguiente**.

3. En la página de **Formato de archivo de exportación** , seleccione **Base 64 codificado X.509 (. CER).** A continuación, haga clic en **siguiente**. 

4. En el **archivo de exportación**, **busque** la ubicación a la que desea exportar el certificado. Para **nombre de archivo**, el nombre del archivo de certificado. A continuación, haga clic en **siguiente**.

5. Haga clic en **Finalizar** para exportar el certificado.

### <a name="generateclientcert"></a>2. generar un certificado de cliente

Se puede generar un certificado único para cada cliente que se conectará, o puede usar el mismo certificado en varios clientes. La ventaja de generar los certificados de cliente único es la capacidad para revocar un certificado si es necesario. En caso contrario, si todos los usuarios usa el mismo certificado de cliente y descubre que necesita revocar el certificado para un cliente, debe generar e instale nuevos certificados para todos los clientes que usan el certificado para autenticar.

- Si está utilizando una solución de certificado de empresa, generar un certificado de cliente con el formato de valor de nombre común 'name@yourdomain.com', en lugar del formato 'nombre_dominio\nombre_usuario'. 

- Si está utilizando un certificado autofirmado, vea [trabajar con los certificados raíz firmados para las configuraciones de sitio de punto](vpn-gateway-certificates-point-to-site.md) para generar un certificado de cliente.

### <a name="exportclientcert"></a>3. exporte el certificado de cliente

Se requiere para la autenticación un certificado de cliente. Después de generar el certificado de cliente, exportarlo. Exportar el certificado de cliente se instalará más adelante en cada equipo cliente.

1. Para exportar un certificado de cliente, puede usar *certmgr.msc*. Haga clic en el certificado de cliente que desea exportar, haga clic en **todas las tareas**y, a continuación, haga clic en **Exportar**.
2. Exporte el certificado de cliente con la clave privada. Se trata de un archivo *.pfx* . Asegúrese de recordar la contraseña (clave) que ha configurado para este certificado o grabar.

## <a name="addresspool"></a>Parte 4: agregar el grupo de direcciones de cliente

1. Una vez creada la puerta de enlace de red virtual, vaya a la sección **configuración** de la hoja de puerta de enlace de red virtual. En la sección **configuración** , haga clic en configuración de punto a sitios **** para abrir la hoja de **configuración** .

    ![Seleccione el módulo de sitio] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png "Seleccione el módulo de sitio")

2. **Grupo de dirección** es el grupo de direcciones IP desde la que los clientes que se conectan recibirán una dirección IP. Agregar el grupo de direcciones y, a continuación, haga clic en **Guardar**.

    ![grupo de direcciones de cliente] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/addresspool.png "grupo de direcciones de cliente")

## <a name="uploadfile"></a>Parte 5: cargar el archivo .cer del certificado raíz

Después de la puerta de enlace se ha creado, puede cargar el archivo .cer de certificados raíz de confianza en Azure. Puede cargar archivos de hasta 20 certificados raíz. No cargar la clave privada para el certificado raíz en Azure. Una vez que se carga el archivo .cer, Azure usa para autenticar a los clientes que se conectan a la red virtual.

1. Vaya a la configuración de punto a sitios **** módulo. Agregará los archivos .cer en la sección de **certificado raíz** de este módulo.

    ![Seleccione el módulo de sitio] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcert.png "Seleccione el módulo de sitio")

2. Asegúrese de que exportar el certificado raíz como Base 64 codificado archivo X.509 (.cer). Debe exportar en este formato para que pueda abrir el certificado con el editor de texto.
3. Abra el certificado con un editor de texto, como el Bloc de notas. Copiar solo la sección siguiente:

    ![datos de certificado] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png "datos de certificado")

4. Pegue los datos del certificado en la sección de **Datos de certificado público** del portal. Poner el nombre del certificado en el espacio de **nombres** y, a continuación, haga clic en **Guardar**. Puede agregar hasta 20 certificados raíz de confianza.

    ![Cargar certificado] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploadcert.png "Cargar certificado")

## <a name="clientconfig"></a>Parte 6: descargar e instalar el paquete de configuración de cliente VPN

Clientes que se conectan a Azure con P2S deben tener un certificado de cliente y de un paquete de configuración de cliente VPN instalado. Paquetes de configuración de cliente VPN están disponibles para los clientes de Windows. 

El paquete de cliente VPN contiene información para configurar el software cliente VPN integrado en Windows. La configuración es específica de la VPN que desea conectarse. El paquete no instala software adicional. Consulte las [Preguntas más frecuentes de puerta de enlace de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) para obtener más información.

1. En la configuración de punto a sitios **** módulo, haga clic en el **cliente VPN descargar** para abrir el módulo de **cliente VPN descargar** .

    ![Descargar el cliente VPN] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadclient.png "Descargar el cliente VPN")

2. Seleccione el paquete correcto para su cliente y luego haga clic en **Descargar**. Para los clientes de 64 bits, seleccione **AMD64**. Para los clientes de 32 bits, seleccione **x86**.

3. Instale el paquete en el equipo cliente. Si recibe un mensaje SmartScreen emergente, haga clic en **más información**, **de todos modos ejecutar** para instalar el paquete.

4. En el equipo cliente, vaya a **Configuración de la red** y haga clic en **VPN**. Verá la conexión que aparece. Se muestran el nombre de la red virtual que se conecta a y un aspecto similar a este ejemplo: 

    ![Cliente VPN] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpn.png "Cliente VPN")

## <a name="installclientcert"></a>Parte 7: instalar el certificado de cliente

Cada equipo cliente debe tener un certificado de cliente para autenticar. Cuando instala el certificado de cliente, necesitará la contraseña que se creó cuando se exporta el certificado de cliente.

1. Copie el archivo .pfx en el equipo cliente.
2. Haga doble clic en el archivo .pfx para instalarlo. No modifique la ubicación de instalación.

## <a name="connect"></a>Parte 8: conectar con Azure

1. Para conectarse a su VNet, en el equipo cliente, vaya a las conexiones VPN y busque la conexión VPN que ha creado. Se denomina el mismo nombre que su red virtual. Haga clic en **Conectar**. Puede aparecer un mensaje emergente que hace referencia a utilizando el certificado. Si esto sucede, haga clic en **continuar** para usar privilegios elevados. 

2. En la página de estado de **conexión** , haga clic en **Conectar** para iniciar la conexión. Si ve una pantalla **Seleccionar certificado** , compruebe que el certificado de cliente que muestra es el que desea usar para conectarse. Si no es así, use la flecha de lista desplegable para seleccionar el certificado correcto y, a continuación, haga clic en **Aceptar**.

    ![Cliente VPN 2] (./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "Conexión de cliente VPN")

3. Ahora debe establecerse la conexión.

    ![Cliente VPN 3] (./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Conexión de cliente VPN 2")

## <a name="verify"></a>Parte 9: Compruebe la conexión

1. Para comprobar que la conexión VPN está activa, abra un símbolo del sistema con privilegios elevados y ejecutar *ipconfig/all*.

2. Ver los resultados. Observe que la dirección IP que ha recibido es una de las direcciones en el grupo de direcciones de cliente VPN de sitio a punto especificado en la configuración. Los resultados deben ser algo parecido a este:
    
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="add"></a>Para agregar o quitar certificados raíz de confianza

Puede quitar el certificado raíz de confianza de Azure. Al quitar un certificado de confianza, los certificados de cliente que generaron el certificado raíz ya no podrá conectarse a Azure a través del sitio de punto. Si desea que los clientes se conecten, que necesitan instalar un nuevo certificado de cliente que se genera a partir de un certificado de confianza en Azure.

Puede administrar la lista de certificados de cliente revocados en la configuración de punto a sitios **** módulo. Este es el módulo que ha usado para [cargar el certificado raíz de confianza](#uploadfile).

## <a name="revokeclient"></a>Para administrar la lista de certificados de cliente revocados

Puede revocar certificados de cliente. La lista de revocación de certificados le permite denegar selectivamente conectividad punto a un sitio basado en certificados de cliente individuales. Si quita un .cer de certificados raíz de Azure, revoca el acceso para todos los certificados de cliente generados de firmado por el certificado raíz revocados. Si desea revocar un certificado de cliente en particular, no en la raíz, puede hacerlo. De este modo los certificados generados desde el certificado raíz seguirá ser válidos. 

Lo más habitual es usar el certificado raíz para administrar el acceso a los niveles de equipo o la organización, con el uso de certificados de cliente revocados poseen control de acceso de usuarios individuales.

Puede administrar la lista de certificados de cliente revocados en la configuración de punto a sitios **** módulo. Este es el módulo que ha usado para [cargar el certificado raíz de confianza](#uploadfile).


## <a name="next-steps"></a>Pasos siguientes

Puede agregar una máquina virtual a su red virtual. Consulte [crear una máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para los pasos.