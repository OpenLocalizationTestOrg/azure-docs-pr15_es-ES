<properties
    pageTitle="Habilitar Proxy de la aplicación de Azure AD | Microsoft Azure"
    description="Activar Proxy de aplicación en el portal de clásico Azure e instalar los conectores para el servidor proxy inverso."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="kgremban"/>

# <a name="enable-application-proxy-in-the-azure-portal"></a>Habilitar a Proxy de aplicación en el portal de Azure

En este artículo le guiará por los pasos para habilitar a Proxy de aplicación de Microsoft Azure AD para el directorio de la nube en Azure AD.

Si no está familiarizado con Proxy de la aplicación puede ayudarle a hacer, obtenga más información sobre [cómo proporcionar acceso remoto seguro a aplicaciones locales](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Requisitos previos de Proxy de aplicación
Para poder habilitar y usar los servicios de Proxy de la aplicación, debe tener:

- Una [suscripción a Microsoft Azure AD basic o premium](active-directory-editions.md) y un directorio de Azure AD que es un administrador global.
- Un servidor que ejecuta Windows Server 2012 R2 o Windows 8.1 o superior, en el que puede instalar el conector de Proxy de aplicación. El servidor envía solicitudes a los servicios de Proxy de la aplicación en la nube y tiene una conexión HTTP o HTTPS para las aplicaciones que se están publicando.

    - Para el inicio de sesión único para las aplicaciones publicadas, este equipo debe dominio-combinarse en el mismo dominio de AD que las aplicaciones que se están publicando.

- Si hay un firewall en la ruta de acceso, asegúrese de que está abierto para que el conector puede realizar solicitudes HTTPS (TCP) en el Proxy de aplicación. El conector utiliza estos puertos junto con subdominios que forman parte de los dominios de alto nivel msappproxy.net y servicebus.windows.net. Asegúrese de que abra los puertos siguientes para el tráfico **saliente** :

  	| Número de puerto | Descripción |
  	| --- | --- |
  	| 80 | Habilitar el tráfico saliente de HTTP de validación de seguridad. |
  	| 443 | Habilitar la autenticación de usuario frente a Azure AD (requerido solo para el proceso de registro de conector) |
  	| 10100 – 10120 | Habilitar respuestas LOB HTTP enviadas al servidor proxy |
  	| 9352, 5671 | Habilitar la comunicación entre el conector hacia el servicio de Azure para las solicitudes entrantes. |
  	| 9350 | Opcional, para habilitar el mejor rendimiento para las solicitudes entrantes |
  	| 8080 | Habilitar la secuencia de inicio del conector y la actualización automática de conector |
  	| 9090 | Habilitar el registro de conector (requerido solo para el proceso de registro de conector) |
  	| 9091 | Activar la renovación automática de certificado de confianza de conector |

    Si el firewall exige el tráfico de acuerdo con los usuarios de origen, abra los puertos para el tráfico procedente de servicios de Windows que se ejecutan como un servicio de red. Además, asegúrese de habilitar el puerto 8080 para NT Authority\System.

- Si su organización usa servidores proxy para conectarse a internet, dedique un vistazo a la entrada de blog que [trabajar con los servidores proxy de local existente](https://blogs.technet.microsoft.com/applicationproxyblog/2016/03/07/working-with-existing-on-prem-proxy-servers-configuration-considerations-for-your-connectors/) para obtener más información acerca de cómo configurarlos.

## <a name="step-1-enable-application-proxy-in-azure-ad"></a>Paso 1: Habilitar Proxy de aplicación en Azure AD
1. Inicie sesión como administrador en el [portal de clásico de Azure](https://manage.windowsazure.com/).
2. Vaya a Active Directory y seleccione el directorio en el que desea habilitar a Proxy de la aplicación.

    ![Active Directory - icono](./media/active-directory-application-proxy-enable/ad_icon.png)

3. Seleccione **Configurar** en la página directorio y desplácese hacia abajo hasta **Proxy de la aplicación**.
4. Alternar **Habilitar servicios de Proxy de aplicación para este directorio** **habilitadas**.

    ![Habilitar a Proxy de aplicación](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

5. Seleccione **Descargar ahora**. Se abrirá la **Azure AD aplicación Proxy conector descargar**. Lea y acepte los términos de licencia y haga clic en **Descargar** para guardar el archivo de Windows Installer (.exe) para el conector.

## <a name="step-2-install-and-register-the-connector"></a>Paso 2: Instalar y registrar el conector
1. Ejecute **AADApplicationProxyConnectorInstaller.exe** en el servidor preparado según los requisitos previos.
2. Siga las instrucciones del Asistente para instalar.
3. Durante la instalación, le solicitará que registre el conector con el Proxy de aplicación de su inquilino de Azure AD.

  - Proporcionar sus credenciales de administrador global de Azure AD. El inquilino de administrador global pueden diferir de las credenciales de Microsoft Azure.
  - Asegúrese de que el administrador que registra el conector se encuentra en el mismo directorio donde se ha habilitado el servicio Proxy de aplicación. Por ejemplo, si el dominio del inquilino es contoso.com, el administrador debe ser admin@contoso.com o cualquier otro alias en ese dominio.
  - Si **La configuración de seguridad mejorada de IE** se establece en **** en el servidor donde se instala el conector, la pantalla de registro puede estar bloqueada. Siga las instrucciones en el mensaje de error para permitir el acceso. Asegúrese de que la seguridad mejorada de Internet Explorer está desactivado.
  - Si el registro de conector no se realiza correctamente, vea [Solucionar problemas de Proxy de la aplicación](active-directory-application-proxy-troubleshoot.md).  

4. Cuando finalice la instalación, se agregan dos nuevos servicios al servidor:

    - **Conector de Proxy de aplicación de Microsoft AAD** permite la conectividad
    - **Actualización para Microsoft AAD aplicación Proxy conector** es un servicio de actualización automática, que periódicamente comprueba si hay nuevas versiones del conector y el conector se actualiza según sea necesario.

    ![Servicios de conector de Proxy de aplicación: captura de pantalla](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. Haga clic en **Finalizar** en la ventana de instalación.

Por motivos de alta disponibilidad, debe implementar al menos dos conectores. Para implementar más conectores, repita los pasos 2 y 3, anteriormente. Cada conector debe estar registrado por separado.

Si desea desinstalar el conector, desinstale el servicio de conector y el servicio de actualización. Reinicie el equipo para quitar completamente el servicio.


## <a name="next-steps"></a>Pasos siguientes

Ya está listo para [publicar aplicaciones con Proxy de la aplicación](active-directory-application-proxy-publish.md).

Si tiene aplicaciones que se encuentran en redes independientes o en diferentes ubicaciones, puede usar grupos de conector para organizar los distintos conectores en unidades lógicas. Más información sobre cómo [trabajar con conectores de Proxy de la aplicación](active-directory-application-proxy-connectors.md).
