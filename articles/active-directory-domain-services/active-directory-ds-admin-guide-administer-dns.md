<properties
    pageTitle="Azure Active Directory Domain Services: Administrar DNS en dominios administrados | Microsoft Azure"
    description="Administrar DNS en dominios administrados de Azure Active Directory Domain Services"
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
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Administrar DNS en un dominio administrado de servicios de dominio de Active Directory de Azure
Azure Active Directory Domain Services incluye un servidor DNS (resolución de nombres de dominio) que proporciona la resolución de DNS del dominio administrado. En ocasiones, tendrá que configurar DNS en el dominio administrado. Debe crear registros DNS para equipos que no se ha unido al dominio, configurar direcciones IP virtuales para equilibradores de carga o configurar los reenviadores DNS externos. Por este motivo, los usuarios que pertenecen al grupo 'AAD DC administradores' se conceden privilegios de administración de DNS en el dominio administrado.


## <a name="before-you-begin"></a>Antes de empezar
Para realizar las tareas que se enumeran en este artículo, debe:

1. Una **suscripción de Azure**válida.

2. Un **directorio de Azure AD** - ya sea sincronizado con un directorio local o solo de la nube.

3. **Servicios de dominio de Active Directory de Azure** debe estar habilitada para el directorio de Azure AD. Si todavía no lo ha hecho, siga todas las tareas descritas en la [Guía de introducción](./active-directory-ds-getting-started.md).

4. Una **máquina virtual de dominio** desde donde administra los servicios de dominio de Active Directory de Azure administra el dominio. Si no tiene un equipo virtual, siga todas las tareas descritas en el artículo titulado [unirse a una máquina virtual de Windows a un dominio administrado](./active-directory-ds-admin-guide-join-windows-vm.md).

5. Necesita las credenciales de una **cuenta de usuario que pertenecen al grupo ' AAD DC administradores '** en el directorio, para administrar el DNS de su dominio administrado.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>Tarea 1: aprovisionar una máquina virtual de dominio para administrar DNS del dominio administrados de forma remota
Azure AD administrados dominios pueden administrarse remotamente con familiares herramientas administrativas de Active Directory como el centro administrativo de directorio activo (ADAC) o AD PowerShell. Del mismo modo, se puede administrar DNS del dominio administrado remotamente con las herramientas de administración del servidor DNS.

Los administradores en su directorio de Azure AD no tienen privilegios para conectarse a los controladores de dominio en el dominio administrado a través de escritorio remoto. Los miembros del grupo 'AAD DC administradores' pueden administrar el DNS para dominios administrados remotamente con herramientas de servidor DNS desde un equipo cliente de Windows Server que se ha unido al dominio administrado. Herramientas de servidor DNS pueden instalarse como parte de la función opcional de herramientas de administración de servidor remoto (RSAT) en Windows Server y equipos cliente Unidos al dominio administrado.

La primera tarea está proporcionando una máquina virtual de Windows Server que se ha unido al dominio administrado. Para obtener instrucciones, consulte el artículo titulado [unirse a una máquina virtual de Windows Server en un dominio de servicios de dominio de Active Directory de Azure administrados](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>Tarea 2: herramientas de servidor DNS de instalación en la máquina virtual
Realice los pasos siguientes para instalar las herramientas de administración de DNS en la máquina virtual dominio unido. Para obtener más información sobre [cómo instalar y usar herramientas de administración de servidor remoto](https://technet.microsoft.com/library/hh831501.aspx), vea Technet.

1. Desplácese al nodo de **máquinas virtuales** en el portal de clásico de Azure. Seleccione la máquina virtual que creó en la tarea 1 y haga clic en **Conectar** en la barra de comandos en la parte inferior de la ventana.

    ![Conectar a máquina virtual de Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. El portal clásico le pregunta si desea abrir o guardar un archivo con una extensión 'RDP', que se usa para conectarse a la máquina virtual. Cuando termine la descarga, haga clic en el archivo.

3. En el símbolo de inicio de sesión, utilice las credenciales de un usuario que pertenece al grupo 'AAD DC administradores'. Por ejemplo, usamos 'bob@domainservicespreview.onmicrosoft.com' en nuestro caso.

4. Desde la pantalla de inicio, abra el **Administrador del servidor**. Haga clic en **Agregar funciones y características** en el panel central de la ventana Administrador del servidor.

    ![Iniciar el administrador Server en máquina virtual](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)

5. En la página **Antes de comenzar** de **Agregar funciones y características de asistente**, haga clic en **siguiente**.

    ![Antes de comenzar a página](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)

6. En la página **Tipo de instalación** , deje la opción de **instalación basado en roles o característica** activada y haga clic en **siguiente**.

    ![Página tipo de instalación](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)

7. En la página de **Selección de servidor** , seleccione la máquina virtual actual desde el grupo de servidores y haga clic en **siguiente**.

    ![Página de selección de servidor](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)

8. En la página de **Roles del servidor** , haga clic en **siguiente**. Nos omitir esta página, ya que nos estamos instalando las funciones en el servidor.

9. En la página **características** , haga clic para expandir el nodo de **Herramientas de administración de servidor remoto** y, a continuación, haga clic para expandir el nodo de **Herramientas de administración de roles** . Seleccione la característica de **Herramientas del servidor DNS** de la lista de herramientas de administración de roles.

    ![Página de características](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

10. En la página de **confirmación** , haga clic en **instalar** para instalar la característica de las herramientas de servidor DNS en la máquina virtual. Cuando finalice correctamente la instalación de características, haga clic en **Cerrar** para salir del Asistente para **Agregar funciones y características** .

    ![Página de confirmación](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)


## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>Tarea 3: iniciar la consola de administración de DNS para administrar DNS
Ahora que está instalada la característica de herramientas del servidor DNS en el dominio había unido máquina virtual, podemos usar las herramientas DNS para administrar DNS en el dominio administrado.

> [AZURE.NOTE] Debe ser miembro del grupo 'AAD DC administradores', administrar DNS en el dominio administrado.

1. Desde la pantalla de inicio, haga clic en **Herramientas administrativas**. Ahora debe aparecer la consola **DNS** instalada en la máquina virtual.

    ![Herramientas administrativas - consola DNS](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)

2. Haga clic en **DNS** para iniciar la consola de administración de DNS.

3. En el cuadro de diálogo **Conectar a servidor DNS** , haga clic en la opción titulada **el siguiente equipo**y escriba el nombre de dominio DNS del dominio administrado (por ejemplo, ' contoso100.com').

    ![Consola DNS - conectarse al dominio](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

4. La consola DNS se conecta al dominio administrado.

    ![Consola DNS - administrar dominio](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

5. Ahora puede usar la consola DNS para agregar entradas DNS para equipos dentro de la red virtual en el que se han habilitado Servicios de dominio de AAD.

> [AZURE.WARNING] Tenga cuidado al administrar DNS del dominio administrado mediante herramientas de administración de DNS. Asegúrese de que usted **no eliminar o modificar los registros DNS integrados que se usan los servicios de dominio en el dominio**. Registros DNS integrados incluyen registros DNS del dominio, registros del servidor DNS y otros registros utilizados para DC ubicación. Si modifica estos registros, se interrumpen los servicios de dominio de la red virtual.


Vea el [artículo de herramientas DNS en Technet](https://technet.microsoft.com/library/cc753579.aspx) para obtener más información sobre la administración de DNS.


## <a name="related-content"></a>Contenido relacionado

- [Servicios de dominio de Azure AD - Guía de introducción](./active-directory-ds-getting-started.md)

- [Unirse a una máquina virtual de Windows Server a un dominio administrado de servicios de dominio de Active Directory de Azure](active-directory-ds-admin-guide-join-windows-vm.md)

- [Administrar un dominio administrado de servicios de dominio de Active Directory de Azure](active-directory-ds-admin-guide-administer-domain.md)

- [Herramientas de administración de DNS](https://technet.microsoft.com/library/cc753579.aspx)
