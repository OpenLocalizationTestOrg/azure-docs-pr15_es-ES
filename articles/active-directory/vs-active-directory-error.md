<properties 
    pageTitle="Error durante la detección de autenticación" 
    description="El Asistente para la conexión de active directory detecta un tipo de autenticación incompatible" 
    services="active-directory" 
    documentationCenter="" 
    authors="TomArcher" 
    manager="douge" 
    editor=""/>
  
<tags 
    ms.service="active-directory" 
    ms.workload="web" 
    ms.tgt_pltfrm="vs-getting-started" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="tarcher"/>

# <a name="error-during-authentication-detection"></a>Error durante la detección de autenticación

Al detectar el anterior código de autenticación, el asistente detectó un tipo de autenticación incompatible.   

##<a name="what-is-being-checked"></a>¿Qué se va a revisar?

**Nota:** Para detectar correctamente anterior código de autenticación en un proyecto, debe generarse el proyecto.  Si se produjo el error y no tiene un código de autenticación anterior en su proyecto, volver a generar e inténtelo de nuevo.

###<a name="project-types"></a>Tipos de proyecto

El asistente comprueba el tipo de proyecto que se desarrolla para que puede insertar la lógica de autenticación adecuada en el proyecto.  Si no hay ningún controlador que se deriva de `ApiController` en el proyecto, el proyecto se considera un proyecto WebAPI.  Si hay sólo los controladores que derivan de `MVC.Controller` en el proyecto, el proyecto se considera un proyecto de MVC.  Nada no es compatible con el asistente.  Proyectos de Web Forms no se admiten actualmente.

###<a name="compatible-authentication-code"></a>Código de autenticación compatibles

También comprueba el Asistente para la configuración de autenticación que se han configurado previamente con el asistente o es compatibles con el asistente.  Si todos los valores están presentes, se considera un caso el y abrir el asistente y mostrar la configuración.  Si sólo algunos de los ajustes están presentes, se considera un caso de error.

En un proyecto MVC, el asistente comprueba por cualquiera de las siguientes opciones, que resulten de uso anterior del asistente:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Además, el asistente comprueba por cualquiera de las siguientes opciones en un proyecto de Web API que resulten de uso anterior del asistente:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

###<a name="incompatible-authentication-code"></a>Código de autenticación incompatible

Por último, el asistente intenta detectar versiones de código de autenticación que se han configurado con versiones anteriores de Visual Studio. Si recibe este error, significa que el proyecto contiene un tipo de autenticación incompatible. El asistente detecta los siguientes tipos de autenticación de versiones anteriores de Visual Studio:

* Autenticación de Windows 
* Cuentas de usuario individuales 
* Cuentas de la organización 
 

Para detectar la autenticación de Windows en un proyecto de MVC, el asistente buscará el `authentication` elemento de su archivo **web.config** .

<pre>
    &lt;configuración&gt;
        &lt;system.web&gt;
            <span style="background-color: yellow">&lt;modo de autenticación = "Windows" /&gt;</span>
        &lt;/System.Web&gt;
    &lt;/configuration&gt;
</pre>

Para detectar la autenticación de Windows en un proyecto de Web API, el asistente buscará el `IISExpressWindowsAuthentication` elemento de **archivo de su proyecto** :

<pre>
    &lt;Proyecto&gt;
&lt;PropertyGroup&gt;
            <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;habilitado&lt;/IISExpressWindowsAuthentication&gt;</span>
        &lt;/PropertyGroup > &lt;/proyecto        &gt;
</pre>

Para detectar autenticación de cuentas de usuario individuales, el asistente buscará el elemento de paquete desde el archivo **Packages.config** .

<pre>
    &lt;paquetes&gt;
        <span style="background-color: yellow">&lt;id="Microsoft.AspNet.Identity.EntityFramework del paquete" versión = "2.1.0" targetFramework = "net45" /&gt;</span>
    &lt;/paquetes&gt;
</pre>

Para detectar una forma antigua de autenticación de cuenta de la organización, el asistente buscará el siguiente elemento de **web.config**:

<pre>
    &lt;configuración&gt;
        &lt;appSettings&gt;
            <span style="background-color: yellow">&lt;Agregar clave = valor de "ida: territorio" = "***" /&gt;</span>
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

Para cambiar el tipo de autenticación, quite el tipo de autenticación compatibles y vuelva a ejecutar al asistente.

Para obtener más información, vea [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md).