## <a name="how-to-create-a-vnet-in-the-azure-portal"></a>Cómo crear un VNet en el portal de Azure

Para crear un VNet basado en el ejemplo anterior, siga los pasos siguientes.

1. Desde un explorador, vaya a http://manage.windowsazure.com y, si es necesario, inicie sesión con su cuenta de Azure.
2. Haga clic en **nuevo** > **Servicios de red** > **Red VIRTUAL** > **Crear personalizado** , como se muestra en la siguiente ilustración.

    ![Crear VNet en el portal](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure1.gif)

3. En la página **Detalles de la red Virtual** , escriba el **nombre** de la VNet, seleccione su **ubicación**y, a continuación, haga clic en la flecha situada en la esquina inferior derecha de la página para avanzar al paso 2. La figura siguiente muestra la configuración de nuestro escenario.

    ![Página de detalles de la red virtual](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure2.png)

4. En la página **conectividad VPN y los servidores DNS** , especifique el nombre y la dirección IP de hasta 9 servidores DNS para usar. Si no especifica un servidor DNS, el VNet usará la resolución de resolución nomenclatura interno proporcionada por Azure. En nuestro escenario, no se configurará los servidores DNS.
5. Si desea acceder a su VNet VPN punto al sitio, habilite la casilla de verificación **Configurar una VPN de sitio a punto** . Si no se configura una VPN punto al sitio, puede agregar a su VNet en cualquier momento después de la creación. En el escenario, no para configurar una VPN de sitio a punto.
6. Si desea proporcionar conectividad VPN de sitio a sitio entre el VNet y otra VNet o a su red local, activar la casilla de verificación **Configurar una VPN de sitio a sitio** y especificar si desea usar **ExpressRoute** o nota y el nombre de la red para conectarse a. Si no se configura una VPN de sitio a sitio, puede agregar a su VNet en cualquier momento después de la creación. En el escenario, no para configurar una VPN de sitio a otro.
7. Haga clic en la flecha situada en la esquina inferior derecha de la página para avanzar a 3.en paso que la ilustración siguiente muestra la configuración de nuestro escenario.

    ![Página de conectividad VPN y los servidores DNS](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure3.png)

8. En la página **Virtual espacios de direcciones de red** , bajo **IP inicial**, haga clic en *10.0.0.0* para cambiar el espacio de direcciones de VNet y, a continuación, escriba el espacio de direcciones inicial que desee usar. En el escenario, escriba *192.168.0.0*. 
9. En **CIDR (dirección recuento)** seleccione el número de bits de la máscara de subred. En el escenario, seleccione *16 (65536)*.
10. **SUBREDES**, haga clic en *subred 1* y cambiar el nombre de la subred si es necesario. En el escenario, cambie el nombre a *front-end*.

    >[AZURE.NOTE] Si hace clic fuera del cuadro de texto de nombre para una subred no podrá editar el nombre si la subred nuevamente. Para corregirlo, debe quitar la subred haciendo clic en el botón X a la derecha y, después, agregar una nueva subred como se describe en el paso 13 debajo.

11. En **IP inicial** para la primera subred, especifique la dirección IP inicial para la subred. En el escenario, escriba *192.168.1.0*.
12. En **CIDR (dirección recuento)** seleccione el número de bits de la máscara para la primera subred. En el escenario, seleccione *24 (256)*.
13. Haga clic en **Agregar subred** para agregar una nueva subred, si es necesario. En el escenario, agregue una subred y repita los pasos del 10 al 12 para configurar la VNet tal como se muestra en la figura siguiente.

    ![Página de espacios de dirección de una red virtual](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure4.png)

14. Haga clic en el botón de marca de verificación en la esquina inferior derecha de la página para crear la VNet. Después de unos segundos su VNet se mostrará en la lista de VNets disponible, tal como se muestra en la figura siguiente.

    ![Nueva red virtual](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure5.png)