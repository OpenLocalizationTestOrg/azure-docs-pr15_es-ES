<properties
    pageTitle="Azure Active Directory Domain Services: Administrar un dominio administrado | Microsoft Azure"
    description="Administrar dominios administrados de Azure Active Directory Domain Services"
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
    ms.date="10/02/2016"
    ms.author="maheshu"/>

# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Administrar un dominio administrado de Azure Active Directory Domain Services
Este artículo le muestra cómo administrar un dominio administrado de servicios de dominio de Azure Active Directory (AD).


## <a name="before-you-begin"></a>Antes de empezar
Para realizar las tareas que se enumeran en este artículo, debe:

1. Una **suscripción de Azure**válida.

2. Un **directorio de Azure AD** - ya sea sincronizado con un directorio local o solo de la nube.

3. **Servicios de dominio de Active Directory de Azure** debe estar habilitada para el directorio de Azure AD. Si todavía no lo ha hecho, siga todas las tareas descritas en la [Guía de introducción](./active-directory-ds-getting-started.md).

4. Una **máquina virtual de dominio** desde donde administra los servicios de dominio de Active Directory de Azure administra el dominio. Si no tiene un equipo virtual, siga todas las tareas descritas en el artículo titulado [unirse a una máquina virtual de Windows a un dominio administrado](./active-directory-ds-admin-guide-join-windows-vm.md).

5. Necesita las credenciales de una **cuenta de usuario que pertenecen al grupo ' AAD DC administradores '** en el directorio, para administrar el dominio administrado.

<br>


## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Tareas administrativas que puede realizar con un dominio administrado
Los miembros del grupo 'AAD DC administradores' se conceden privilegios en el dominio administrado que les permite realizar tareas como:

- Unirse a equipos en el dominio administrado.

- Configure el GPO integrado para los contenedores 'AADDC equipos' y 'AADDC usuarios' en el dominio administrado.

- Administrar DNS en el dominio administrado.

- Crear y administrar las unidades organizativas personalizado (OU) en el dominio administrado.

- Obtenga acceso administrativo a equipos unido al dominio administrado.


## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>En un dominio administrado no tiene privilegios de administrador
El dominio es administrado por Microsoft, incluidas las actividades como revisiones, supervisión y realizar copias de seguridad. Por lo tanto, se bloquea el dominio y no tiene privilegios para realizar ciertas tareas administrativas en el dominio. Algunos ejemplos de tareas que no se puede realizar están por debajo.

- No se conceden privilegios de administrador de dominio o el Administrador de la empresa para el dominio administrado.

- No puede extender el esquema del dominio administrado.

- No puede conectarse a los controladores de dominio para el dominio administrado usando Escritorio remoto.

- No puede agregar controladores de dominio para el dominio administrado.


## <a name="task-1---provision-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>Tarea 1: aprovisionar una máquina virtual de Windows Server Unidos a un dominio para administrar el dominio administrado de forma remota
Azure AD administrados dominios pueden administrarse con familiares herramientas administrativas de Active Directory como el centro administrativo de directorio activo (ADAC) o AD PowerShell. Los administradores de inquilinos no tienen privilegios para conectarse a los controladores de dominio en el dominio administrado a través de escritorio remoto. Por tanto, los miembros del grupo 'AAD DC administradores' pueden administrar dominios administrados remotamente mediante herramientas administrativas de AD desde un equipo cliente de Windows Server que se ha unido al dominio administrado. Herramientas administrativas de AD pueden instalarse como parte de la función opcional de herramientas de administración de servidor remoto (RSAT) en Windows Server y equipos cliente Unidos al dominio administrado.

Es el primer paso configurar una máquina virtual de Windows Server que se ha unido al dominio administrado. Para obtener instrucciones, consulte el artículo titulado [unirse a una máquina virtual de Windows Server en un dominio de servicios de dominio de Active Directory de Azure administrados](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Administrar el dominio administrado de forma remota desde un equipo cliente (por ejemplo, Windows 10)
Las instrucciones de este artículo utilizan una máquina virtual de Windows Server para administrar los criterios de AAD administran el dominio. Sin embargo, también puede usar una máquina virtual de cliente (por ejemplo, Windows 10) de Windows para hacerlo.

Puede [instalar herramientas de administración de servidor remoto (RSAT)](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) en una máquina virtual de cliente de Windows siguiendo las instrucciones en TechNet.


## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>Tarea 2: herramientas de administración de la instalación de Active Directory en la máquina virtual
Realice los pasos siguientes para instalar las herramientas de administración de Active Directory en la máquina virtual dominio unido. Para obtener más [información sobre cómo instalar y usar herramientas de administración de servidor remoto](https://technet.microsoft.com/library/hh831501.aspx), vea Technet.

1. Desplácese al nodo de **máquinas virtuales** en el portal de clásico de Azure. Seleccione la máquina virtual que creó en la tarea 1 y haga clic en **Conectar** en la barra de comandos en la parte inferior de la ventana.

    ![Conectar a máquina virtual de Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. El portal clásico le pregunta si desea abrir o guardar un archivo con una extensión 'RDP', que se usa para conectarse a la máquina virtual. Haga clic para abrir el archivo cuando ha finalizado la descarga.

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

9. En la página **características** , haga clic para expandir el nodo de **Herramientas de administración de servidor remoto** y, a continuación, haga clic para expandir el nodo de **Herramientas de administración de roles** . Seleccione la función de **AD DS y AD LDS herramientas** de la lista de herramientas de administración de roles.

    ![Página de características](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)

10. En la página de **confirmación** , haga clic en **instalar** para instalar el anuncio y la característica de herramientas de AD LDS de la máquina virtual. Cuando finalice correctamente la instalación de características, haga clic en **Cerrar** para salir del Asistente para **Agregar funciones y características** .

    ![Página de confirmación](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)


## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>Tarea 3: conectar con y explorar el dominio administrado
Ahora que están instaladas las herramientas administrativas de AD en el dominio había unido máquina virtual, podemos usar estas herramientas para explorar y administrar el dominio administrado.

> [AZURE.NOTE] Debe ser miembro del grupo 'AAD DC administradores', para administrar el dominio administrado.

1. Desde la pantalla de inicio, haga clic en **Herramientas administrativas**. Debería ver las herramientas administrativas de AD instaladas en la máquina virtual.

    ![Herramientas administrativas instaladas en el servidor](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Haga clic en **Centro de administración de Active Directory**.

    ![Centro de administración de Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. Para explorar el dominio, haga clic en el nombre de dominio en el panel izquierdo (por ejemplo, ' contoso100.com'). Tenga en cuenta dos contenedores denominados 'AADDC equipos' y 'AADDC usuarios' respectivamente.

    ![ADAC - ver dominio](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)

4. Haga clic en el contenedor llamado **AADDC usuarios** para ver todos los usuarios y grupos que pertenezcan al dominio administrado. Debería ver las cuentas de usuario y grupos de su Azure AD de inquilinos mostrar hacia arriba en este contenedor. Observe en este ejemplo, una cuenta de usuario para el usuario llamado 'bob' y un grupo denominado 'AAD DC administradores' están disponibles en este contenedor.

    ![ADAC: los usuarios del dominio](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)

5. Haga clic en el contenedor de **Equipos de AADDC** para ver los equipos que se ha unido a este dominio administrado. Debería ver una entrada de la máquina virtual actual, que se ha unido al dominio. Cuentas de equipo para todos los equipos que se unen en el dominio de servicios de dominio de Active Directory de Azure administrado se almacenan en este contenedor 'AADDC equipos'.

    ![ADAC - dominio unido equipos](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Contenido relacionado

- [Servicios de dominio de Azure AD - Guía de introducción](./active-directory-ds-getting-started.md)

- [Unirse a una máquina virtual de Windows Server a un dominio administrado de servicios de dominio de Active Directory de Azure](active-directory-ds-admin-guide-join-windows-vm.md)

- [Implementar herramientas de administración de servidor remoto](https://technet.microsoft.com/library/hh831501.aspx)
