<properties 
   pageTitle="Adición de Azure Active Directory mediante servicios conectados en Visual Studio | Microsoft Azure"
   description="Agregar un Azure Active Directory mediante el cuadro de diálogo Servicios de conectados agregar de Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="active-directory"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Adición de Azure Active Directory mediante servicios conectados en Visual Studio 

##<a name="overview"></a>Información general
Mediante el uso de Azure Active Directory (AD Azure), puede admitir el inicio de sesión único (SSO) para aplicaciones web de ASP.NET MVC o la autenticación de AD en servicios de Web API. Con la autenticación de Azure AD, los usuarios pueden usar sus cuentas de Azure AD para conectarse a sus aplicaciones web. Las ventajas de autenticación de Azure AD con la API de Web incluyen seguridad de datos mejorada al exponer una API desde una aplicación web. Con Azure AD no tiene que administrar un sistema de autenticación independiente con su propia administración de usuario y de cuenta.

## <a name="supported-project-types"></a>Tipos de proyectos compatibles

Puede usar el cuadro de diálogo servicios conectados para conectarse a Azure AD en los siguientes tipos de proyecto.

- Proyectos MVC de ASP.NET

- Proyectos Web de ASP.NET API


### <a name="connect-to-azure-ad-using-the-connected-services-dialog"></a>Conectarse a Azure AD mediante el cuadro de diálogo servicios conectados

1. Asegúrese de que dispone de una cuenta de Azure. Si no tiene una cuenta de Azure, puede registrarse para una [prueba gratuita](http://go.microsoft.com/fwlink/?LinkId=518146).

1. En Visual Studio, abra el menú contextual del nodo **referencias** en su proyecto y elija **Agregar servicios conectados**.
1. Seleccione **autenticación de Azure AD** y, a continuación, elija **Configurar**.

    ![Elija agregar autenticación de Azure AD](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. En la primera página de la **autenticación de configurar Azure Active Directory**, consulte **configurar inicio de sesión único con Azure AD**.

    Si su proyecto está configurado con otra configuración de autenticación, el asistente le advierte de que continuar deshabilitará la configuración anterior.

    ![Configurar Azure AD en el Asistente](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1.  En la segunda página, seleccione un dominio de la lista desplegable de **dominio** . La lista de dominios contiene todos los dominios accesibles por las cuentas que aparecen en el cuadro de diálogo Configuración de la cuenta. Como alternativa, puede escribir un nombre de dominio, si no encuentra lo que busca, como mydomain.onmicrosoft.com. Puede elegir la opción para crear una nueva aplicación de Azure AD o usar la configuración de una aplicación de Azure AD existente. 

    ![Configurar Azure AD en el Asistente](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)


1. En la tercera página del asistente, asegúrese de que está activada la opción **leer datos de directorio** . El asistente rellenará el **secreto de cliente**. 

    ![Configurar Azure AD en el Asistente](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Elija el botón **Finalizar** . El cuadro de diálogo agrega el código de configuración necesarios y referencias para habilitar el proyecto para la autenticación de Azure AD. Puede ver el dominio de Active Directory en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Revise la página de introducción que aparece en el explorador para obtener ideas sobre los siguientes pasos y la página ¿Qué ha ocurrido para ver cómo se ha modificado el proyecto. Si desea comprobar que todo funcionaba, abrir uno de los archivos de configuración modificada y comprobar la configuración mencionada en ¿qué ha ocurrido existen. Por ejemplo, web.config principal en un proyecto de ASP.NET MVC tendrá estos valores agregados:

        <appSettings> 
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:AADInstance" value="https://login.windows.net/" />
            <add key="ida:Domain" value="Your selected domain" />
            <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
            <add key="ida:PostLogoutRedirectUri" value="The default redirect URI from the project" />
        </appSettings>

## <a name="how-your-project-is-modified"></a>¿Cómo se modifica el proyecto

Cuando se ejecuta el asistente, Visual Studio agrega Azure AD y asociados referencias a su proyecto. También se modifican los archivos de configuración y archivos de código del proyecto para agregar compatibilidad con Azure AD. Las modificaciones específicas que Visual Studio realiza dependen del tipo de proyecto. Para obtener información detallada sobre cómo se modifican los proyectos de ASP.NET MVC, vea [¿Qué proyectos MVC ocurrió:](http://go.microsoft.com/fwlink/p/?LinkID=513809). Para proyectos de Web API, vea [¿Qué ha ocurrido: Web API de proyectos](http://go.microsoft.com/fwlink/p/?LinkId=513810).

##<a name="next-steps"></a>Pasos siguientes

Formular preguntas y obtener ayuda.

 - [Foro de MSDN: Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)

 - [Documentación de Azure AD](https://azure.microsoft.com/documentation/services/active-directory/)

 - [Entrada de blog: Introducción a Azure AD](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

