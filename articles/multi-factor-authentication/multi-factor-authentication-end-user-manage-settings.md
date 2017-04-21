<properties
    pageTitle="Administrar la configuración de comprobación de dos pasos | Microsoft Azure"
    description="Administrar cómo usar autenticación multifactor de Azure incluidos cambiando su información de contacto o configurar los dispositivos."
    services="multi-factor-authentication"
    keywords = "cliente de la autenticación, el problema de autenticación, Id. de correlación"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="manage-your-settings-for-two-step-verification"></a>Administrar la configuración de verificación en dos pasos

Este artículo responde preguntas acerca de cómo actualizar la configuración para la autenticación de verificación o multifactor. Si está teniendo problemas para iniciar sesión su cuenta, consulte [¿tiene problemas con la verificación en dos pasos](multi-factor-authentication-end-user-troubleshoot.md) para la solución de problemas.


## <a name="where-to-find-the-settings-page"></a>Dónde encontrar la página de configuración
Según cómo su empresa configurado autenticación multifactor de Azure, hay algunos lugares donde puede cambiar la configuración como el número de teléfono.

Si el Administrador de TI envía una dirección URL específica o pasos para administrar activó la verificación, siga las instrucciones. En caso contrario, deberían funcionar las siguientes instrucciones para los demás. Si siga estos pasos, pero no ve las mismas opciones, esto significa que su trabajo o escuela personalizar su propio portal. Pida a su administrador para el vínculo a su portal de Azure con autenticación multifactor.


1. Inicie sesión en [https://myapps.microsoft.com](https://myapps.microsoft.com)  
2. En la parte superior, seleccione el **perfil**.  
3. Seleccione la **comprobación de seguridad adicional**.  

    ![Mis aplicaciones](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. Carga de la página de comprobación de seguridad adicional con la configuración.

    ![Proofup](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)


## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>Deseo cambiar mi número de teléfono o agregar un número secundario

Es importante configurar un número de teléfono de autenticación secundaria.  Dado que el número de teléfono principal y la aplicación móvil son probablemente en el mismo teléfono, el número de teléfono secundario es la única manera podrá volver a su cuenta, si la pérdida o robo de su teléfono.

> [AZURE.NOTE]
> Si no tiene acceso a su número de teléfono principal y necesita ayuda su cuenta, vea nuestros temas de ayuda en [¿tiene problemas con la verificación en dos pasos](multi-factor-authentication-end-user-troubleshoot.md).

**Para cambiar el número de teléfono principal:**  

1. En la página de comprobación de seguridad adicionales, seleccione el cuadro de texto con su número de teléfono actual y editar con el nuevo número de teléfono.  
2. Seleccione **Guardar**.  
3. Si este es el número que se usa para la opción de verificación preferido, deberá comprobar el nuevo número para que pueda guardar.  


**Para agregar un número de teléfono secundario:**  

1. En la página de comprobación de seguridad adicional, active la casilla junto a **teléfono de autenticación alternativas.**  
2. Escriba su número de teléfono secundario en el cuadro de texto.  
3. Seleccione **Guardar** y los cambios han finalizado.  


## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>¿Cómo limpiar Microsoft Authenticator desde mi dispositivo antiguo y mover a una nueva
Al desinstalar la aplicación desde el dispositivo o restablecer el dispositivo, no se elimina la activación en el back-end. Debe usar los pasos descritos en [mover a un nuevo dispositivo](multi-factor-authentication-microsoft-authenticator.md#how-to-move-to-the-new-microsoft-authenticator-app).

## <a name="next-steps"></a>Pasos siguientes
- Obtener sugerencias de solución de problemas y ayuda sobre [¿tiene problemas con la verificación en dos pasos](multi-factor-authentication-end-user-troubleshoot.md)
- Configurar [las contraseñas de aplicación](multi-factor-authentication-end-user-app-passwords.md) para las aplicaciones que no son compatibles con la verificación en dos pasos.
