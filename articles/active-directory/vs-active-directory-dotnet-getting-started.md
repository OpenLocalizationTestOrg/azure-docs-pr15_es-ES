<properties 
    pageTitle="Empezar con Azure Active Directory y servicios de Visual Studio conectado (proyectos MVC) | Microsoft Azure" 
    description="Cómo empezar a trabajar con Azure Active Directory en proyectos MVC después de conectarse a o crear un anuncio de Azure mediante Visual Studio conectado servicios" 
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

# <a name="getting-started-with-azure-active-directory-and-visual-studio-connected-services-mvc-projects"></a>Introducción a Azure Active Directory y Visual Studio conectado servicios (proyectos MVC)

> [AZURE.SELECTOR]
> - [Introducción](vs-active-directory-dotnet-getting-started.md)
> - [Qué ha pasado](vs-active-directory-dotnet-what-happened.md)
 
##<a name="requiring-authentication-to-access-controllers"></a>Requerir autenticación a los controladores de access 

Todos los controladores de su proyecto se adornar con el atributo **autorizar** . Este atributo requerirá al usuario autenticarse antes de obtener acceso a estos controladores. Para permitir que el controlador de accesibles anónimamente, quitar este atributo desde el controlador. Si desea configurar los permisos en un nivel más detallado, aplique el atributo en cada método que requieren autorización en lugar de aplicar a la clase de controlador.
 
##<a name="adding-signin--signout-controls"></a>Adición de inicio de sesión / SignOut controles 

Para agregar un los controles de inicio de sesión/SignOut a la vista, puede usar la vista parcial de **_LoginPartial.cshtml** para agregar la funcionalidad de una de las vistas. Aquí es un ejemplo de la función de agregado a la vista estándar **_Layout.cshtml** . (Tenga en cuenta el último elemento de la etiqueta div con la barra de exploración de clase y contraer):

<pre>
    &lt;! DOCTYPE html&gt; 
&lt;html&gt; 
&lt;head&gt; 
&lt;meta charset="utf-8" /&gt; 
&lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt; 
&lt;title&gt;@ViewBag.Title - My ASP.NET Application&lt;/title&gt; 
@Styles.Render("~/Content/css") @Scripts.Render("~/bundles/modernizr") &lt;/head&gt; 
&lt;body&gt; 
&lt;div class="navbar navbar-inverse navbar-fixed-top"&gt; 
&lt;div class="container"&gt; 
&lt;div class="navbar-header"&gt; 
&lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;/button&gt; 
@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) &lt;/div&gt; 
&lt;div class="navbar-collapse collapse"&gt; 
&lt;ul class="nav navbar-nav"&gt; 
&lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt; 
&lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt; 
&lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt; 
&lt;/ul&gt; 
                    <span style="background-color:yellow">@Html.Partial("_LoginPartial")</span> 
                &lt;/ div&gt; 
&lt;/div&gt; 
&lt;/div&gt; 
&lt;div class="container body-content"&gt; 
@RenderBody() &lt;hr /&gt; 
&lt;footer&gt; 
&lt;p&gt;&amp;copy; @DateTime.Now.Year - My ASP.NET Application&lt;/p&gt; 
&lt;/footer&gt; 
&lt;/div&gt; 
@Scripts.Render("~/bundles/jquery") @Scripts.Render("~/bundles/bootstrap") @RenderSection("scripts", required: false) &lt;/body&gt; 
&lt;/html                                                                                                                                                                                                                                                                                                                                                                                                                                                           &gt;
</pre>

[Más información acerca de Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 
