<properties 
    pageTitle="La autenticación multifactor Azure: cómo funciona"
    description="Azure autenticación multifactor ayuda a proteger el acceso a datos y aplicaciones cumpliendo demanda de usuario de un proceso de inicio de sesión simple. Proporciona seguridad adicional si solicitan una segunda forma de autenticación y ofrece autenticación segura a través de un rango de opciones de comprobación fácil."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

#<a name="how-azure-multi-factor-authentication-works"></a>Cómo funciona la autenticación multifactor de Azure

La seguridad de la autenticación multifactor radica en su enfoque de capas. Poner en peligro varios factores de autenticación representa un reto significativo para los atacantes. Incluso si un intruso administra obtener la contraseña del usuario, es inútil sin tener también disponga de dispositivo de confianza. Caso de que el usuario pierda el dispositivo, la persona que busca, no podrá usarlo a menos que también sepa la contraseña del usuario.

![Proofup](./media/multi-factor-authentication-how-it-works/howitworks.png)



Azure autenticación multifactor ayuda a proteger el acceso a datos y aplicaciones cumpliendo demanda de usuario de un proceso de inicio de sesión simple.  Proporciona seguridad adicional si solicitan una segunda forma de autenticación y ofrece autenticación segura a través de un rango de opciones de comprobación fácil:

- llamada de teléfono
- mensaje de texto
- notificación de la aplicación móvil: permitir a los usuarios elegir el método prefieren
- código de comprobación de la aplicación móvil
- 3er tokens JURAMENTO de fiesta

Para obtener información adicional AH cómo funciona vea el siguiente vídeo.

>[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

##<a name="methods-available-for-multi-factor-authentication"></a>Métodos disponibles para la autenticación multifactor
Cuando un usuario inicia sesión, se envía una comprobación adicional al usuario.  Los siguientes son una lista de métodos que puede usar para la comprobación de segunda.

Método de verificación  | Descripción
------------- | ------------- |
Llamada de teléfono | Se coloca una llamada al teléfono inteligente de un usuario pedirle que compruebe que están firmando presionando el signo #.  Esto complete el proceso de verificación.  Esta opción es configurable y se puede cambiar a un código que especifique.
Mensaje de texto | Un mensaje de texto se enviarán a Smartphone de un usuario con un código de 6 dígitos.  Escriba este código en para completar el proceso de verificación.
Notificación de la aplicación móvil | Se enviará una solicitud de comprobación para pedirle completa la verificación seleccionando verificar desde la aplicación móvil de teléfono inteligente de un usuario. Esto ocurre si seleccionó la notificación de aplicación como su método de verificación principal.  Si reciben cuando no están firmando, puede informar como fraude.
Código de comprobación con la aplicación móvil | Un código de comprobación se enviarán a la aplicación móvil que se ejecuta en Smartphone de un usuario.  Esto ocurre si ha seleccionado un código de verificación como su método de verificación principal.


##<a name="available-versions-of-azure-multi-factor-authentication"></a>Versiones disponibles de autenticación multifactor de Azure
Azure autenticación multifactor está disponible en tres versiones diferentes.  La tabla siguiente describe cada uno de ellos con más detalle.

Versión  | Descripción
------------- | ------------- |
Autenticación multifactor para Office 365 | Esta versión funciona exclusivamente con aplicaciones de Office 365 y se administra desde el portal de Office 365. Para que los administradores ahora pueden ayudar a proteger sus recursos de Office 365 mediante la autenticación multifactor.  Esta versión incluye una suscripción de Office 365.
Autenticación multifactor para administradores de Azure | El mismo subconjunto de capacidades de autenticación multifactor para Office 365 estará disponible sin cargo para todos los administradores de Azure. Cada cuenta administrativa de una suscripción de Azure ahora puede obtener protección adicional habilitando esta funcionalidad de autenticación multifactor principal. Para que un administrador que desea tener acceso a Azure portal para crear una máquina virtual, un sitio web, administrar almacenamiento de información, servicios móviles o cualquier otro servicio de Azure puede agregar autenticación multifactor a su cuenta de administrador.
Azure autenticación multifactor | La autenticación multifactor Azure ofrece el mayor conjunto de capacidades. <br><br>Proporciona opciones de configuración adicionales a través del portal de administración de Azure, avanzada informes y soporte técnico para un rango de local y aplicaciones en la nube. Azure autenticación multifactor se puede adquirir como una licencia independiente y se incluye dentro de Azure Active Directory Premium y serie de movilidad para empresas. <br><br>También se pueden adquirir en una base de consumo mediante la creación de un proveedor de autenticación multifactor de Azure en una suscripción de Azure.
##<a name="feature-comparison-of-versions"></a>Comparación de características de versiones
La siguiente tabla siguiente proporciona una lista de las características que están disponibles en las distintas versiones de Azure con autenticación multifactor.


Característica  | Autenticación multifactor para Office 365 (incluido en Office 365 SKU)|Autenticación multifactor para administradores de Azure (incluido con la suscripción de Azure) | Azure con autenticación multifactor (incluido en Azure AD Premium y serie de movilidad para empresas)
------------- | :-------------: |:-------------: |:-------------: |
Los administradores pueden proteger cuentas con AMF| * | * (Disponible únicamente para las cuentas de administrador de Azure)|*
Aplicación móvil como un segundo factor|* | * | *
Llamada de teléfono como un segundo factor|* | * | *
SMS como un segundo factor|* | * | *
Contraseñas de aplicaciones para los clientes que no son compatibles con MFA|* | * | *
Control de administrador sobre métodos de autenticación| *| *| *
Modo PIN| | | *
Alerta de fraude| | | *
Informes de AMF| | | *
Omitir una sola vez| | | *
Saludos personalizados para las llamadas de teléfono| | | *
Personalización de identificador de llamada para llamadas de teléfono| | | *
Confirmación de evento| | | *
Direcciones IP de confianza| | | *
Suspender AMF para dispositivos de recordar (Public Preview)| | | *
AMF SDK| | | *
AMF para aplicaciones locales con AMF server| | | *


##<a name="how-to-get-azure-multi-factor-authentication"></a>Cómo obtener la autenticación multifactor de Azure

Si desea que la funcionalidad completa ofrecida autenticación multifactor de Azure en lugar de simplemente previstos para usuarios de Office 365 y Azure administradores, hay varias opciones para conseguirlo:

1.  Comprar licencias de autenticación multifactor de Azure y asignarlos a los usuarios.
2.  Comprar licencias que tienen la autenticación multifactor de Azure incluido en ellas como Azure Active Directory Premium o serie de movilidad para empresas y asignarlos a los usuarios.
3.  Crear un proveedor de autenticación multifactor de Azure dentro de una suscripción de Azure. Si todavía no tiene una suscripción de Azure, puede registrarse para una suscripción de prueba de Azure. Suscripciones de evaluación tendrán que se convierten en suscripciones normales antes de caducidad de la prueba.

Cuando se usa un proveedor de servicios de autenticación de varios factores Azure, hay dos modelos disponibles de uso que se ha facturado a través de su suscripción de Azure:


- **Por usuario**. Generalmente para empresas que desee habilitar la autenticación multifactor para un número fijo de empleados que necesitan con regularidad la autenticación.
- **Por autenticación**. Generalmente para empresas que desee habilitar la autenticación multifactor para un grupo grande de usuarios externos que necesitan con poca frecuencia autenticación.

Para obtener detalles de precios vea [precios de Azure AMF.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Elija la por persona o el modelo basado en consumo que funciona mejor para su organización.   A continuación, para obtener consulte Introducción [Introducción](multi-factor-authentication-get-started.md)
