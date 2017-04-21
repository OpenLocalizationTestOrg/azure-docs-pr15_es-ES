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

# <a name="service-to-serivce-authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticación de servicio para el servicio con el almacén de datos lago con Azure Active Directory

> [AZURE.SELECTOR]
- [Servicio de servicio de autenticación](data-lake-store-authenticate-using-active-directory.md)
- [Autenticación de usuario final](data-lake-store-end-user-authenticate-using-active-directory.md)

Almacén de lago de datos de Azure usa Azure Active Directory para la autenticación. Antes de crear una aplicación que funciona con lago almacén de datos de Azure o análisis de lago de datos de Azure, primero debe decidir cómo desea autenticar la aplicación con Azure Active Directory (AD Azure). Las dos opciones principales disponibles son:

* Autenticación de usuario final, y 
* Servicio de servicio de autenticación. 

Ambas opciones como resultado de la aplicación que se proporcionan con un símbolo de OAuth 2.0, que se asocia a cada solicitud realizada al almacén de lago de datos de Azure o análisis de lago de datos de Azure.

Las conversaciones de este artículo sobre cómo crear una aplicación web de Azure AD para la autenticación de servicio para el servicio. Para obtener instrucciones sobre la configuración de la aplicación de Azure AD para la autenticación de usuario final, vea [autenticación de usuario final con el almacén de datos de lago con Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).


## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).
* Su identificador de suscripción. Se puede recuperar desde el Portal de Azure. Por ejemplo, está disponible desde el módulo de la cuenta de almacén de datos lago.

    ![Obtener el identificador de suscripción](./media/data-lake-store-authenticate-using-active-directory/get-subscription-id.png)

* El nombre de dominio de Azure AD. Se puede recuperar pasando el mouse en la esquina superior derecha del Portal de Azure. En la captura de pantalla a continuación, el nombre de dominio es **contoso.microsoft.com**y el GUID entre corchetes es el identificador del inquilino. 

    ![Obtener el dominio AAD](./media/data-lake-store-authenticate-using-active-directory/get-aad-domain.png)

## <a name="service-to-service-authentication"></a>Servicio de servicio de autenticación

Este es el enfoque recomendado si desea que su aplicación para autenticar automáticamente con Azure AD, sin necesidad de un usuario final proporcionar sus credenciales. La aplicación podrá autenticarse para siempre que sus credenciales son válidas, que puede personalizar para estar en el orden de los años.

### <a name="what-do-i-need-to-use-this-approach"></a>¿Qué debo usar este método?

* Nombre de dominio de Azure AD. Ya aparece en el requisito previo de este artículo.

* Azure AD **aplicación web**.

* Identificador de cliente para la aplicación web de Azure AD.

* Secreto de cliente para la aplicación web de Azure AD.

* Extremo token para la aplicación web de Azure AD.

* Habilitar el acceso de la aplicación web de Azure AD en el el archivo o carpeta del almacén de datos lago o la cuenta de análisis de datos lago que quiere trabajar con.

Para obtener instrucciones sobre cómo crear una aplicación web de Azure AD y configúrelo para los requisitos anteriores, vea la sección [crear una aplicación de Active Directory](#create-an-active-directory-application) a continuación.

>[AZURE.NOTE] De forma predeterminada, la aplicación de Azure AD está configurada para usar el secreto de cliente, puede recuperar desde la aplicación de Azure AD. Sin embargo, si desea que la aplicación de Azure AD para usar un certificado en su lugar, debe crear la aplicación web de Azure AD con PowerShell de Azure, como se describe en [crear un servicio principal con certificado](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).

## <a name="create-an-active-directory-application"></a>Crear una aplicación de Active Directory

En esta sección se obtenga información sobre cómo crear y configurar una aplicación web de Azure AD autenticación de servicio para el servicio de almacén de lago de datos de Azure con Azure Active Directory. 


### <a name="step-1-create-an-azure-active-directory-application"></a>Paso 1: Crear una aplicación de Azure Active Directory

>[AZURE.NOTE] Los pasos siguientes utilizan el Portal de Azure. También puede crear una aplicación de Azure AD usando [PowerShell Azure](../resource-group-authenticate-service-principal.md) o [CLI de Azure](../resource-group-authenticate-service-principal-cli.md).

1. Inicie sesión en su cuenta de Azure a través del [portal clásico](https://manage.windowsazure.com/).

2. Seleccione **Active Directory** en el panel izquierdo.

     ![Seleccione Active Directory](./media/data-lake-store-authenticate-using-active-directory/active-directory.png)
     
3. Seleccione Active Directory que desea usar para crear la nueva aplicación. Si tiene más de un Active Directory, normalmente desea crear la aplicación en el directorio donde se encuentra la suscripción. Solo puede conceder acceso a recursos de la suscripción para las aplicaciones en el mismo directorio que la suscripción.  

     ![Seleccionar directorio](./media/data-lake-store-authenticate-using-active-directory/active-directory-details.png)
    
    
3. Para ver las aplicaciones en el directorio, haga clic en **aplicaciones**.

     ![ver aplicaciones](./media/data-lake-store-authenticate-using-active-directory/view-applications.png)

4. Si no ha creado una aplicación en el directorio antes de que verá algo similar a la siguiente imagen. Haga clic en **Agregar una aplicación**

     ![Agregar aplicación](./media/data-lake-store-authenticate-using-active-directory/create-application.png)

     O bien, haga clic en **Agregar** en el panel inferior.

     ![Agregar](./media/data-lake-store-authenticate-using-active-directory/add-icon.png)

6. Proporcione un nombre para la aplicación y seleccione el tipo de aplicación que desea crear. En este tutorial, cree una **Aplicación o WEB API de WEB** y haga clic en el botón siguiente.

     ![aplicación de nombre](./media/data-lake-store-authenticate-using-active-directory/tell-us-about-your-application.png)

7. Rellene las propiedades de la aplicación. Para la **Dirección URL de inicio de sesión**, proporcione la URI a un sitio web que describa la aplicación. No se valida la existencia del sitio web. **URI de ID de aplicación**, proporcione el URI que identifica la aplicación.

     ![propiedades de la aplicación](./media/data-lake-store-authenticate-using-active-directory/app-properties.png)

    Haga clic en la marca de verificación para completar al asistente y crear la aplicación.

### <a name="step-2-get-client-id-client-secret-and-token-endpoint"></a>Paso 2: Obtener el identificador de cliente, secreto cliente y extremo token

Al registro mediante programación en, necesita el identificador de la aplicación. Si la aplicación se ejecuta en sus propia credenciales, también necesitará una clave de autenticación.

1. Haga clic en la ficha **Configurar** para configurar la contraseña de la aplicación.

     ![configurar la aplicación](./media/data-lake-store-authenticate-using-active-directory/application-configure.png)

2. Copie el **identificador de cliente**.
  
     ![identificador de cliente](./media/data-lake-store-authenticate-using-active-directory/client-id.png)

3. Si la aplicación se ejecutará en sus propias credenciales, desplácese hacia abajo hasta la sección de **claves** y seleccione cuánto tiempo desea que la contraseña sea válido.

     ![teclas](./media/data-lake-store-authenticate-using-active-directory/create-key.png)

4. Seleccione **Guardar** para crear la clave.

    ![Guardar](./media/data-lake-store-authenticate-using-active-directory/save-icon.png)

    Se muestra la clave guardada y se puede copiar. No podrá recuperar la clave posteriormente, por lo que debe copiar ahora.

    ![Guardar clave](./media/data-lake-store-authenticate-using-active-directory/save-key.png)

5. Recuperar el token extremo seleccionando **extremos de vista** en la parte inferior de la pantalla y recuperar el valor de campo de **Extremo de Token OAuth 2.0** , tal como se muestra a continuación.  

    ![Id. de inquilinos](./media/data-lake-store-authenticate-using-active-directory/save-tenant.png)

### <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-store-account-file-or-folder-only-for-service-to-service-authentication"></a>Paso 3: Asignar la aplicación de Azure AD para el archivo de cuenta de la tienda de lago de datos de Azure o una carpeta (solo para la autenticación de servicio de servicio)

1. Inicie sesión el nuevo [Portal de Azure](https://portal.azure.com) y abra la cuenta de Azure datos lago almacén que desea asociar con la aplicación de Azure Active Directory que creó anteriormente.

1. En el módulo de almacén de datos lago cuenta, haga clic en **Explorador de datos**.

    ![Crear directorios de almacén de datos de lago cuenta] (./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Crear directorios de datos lago cuenta")

2. En el módulo de **Explorador de datos** , haga clic en el archivo o carpeta para la que desea proporcionar acceso a la aplicación de Azure AD y, a continuación, haga clic en **acceso**. Para configurar el acceso a un archivo, debe hacer clic en **acceso** de la hoja de **Vista previa del archivo** .

    ![Establecer ACL en el sistema de archivos de datos lago] (./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Establecer ACL en el sistema de archivos de datos lago")

3. El módulo de **Access** muestra el acceso estándar y personalizado ya asignado a la raíz. Haga clic en el icono **Agregar** para agregar las ACL de nivel personalizado.

    ![Lista estándar y acceso personalizado] (./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Lista estándar y acceso personalizado")

4. Haga clic en el icono de **Agregar** para abrir el módulo de **Agregar acceso de personalizado** . En este módulo, haga clic en **Seleccionar usuario o grupo**y, a continuación, en el módulo de **Seleccionar usuario o grupo** , busque el grupo de seguridad que creó anteriormente en Azure Active Directory. Si tiene una gran cantidad de grupos para buscar desde, use el cuadro de texto en la parte superior para filtrar en el nombre del grupo. Haga clic en el grupo que desea agregar y, a continuación, haga clic en **Seleccionar**.

    ![Agregar un grupo] (./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Agregar un grupo")

5. Haga clic en **Seleccionar permisos**, seleccione los permisos y si desea asignar los permisos de forma predeterminada ACL, tener acceso a ACL o ambos. Haga clic en **Aceptar**.

    ![Asignar permisos de grupo] (./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Asignar permisos de grupo")

    Para obtener más información sobre los permisos en el almacén de datos de lago y ACL del acceso o de forma predeterminada, consulte [Control de acceso en el almacén de datos de lago](data-lake-store-access-control.md).


6. En el módulo de **Agregar acceso de personalizada** , haga clic en **Aceptar**. El grupo recién agregado, con los permisos asociados, ahora se mostrarán en el módulo de **Access** .

    ![Asignar permisos de grupo] (./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Asignar permisos de grupo") 


## <a name="next-steps"></a>Pasos siguientes

En este artículo se crea una aplicación web de Azure AD y recopila la información que necesita en las aplicaciones cliente que autor mediante .NET SDK, SDK de Java, etcetera. Ahora puede continuar con los siguientes artículos que hablar sobre cómo usar la aplicación web de Azure AD para autenticar primero con lago almacén de datos y, a continuación, realizar otras operaciones en la tienda.

- [Introducción a Azure datos lago tienda con .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Introducción a Azure datos lago tienda mediante el SDK de Java](data-lake-store-get-started-java-sdk.md)
