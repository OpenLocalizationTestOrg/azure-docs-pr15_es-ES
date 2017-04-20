<properties
    pageTitle="Agregar nombre de dominio personalizado a la vista previa de Azure Active Directory | Microsoft Azure"
    description="Cómo agregar nombres de dominio de su empresa a Azure Active Directory y cómo comprobar el nombre de dominio."
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
    ms.date="10/17/2016"
    ms.author="curtand"/>

# <a name="add-a-custom-domain-name-to-azure-active-directory-preview"></a>Agregar un nombre de dominio personalizado a la vista previa de Azure Active Directory

> [AZURE.SELECTOR]
- [Portal de Azure](active-directory-domains-add-azure-portal.md)
- [Portal de clásico de Azure](active-directory-add-domain.md)

Tiene uno o más nombres de dominio que su organización usa para hace negocios y, a continuación, los usuarios iniciar sesión en su red corporativa utilizando su nombre de dominio corporativo. Usar la vista previa de Azure Active Directory (AD Azure), puede agregar el nombre de dominio corporativo a Azure AD también. [¿Qué es la vista previa?](active-directory-preview-explainer.md) Esto le permite asignar nombres de usuario en el directorio que ya conocidos a los usuarios, como por ejemplo ‘alice@contoso.com.’ el proceso es sencillo:

1. Agregue el nombre de dominio personalizado a su directorio
2. Agregar una entrada de DNS para el nombre de dominio en el registrador de nombres de dominio
3. Compruebe el nombre de dominio personalizado en Azure AD

## <a name="how-do-i-add-a-domain-name"></a>¿Cómo puedo agregar un nombre de dominio?

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com) con una cuenta que sea un administrador global para el directorio.

2.  Seleccione **más servicios**, escriba **Azure Active Directory** en el cuadro de texto y, a continuación, presione **ENTRAR**.

    ![Administración de usuarios de apertura](./media/active-directory-domains-add-azure-portal/user-management.png)

3. En el módulo de ***nombre del directorio*** , seleccione **los nombres de dominio**.

4. En el módulo de ** *nombre de directorio* : nombres de dominio** , seleccione el comando **Agregar** .

  ![Seleccionar el comando Agregar](./media/active-directory-domains-add-azure-portal/add-command.png)

5. En el módulo de **nombre de dominio** , escriba el nombre del dominio personalizado en el cuadro, como 'contoso.com' y, a continuación, seleccione **Agregar dominio**. Asegúrese de incluir el .com, .net u otra extensión de nivel superior.

6. En el módulo de ***nombre de dominio*** (es decir, el módulo que se abre con el nuevo nombre de dominio en el título), obtener la información de entrada DNS Azure AD se usan para verificar que posee el nombre de dominio personalizado en su organización.

  ![obtener información de entrada DNS](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

Ahora que ha agregado el nombre de dominio, Azure AD debe comprobar que posee el nombre de dominio de su organización. Antes de Azure AD puede llevar a cabo esta comprobación, debe agregar una entrada DNS en el archivo de zona DNS para el nombre de dominio. Esta tarea se realiza en el sitio Web para el registrador de nombres de dominio para el nombre de dominio.

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Agregar la entrada DNS en el registrador de nombres de dominio para el dominio

El siguiente paso para usar su nombre de dominio personalizado con Azure AD es para actualizar el archivo de zona DNS del dominio. Esto permite Azure AD verificar que posee el nombre de dominio personalizado en su organización.

1.  Inicie sesión en el registrador de nombres de dominio para el dominio. Si no tiene acceso a la actualización de la entrada DNS, pida a la persona o grupo que tenga acceso para completar el paso 2 y le avise cuando se complete.

2.  Actualice el archivo de zona DNS del dominio mediante la adición de la entrada DNS provista por Azure AD. Esta entrada DNS permite Azure AD que compruebe la propiedad del dominio. La entrada DNS no cambia alguno de los comportamientos, como el enrutamiento de correo u hospedaje web.

Para obtener ayuda con este agregar la entrada DNS, lea [las instrucciones para agregar una entrada DNS en registradores DNS populares](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Compruebe el nombre de dominio con Azure AD

Una vez que haya agregado la entrada DNS, ya está listo para comprobar el nombre de dominio con Azure AD.

Puede comprobar un nombre de dominio después de que hayan propagado los registros DNS. A menudo esta propagación tarda a segundos, pero a veces puede tardar una hora o más. Si la comprobación no funciona la primera vez, intente más tarde.

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com) con una cuenta que sea un administrador global para el directorio.

2.  Seleccione **Examinar**, escriba administración de usuarios en el cuadro de texto y, a continuación, presione **ENTRAR**.

    ![Administración de usuarios de apertura](./media/active-directory-domains-add-azure-portal/user-management.png)

3. En el módulo de **administración de usuario, nombres de dominio** , seleccione el nombre de dominio no verificada que desea comprobar.

4. En el módulo de ***nombre de dominio*** (es decir, el módulo que se abre con el nuevo nombre de dominio en el título), seleccione **Comprobar** para completar la verificación.

Ahora puede [asignar nombres de usuario que incluyen su nombre de dominio personalizado](active-directory-users-create-azure-portal.md).

## <a name="troubleshooting"></a>Solución de problemas

Si no puede comprobar un nombre de dominio personalizado, pruebe lo siguiente. Se deberá iniciar con los más comunes y trabajar hacia abajo hasta la menos comunes.

1.  **Espere una hora**. Registros DNS que necesite propagarse antes de Azure AD puede comprobar el dominio. Esto puede tardar una hora o más.

2.  **Asegúrese de que se ha introducido el registro DNS, y que es correcta**. Completar este paso en el sitio Web para el registrador de nombres de dominio para el dominio. Azure AD no puede comprobar el nombre de dominio, si la entrada DNS no está presente en el archivo de zona DNS, o si no es una coincidencia exacta con la entrada DNS que Azure AD proporcionado. Si no tiene acceso al actualizar los registros DNS para el dominio en el registrador de nombres de dominio, compartir la entrada DNS con la persona o grupo en su organización que tiene este acceso y pídale que agregar la entrada DNS.

3.  **Eliminar el nombre de dominio desde otro directorio de Azure AD**. Puede comprobar un nombre de dominio en solo un único directorio. Si previamente se ha comprobado un nombre de dominio en otro directorio, se deben eliminarse allí antes de que se pueden comprobar en su directorio nuevo. Para obtener información sobre cómo eliminar los nombres de dominio, lea [Administrar nombres de dominio personalizado](active-directory-domains-manage-azure-portal.md).    

## <a name="add-more-custom-domain-names"></a>Agregar más nombres de dominio personalizado

Si su organización usa varios nombres de dominio personalizado, como 'contoso.com' y 'contosobank.com', puede agregarlos hasta un máximo de 900 nombres de dominio. Use los mismos pasos de este artículo para agregar cada uno de los nombres de dominio.

## <a name="next-steps"></a>Pasos siguientes

[Administrar nombres de dominio personalizado](active-directory-domains-manage-azure-portal.md)
