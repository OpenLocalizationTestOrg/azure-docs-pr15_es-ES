<properties
    pageTitle="Administrar nombres de dominio personalizado en la vista previa de Azure Active Directory | Microsoft Azure"
    description="Conceptos de administración y procedimientos de administración de un nombre de dominio de Active Directory de Azure"
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="curtand;jeffsta"/>

# <a name="managing-custom-domain-names-in-your-azure-active-directory-preview"></a>Administrar nombres de dominio personalizado en la vista previa de Azure Active Directory

Un nombre de dominio es una parte importante del identificador de muchos de los recursos de directorio: forma parte de una dirección de correo electrónico o de nombre de usuario para un usuario, parte de la dirección de un grupo y puede que forme parte de la aplicación URI de identificador para una aplicación. Un recurso en la vista previa de Azure Active Directory (AD Azure) puede incluir un nombre de dominio que ya se comprueba que sea propietario del directorio que contiene el recurso. [¿Qué es la vista previa?](active-directory-preview-explainer.md) Solo un administrador global puede realizar las tareas de administración de dominios en Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Establecer el nombre de dominio principal para el directorio de Azure AD

Cuando se crea el directorio, el nombre de dominio inicial, como 'contoso.onmicrosoft.com', también es el nombre de dominio principal. El dominio principal es el nombre de dominio predeterminado para un nuevo usuario cuando se crea un nuevo usuario. Esto simplifica el proceso de administrador para crear nuevos usuarios en el portal. Para cambiar el nombre de dominio principal:

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com) con una cuenta que sea un administrador global para el directorio.

2.  Seleccione **más servicios**, escriba **Azure Active Directory** en el cuadro de texto y, a continuación, presione **ENTRAR**.

    ![Administración de usuarios de apertura](./media/active-directory-domains-add-azure-portal/user-management.png)

3. En el módulo de ***nombre del directorio*** , seleccione **los nombres de dominio**.

4. En el módulo de ** *nombre de directorio* : nombres de dominio** , seleccione el nombre de dominio que desea que el nombre de dominio principal.

5.  En el módulo de ***nombre de dominio*** (es decir, el módulo que se abre con el nuevo nombre de dominio en el título), seleccione el comando **convertir en principal** . Confirmar la elección cuando se le solicite.

    ![Convertir en un nombre de dominio principal](./media/active-directory-domains-manage-azure-portal/make-primary.png)

Puede cambiar el nombre de dominio principal para el directorio de ser cualquier dominio personalizado comprobado no federado. Cambiar el dominio principal para el directorio no cambiará los nombres de usuario para todos los usuarios existentes.

## <a name="add-custom-domain-names-to-your-azure-ad"></a>Agregar nombres de dominio personalizado a su Azure AD

Puede agregar hasta 900 nombres de dominio personalizado para cada directorio de Azure AD. El proceso para [Agregar un nombre de dominio personalizado adicional](active-directory-domains-add-azure-portal.md) es el mismo para el primer nombre de dominio personalizado.

## <a name="add-subdomains-of-a-custom-domain"></a>Agregar subdominios de un dominio personalizado

Si desea agregar un nombre de dominio de tercer nivel, como 'europe.contoso.com' al directorio, primero debe agregar y comprobar el dominio de segundo nivel, como por ejemplo contoso.com. El subdominio automáticamente verificará Azure AD. Para ver que se ha comprobado el subdominio que acaba de agregar, actualizar la página en el explorador que se muestra los dominios.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Qué hacer si cambia al registrador DNS para el nombre de dominio personalizado

Si cambia al registrador DNS para el nombre de dominio personalizado, aún puede usar su nombre de dominio personalizado con Azure AD propio sin interrupciones y sin tareas de configuración adicionales. Si usa el nombre de dominio personalizado con Office 365, Intune u otros servicios que se basan en los nombres de dominio personalizado en Azure AD, consulte la documentación de los servicios.

## <a name="delete-a-custom-domain-name"></a>Eliminar un nombre de dominio personalizado

Puede eliminar un nombre de dominio personalizado de su Azure AD si su organización ya no utiliza ese nombre de dominio, o si necesita usar ese nombre de dominio con otro Azure AD.

Para eliminar un nombre de dominio personalizado, primero debe asegurarse de que no hay recursos en el directorio se basan en el nombre de dominio. No puede eliminar un nombre de dominio desde el directorio si:

-   Cualquier usuario tiene un nombre de usuario, la dirección de correo electrónico o la dirección de proxy que incluye el nombre de dominio.

-   Cualquier grupo tiene una dirección de correo electrónico o la dirección del proxy que incluya el nombre de dominio.

-   Cualquier aplicación de su Azure AD tiene una identificador URI que incluya el nombre de dominio de aplicación.

Debe cambiar o eliminar dichos recursos en el directorio de Azure AD antes de eliminar el nombre de dominio personalizado.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Usar PowerShell o gráfico API para administrar los nombres de dominio

También pueden completar tareas de administración de la mayoría de los nombres de dominio de Active Directory de Azure con PowerShell de Microsoft o mediante programación utilizando las API de Azure AD gráfico (en la versión preliminar pública).

-   [Usar PowerShell para administrar los nombres de dominio en Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Usar gráfico API para administrar los nombres de dominio en Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Pasos siguientes

-   [Agregar nombres de dominio personalizado](active-directory-domains-add-azure-portal.md)
