<properties
    pageTitle="Asignar licencias para AMF Azure | Microsoft Azure"
    description="Obtenga información sobre cómo asignar licencias de usuario para la autenticación multifactor de Microsoft Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users"></a>Asignar una licencia de Azure AMF, Azure AD Premium o movilidad de la empresa a los usuarios

Si ha adquirido AMF Azure, Azure AD Premium o licencias de la serie de movilidad para empresas, no es necesario crear un proveedor de autenticación multifactor. Una vez que asigne las licencias a los usuarios, puede empezar a habilitarlos para MFA.

## <a name="to-assign-a-license"></a>Para asignar una licencia

1. Inicie sesión como administrador en el [portal de clásico de Azure](https://manage.windowsazure.com) .
2. En la parte izquierda, seleccione **Active Directory**.
3. En la página de Active Directory, haga doble clic en el directorio con los usuarios que desea habilitar.
4. En la parte superior de la página directorio de, seleccione **licencias**.
![Asignar licencias](./media/multi-factor-authentication-get-started-assign-licenses/assign1.png)
5. En la página licencias, seleccione **Autenticación multifactor de Azure**, **Premium de Active Directory**o **Serie de movilidad para empresas**.  Si solo tiene una, a continuación, se debe seleccionar automáticamente.
6. En la parte inferior de la página, haga clic en **asignar**.
![Asignar licencias](./media/multi-factor-authentication-get-started-assign-licenses/assign3.png)
6. En el cuadro que aparece, haga clic en junto a los usuarios o grupos que desee asignar licencias a.  Debe aparecer una marca de verificación verde.
7. Haga clic en el icono de marca de verificación para guardar los cambios.
![Asignar licencias](./media/multi-factor-authentication-get-started-assign-licenses/assign4.png)
8. Verá un mensaje indicando que se han asignado las licencias y cuántas pueden haber un error.  Haga clic en **Aceptar**.
![Asignar licencias](./media/multi-factor-authentication-get-started-assign-licenses/assign5.png)
