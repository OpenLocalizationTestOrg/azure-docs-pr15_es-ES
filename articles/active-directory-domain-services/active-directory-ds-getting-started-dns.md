<properties
    pageTitle="Servicios de dominio de Azure AD: Configuración actualizar DNS de la red virtual Azure | Microsoft Azure"
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
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services---update-dns-settings-for-the-azure-virtual-network"></a>Servicios de dominio de Azure AD - configuración de DNS de la actualización de la red virtual de Azure

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Tarea 4: Actualizar la configuración de DNS de la red virtual de Azure
En anteriores tareas de configuración, ha activado correctamente Servicios de dominio de Active Directory de Azure para el directorio. La siguiente tarea es asegurarse de que los equipos de la red virtual pueden conectar y utilizar estos servicios. Actualizar la configuración del servidor DNS para su red virtual para que apunten a las dos direcciones IP en el que los servicios de dominio de Azure AD está disponible en la red virtual.

> [AZURE.NOTE] Tenga en cuenta las direcciones IP para los servicios de dominio de Active Directory de Azure aparece en la ficha **Configurar** del directorio, después de habilitar servicios de dominio de Active Directory de Azure para el directorio.

Realice los siguientes pasos de configuración para actualizar la configuración del servidor DNS de la red virtual en el que ha habilitado Servicios de dominio de Active Directory de Azure.

1. Desplácese hasta el **portal clásica Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Seleccione el nodo de **redes** en el panel izquierdo.

    ![Nodo de redes virtuales](./media/active-directory-domain-services-getting-started/virtual-network-select.png)

3. En la pestaña **Redes virtuales** , seleccione la red virtual que habilitado Servicios de dominio de Active Directory de Azure ver sus propiedades.

4. Haga clic en la ficha **Configurar** .

    ![Nodo de redes virtuales](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)

5. En la sección **servidores DNS** , escriba las direcciones IP de servicios de dominio de Active Directory de Azure.

6. Asegúrese de que se especifica las direcciones IP que se muestran en la sección de **Servicios de dominio** en la ficha **Configurar** del directorio.

7. Para guardar la configuración del servidor DNS para esta red virtual, haga clic en **Guardar** en el panel de tareas en la parte inferior de la página.

   ![Actualizar la configuración del servidor DNS de la red virtual.](./media/active-directory-domain-services-getting-started/update-dns.png)

> [AZURE.NOTE] Después de actualizar la configuración del servidor DNS de la red virtual, puede tardar unos instantes en máquinas virtuales de la red para obtener la configuración de DNS actualizados. Si no puede conectarse al dominio una máquina virtual, puede vaciar la caché de DNS (ej. ' ipconfig/flushdns') en la máquina virtual. Este comando fuerza una actualización de la configuración de DNS en la máquina virtual.


## <a name="task-5---enable-password-synchronization-to-azure-ad-domain-services"></a>Tarea 5: habilitar la sincronización de contraseña a los servicios de dominio de Active Directory de Azure
Es la siguiente tarea de configuración para [Habilitar la sincronización de contraseña a los servicios de dominio de Active Directory de Azure](active-directory-ds-getting-started-password-sync.md).
