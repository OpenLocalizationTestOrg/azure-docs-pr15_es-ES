<properties
   pageTitle="Autenticar con el almacén de datos lago con Active Directory | Microsoft Azure"
   description="Obtenga información sobre cómo autenticar con el almacén de datos lago con Active Directory"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/17/2016"
   ms.author="nitinme"/>

# <a name="end-user-authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticación de usuario final con el almacén de datos lago con Azure Active Directory

> [AZURE.SELECTOR]
- [Servicio de servicio de autenticación](data-lake-store-authenticate-using-active-directory.md)
- [Autenticación de usuario final](data-lake-store-end-user-authenticate-using-active-directory.md)


Almacén de lago de datos de Azure usa Azure Active Directory para la autenticación. Antes de crear una aplicación que funciona con lago almacén de datos de Azure o análisis de lago de datos de Azure, primero debe decidir cómo desea autenticar la aplicación con Azure Active Directory (AD Azure). Las dos opciones principales disponibles son:

* Autenticación de usuario final, y 
* Servicio de servicio de autenticación. 

Ambas opciones como resultado de la aplicación que se proporcionan con un símbolo de OAuth 2.0, que se asocia a cada solicitud realizada al almacén de lago de datos de Azure o análisis de lago de datos de Azure.

Las conversaciones de este artículo sobre cómo crear una aplicación web de Azure AD para la autenticación de usuario final. Para obtener instrucciones sobre la configuración de la aplicación de Azure AD para la autenticación de servicio de servicio, vea [servicio de servicio de autenticación con el almacén de datos de lago con Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).
* Su identificador de suscripción. Se puede recuperar desde el Portal de Azure. Por ejemplo, está disponible desde el módulo de la cuenta de almacén de datos lago.

    ![Obtener el identificador de suscripción](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* El nombre de dominio de Azure AD. Se puede recuperar pasando el mouse en la esquina superior derecha del Portal de Azure. En la captura de pantalla a continuación, el nombre de dominio es **contoso.microsoft.com**y el GUID entre corchetes es el identificador del inquilino. 

    ![Obtener el dominio AAD](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

## <a name="end-user-authentication"></a>Autenticación de usuario final

Este es el enfoque recomendado si desea que el usuario inicie sesión en la aplicación a través de Azure AD. La aplicación podrán obtener acceso a recursos de Azure con el mismo nivel de acceso que el usuario final que ha iniciado sesión. El usuario final tendrá que proporcionar sus credenciales periódicamente en orden al mantener el acceso a la aplicación.

El resultado de la necesidad de iniciar sesión en el usuario final es que la aplicación se expresa un token de acceso y un token de actualización. El token de acceso se asocia a cada solicitud realizada al almacén de datos de lago o el análisis de datos lago y es válido para una hora de forma predeterminada. El token de actualización puede utilizarse para obtener un nuevo token de acceso, y es válido hasta dos semanas de forma predeterminada, si utiliza con regularidad. Puede usar dos enfoques diferentes para el inicio de sesión del usuario final.

### <a name="using-the-oauth-20-pop-up"></a>Uso de la ventana emergente de OAuth 2.0

La aplicación puede desencadenar una ventana emergente de autorización OAuth 2.0, en el que el usuario final puede introducir sus credenciales. Esta ventana emergente también funciona con el proceso de autenticación de dos factores de Azure AD (2FA), si es necesario. 

>[AZURE.NOTE] Este método no se admite todavía en la biblioteca de autenticación de Azure AD (ADAL) para Python o Java.

### <a name="directly-passing-in-user-credentials"></a>Pasar directamente en las credenciales de usuario

La aplicación puede proporcionar directamente las credenciales de usuario a Azure AD. Este método solo funciona con cuentas de usuario de organización ID; no es compatible con personal o cuentas de usuario "live ID", incluidos aquellos que terminen en @outlook.com o @live.com. Además, este método no es compatible con cuentas de usuario que requieren autenticación de dos factores de Azure AD (2FA).

### <a name="what-do-i-need-to-use-this-approach"></a>¿Qué debo usar este método?

* Nombre de dominio de Azure AD. Ya aparece en el requisito previo de este artículo.

* Azure AD **aplicación web**

* Identificador de cliente para la aplicación web de Azure AD

* Respuesta URI de la aplicación web de Azure AD

* Establecer delegar permisos

Para obtener instrucciones sobre cómo crear una aplicación web de Azure AD y configúrelo para los requisitos anteriores, vea la sección [crear una aplicación de Active Directory](#create-an-active-directory-application) a continuación. 

## <a name="create-an-active-directory-application"></a>Crear una aplicación de Active Directory

En esta sección se obtenga información sobre cómo crear y configurar una aplicación web de Azure AD para la autenticación de usuario final con lago almacén de datos de Azure con Azure Active Directory.


### <a name="step-1-create-an-azure-active-directory-application"></a>Paso 1: Crear una aplicación de Azure Active Directory

>[AZURE.NOTE] Los pasos siguientes utilizan el Portal de Azure. También puede crear una aplicación de Azure AD usando [PowerShell Azure](../resource-group-authenticate-service-principal.md) o [CLI de Azure](../resource-group-authenticate-service-principal-cli.md).

1. Inicie sesión en su cuenta de Azure a través del [portal clásico](https://manage.windowsazure.com/).

2. Seleccione **Active Directory** en el panel izquierdo.

     ![Seleccione Active Directory](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory.png)
     
3. Seleccione Active Directory que desea usar para crear la nueva aplicación. Si tiene más de un Active Directory, normalmente desea crear la aplicación en el directorio donde se encuentra la suscripción. Solo puede conceder acceso a recursos de la suscripción para las aplicaciones en el mismo directorio que la suscripción.  

     ![Seleccionar directorio](./media/data-lake-store-end-user-authenticate-using-active-directory/active-directory-details.png)
    
    
3. Para ver las aplicaciones en el directorio, haga clic en **aplicaciones**.

     ![ver aplicaciones](./media/data-lake-store-end-user-authenticate-using-active-directory/view-applications.png)

4. Si no ha creado una aplicación en el directorio antes de que verá algo similar a la siguiente imagen. Haga clic en **Agregar una aplicación**

     ![Agregar aplicación](./media/data-lake-store-end-user-authenticate-using-active-directory/create-application.png)

     O bien, haga clic en **Agregar** en el panel inferior.

     ![Agregar](./media/data-lake-store-end-user-authenticate-using-active-directory/add-icon.png)

6. Proporcione un nombre para la aplicación y seleccione el tipo de aplicación que desea crear. En este tutorial, cree una **Aplicación o WEB API de WEB** y haga clic en el botón siguiente.

     ![aplicación de nombre](./media/data-lake-store-end-user-authenticate-using-active-directory/tell-us-about-your-application.png)

7. Rellene las propiedades de la aplicación. Para la **Dirección URL de inicio de sesión**, proporcione la URI a un sitio web que describa la aplicación. No se valida la existencia del sitio web. **URI de ID de aplicación**, proporcione el URI que identifica la aplicación.

     ![propiedades de la aplicación](./media/data-lake-store-end-user-authenticate-using-active-directory/app-properties.png)

    Haga clic en la marca de verificación para completar al asistente y crear la aplicación.

### <a name="step-2-get-client-id-reply-uri-and-set-delegated-permissions"></a>Paso 2: Obtener el identificador de cliente, responder URI y establecer permisos delegados

1. Haga clic en la ficha **Configurar** para configurar la contraseña de la aplicación.

     ![configurar la aplicación](./media/data-lake-store-end-user-authenticate-using-active-directory/application-configure.png)

2. Copie el **identificador de cliente**.
  
     ![identificador de cliente](./media/data-lake-store-end-user-authenticate-using-active-directory/client-id.png)

3. En la sección **Inicio de sesión único** , copie el **URI de respuesta**.

    ![identificador de cliente](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-get-reply-uri.png)

4. En **permisos para otras aplicaciones**, haga clic en **Agregar aplicación**

    ![identificador de cliente](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

5. En el Asistente de **permisos a otras aplicaciones** , seleccione **Lago de datos de Azure** y **Windows** **Azure API de administración de servicio**y haga clic en la marca de verificación.

6. De forma predeterminada los **Permisos de delegado** para los servicios recién agregados se establece en cero. Haga clic en los **Permisos de delegado la** lista desplegable de Azure datos lago y servicio de administración de Windows Azure y seleccione las casillas de verificación disponibles para establecer los valores a 1. El resultado debería tener este aspecto.

     ![identificador de cliente](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)

7. Haga clic en **Guardar**.


## <a name="next-steps"></a>Pasos siguientes

En este artículo se crea una aplicación web de Azure AD y recopila la información que necesita en las aplicaciones cliente que autor mediante .NET SDK, SDK de Java, etcetera. Ahora puede continuar con los siguientes artículos que hablar sobre cómo usar la aplicación web de Azure AD para autenticar primero con lago almacén de datos y, a continuación, realizar otras operaciones en la tienda.

- [Introducción a Azure datos lago tienda con .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Introducción a Azure datos lago tienda mediante el SDK de Java](data-lake-store-get-started-java-sdk.md)
