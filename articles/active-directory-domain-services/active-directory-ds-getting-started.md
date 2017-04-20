<properties
    pageTitle="Servicios de dominio de Azure AD: Crear el grupo de administradores de DC AAD | Microsoft Azure"
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
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="get-started-with-azure-ad-domain-services"></a>Introducción a servicios de dominio de Active Directory de Azure

En este artículo le guía a través de las tareas de configuración necesarias para habilitar los servicios de dominio de Active Directory de Azure para su inquilino de Azure AD.

## <a name="task-1-create-the-aad-dc-administrators-group"></a>Tarea 1: Crear el grupo 'AAD DC administradores'
La primera tarea es crear un grupo administrativo en su inquilino de Azure Active Directory. Este grupo administrativo especial se denomina **AAD DC administradores**. Los miembros de este grupo se conceden privilegios de administrador en equipos que están unidos a un dominio en el dominio de servicios de dominio de Active Directory de Azure administrado. En equipos unidos a un dominio, este grupo se agrega al grupo 'Administradores'. Además, los miembros de este grupo pueden utilizar Escritorio remoto conectarse remotamente a equipos unidos a un dominio.  

> [AZURE.NOTE] No tiene privilegios de administrador de dominio o el Administrador de la empresa en el dominio administrado creado con servicios de dominio de Active Directory de Azure. En dominios administrados, estos privilegios reservados por el servicio y no están disponibles para los usuarios dentro del inquilino. Sin embargo, puede usar el administrador especial de grupo creado en esta tarea de configuración, para realizar algunas operaciones con privilegios. Estas operaciones incluyen uniendo los equipos del dominio, que pertenecen al grupo Administradores en equipos unidos a un dominio, configurar etcetera de directiva de grupo.

En esta tarea de configuración, crear un grupo administrativo y agregar uno o más usuarios en el directorio al grupo. Realice los pasos siguientes para crear un grupo administrativo para servicios de dominio de Active Directory de Azure:

1. Desplácese hasta el **portal clásica Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com))

2. Seleccione el nodo de **Active Directory** en el panel izquierdo.

3. Seleccione al inquilino de Azure AD (directorio) para el que desea habilitar servicios de dominio de Active Directory de Azure. Solo puede crear un dominio para cada directorio de Azure AD.

    ![Seleccionar directorio de Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Haga clic en la ficha **grupos** .

5. Para agregar un grupo a su inquilino de Azure AD, haga clic en **Agregar grupo** desde el panel de tareas en la parte inferior de la página.

    ![Agregar botón de grupo](./media/active-directory-domain-services-getting-started/add-group-button.png)

6. Crear un grupo denominado **AAD DC administradores**. Establecer **tipo de grupo de** **seguridad**.

    > [AZURE.WARNING] Para habilitar el acceso de los servicios de dominio de Active Directory de Azure administra el dominio, cree un grupo con este nombre exacto.

    ![Crear grupo de administrador](./media/active-directory-domain-services-getting-started/create-admin-group.png)

7. Agregue una descripción para este grupo para que otros usuarios comprender que este grupo se utiliza para conceder privilegios administrativos dentro de los servicios de dominio de Active Directory de Azure.

8. Una vez creado el grupo, haga clic en el nombre del grupo para ver las propiedades de este grupo. Para agregar usuarios como miembros de este grupo, haga clic en el botón **Agregar miembros** en el panel inferior.

    ![Agregar botón de miembros del grupo](./media/active-directory-domain-services-getting-started/add-group-members-button.png)

9. En el cuadro de diálogo **Agregar miembros** , seleccione los usuarios que deben ser miembros de este grupo y seleccione la casilla de verificación cuando haya terminado.

    ![Agregar usuarios al grupo de administradores](./media/active-directory-domain-services-getting-started/add-group-members.png)

<br>

## <a name="task-2-create-or-select-an-azure-virtual-network"></a>Tarea 2: Crear o seleccionar una red virtual de Azure
[Crear o seleccionar una red virtual Azure](active-directory-ds-getting-started-vnet.md)es la siguiente tarea de configuración.
