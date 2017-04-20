<properties
pageTitle="Agregar específicas del idioma de marca a la página de inicio de sesión en la vista previa de Azure Active Directory de la empresa | Microsoft Azure"
description="Obtenga información sobre cómo agregar una empresa específica del idioma marca imágenes y texto en una página de inicio de sesión de Azure"
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
ms.date="09/12/2016"
ms.author="curtand"/>

# <a name="add-language-specific-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Agregar la marca a la página de inicio de sesión en la vista previa de Azure Active Directory de la empresa de específicas del idioma

Para evitar confusiones, muchas empresas desean aplicar un aspecto coherente en todos los sitios Web y los servicios que administran. Vista previa de Active Directory Azure proporciona esta capacidad por lo que le permite personalizar la apariencia de la página de inicio de sesión con el logotipo de la compañía y las combinaciones de colores personalizadas. [¿Qué es la vista previa?](active-directory-preview-explainer.md) La página de inicio de sesión es la página que aparece al iniciar sesión en Office 365 u otras aplicaciones basadas en web que están utilizando Azure AD como su proveedor de identidades. Interactuar con esta página para escribir sus credenciales.

## <a name="customizing-the-sign-in-page-for-another-language"></a>Personalizar la página de inicio de sesión de otro idioma

Puede agregar elementos específicos de idioma para la página de inicio de sesión personalizada solo si ya ha creado una página de inicio de sesión personalizada como se describe en [Agregar marca a la página de inicio de sesión de la empresa](active-directory-branding-custom-signon-azure-portal.md). Puede configurar una página de inicio de sesión por directorio con un conjunto predeterminado de elementos que se pueden personalizar. Una vez que haya configurado el conjunto predeterminado de elementos de la página, puede configurar más versiones para diferentes idiomas. También puede mezclar y coincidir con varios elementos. Por ejemplo, puede:

- Crear una **imagen de la página de inicio de sesión** que funciona para todas las referencias culturales, a continuación, crear versiones específicas para inglés y en francés de forma predeterminada. Al establecer los exploradores en uno de estos dos idiomas, aparece la imagen específicas del idioma, mientras que aparece en la ilustración de forma predeterminada para todos los otros idiomas.

- Configurar logotipos diferentes para su organización (por ejemplo, versiones japonés o el hebreo).

Le recomendamos que mantenga el número de variantes de idioma baja por motivos de mantenimiento y rendimiento.

**Para agregar la marca al directorio de la empresa:**

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com) con una cuenta que sea un administrador global para el directorio.

2.  Seleccione **más servicios**, escriba **los usuarios y grupos** en el cuadro de texto y, a continuación, presione **ENTRAR**.

    ![Administración de usuarios de apertura](./media/active-directory-branding-localize-azure-portal/user-management.png)

3. En el módulo de **usuarios y grupos** , seleccione la **marca de empresa**.

4. En el **usuarios y grupos: personalización de marca de la compañía** módulo, seleccione el comando **Agregar idioma** .

    ![Agregar elementos de personalización de marca específicos de idioma](./media/active-directory-branding-localize-azure-portal/add-language.png)

5. Modificar los elementos que desea personalizar. Todos los elementos son opcionales.

6. Haga clic en **Guardar**.

Puede tardar hasta una hora para los cambios realizados a la página de inicio de sesión de marca para que aparezca.

## <a name="next-steps"></a>Pasos siguientes

[Agregar la marca a la página de inicio de sesión de la empresa](active-directory-branding-custom-signon-azure-portal.md)
