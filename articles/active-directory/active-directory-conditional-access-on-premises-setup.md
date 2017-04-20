<properties
    pageTitle="Configurar el acceso condicional locales con Azure Active Directory dispositivo registro | Microsoft Azure"
    description="Guía paso a paso para habilitar el acceso condicional a aplicaciones locales mediante el servicio de federación de Active Directory (AD FS) en Windows Server 2012 R2."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>


# <a name="setting-up-on-premises-conditional-access-using-azure-active-directory-device-registration"></a>Configurar el acceso condicional locales con Azure Active Directory dispositivo de registro

Personalmente dispositivos propietario de los usuarios pueden marcarse sabe que su organización al requerir que los usuarios en lugar de trabajo combinación sus dispositivos para el servicio de registro de dispositivo de Azure Active Directory. A continuación se muestra una guía paso a paso para habilitar el acceso condicional a aplicaciones locales mediante el servicio de federación de Active Directory (AD FS) en Windows Server 2012 R2.

> [AZURE.NOTE]
> Licencia de Azure AD Premium o Office 365 se requiere cuando usa dispositivos registrado en directivas de acceso condicional del servicio de registro de dispositivo de Azure Active Directory. Esto incluye las directivas de servicios de federación de Active Directory (AD FS) aplicadas a los recursos locales.

Para obtener más información sobre los escenarios de acceso condicional para local, consulte [unirse al área de trabajo desde cualquier dispositivo para SSO y fluido segundo Factor de autenticación a través de aplicaciones de la empresa](https://technet.microsoft.com/library/dn280945.aspx).

Estas funciones están disponibles para los clientes que comprar una licencia de Azure Active Directory Premium.

<a name="supported-devices"></a>Dispositivos compatibles
-------------------------------------------------------------------------
* Dispositivos de pertenencia a un dominio de Windows 7.
* Windows 8.1 personal y el dominio unido dispositivos.
* iOS 6 y posteriores, para el explorador Safari
* Android 4.0 o posterior, Samsung GS3 o por encima de los teléfonos, Samsung Note2 o por encima de la tableta.


<a name="scenario-prerequisites"></a>Requisitos previos de escenario
------------------------------------------------------------------------
* Suscripción a Office 365 o Premium de Azure Active Directory
* Un inquilino de Azure Active Directory
* Active Directory de Windows Server (Windows Server 2008 o posterior)
* Esquema actualizado en Windows Server 2012 R2
* Licencia de Azure Active Directory Premium
* Windows Server 2012 R2 servicios de federación, configurado para SSO de Azure AD
* Windows Server 2012 R2 Web Application Proxy Microsoft Azure Active Directory conectar (Azure AD Connect). [Descargar Azure AD Connect aquí](http://www.microsoft.com/en-us/download/details.aspx?id=47594).
* Dominio comprobado.

<a name="known-issues-in-this-release"></a>Problemas conocidos en esta versión
-------------------------------------------------------------------------------
* Las directivas de acceso condicional de dispositivo basado requieren dispositivo objeto reescritura a Active Directory de Azure Active Directory. Puede tardar hasta 3 horas para objetos de dispositivo sean atrás escrito a Active Directory
* dispositivos iOS 7 siempre solicitará al usuario que seleccione un certificado durante la autenticación de certificado de cliente.
* Algunas versiones de iOS8, antes de iOS 8.3 no funcionan.

## <a name="scenario-assumptions"></a>Suposiciones de escenario
Este escenario se supone que tiene un entorno híbrido formada por un inquilino de Azure AD y un Active Directory local. Estos inquilinos deben estar conectados con Azure AD Connect y con un dominio comprobado y AD FS para SSO. La lista de comprobación siguiente le ayudará a configurar su entorno de la fase descrita anteriormente.

<a name="checklist-prerequisites-for-conditional-access-scenario"></a>Lista de comprobación: Requisitos previos de escenario de acceso condicional
--------------------------------------------------------------
Conecte al inquilino de Azure AD con Active Directory local.

## <a name="configure-azure-active-directory-device-registration-service"></a>Configurar el servicio de registro de dispositivo de Azure Active Directory
Use esta guía para implementar y configurar el servicio de registro de Azure Active Directory dispositivo para su organización.

Esta guía se supone que se ha configurado Active Directory de Windows Server y se ha suscrito a Microsoft Azure Active Directory. Consulte los requisitos previos anteriores.

Para implementar el servicio de registro de Azure Active Directory dispositivo con el inquilino de Azure Active Directory, realice las tareas en la siguiente lista de comprobación en orden. Cuando un vínculo de referencia le lleva a un tema conceptual, vuelva a esta lista de comprobación después de revisar el tema conceptual para que puede continuar con las tareas restantes de esta lista de comprobación. Algunas tareas incluirá un paso de validación de escenario que puede ayudarle a confirmar que el paso se completó correctamente.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>Parte 1: Activar registro de dispositivo de Azure Active Directory

Siga la lista de comprobación para habilitar y configurar el servicio de registro del dispositivo de Azure Active Directory.

| Tarea                                                                                                                                                                                                                                                                                                                                                                                             | Referencia                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Habilitar el registro de dispositivo en el inquilino de Azure Active Directory para permitir que los dispositivos para combinar el área de trabajo. De forma predeterminada, la autenticación multifactor no está habilitada para el servicio. Sin embargo, se recomienda autenticación multifactor al registrar un dispositivo. Antes de habilitar la autenticación multifactor en ADRS, asegúrese de que AD FS está configurado para un proveedor de autenticación multifactor. | [Activar registro de dispositivo de Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)               |
| Dispositivos descubrir el servicio de registro del dispositivo de Azure Active Directory buscando conocidos registros DNS. Configure su compañía DNS para que los dispositivos puedan detectar el servicio de registro del dispositivo de Azure Active Directory.                                                                                                                                                   | [Configurar la detección de registro de dispositivo de Azure Active Directory.](active-directory-conditional-access-device-registration-overview.md) |

##<a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>Parte 2: Implementar y configurar Windows Server 2012 R2 Active Directory Federation Services y configurar una relación de la federación con Azure AD

| Tarea                                                                                                                                                                                                                                                                                                                                                                                             | Referencia                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Implementar el dominio de servicios de dominio de Active Directory con las extensiones de esquema de Windows Server 2012 R2. No es necesario actualizar alguno de los controladores de dominio a Windows Server 2012 R2. La actualización de esquema es el único requisito. | [Actualizar el esquema de servicios de dominio de Active Directory](#upgrade-your-active-directory-domain-services-schema)               |
| Dispositivos descubrir el servicio de registro del dispositivo de Azure Active Directory buscando conocidos registros DNS. Configure su compañía DNS para que los dispositivos puedan detectar el servicio de registro del dispositivo de Azure Active Directory.                                                                                                                                                   | [Preparar los dispositivos de soporte técnico de Active Directory](#prepare-your-active-directory-to-support-devices) |


##<a name="part-3-enable-device-writeback-in-azure-ad"></a>Parte 3: Habilitar dispositivo reescritura en Azure AD

| Tarea                                                                                                                                                                                                                                                                                                                                                                                             | Referencia                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Completar la parte 2 de habilitar reescritura de dispositivo en Azure AD Connect. Al finalizar, devolver esto en esta guía. | [Habilitar reescritura de dispositivo en Azure AD Connect](#upgrade-your-active-directory-domain-services-schema)               |


##<a name="optional-part-4-enable-multi-factor-authentication"></a>[Opcional] Parte 4: Habilitar la autenticación multifactor

Se recomienda que configure una de las diversas opciones para la autenticación multifactor. Si desea requerir AMF, vea [Elegir la solución de seguridad de varios factores para usted](../multi-factor-authentication/multi-factor-authentication-get-started.md). Incluye una descripción de cada solución y vínculos para ayudarle a configurar la solución de su elección.

## <a name="part-5-verification"></a>Parte 5: verificación

La implementación está ahora completa. Ahora puede probar algunos escenarios. Siga los vínculos siguientes para probar el servicio y familiarizarse con las características


| Tarea                                                                                                                                                                                                                         | Referencia                                                                       |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| Unirse a algunos dispositivos al área de trabajo con el registro de dispositivo de Azure Active Directory. Puede unirse a, Windows, dispositivos iOS y Android                                                                                         | [Unirse a dispositivos para el área de trabajo con el registro de dispositivo de Azure Active Directory](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| Puede ver y habilitar o deshabilitar dispositivos registrados con el Portal de administrador. En esta tarea verá algunos dispositivos registrados con el Portal de administrador.                                                        | [Información general sobre el registro de dispositivos de Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)                             |
| Compruebe que los objetos del dispositivo se escriben de Azure Active Directory en Active Directory de Windows Server.                                                                                                                  | [Compruebe los dispositivos registrados son atrás escrito a Active Directory](#verify-registered-devices-are-written-back-to-active-directory)                  |
| Ahora que los usuarios pueden registrar sus dispositivos, puede crear la aplicación de directivas de acceso en AD FS que permitir que solo los dispositivos registrados. En esta tarea que va a crear una regla de acceso de la aplicación y una personalizada acceso denegado mensaje | [Crear una directiva de acceso de la aplicación y personalizado mensaje de acceso denegado](#create-an-application-access-policy-and-custom-access-denied-message)            |



## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Intégrelo Azure Active Directory local de Active Directory
Esto le ayudará a integrar al inquilino de Azure AD con su en active directory local, con Azure AD Connect. Aunque los pasos están disponibles en el portal de clásico Azure, tome nota de las instrucciones especiales enumerados en esta sección.

1.  Inicie sesión en el portal de clásico Azure con una cuenta que es un administrador Global en Azure AD.
2.  En el panel izquierdo, seleccione **Active Directory**.
3.  En la ficha **directorio** , seleccione el directorio.
4.  Seleccione la pestaña de la **Integración de directorios** .
5.  En la sección **implementar y administrar** , siga los pasos 1 a 3 para integrar Azure Active Directory con el directorio local.
  1.    Agregar dominios.
  2.    Instalar y ejecutar Azure AD Connect: instalar Azure AD Connect mediante las siguientes instrucciones, [instalación personalizada de Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md).
  3. Comprobar y administrar la sincronización de directorios. Instrucciones de inicio de sesión único están disponibles en este paso.

  > [AZURE.NOTE]
  > Configurar la federación con AD FS tal como se describe en el documento vinculado anteriormente. No necesita configurar cualquiera de las características de vista previa.


## <a name="upgrade-your-active-directory-domain-services-schema"></a>Actualizar su esquema de los servicios de dominio de Active Directory

> [AZURE.NOTE]
> Actualizar el esquema de Active Directory no se pueden invertir. Se recomienda que primero realice esto en un entorno de prueba.

1. Inicie sesión en el controlador de dominio con una cuenta que tenga derechos de administrador de empresa y administración de esquema.
2. Copie el directorio de **\support\adprep [media]** y subdirectorios a uno de los controladores de dominio de Active Directory.
3. Donde [media] es la ruta de acceso al medio de instalación de Windows Server 2012 R2.
4. Desde el símbolo del sistema, vaya al directorio adprep y ejecutar: **adprep.exe /forestprep**. Siga las instrucciones en pantalla para completar la actualización del esquema.

## <a name="prepare-your-active-directory-to-support-devices"></a>Preparar Active Directory para admitir dispositivos

>[AZURE.NOTE] Se trata de una operación de una sola vez que se debe ejecutar para preparar el bosque de Active Directory para admitir dispositivos. Debe estar registrado con permisos de administrador de empresa y el bosque de Active Directory debe tener el esquema de Windows Server 2012 R2 para completar este procedimiento.


##<a name="prepare-your-active-directory-forest-to-support-devices"></a>Preparar el bosque de Active Directory para admitir dispositivos

> [AZURE.NOTE]
>Se trata de una operación de una sola vez que se debe ejecutar para preparar el bosque de Active Directory para admitir dispositivos. Debe estar registrado con permisos de administrador de empresa y el bosque de Active Directory debe tener el esquema de Windows Server 2012 R2 para completar este procedimiento.

### <a name="prepare-your-active-directory-forest"></a>Preparación del bosque de Active Directory

1.  En el servidor de federación, abra una ventana de comandos de Windows PowerShell y escribir: inicialización ADDeviceRegistration
2.  Cuando se le pida **ServiceAccountName**, escriba el nombre de la cuenta de servicio seleccionado como la cuenta de servicio de AD FS. Si se trata de una cuenta de gMSA, especifique la cuenta en el formato **dominio\nombreDeCuenta$** . Para una cuenta de dominio, use el formato **dominio\nombreDeCuenta**.



### <a name="enable-device-authentication-in-ad-fs"></a>Habilitar la autenticación de dispositivo de AD FS

1. En el servidor de federación, abra la consola de administración de AD FS y vaya a **AD FS** > **Directivas de autenticación**.
2. Seleccione **Editar Global autenticación principal...** en el panel de **acciones** .
3. Active **Habilitar la autenticación del dispositivo** y, a continuación, seleccione**Aceptar**.
4. De forma predeterminada, AD FS periódicamente quitará sin usar dispositivos de Active Directory. Debe deshabilitar esta tarea cuando se usa el registro de dispositivo de Azure Active Directory para que los dispositivos pueden administrarse en Azure.


### <a name="disable-unused-device-cleanup"></a>Deshabilitar el Liberador de espacio en un dispositivo sin usar
1. En el servidor de federación, abra una ventana de comandos de Windows PowerShell y escribir: Set-AdfsDeviceRegistration - MaximumInactiveDays 0

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Preparar Azure AD Connect para reescritura de dispositivo

1.  Completar la parte 1: Preparar Azure AD conectarse.


## <a name="join-devices-to-your-workplace-using-azure-active-directory-device-registration"></a>Unirse a dispositivos para el área de trabajo con el registro de dispositivo de Azure Active Directory

### <a name="join-an-ios-device-using-azure-active-directory-device-registration"></a>Unirse a un dispositivo iOS con el registro de dispositivo de Azure Active Directory

Registro Azure de Active Directory dispositivo usa el proceso de inscripción Over-the-Air perfil para dispositivos iOS. Este proceso comienza con el usuario que se conecta a la dirección URL de inscripción de perfil con el explorador web Safari. El formato de dirección URL es la siguiente:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Donde `yourdomainname` es el nombre de dominio que ha configurado con Azure Active Directory. Por ejemplo, si el nombre de dominio es contoso.com, la dirección URL sería:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Existen varias formas diferentes para comunicarse esta dirección URL para los usuarios. Una forma recomendada es publicar esta dirección URL en una aplicación personalizada de acceso denegado mensaje en AD FS. Se trata en la sección próxima: [crear una directiva de acceso de la aplicación y personalizado mensaje acceso denegado](#create-an-application-access-policy-and-custom-access-denied-message).

###<a name="join-a-windows-81-device-using-azure-active-directory-device-registration"></a>Unirse a un dispositivo de Windows 8.1 con registro de dispositivo de Azure Active Directory

1. En el dispositivo Windows 8.1, vaya a **Configuración de PC** > **red** > **lugar de trabajo**.
2. Escriba su nombre de usuario en formato UPN. Por ejemplo, dan@contoso.com..
3. Seleccione **unirse**.
4. Cuando se le solicite, iniciar sesión con sus credenciales. Ahora se une el dispositivo.

###<a name="join-a-windows-7-device-using-azure-active-directory-device-registration"></a>Unirse a un dispositivo de Windows 7 con registro de dispositivo de Azure Active Directory
Para registrar Windows 7 dominio unido dispositivos que necesita para implementar el paquete de software de registro de dispositivo. El paquete de software se denomina unirse de área de trabajo para Windows 7 y está disponible para su descarga en el [sitio Web de Microsoft Connect](https://connect.microsoft.com/site1164). Instrucciones de cómo usar el paquete están disponibles en el [registro de dispositivo automática de configurar el dominio de Windows 7 unido dispositivos](active-directory-conditional-access-automatic-device-registration-windows7.md).

### <a name="join-an-android-device-using-azure-active-directory-device-registration"></a>Unirse a un dispositivo Android con el registro de dispositivo de Azure Active Directory

El [Autenticador de Azure para Android tema](active-directory-conditional-access-azure-authenticator-app.md) contiene instrucciones sobre cómo instalar la aplicación de Azure autenticador en su dispositivo Android y agregar una cuenta de trabajo. Cuando se crea correctamente una cuenta de trabajo en un dispositivo Android, que el dispositivo esté unido a la organización del área de trabajo.

## <a name="verify-registered-devices-are-written-back-to-active-directory"></a>Compruebe los dispositivos registrados son atrás escrito a Active Directory
Puede ver y compruebe que los objetos de dispositivo se hayan escrito atrás en Active Directory con LDP.exe o Adsiedit. Ambos están disponibles con las herramientas de administradores de Active Directory.

De forma predeterminada, los objetos de dispositivo que están escritas en espera de Azure Active Directory se colocarán en el mismo dominio que su granja de AD FS.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Crear una directiva de acceso de la aplicación y personalizado mensaje de acceso denegado
Considere el siguiente escenario: crear una aplicación de confianza de fiesta Relying en AD FS y configurar una regla de autorización de emisión que permite solo los dispositivos registrados. Ahora sólo los dispositivos que están registrados pueden tener acceso a la aplicación. Para facilitar a los usuarios obtener acceso a la aplicación, configure un personalizado mensaje de acceso denegado que incluye instrucciones sobre cómo unirse a su dispositivo. Ahora los usuarios tienen una manera perfecta para registrar sus dispositivos para poder acceder a una aplicación.

Los siguientes pasos le mostrará cómo implementar este escenario.

>[AZURE.NOTE]
Esta sección se supone que ya ha configurado una fiesta confiar confianza para su aplicación de AD FS.

1. Abra la herramienta MMC de AD FS y navegue hasta AD FS > relaciones de confianza > confiar confía de terceros.
2. Busque la aplicación para la que se aplicará esta nueva regla de acceso. Haga clic en la aplicación y seleccione Editar reglas de notificación...
3. Seleccione la pestaña **Reglas de autorización de emisión** y, a continuación, seleccione **Agregar regla...**
4. En la **regla de la reclamación** plantilla lista desplegable, seleccione **Permitir o denegar los usuarios en función de una notificación entrante**. Seleccione **siguiente**.
5. En el nombre de la regla de notificación: tipo de campo: **Permitir el acceso desde dispositivos registrados**
6. Tipo de notificación de la entrada: lista desplegable, seleccione **Es usuario registrado**.
7. En la entrada de valor de notificación:, escriba: **true**
8. Seleccione el botón de opción **Permitir el acceso a los usuarios con esta notificación entrante** .
9. Seleccione **Finalizar** y, a continuación, seleccione **Aplicar**.
10. Quitar todas las reglas que son más permisivas que la regla que acaba de crear. Por ejemplo, quitar la regla de **Permitir acceso a todos los usuarios** de forma predeterminada.

Ahora, la aplicación está configurada para permitir el acceso únicamente cuando el usuario procede de un dispositivo que haya registrado y unido al lugar de trabajo. Para directivas de acceso más avanzado, vea [Administrar el riesgo con Control de acceso de varios factores](https://technet.microsoft.com/library/dn280949.aspx).

A continuación, configurará un mensaje de error personalizado para la aplicación. El mensaje de error que los usuarios sepan que debe unirse a su dispositivo en el área de trabajo antes de que se permiten el acceso a la aplicación. Puede crear un aplicación personalizada mensaje acceso denegado con Windows PowerShell y HTML personalizados.

En el servidor de federación, abra una ventana de comandos de Windows PowerShell y escriba el siguiente comando. Reemplazar partes del comando con los elementos que son específicos de su sistema:

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Registre el dispositivo antes de que puede tener acceso a esta aplicación.

**Si está utilizando un dispositivo iOS, seleccione este vínculo para unirse a su dispositivo**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Unirse a este dispositivo iOS al área de trabajo.


**Si está usando un dispositivo de Windows 8.1**, puede unirse a su dispositivo, vaya a **Configuración de PC ** >  **red** > **lugar de trabajo**.


Donde "**Confiar nombre de confianza de fiesta**" es el nombre del objeto de confianza de fiesta confiar aplicaciones en AD FS.
Donde **sudominio.com** es el nombre de dominio que se ha configurado con Azure Active Directory. Por ejemplo, contoso.com.
Asegúrese de quitar todos los saltos de línea (si existe) de contenido html que se pasa al cmdlet **Set-AdfsRelyingPartyWebContent** .


Ahora, cuando los usuarios acceso a la aplicación desde un dispositivo que no está registrado con el servicio de registro del dispositivo de Azure Active Directory, recibirán una página que tiene un aspecto similar a la siguiente captura de pantalla.

![Screeshot de un error cuando los usuarios no ha registrado su dispositivo con Azure AD](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)

##<a name="related-articles"></a>Artículos relacionados

- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
