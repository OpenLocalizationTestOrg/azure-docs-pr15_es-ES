<properties 
    pageTitle="Introducción al servidor de autenticación multifactor de Azure"
    description="Esta es la página de autenticación multifactor de Azure que describe cómo empezar a trabajar con el servidor de Azure AMF."
    services="multi-factor-authentication"
    keywords="autenticación, azure entre varios factores autenticación aplicación activación página del servidor, descargar del servidor de autenticación"
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

# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Introducción al servidor de autenticación multifactor de Azure




<center>![Nube](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Ahora que hemos determinado si usar local con autenticación multifactor, vamos a Guía de inicio. Esta página cubre una instalación nueva del servidor y obtener el programa de instalación con local de Active Directory. Si ya tiene instalado el servidor de PhoneFactor y desea actualizar, consulte [Actualizar al servidor de Azure multifactor](multi-factor-authentication-get-started-server-upgrade.md) o si desea obtener información sobre cómo instalar el servicio web vea [implementar Azure multifactor autenticación de servidor Mobile aplicación de servicio Web](multi-factor-authentication-get-started-server-webservice.md).


## <a name="download-the-azure-multi-factor-authentication-server"></a>Descargar el servidor de Azure autenticación multifactor



Hay dos formas diferentes que puede descargar el servidor de autenticación multifactor de Azure. Ambos se realizan a través del portal de Azure. La primera es administrar el proveedor de servicios de autenticación multifactor directamente. La segunda es a través de la configuración del servicio. La segunda opción requiere un proveedor de autenticación multifactor o una licencia de Azure AMF, Azure AD Premium o serie de movilidad para empresas.


### <a name="to-download-the-azure-multi-factor-authentication-server-from-the-azure-portal"></a>Para descargar el servidor de autenticación multifactor de Azure desde el portal de Azure
--------------------------------------------------------------------------------

1. Inicie sesión en el Portal de Azure como administrador.
2. En la parte izquierda, seleccione Active Directory.
3. En la página de Active Directory, en la parte superior haga clic en **Proveedores de autenticación multifactor**
4. En la parte inferior, haga clic en **Administrar**
5. Se abrirá una nueva página.  Haga clic en **descargas.** 
 ![Descargar](./media/multi-factor-authentication-sdk/download.png)
6. Encima **Generar credenciales de activación**, haga clic en **descargar.** 
 ![Descargar](./media/multi-factor-authentication-get-started-server/download4.png)
7. Guarde la descarga.



### <a name="to-download-the-azure-multi-factor-authentication-server-via-the-service-settings"></a>Para descargar el servidor de autenticación multifactor de Azure a través de la configuración del servicio


1. Inicie sesión en el Portal de Azure como administrador.
2. En la parte izquierda, seleccione Active Directory.
3. Haga doble clic en la instancia de Azure AD.
4. En la parte superior, haga clic en **Configurar**
![descargar](./media/multi-factor-authentication-sdk/download2.png)
5. En la autenticación multifactor, seleccione **Administrar la configuración de servicio**
6. En la página de configuración de servicios en la parte inferior de la pantalla, haga clic en **Ir al portal**.
![Descargar](./media/multi-factor-authentication-get-started-server/servicesettings.png)
7. Se abrirá una nueva página. Haga clic en **descargas.**
8. Encima **Generar credenciales de activación**, haga clic en **descargar.**
9. Guarde la descarga.




## <a name="install-and-configure-the-azure-multi-factor-authentication-server"></a>Instalar y configurar el servidor de Azure autenticación multifactor
Ahora que ha descargado del servidor que puede instalar y configurar.  Asegúrese de que el servidor que va a instalar cumple los siguientes requisitos:



Requisitos del servidor de Azure autenticación multifactor|Descripción|
:------------- | :------------- |
Hardware|<li>200 MB de espacio en disco duro</li><li>procesador compatible con x32 o x64</li><li>1 GB o más de RAM</li>
Software|<li>Windows Server 2008 o mayor si el host es un servidor de sistema operativo</li><li>Windows 7 o posterior si el host es un sistema operativo de cliente</li><li>4.0 de Microsoft .NET Framework</li><li>SDK de IIS 7.0 o posterior si instalar el servicio web o portal de usuario</li>

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Requisitos del firewall de servidor de autenticación multifactor Azure
--------------------------------------------------------------------------------
Cada servidor AMF puedan comunicarse en el puerto 443 de salida a la siguiente:

- https://PFD.phonefactor.NET
- https://pfd2.phonefactor.NET
- https://CSS.phonefactor.NET

Si se restringe el acceso de los firewalls de salida en el puerto 443, necesitará los siguientes intervalos de direcciones IP que se abran:

Subred IP|Máscara de red|Intervalo de IP
:------------- | :------------- | :------------- |
134.170.116.0/25|255.255.255.128.|134.170.116.1: 134.170.116.126
134.170.165.0/25|255.255.255.128.|134.170.165.1: 134.170.165.126
70.37.154.128/25|255.255.255.128.|70.37.154.129: 70.37.154.254

Si no usa características de confirmación de eventos de autenticación de varios factores de Azure y los usuarios no están autenticar con las aplicaciones móviles de autenticación multifactor de dispositivos en la red corporativa, la dirección IP rangos se pueden reducir a la siguiente:


Subred IP|Máscara de red|Intervalo de IP
:------------- | :------------- | :------------- |
134.170.116.72/29|255.255.255.248|134.170.116.72: 134.170.116.79
134.170.165.72/29|255.255.255.248|134.170.165.72: 134.170.165.79
70.37.154.200/29|255.255.255.248|70.37.154.201: 70.37.154.206


### <a name="to-install-and-configure-the-azure-multi-factor-authentication-server"></a>Instalar y configurar el servidor de autenticación multifactor de Azure
--------------------------------------------------------------------------------


1. Haga doble clic en el archivo ejecutable. Se iniciará la instalación.
2. En la pantalla Seleccionar carpeta de instalación, asegúrese de que la carpeta es correcta y haga clic en siguiente.
3. Una vez completada la instalación, haga clic en Finalizar.  Se iniciará al Asistente para configuración.
4. En el Asistente para configuración de pantalla de bienvenida, active **Omitir mediante el Asistente para configuración de autenticación** y haga clic en **siguiente**.  Esto se cierre al asistente e inicie el servidor.
    ![Nube](./media/multi-factor-authentication-get-started-server/skip2.png)
5. En la página que se descarga desde el servidor de, haga clic en el botón **Generar credenciales de activación** .  Copie esta información en el servidor de Azure AMF en los cuadros correspondientes y haga clic en **Activar**.


Los pasos anteriores muestran una instalación express con el Asistente para configuración.  Volver a puede ejecutar al Asistente para la autenticación, selecciónelo en el menú Herramientas en el servidor.



##<a name="import-users-from-active-directory"></a>Importar usuarios de Active Directory

Ahora que el servidor está instalado y configurado puede importar rápidamente los usuarios en el servidor de Azure AMF.

### <a name="to-import-users-from-active-directory"></a>Para importar usuarios de Active Directory
--------------------------------------------------------------------------------


1. En el servidor de AMF Azure, en la parte izquierda, seleccione **los usuarios**.
2. En la parte inferior, seleccione **Importar desde Active Directory**.
3. Ahora puede buscar usuarios individuales o buscar en el directorio de AD para las unidades organizativas con usuarios en ellos.  En este caso, se especificará los usuarios OU.
4. Resalte todos los usuarios a la derecha y haga clic en **Importar**.  Debe aparecer una ventana emergente que indica que está correctamente.  Cierre la ventana Importar.

![Nube](./media/multi-factor-authentication-get-started-server/import2.png)

## <a name="send-users-an-email"></a>Enviar un correo electrónico a los usuarios
Ahora que haya importado los usuarios en el servidor de autenticación multifactor de Azure, se recomienda que enviar a los usuarios un correo electrónico que les informa de que ha inscrito en autenticación multifactor.

Con el servidor de autenticación multifactor de Azure hay varias maneras de configurar los usuarios para usar la autenticación multifactor.  Por ejemplo, si conoce los números de teléfono de los usuarios o pudieron importar los números de teléfono en el servidor de autenticación multifactor de Azure desde el directorio de la compañía, el correo electrónico le permitirá usuarios saber que se han configurado para usar autenticación multifactor de Azure, proporcionan instrucciones sobre el uso de la autenticación multifactor de Azure e informar al usuario del número de teléfono que recibirán sus autenticaciones.  

El contenido del correo electrónico varían según el método de autenticación que se ha definido para el usuario (por ejemplo, llamada de teléfono, SMS, la aplicación móvil).  Por ejemplo, si el usuario debe usar un PIN cuando se autentican, el correo electrónico le indicará ellos su PIN inicial que se estableció en.  Normalmente los usuarios deben cambiar su PIN durante su primera autenticación.

Si los números de teléfono de los usuarios no se han configurado o importados en el servidor de autenticación multifactor de Azure o los usuarios están preconfigurados para utilizar la aplicación móvil para la autenticación, puede enviarles un correo electrónico que les permite saber que se han configurado para usar la autenticación multifactor de Azure y dirigirá para completar la inscripción de su cuenta a través del Portal de usuario de autenticación de varios factores de Azure.  Un hipervínculo se incluirán que el usuario hace clic en para acceder al Portal de usuario. Cuando el usuario hace clic en el hipervínculo, el explorador web abrirá y llevarlos al Portal de usuario de autenticación multifactor de su empresa Azure.   


### <a name="configuring-email-and-email-templates"></a>Configurar correo electrónico y plantillas de correo electrónico

Puede configurar la configuración para enviar estos mensajes de correo electrónico haciendo clic en el icono de correo electrónico a la izquierda.  Aquí es donde puede especificar la información de SMTP de su servidor de correo le permite enviar un correo amplia global agregando una comprobación al enviar mensajes a la casilla de verificación de los usuarios.

![Configuración del correo electrónico](./media/multi-factor-authentication-get-started-server/email1.png)

En la pestaña contenido de correo electrónico, verá todas las distintas plantillas de correo electrónico que están disponibles para elegir.  Dependiendo de cómo haya configurado los usuarios para usar la autenticación multifactor, que puede elegir la plantilla que mejor se adapte a usted.

![Plantillas de correo electrónico](./media/multi-factor-authentication-get-started-server/email2.png)

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Cómo administra el servidor de autenticación multifactor de Azure los datos de usuario

Cuando utiliza la autenticación multifactor (AMF) servidor local, los datos de un usuario se almacenan en los servidores locales. No hay datos de usuario persistentes se almacenan en la nube. Cuando el usuario realiza una autenticación de dos factores, el servidor AMF envía datos al servicio de nube Azure AMF para realizar la autenticación. Cuando se envían estas peticiones de autenticación del servicio de nube, se envían los siguientes campos en la solicitud y los registros para que estén disponibles en los informes de autenticación y uso del cliente. Algunos de los campos son opcionales para habilitar o deshabilitar en el servidor de autenticación multifactor. La comunicación desde el servidor AMF al servicio de nube AMF utiliza SSL/TLS sobre el puerto 443 saliente. Estos campos son:

- Identificador exclusivo: nombre de usuario o identificador de servidor interno AMF
- Primer y último nombre - opcional
- Dirección de correo electrónico - opcional
- Número de teléfono: cuando se realiza una llamada de voz o la autenticación de SMS
- Símbolo de dispositivo - cuando se realiza la autenticación de la aplicación móvil
- Modo de autenticación
- Resultado de la autenticación
- Nombre del servidor MFA
- Servidor AMF IP
- Cliente IP: si está disponible



Además de los campos anteriores, el resultado de autenticación (éxito o denegación) y la razón para cualquier negaciones también es almacenados con los datos de autenticación y disponible a través de los informes de autenticación y uso.


## <a name="advanced-azure-multi-factor-authentication-server-configurations"></a>Configuraciones de servidor de Azure autenticación multifactor avanzadas
Para obtener más información sobre la configuración avanzada y la información de configuración, use la tabla siguiente.

Método|Descripción
:------------- | :------------- |
[Portal de usuario](multi-factor-authentication-get-started-portal.md)|  Información sobre la instalación y configuración del portal de usuario, incluida la implementación y sin intervención del usuario.
[Servicios de federación de Active Directory](multi-factor-authentication-get-started-adfs.md)|Información sobre cómo configurar la autenticación multifactor de Azure con AD FS.
[Autenticación RADIUS](multi-factor-authentication-get-started-server-radius.md)|  Información sobre la instalación y configuración del servidor de AMF de Azure con RADIUS.
[Autenticación de IIS](multi-factor-authentication-get-started-server-iis.md)|Información sobre la instalación y configuración del servidor de AMF de Azure con IIS.
[Autenticación de Windows](multi-factor-authentication-get-started-server-windows.md)|  Información sobre la instalación y configuración del servidor de AMF de Azure con autenticación de Windows.
[Autenticación LDAP](multi-factor-authentication-get-started-server-ldap.md)|Información sobre la instalación y configuración del servidor de AMF de Azure con autenticación LDAP.
[Remoto puerta de enlace de escritorio y servidor de autenticación multifactor de Azure con RADIUS](multi-factor-authentication-get-started-server-rdg.md)|  Información sobre la instalación y configuración del servidor de AMF de Azure con la puerta de enlace de escritorio remoto con RADIUS.
[Sincronización con Active Directory de Windows Server](multi-factor-authentication-get-started-server-dirint.md)|Información sobre la instalación y configuración de sincronización entre Active Directory y el servidor de Azure AMF.
[Implementar el servicio Web aplicación móvil de servidor de Azure autenticación multifactor](multi-factor-authentication-get-started-server-webservice.md)|Información sobre la instalación y configuración del servicio web de servidor de Azure AMF.
