<properties
    pageTitle="La autenticación multifactor Azure: ¿qué es la siguiente"
    description="Esta es la página de autenticación multifactor de Azure que describa lo siguiente con MFA.  Esto incluye informes, alerta de fraude, omisión de una sola vez, mensajes de voz personalizado, almacenamiento en caché, las contraseñas de direcciones IP y aplicación de confianza."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="kgremban"/>

# <a name="configuring-azure-multi-factor-authentication"></a>Configurar la autenticación multifactor Azure

En este artículo le ayuda a administrar la autenticación multifactor de Azure ahora que ya está listo y en funcionamiento.  Cubre una gran variedad de temas que le ayudarán a sacar el máximo partido de Azure con autenticación multifactor.  No todas estas características están disponibles en todas las versiones de Azure con autenticación multifactor.

La configuración de algunas de las siguientes funciones se encuentra en el Portal de administración de autenticación de varios factores de Azure. Hay dos maneras diferentes puede acceder al portal de administración de AMF, que se realicen a través del portal de Azure. La primera es mediante la administración de un proveedor de autenticación multifactor si usa AMF basado en consumo. La segunda es a través de la configuración del servicio MFA. La segunda opción requiere un proveedor de autenticación multifactor o una licencia de Azure AMF, Azure AD Premium o serie de movilidad para empresas.

Para acceder al Portal de administración de AMF a través de un proveedor de autenticación multifactor de Azure, inicie sesión en el portal de Azure como administrador y seleccione la opción de Active Directory. Haga clic en la ficha de **Proveedores de autenticación multifactor** , a continuación, seleccione el directorio y haga clic en el botón **Administrar** en la parte inferior.

Para acceder al Portal de administración de AMF a través de la página Configuración del servicio MFA, inicie sesión en el portal de Azure como administrador y seleccione la opción de Active Directory. Haga clic en el directorio y, a continuación, haga clic en la ficha **Configurar** . En la sección autenticación multifactor, seleccione **Administrar la configuración de servicio**. En la parte inferior de la página Configuración del servicio MFA, haga clic en el vínculo **vaya al portal** .


Característica| Descripción| ¿Qué está cubierto
:------------- | :------------- | :------------- |
[Alerta de fraude](#fraud-alert)|Alerta de fraude puede ser configurado y configurar para que los usuarios pueden informar fraudulentos intentos de acceso a sus recursos.|Cómo configurar, configurar y fraude de informe
[Omitir una sola vez](#one-time-bypass) |Una sola vez omisión permite a un usuario autenticarse una sola vez "omitiendo" autenticación multifactor.|Cómo instalar y configurar una sola vez omisión
[Mensajes de voz personalizado](#custom-voice-messages) |Mensajes de voz personalizadas le permiten usar su propio grabaciones o saludos con autenticación multifactor. |Cómo instalar y configurar mensajes y saludos personalizados
[Almacenamiento en caché](#caching-in-azure-multi-factor-authentication)|Almacenamiento en caché permite establecer un tiempo determinado período para que los intentos subsiguientes de autenticación correctamente automáticamente. |Cómo instalar y configurar la autenticación, almacenamiento en caché.
[Direcciones IP de confianza](#trusted-ips)|Direcciones IP es una característica de autenticación multifactor que permite a los administradores de un inquilino administrado o federado omitir la autenticación multifactor para los usuarios que está iniciando sesión de intranet local de la empresa de confianza.|Configurar y configurar las direcciones IP que están exentas de la autenticación multifactor
[Contraseñas de aplicaciones](#app-passwords)|Contraseña de aplicación permite a una aplicación que no es compatible con AMF omitir la autenticación multifactor y continuar trabajando.|Información sobre las contraseñas de la aplicación.
[Recuerde que la autenticación multifactor de recordar dispositivos y exploradores](#remember-multi-factor-authentication-for-devices-users-trust)|Le permite recordar dispositivos para un determinado número de días después de que un usuario ha iniciado sesión correctamente en el uso de AMF.|Información sobre cómo habilitar esta característica y configurar el número de días.
[Métodos de verificación seleccionables](#selectable-verification-methods)|Le permite elegir los métodos de autenticación que están disponibles para los usuarios a usar.|Información sobre la activación o desactivación de métodos de autenticación específicos, como mensajes de texto o de la llamada.



## <a name="fraud-alert"></a>Alerta de fraude
Alerta de fraude puede ser configurado y configurar para que los usuarios pueden informar fraudulentos intentos de acceso a sus recursos.  Los usuarios puedan informar fraude con la aplicación móvil o a través de su teléfono.

### <a name="to-set-up-and-configure-fraud-alert"></a>Para instalar y configurar la alerta de fraude

1.  Inicie sesión en http://azure.microsoft.com
2.  Desplácese hasta el Portal de administración de AMF según las instrucciones en la parte superior de esta página.
3.  Portal de administración de autenticación de varios factores de Azure, haga clic en configuración en la sección Configurar.
4.  En la sección de alerta de fraude de la página Configuración, active la permitir a los usuarios para enviar la casilla de verificación de las alertas de fraude.
5.  Si desea que los usuarios que se bloquee cuando se notifica fraude, marque en evitar que los usuarios cuando se notifica fraude.
6.  En el cuadro de texto **Código a informe de fraude durante inicial de saludo** , introduzca un código de número que se pueden usar durante la comprobación de la llamada. Si un usuario escribe este código más # en lugar de solo el signo #, se notificará una alerta de fraude.
7.  En la parte inferior, haga clic en Guardar.

>[AZURE.NOTE]
>Saludos de voz de Microsoft predeterminada indique a los usuarios que presione # 0 para enviar una alerta de fraude. Si desea utilizar un código distinto de 0, debe grabar y cargar sus propios saludos voz personalizado con instrucciones apropiadas.


![Nube](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="to-report-fraud-alert"></a>Alerta de fraude de informe
Alerta de fraude se puede registrar de dos maneras.  Ya sea a través de la aplicación móvil o el teléfono.  

### <a name="to-report-fraud-alert-with-the-mobile-app"></a>Alerta de fraude de informe con la aplicación móvil



1. Cuando se envía una comprobación a su teléfono, seleccione que se inicie la aplicación de Microsoft Authenticator.
2. Fraude de informe, haga clic en la Cancelar y fraude de informe. Esto incorpora un cuadro que dice que recibirán una notificación personal de soporte técnico de su organización.
3. Haga clic en fraude de informe.
4. En la aplicación, haga clic en Cerrar.

![Nube](./media/multi-factor-authentication-whats-next/report1.png)


![Nube](./media/multi-factor-authentication-whats-next/fraud2.png)

### <a name="to-report-fraud-alert-with-the-phone"></a>Alerta de fraude de informe con el teléfono

1. Cuando recibe una llamada de verificación a su teléfono, responda a ella.  
2. Para denunciar fraude, introduzca el código que se ha configurado para que se corresponden con los informes de fraude mediante el teléfono y, a continuación, en el signo #. Se le notificará que se ha enviado una alerta de fraude.
3. Finalizar la llamada.

### <a name="to-view-the-fraud-report"></a>Para ver el informe de fraude

1. Inicie sesión en [http://azure.microsoft.com](https://azure.microsoft.com/)
2. En la parte izquierda, seleccione Active Directory.
3. En la parte superior, seleccione los proveedores de autenticación multifactor. Aparece una lista de los proveedores de autenticación multifactor.
4. Si tiene más de un proveedor de autenticación multifactor, seleccione el que desea ver el informe de alerta de fraude y haga clic en administrar en la parte inferior de la página. Si sólo tiene uno, haga clic en administrar. Se abrirá el Portal de administración de autenticación de varios factores de Azure.
5. En el Portal de administración de la autenticación multifactor de Azure, a la izquierda, en ver un informe, haga clic en la alerta de fraude.
6. Especifique el intervalo de fechas que desea ver en el informe. También puede especificar los nombres de usuario, números de teléfono y el estado del usuario.
7. Haga clic en ejecutar. Esto incorpora un informe similar a la siguiente. También puede hacer clic en Exportar a CSV si desea exportar el informe.

## <a name="one-time-bypass"></a>Omitir una sola vez

Una sola vez omisión permite a un usuario autenticarse una sola vez "omitiendo" autenticación multifactor. La derivación es temporal y caduca después del número especificado de segundos.  Por lo que en situaciones donde la aplicación móvil o el teléfono no recibe una notificación o una llamada de teléfono, puede habilitar la omisión de una sola vez para que el usuario puede tener acceso al recurso deseado.

### <a name="to-create-a-one-time-bypass"></a>Para crear una sola vez omisión

1.  Inicie sesión en http://azure.microsoft.com
2.  Desplácese hasta el Portal de administración de AMF según las instrucciones en la parte superior de esta página.
3.  En el Portal de administración de autenticación de varios factores de Azure, si ve el nombre del inquilino o de Azure AMF proveedor a la izquierda con un + junto a él, haga clic en el + ver diferentes grupos de replicación de servidor AMF y el grupo predeterminado de Azure. Haga clic en el grupo adecuado.
4.  En administración de usuario, haga clic en **Omitir temporal**.
![Nube](./media/multi-factor-authentication-whats-next/create1.png)
5.  En la página de derivación temporal, haga clic en **Nuevo omisión temporal**.
6.  Escriba el nombre de usuario, el número de segundos que se existe la omisión, el motivo de la omisión de y haga clic en **Omitir**.
![Nube](./media/multi-factor-authentication-whats-next/create2.png)
7.  En este momento, el usuario debe iniciar sesión antes de que caduque la omisión de una sola vez.



### <a name="to-view-the-one-time-bypass-report"></a>Para ver el informe de derivación de una sola vez

1. Inicie sesión en [http://azure.microsoft.com](https://azure.microsoft.com/)
2. En la parte izquierda, seleccione Active Directory.
3. En la parte superior, seleccione los proveedores de autenticación multifactor. Aparece una lista de los proveedores de autenticación multifactor.
4. Si tiene más de un proveedor de autenticación multifactor, seleccione el que desea ver el informe de alerta de fraude y haga clic en administrar en la parte inferior de la página. Si sólo tiene uno, haga clic en administrar. Se abrirá el Portal de administración de autenticación de varios factores de Azure.
5. En el Portal de administración de la autenticación multifactor de Azure, a la izquierda, en ver un informe, haga clic en Omitir temporal.
6. Especifique el intervalo de fechas que desea ver en el informe. También puede especificar los nombres de usuario, números de teléfono y el estado del usuario.
7. Haga clic en ejecutar. Esto incorpora un informe similar a la siguiente. También puede hacer clic en Exportar a CSV si desea exportar el informe.

<center>![Nube](./media/multi-factor-authentication-whats-next/report.png)</center>

## <a name="custom-voice-messages"></a>Mensajes de voz personalizado

Mensajes de voz personalizadas le permiten usar su propio grabaciones o saludos con autenticación multifactor.  Pueden utilizarse además o reemplazar Microsoft registros.

Antes de comenzar tenga en cuenta lo siguiente:

- Los formatos de archivo admitidos actual son .wav y. mp3.
- El límite de tamaño de archivo es 5 MB.
- Se recomienda que, para los mensajes de autenticación que sea no más de 20 segundos. Nada mayor que esto podría provocar la comprobación de errores porque el usuario no puede responder antes de que finalice el mensaje y el tiempo de espera de la comprobación.



### <a name="to-set-up-custom-voice-messages-in-azure-multi-factor-authentication"></a>Para configurar los mensajes de voz personalizado en la autenticación multifactor de Azure
1.  Crear un mensaje de voz personalizado mediante uno de los formatos de archivo compatibles.
2.  Inicie sesión en http://azure.microsoft.com
3.  Desplácese hasta el Portal de administración de AMF según las instrucciones en la parte superior de esta página.
4.  En el Portal de administración de autenticación de varios factores de Azure, haga clic en mensajes de voz en la sección Configurar.
5.  En la sección de mensajes de voz, haga clic en **Nuevo mensaje de voz**.
![Nube](./media/multi-factor-authentication-whats-next/custom1.png)
6.  En la opción configurar: mensajes de voz nueva página, haga clic en **Administrar archivos de sonido**.
![Nube](./media/multi-factor-authentication-whats-next/custom2.png)
7.  En la opción configurar: página de archivos de sonido, haga clic en **Cargar archivo de sonido**.
![Nube](./media/multi-factor-authentication-whats-next/custom3.png)
8.  En la opción configurar: cargar el archivo de sonido, haga clic en **Examinar** y navegue hasta el mensaje de voz, haga clic en **Abrir**.
![Nube](./media/multi-factor-authentication-whats-next/custom4.png)
9.  Agregar una descripción y haga clic en cargar.
10. Una vez se termine, un mensaje confirma que ha cargado correctamente el archivo.
11. En la parte izquierda, haga clic en mensajes de voz.
12. En la sección de mensajes de voz, haga clic en nuevo mensaje de voz.
13. En la lista desplegable idioma, seleccione un idioma.
14. Si este mensaje es para una aplicación específica, especifique en el cuadro de la aplicación.
15. En el tipo de mensaje, seleccione el tipo de mensaje a reemplazarse con nuestro nuevo mensaje personalizado.
16. En la lista desplegable archivo de sonido, seleccione el archivo de sonido.
17. Haga clic en **crear**. Un mensaje confirma que se ha creado correctamente un mensaje de voz.
![Nube](./media/multi-factor-authentication-whats-next/custom5.png)</center>



## <a name="caching-in-azure-multi-factor-authentication"></a>Almacenamiento en caché en Azure autenticación multifactor

Almacenamiento en caché permite establecer un tiempo determinado período para que los intentos subsiguientes de autenticación correctamente automáticamente. Se utiliza principalmente cuando sistemas locales como VPN envían varias solicitudes de verificación mientras la primera solicitud todavía está en curso. Esto permite que las solicitudes subsiguientes correctamente automáticamente cuando el usuario se complete correctamente la comprobación en curso. Tenga en cuenta que el almacenamiento en caché no está pensado para usarse con inicios de sesión a Azure AD.


### <a name="to-set-up-caching-in-azure-multi-factor-authentication"></a>Configurar el almacenamiento en caché en autenticación multifactor de Azure

1.  Inicie sesión en http://azure.microsoft.com
2.  Desplácese hasta el Portal de administración de AMF según las instrucciones en la parte superior de esta página.
3.  En el Portal de administración de autenticación de varios factores de Azure, haga clic en caché en la sección Configurar.
4.  En la opción configurar almacenamiento en caché de la página, haga clic en nueva caché
5.  Seleccione el tipo de caché y los segundos de la memoria caché. Haga clic en crear.

<center>![Nube](./media/multi-factor-authentication-whats-next/cache.png)</center>

## <a name="trusted-ips"></a>Direcciones IP de confianza

Direcciones IP es una característica de autenticación multifactor que permite a los administradores de un inquilino administrado o federado omitir la autenticación multifactor para los usuarios que está iniciando sesión de intranet local de la empresa de confianza. Las características están disponibles para los inquilinos de Azure AD que tengan licencias de Azure AD Premium, Enterprise movilidad Suite o autenticación multifactor de Azure.


Tipo de inquilinos de Azure AD| Opciones de IP de confianza disponibles
:------------- | :------------- |
Administra|Intervalos de direcciones IP específicos: los administradores pueden especificar un intervalo de direcciones IP que puede omitir la autenticación multifactor para los usuarios que están iniciar sesión desde la intranet de la organización.
Federado|<li>Todos los usuarios de federado - todos los usuarios federados que iniciar sesión en desde dentro de la organización evitarán autenticación multifactor mediante una notificación de AD FS.</li><li>Intervalos de direcciones IP específicos: los administradores pueden especificar un intervalo de direcciones IP que puede omitir la autenticación multifactor para los usuarios que están iniciar sesión desde la intranet de la organización.

Omitir la solo funciona desde dentro de la intranet de la compañía. Por ejemplo, si seleccionó solo todos los usuarios federados y un usuario inicia sesión desde fuera de la intranet de la empresa, ese usuario tiene que autenticar con autenticación multifactor, incluso si el usuario presenta una notificación de AD FS. La siguiente tabla describe cuando las contraseñas de autenticación y la aplicación de varios factores son necesarias dentro de su red corporativa y fuera de su red corporativa cuando IP fiables está habilitado.


|IP habilitado fiables| IP deshabilitado fiables
:------------- | :------------- | :------------- |
Red corporativa interno|Para los flujos de explorador, no requiere autenticación multifactor.|Para los flujos de explorador requiere autenticación multifactor
|Para las aplicaciones de cliente enriquecido, contraseñas normales funcionan si el usuario no ha creado las contraseñas de aplicación. Una vez que se ha creado una contraseña de aplicación, se requieren contraseñas de aplicaciones.|Para las aplicaciones de cliente enriquecido, se requiere contraseñas de aplicaciones
Red corporativa exterior|Para los flujos de explorador requiere autenticación multifactor.|Para los flujos de explorador requiere autenticación multifactor.
|Para las aplicaciones de cliente enriquecido, se requiere contraseñas de aplicaciones.|Para las aplicaciones de cliente enriquecido, se requiere contraseñas de aplicaciones.

### <a name="to-enable-trusted-ips"></a>Para habilitar las direcciones IP de confianza

1. Inicie sesión en el portal de clásico de Azure.
2. En la parte izquierda, haga clic en Active Directory.
3. En, haga clic en directorio en el directorio que desea configurar IPsing de confianza en.
4. En el directorio que haya seleccionado, haga clic en configurar.
5. En la sección autenticación multifactor, haga clic en administrar la configuración de servicio.
6. En la página Configuración del servicio, en direcciones IP de confianza, seleccione:

    - Para federación de solicitudes de los usuarios que provienen de la intranet: todo federado los usuarios que están iniciar sesión desde la red corporativa evitarán autenticación multifactor mediante una notificación de AD FS.
    - Las solicitudes de un rango específico de direcciones IP de público: escriba las direcciones IP en los cuadros correspondientes utilizando la notación CIDR. Por ejemplo: xxx.xxx.xxx.0/24 para las direcciones IP de xxx.xxx.xxx.1 de rango: xxx.xxx.xxx.254 o xxx.xxx.xxx.xxx/32 para una única dirección IP. Puede introducir hasta 50 intervalos de direcciones IP.

7. Haga clic en Guardar.
8. Una vez que se han aplicado las actualizaciones, haga clic en Cerrar.



![Direcciones IP de confianza](./media/multi-factor-authentication-whats-next/trustedips3.png)




## <a name="app-passwords"></a>Contraseñas de aplicaciones

En algunas aplicaciones, como Office 2010 o versiones anteriores y Apple Mail no puede usar autenticación multifactor.  Para usar estas aplicaciones, deberá usar "contraseñas de aplicaciones" en lugar de la contraseña tradicional.  La contraseña de aplicación permite a la aplicación omitir la autenticación multifactor y continuar trabajando.

>[AZURE.NOTE] Autenticación moderna para los clientes de Office 2013
>
> Clientes de Office 2013 (como Outlook) ahora admiten nuevos protocolos de autenticación y se pueden habilitar para admitir autenticación multifactor.  Esto significa que una vez habilitada, las contraseñas de aplicación no son necesarias para los clientes de Office 2013.  Para obtener más información, vea [Office 2013 autenticación moderna público preview anunciada](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).



### <a name="important-things-to-know-about-app-passwords"></a>Cosas importantes que debe saber sobre contraseñas de aplicaciones

La siguiente es una lista importante de cosas que debe saber sobre las contraseñas de la aplicación.

- Los usuarios pueden tener varias contraseñas de aplicaciones, lo que aumenta la superficie de robo. Dado que son difíciles de recordar contraseñas de aplicaciones, podría aliente a personas a escribir hacia abajo. Esto no se recomienda y no debe hacerse porque sólo un factor es necesario para iniciar sesión con la contraseña de aplicación.
- Pueden iniciar aplicaciones que caché contraseñas y usarla en escenarios locales errores ya que no se conoce la contraseña de aplicación fuera de la organización id. Un ejemplo es que los mensajes de correo electrónico de Exchange son locales pero el correo archivado está en la nube. La misma contraseña no funciona.
- La contraseña real se genera automáticamente y no se proporciona por el usuario. Esto es porque es más difícil para un intruso adivinar la contraseña generada automáticamente y es más seguro.
- Hay un límite de 40 contraseñas por usuario. Se le pedirá para eliminar una de las contraseñas de aplicación existentes para crear uno nuevo.
- Una vez habilitada la autenticación multifactor en una cuenta de usuario, contraseñas de aplicaciones pueden utilizarse con la mayoría de los clientes como Outlook y Lync no explorador, pero no se puede realizar acciones administrativas con las contraseñas de aplicación a través de las aplicaciones del explorador no como Windows PowerShell, incluso si ese usuario tiene una cuenta administrativa.  Asegúrese de crear una cuenta de servicio con una contraseña segura para ejecutar las secuencias de comandos de PowerShell y no habilita esa cuenta para la autenticación multifactor.

>[AZURE.WARNING]  Contraseñas de aplicaciones no funcionan en entornos híbridos de donde los clientes comunican con ambos local y extremos de detección automática de la nube. Esto es porque las contraseñas de dominio requiere autenticación local y contraseñas de aplicaciones deben autenticar con la nube.


### <a name="naming-guidance-for-app-passwords"></a>Guía de nomenclatura para contraseñas de aplicaciones
Se recomienda que los nombres de contraseña de aplicación deberían reflejar el dispositivo en el que se usan. Por ejemplo, si tiene un equipo portátil que tiene no son exploradores aplicaciones como Outlook, Word y Excel, solo necesita crear una contraseña de aplicación denominada portátil y utilizar esa contraseña de aplicación en todas estas aplicaciones. Aunque puede crear contraseñas independientes para todas estas aplicaciones, no se recomienda. La recomienda forma es usar una contraseña de aplicación por dispositivo.


<center>![Nube](./media/multi-factor-authentication-whats-next/naming.png)</center>


### <a name="federated-sso-app-passwords"></a>Contraseñas de aplicación federado (SSO)
Azure AD admite la federación con local Windows Server Active Directory Domain Services (AD DS). Si su organización es federated(SSO) con Azure AD y va a utilizar autenticación multifactor de Azure, entonces es la siguiente información importante que debe tener en cuenta al usar contraseñas de aplicación. Esto se aplica solo a los clientes de federated(SSO).

- La contraseña de aplicación verificar Azure AD y, por tanto, evita la federación. Federación conocidas solo se usa cuando configure la contraseña de aplicación.
- Para usuarios de federated(SSO), nunca vamos al proveedor de identidades (IdP) a diferencia del flujo pasivo. Las contraseñas se almacenan en el identificador de la organización. Si el usuario deja la empresa, tiene esa información de flujo de identificador de organización con DirSync en tiempo real. Deshabilitar o eliminar la cuenta puede tardar hasta tres horas para sincronizar, retrasar deshabilitar o eliminación de contraseña de aplicación en Azure AD.
- No se respeta la configuración de Control de acceso de cliente local mediante una contraseña de aplicación
- Autenticación local registro / capacidad de auditoría no está disponible para la contraseña de aplicación
- Más formación del usuario final se requiere para el cliente de Microsoft Lync 2013. Para ver los pasos necesarios, vea cómo cambiar la contraseña en su correo electrónico a la contraseña de aplicación.
- Algunos diseños de arquitectura avanzados pueden requerir mediante una combinación de organización nombre de usuario y contraseñas y contraseñas de aplicaciones cuando se utiliza la autenticación multifactor con clientes, en función de dónde se autentican. Para los clientes que autenticarse una infraestructura local, debe usar un usuario profesional y la contraseña. Para los clientes que autenticarse Azure AD, debería usar la contraseña de aplicación.

Por ejemplo, suponga que tiene una arquitectura que consta de las siguientes acciones:

- Se federar su instancia local de Active Directory con Azure AD
- Está usando Exchange online
- Uso de Lync que está específicamente local
- Está utilizando la autenticación multifactor de Azure


![Proofup](./media/multi-factor-authentication-whats-next/federated.png)

 En estos casos, haga lo siguiente:

- Al iniciar sesión en Lync, use el nombre de usuario y la contraseña de su organización.
- Al intentar obtener acceso a la libreta de direcciones a través de un cliente de Outlook que se conecta a Exchange online, use una contraseña de aplicación.

### <a name="allowing-app-password-creation"></a>Permitir la creación de la contraseña de aplicación
De forma predeterminada, los usuarios no pueden crear contraseñas de aplicaciones.  Esta característica debe estar habilitada.  Para permitir que a los usuarios la capacidad para crear contraseñas de aplicaciones, utilice el procedimiento siguiente.

#### <a name="to-enable-users-to-create-app-passwords"></a>Permitir a los usuarios crear contraseñas de aplicaciones



1. Inicie sesión en el portal de clásico de Azure.
2. En la parte izquierda, haga clic en Active Directory.
3. En, haga clic en directorio en el directorio para el usuario que desea habilitar.
4. En la parte superior, haga clic en usuarios.
5. En la parte inferior de la página, haga clic en administrar la autenticación de varios factores  
6. En la parte superior de la página de autenticación multifactor, haga clic en configuración del servicio.
7. Asegúrese de que el botón de opción junto a permitir a los usuarios crear contraseñas de aplicaciones para iniciar sesión en las aplicaciones del explorador no está seleccionado.


![Crear contraseñas de aplicaciones](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="creating-app-passwords"></a>Crear contraseñas de aplicaciones
Los usuarios pueden crear contraseñas de aplicaciones durante su registro inicial.  Reciben una opción al final del proceso de registro que les permite crearlos.

Además los usuarios también pueden crear contraseñas de aplicaciones más adelante cambiando su configuración en el portal de Azure, el portal de Office 365 o

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>Para crear contraseñas de aplicaciones en el portal de Office 365
--------------------------------------------------------------------------------


1. Inicie sesión en el portal de Office 365
2. En la esquina superior derecha, seleccione el widget de configuración
3. A la izquierda, seleccione la comprobación de seguridad adicionales
4. A la derecha, seleccione **Actualizar mis números de teléfono que se utiliza para la seguridad de la cuenta**
5. En la página proofup, en la parte superior, seleccione contraseñas de aplicaciones
6. Haga clic en **crear**
7. Escriba un nombre para la contraseña de la aplicación y haga clic en **siguiente**
8. Copiar la contraseña de aplicación en el Portapapeles y pegarlo en la aplicación.

<center>![Nube](./media/multi-factor-authentication-whats-next/security.png)</center>


### <a name="to-create-app-passwords-in-the-azure-portal"></a>Para crear contraseñas de aplicaciones en el portal de Azure
--------------------------------------------------------------------------------
1. Inicie sesión en el portal de clásico de Azure.
3. En la parte superior, haga clic en el nombre de usuario y seleccione la comprobación de seguridad adicionales.
5. En la página proofup, en la parte superior, seleccione contraseñas de aplicaciones
6. Haga clic en **crear**
7. Escriba un nombre para la contraseña de la aplicación y haga clic en **siguiente**
8. Copiar la contraseña de aplicación en el Portapapeles y pegarlo en la aplicación.


![Contraseñas de aplicaciones](./media/multi-factor-authentication-whats-next/app2.png)

### <a name="to-create-app-passwords-if-you-do-not-have-an-office-365-or-azure-subscription"></a>Para crear contraseñas de aplicaciones si no tiene una suscripción a Office 365 o de Azure
--------------------------------------------------------------------------------
1. Inicie sesión en [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. En la parte superior, seleccione el perfil.
3. Haga clic en el nombre de usuario y seleccione la comprobación de seguridad adicionales.
5. En la página proofup, en la parte superior, seleccione contraseñas de aplicaciones
6. Haga clic en **crear**
7. Escriba un nombre para la contraseña de la aplicación y haga clic en **siguiente**
8. Copiar la contraseña de aplicación en el Portapapeles y pegarlo en la aplicación.

![Contraseñas de aplicaciones](./media/multi-factor-authentication-whats-next/myapp.png)

## <a name="remember-multi-factor-authentication-for-devices-users-trust"></a>Recuerde que la autenticación multifactor de confianza de los usuarios de dispositivos

Recordar la autenticación multifactor para dispositivos y exploradores que los usuarios confianza es una característica gratuita para todos los usuarios de AMF.  Le permite proporcionar a los usuarios la opción de derivación AMF para un determinado número de días después de realizar una correcta con AMF de inicio de sesión. Esto puede mejorar el uso de los usuarios.

Sin embargo, dado que los usuarios pueden recordar AMF para dispositivos de confianza, esta característica puede reducir la seguridad de la cuenta. Para garantizar la seguridad de la cuenta, debe restaurar la autenticación multifactor para sus dispositivos por cualquiera de las siguientes situaciones:

- Si se ha visto comprometido su cuenta corporativa
- Si un dispositivo de recordar pérdido o robo

> [AZURE.NOTE] Esta característica se implementa como una caché de cookies del explorador. No funciona si no tiene habilitadas las cookies de tu explorador.

### <a name="how-to-enabledisable-remember-multi-factor-authentication"></a>Cómo habilitar o deshabilitar la autenticación multifactor de recordar

1. Inicie sesión en el portal de clásico de Azure.
2. En la parte izquierda, haga clic en Active Directory.
3. En Active Directory, haga clic en el directorio que desea configurar la autenticación multifactor de recordar para dispositivos.
4. En el directorio que haya seleccionado, haga clic en configurar.
5. En la sección autenticación multifactor, haga clic en administrar la configuración de servicio.
6. En la página Configuración del servicio, en administrar la configuración de dispositivo de usuario, seleccionar o anular la selección de **Permitir a los usuarios para recordar la autenticación multifactor en dispositivos confían**.
![Recuerde dispositivos](./media/multi-factor-authentication-whats-next/remember.png)
8. Establezca el número de días que desea permitir la suspensión. El valor predeterminado es 14 días.
9. Haga clic en Guardar.
10. Haga clic en Cerrar.


## <a name="selectable-verification-methods"></a>Métodos de verificación seleccionables
En las versiones de la nube y el local, puede elegir qué métodos de verificación están disponibles para los usuarios. La tabla siguiente proporciona una breve descripción de cada método.

Cuando los usuarios inscriban sus cuentas AMF, eligen su método de verificación preferido fuera de las opciones que ha habilitado. Se tratan las instrucciones para su proceso de inscripción en [Configurar Mi cuenta para la verificación en dos pasos](multi-factor-authentication-end-user-first-time.md)

Método|Descripción
:------------- | :------------- |
Llamar al teléfono |  Realiza una llamada de voz automatizado al teléfono de autenticación. El usuario responde a la llamada y presione # en el teclado del teléfono para autenticar. Este número de teléfono no se sincroniza a local de Active Directory.
Mensaje de texto al teléfono | Envía un mensaje de texto que contiene un código de comprobación para el usuario. Se pide en responder al mensaje de texto con el código de verificación o introducir el código de verificación en la interfaz de inicio de sesión del usuario.
Notificación a través de la aplicación móvil | En este modo, la aplicación de Microsoft Authenticator impide el acceso no autorizado a las cuentas y deja las transacciones fraudulentas. Esto se hace con una notificación de inserción a su teléfono o dispositivo registrado. Simplemente ver la notificación y si es legítima pulse comprobar. En caso contrario, se puede elegir denegar o denegar y la notificación fraudulenta de informes. Para obtener información sobre cómo informar de notificaciones fraudulentas consulte cómo usar la característica de fraude de informe y denegar con autenticación multifactor.</br></br>La aplicación de Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)y [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).|
Código de comprobación de la aplicación móvil | En este modo, la aplicación de Microsoft Authenticator puede utilizarse como un símbolo de software para generar un código de comprobación de JURAMENTO. Este código de comprobación, a continuación, se pueden especificar junto con el nombre de usuario y contraseña para proporcionar la segunda forma de autenticación.</li><br><p> La aplicación de Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)y [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

### <a name="how-to-enabledisable-authentication-methods"></a>Cómo habilitar o deshabilitar los métodos de autenticación

1. Inicie sesión en el portal de clásico de Azure.
2. En la parte izquierda, haga clic en Active Directory.
3. En Active Directory, haga clic en el directorio que desea habilitar o deshabilitar los métodos de autenticación.
4. En el directorio que haya seleccionado, haga clic en configurar.
5. En la sección autenticación multifactor, haga clic en administrar la configuración de servicio.
6. En la página Configuración del servicio, en Opciones de comprobación, seleccione o anule la selección de las opciones que desee usar.</br></br>
![Opciones de comprobación](./media/multi-factor-authentication-whats-next/authmethods.png)
9. Haga clic en Guardar.
10. Haga clic en Cerrar.
