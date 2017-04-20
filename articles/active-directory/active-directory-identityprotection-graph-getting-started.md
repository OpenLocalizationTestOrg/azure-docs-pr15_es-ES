<properties
    pageTitle="Introducción a la protección de identidad de Azure Active Directory y Microsoft Graph | Microsoft Azure"
    description="Proporciona una introducción a Microsoft Graph de consulta para una lista de eventos de riesgo e información asociada de Azure Active Directory."
    services="active-directory"
    keywords="protección de la identidad de Azure directorio activo, evento de riesgo, vulnerabilidad, directiva de seguridad de Microsoft Graph"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="markvi"/>

# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Introducción a la protección de identidad de Azure Active Directory y Microsoft Graph

Microsoft Graph es el extremo de API unificada de Microsoft y el inicio de [Azure Active Directory protección de identidad de](active-directory-identityprotection.md) API. Nuestra primera API, **identityRiskEvents**, le permite consultar Microsoft Graph para una lista de [eventos de riesgo](active-directory-identityprotection-risk-events-types.md) e información asociada. En este artículo le permite iniciar consultar esta API. Para detalladamente introducción, documentación completa y acceso a Explorador de gráfico, vea el [sitio de Microsoft Graph](https://graph.microsoft.io/).

Hay tres pasos para obtener acceso a datos de protección de identidad a través de Microsoft Graph:

1. Agregar una aplicación con un secreto de cliente. 

2. Use este secreto y algunos otros tipos de información para autenticarse en Microsoft Graph, que recibe un símbolo de autenticación. 

3. Use este token para realizar las solicitudes en el extremo de la API y volver a los datos de protección de la identidad.

Antes de empezar, debe:

- Privilegios de administrador para crear la aplicación en Azure AD
- El nombre del dominio de su inquilino (por ejemplo, contoso.onmicrosoft.com)



## <a name="add-an-application-with-a-client-secret"></a>Agregar una aplicación con un secreto de cliente


1. [Inicie sesión en](https://manage.windowsazure.com) el portal de Azure clásico como administrador. 

1. En el panel de navegación izquierdo, haga clic en **Active Directory**. 

    ![Crear una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)

2. En la lista de **directorio** , seleccione el directorio para el que desea habilitar la integración de directorios.

3. En el menú de la parte superior, haga clic en **aplicaciones**.

    ![Crear una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)

4. Haga clic en **Agregar** en la parte inferior de la página.

    ![Crear una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)

5. En el cuadro de diálogo **¿Qué desea hacer** , haga clic en **Agregar una aplicación se desarrolla mi organización**.

    ![Crear una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)


5. En el cuadro de diálogo **Díganos acerca de la aplicación** , realice los pasos siguientes:

    ![Crear una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)

    una. En el cuadro de texto **nombre** , escriba un nombre para la aplicación (por ejemplo: aplicación de API de evento de riesgo AADIP).

    b. Como **tipo**, seleccione **Web API de aplicación o la Web**.

    c. Haga clic en **siguiente**.


5. En el cuadro de diálogo **Propiedades de la aplicación** , realice los pasos siguientes:

    ![Crear una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)

    una. En el cuadro de texto **Dirección URL de inicio de sesión** , escriba `http://localhost`.

    b. En el cuadro de texto de **URI de ID de aplicación** , escriba `http://localhost`.

    c. Haga clic en **Finalizar**.


Ahora, puede configurar la aplicación.

![Crear una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)



## <a name="grant-your-application-permission-to-use-the-api"></a>Otorgar permiso para usar la API de aplicación


1. En la página de la aplicación, en el menú de la parte superior, haga clic en **Configurar**. 

    ![Crear una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)

2. En la sección **permisos a otras aplicaciones** , haga clic en **Agregar aplicación**.

    ![Crear una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)


2. En el cuadro de diálogo **permisos a otras aplicaciones** , realice los pasos siguientes:

    ![Crear una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)

    una. Seleccione **Microsoft Graph**.

    b. Haga clic en **Finalizar**.


1. Haga clic en **permisos de aplicación: 0**y, a continuación, seleccione **leer toda la información de identidad riesgo evento**.

    ![Crear una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)

1. Haga clic en **Guardar** en la parte inferior de la página.

    ![Crear una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)


## <a name="get-an-access-key"></a>Obtener una tecla de acceso

1. En la página de la aplicación, en la sección de **claves** , seleccione 1 año como duración.

    ![Crear una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)

1. Haga clic en **Guardar** en la parte inferior de la página.

    ![Crear una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

1. en la sección de teclas, copie el valor de la clave recién creado y, a continuación, péguelo en una ubicación segura.

    ![Crear una aplicación](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)

    > [AZURE.NOTE] Si pierde esta clave, tendrá que volver a esta sección y crear una nueva clave. Guardar un secreto de esta clave: cualquier persona que tenga puede tener acceso a los datos.


1. En la sección **Propiedades** , copie el **Identificador de cliente**y, a continuación, péguelo en una ubicación segura. 


## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Autenticar a Microsoft Graph y la API de eventos de riesgo de identidad de la consulta

En este momento, debe tener:

- El identificador de cliente que copió anteriormente

- La clave que copió anteriormente

- El nombre del dominio de su inquilino


Para autenticar, envíe una solicitud de publicación para `https://login.microsoft.com` con los siguientes parámetros en el cuerpo:

- grant_type: "**client_credentials**"

- recursos: "**https://graph.microsoft.com**"

- client_id:<your client ID>

- client_secret:<your key>


> [AZURE.NOTE] Debe proporcionar los valores para el **client_id** y el parámetro **client_secret** .

Si es correcto, se devuelve un símbolo de autenticación.  
Para llamar a la API, cree un encabezado con el parámetro siguiente:

    `Authorization`=”<token_type> <access_token>"


Cuando se autentica, puede encontrar el tipo de token y el token de acceso en el token devuelto.

Enviar este encabezado como una solicitud a la siguiente URL API:`https://graph.microsoft.com/beta/identityRiskEvents`

La respuesta, si es correcto, es una colección de eventos de riesgo de identidad y datos asociados con el formato OData JSON, que se puede analizar y tratar como convenga.

Aquí es el código de ejemplo para autenticar y llamar a la API con Powershell.  
El identificador de cliente, basta con Agregar clave y dominio de inquilinos.

    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 


## <a name="next-steps"></a>Pasos siguientes

Enhorabuena, que acaba de crear su primera llamada a Microsoft Graph.  
Ahora puede consultar los eventos de riesgo de identidad y usar los datos le convenga.

Para obtener más información sobre Microsoft Graph y cómo crear aplicaciones con la API de gráfico, consulte la [documentación](https://graph.microsoft.io/docs) y mucho más en el [sitio de Microsoft Graph](https://graph.microsoft.io/). Además, asegúrese de marcar la página de la [API de protección de identidad de Azure AD](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) que muestra todas las API de protección de identidad disponibles en el gráfico. Como agregamos nuevas posibilidades para trabajar con protección de identidad a través de la API, podrá verlos en esa página.


## <a name="additional-resources"></a>Recursos adicionales

- [Protección de la identidad de Azure Active Directory](active-directory-identityprotection.md)

- [Tipos de eventos de riesgo detectados por protección de identidad de Azure Active Directory](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Información general de Microsoft Graph](https://graph.microsoft.io/docs)

- [Raíz del servicio de protección de identidad de Azure AD](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)
