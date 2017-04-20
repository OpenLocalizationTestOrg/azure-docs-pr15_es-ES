<properties
pageTitle="Personalizar la página de inicio de sesión en la vista previa de Azure Active Directory | Microsoft Azure"
description="Obtenga información sobre cómo agregar una marca a la página de inicio de sesión de Azure de la empresa"
services="active-directory"
documentationCenter=""
authors="curtand"
manager="femila"
editor=""/>

<tags
ms.service="active-directory"
ms.workload="identity"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="09/30/2016"
ms.author="curtand"/>

# <a name="add-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Agregar la marca a la página de inicio de sesión en la vista previa de Azure Active Directory de la empresa

Para evitar confusiones, muchas empresas desean aplicar un aspecto coherente en todos los sitios Web y los servicios que administran. Vista previa de Active Directory Azure proporciona esta capacidad por lo que le permite personalizar la apariencia de la página de inicio de sesión con el logotipo de la compañía y las combinaciones de colores personalizadas. [¿Qué es la vista previa?](active-directory-preview-explainer.md) La página de inicio de sesión es la página que aparece al iniciar sesión en Office 365 u otras aplicaciones basadas en web que están utilizando Azure AD como su proveedor de identidades. Interactuar con esta página para escribir sus credenciales.

Si desea mostrar la marca de su empresa, colores y otros elementos personalizables en esta página, vea las imágenes siguientes para comprender la diferencia entre las dos experiencias.

La siguiente captura de pantalla muestra y ejemplo de la página de inicio de sesión de Office 365 en un equipo de escritorio **antes de** una personalización:

![Página de inicio de sesión de Office 365 antes de personalización](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-before-customization.png)

La siguiente captura de pantalla muestra y ejemplo de la página de inicio de sesión de Office 365 en un equipo de escritorio **después de** una personalización:

![Página de inicio de sesión de Office 365 después de personalización](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-after-customization.png)


## <a name="customizing-the-sign-in-page"></a>Personalizar la página de inicio de sesión

Normalmente, si necesita acceso basado en explorador a las aplicaciones de la nube y servicios que se ha suscrito su organización, use la página de inicio de sesión.

Si ha aplicado los cambios a la página de inicio de sesión, puede tardar hasta una hora para que aparezcan los cambios.

Una página de inicio de sesión con su marca sólo aparece cuando visita un servicio con una dirección URL de inquilino específicos como https://outlook.com/**contoso**.com o https://mail. **Contoso**. com.

Cuando visita un servicio con direcciones URL no inquilino (por ejemplo: https://mail.office365.com), aparece una página de inicio de sesión sin ninguna marca. en este caso, la marca aparece una vez que ha escrito el identificador de usuario o que ha seleccionado un mosaico de usuario.

> [AZURE.NOTE]
>
- El nombre de dominio debe aparecer como "Activa" en la parte de **dominios** del portal de Azure en la que ha configurado personalización de marca. Para obtener más información, vea [Agregar nombres de dominio personalizado](active-directory-domains-add-azure-portal.md).
- Página de inicio de sesión de marca no llevar a la página de Microsoft de inicio de sesión de consumidores. Si inicie sesión con una cuenta de Microsoft, puede ver una lista con marca de mosaicos de usuario procesados por Azure AD, pero la personalización de marca de su organización no se aplica a la página de inicio de sesión de cuenta de Microsoft.

En la página de inicio de sesión, la casilla de verificación **mantener la sesión iniciada** permite a un usuario seguir conectado cuando cierre y vuelva a abrir el explorador. 

   ![Mantenerme ha iniciado sesión en](./media/active-directory-branding-custom-signon-azure-portal/01.png)

No afecta a duración de la sesión. Puede ocultar la casilla de verificación en la página de inicio de sesión de Azure Active Directory.
Si se muestra la casilla de verificación depende de la configuración de **mantener la sesión iniciada en deshabilitado**.

   ![Mantenerme ha iniciado sesión en](./media/active-directory-branding-custom-signon-azure-portal/02.png)


Para ocultar la casilla de verificación, configure este valor en **Sí**. 

> [AZURE.NOTE] Algunas características de SharePoint Online y Office 2010 dependen de usuarios poder Active esta casilla. Configure esta opción en oculto, los usuarios pueden aparecer adicionales e inesperados indicaciones para iniciar sesión.




**Para agregar la marca al directorio de la empresa:**

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com) con una cuenta que sea un administrador global para el directorio.

2.  Seleccione **más servicios**, escriba **los usuarios y grupos** en el cuadro de texto y, a continuación, presione **ENTRAR**.

    ![Administración de usuarios de apertura](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)

3. En el módulo de **usuarios y grupos** , seleccione la **marca de empresa**.

4. En el **usuarios y grupos: personalización de marca de la compañía** módulo, seleccione el comando **Editar** .

    ![Editar la personalización de marca](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)

5. Modificar los elementos que desea personalizar. Todos los elementos son opcionales.

6. Haga clic en **Guardar**.

Puede tardar hasta una hora para los cambios realizados a la página de inicio de sesión de marca para que aparezca.

## <a name="next-steps"></a>Pasos siguientes

[Agregar la marca de la empresa específicas del idioma](active-directory-branding-localize-azure-portal.md)
