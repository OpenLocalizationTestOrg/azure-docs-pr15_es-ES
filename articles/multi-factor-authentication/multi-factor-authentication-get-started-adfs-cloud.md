<properties
    pageTitle="Proteger recursos de nube con Azure AMF y AD FS"
    description="Esta es la página de autenticación multifactor de Azure que describe cómo empezar a trabajar con Azure AMF y AD FS en la nube."
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
    ms.date="10/14/2016"
    ms.author="kgremban"/>

# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>Proteger recursos de nube con autenticación multifactor de Azure y AD FS

Si su organización se federa con Azure Active Directory, use autenticación multifactor de Azure o los servicios de federación de Active Directory para proteger los recursos que tienen acceso Azure AD. Use los siguientes procedimientos para proteger recursos de Azure Active Directory con autenticación multifactor de Azure o los servicios de federación de Active Directory.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Proteger recursos de Azure AD con AD FS

Para proteger los recursos de la nube, habilitar una cuenta de usuarios y configurar una regla de reclamaciones. Siga este procedimiento para recorrer los pasos:

1. Siga los pasos descritos en [al encendido con autenticación multifactor para los usuarios](active-directory/multi-factor-authentication-get-started-cloud.md#turn-on-multi-factor-authentication-for-users) para habilitar una cuenta.
2. Inicie la consola de administración de AD FS.
![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)
3. Vaya a **Confiar confía a fabricantes** y haga clic en confiar fiesta de confianza. Seleccione **Editar reglas de notificación...**
4. Haga clic en **Agregar regla...**
5. En la lista desplegable, seleccione **Enviar notificaciones usando una regla personalizada** y haga clic en **siguiente**.
6. Escriba un nombre para la regla de notificación.
7. En la regla personalizada: agregar el siguiente texto:

    ```
    => issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");
    ```

    Reclamar correspondiente:

    ```
    <saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
    <saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
    </saml:Attribute>
    ```

8. Haga clic en **Aceptar** y **Finalizar**. Cierre la consola de administración de AD FS.

A continuación, los usuarios pueden completar iniciar sesión con el método local (por ejemplo, tarjeta inteligente).

## <a name="trusted-ips-for-federated-users"></a>Direcciones IP de confianza para los usuarios federados
Direcciones IP de confianza permiten a los administradores activó la verificación derivación para direcciones IP específicas, o para los usuarios federados que tienen solicitudes que provienen de dentro de su propia intranet. Las secciones siguientes describen cómo configurar direcciones de IP de confianza en autenticación multifactor de Azure con los usuarios federados y activó la verificación derivación cuando una solicitud se origina desde dentro de la intranet de los usuarios federados. Esto se logra mediante la configuración de AD FS para usar un paso o filtrar una plantilla de Reclamación entrantes con el tipo de notificación dentro de la red corporativa.

Este ejemplo usa Office 365 para nuestra fiesta confía en confiar.

### <a name="configure-the-ad-fs-claims-rules"></a>Configurar las reglas de notificaciones de AD FS

Lo primero que necesitamos hacer es configurar las notificaciones de AD FS. Se crea reglas de dos reclamaciones, uno para el tipo de notificación dentro de la red corporativa y una adicional para mantener nuestros usuarios iniciadas.

1. Abra Administración de AD FS.
2. En la parte izquierda, seleccione **Confiar confía de terceros**.
3. Haga clic en la **Plataforma de identidad de Microsoft Office 365** y seleccione **Editar reglas de notificación...** 
 ![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. En las reglas de transformación de emisión, haga clic en **Agregar regla.** 
 ![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. En el agregar transformar reclamación Asistente para reglas, seleccione **paso a través o filtrar una notificación entrante** de la lista desplegable y haga clic en **siguiente**.
![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. En el cuadro situado junto al nombre de la regla de notificación, asigne a la regla un nombre. Por ejemplo: InsideCorpNet.
7. En la lista desplegable, situada junto a correo entrante, tipo de notificación, seleccione **Dentro de la red corporativa**.
![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. Haga clic en **Finalizar**.
9. En las reglas de transformación de emisión, haga clic en **Agregar regla**.
10. En el agregar transformar reclamación Asistente para reglas, seleccione **notificaciones de envío usando una regla personalizada** en la lista desplegable y haga clic en **siguiente**.
11. En el cuadro en nombre de la regla de notificación: escriba *Mantener usuarios ha iniciado sesión en*.
12. En el cuadro de regla personalizada, escriba:

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. Haga clic en **Finalizar**.
14. Haga clic en **Aplicar**.
15. Haga clic en **Aceptar**.
16. Cierre Administración de AD FS.



### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>Configurar multifactor Azure autenticación direcciones IP con usuarios federados de confianza
Ahora que las notificaciones en su lugar, podemos configurar direcciones IP de confianza.

1. Inicie sesión en el [portal de clásico de Azure](https://manage.windowsazure.com).
2. En la parte izquierda, haga clic en **Active Directory**.
3. En el directorio, seleccione el directorio donde desea configurar direcciones IP de confianza.
4. En el directorio que haya seleccionado, haga clic en **Configurar**.
5. En la sección autenticación multifactor, haga clic en **Administrar la configuración de servicio**.
6. En la página Configuración del servicio, en estas direcciones IP, seleccione **Omitir múltiples la autenticación multifactor para las solicitudes de usuarios federados en la intranet.** 
 ![Nube](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. Haga clic en **Guardar**.
8. Una vez que se han aplicado las actualizaciones, haga clic en **Cerrar**.


¡Eso es todo! En este momento, los usuarios de Office 365 federados solo deben tener usar AMF cuando procede de una notificación de fuera de la intranet corporativa.
