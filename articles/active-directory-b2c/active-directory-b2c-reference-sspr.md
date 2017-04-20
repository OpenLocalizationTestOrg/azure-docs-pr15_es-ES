<properties
    pageTitle="Azure Active B2C de directorio: Restablecimiento de contraseña de autoservicio | Microsoft Azure"
    description="Un tema que muestra cómo configurar el autoservicio restablecimiento de contraseña para los consumidores de Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="curtand"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>


# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Azure B2C directorio activo: Configurar el autoservicio restablecimiento de contraseña para los consumidores

Con la característica de restablecimiento de contraseña de autoservicio, los consumidores (que se hayan registrado para las cuentas locales) pueden restablecer las contraseñas en sus propios. Esto reduce significativamente la carga de su personal de soporte técnico, sobre todo si su aplicación tiene millones de consumidores con de forma regular. Actualmente, solo se admite mediante una dirección de correo electrónico comprobado como un método de recuperación. Métodos de recuperación adicional (número de teléfono comprobado, preguntas de seguridad, etc.), agregaremos en el futuro.

> [AZURE.NOTE]
En este artículo se aplica a autoservicio contraseña restablecer utilizado en el contexto de una directiva de inicio de sesión. Si necesita totalmente personalizable invoca desde la aplicación de directivas de restablecimiento de contraseña, consulte [este artículo](./active-directory-b2c-reference-policies.md#create-a-password-reset-policy).

De forma predeterminada, el directorio no tiene la contraseña de autoservicio restablecer activado. Siga los pasos siguientes para activarlo:

1. Inicie sesión en el [portal de clásica Azure](https://manage.windowsazure.com/) como administrador de suscripción. Este es el mismo trabajo o escuela o la misma cuenta de Microsoft que usó para crear el directorio.
2. Vaya a la extensión de Active Directory en la barra de navegación en el lado izquierdo.
3. Busque el directorio en la ficha **directorio** y haga clic en él.
4. Haga clic en la ficha **Configurar** .
5. Desplácese hacia abajo hasta la sección **directivas de restablecimiento de contraseña de usuario** y activar o desactivar la opción **Restablecer los usuarios habilitados para la contraseña** en **Sí**. Observe que la opción de **Dirección de correo electrónico alternativa** está activada; dejar como está.

    ![Restablecimiento de contraseña de autoservicio](./media/active-directory-b2c-reference-sspr/sspr.png)

6. Haga clic en **Guardar** en la parte inferior de la página. Eso es todo.

Para probar, use la característica "Ejecutar ahora" en cualquier directiva de inicio de sesión que tiene las cuentas locales como un proveedor de identidades. En el inicio de sesión de cuenta local página (donde se introduce una dirección de correo electrónico y contraseña, o un nombre de usuario y contraseña), haga clic en **no se puede acceder a su cuenta?** para comprobar la experiencia de consumidores.

> [AZURE.NOTE]
Pueden personalizar las páginas de restablecimiento de contraseña de autoservicio con la [característica de personalización de marca de empresa](../active-directory/active-directory-add-company-branding.md).
