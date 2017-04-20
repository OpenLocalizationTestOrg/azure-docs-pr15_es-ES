<properties
    pageTitle="Azure Active Directory B2C: Soporte técnico | Microsoft Azure"
    description="Cómo solicitudes de Azure Active Directory B2C soporte de archivo"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="msmbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-file-support-requests"></a>B2C de Azure Active Directory: Las solicitudes de soporte de archivo

Puede archivar las solicitudes de soporte para B2C de Azure Active Directory (AD Azure) en el portal de Azure con los pasos siguientes:

1. [Siga estos pasos para navegar hasta el módulo de características B2C en el portal de Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cambiar desde su inquilino B2C a otro inquilino que tiene una suscripción de Azure asociada. Normalmente, éste es el inquilino de empleados o el inquilino de forma predeterminada creada automáticamente cuando se ha suscrito a una suscripción de Azure. Para obtener más información, consulte [cómo Azure AD relacionada con una suscripción de Azure](active-directory-how-subscriptions-associated-directory.md#how-an-azure-subscription-is-related-to-azure-ad).

    ![Compatibilidad: cambiar inquilinos](./media/active-directory-b2c-support/support-switch-dir.png)

3. Después de cambiar los inquilinos, haga clic en **ayuda + soporte técnico**.

    ![Soporte técnico - ayuda + soporte técnico](./media/active-directory-b2c-support/support-support.png)

4. Haga clic en **nueva solicitud de soporte técnico**.

    ![Compatibilidad: nuevo](./media/active-directory-b2c-support/support-new.png)

5. En el módulo de **conceptos básicos** , use estos detalles y haga clic en **siguiente**.

    - **Tipo de problema** es **técnico**.
    - Elija la **suscripción**de correspondiente.
    - **Servicio** es **Active Directory**.
    - Elegir el **plan de soporte técnico**de correspondiente. Si no tiene una, puede registrarse para una [aquí](https://azure.microsoft.com/en-us/support/plans/).

    ![Compatibilidad: conceptos básicos](./media/active-directory-b2c-support/support-basics.png)

6. En el módulo de **problema** , use estos detalles y haga clic en **siguiente**.

    - Elija el nivel de **gravedad** adecuado.
    - **Tipo de problema** es **B2C**.
    - Elija la **categoría**de correspondiente.
    - Describa el problema en el campo **Detalles** . Proporcionar detalles como el nombre del inquilino B2C, la descripción del problema, mensajes de error, correlación identificadores (si está disponible) y así sucesivamente.
    - En el campo **período de tiempo** , indique la fecha y hora (zona horaria incluido) que se produjo el problema.
    - En la **carga de archivos**, cargar todos los archivos que cree que podría ayudar a resolver el problema y capturas de pantalla.

    ![Compatibilidad: problema](./media/active-directory-b2c-support/support-problem.png)

7. En el módulo de **información de contacto** , agregue la información de contacto. Haga clic en **crear**.

    ![Compatibilidad: contacto](./media/active-directory-b2c-support/support-contact.png)

8. Después de enviar su solicitud de soporte técnico, puede supervisar haciendo clic en **ayuda + soporte** Startboard y, a continuación, **solicitudes de soporte técnico de administrar**.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Problema conocido: archivar una solicitud de soporte en el contexto de un inquilino B2C

Si perdido paso 2 descrito anteriormente y pruebe a crear una solicitud de soporte en el contexto de su inquilino B2C, verá el siguiente error.

> [AZURE.IMPORTANT]
> No intentar iniciar sesión en una nueva suscripción de Azure en el inquilino B2C.  

![Compatibilidad: ninguna suscripción](./media/active-directory-b2c-support/support-no-sub.png)
