<properties 
    pageTitle="Configurar una red virtual con un archivo de configuración de red" 
    description="Instrucciones para exportar e importar un archivo de configuración de red en el Portal de administración de Azure para crear o modificar las redes virtuales. " 
    services="virtual-network" 
    documentationCenter="" 
    authors="jimdial" 
    manager="carmonm" 
    editor="tysonn"/>

<tags
    ms.service="virtual-network"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services" 
    ms.date="03/15/2016"
    ms.author="jdial"/>

# <a name="configure-a-virtual-network-using-a-network-configuration-file"></a>Configurar una red virtual con un archivo de configuración de red

Puede configurar una red virtual (VNet) a través del portal de administración de Azure, o mediante un archivo de configuración de red.

## <a name="creating-and-modifying-a-network-configuration-file"></a>Crear y modificar un archivo de configuración de red 
Es la manera más sencilla de crear un archivo de configuración de red exportar la configuración de la red de una configuración de red virtual existente, a continuación, modifique el archivo para que contenga la configuración que desea configurar para sus redes virtuales.

Para editar el archivo de configuración de red, simplemente puede abrir el archivo, realice los cambios necesarios y guarde el archivo. Puede usar cualquier editor *xml* para realizar cambios en el archivo de configuración de red. 

Estrechamente debería seguir las instrucciones para la [configuración de esquema de archivo de configuración de red](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

Azure considera una subred que tiene algo implementado como **en uso**. Cuando se usa una subred, no se puede modificar. Antes de modificar, mover todo lo que ha implementado a la subred para una subred diferente que no se modifica.   Consulte [mover una máquina virtual o una instancia de rol a una subred diferente](virtual-networks-move-vm-role-to-subnet.md).

## <a name="export-and-import-virtual-network-settings-using-the-management-portal"></a>Exportar e importar con el Portal de administración de configuración de red virtual  
Puede importar y exportar la configuración de red contenida en el archivo de configuración de red con PowerShell o el Portal de administración. Las siguientes instrucciones le ayudará a exportar e importar con el Portal de administración. 

### <a name="to-export-your-network-settings"></a>Para exportar los ajustes de red
Al exportar, se escribirá toda la configuración de las redes virtuales en su suscripción a un archivo XML. 

1. Inicie sesión en el **Portal de administración**.
2. En el Portal de administración, en la parte inferior de la página de **redes** , haga clic en **Exportar**. 
3. En la ventana **Exportar configuración de red** , compruebe que ha seleccionado la suscripción para la que desea exportar la configuración de red. A continuación, haga clic en la marca de verificación en la esquina inferior derecha. 
4. Cuando se le solicita, guarde el archivo de *NetworkConfig.xml* en la ubicación de su elección.


### <a name="to-import-your-network-settings"></a>Para importar la configuración de red

1. En el **Portal de administración**, en el panel de navegación en la parte inferior izquierda, haga clic en **nuevo**.
2. Haga clic en **Servicios de red** -> **red Virtual** -> **configuración de importación**.
3. En la página **importar el archivo de configuración de red** , busque el archivo de configuración de red y, a continuación, haga clic en la flecha **siguiente** .
4. En la página **crear su red** , verá la información en pantalla que muestra las secciones de la configuración de red se ha cambiado o creadas. Si el aspecto correctos para los cambios, haga clic en la marca de verificación para continuar para actualizar o crear una red virtual. 