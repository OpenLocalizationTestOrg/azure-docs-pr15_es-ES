<properties
   pageTitle="Configurar una conexión de puerta de enlace VPN de sitio a punto a una red Virtual de Azure con el portal clásico | Microsoft Azure"
   description="Conectar forma segura a su red Virtual de Azure mediante la creación de una conexión VPN de sitio a punto de la puerta de enlace."
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

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-classic-portal"></a>Configurar una conexión de sitio a punto a una VNet con el portal clásico

> [AZURE.SELECTOR]
- [Administrador de recursos - Portal de Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Administrador de recursos - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Clásico: Portal de Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
- [Clásico: Portal clásica](vpn-gateway-point-to-site-create.md)

Configuración de un punto de sitio (P2S) le permite crear una conexión segura desde un equipo cliente individuales a una red virtual. Una conexión de P2S es útil cuando desea conectarse a su VNet desde una ubicación remota, como desde casa o de una conferencia, o solo tiene unos pocos clientes que necesitan conectarse a una red virtual.

En este artículo le guiará a través de la creación de un VNet con una conexión punto a sitio en el **modelo de implementación clásica** con el **portal clásico**.

Conexiones de punto a sitio no requieren un dispositivo VPN o una dirección IP de público para que funcione. Establecer una conexión VPN iniciando la conexión desde el equipo cliente. Para obtener más información acerca de las conexiones de punto a sitios, consulte las [P+F de puerta de enlace de VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) y la [planificación y diseño](vpn-gateway-plan-design.md).


### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modelos de implementación y métodos para las conexiones P2S

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

La siguiente tabla muestra los métodos de implementación disponibles para las configuraciones de P2S y dos modelos de implementación. Cuando esté disponible un artículo con pasos de configuración, nos vincularlo directamente desde esta tabla.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 



## <a name="basic-workflow"></a>Flujo de trabajo básico

![Punto-al-sitio-diagrama] (./media/vpn-gateway-point-to-site-create/p2sclassic.png "sitio de punto")
 
Los siguientes pasos le guiará por los pasos para crear una conexión de sitio a punto segura a una red virtual. 

La configuración para una conexión punto a sitio se divide en cuatro secciones. El orden en el que configurar cada una de estas secciones es importante. No omitir pasos o avanzar.

- **Sección 1** Crear una red virtual y la puerta de enlace VPN.
- **Sección 2** Cree los certificados que se utiliza para la autenticación y cargarlos.
- **Sección 3** Exportar e instalar los certificados de cliente.
- **Sección 4** Configurar al cliente VPN.

## <a name="vnetvpn"></a>Sección 1: crear una red virtual y una puerta de enlace VPN

### <a name="part-1-create-a-virtual-network"></a>Parte 1: Crear una red virtual

1. Inicie sesión en el [portal de clásico de Azure](https://manage.windowsazure.com/). Estos pasos utiliza el portal clásico, no el portal de Azure. Actualmente, no puede crear una conexión de P2S con el portal de Azure.

2. En la esquina inferior izquierda de la pantalla, haga clic en **nuevo**. En el panel de navegación, haga clic en **Servicios de red**y, a continuación, haga clic en **Una red Virtual**. Haga clic en **Crear personalizado** para iniciar al Asistente de configuración.

3. En la página **Detalles de la red Virtual** , escriba la información siguiente y, a continuación, haga clic en la flecha siguiente en la esquina inferior derecha.
    - **Nombre**: el nombre de su red virtual. Por ejemplo, 'VNet1'. Este es el nombre que le hacen referencia a implementar máquinas virtuales a este VNet.
    - **Ubicación**: la ubicación directamente relacionada con la ubicación física (región) donde desea que los recursos (VM) resida. Por ejemplo, si desea que las máquinas virtuales que se implementación en esta red virtual que se encuentra en Estados Unidos oriental, seleccione esa ubicación. No puede cambiar la región asociada a su red virtual después de crearlo.

4. En la página **conectividad VPN y los servidores DNS** , escriba la información siguiente y, a continuación, haga clic en la flecha siguiente en la esquina inferior derecha.
    - **Los servidores DNS**: escriba el nombre del servidor DNS y la dirección IP o seleccione un servidor DNS previamente registrado en el menú contextual. Esta configuración no crear un servidor DNS. Permite especificar los servidores DNS que desea usar para la resolución de nombre para esta red virtual. Si desea usar el servicio de resolución de nombres de Azure predeterminado, deje en blanco en esta sección.
    - **Sitio de punto de configurar VPN**: Active la casilla de verificación.

5. En la página de **Sitio de punto de conexión** , especifique el intervalo de direcciones IP desde la que los clientes VPN recibirán una dirección IP cuando esté conectado. Existen algunas reglas sobre los intervalos de direcciones que puede especificar. Es importante comprobar que el rango que especifique no se superpone con cualquiera de los rangos que se encuentra en su red local.

6. Escriba la información siguiente y, a continuación, haga clic en la flecha siguiente.
 - **Espacio de direcciones**: incluir la IP inicial y CIDR (recuento de dirección).
 - **Agregar espacio de direcciones**: agregar espacio de direcciones solo si es necesario para el diseño de la red.

7. En la página **Virtual espacios de direcciones de red** , especifique el intervalo de direcciones que desee usar para su red virtual. Son las direcciones IP dinámicas (DIP) que se asignará a las máquinas virtuales y otras instancias de la función se implementación en esta red virtual.<br><br>Es especialmente importante seleccionar un rango que no se superpone con cualquiera de los rangos que se usan para su red local. Se debe coordinar con el Administrador de red que necesite dividir fuera de un intervalo de direcciones IP de su espacio de direcciones de red local para su uso para su red virtual.

8. Escriba la información siguiente y, a continuación, haga clic en la marca de verificación para empezar a crear su red virtual.
 - **Espacio de direcciones**: agregar el intervalo de direcciones IP interno que desea utilizar para esta red virtual, incluidos IP inicial y el recuento. Es importante seleccionar un rango que no se superpone con cualquiera de los rangos que se usan para su red local. 
 - **Agregar subred**: subredes adicionales no son obligatorios, pero desea crear una subred independiente para máquinas virtuales que tendrán DIP estático. O bien, es posible que desee tener sus máquinas virtuales en una subred independiente de las instancias del rol.
 - **Agregar subred de puerta de enlace**: la subred de puerta de enlace es necesaria para una VPN de sitio a punto. Haga clic para agregar la subred de puerta de enlace. La subred de puerta de enlace se usa solamente para la puerta de enlace de red virtual.

9. Una vez que se ha creado su red virtual, verá **que creado** aparece en el **estado** en la página de redes en el portal de clásico de Azure. Una vez que se ha creado su red virtual, puede crear la puerta de enlace enrutamiento dinámico.

### <a name="part-2-create-a-dynamic-routing-gateway"></a>Parte 2: Crear una puerta de enlace enrutamiento dinámico

El tipo de puerta de enlace debe estar configurado como dinámica. Las puertas de enlace enrutamiento estáticas no funcionan con esta característica.

1. En el portal de clásico Azure, en la página de **redes** , haga clic en la red virtual que ha creado y vaya a la página de **panel** .

2. Haga clic en **Crear la puerta de enlace**, que se encuentra en la parte inferior de la página de **panel** . Aparece un mensaje preguntándole **¿desea crear una puerta de enlace de red virtual "VNet1"**. Haga clic en **Sí** para empezar a crear la puerta de enlace. Puede tardar unos 15 minutos para crear la puerta de enlace.

## <a name="generate"></a>Sección 2: generar y cargar certificados

Los certificados se utilizan para autenticar a clientes VPN de sitio a punto VPN. Puede usar un certificado de raíz generado por una solución de certificado de empresa, o puede usar un certificado autofirmado. Puede cargar hasta 20 certificados raíz en Azure. Una vez que se carga el archivo .cer, Azure puede usar la información contenida en él para autenticar a los clientes que tienen instalado un certificado de cliente. Debe generar el certificado de cliente desde el mismo certificado que representa el archivo .cer.

En esta sección hacer lo siguiente:

- Obtener el archivo .cer para un certificado raíz. Puede ser un certificado autofirmado o puede usar el sistema de certificados de empresa.
- Cargue el archivo .cer en Azure.
- Generar certificados de cliente.

### <a name="root"></a>Parte 1: Obtener el archivo .cer del certificado raíz

Si está utilizando un sistema de certificados de empresa, obtenga el archivo .cer del certificado raíz que desea usar. En la [parte 3](#createclientcert), generar los certificados de cliente desde el certificado raíz.

Si no está utilizando una solución de certificado de empresa, deberá generar un certificado autofirmado raíz. Para conocer los pasos de Windows 10, puede hacer referencia a [trabajar con los certificados raíz firmados para las configuraciones de sitio de punto](vpn-gateway-certificates-point-to-site.md). El artículo le guiará por usando makecert para generar un certificado autofirmado y, a continuación, exportar el archivo .cer.

### <a name="upload"></a>Parte 2: Cargar el archivo de .cer de certificado raíz en el portal de clásico de Azure

Agregar un certificado de confianza en Azure. Cuando agrega un archivo X.509 codificado base 64 (.cer) en Azure, está indicando a Azure que confíe en el certificado raíz que representa el archivo.

1. En el portal de clásico Azure, en la página de **certificados** para su red virtual, haga clic en **cargar un certificado raíz**.

2. En la página **Cargar certificado** , busque el certificado de raíz de .cer y, a continuación, haga clic en la marca de verificación.

### <a name="createclientcert"></a>Parte 3: Generar un certificado de cliente

A continuación, generar los certificados de cliente. Se puede generar un certificado único para cada cliente que se conectará, o puede usar el mismo certificado en varios clientes. La ventaja de generar los certificados de cliente único es la capacidad para revocar un certificado si es necesario. En caso contrario, si todos los usuarios usa el mismo certificado de cliente y descubre que necesita revocar el certificado para un cliente, debe generar e instale nuevos certificados para todos los clientes que usan el certificado para autenticar.

- Si está utilizando una solución de certificado de empresa, generar un certificado de cliente con el formato de valor de nombre común 'name@yourdomain.com', en lugar de formato de la NetBIOS 'Dominio\nombre de usuario'. 

- Si está utilizando un certificado autofirmado, vea [trabajar con los certificados raíz firmados para las configuraciones de sitio de punto](vpn-gateway-certificates-point-to-site.md) para generar un certificado de cliente.

## <a name="installclientcert"></a>Sección 3: exportar e instalar el certificado de cliente

Instalar un certificado de cliente en cada PC en el que desea conectarse a la red virtual. Se requiere para la autenticación un certificado de cliente. Automatizar la instalación del certificado de cliente, o puede instalar manualmente. Los siguientes pasos le guiarán a través de exportar e instalar el certificado de cliente de forma manual.

1. Para exportar un certificado de cliente, puede usar *certmgr.msc*. Haga clic en el certificado de cliente que desea exportar, haga clic en **todas las tareas**y, a continuación, haga clic en **Exportar**.
2. Exporte el certificado de cliente con la clave privada. Se trata de un archivo *.pfx* . Asegúrese de recordar la contraseña (clave) que ha configurado para este certificado o grabar.
3. Copie el archivo *.pfx* en el equipo cliente. En el equipo cliente, haga doble clic en el archivo *.pfx* para instalarlo. Escriba la contraseña cuando se solicita. No modifique la ubicación de instalación.

## <a name="vpnclientconfig"></a>Sección 4: configurar el cliente VPN

Para conectarse a la red virtual, también debe configurar a un cliente VPN. El cliente requiere un certificado de cliente y la configuración de cliente VPN adecuada para conectarse. Para configurar a un cliente VPN, realice los pasos siguientes, en orden.

### <a name="part-1-create-the-vpn-client-configuration-package"></a>Parte 1: Crear el paquete de configuración de cliente VPN

1. En el portal de clásico Azure, en la página de **panel** para su red virtual, vaya al menú vistazo rápido en la esquina derecha. Para la lista de sistemas operativos admitidos, vea las conexiones de punto a sitios [](vpn-gateway-vpn-faq.md#point-to-site-connections) sección de la puerta de enlace VPN, preguntas más frecuentes. El paquete de cliente VPN contiene información de configuración para configurar el software de cliente VPN integrado en Windows. El paquete no instala software adicional. La configuración es específica de la red virtual que desea conectarse.<br><br>Seleccione el paquete de descarga que corresponda al sistema operativo de cliente en el que se van a instalar:
 - Para los clientes de 32 bits, seleccione **descargar el paquete de VPN de cliente de 32 bits**.
 - Para los clientes de 64 bits, seleccione **descargar el paquete de VPN de cliente de 64 bits**.

2. Se necesitan unos minutos para crear el paquete de cliente. Una vez que el paquete se ha completado, puede descargar el archivo. El archivo *.exe* que descargar puede almacenarse de forma segura en el equipo local.

3. Después de generar y descargar el paquete de cliente VPN desde el portal de clásico Azure, puede instalar el paquete de cliente en el equipo cliente desde el que desea conectarse a una red virtual. Si planea instalar el paquete de cliente VPN en varios equipos de cliente, asegúrese de que cada también tienen instalado un certificado de cliente.

### <a name="part-2-install-the-vpn-configuration-package-on-the-client"></a>Parte 2: Instalar el paquete de configuración de la VPN en el cliente

1. Copie el archivo de configuración localmente en el equipo que va a conectarse a una red virtual y haga doble clic en el archivo .exe. 

2. Una vez instalado el paquete, puede iniciar la conexión VPN. El paquete de configuración no está firmado por Microsoft. Puede desea firmar el paquete mediante el servicio de firmas de su organización o firmar mediante [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327). Es usar el paquete sin iniciar sesión. Sin embargo, si el paquete no se ha iniciado sesión, aparece una advertencia cuando instale el paquete.

3. En el equipo cliente, vaya a **Configuración de la red** y haga clic en **VPN**. Verá la conexión que aparece. Se muestran el nombre de la red virtual que se conectará y tendrá un aspecto similar a este: 

    ![Cliente VPN] (./media/vpn-gateway-point-to-site-create/vpn.png "Cliente VPN")


### <a name="part-3-connect-to-azure"></a>Parte 3: Conectar con Azure

1. Para conectarse a su VNet, en el equipo cliente, vaya a las conexiones VPN y busque la conexión VPN que ha creado. Se denomina el mismo nombre que su red virtual. Haga clic en **Conectar**. Puede aparecer un mensaje emergente que hace referencia a utilizando el certificado. Si esto sucede, haga clic en **continuar** para usar privilegios elevados. 

2. En la página de estado de **conexión** , haga clic en **Conectar** para iniciar la conexión. Si ve una pantalla **Seleccionar certificado** , compruebe que el certificado de cliente que muestra es el que desea usar para conectarse. Si no es así, use la flecha de lista desplegable para seleccionar el certificado correcto y, a continuación, haga clic en **Aceptar**.

    ![Cliente VPN 2] (./media/vpn-gateway-point-to-site-create/clientconnect.png "Conexión de cliente VPN")

3. Ahora debe establecerse la conexión.

    ![Cliente VPN 3] (./media/vpn-gateway-point-to-site-create/connected.png "Conexión de cliente VPN 2")

### <a name="part-4-verify-the-vpn-connection"></a>Parte 4: Compruebe la conexión VPN

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

Si desea obtener más información acerca de las redes virtuales, consulte la página de la [Documentación de red Virtual](https://azure.microsoft.com/documentation/services/virtual-network/) .
