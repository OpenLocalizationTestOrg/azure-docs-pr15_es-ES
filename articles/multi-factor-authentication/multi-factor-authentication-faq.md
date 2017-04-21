<properties
    pageTitle="Preguntas más frecuentes de Azure autenticación multifactor"
    description="Proporciona una lista de las preguntas más frecuentes y respuestas relacionados con la autenticación multifactor de Azure. Autenticación multifactor es un método para comprobar la identidad de un usuario que requiere más de un nombre de usuario y contraseña. Proporciona una capa adicional de seguridad para el inicio de sesión de usuario y las transacciones."
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
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="kgremban"/>

# <a name="azure-multi-factor-authentication-faq"></a>Preguntas más frecuentes de Azure autenticación multifactor


Este artículo responde preguntas habituales sobre la autenticación multifactor de Azure y usar el servicio de autenticación multifactor, incluidas preguntas sobre el modelo de facturación y el uso.

## <a name="general"></a>General

**P: ¿cómo manejar servidor de autenticación multifactor de Azure los datos de usuario?**

Con el servidor de autenticación multifactor, datos de usuario se almacenan únicamente en los servidores locales. No hay datos de usuario persistentes se almacenan en la nube. Cuando el usuario realiza la verificación en dos pasos, servidor de autenticación multifactor envía datos al servicio de nube de Azure con autenticación multifactor para la autenticación. Comunicación entre el servidor de autenticación multifactor y el servicio de nube de autenticación multifactor utiliza Secure Sockets Layer (SSL) o seguridad de capa de transporte (TLS) a través del puerto 443 saliente.

Cuando se envían las solicitudes de autenticación al servicio de nube, se recopilan los datos de autenticación y el uso de informes. Campos de datos incluidos en los registros de comprobación de dos pasos son los siguientes:

- **Identificador exclusivo** (cualquier nombre o local multifactor autenticación Server ID de usuario)
- **Nombre y apellidos** (opcional)
- **Dirección de correo electrónico** (opcional)
- **Número de teléfono** (cuando se usa una llamada de voz o la autenticación de SMS)
- **Símbolo de dispositivo** (cuando se utiliza la autenticación de la aplicación móvil)
- **Modo de autenticación**
- **Resultado de la autenticación**
- **Nombre del servidor de autenticación multifactor**
- **Servidor de autenticación multifactor IP**
- **Cliente IP** (si está disponible)

Los campos opcionales se pueden configurar en servidor de autenticación multifactor.

El resultado de comprobación (éxito o denegación) y el motivo si le fue denegado, se almacena con los datos de autenticación y está disponibles en los informes de autenticación y el uso.


## <a name="billing"></a>Facturación

Pueden responder a preguntas de facturación mayoría puede hacer referencia a la [página de precios de autenticación multifactor](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

**¿P: mi organización cargada para llamadas de teléfono o mensajes de texto sirve para autenticar Mis usuarios?**

No se cargan las organizaciones individuales llamadas de teléfono a mano o texto mensajes enviados a los usuarios a través de la autenticación multifactor de Azure. Los propietarios de teléfono podrían cobrará por las llamadas de teléfono o mensajes de texto que reciban, según su servicio de teléfono personales.

**P: ¿sirve el coste de modelo de facturación de cada usuario basándose en el número de usuarios que están configurados para usar autenticación multifactor o el número de usuarios que realizar comprobaciones?**

Facturación se basa en el número de usuarios configurado para usar autenticación multifactor.

**P: ¿cómo funciona la facturación de la autenticación multifactor**

Cuando se utiliza el "por usuario" o "por"autenticación AMF modelo, Azure es un recurso de consumo. Se cargarán los gastos de suscripción de Azure de la organización como máquinas virtuales, sitios Web, etcetera.

Cuando se utiliza el modelo de licencias, licencias de Azure con autenticación multifactor son comprados y asignados a los usuarios, como para Office 365 y otros productos de suscripción.

**P: ¿hay una versión gratuita de Azure con autenticación multifactor para administradores?**

En algunos casos, sí. Autenticación multifactor para administradores de Azure ofrece un subconjunto de características de Azure AMF sin costo. Esta oferta se aplica a los miembros del grupo de administradores Global de Azure en instancias de Azure Active Directory que no están vinculadas a un proveedor de autenticación multifactor de Azure basado en consumo. Usar un proveedor de autenticación multifactor actualiza todos los administradores y usuarios en el directorio que están configurados para usar autenticación multifactor a la versión completa de Azure con autenticación multifactor.

**P: ¿hay una versión gratuita de Azure con autenticación multifactor para usuarios de Office 365?**

En algunos casos, sí. Autenticación multifactor para Office 365 ofrece un subconjunto de características de Azure AMF sin costo. Esta oferta se aplica a los usuarios que tienen una licencia de Office 365 asignada, cuando un proveedor de autenticación multifactor de Azure basado en consumo no se ha vinculado a la instancia correspondiente de Azure Active Directory. Con el proveedor de autenticación multifactor actualiza todos los administradores y usuarios en el directorio que están configurados para usar autenticación multifactor a la versión completa de Azure con autenticación multifactor.

**P: ¿puede cambiar de mi organización entre modelos de facturación del consumo de por usuario y por autenticación en cualquier momento?**

Su organización elige un modelo de facturación cuando se crea un recurso. No puede cambiar un modelo de facturación después de que se aprovisiona el recurso. Sin embargo, puede crear otro recurso de autenticación multifactor para reemplazar el original. Configuración de usuario y las opciones de configuración no se puede transferir al nuevo recurso.

**P: ¿puede mi organización alternar la facturación de consumo y el modelo de licencia en cualquier momento?**

Cuando se agregan licencias a un directorio que ya tiene un proveedor de autenticación multifactor de Azure por usuario, basado en el consumo de facturación es disminuye el número de licencias de su propiedad. Si todos los usuarios configurados para usar autenticación multifactor tienen licencias asignadas, el administrador puede eliminar el proveedor de servicios de autenticación multifactor de Azure.

No se pueden mezclar facturación de consumo por la autenticación con un modelo de licencia. Cuando un proveedor de autenticación multifactor por autenticación está vinculado a un directorio, la organización se cobra para todas las solicitudes de comprobación de autenticación multifactor, independientemente de las licencias de su propiedad.

**¿P: mi organización tiene que utilizar y sincronizar identidades para usar la autenticación multifactor de Azure?**

Cuando una organización utiliza un modelo de facturación de consumo, Azure Active Directory no es necesario. Vincular un proveedor de autenticación multifactor en un directorio es opcional. Si su organización no está vinculado a un directorio, puede implementar servidor de autenticación multifactor de Azure o el SDK de autenticación multifactor de Azure local.

Azure Active Directory se requiere para el modelo de licencia porque se agregan licencias en el directorio cuando compra y asignarlos a los usuarios en el directorio.


## <a name="usability"></a>Facilidad de uso

**P: ¿qué hace un usuario si no reciben una respuesta en su teléfono, o si el teléfono no está disponible para el usuario?**

Si el usuario ha configurado un teléfono de copia de seguridad, deben seleccionar ese teléfono cuando se le solicite en la página de inicio de sesión y vuelva a intentarlo. Si el usuario no tiene otro método configurado, el Administrador de la organización puede actualizar el número asignado al teléfono del usuario principal.


**P: ¿qué hace el administrador si el Administrador de una cuenta que el usuario no puede acceder de contactos de un usuario?**

El administrador puede restablecer la cuenta de usuario por pedirle que vuelva a través del proceso de registro. Más información sobre la [administración de usuario y la configuración del dispositivo con autenticación multifactor de Azure en la nube](multi-factor-authentication-manage-users-and-devices.md).

**P: ¿qué hace un administrador si la pérdida o robo de teléfono de un usuario que está utilizando contraseñas de aplicaciones?**

El administrador puede eliminar contraseñas de aplicaciones de todos los del usuario para evitar el acceso no autorizado. Después de que el usuario tiene un dispositivo de reemplazo, puede volver a crear las contraseñas. Más información sobre la [administración de usuario y la configuración del dispositivo con autenticación multifactor de Azure en la nube](multi-factor-authentication-manage-users-and-devices.md).

**P: ¿qué pasa si el usuario no puede iniciar sesión en aplicaciones del explorador no?**

Un usuario que está configurado para utilizar autenticación multifactor requiere una contraseña de aplicación para iniciar sesión en algunas aplicaciones no son exploradores. Un usuario debe borrar la información de inicio de sesión (eliminar), reinicie la aplicación e inicie sesión utilizando su nombre y aplicación de contraseña de usuario.

Obtenga más información sobre cómo crear contraseñas de aplicaciones y otros [Ayuda con contraseñas de aplicaciones](multi-factor-authentication-end-user-app-passwords.md).


>[AZURE.NOTE] Autenticación moderna para clientes de Office 2013
>
> Clientes de Office 2013 (incluidos Outlook) admiten protocolos de autenticación de nuevo. Puede configurar Office 2013 para admitir autenticación multifactor. Después de configurar Office 2013, las contraseñas de aplicación no son necesarias para los clientes de Office 2013. Para obtener más información, consulte el [anuncio de versión preliminar pública de Office 2013 autenticación moderna](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**P: ¿qué hace un usuario si el usuario no recibe un mensaje de texto, o si el usuario responde a un mensaje de texto bidireccional pero agota el tiempo de espera de la comprobación?**

Entregar de mensajes de texto y, a continuación, no se garantiza la recepción de respuestas de SMS bidireccional porque hay no controlados factores que pueden afectar a la confiabilidad del servicio. Estos factores incluyen el país de destino, el operador de telefonía móvil y la intensidad de señal.

Los usuarios que tengan dificultades para recibir mensajes de texto de forma segura deben seleccionar el método de aplicación o llamada de teléfono móvil en su lugar. La aplicación móvil puede recibir notificaciones tanto sobre la red de telefonía móvil y conexiones Wi-Fi. Además, la aplicación móvil puede generar códigos de verificación incluso cuando el dispositivo no tiene ninguna señal en absoluto. La aplicación de Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)y [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Si debe usar mensajes de texto, se recomienda usar SMS unidireccional en lugar de SMS bidireccionales cuando sea posible. SMS unidireccional es más confiable e impide que los usuarios provocar globales cargos SMS de responder a un mensaje de texto que se envía desde otro país.


**P: ¿puedo usar tokens de hardware con el servidor de autenticación multifactor de Azure?**

Si está utilizando el servidor de autenticación multifactor de Azure, puede importar tokens de contraseña de una sola vez, basado en tiempo (TOTP) de terceros autenticación abierta (JURAMENTO) y usarlas para activó la verificación.

Puede usar tokens ActiveIdentity que están tokens JURAMENTO TOTP si coloca el archivo de clave secreto en un archivo CSV e importar al servidor de autenticación multifactor de Azure. Puede usar tokens JURAMENTO con Active Directory Federation Services (ADFS), servicio autenticación remota telefónica de usuario (RADIUS) cuando el sistema de cliente puede procesar las respuestas de desafío de acceso y la autenticación basada en formularios de Internet Information Server (IIS).

Puede importar tokens JURAMENTO TOTP de terceros con los siguientes formatos:  
- Contenedor de clave simétrica portátil (PSKC)  
- CSV si el archivo contiene un número de serie, una clave secreta en formato 32 Base y un intervalo de tiempo  

**P: ¿puedo usar el servidor de autenticación multifactor de Azure proteger Terminal Services?**

Sí, pero, si está utilizando Windows Server 2012 R2 o versiones posteriores, solo mediante puerta de enlace de escritorio remoto (puerta de enlace de escritorio remoto).

Cambios de seguridad de Windows Server 2012 R2 han cambiado la forma en que el servidor de autenticación multifactor de Azure se conecta al paquete de seguridad de la entidad de seguridad Local (LSA) en Windows Server 2012 y versiones anteriores. Las versiones de Terminal Services en Windows Server 2012 o versiones anteriores, puede [proteger una aplicación con la autenticación de Windows](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Si usa Windows Server 2012 R2, debe puerta de enlace de escritorio remoto.

**P: ¿por qué un usuario recibiría una llamada de autenticación multifactor de una persona que llama anónima después de configurar el identificador de llamada?**

Cuando se colocan autenticación multifactor llamadas a través de la red telefónica pública, a veces se redirigen a través de un operador que no admite el Id. Por este motivo, no se garantiza identificador de llamada, aunque el sistema de autenticación multifactor envía siempre.


## <a name="errors"></a>Errores

**P: ¿qué deberían hacer los usuarios si ve un mensaje de error "solicitud de autenticación no es para una cuenta activada" al usar notificaciones de la aplicación móvil?**

Pedirles que siga este procedimiento para quitar su cuenta de la aplicación móvil, a continuación, vuelva a agregarla:

1. Vaya a [su perfil de portal Azure](https://account.activedirectory.windowsazure.com/profile/) e inicie sesión con su cuenta de la organización.
2. Seleccione la **comprobación de seguridad adicional**.
4. Quitar la cuenta existente de la aplicación móvil.
5. Haga clic en **Configurar**y, a continuación, siga las instrucciones para volver a configurar la aplicación móvil.


**P: ¿qué deberían hacer los usuarios si ve un mensaje de error 0x800434D4L al iniciar sesión en una aplicación de explorador no?**

Actualmente, un usuario puede usar la comprobación de seguridad adicional solo con aplicaciones y servicios que el usuario puede tener acceso a través de un explorador. Aplicaciones de explorador no (también contempladas como *aplicaciones de cliente enriquecido*) que están instaladas en un equipo local, como Windows PowerShell, no funciona con las cuentas que requieren comprobación de seguridad adicional. En este caso, el usuario puede ver la aplicación genera un error 0x800434D4L.

Solución alternativa para esto es que el usuario independientes cuentas para relacionados con el administrador y no sean administradores operaciones. Más adelante, puede vincular buzones entre su cuenta de administrador y la cuenta de administrador no para que puede iniciar sesión en Outlook con su cuenta no sean administradores. Para obtener más información sobre esto, obtenga información sobre cómo [asignar a un administrador la posibilidad de abrir y ver el contenido del buzón de un usuario](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Pasos siguientes

Si no se responde su pregunta aquí, dejarlo en los comentarios en la parte inferior de la página. O bien, estas son algunas opciones adicionales para obtener ayuda:


**P: ¿Cómo puedo obtener ayuda con la autenticación multifactor de Azure?**

- Busque el [soporte técnico de Microsoft Knowledge Base](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) soluciones para problemas técnicos comunes.

- Buscar y examinar las preguntas y respuestas de la Comunidad técnicas o su propia pregunta en los [foros de Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

- Si es un cliente de PhoneFactor heredado y tiene alguna pregunta o necesita ayuda para restablecer una contraseña, utilice el vínculo [de restablecimiento de contraseña](mailto:phonefactorsupport@microsoft.com) para abrir un caso de soporte técnico.

- Póngase en contacto con un profesional de soporte técnico a través de la [compatibilidad con el servidor de autenticación multifactor de Azure (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Al ponerse en contacto con nosotros, resulta útil si puede incluir toda la información sobre el problema como sea posible. Puede proporcionar la información incluye la página donde aparece el error, el código de error específico, el identificador de sesión y el identificador del usuario que ha visto el error.
