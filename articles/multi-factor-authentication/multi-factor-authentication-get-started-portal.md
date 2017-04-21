<properties 
    pageTitle="Implementar el portal de usuario para el servidor de autenticación multifactor de Azure"
    description="Esta es la página de autenticación multifactor de Azure que describe cómo empezar a trabajar con Azure AMF y el portal de usuario."
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
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="kgremban"/>

# <a name="deploying-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Implementar el portal de usuario para el servidor de autenticación multifactor de Azure

El Portal de usuario permite al administrador instalar y configurar el Portal de usuario de autenticación de varios factores de Azure. El Portal de usuario es un sitio web IIS que permite a los usuarios para inscribirse en Azure con autenticación multifactor y mantener sus cuentas. Un usuario puede cambiar su número de teléfono, cambiar su PIN o eludir autenticación multifactor de Azure durante el inicio de sesión siguiente en.

Los usuarios se inicie sesión en el portal de usuario con su nombre de usuario normal y la contraseña y se completar una llamada de autenticación multifactor de Azure o responder a preguntas de seguridad para completar la autenticación. Si se permite la inscripción de usuario, un usuario configurará su número de teléfono y el PIN la primera vez que inicie sesión en el Portal de usuario.

Los administradores del Portal de usuario se puede configurar y le ha concedido permiso para agregar nuevos usuarios y actualizar los usuarios existentes.

<center>![Programa de instalación](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## <a name="deploying-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Implementar el portal de usuario en el mismo servidor que el servidor de autenticación multifactor de Azure

Los siguientes requisitos previos se necesitan para instalar el Portal de usuarios en el mismo servidor que el servidor de autenticación multifactor de Azure:

- IIS debe instalarse incluido asp.net y compatibilidad de base de metadatos de IIS 6 (para IIS 7 o posterior)
- Sesión iniciada en usuario debe tener derechos de administrador para el equipo y el dominio, si corresponde.  Esto es porque la cuenta necesita permisos para crear grupos de seguridad de Active Directory.

### <a name="to-deploy-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Para implementar el portal de usuario para el servidor de autenticación multifactor de Azure

1. En el servidor de autenticación multifactor Azure: haga clic en el icono de Portal de usuario en el menú de la izquierda, haga clic en el botón instalar el Portal de usuario.
1. Haga clic en siguiente.
1. Haga clic en siguiente.
1. Si el equipo está unido a un dominio y la configuración de Active Directory para proteger la comunicación entre el Portal de usuario y el servicio de autenticación multifactor de Azure no está completa, se mostrará el paso de Active Directory. Haga clic en el botón siguiente para completar automáticamente esta configuración.
1. Haga clic en siguiente.
1. Haga clic en siguiente.
1. Haga clic en Cerrar.
1. Abra un navegador web desde cualquier equipo y vaya a la dirección URL donde se instaló el Portal de usuario (por ejemplo, https://www.publicwebsite.com/MultiFactorAuth). Asegúrese de que no se muestran certificado advertencias ni errores.

<center>![Programa de instalación](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## <a name="deploying-the-azure-multi-factor-authentication-server-user-portal-on-a-separate-server"></a>Implementar el Portal de usuario de servidor de Azure autenticación multifactor en un servidor independiente

Para usar la aplicación de autenticación multifactor de Azure, se requiere lo siguiente para que la aplicación puede comunicarse correctamente con el Portal de usuario:

Consulte requisitos de Hardware y Software de requisitos de hardware y software:

- Debe estar usando v6.0 o posterior del servidor de autenticación multifactor de Azure.
- Portal de usuario debe estar instalado en un servidor de web de a través de Internet con Microsoft® Internet Information Services (IIS) 6.x, IIS 7.x o superior.
- Cuando se utiliza IIS 6.x, compruebe ASP.NET v2.0.50727 está instalado, registrados y permitido.
- Servicios de rol requeridos cuando se utiliza IIS 7.x o superior incluyen ASP.NET y compatibilidad de Metabase de IIS 6.
- Portal de usuario debe protegerse con un certificado SSL.
- Debe estar instalado el SDK del servicio Web de Azure de autenticación multifactor en IIS 6.x, IIS 7.x o superior en el servidor que está instalado el servidor de autenticación multifactor de Azure.
- El SDK del servicio Web de Azure de autenticación multifactor deben estar protegido con un certificado SSL.
- Portal de usuario debe poder conectar con el SDK del servicio Web de Azure de autenticación multifactor sobre SSL.
- Portal de usuario debe ser capaces de autenticarse en el SDK de servicio Azure multifactor autenticación Web con las credenciales de una cuenta de servicio que es miembro de un grupo de seguridad denominado "PhoneFactor administradores". Esta cuenta de servicio y el grupo existen en Active Directory si el servidor de autenticación multifactor de Azure se ejecuta en un servidor de dominio. Esta cuenta de servicio y grupo existen localmente en el servidor de autenticación multifactor de Azure si no se ha unido a un dominio.

Instalar el portal de usuario en un servidor distinto del servidor de autenticación multifactor de Azure requiere los siguientes pasos:

1. Instalar el servicio web SDK
2. Instalar el portal de usuario
3. Configurar la configuración del Portal de usuario en el servidor de Azure autenticación multifactor


### <a name="install-the-web-service-sdk"></a>Instalar el servicio web SDK

Si el SDK del servicio Web de Azure de autenticación multifactor ya no está instalado en el servidor de autenticación multifactor de Azure, vaya a ese servidor y abra el servidor de autenticación multifactor de Azure. Haga clic en el icono de SDK del servicio Web, haga clic en el SDK de servicio Web instalar... botón y siga las instrucciones. El SDK de servicio Web deben estar protegido con un certificado SSL. Un certificado autofirmado es correcto para ello, pero tiene que se pueden importar a la tienda "Entidades de certificación de raíz de confianza" de la cuenta de equipo Local en el servidor web de Portal de usuario para que confíe certificado al iniciar la conexión SSL.

<center>![Programa de instalación](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### <a name="install-the-user-portal"></a>Instalar el portal de usuario

Antes de instalar el portal de usuario en un servidor independiente, tenga en cuenta lo siguiente:

- Resulta útil abrir un explorador web en el servidor web a través de Internet y vaya a la dirección URL del SDK de servicio Web que se especificó en el archivo web.config. Si el explorador puede acceder al servicio web correctamente, debe solicitar las credenciales. Escriba el nombre de usuario y la contraseña que se especificaron en el archivo web.config tal y como aparece en el archivo. Asegúrese de que no se muestran certificado advertencias ni errores.
- Si se encuentra un proxy inverso o un firewall delante del servidor web de Portal de usuario y realizar la descarga de SSL, puede editar el archivo web.config de Portal de usuario y agregar la siguiente clave para el <appSettings> sección para que el Portal de usuario puede usar http en lugar de https. <add key="SSL_REQUIRED" value="false"/>

#### <a name="to-install-the-user-portal"></a>Para instalar el portal de usuario

1. Abra el Explorador de Windows en el servidor de servidor de autenticación multifactor de Azure y vaya a la carpeta donde está instalado el servidor de autenticación multifactor de Azure (por ejemplo, C:\Program Files\Multi Factor autenticación de servidor). Elegir la versión de 32 bits o 64 bits del archivo de instalación MultiFactorAuthenticationUserPortalSetup según corresponda para el servidor que se instala en el Portal de usuario. Copie el archivo de instalación en el servidor a través de Internet.
2. En el servidor web a través de Internet, se debe ejecutar el archivo de instalación con derechos de administrador. La manera más sencilla de hacerlo es abrir un símbolo del sistema como administrador y desplácese hasta la ubicación donde se ha copiado el archivo de instalación.
3. Ejecute el archivo de instalación MultiFactorAuthenticationUserPortalSetup64, cambie el nombre del sitio y directorio Virtual si lo desea.
4. Una vez finalizada la instalación del Portal de usuario, vaya a C:\inetpub\wwwroot\MultiFactorAuth (o directorio apropiado basado en el nombre del directorio virtual) y editar el archivo web.config.
5. Busque la clave USE_WEB_SERVICE_SDK y cambie el valor de false a true. Busque las teclas WEB_SERVICE_SDK_AUTHENTICATION_USERNAME y WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD y establezca los valores en el nombre de usuario y la contraseña de la cuenta de servicio que es miembro de la seguridad PhoneFactor Admins grupo (vea la sección requisitos anterior). Asegúrese de escribir el nombre de usuario y la contraseña entre las comillas al final de la línea (valor = "" / >). Se recomienda usar un nombre de usuario completo (por ejemplo, DOMINIO\nombre de usuario o equipo ombreDeUsuario)
6. Busque la configuración de pfup_pfwssdk_PfWsSdk y cambie el valor de "http://localhost:4898/PfWsSdk.asmx" a la dirección URL del SDK de servicio Web que se ejecuta en el servidor de autenticación multifactor de Azure (por ejemplo, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Puesto que se utiliza SSL para esta conexión, debe hacer referencia el SDK de servicio Web por el nombre del servidor y no la dirección IP ya habrá ha emitido el certificado SSL para el nombre del servidor y la dirección URL utilizada debe coincidir con el nombre del certificado. Si el nombre del servidor no se resuelve en una dirección IP del servidor a través de Internet, agregue una entrada al archivo hosts en el servidor para asignar el nombre del servidor de autenticación multifactor de Azure a su dirección IP. Guarde el archivo web.config después de que se han realizado cambios.
7. Si el sitio Web que el Portal de usuario se ha instalado en (por ejemplo, sitio Web predeterminado) ya no está enlazado con un certificado de firma públicamente, instale el certificado en el servidor si no están instalados, abra el Administrador de IIS y enlazar el certificado al sitio Web.
8. Abra un navegador web desde cualquier equipo y vaya a la dirección URL donde se instaló el Portal de usuario (por ejemplo, https://www.publicwebsite.com/MultiFactorAuth). Asegúrese de que no se muestran certificado advertencias ni errores.



## <a name="configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Configurar la configuración del portal de usuario en el servidor de autenticación multifactor de Azure
Ahora que está instalado el portal, debe configurar el servidor de autenticación multifactor de Azure para trabajar con el portal.

Servidor de autenticación multifactor Azure ofrece varias opciones para el portal de usuario.  La tabla siguiente proporciona una lista de estas opciones y obtener una explicación de lo que se utilizan.

Configuración del Portal de usuario|Descripción|
:------------- | :------------- |
URL del Portal de usuario| Le permite especificar la dirección URL de donde se hospeda el portal.
Autenticación principal| Le permite especificar el tipo de autenticación que se utiliza al iniciar sesión el portal.  Autenticación de Windows, el radio o LDAP.
Permitir a los usuarios iniciar sesión|Permite a los usuarios escribir un nombre de usuario y contraseña en la página de inicio de sesión para el portal de usuario.  Si esta opción no está seleccionada, los cuadros se atenuado.
Permitir la inscripción de usuario|Permite que el usuario para inscribirse en autenticación multifactor realizando una pantalla de configuración que les pedirá información adicional, como el número de teléfono.  Solicitar una copia de seguridad teléfono permite a los usuarios especificar un número de teléfono secundario.  Preguntar por terceros token JURAMENTO permite a los usuarios especificar un símbolo de JURAMENTO parte 3ª.
Permitir a los usuarios iniciar omisión temporal| Esto permite a los usuarios iniciar una sola vez omisión.  Si ninguno de los conjuntos de usuario, tardará afectan a la próxima vez que el usuario inicia sesión.  Solicitar omisión segundos proporciona al usuario con un cuadro para que puedan cambiar el valor predeterminado de 300 segundos.  En caso contrario, la omisión de una sola vez es buena para 300 segundos.
Permitir a los usuarios seleccionar método| Permite a los usuarios especificar el método de contacto principal.  Puede ser de llamada de teléfono, mensaje de texto, una aplicación móvil o token JURAMENTO.
Permitir que los usuarios puedan seleccionar idioma|  Permite al usuario cambiar el idioma que se usa para la llamada de teléfono, mensaje de texto, una aplicación móvil o token JURAMENTO.
Permitir a los usuarios activar una aplicación móvil| Permite a los usuarios generar un código de activación para completar el proceso de activación de la aplicación móvil que se utiliza con el servidor.  También puede establecer el número de dispositivos que pueden activar en.  Entre 1 y 10.
Utilizar preguntas de seguridad para reserva|Le permite utilizar preguntas de seguridad en caso de que se produce un error en la autenticación multifactor.  Puede especificar el número de preguntas de seguridad que se deben responder correctamente.
Permitir a los usuarios asociar token JURAMENTO de terceros| Permite a los usuarios especificar un símbolo de JURAMENTO de terceros.
Usar el token de JURAMENTO para reserva|Permite el uso de un símbolo de JURAMENTO en el caso de que la autenticación multifactor no es correcta.  También puede especificar el tiempo de espera de sesión en minutos.
Habilitar el registro|Habilita el registro en el portal de usuario.  Los archivos de registro se encuentran en: C:\Program Files\Multi Factor autenticación Server\Logs.

La mayoría de estos parámetros son visibles para el usuario una vez que están habilitados y los signos de usuario en el portal de usuario.

![Configuración del portal de usuario](./media/multi-factor-authentication-get-started-portal/portalsettings.png)



### <a name="to-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Configurar la configuración del portal de usuario en el servidor de autenticación multifactor de Azure




1. En el servidor de autenticación multifactor de Azure, haga clic en el icono de Portal de usuario. En la pestaña Configuración, escriba la dirección URL del Portal de usuario en el cuadro de texto de la dirección URL del Portal de usuario. Esta dirección URL se insertará en mensajes de correo electrónico que se envían a los usuarios cuando se importan en el servidor de autenticación multifactor de Azure si se ha habilitado la funcionalidad de correo electrónico.
2. Elija la configuración que desea usar en el Portal de usuario. Por ejemplo, si los usuarios pueden controlar sus métodos de autenticación, asegúrese de que está seleccionado permitir a los usuarios seleccionar método junto con los métodos puede elegir entre.
3. Haga clic en el vínculo Ayuda en la esquina superior derecha para obtener ayuda sobre cualquiera de las opciones que aparecen.

<center>![Programa de instalación](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## <a name="administrators-tab"></a>Ficha administradores
Esta pestaña simplemente le permite agregar usuarios que tendrán privilegios de administrador.  Al agregar un administrador, puede ajustar los permisos que reciben.  De este modo, puede Asegúrese conceda sólo los permisos necesarios para el administrador.  Simplemente haga clic en el botón Agregar y, a continuación, seleccione y usuario y sus permisos y, a continuación, haga clic en Agregar.

![Administradores del portal de usuario](./media/multi-factor-authentication-get-started-portal/admin.png)


## <a name="security-questions"></a>Preguntas de seguridad
Esta pestaña permite especificar las preguntas de seguridad que los usuarios tendrán que proporcionan respuestas a si las preguntas de seguridad de uso de la opción de reserva está seleccionada.  Servidor de autenticación multifactor Azure incluye preguntas predeterminadas que puede usar.  También puede cambiar el orden o agregar sus propias preguntas.  Al agregar sus propias preguntas, puede especificar el idioma que desea que esas pregunta aparezca en también.

![Preguntas de seguridad del portal de usuario](./media/multi-factor-authentication-get-started-portal/secquestion.png)


## <a name="passed-sessions"></a>Sesiones pasadas

## <a name="saml"></a>SAML
Le permite configurar el portal de usuario para aceptar las notificaciones de un proveedor de identidades con SAML.  Puede especificar el tiempo de espera sesión, especifique el certificado de verificación y el registro de la dirección URL de redireccionamiento.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## <a name="trusted-ips"></a>Direcciones IP de confianza
Esta pestaña permite especificar direcciones IP únicas o intervalos de direcciones IP que se pueden agregar para que si un usuario está iniciando sesión desde una de estas direcciones IP, se omite la autenticación multifactor.

![Direcciones IP de confianza de portal de usuario](./media/multi-factor-authentication-get-started-portal/trusted.png)

## <a name="self-service-user-enrollment"></a>Inscripción de autoservicio de usuario
Si desea que los usuarios a iniciar sesión e inscribir debe seleccionar el inicio de sesión en permitir que los usuarios y permitir opciones de inscripción del usuario. Recuerde que la configuración que seleccione afectará a la experiencia de inicio de sesión del usuario.

Por ejemplo, cuando un usuario inicia sesión en el Portal de usuario y hace clic en el botón Iniciar sesión, a continuación, se toman a la página de configuración de usuario de autenticación multifactor de Azure.  Dependiendo de cómo haya configurado la autenticación multifactor de Azure, el usuario puede seleccionar el método de autenticación.  

Si se selecciona el método de autenticación de llamada de voz o se han preconfigurado para usar este método, la página solicitará al usuario que escriba su número de teléfono principal y la extensión, si procede.  También pueden especificar un número de teléfono de copia de seguridad.  

![Direcciones IP de confianza de portal de usuario](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Si el usuario debe usar un PIN cuando se autentican, la página también les pedirá que escriba un PIN.  Después de escribir sus números de teléfono y el PIN (si procede), el usuario hace clic en la llamada Me ahora para autenticar botón.  La autenticación multifactor Azure realizará una autenticación de llamada de teléfono a número de teléfono principal del usuario.  El usuario debe responder a la llamada de teléfono y escriba su PIN (si procede) y presione # para pasar al siguiente paso del proceso de inscripción automática.   

Si el usuario selecciona el método de autenticación de texto SMS o se ha preconfigurado para usar este método, la página solicitará al usuario para su número de teléfono móvil.  Si el usuario debe usar un PIN cuando se autentican, la página también les pedirá que escriba un PIN.  Después de escribir su número de teléfono y el PIN (si procede), el usuario hace clic el texto Me ahora al botón autenticar.  La autenticación multifactor Azure realizará una autenticación de SMS en el teléfono del usuario móvil.  El usuario debe recibir el servicio SMS que contiene un código de tiempo de uno (OTP) y responder al mensaje con ese OTP plus su PIN si procede) para pasar al siguiente paso del proceso de inscripción automática.

![Portal de usuario SMS](./media/multi-factor-authentication-get-started-portal/text.png)   

Si el usuario selecciona el método de autenticación de la aplicación móvil o se ha preconfigurado para usar este método, la página solicitará al usuario que instale la aplicación de autenticación multifactor de Azure en su dispositivo y generar un código de activación.  Después de instalar la aplicación de autenticación multifactor de Azure, el usuario hace clic en el botón Generar código de activación.    

>[AZURE.NOTE]Para usar la aplicación de autenticación multifactor de Azure, el usuario debe habilitar las notificaciones de inserción para su dispositivo.

La página, a continuación, muestra un código de activación y la dirección URL junto con una imagen de código de barras.  Si el usuario debe usar un PIN cuando se autentican, la página también les pedirá que escriba un PIN.  El usuario escribe el código de activación y la dirección URL en la aplicación de autenticación multifactor de Azure o utiliza el escáner para digitalizar la imagen de código de barras y hace clic en el botón Activar.    

Una vez completada la activación, el usuario hace clic en el botón autenticar ahora.  La autenticación multifactor Azure realizará una autenticación para una aplicación móvil del usuario.  El usuario debe escribir su PIN (si procede) y presione el botón de autenticación en su aplicación móvil para pasar al siguiente paso del proceso de inscripción automática.  


Si los administradores han configurado el servidor de autenticación multifactor de Azure para recopilar preguntas y respuestas acerca de seguridad, el usuario pasa a la página de preguntas de seguridad.  El usuario debe seleccionar cuatro preguntas de seguridad y se proporcionan respuestas a sus preguntas seleccionados.    

![Preguntas de seguridad del portal de usuario](./media/multi-factor-authentication-get-started-portal/secq.png)  

La inscripción automática de usuario está ahora completa y el usuario se ha iniciado sesión en el Portal de usuario.  Usuarios pueden iniciar sesión en el Portal de usuario en cualquier momento en el futuro para cambiar sus números de teléfono, PIN, métodos de autenticación y preguntas de seguridad si lo permite sus administradores.
