<properties
    pageTitle="Agregar nombre de dominio personalizado y configurar el inicio de sesión federados en Azure Active Directory | Microsoft Azure"
    description="Cómo agregar nombres de dominio de su empresa a Azure Active Directory y cómo configurar un inicio de sesión federado entre Azure Active Directory y la solución de federación local."
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
    ms.topic="get-started-article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="add-your-custom-domain-name-to-azure-active-directory"></a>Agregar nombre de dominio personalizado a Azure Active Directory

Puede configurar un nombre de dominio personalizado, como 'contoso.com', para que los usuarios en contoso.com pueden tener un federados inicio de sesión único de la red corporativa. Si ya dispone de los servicios de federación de Active Directory (AD FS) o un servidor de federación diferentes que se ejecutan en su red corporativa, puede configurar Azure AD para utilizar su nombre de dominio personalizado con la herramienta de Azure AD Connect. También puede usar Azure AD Connect para implementar un entorno de AD FS y configurar para federados inicio de sesión único a Azure AD.

Si no tiene y no va a implementar AD FS u otro servidor de federación, siga estas instrucciones: [Agregar un nombre de dominio personalizado para Azure Active Directory](active-directory-add-domain.md).

## <a name="add-a-custom-domain-name-to-your-directory"></a>Agregar un nombre de dominio personalizado a su directorio

1. Inicie sesión en el [portal de clásica Azure](https://manage.windowsazure.com/) con una cuenta de usuario es un administrador global de su directorio de Azure AD.

2. En **Active Directory**, abra el directorio y seleccione la pestaña **Domains** .

3. En la barra de comandos, seleccione **Agregar**y, a continuación, escriba el nombre de dominio personalizado, como por ejemplo 'contoso.com'. Asegúrese de incluir el .com, .net u otra extensión de nivel superior.

4. Seleccione la casilla de verificación **va a configurar este dominio para el inicio de sesión único con mi Active Directory local** .

5. Haga clic en **Agregar**.

Ejecutar la herramienta de Azure AD Connect para obtener la entrada DNS Azure AD utilizará para comprobar el dominio. Verá la entrada DNS en el paso de **Dominio de Active Directory de Azure** en el asistente. Puede ver lo que este paso del asistente es similar a [en estas instrucciones](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Si no tiene la herramienta de Azure AD Connect, puede [descargarlo aquí](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Agregar la entrada DNS en el registrador de nombres de dominio para el dominio

El siguiente paso para usar su nombre de dominio personalizado con Azure AD es para actualizar el archivo de zona DNS del dominio. Esto permite Azure AD verificar que posee el nombre de dominio personalizado en su organización.

1. Inicie sesión en el sitio Web para el registrador de nombres de dominio para el nombre de dominio. Si no tiene acceso a hacerlo, pida a la persona o grupo de su organización que tenga acceso para completar el paso 2 y le avise cuando se complete.

2. Actualice el archivo de zona DNS del dominio mediante la adición de la entrada DNS provista por Azure AD. Esta entrada DNS permite Azure AD que compruebe la propiedad del dominio. La entrada DNS no cambia alguno de los comportamientos, como el enrutamiento de correo u hospedaje web.

Para obtener ayuda con este paso, lea [las instrucciones para agregar una entrada DNS en registradores DNS populares](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Compruebe el nombre de dominio con Azure AD

Una vez que haya agregado la entrada DNS, ya está listo para comprobar el nombre de dominio con Azure AD.

Para comprobar el dominio, seleccione **siguiente** en el paso de **Azure AD dominio** del Asistente de Azure AD Connect. Azure AD buscará la entrada DNS en el archivo de zona DNS del dominio. Azure AD comprobar solo el nombre de dominio, una vez que se han propagado los registros DNS. A menudo propagación tarda a segundos, pero a veces puede tardar una hora o más. Si la comprobación no funciona la primera vez, intente más tarde.

A continuación, continúe con los pasos restantes del Asistente de Azure AD Connect. Se sincronizarán de los usuarios de su servidor AD de Windows Azure AD. Usuarios sincronizados en el dominio que ha configurado para la federación podrán obtener un federados inicio de sesión único de su red corporativa a Azure AD.

## <a name="troubleshooting"></a>Solución de problemas

Si no puede comprobar un nombre de dominio personalizado, pruebe lo siguiente. Se deberá iniciar con los más comunes y trabajar hacia abajo hasta la menos comunes.

1.  **Espere una hora**. Registros DNS que necesite propagarse antes de Azure AD puede comprobar el dominio. Esto puede tardar una hora o más.

2.  **Asegúrese de que se ha introducido el registro DNS, y que es correcta**. Completar este paso en el sitio Web para el registrador de nombres de dominio para el dominio. Azure AD no puede comprobar el nombre de dominio, si la entrada DNS no está presente en el archivo de zona DNS, o si no es una coincidencia exacta con la entrada DNS que Azure AD proporcionado. Si no tiene acceso al actualizar los registros DNS para el dominio en el registrador de nombres de dominio, compartir la entrada DNS con la persona o grupo en su organización que tiene este acceso y pídale que agregar la entrada DNS.

3.  **Eliminar el nombre de dominio desde otro directorio de Azure AD**. Puede comprobar un nombre de dominio en solo un único directorio. Si previamente se ha comprobado un nombre de dominio en otro directorio, se deben eliminarse allí antes de que se pueden comprobar en su directorio nuevo. Para obtener información sobre cómo eliminar los nombres de dominio, lea [Administrar nombres de dominio personalizado](active-directory-add-manage-domain-names.md).

## <a name="add-more-custom-domain-names"></a>Agregar más nombres de dominio personalizado

Si su organización usa varios nombres de dominio personalizado, como 'contoso.com' y 'contosobank.com', puede agregarlos hasta un máximo de 900 nombres de dominio. Use los mismos pasos de este artículo para agregar cada uno de los nombres de dominio.

## <a name="next-steps"></a>Pasos siguientes

-   [Administrar nombres de dominio personalizado](active-directory-add-manage-domain-names.md)
-   [Obtenga más información sobre los conceptos de administración de dominio en Azure AD](active-directory-add-domain-concepts.md)
-   [Mostrar la que marca de su empresa de cuando los usuarios iniciar sesión](active-directory-add-company-branding.md)
-   [Usar PowerShell para administrar los nombres de dominio en Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
