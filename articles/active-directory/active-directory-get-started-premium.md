<properties
    pageTitle="Introducción a Azure Active Directory Premium"
    description="Tema que explica cómo registrarse para edición de Azure Active Directory Premium a través del sitio web de licencias por volumen."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila" 
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/16/2016"
    ms.author="markvi"/>

# <a name="getting-started-with-azure-active-directory-premium"></a>Introducción a Azure Active Directory Premium


Para iniciar sesión en Premium de Active Directory, tiene varias opciones: 

**Azure u Office 365** - como suscriptor de Azure u Office 365, puede comprar Premium de Active Directory en línea. Para conocer los pasos detallados, vea [cómo adquirir Azure Active Directory Premium - los clientes existentes](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer) o [compra de Azure Active Directory Premium - nuevos clientes](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers).  

**Movilidad de la empresa + seguridad** - Enterprise movilidad + seguridad (anteriormente Enterprise movilidad Suite) es una manera rentable para organizaciones usar los siguientes servicios juntos en un plan de licencias: Active Directory Premium, Azure Rights Management, Microsoft Intune. Para obtener más información, consulte el sitio web de [movilidad de la empresa + seguridad](https://www.microsoft.com/en-us/server-cloud/enterprise-mobility/overview.aspx) . Para obtener la prueba gratuita de 30 días de e, haga clic en [aquí](https://portal.office.com/Signup/Signup.aspx?OfferId=2E63A04D-BE0B-4A0F-A8CF-407C1C299221&dl=EMS&ali=1#0).


**Licencias por volumen de Microsoft** - Azure Active Directory Premium está disponible a través de un [Contrato Microsoft Enterprise](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) (250 o más licencias) o el programa de [Licencias por volumen Open](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) (5: 250 licencias).


En este tema se muestra cómo empezar a trabajar con un Premium de Azure Active Directory que ha comprado a través del programa de licencias por volumen. Si no todavía está familiarizado con las diferentes ediciones de Azure Active Directory, vea [ediciones de Azure Active Directory](active-directory-editions.md).  

> [AZURE.NOTE]
Azure Active Directory Premium y básico las ediciones están disponibles para los clientes en China utilizando la instancia de Azure Active Directory en todo el mundo. Azure Active Directory Premium y básico las ediciones no se admiten actualmente en el servicio de Microsoft Azure a través de 21Vianet en China. Para obtener más información, póngase en contacto con nosotros en el [Foro de Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).




## <a name="step-1-sign-up-for-active-directory-premium"></a>Paso 1: Iniciar sesión para Premium de Active Directory

Para suscribirse, vea [cómo comprar mediante licencias por volumen](http://www.microsoft.com/en-us/licensing/how-to-buy/how-to-buy.aspx).



## <a name="step-2-activate-your-license-plan"></a>Paso 2: Activar su plan de licencia

¿Es la primera compra de plan de licencia a través del programa de licencias por volumen de empresa de Microsoft?
En este caso, recibe un mensaje de confirmación cuando se haya completado la compra.
Necesita este correo electrónico para activar el primer plan de licencia.

En cualquier otra compra subsiguientes para este directorio, las licencias se activan automáticamente en el mismo directorio.



**Para activar su plan de licencias, realice uno de los siguientes pasos:**


1. Para iniciar la activación, haga clic en **Iniciar sesión** o **Registrarse**.

    ![Inicia sesión][1]



    - Si tiene un inquilino existente, haga clic en **Iniciar sesión** para iniciar sesión con su cuenta de administrador existente. Debe iniciar sesión con las credenciales de administrador global desde el directorio donde deben activar las licencias.

    - Si desea crear un nuevo inquilino de Azure Active Directory para usarlo con su plan de licencias, haga clic en **Registrarse** para abrir el cuadro de diálogo **Crear cuenta de perfil** .

        ![Cree el perfil de la cuenta][2]

Cuando haya terminado, se muestra el cuadro de diálogo siguiente como confirmación para la activación del plan de licencia para el inquilino.

![Confirmación][3]

## <a name="step-3-activate-your-azure-active-directory-access"></a>Paso 3: Activar el acceso de Azure Active Directory

Si ha utilizado Microsoft Azure antes, puede continuar con el [paso 4](#step-4-assign-license-to-user-accounts). 

Cuando las licencias se aprovisiona al directorio, se envía un **correo electrónico de bienvenida** a usted. El correo electrónico confirma que puede empezar a administrar licencias de Azure Active Directory Premium o serie de movilidad para empresas y características. 

Si comete un intento de activar el acceso a Azure Active Directory antes de recibir el correo electrónico de bienvenida, recibe el siguiente mensaje de error. 

![Access no está disponible][9]

Si se vuelva a intentarlo en unos minutos una vez ha recibido el correo electrónico.

Administradores de nuevos en su suscripción también pueden activar el acceso al portal de clásico Azure a través de este vínculo.






**Para activar el acceso de Azure Active Directory, realice los pasos siguientes:**

1. En el **correo electrónico de bienvenida**, haga clic en **Iniciar sesión**. 
    
    ![Correo electrónico de bienvenida][4]

2. Cuando haya iniciado sesión correctamente, debe completar una segunda autenticación factor en forma de una comprobación móvil:

    ![Comprobación de móvil][5]

La activación puede tardar unos minutos. Una vez que se activa el acceso, desaparece de la barra de color marrón y puede hacer clic en el **Portal**.

![Espere mientras se configura][6]

En este caso, el acceso de Azure se limita a Azure Active Directory.

![Capacidades de Azure][7]

Es posible ya tengas acceso a Azure de uso anterior; Además, puede actualizar el acceso de Azure Active Directory para Azure acceso completo activando la suscripciones adicionales de Azure. En estos casos, el portal clásico Azure tiene más funciones.

![Capacidades de Azure][8]



## <a name="step-4-assign-license-to-user-accounts"></a>Paso 4: Asignar licencias a cuentas de usuario

Antes de empezar a utilizar el plan que ha adquirido, debe manualmente asignar licencias a cuentas de usuario de la organización para que pueden usar las características avanzadas proporcionadas Premium. Use los siguientes pasos para asignar licencias a usuarios para que se pueden usar las características Premium de Azure Active Directory.

**Para asignar licencias a los usuarios, siga estos pasos:**

1. Inicie sesión en el portal de clásico Azure como administrador global del directorio que desea personalizar.
2. Haga clic en **Active Directory**y, a continuación, seleccione el directorio donde desea asignar licencias.
3. Seleccione la ficha **licencias** , seleccione **Premium de Active Directory** o **Serie de movilidad para empresas**y, a continuación, haga clic en **asignar**.

    ![Planes de licencia][10]

4. En el cuadro de diálogo, seleccione los usuarios que desea asignar licencias a y, a continuación, haga clic en el icono de marca de verificación para guardar los cambios.

    ![Asignar licencias][11]

### <a name="license-restrictions"></a>Restricciones de licencia

Algunos planes de licencia son subconjuntos o superconjuntos de otros planes de licencia. Normalmente, un usuario no se puede asignar un plan de licencia que ya se ha asignado a ellos. Si es la intención de asignar un plan de licencia que es un superconjunto, debe quitar primero el plan de licencia subconjunto.

### <a name="license-requirements"></a>Requisitos de licencia

Cuando se asigna una licencia a un usuario, puede especificar una ubicación de uso principal en las propiedades de su cuenta. Si no se especifica una ubicación de uso, la ubicación del inquilino se asigna automáticamente al usuario.

![Ubicación de usuario][12]

La disponibilidad de características de un servicio de nube de Microsoft y servicios varía según el país o región. Puede ser un servicio, como voz sobre IP (VoIP), disponible en un país o región y no está disponible en otro. Funciones dentro de un servicio se pueden restringidas por razones legales en algunos países o regiones. Para ver si un servicio o característica está disponible con o sin restricciones, busque su país o región en sitio de restricciones de licencia de un servicio.

## <a name="whats-next"></a>¿Qué es la siguiente

- [Agregar la marca para iniciar sesión y Panel de acceso a las páginas de la empresa](active-directory-add-company-branding.md)
- [Ver los informes de access y el uso](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png
