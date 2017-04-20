<properties
    pageTitle="Azure Active Directory B2C: Preguntas más frecuentes | Microsoft Azure"
    description="Preguntas más frecuentes sobre B2C de Azure Active Directory"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/09/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-faqs"></a>Azure Active Directory B2C: preguntas más frecuentes

Esta página responde a preguntas más frecuentes sobre la B2C Azure Active Directory (AD Azure). Mantener comprobación de actualizaciones de.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>¿Puedo usar características de Azure AD B2C en mi inquilino de Azure AD existente, en función de empleado?

Actualmente Azure AD B2C características no pueden activarse en su inquilino de Azure AD existente. Le recomendamos que cree un inquilino independiente para usar características de Azure AD B2C para administrar los consumidores.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>¿Puedo usar Azure AD B2C proporcionar social inicio de sesión (Facebook y Google +) en Office 365?

Azure AD B2C no se pueden usar con Microsoft Office 365. En general, no pueden usarse para proporcionar autenticación para las aplicaciones SaaS (Office 365, Salesforce, día laborable, etcetera). Proporciona administración de identidades y acceso sólo para web de orientación de consumidor y aplicaciones móviles y no es aplicable al empleado o un socio escenarios.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>¿Cuáles son las cuentas locales en Azure AD B2C? ¿Cómo son distintas de las cuentas de trabajo o escuela en Azure AD?

En un inquilino de Azure AD, cada usuario en el inquilino (excepto los usuarios con cuentas de Microsoft existentes) inicia sesión con una dirección de correo electrónico del formulario `<xyz>@<tenant domain>`, donde `<tenant domain>` es uno de los dominios comprobados en el inquilino o la inicial `<...>.onmicrosoft.com` dominio. Este tipo de cuenta es una cuenta profesional o educativa.

En un inquilino de Azure AD B2C, la mayoría de aplicaciones desea que el usuario que inicie sesión con cualquier dirección de correo electrónico arbitrario (por ejemplo, joe@comcast.net, bob@gmail.com, sarah@contoso.com, o jim@live.com). Este tipo de cuenta es una cuenta local. En la actualidad, también se admiten nombres de usuario arbitrario (simplemente cadenas) como cuentas locales (por ejemplo, joe, bob, Amaya o jim). Puede elegir uno de estos dos tipos de cuenta locales en el servicio de Azure AD B2C.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Los proveedores de identidades social ¿admiten ahora? ¿Cuáles piensa en el futuro?

Actualmente se admiten Facebook, Google +, LinkedIn y Amazon. Agregaremos compatibilidad con otros proveedores de identidades sociales más populares según demanda de los clientes.

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>¿Configurar ámbitos para obtener más información sobre los consumidores de varios proveedores de identidades social?

No, pero esta característica está en nuestra guía. Los ámbitos de predeterminado utilizados para nuestro conjunto de proveedores de identidades social compatible son:

- Facebook: correo electrónico
- Google +: correo electrónico
- Cuenta de Microsoft: openid perfil de correo electrónico
- Amazon: perfil
- LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>¿Tiene mi aplicación se pueden ejecutar en Azure para que funcione con Azure AD B2C?

No, puede hospedar su aplicación en cualquier lugar (en la nube o local). Todo lo que necesita para interactuar con Azure AD B2C es la capacidad de enviar y recibir solicitudes HTTP en los extremos accesible públicamente.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Tengo varios inquilinos de Azure AD B2C. ¿Cómo puedo administrar ellos en el Portal de Azure?

Cada inquilino de Azure AD B2C tiene su propio módulo de características B2C en el portal de Azure. Consulte [Azure AD B2C: registrar la aplicación](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) para obtener información sobre cómo navegar a módulo de características de B2C del inquilino específico en el portal de Azure. Cambiar entre los directorios de Azure AD B2C en el portal de Azure no conserva las características de B2C placa abrir en la mayoría de los exploradores.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>¿Cómo personalizar correos electrónicos de verificación (el contenido y la "de:" campo) enviado por Azure AD B2C?

Puede usar la [característica de personalización de marca de empresa](../active-directory/active-directory-add-company-branding.md) para personalizar el contenido de los mensajes de correo electrónico de verificación. Más concretamente, se pueden personalizar estos dos elementos del correo electrónico:

- **Logotipo de pancarta**: se muestra en la parte inferior derecha.
- **Color de fondo**: se muestra en la parte superior.

    ![Captura de pantalla de un correo electrónico de verificación personalizada](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

La firma de correo electrónico contiene el nombre del inquilino B2C que proporcionan cuando crea por primera vez el inquilino B2C. Puede cambiar el nombre de estas instrucciones:

- Iniciar sesión en el [portal de clásica Azure](https://manage.windowsazure.com/) como administrador de suscripción.
- Vaya a su inquilino B2C.
- Haga clic en la ficha **Configurar** .
- Cambiar el campo **nombre** en la sección **Propiedades del directorio** .
- Haga clic en **Guardar** en la parte inferior de la página.

Actualmente no hay ninguna manera de cambiar la "de:" en el correo electrónico. Si está interesado en esta función y en personalizar completamente el cuerpo del correo electrónico de verificación, votar por la característica de [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails).

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>¿Cómo puedo migrar mi existente nombres de usuario, contraseñas y perfiles de la base de datos a Azure AD B2C?

Puede usar la API de Azure AD Graph para escribir la herramienta de migración. Vea el [ejemplo de gráfico API](active-directory-b2c-devquickstarts-graph-dotnet.md) para obtener información detallada. Le proporcionamos distintas opciones de migración y herramientas cuadro en el futuro.

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>¿Qué directiva de contraseñas se utiliza para las cuentas locales en Azure AD B2C?

La directiva de contraseñas de Azure AD B2C para las cuentas locales se basa en la directiva de Azure AD. AD B2C Azure de suscripción, suscripción o inicio de sesión y la contraseña restablecer directivas utiliza la seguridad de la contraseña "seguro" y no expiren las contraseñas. Lea la [Directiva de contraseñas de Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) para obtener más detalles.

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>¿Puedo usar Azure AD Connect para migrar las identidades de consumidor que se almacenan en mi local de Active Directory para Azure AD B2C?

No, Azure AD Connect no está diseñado para funcionar con Azure AD B2C. Le proporcionamos distintas opciones de migración y herramientas cuadro en el futuro.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>¿Azure AD B2C funciona con los sistemas como Microsoft Dynamics CRM?

En este momento no. Integración de estos sistemas es en nuestra guía.

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>¿Es Azure AD B2C con SharePoint 2016 local o con versiones anteriores?

En este momento no. Azure AD B2C no son compatibles con tokens SAML 1.1 que portales y aplicaciones de comercio electrónico integradas en la necesidad de SharePoint local. Tenga en cuenta que Azure AD B2C está destinado el SharePoint externo compartir partner escenario; vea [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) en su lugar.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>¿Debo usar Azure AD B2C o B2B para administrar identidades externas?

Lea este artículo sobre [identidades externas](../active-directory/active-directory-b2b-compare-external-identities.md) para obtener más información sobre cómo aplicar las características apropiadas para los escenarios de identidad externos.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>¿Qué características de auditoría y reporting proporcionar Azure AD B2C? ¿Son los mismos que Azure AD Premium?

No, Azure AD B2C no admite el mismo conjunto de informes que Azure AD Premium. Se lanzará B2C de Azure AD informes básicos y las API de auditoría pronto.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>¿Localizar la interfaz de usuario de páginas servidas por Azure AD B2C? ¿Qué idiomas son compatibles?

Actualmente, Azure AD B2C está optimizado para inglés solo. Tenemos previsto para dar a conocer las características de localización tan pronto como sea posible.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>¿Puedo usar mi propia URL de las páginas de suscripción e inicio de sesión que sirven Azure AD B2C? ¿Por ejemplo, ¿puedo cambiar la dirección URL de://Login.microsoftonline.com) a login.contoso.com?

En este momento no. Esta característica está en nuestra guía. Tenga en cuenta que verificar su dominio en la pestaña **dominios** de su inquilino en el portal de clásico Azure no va a hacer esto.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>¿Cómo elimino mi inquilino de Azure AD B2C?

Siga estos pasos para eliminar al inquilino de Azure AD B2C:

- Siga estos pasos para [navegar hasta el módulo de características B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) en el portal de Azure.
- Vaya a los módulos de **aplicaciones**, **proveedores de identidades** y **todas las directivas** y elimine todas las entradas en cada uno de ellos.
- Ahora inicie sesión en el [portal de clásica Azure](https://manage.windowsazure.com/) como administrador de suscripción. (Este es el mismo trabajo o escuela o la misma cuenta de Microsoft que usó para iniciar sesión en Azure).
- Vaya a la extensión de Active Directory de la izquierda y haga clic en su inquilino B2C.
- Haga clic en la ficha **usuarios** .
- Seleccione cada usuario en Active (excluir el usuario que se ha iniciado sesión en como, por ejemplo, el Administrador de suscripciones). Haga clic en **Eliminar** en la parte inferior de la página y haga clic en **Sí** cuando se le solicite.
- Haga clic en la ficha **aplicaciones** .
- Seleccione **aplicaciones es propietario de mi compañía** en el campo de lista desplegable **Mostrar** y haga clic en la marca de verificación.
- Verá una aplicación de **aplicación de extensiones b2c** enumeran a continuación. Haga clic en **Eliminar** en la parte inferior de la página y haga clic en **Sí** cuando se le solicite.
- Desplazarse a la extensión de Active Directory de nuevo y seleccione al inquilino B2C.
- Haga clic en **Eliminar** en la parte inferior de la página. Siga las instrucciones en pantalla para completar el proceso.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>¿Puedo obtener Azure AD B2C como parte de la serie de movilidad para empresas?

No, Azure AD B2C es un pago por uso servicio Azure y no forma parte de la serie de movilidad para empresas.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>¿Cómo informo de problemas con Azure AD B2C?

Ver [archivo admite las solicitudes de Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="more-information"></a>Más información

Que también desea revisar actual [restricciones, restricciones y limitaciones de servicio](active-directory-b2c-limitations.md).
