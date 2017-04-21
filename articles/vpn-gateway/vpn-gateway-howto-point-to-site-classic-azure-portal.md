<properties
   pageTitle="Configurar una conexión de puerta de enlace VPN de sitio a punto a una red Virtual de Azure con el portal de Azure | Microsoft Azure"
   description="Conectar forma segura a su red Virtual de Azure mediante la creación de una conexión de puerta de enlace VPN de sitio a punto con el portal de Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="cherylmc"/>

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Configurar una conexión punto a sitio para un VNet con el portal de Azure

> [AZURE.SELECTOR]
- [Administrador de recursos - Portal de Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Administrador de recursos - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Clásico: Portal de Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

En este artículo le guiará a través de la creación de un VNet con una conexión punto a sitio en el modelo de implementación clásica con el portal de Azure. Configuración de un punto de sitio (P2S) le permite crear una conexión segura desde un equipo cliente individuales a una red virtual. Una conexión de P2S es útil cuando desea conectarse a su VNet desde una ubicación remota, como desde casa o de una conferencia. O bien, cuando sólo tiene unos pocos clientes que necesitan conectarse a una red virtual.

Conexiones de punto a sitio no requieren un dispositivo VPN o una dirección IP de público para que funcione. Establecer una conexión VPN iniciando la conexión desde el equipo cliente. Para obtener más información acerca de las conexiones de punto a sitios, consulte las [P+F de puerta de enlace de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) y [Acerca de la puerta de enlace VPN](vpn-gateway-about-vpngateways.md#point-to-site).

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos de implementación y métodos para las conexiones P2S

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

La siguiente tabla muestra los métodos de implementación disponibles para las configuraciones de P2S y dos modelos de implementación. Cuando esté disponible un artículo con pasos de configuración, nos vincularlo directamente desde esta tabla.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Flujo de trabajo básico 

![Punto-al-sitio-diagrama] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "sitio de punto")


Las secciones siguientes le guiará por los pasos para crear una conexión de sitio a punto segura a una red virtual. 

1. Crear una red virtual y la puerta de enlace VPN
2. Generar certificados
3. Cargue el archivo .cer
4. Generar el paquete de configuración de cliente VPN
5. Configurar el equipo cliente
6. Conectarse a Azure

### <a name="example-settings"></a>Configuración de ejemplo

Puede usar la configuración de ejemplo siguientes:

- **Nombre: VNet1**
- **Espacio de direcciones: 192.168.0.0/16**
- **Nombre de subred: front-end**
- **Intervalo de direcciones de subred: 192.168.1.0/24**
- **Suscripción:** Si tiene más de una suscripción, compruebe que está usando correcto.
- **Grupo de recursos: TestRG**
- **Ubicación: Estados Unidos oriental**
- **Tipo de conexión: punto al sitio**
- **Espacio de direcciones de cliente: 172.16.201.0/24**. Clientes VPN que se conecten a la VNet usando una conexión punto a sitio recibir una dirección IP desde el grupo especificado.
- **GatewaySubnet: 192.168.200.0/24**. La subred de puerta de enlace debe utilizar el nombre "GatewaySubnet".
- **Tamaño:** Seleccione la puerta de enlace SKU que desea usar.
- **Tipo de distribución: dinámico**

## <a name="vnetvpn"></a>Sección 1: crear una red virtual y una puerta de enlace VPN

### <a name="createvnet"></a>Parte 1: Crear una red virtual

Si todavía no tiene una red virtual, crear uno. Capturas de pantalla se proporcionan ejemplos. Asegúrese de reemplazar los valores con su propio. Para crear un VNet con el portal de Azure, realice los siguientes pasos: 

1. Desde un explorador, vaya al [portal de Azure](http://portal.azure.com) y, si es necesario, inicie sesión con su cuenta de Azure.

2. Haga clic en **nuevo**. En el campo **el catálogo de soluciones de búsqueda** , escriba "Red Virtual". Busque **Una red Virtual** de la lista devuelta y haga clic para abrir el módulo de **Red Virtual** .

    ![Busque el módulo de red virtual] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png "Busque el módulo de red virtual")

3. En la parte inferior del módulo de red Virtual, en la lista **Seleccionar un modelo de implementación** , seleccione **clásico**y, a continuación, haga clic en **crear**.

    ![Seleccione el modelo de implementación] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png "Seleccione el modelo de implementación")

4. En el módulo de **crear una red virtual** , configure las opciones de VNet. En este módulo, deberá agregar el primer espacio de direcciones y un intervalo de direcciones de subred única. Cuando termine de crear el VNet, puede volver atrás y agregar subredes adicionales y espacios de direcciones.

    ![Módulo de crear una red virtual] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png "Módulo de crear una red virtual")

5. Compruebe que la **suscripción** es la correcta. Puede cambiar las suscripciones mediante la lista desplegable.

6. Haga clic en **agrupar recursos** y seleccione un grupo de recursos existente o cree uno nuevo, escriba un nombre para el nuevo grupo de recursos. Si va a crear un grupo nuevo, el nombre del grupo de recursos según sus valores de configuración planeada. Para obtener más información acerca de los grupos de recursos, visite [Información general del Administrador de recursos de Azure](azure-resource-manager/resource-group-overview.md#resource-groups).

7. A continuación, seleccione la configuración de la **ubicación** de su VNet. Determina la ubicación donde se guardarán los recursos que se implementación en este VNet.

8. Seleccione **Anclar al panel** si desea que puedan encontrar su VNet fácilmente en el panel y, a continuación, haga clic en **crear**.
    
    ![Anclar a paneles] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png "Anclar a paneles")


9. Después de hacer clic en crear, verá un icono en el panel que refleje el progreso de su VNet. El mosaico cambia a medida que se va a crear la VNet.

    ![Crear una red virtual de mosaico] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Crear una red virtual de mosaico")

10. Después de crear la red virtual, puede agregar la dirección IP de un servidor DNS para controlar la resolución de nombres. Abra la configuración de su red virtual, haga clic en los servidores DNS y agregue la dirección IP del servidor DNS que desee usar. Esta configuración no se crea un nuevo servidor DNS. Asegúrese de agregar un servidor DNS que se pueden comunicar con los recursos.

Una vez que se ha creado su red virtual, verá **que creado** aparece en el **estado** en la página de redes en el portal de clásico de Azure.

### <a name="gateway"></a>Parte 2: Crear una puerta de enlace enrutamiento dinámico y de subred de puerta de enlace

En este paso, creará una subred de puerta de enlace y una puerta de enlace enrutamiento dinámico. En el portal de Azure para el modelo de implementación clásica, la creación de la subred de puerta de enlace y la puerta de enlace puede hacerse a través de la misma módulos de configuración.

1. En el portal, vaya a la red virtual para el que desea crear una puerta de enlace.

2. En el módulo para su red virtual, en el módulo de **información general** , en la sección de conexiones VPN, haga clic en **puerta de enlace**.

    ![Haga clic aquí para crear una puerta de enlace] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png "Haga clic aquí para crear una puerta de enlace")


3. En el módulo de **Nueva conexión VPN** , seleccione punto al sitio de ****.

    ![Tipo de conexión P2S] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png "Tipo de conexión P2S")

4. Para el **Espacio de direcciones de cliente**, agregue el intervalo de direcciones IP. Esto es el rango de que los clientes VPN reciben una dirección IP al conectarse. Eliminar el rango de relleno automático y agregar los suyos propios.

    ![Espacio de direcciones de cliente] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png "Espacio de direcciones de cliente")

5. Seleccione la casilla de verificación **crear inmediatamente la puerta de enlace** .

    ![Crear inmediatamente la puerta de enlace] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png "Crear inmediatamente la puerta de enlace")

6. Haga clic en **configuración de puerta de enlace opcional** para abrir el módulo de **configuración de puerta de enlace** .

    ![Haga clic en configuración de puerta de enlace opcional] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png "Haga clic en configuración de puerta de enlace opcional")

7. Haga clic en **Configurar subred configuración necesaria** para agregar la **subred de puerta de enlace**. Si es posible crear una subred de puerta de enlace tan pequeños como /29, le recomendamos que cree una subred mayor que incluye más direcciones seleccionando al menos /28 o /27. Esto le permitirá para que las direcciones de suficiente acomodar posibles configuraciones adicionales que desee en el futuro.

    >[AZURE.IMPORTANT] Cuando trabaje con subredes de puerta de enlace, evite asociar un grupo de seguridad de red (GSN) a la subred de puerta de enlace. Asociar un grupo de seguridad de la red para esta subred puede hacer que la puerta de enlace VPN deje de funcionar según lo esperado. Para obtener más información acerca de los grupos de seguridad de red, consulte [¿Qué es un grupo de seguridad de red?](../articles/virtual-network/virtual-networks-nsg.md)

    ![Agregar GatewaySubnet] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png "Agregar GatewaySubnet")

8. Seleccione el **tamaño**de la puerta de enlace. Esta es la puerta de enlace SKU que usará para crear la puerta de enlace de red virtual. En el portal, el SKU predeterminado es **básico**. Para obtener más información acerca de la puerta de enlace SKU, consulte [Acerca de la configuración de puerta de enlace VPN](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![tamaño de la puerta de enlace](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)

9. Seleccione el **Tipo de enrutamiento** de la puerta de enlace. Configuraciones de P2S requieren un tipo de distribución **dinámico** . Haga clic en **Aceptar** cuando haya terminado de configurar este módulo.

    ![Configurar tipo de enrutamiento] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png "Configurar tipo de enrutamiento")

10. En el módulo de **Nueva conexión VPN** , haga clic en **Aceptar** en la parte inferior del módulo para empezar a crear la puerta de enlace de red virtual. Esto puede tardar hasta 45 minutos en completarse. 


## <a name="generatecerts"></a>Sección 2: generar certificados

Azure utilizan certificados para autenticar a clientes VPN de sitio a punto VPN. Exportar datos de certificado público (no la clave privada) como Base 64 codificado X.509 .cer archivo desde un certificado de raíz generado por una solución de certificado de empresa o un certificado autofirmado raíz. A continuación, importar los datos del certificado público desde el certificado raíz a Azure. Además, debe generar un certificado de cliente desde el certificado raíz para clientes. Cada cliente que quiere conectarse a la red virtual mediante una conexión de P2S debe tener un certificado de cliente generado desde el certificado raíz.

### <a name="cer"></a>Parte 1: Obtener el archivo .cer del certificado raíz


Si está utilizando una solución empresarial, puede usar la cadena de certificados existente. Si no está usando una solución de entidad emisora de certificados de empresa, puede crear un certificado autofirmado raíz. Un método para crear un certificado autofirmado es makecert.

- Si está utilizando un sistema de certificados de empresa, obtenga el archivo .cer del certificado raíz que desea usar. 

- Si no está utilizando una solución de certificado de empresa, debe generar un certificado autofirmado raíz. Para conocer los pasos de Windows 10, puede hacer referencia a [trabajar con los certificados raíz firmados para las configuraciones de sitio de punto](vpn-gateway-certificates-point-to-site.md).

1. Para obtener un certificado de un archivo .cer, abra **certmgr.msc** y busque el certificado raíz. Haga clic en el certificado autofirmado raíz, haga clic en **todas las tareas**y, a continuación, haga clic en **Exportar**. Se abre el **Asistente para exportación de certificados**.

2. En el asistente, haga clic en **siguiente**, seleccione **No, no exportar la clave privada**y, a continuación, haga clic en **siguiente**.

3. En la página de **Formato de archivo de exportación** , seleccione **Base 64 codificado X.509 (. CER).** A continuación, haga clic en **siguiente**. 

4. En el **archivo de exportación**, **busque** la ubicación a la que desea exportar el certificado. Para **nombre de archivo**, el nombre del archivo de certificado. A continuación, haga clic en **siguiente**.

5. Haga clic en **Finalizar** para exportar el certificado.


### <a name="genclientcert"></a>Parte 2: Generar un certificado de cliente

Se puede generar un certificado único para cada cliente que se conectará, o puede usar el mismo certificado en varios clientes. La ventaja de generar los certificados de cliente único es la capacidad para revocar un certificado si es necesario. En caso contrario, si todos los usuarios usa el mismo certificado de cliente y descubre que necesita revocar el certificado para un cliente, debe generar e instale nuevos certificados para todos los clientes que usan el certificado para autenticar.

- Si está utilizando una solución de certificado de empresa, generar un certificado de cliente con el formato de valor de nombre común 'name@yourdomain.com', en lugar del formato 'nombre_dominio\nombre_usuario'. 

- Si está utilizando un certificado autofirmado, vea [trabajar con los certificados raíz firmados para las configuraciones de sitio de punto](vpn-gateway-certificates-point-to-site.md) para generar un certificado de cliente.

### <a name="exportclientcert"></a>Parte 3: Exportar el certificado de cliente

Instalar un certificado de cliente en cada PC en el que desea conectarse a la red virtual. Se requiere para la autenticación un certificado de cliente. Automatizar la instalación del certificado de cliente, o puede instalar manualmente. Los siguientes pasos le guiarán a través de exportar e instalar el certificado de cliente de forma manual.

1. Para exportar un certificado de cliente, puede usar *certmgr.msc*. Haga clic en el certificado de cliente que desea exportar, haga clic en **todas las tareas**y, a continuación, haga clic en **Exportar**.
2. Exporte el certificado de cliente con la clave privada. Se trata de un archivo *.pfx* . Asegúrese de recordar la contraseña (clave) que ha configurado para este certificado o grabar.

## <a name="upload"></a>Sección 3: cargar el archivo .cer del certificado raíz

Después de la puerta de enlace se ha creado, puede cargar el archivo .cer de certificados raíz de confianza en Azure. Puede cargar archivos de hasta 20 certificados raíz. No cargar la clave privada para el certificado raíz en Azure. Una vez que se carga el archivo .cer, Azure usa para autenticar a los clientes que se conectan a la red virtual.

1. En la sección **conexiones VPN** del módulo para su VNet, haga clic en el gráfico de **los clientes** para abrir el **punto a sitio VPN conexión** módulo.

    ![Clientes] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png "Clientes")

2. En la conexión punto a sitios **** módulo, haga clic en **Administrar certificados** para abrir el módulo de **certificados** .<br>

    ![Módulo de certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png "Certificates blade")<br><br>


3. En el módulo de **certificados** , haga clic en **cargar** para abrir el módulo de **cargar el certificado** .<br>

    ![Cargar el módulo de certificados](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png "Upload certificates blade")<br>


4. Haga clic en el gráfico de carpeta para buscar el archivo .cer. Seleccione el archivo y luego haga clic en **Aceptar**. Actualice la página para ver el certificado cargado en el módulo de **certificados** .

    ![Cargar el certificado](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png "Upload certificate")<br>
    

## <a name="vpnclientconfig"></a>Sección 4: generar el paquete de configuración de cliente VPN

Para conectarse a la red virtual, también debe configurar a un cliente VPN. El equipo cliente requiere un certificado de cliente y el paquete de configuración de cliente VPN adecuado para conectarse.

El paquete de cliente VPN contiene información de configuración para configurar el software de cliente VPN integrado en Windows. El paquete no instala software adicional. La configuración es específica de la red virtual que desea conectarse. Para la lista de sistemas operativos admitidos, vea las conexiones de punto a sitios [](vpn-gateway-vpn-faq.md#point-to-site-connections) sección de la puerta de enlace VPN, preguntas más frecuentes. 

### <a name="to-generate-the-vpn-client-configuration-package"></a>Para generar el paquete de configuración de cliente VPN

1. En el portal de Azure, en el módulo de **Introducción** para su VNet en **las conexiones VPN**, haga clic en el gráfico de cliente para abrir el **punto a sitio VPN conexión** módulo.
2. En la parte superior de la **punto a sitio VPN conexión** módulo, haga clic en el paquete de descarga que corresponda al sistema operativo de cliente en el que se van a instalar:

 - Para los clientes de 64 bits, seleccione **El cliente VPN (64 bits)**.
 - Para los clientes de 32 bits, seleccione **El cliente VPN (32 bits)**.

    ![Descargue el paquete de configuración de cliente VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png "Download VPN client configuration package")<br>


3. Verá un mensaje que Azure genera el paquete de configuración de cliente VPN de la red virtual. Después de unos minutos, se genera el paquete y se verá un mensaje en el equipo local que se ha descargado el paquete. Guarde el archivo de paquete de configuración. Se instalará en cada equipo cliente que se conecte a la red virtual con P2S.


## <a name="clientconfiguration"></a>Sección 5: configurar el equipo cliente

### <a name="part-1-install-the-client-certificate"></a>Parte 1: Instalar el certificado de cliente

Cada equipo cliente debe tener un certificado de cliente para autenticar. Cuando instala el certificado de cliente, necesitará la contraseña que se creó cuando se exporta el certificado de cliente.

1. Copie el archivo .pfx en el equipo cliente.
2. Haga doble clic en el archivo .pfx para instalarlo. No modifique la ubicación de instalación.

### <a name="part-2-install-the-vpn-client-configuration-package"></a>Parte 2: Instalar el paquete de configuración de cliente VPN

Puede utilizar el mismo paquete de configuración de cliente VPN en cada equipo cliente, siempre que la versión coincide con la arquitectura del cliente.

1. Copie el archivo de configuración localmente en el equipo que va a conectarse a una red virtual y haga doble clic en el archivo .exe. 

2. Una vez instalado el paquete, puede iniciar la conexión VPN. El paquete de configuración no está firmado por Microsoft. Puede desea firmar el paquete mediante el servicio de firmas de su organización o firmar mediante [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327). Es usar el paquete sin iniciar sesión. Sin embargo, si el paquete no se ha iniciado sesión, aparece una advertencia cuando instale el paquete.

3. En el equipo cliente, vaya a **Configuración de la red** y haga clic en **VPN**. Verá la conexión que aparece. Muestra el nombre de la red virtual que se conectará y tendrá un aspecto similar a este: 

    ![Cliente VPN] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png "Cliente VPN VNet")

## <a name="connect"></a>Sección 6 - conectarse a Azure

### <a name="connect-to-your-vnet"></a>Conectarse a su VNet

1. Para conectarse a su VNet, en el equipo cliente, vaya a las conexiones VPN y busque la conexión VPN que ha creado. Se denomina el mismo nombre que su red virtual. Haga clic en **Conectar**. Puede aparecer un mensaje emergente que hace referencia a utilizando el certificado. Si esto sucede, haga clic en **continuar** para usar privilegios elevados. 

2. En la página de estado de **conexión** , haga clic en **Conectar** para iniciar la conexión. Si ve una pantalla **Seleccionar certificado** , compruebe que el certificado de cliente que muestra es el que desea usar para conectarse. Si no es así, use la flecha de lista desplegable para seleccionar el certificado correcto y, a continuación, haga clic en **Aceptar**.

    ![Conectar] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png "Conexión de cliente VPN")

3. Ahora debe establecerse la conexión.

    ![Conexión establecido] (./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png "Establecer la conexión")

### <a name="verify-the-vpn-connection"></a>Compruebe la conexión VPN

1. Para comprobar que la conexión VPN está activa, abra un símbolo del sistema con privilegios elevados y ejecutar *ipconfig/all*.
2. Ver los resultados. Observe que la dirección IP que ha recibido es una de las direcciones dentro del rango de direcciones de conectividad punto al sitio que especificó al crear la VNet. Los resultados deben ser algo parecido a este:

Ejemplo:



    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

## <a name="next-steps"></a>Pasos siguientes

Puede agregar máquinas virtuales a su red virtual. Aprenda [a crear una máquina virtual personalizada](../virtual-machines/virtual-machines-windows-classic-createportal.md).