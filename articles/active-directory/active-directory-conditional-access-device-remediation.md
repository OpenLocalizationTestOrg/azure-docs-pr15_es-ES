<properties
    pageTitle="Solución de problemas de acceso de Azure Active Directory | Microsoft Azure"
    description="Obtenga información sobre los pasos que puede realizar para resolver problemas de access con los recursos en línea de su organización."
    services="active-directory"
    keywords="acceso condicional basado en dispositivos, registro de dispositivos, habilitar el registro de dispositivo, registro de dispositivo y MDM"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="markvi"/>


# <a name="troubleshooting-for-azure-active-directory-access-issues"></a>Solución de problemas de acceso de Azure Active Directory

Intenta obtener acceso a la intranet de SharePoint Online de su organización y recibe un mensaje de error "acceso denegado". ¿A qué te dedicas?

Este artículo tratan los pasos de corrección que pueden ayudarle a resolver problemas de acceso con recursos en línea de su organización.

Para que le ayude a resolver Azure Active Directory (AD Azure) tener acceso a problemas, vaya a la sección en el artículo que trata sobre la plataforma de dispositivos:

-   Dispositivo de Windows
-   dispositivo iOS (consulte atrás pronto para obtener ayuda con iPhone y iPads.)
-   Dispositivo Android (compruébelos pronto para obtener ayuda con Android teléfonos y tabletas.)

## <a name="access-from-a-windows-device"></a>Acceso desde un dispositivo de Windows

Si el dispositivo ejecuta una de las siguientes plataformas, mire en las siguientes secciones para el mensaje de error que aparece cuando intenta obtener acceso a una aplicación o servicio:

- Windows 10
- Windows 8.1
- Windows 8
- Windows 7
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

### <a name="device-is-not-registered"></a>Dispositivo no está registrado

Si el dispositivo no está registrado con Azure AD y la aplicación está protegida con una directiva de dispositivo, es posible que vea una página que muestra uno de estos mensajes de error:

!["Que no se puede acceder a ellos desde aquí" mensajes para dispositivos no registrados] (./media/active-directory-conditional-access-device-remediation/01.png "Escenario")

Si el dispositivo está unido al dominio a Active Directory de su organización, pruebe lo siguiente:

1.  Asegúrese de que inicie sesión Windows usando su cuenta de trabajo (la cuenta de Active Directory).
2.  Conectarse a su red corporativa a través de una red privada virtual (VPN) o DirectAccess.
3.  Cuando se haya conectado, presione la tecla del logotipo de Windows + la tecla L para bloquear la sesión de Windows.
4.  Escriba sus credenciales de cuenta de trabajo para desbloquear su sesión de Windows.
5.  Espere un minuto y, a continuación, inténtelo de nuevo tener acceso a la aplicación o servicio.
6.  Si ve la misma página, haga clic en el vínculo **más información** y, a continuación, póngase en contacto con el Administrador con los detalles.

Si el dispositivo no está unido al dominio y ejecuta Windows 10, tiene dos opciones:

- Ejecutar la combinación de Azure AD
- Agregar su cuenta profesional o educativa para Windows

Para obtener información acerca de cómo estas opciones son diferentes, vea [usar Windows 10 dispositivos en su lugar de trabajo](active-directory-azureadjoin-windows10-devices.md).

Para ejecutar la combinación de Azure AD, siga los siguientes pasos para la plataforma de su dispositivo se inicia. (Combinación de azure AD no está disponible en Windows Phone).

**Actualización de Windows 10 aniversario**

1.  Abra la aplicación de **configuración** .
2.  Haga clic en **cuentas** > **acceso trabajo o la escuela**.
3.  Haga clic en **Conectar**.
4.  Haga clic en **unirse a este dispositivo Azure AD**.
5.  Autenticar a su organización, le proporcionan autenticación multifactor si se le solicita y, a continuación, siga los pasos que se muestran.
6.  Cerrar sesión y, a continuación, inicie sesión con su cuenta de trabajo.
7.  Vuelva a intentar obtener acceso a la aplicación.


**Actualización de Windows 10 de noviembre de 2015**

1.  Abra la aplicación de **configuración** .
2.  Haga clic en **sistema** > **sobre**.
3.  Haga clic en **unirse a Azure AD**.
4.  Autenticar a su organización, le proporcionan autenticación multifactor si se le solicita y, a continuación, siga los pasos que se muestran.
5.  Cerrar sesión y, a continuación, inicie sesión con su cuenta de trabajo (la cuenta de Azure AD).
6.  Vuelva a intentar obtener acceso a la aplicación.

Para agregar su cuenta profesional o educativa, realice los pasos siguientes:

**Actualización de Windows 10 aniversario**

1.  Abra la aplicación de **configuración** .
2.  Haga clic en **cuentas** > **acceso trabajo o la escuela**.
3.  Haga clic en **Conectar**.
4.  Autenticar a su organización, le proporcionan autenticación multifactor si se le solicita y, a continuación, siga los pasos que se muestran.
5.  Vuelva a intentar obtener acceso a la aplicación.


**Actualización de Windows 10 de noviembre de 2015**

1.  Abra la aplicación de **configuración** .
2.  Haga clic en **cuentas** > **sus cuentas**.
3.  Haga clic en **Agregar trabajo o escuela cuenta**.
4.  Autenticar a su organización, le proporcionan autenticación multifactor si se le solicita y, a continuación, siga los pasos que se muestran.
5.  Vuelva a intentar obtener acceso a la aplicación.

Si el dispositivo no está unido al dominio y ejecuta Windows 8.1, para realizar una combinación de lugar de trabajo e inscribirse en Intune de Microsoft, siga los pasos siguientes:

1.  Abrir **configuración de PC**.
2.  Haga clic en **red** > **lugar de trabajo**.
3.  Haga clic en **unirse**.
4.  Autenticar a su organización, le proporcionan autenticación multifactor si se le solicita y, a continuación, siga los pasos que se muestran.
5.  Haga clic en **Activar**.
6.  Vuelva a intentar obtener acceso a la aplicación.


### <a name="browser-is-not-supported"></a>Explorador no es compatible

Podría denegado acceso si intenta obtener acceso a una aplicación o servicio mediante uno de los siguientes exploradores:

- Chrome, Firefox o cualquier otro explorador que no sea Microsoft Edge o Microsoft Internet Explorer en Windows 10 o Windows Server 2016
- Firefox en Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2

Verá una página de error que es similar a esta:

!["No se puede acceder a ellos desde aquí" mensaje para exploradores no compatibles] (./media/active-directory-conditional-access-device-remediation/02.png "Escenario")

La corrección sola es utilizar un explorador compatible con la aplicación de la plataforma de dispositivo.

## <a name="next-steps"></a>Pasos siguientes

[Acceso condicional de Azure Active Directory](active-directory-conditional-access.md)
