<properties
    pageTitle="Ejemplos de informe API de actividad de inicio de sesión de Azure Active Directory | Microsoft Azure"
    description="Cómo empezar a trabajar con la API informes del directorio de Azure Active"
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

# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Ejemplos de informe API de actividad de inicio de sesión de Azure Active Directory

Este tema es parte de una colección de temas de Azure Active Directory con la API de informes.  
Informes de Azure AD proporcionan una API que permite obtener acceso a datos de la actividad de inicio de sesión utilizando código o herramientas relacionadas.  
El ámbito de este tema es proporcionar con código de ejemplo para el **Inicio de sesión de actividad API**.

Vea:

- [Registros de auditoría](active-directory-reporting-azure-portal.md#audit-logs) para obtener más información
- [Introducción a Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md) para obtener más información acerca de la API de generación de informes.

Si tiene preguntas, problemas o comentarios, póngase en contacto con [Informes le ayudan a AAD](mailto:aadreportinghelp@microsoft.com).


## <a name="prerequisites"></a>Requisitos previos
Para poder usar los ejemplos de este tema, debe completar los [requisitos previos para obtener acceso a la Azure AD API de informes](active-directory-reporting-api-prerequisites.md).  


##<a name="powershell-script"></a>Secuencia de comandos de PowerShell

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID       = "<clientId>"             # Should be a ~35 character string insert your info here
    $ClientSecret   = "<clientSecret>"         # Should be a ~44 character string insert your info here
    $loginURL       = "https://login.windows.net/"
    $tenantdomain   = "<tenantDomain>"
    $ daterange            # For example, contoso.onmicrosoft.com

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}

    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    $url = "https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&`$filter=signinDateTime ge $7daysago"
    
    $i=0
    
    Do{
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        Write-Output "Save the output to a file SigninActivities$i.json"
        Write-Output "---------------------------------------------"
        $myReport.Content | Out-File -FilePath SigninActivities$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)

    } else {
    
        Write-Host "ERROR: No Access Token"
    }




## <a name="executing-the-script"></a>Ejecutando el script
Se devuelve una vez que termine de modificar la secuencia de comandos, ejecutarla y comprobar que los datos esperados de la auditoría registran el informe.

La secuencia de comandos devuelve resultados del informe de inicio de sesión en formato JSON. También crea una `SigninActivities.json` archivo con el mismo resultado. Puede experimentar modificando la secuencia de comandos para devolver los datos de otros informes y comente los formatos de salida que no necesita.



## <a name="next-steps"></a>Pasos siguientes

- ¿Desea personalizar los ejemplos de este tema? Consulte la [referencia de la API Azure Active Directory sesión actividad](active-directory-reporting-api-sign-in-activity-reference.md). 

- Si desea ver un resumen de uso de la API de informes de Azure Active Directory, consulte [Introducción a Azure Active Directory reporting API](active-directory-reporting-api-getting-started.md).

- Si desea obtener más información sobre informes de Azure Active Directory, consulte la [Azure Active Directory Reporting guía](active-directory-reporting-guide.md).  