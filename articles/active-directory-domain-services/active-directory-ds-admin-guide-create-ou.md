<properties
    pageTitle="Servicios de dominio de Azure Active Directory: Guía de administración | Microsoft Azure"
    description="Crear una unidad organizativa (OU) en servicios de dominio de Active Directory de Azure administrados dominios"
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
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Crear una unidad organizativa (OU) en un dominio administrado de servicios de dominio de Active Directory de Azure
Azure AD administrados dominios incluyen dos contenedores integrados denominados 'AADDC equipos' y 'AADDC usuarios' respectivamente. El contenedor 'AADDC equipos' tiene objetos de equipo para todos los equipos que se unen al dominio administrado. El contenedor 'AADDC usuarios' incluye los usuarios y grupos en el inquilino de Azure AD. En ocasiones, puede ser necesario crear cuentas de servicio en el dominio administrado para implementar cargas de trabajo. Para ello, puede crear una unidad organizativa (OU) personalizado en el dominio administrado y crear cuentas de servicio en esa unidad organizativa. En este artículo se muestra cómo crear una unidad organizativa en su dominio administrado.


## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Instalar herramientas de administración de AD en una máquina virtual de dominio para la administración remota
Azure AD administrados dominios pueden administrarse remotamente con familiares herramientas administrativas de Active Directory como el centro administrativo de directorio activo (ADAC) o AD PowerShell. Los administradores de inquilinos no tienen privilegios para conectarse a los controladores de dominio en el dominio administrado a través de escritorio remoto. Para administrar el dominio administrado, instale la característica de herramientas de administración de AD en una máquina virtual unida al dominio administrado. Consulte el artículo titulado [administrar un dominio de servicios de dominio de Active Directory de Azure administrados](active-directory-ds-admin-guide-administer-domain.md) para obtener instrucciones.

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Crear a una unidad organizativa en el dominio administrado
Ahora que están instaladas las herramientas administrativas de AD en el dominio había unido máquina virtual, podemos usar estas herramientas para crear una unidad organizativa en el dominio administrado. Realice los pasos siguientes:

> [AZURE.NOTE] Solo los miembros del grupo 'AAD DC administradores' tienen los privilegios necesarios para crear una OU personalizada. Asegúrese de seguir estos pasos como un usuario que pertenece a este grupo.

1. Desde la pantalla de inicio, haga clic en **Herramientas administrativas**. Debería ver las herramientas administrativas de AD instaladas en la máquina virtual.

    ![Herramientas administrativas instaladas en el servidor](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Haga clic en **Centro de administración de Active Directory**.

    ![Centro de administración de Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. Para ver el dominio, haga clic en el nombre de dominio en el panel izquierdo (por ejemplo, ' contoso100.com').

    ![ADAC - ver dominio](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

4. En el panel de **tareas** del lado derecho, haga clic en **nuevo** en el nodo de nombre de dominio. En este ejemplo, haga clic en **nuevo** en el nodo 'contoso100(local)' en el panel de **tareas** del lado derecho.

    ![ADAC - nueva unidad organizativa](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

5. Verá la opción para crear a una unidad organizativa. Haga clic en la **Unidad organizativa** para iniciar el cuadro de diálogo **Crear unidad organizativa** .

6. En el cuadro de diálogo **Crear unidad organizativa** , especifique un **nombre** para la nueva unidad organizativa. Proporcione una breve descripción para la unidad organizativa. También puede establecer el campo **Administrado por** para la unidad organizativa. Para crear la OU personalizada, haga clic en **Aceptar**.

    ![ADAC - cuadro de diálogo OU crear](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

7. La unidad organizativa recién creada ahora debería aparecer en el centro de administración AD (ADAC).

    ![ADAC - OU creado](./media/active-directory-domain-services-admin-guide/create-ou-done.png)


## <a name="permissionssecurity-for-newly-created-ous"></a>Permisos y seguridad de la OU recién creado
De forma predeterminada, el usuario (miembro del grupo 'AAD DC administradores') que creó la OU personalizada se concede privilegios de administrador (control total) sobre la unidad organizativa. El usuario puede continuar y conceder privilegios a otros usuarios o para el grupo 'AAD DC administradores' como desee. Como se ve en la siguiente captura de pantalla, el usuario 'bob@domainservicespreview.onmicrosoft.com' persona que creó la unidad organizativa 'MyCustomOU' se concede control total sobre él.

 ![ADAC - nueva seguridad OU](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)


## <a name="notes-on-administering-custom-ous"></a>Notas acerca de cómo administrar unidades organizativas personalizadas
Ahora que ha creado una OU personalizada, puede continuar y crear usuarios, grupos, equipos y cuentas de servicio en esta unidad organizativa. No puede mover los usuarios o grupos de la 'AAD DC usuarios' OU a unidades organizativas personalizadas.

> [AZURE.WARNING] Cuentas de usuario, grupos, cuentas de servicio y objetos de equipo que cree en unidades organizativas personalizadas no están disponibles en su inquilino de Azure AD. En otras palabras, estos objetos no se muestran el uso de la API de Azure AD gráfico o en la interfaz de usuario de Azure AD. Estos objetos solo están disponibles en su dominio de servicios de dominio de Active Directory de Azure administrado.


## <a name="related-content"></a>Contenido relacionado

- [Administrar un dominio administrado de servicios de dominio de Active Directory de Azure](active-directory-ds-admin-guide-administer-domain.md)

- [Centro de administración de Active Directory: Introducción](https://technet.microsoft.com/library/dd560651.aspx)

- [Guía paso a paso de las cuentas de servicio](https://technet.microsoft.com/library/dd548356.aspx)
