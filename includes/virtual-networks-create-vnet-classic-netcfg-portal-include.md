## <a name="how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal"></a>Cómo crear un VNet con un archivo de configuración de red en el portal de Azure

Azure utiliza un archivo xml para definir todas las VNets disponibles para una suscripción. Puede descargar el archivo y editarlo para modificar o eliminar VNets existentes y crear nuevos. En este documento se Aprenda a descargar este archivo, que se conoce como archivo de configuración (o netcgf) de la red y editarlo para crear un nuevo VNet. Compruebe el [esquema de configuración de red virtual Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) para obtener más información sobre el archivo de configuración de red.

Para crear un VNet con un archivo de netcfg a través del portal de Azure, siga los pasos siguientes.

1. Desde un explorador, vaya a http://manage.windowsazure.com y, si es necesario, inicie sesión con su cuenta de Azure.
2. Desplácese hacia abajo en la lista de servicios y haga clic en **redes** , tal como se muestra a continuación.

    ![Redes virtuales Azure](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure1.gif)

3. En la parte inferior de la página, haga clic en el botón **Exportar** , como se muestra a continuación.

    ![Botón Exportar](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure2.png)

4. En la página **exportar la configuración de red** , seleccione la suscripción que desea exportar la configuración de red virtual de y, a continuación, haga clic en el botón de marca de verificación en la parte inferior izquierda esquina de la página.
5. Siga las instrucciones de su navegador para guardar el archivo **NetworkConfig.xml** . Asegúrese de tenga en cuenta que va a guardar el archivo.
6. Abra el archivo que guardó en el paso 5 con cualquier aplicación de edición de texto o XML y busque el **<VirtualNetworkSites>** elemento. Si tiene cualquier redes ya ha creado, cada red se mostrará como su propia **<VirtualNetworkSite>** elemento.
7. Para crear la red virtual descritas en este escenario, agregue el siguiente código XML justo por debajo del **<VirtualNetworkSites>** elemento:

        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>

8.  Guarde el archivo de configuración de red.
9.  En el portal de Azure, en el extremo inferior izquierdo de la página, haga clic en **nuevo**, a continuación, haga clic en **Servicios de red**, a continuación, haga clic en **Una red VIRTUAL**y, a continuación, haga clic en **Configuración de importación** como se muestra en la siguiente ilustración.

    ![Configuración de importación](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure3.gif)

10.  En la página **importar el archivo de configuración de red** , haga clic en **Examinar para archivo...**, a continuación, vaya a la carpeta que ha guardado el archivo en el paso 8, seleccione el archivo y, a continuación, haga clic en **Abrir**. La página web debe ser similar a la siguiente ilustración. En la parte inferior derecha esquina de la página, haga clic en el botón de flecha para moverse al siguiente paso.

    ![Página de archivo de configuración de red de importación](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure4.png)

11.   En la página **crear su red** , observe la entrada para el nuevo VNet, como se muestra en la siguiente ilustración.

    ![Creación de la página de su red](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure5.png)

12.   Haga clic en el botón de marca de verificación en la esquina inferior derecha de la página para crear la VNet. Después de unos segundos su VNet se mostrará en la lista de VNets disponible, tal como se muestra en la figura siguiente.

    ![Nueva red virtual](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure6.png)