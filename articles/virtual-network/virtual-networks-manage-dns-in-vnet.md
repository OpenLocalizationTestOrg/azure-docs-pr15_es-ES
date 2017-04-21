<properties 
   pageTitle="Administrar los servidores DNS que utiliza una red virtual (VNet)"
   description="Obtenga información sobre cómo agregar y quitar los servidores DNS en una red virtual (vnet)"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="manage-dns-servers-used-by-a-virtual-network-vnet"></a>Administrar los servidores DNS que utiliza una red virtual (VNet)

Puede administrar la lista de los servidores DNS utilizados en una VNet en el Portal de administración o en el archivo de configuración de red. Puede agregar hasta 12 servidores DNS para cada VNet. Cuando se especifican los servidores DNS, es importante comprobar la lista de los servidores DNS en el orden correcto para su entorno. Listas de servidores DNS no funcionan turnos. Se utilizan en el orden en que se especifican. Si puede alcanzar el primer servidor DNS en la lista, el cliente usará dicho servidor DNS independientemente de si el servidor DNS funciona correctamente o no. Para cambiar el orden de servidor DNS para su red virtual, quitar los servidores DNS de la lista y agregarlos en el orden que desee.

>[AZURE.WARNING] Después de actualizar la lista DNS, debe reiniciar los equipos virtuales ubicados en su red virtual para que seleccione la nueva configuración del servidor DNS. Máquinas virtuales seguirá utilizando la configuración actual hasta que se vuelven a iniciar.

## <a name="edit-a-dns-server-list-for-a-virtual-network-using-the-management-portal"></a>Editar una lista de servidores DNS para una red virtual con el Portal de administración

1. Inicie sesión en el **Portal de administración**.

1. En el panel de navegación, haga clic en **redes**y, a continuación, haga clic en el nombre de su red virtual en la columna **nombre** .

1. Haga clic en **Configurar**.

1. En **Los servidores DNS**, puede configurar lo siguiente:

    - **Registrar (Agregar) un nuevo DNS server:** Solo tiene que escribir el nombre y la dirección IP en los cuadros. Esto agrega un servidor DNS a su lista de los servidores DNS de la red virtual y también registra el servidor DNS con Azure.

    - **Para agregar un servidor DNS que se registró anteriormente:** Si ya ha registrado un servidor DNS en Azure, puede seleccionar de la lista rellenarán.

    - **Para quitar un servidor DNS de su red virtual:** Haga clic en la X situada junto al servidor que desea quitar. Tenga en cuenta que esto solo se quita el servidor de esta lista de red virtual. El servidor DNS permanece registrado en Azure para sus otras redes virtuales para usar. Para eliminar un servidor DNS de su suscripción, vaya a la página **redes -> servidores DNS** .

    - **Para cambiar el orden de los servidores DNS:** Quitar todos los servidores DNS que aparecen y agregarlos a continuación, hacer una copia en el orden que desee. Recuerde que no es una lista de turnos DNS.

    - **Para cambiar el nombre de un servidor DNS:** Resalte el servidor DNS en la lista y luego escriba el nuevo nombre. Esto se registre un servidor DNS nuevo en Azure, así como agregar a la lista de los servidores DNS para su red virtual. El servidor DNS anterior y su dirección IP permanecerán registrados con Azure. Puede eliminarlo en la página de **Los servidores DNS** , si no se usa para cualquier otra red virtual.

1. Haga clic en **Guardar** en la parte inferior de la página para guardar la nueva configuración de los servidores DNS.

1. Reinicie los equipos virtuales ubicados en la red virtual para poder adquirir la nueva configuración de DNS.

## <a name="edit-a-dns-server-list-using-a-network-configuration-file"></a>Editar una lista de servidores DNS con un archivo de configuración de red

Para editar una lista de servidores DNS mediante un archivo de configuración de red, primero deberá exportar los valores de configuración desde el Portal de administración. A continuación, deberá editar el archivo de configuración de red e impórtelo a través del Portal de administración. A continuación se muestra una lista detallada de los pasos para completar el proceso.

1. Exportar la configuración de red virtual a un archivo de configuración de red. Para que obtener más información y pasos para exportar los valores de configuración de red, consulte [Exportar configuración de red Virtual a un archivo de configuración de red](virtual-networks-using-network-configuration-file.md).

1. Especifique la información del servidor DNS para su red virtual. Para obtener más información acerca de cómo especificar un servidor DNS, vea [especificar un servidor DNS en un archivo de configuración de red Virtual](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md). Para obtener información adicional acerca de los archivos de configuración de red, vea [Esquema de configuración de red Virtual de Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) y [Configurar una red Virtual con un archivo de configuración de red](virtual-networks-using-network-configuration-file.md).

1. Importar el archivo de configuración de red. Para que obtener más información y pasos para importar el archivo de configuración de red, consulte [importar un archivo de configuración de red](virtual-networks-using-network-configuration-file.md).

1. Reinicie los equipos virtuales ubicados en la red virtual para poder adquirir la nueva configuración de DNS.
