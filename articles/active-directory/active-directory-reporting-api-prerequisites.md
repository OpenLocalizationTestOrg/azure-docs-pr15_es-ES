<properties
    pageTitle="Requisitos previos para obtener acceso a la Azure AD API de informes. | Microsoft Azure"
    description="Obtenga más información sobre los requisitos previos para obtener acceso a la Azure AD API de informes"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Requisitos previos para obtener acceso a la Azure AD API de informes 

[Azure AD reporting API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) proporcionan acceso mediante programación a los datos a través de un conjunto de API de REST. Puede llamar a estas API desde una gran variedad de idiomas y herramientas de programación.

La API de generación de informes utiliza [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) para autorizar el acceso a la API de web. 

Para preparar el acceso a la API de generación de informes, debe:

1. Crear una aplicación en su inquilino de Azure AD 

2. Conceder los permisos adecuados de aplicación para tener acceso a los datos de Azure AD

3. Recopile la configuración de su directorio

Si tiene preguntas, problemas o comentarios, póngase en contacto con [Informes le ayudan a AAD](mailto:aadreportinghelp@microsoft.com).


## <a name="create-an-azure-ad-application"></a>Crear una aplicación de Azure AD

Para configurar el directorio para obtener acceso a la Azure AD API de informes, debe iniciar sesión en el portal de Azure clásico con una cuenta de administrador de suscripción Azure también es un miembro de la función de directorio de administrador Global de su inquilino de Azure AD.

> [AZURE.IMPORTANT] Aplicaciones que se ejecutan en credenciales con privilegios de "administrador" así pueden ser muy eficaces, así que asegúrese de proteger las credenciales de identificación o secreto de la aplicación.


1. En el [portal de clásica Azure](https://manage.windowsazure.com), en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/01.png) 

2. En la lista de **active directory** , seleccione el directorio.

3. En el menú de la parte superior, haga clic en **aplicaciones**.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/02.png) 

4. En la barra de la parte inferior, haga clic en **Agregar**.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/03.png) 

5. En la **¿Qué desea hacer?** cuadro de diálogo, haga clic en **Agregar una aplicación se desarrolla mi organización**. 

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/04.png) 


6. En el cuadro de diálogo **Díganos acerca de la aplicación** , realice los pasos siguientes: 

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/05.png) 

    una. En el cuadro de texto **nombre** , escriba un nombre (por ejemplo: aplicación de la API de informes).

    b. Seleccione la **aplicación Web o web API**.

    c. Haga clic en **siguiente**.


7. En el cuadro de diálogo **Propiedades de la aplicación** , realice los pasos siguientes: 

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/06.png) 

    una. En el cuadro de texto **dirección URL de inicio de sesión** , escriba `https://localhost`.

    b. En el cuadro de texto de **URI de ID de aplicación** , escriba ```https://localhost/ReportingApiApp```.

    c. Haga clic en **Finalizar**.



## <a name="grant-your-application-permission-to-use-the-api"></a>Otorgar permiso para usar la API de aplicación

1. En el [portal de clásica Azure](https://manage.windowsazure.com/), en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/01.png) 

2. En la lista de **active directory** , seleccione el directorio.

3. En el menú de la parte superior, haga clic en **aplicaciones**.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/02.png)


3. En la lista de aplicaciones, seleccione la aplicación recién creada.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/07.png)

4. En el menú de la parte superior, haga clic en **Configurar**.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/08.png)


5. En la sección **permisos a otras aplicaciones** , para el recurso de **Azure Active Directory** , haga clic en la lista desplegable de **Permisos de aplicación** y, a continuación, seleccione **los datos de directorio de lectura**.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/09.png)


5. En la barra de la parte inferior, haga clic en **Guardar**.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/10.png)


## <a name="gather-configuration-settings-from-your-directory"></a>Recopile la configuración de su directorio

Esta sección muestra cómo obtener la siguiente configuración desde el directorio:

- Nombre de dominio
- Identificador de cliente
- Secreto de cliente

Necesita estos valores al configurar llamadas a la API de generación de informes. 


### <a name="get-your-domain-name"></a>Obtener el nombre de dominio

1. En el [portal de clásica Azure](https://manage.windowsazure.com), en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/01.png) 

2. En la lista de **active directory** , seleccione el directorio.

3. En el menú de la parte superior, haga clic en **dominios**.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/11.png) 

4. En la columna **Nombre de dominio** , copie el nombre de dominio.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/12.png) 


### <a name="get-the-applications-client-id"></a>Obtener el identificador de cliente de la aplicación

1. En el [portal de clásica Azure](https://manage.windowsazure.com), en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/01.png) 

2. En la lista de **active directory** , seleccione el directorio.

3. En el menú de la parte superior, haga clic en **aplicaciones**.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/02.png) 

4. En la lista de aplicaciones, seleccione la aplicación recién creada.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/07.png)

4. En el menú de la parte superior, haga clic en **Configurar**.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/08.png)

4. Copie el **identificador de cliente**.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/13.png)


### <a name="get-the-applications-client-secret"></a>Obtener el secreto de cliente de la aplicación

Para obtener el secreto de cliente de la aplicación, debe crear una nueva clave y guarde su valor al guardar la nueva clave porque no es posible recuperar este valor ya más adelante.

1. En el [portal de clásica Azure](https://manage.windowsazure.com), en el panel de navegación izquierdo, haga clic en **Active Directory**.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/01.png) 

2. En la lista de **active directory** , seleccione el directorio.

3. En el menú de la parte superior, haga clic en **aplicaciones**.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/02.png) 

4. En la lista de aplicaciones, seleccione la aplicación recién creada.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/07.png)

4. En el menú de la parte superior, haga clic en **Configurar**.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/08.png)

5. En la sección de **teclas** , siga estos pasos: 

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/14.png)

    una. En la lista de la duración, seleccione una duración

    b. En la barra de la parte inferior, haga clic en **Guardar**.

    ![Registrar la aplicación](./media/active-directory-reporting-api-prerequisites/10.png)

    c. Copie el valor de clave.

## <a name="next-steps"></a>Pasos siguientes

- ¿Desea acceder a los datos de Azure AD reporting API mediante programación? Consulte [Introducción a Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md).

- Si desea obtener más información sobre informes de Azure Active Directory, consulte la [Azure Active Directory Reporting guía](active-directory-reporting-guide.md).  
