<properties
   pageTitle="Directrices de marca para aplicaciones | Microsoft Azure"
   description="Una guía completa a recursos orientados a desarrolladores para Azure Active Directory"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/23/2016"
   ms.author="mbaldwin"/>


# <a name="branding-guidelines-for-applications"></a>Directrices de personalización de marca para aplicaciones


Este tema describen las directrices de personalización de marca que debe usar al desarrollar aplicaciones con Azure Active Directory (AD Azure). Estas instrucciones le ayudarán a dirigir a sus clientes al que desean usar su cuenta profesional o educativa, administrado en Azure AD o su personal de la cuenta de suscripción e inicio de sesión a la aplicación.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Cuentas personales frente trabajo o escuela de Microsoft

Microsoft administra dos tipos de cuentas de usuario:

- **Cuentas personales** (anteriormente conocida como Windows Live ID). Estas cuentas representan la relación entre los usuarios *individuales* y Microsoft y se usan para tener acceso a los dispositivos de consumidor y servicios de Microsoft. Estas cuentas están destinadas a su uso personal.

- **Cuentas de trabajo o escuela.** Microsoft administra estas cuentas en nombre de las organizaciones que usan Azure Active Directory. Estas cuentas se utilizan para iniciar sesión en Office 365 y otros servicios de Microsoft.

Cuentas de trabajo o escuela se suelen asignar a los usuarios finales (empleados, estudiantes, empleados federales) por su organización (empresa, escuela, gobierno) de Microsoft. Estas cuentas se domine directamente en la nube, en la plataforma de Azure AD o sincronizadas con Azure AD desde un directorio local, por ejemplo, Active Directory de Windows Server. Microsoft es el *custodio* de las cuentas de trabajo o escuela, pero las cuentas son su propiedad y controladas por la organización.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Hacer referencia a las cuentas de Azure AD en la aplicación

Microsoft no expone los usuarios finales a la Azure o los nombres de marca de Active Directory y ninguna de ellas debe.

- Una vez que los usuarios iniciados sesión, debe usar el nombre y el logotipo tanto como sea posible de la organización. Esto es mejor que con términos genéricos como "la organización".

- Cuando los usuarios no se ha iniciado sesión, debe hacer referencia a su cuenta como "trabajo o escuela cuentas" y usar el logotipo de Microsoft para transmitir que Microsoft administra estas cuentas. No use términos como "cuenta de la empresa", "cuenta de empresa" o "cuenta corporativa" que crea confusión de usuario.

## <a name="user-account-pictogram"></a>Pictograma de cuenta de usuario
En una versión anterior de estas directrices, se recomienda usar un pictograma "distintivo azul". En función de los comentarios de usuario y programador, ahora se recomienda el uso del logotipo de Microsoft en su lugar. Esto le ayudará a los usuarios comprender que puede volver a utilizar la cuenta que uso con Office 365 u otro Microsoft servicios empresarial para iniciar sesión en su aplicación.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Registra y volver a iniciar sesión con Azure AD

La aplicación puede presentar rutas independientes para suscripción e inicio de sesión y las secciones siguientes proporcionan instrucciones visuales en ambos casos.

**Si su aplicación es compatible con el inicio de sesión de usuario final (por ejemplo, gratuita al modelo de evaluación o freemium) de arriba**: puede mostrar un botón de **Inicio de sesión** que permite a los usuarios obtener acceso a la aplicación con su cuenta personal o su cuenta de trabajo. Azure AD mostrará un mensaje de consentimiento la primera vez que tienen acceso a la aplicación.

**Si su aplicación requiere permisos que solo los administradores pueden consentimiento, o si su aplicación requiere licencias organizativa**: debe separar adquisición de administración de inicio de sesión. El **botón "get esta aplicación"** redirigirá a los administradores para iniciar sesión, a continuación, pídales que conceder consentimiento en nombre de los usuarios de su organización. Esto tiene la ventaja de suprimir indicaciones de consentimiento de los usuarios finales a su aplicación.

## <a name="visual-guidance-for-app-acquisition"></a>Guía visual para adquisición de aplicación

El vínculo "obtener la aplicación" debe redirigir al usuario para conceder acceso de Azure AD (autorizar) página, para permitir que el Administrador de la organización autorizar su aplicación para tener acceso a los datos de la organización que está hospedados por Microsoft. Obtener más información sobre cómo solicitar acceso se describe en el artículo de la [Integración de las aplicaciones con Azure Active Directory](active-directory-integrating-applications.md) .

Después de que los administradores consentimiento a su aplicación, puede agregar a la experiencia de selector de aplicación de Office 365 de sus usuarios (accesible desde el icono de retícula y desde [https://portal.office.com/myapps](https://portal.office.com/myapps)). Si desea anunciar esta capacidad, puede usar términos como "Agregar esta aplicación a su organización" y mostrar un botón similar a esta:

![Tipos de aplicaciones y escenarios](./media/active-directory-branding-guidelines/add-to-my-org.png)

Sin embargo, se recomienda que escriba el texto explicativo en lugar de confiar en los botones. Por ejemplo:
> *Si ya usa Office 365 u otro servicio de empresa de Microsoft, simplemente puede conceder < your_app_name > acceso a datos de su organización. Esto le permitirá a los usuarios obtener acceso a < your_app_name > con sus cuentas de trabajo existente.*


## <a name="visual-guidance-for-sign-in"></a>Guía visual para el inicio de sesión
La aplicación debe mostrar un signo en el botón que redirige a los usuarios para el extremo de inicio de sesión que corresponde al protocolo que se utiliza para integrar con Azure AD. En la sección siguiente proporciona detalles sobre aspecto de ese botón.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Pictograma y "Iniciar sesión con Microsoft"
Es la asociación del logotipo de Microsoft y los términos de "Inicio de sesión en con Microsoft" que representa exclusivamente Azure AD entre los otros proveedores de identidades que puede admitir la aplicación. Si no tiene suficiente espacio para "Inicio de sesión en con Microsoft", es aceptar acortar a "Inicio de sesión".

![Tipos de aplicaciones y escenarios](./media/active-directory-branding-guidelines/sign-in-with-microsoft-light.png)

![Tipos de aplicaciones y escenarios](./media/active-directory-branding-guidelines/sign-in-light.png)

También puede usar una combinación de colores oscuro para los botones.

![Tipos de aplicaciones y escenarios](./media/active-directory-branding-guidelines/sign-in-with-microsoft-dark.png)

![Tipos de aplicaciones y escenarios](./media/active-directory-branding-guidelines/sign-in-dark.png)

## <a name="branding-dos-and-donts"></a>Personalización de marca tareas pendientes y prohibiciones

**Debe** utilizar "cuenta profesional o educativa" en combinación con el botón de "Inicio de sesión en con Microsoft" para proporcionar una explicación adicional para ayudar a los usuarios finales reconoce si puede usarlo. **No** use otros términos como "cuenta de la empresa", "cuenta de empresa" o "cuenta corporativa".

**No** use "Office 365 identificador" o "Azure". Office 365 es también el nombre de una oferta de Microsoft que no utilice Azure AD para la autenticación de consumidores.

**No** modifique el logotipo de Microsoft.

**No** exponer los usuarios finales a las marcas de Azure o Active Directory. Sin embargo es Aceptar para usar estos términos con los desarrolladores, los profesionales de TI y los administradores.

## <a name="navigation-dos-and-donts"></a>Normas de navegación

**Proporcione una forma a los usuarios para cerrar sesión y cambiar a otra cuenta de usuario.** Mientras la mayoría de las personas tiene una sola cuenta personal de Microsoft y Facebook o Google o Twitter, personas a menudo se asocian con más de una organización. Compatibilidad con varios usuarios firmado estará disponible próximamente.
