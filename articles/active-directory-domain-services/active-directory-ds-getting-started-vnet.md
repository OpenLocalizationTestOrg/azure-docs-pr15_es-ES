<properties
    pageTitle="Servicios de dominio de Azure AD: Crear o seleccionar una red virtual | Microsoft Azure"
    description="Introducción a servicios de dominio de Azure Active Directory"
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
    ms.topic="get-started-article"
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="create-or-select-a-virtual-network-for-azure-ad-domain-services"></a>Crear o seleccionar una red virtual para los servicios de dominio de Active Directory de Azure

## <a name="guidelines-to-select-an-azure-virtual-network"></a>Directrices para seleccionar una red virtual de Azure
> [AZURE.NOTE] **Antes de comenzar**: consulte [redes consideraciones para los servicios de dominio de Active Directory de Azure](active-directory-ds-networking.md).


## <a name="task-2-create-an-azure-virtual-network"></a>Tarea 2: Crear una red virtual de Azure
La tarea siguiente de la configuración es crear una red virtual Azure y una subred dentro de ella. Habilitar los servicios de dominio de Active Directory de Azure en esta subred dentro de su red virtual. Si ya tiene una red virtual existente que desea usar, puede omitir este paso.

> [AZURE.NOTE] Asegúrese de que la red virtual Azure crear o elija para usar con servicios de dominio de Active Directory de Azure pertenece a un área de Azure es compatible con servicios de dominio de Active Directory de Azure. Consulte la página de [Servicios de Azure por región](https://azure.microsoft.com/regions/#services/) saber las regiones de Azure en la que los servicios de dominio de Azure AD está disponible.

Anote el nombre de la red virtual para seleccionar la red virtual hacia la derecha al habilitar servicios de dominio de Active Directory de Azure en un paso de configuración posteriores.

Realice los siguientes pasos de configuración para crear una red virtual Azure en la que desea habilitar servicios de dominio de Active Directory de Azure.

1. Desplácese hasta el **portal clásica Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Seleccione el nodo de **redes** en el panel izquierdo.

    ![Nodo de redes](./media/active-directory-domain-services-getting-started/networks-node.png)

3. En el panel de tareas en la parte inferior de la página, haga clic en **nuevo** .

    ![Nodo de redes virtuales](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. En el nodo de **Servicios de red** , seleccione **Una red Virtual**.

5. Haga clic en **Crear rápido** para crear una red virtual.

    ![Crear una red virtual - rápida](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. Especifique un **nombre** para su red virtual. También puede configurar el **espacio de direcciones** o la **cuenta de máquina virtual máxima** para esta red. Puede dejar la configuración de **servidor DNS** establecida en 'Ninguna' por ahora. Puede actualizar el servidor DNS establecer después de su dominio de Active Directory de Azure habilitar servicios.

7. Asegúrese de que selecciona un área de Azure admitido en la lista desplegable de **ubicación** . Consulte la página de [Servicios de Azure por región](https://azure.microsoft.com/regions/#services/) saber las regiones de Azure en la que los servicios de dominio de Azure AD está disponible.

8. Para crear su red virtual, haga clic en el botón **crear una red Virtual** .

    ![Crear una red virtual para los servicios de dominio de Active Directory de Azure.](./media/active-directory-domain-services-getting-started/create-vnet.png)

9. Después de crea la red virtual, seleccione la red virtual y haga clic en la ficha **Configurar** .

    ![Crear una subred](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)

10. Vaya a la sección de **espacios de direcciones de red virtual** . Haga clic en **Agregar subred** y especificar una subred con el nombre **AaddsSubnet**. Haga clic en **Guardar** para crear la subred.

    ![Crear una subred para los servicios de dominio de Active Directory de Azure.](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)


<br>

## <a name="task-3---enable-azure-ad-domain-services"></a>Tarea 3: habilitar servicios de dominio de Active Directory de Azure
Es la siguiente tarea de configuración para [Habilitar los servicios de dominio de Active Directory de Azure](active-directory-ds-getting-started-enableaadds.md).
