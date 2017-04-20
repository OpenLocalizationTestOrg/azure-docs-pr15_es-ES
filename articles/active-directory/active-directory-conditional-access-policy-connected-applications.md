<properties
    pageTitle="Establecer la directiva de acceso condicional basado en el dispositivo para aplicaciones conectadas de Azure Active Directory | Microsoft Azure"
    description="Establecer directivas de acceso condicional basado en el dispositivo para Azure AD-aplicaciones conectadas."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="markvi"/>


# <a name="set-device-based-conditional-access-policy-for-azure-active-directory-connected-applications"></a>Establecer la directiva de acceso condicional basado en el dispositivo para aplicaciones conectadas de Azure Active Directory


Basado en dispositivo condicional acceso de Azure Active Directory (AD Azure) puede ayudar a proteger los recursos de la organización de:

- Los intentos de acceso desde dispositivos desconocidos o no administrados.
- Dispositivos que no cumplan las directivas de seguridad de su organización.

Para obtener información general de acceso condicional, consulte [acceso condicional de Azure Active Directory](active-directory-conditional-access.md).

Puede establecer directivas de acceso condicional basado en el dispositivo para proteger estas aplicaciones:

- Office 365 SharePoint Online, para proteger los documentos y sitios de su organización
- Office 365 Exchange Online, para proteger el correo electrónico de su organización
- Software como las aplicaciones de servicio (SaaS) que están conectadas a Azure AD para la autenticación
- Aplicaciones que se publican mediante servicios de Proxy de aplicación de Azure AD local

Para establecer una directiva de acceso condicional basado en el dispositivo, en el portal de Azure, vaya a la aplicación específica en el directorio.


  ![Lista de aplicaciones en el directorio de portal de Azure] (./media/active-directory-conditional-access-policy-connected-applications/01.png "Aplicaciones")


Seleccione la aplicación y, a continuación, haga clic en la ficha **Configurar** para establecer la directiva de acceso condicional.  


  ![Configurar la aplicación] (./media/active-directory-conditional-access-policy-connected-applications/02.png "Dispositivo según las reglas de acceso")




**Para establecer una directiva de acceso condicional basado en el dispositivo, en la sección **dispositivo según las reglas de acceso** , en **Habilitar reglas de acceso**, seleccione.**

Una directiva de acceso condicional basado en el dispositivo tiene tres componentes:

- **Aplicar a**. El ámbito de los usuarios que se aplica la directiva.

- **Reglas de dispositivos**. Las condiciones que debe cumplir un dispositivo antes de que se puede tener acceso a la aplicación.

- **Aplicación de la aplicación**. Las aplicaciones cliente (nativo en comparación con el explorador) al que se aplica la directiva.

  ![Los tres componentes de una directiva de acceso basado en el dispositivo] (./media/active-directory-conditional-access-policy-connected-applications/03.png "Dispositivo según las reglas de acceso")


## <a name="select-the-users-the-policy-applies-to"></a>Seleccione los usuarios de a que la directiva se aplica

En la sección **Aplicar a** , puede seleccionar el ámbito de los usuarios de a que esta directiva se aplica.

Tiene dos opciones cuando se crea un ámbito de la directiva de acceso para los usuarios:

- **Todos los usuarios**. Aplicar la directiva a todos los usuarios acceso a la aplicación.

- **Grupos**. Limitar la directiva a los usuarios que es miembro de un grupo específico.

![Directiva de aplicar a todos los usuarios o a un grupo] (./media/active-directory-conditional-access-policy-connected-applications/11.png "Aplicar a")


 Para excluir un usuario de una directiva, active la casilla de verificación **excepto** . Esto es útil cuando tenga que conceder permisos a un usuario específico temporalmente acceso a la aplicación. Seleccione esta opción, por ejemplo, si algunos de los usuarios tienen dispositivos que no están listos para acceso condicional. Los dispositivos pueden ser aún no registrados o que va a estar fuera de cumplimiento.


## <a name="select-the-conditions-that-devices-must-meet"></a>Seleccione las condiciones que deben cumplir los dispositivos

Usar **Reglas de dispositivos** para definir las condiciones en un dispositivo debe cumplir para obtener acceso a la aplicación.

Puede establecer acceso condicional basado en el dispositivo para estos tipos de dispositivos:

- Actualización de aniversario de Windows 10, Windows 8.1 y Windows 7
- Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 y Windows Server 2008 R2
- dispositivos iOS (iPad, iPhone)
- Dispositivos Android

Soporte para Mac está disponible próximamente.

  ![Aplicar directivas para dispositivos] (./media/active-directory-conditional-access-policy-connected-applications/04.png "Aplicaciones")

 >[AZURE.NOTE] Para obtener información acerca de las diferencias entre dispositivos unido AD Azure y Unidos a un dominio, vea [dispositivos usa Windows 10 en su lugar de trabajo](active-directory-azureadjoin-windows10-devices.md).

Tiene dos opciones para las reglas del dispositivo:

- **Deben cumplir todos los dispositivos**. Se debe cumplir las plataformas de dispositivos que tienen acceso a la aplicación. Dispositivos que ejecutan plataformas que no son compatibles con el acceso condicional basado en el dispositivo no tienen acceso.

- **Solo los dispositivos seleccionados debe cumplir**. Plataformas de dispositivo específico solo debe cumplir. Otras plataformas o en otras plataformas que pueden tener acceso a la aplicación, tienen acceso.

  ![Establecer el ámbito de reglas de dispositivos] (./media/active-directory-conditional-access-policy-connected-applications/05.png "Aplicaciones")

Dispositivos de Azure unido AD son compatibles si están marcadas como **compatibles** en el directorio Intune o un sistema de administración de dispositivos móviles de terceros que se integra con Azure AD.

Es compatible con un dispositivo dominio une si:

- Está registrado con Azure AD. Muchas organizaciones tratan dominio dispositivos como dispositivos de confianza.
- Se marcará como **compatibles** en Azure AD por el Administrador de configuración del centro de sistema 2016.

 ![Dominio dispositivos compatibles] (./media/active-directory-conditional-access-policy-connected-applications/06.png "Reglas de dispositivos")

Dispositivos personales de Windows son compatibles si están marcadas como **compatibles** en el directorio Intune o un sistema de administración de dispositivos móviles de terceros que se integra con Azure AD.

Dispositivos de Windows no son compatibles si están marcadas como **compatibles** en el directorio Intune.

 >[AZURE.NOTE] Para obtener más información acerca de cómo configurar Azure AD de cumplimiento de dispositivo en los sistemas de gestión diferente, consulte [acceso condicional de Azure Active Directory](active-directory-conditional-access.md).


Puede seleccionar una o varias plataformas de dispositivos de una directiva de acceso basado en el dispositivo. Esto incluye Android, iOS, Windows Mobile (Windows 8.1 teléfonos y tabletas) y Windows (todos los otros dispositivos Windows, incluidos todos los dispositivos de Windows 10).
Evaluación de directivas sólo se produce en las plataformas seleccionadas. Si un dispositivo que intenta obtener acceso no está ejecutando una de las plataformas seleccionadas, el dispositivo puede acceder a la aplicación si el usuario tiene acceso. Se evalúa la directiva del dispositivo.

![Seleccione plataformas para las reglas de dispositivo] (./media/active-directory-conditional-access-policy-connected-applications/07.png "Reglas de dispositivos")


## <a name="set-policy-evaluation-for-a-type-of-application"></a>Establecer la evaluación de directivas para un tipo de aplicación

En la sección de la **Aplicación de la aplicación** , establezca el tipo de aplicaciones que se evaluará la directiva de acceso de usuario o dispositivo.

Tiene dos opciones para el tipo de aplicación para incluir:

- Explorador y aplicaciones nativas
- Solo las aplicaciones nativas

![Elija Explorador o aplicaciones nativas] (./media/active-directory-conditional-access-policy-connected-applications/08.png "Aplicaciones")

Para aplicar la directiva de acceso para las aplicaciones, seleccione **para el explorador y aplicaciones nativas**. A continuación, puede incluir:

- Exploradores (por ejemplo, Microsoft Edge en Windows 10) o Safari en iOS.
- Aplicaciones que usan la biblioteca de autenticación de Active Directory (ADAL) en cualquier plataforma, o la WebAccountManager (Administrador de aplicaciones Web) API en Windows 10.

>[AZURE.NOTE] Para obtener más información sobre la compatibilidad del explorador y otras consideraciones para un usuario que tiene acceso a un dispositivo basado en, aplicación protegida autoridad de certificado, consulte [acceso condicional de Azure Active Directory](active-directory-conditional-access.md).

## <a name="help-protect-email-access-from-exchange-activesync-based-applications"></a>Ayudar a proteger el acceso al correo electrónico desde aplicaciones basadas en Exchange ActiveSync

En las aplicaciones de Office 365 Exchange Online, puede usar Exchange ActiveSync para bloquear el acceso de correo electrónico para las aplicaciones de correo basado en Exchange ActiveSync.

![Opciones de cumplimiento de Exchange ActiveSync] (./media/active-directory-conditional-access-policy-connected-applications/09.png "Aplicaciones")

![Se requiere un dispositivo compatible con acceder al correo electrónico] (./media/active-directory-conditional-access-policy-connected-applications/10.png "Aplicaciones")


## <a name="next-steps"></a>Pasos siguientes

- [Acceso condicional de Azure Active Directory](active-directory-conditional-access.md)
