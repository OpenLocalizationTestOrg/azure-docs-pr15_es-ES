<properties 
    pageTitle="Diagnosticar errores y excepciones en las aplicaciones ASP.NET con recomendaciones de aplicación" 
    description="Capturar excepciones de aplicaciones ASP.NET junto con telemetría de solicitud." 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="awills"/>


# <a name="set-up-application-insights-diagnose-exceptions"></a>Configurar perspectivas de aplicación: diagnosticar excepciones

[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]


Mediante la supervisión de la aplicación con [Recomendaciones de aplicación de Visual Studio][start], correlación solicitudes de errores con excepciones y otros eventos en el cliente y el servidor, de modo que puede diagnosticar rápidamente las causas.

Para supervisar una aplicación ASP.NET, primero tiene que [Agregar la aplicación perspectivas SDK] [ greenbrown] a la aplicación, o bien [instalar el Monitor de estado en el servidor IIS][redfield], o bien, si su aplicación es una aplicación Web de Azure, agregue la [Extensión de perspectivas de aplicación](app-insights-azure-web-apps.md).

## <a name="diagnosing-exceptions-using-visual-studio"></a>Diagnosticar excepciones mediante Visual Studio

Abra la solución de aplicación en Visual Studio para ayudarle con la depuración.

Ejecute la aplicación, en el servidor o en su equipo de desarrollo mediante F5.

Abra la ventana búsqueda de información de aplicación en Visual Studio y configurarlo para que muestre los eventos de la aplicación. Mientras está depuración, puede hacerlo haciendo clic en el botón de información de la aplicación.

![Haga clic en el proyecto y elija aplicación perspectivas, abrir.](./media/app-insights-asp-net-exceptions/34.png)

Observe que puede filtrar el informe para mostrar solo excepciones. 

*¿Sin excepciones que muestra? Vea [capturar excepciones](#exceptions).*

Haga clic en un informe de excepción para mostrar su seguimiento de la pila.

![Haga clic en una excepción.](./media/app-insights-asp-net-exceptions/35.png)

Haga clic en una referencia de la línea en la pila de seguimiento, para abrir el archivo relevante.  

## <a name="diagnosing-failures-using-the-azure-portal"></a>Diagnosticar errores con el portal de Azure

Desde la introducción de la información de la aplicación de la aplicación, el mosaico de errores muestra gráficos de excepciones y no se pudo solicitudes HTTP, junto con una lista de la solicitud URL que producen los errores más frecuentes.

![Seleccione configuración de errores](./media/app-insights-asp-net-exceptions/012-start.png)

Haga clic en uno de los tipos de solicitud error en la lista para obtener acceso a las apariciones individuales del error. Desde allí, haga clic en a través de las excepciones o los datos de seguimiento asociados:

![Seleccione una instancia de una solicitud de error y, en detalles de la excepción, acceder a instancias de la excepción.](./media/app-insights-asp-net-exceptions/030-req-drill.png)


**Como alternativa,** puede iniciar de la lista de excepciones que encontrará más abajo en el módulo de errores. Mantener haciendo clic en hasta llegar finalmente a excepciones individuales.


![Obtención de detalles](./media/app-insights-asp-net-exceptions/040-exception-drill.png)

*¿Sin excepciones que muestra? Vea [capturar excepciones](#exceptions).*

Desde allí puede mirar el seguimiento de pila y propiedades detalladas de cada excepción y buscar seguimiento del registro relacionado u otros eventos.


![Obtención de detalles](./media/app-insights-asp-net-exceptions/050-exception-properties.png)

[Obtenga más información sobre la búsqueda de diagnóstico][diagnostic].



## <a name="dependency-failures"></a>Errores de dependencia

Una *dependencia* es un servicio que llama a la aplicación, normalmente a través de una conexión de base de datos o la API de REST. [Monitor de estado de aplicación perspectivas] [ redfield] supervisa automáticamente una variedad de tipos de llamada de dependencia, medir la duración de la llamada y éxito o error. 

Para obtener datos de dependencia, tiene que [instalar el Monitor de estado] [ redfield] en su servidor IIS, o si la aplicación es una aplicación Web de Azure, utilice la [Extensión de perspectivas de aplicación](app-insights-azure-web-apps.md). 

Error llamadas a las dependencias aparecen en el módulo de errores, y también puede encontrarlos en elementos relacionados en los detalles de la solicitud y los detalles de excepción.

*¿Sin errores de dependencia? Está bien. Pero, para comprobar que recibe los datos de dependencia, abra el módulo de rendimiento y observe el gráfico de dependencia duración.*

 

## <a name="custom-tracing-and-log-data"></a>Traza personalizados y datos del registro

Para obtener datos de diagnóstico específicos de la aplicación, puede insertar código para enviar sus propios datos de telemetría. Esto se muestra en diagnóstico búsqueda junto con la solicitud, vista de página y otros datos recopila automáticamente. 

Tiene varias opciones:

* [TrackEvent()](app-insights-api-custom-events-metrics.md#track-event) normalmente se utiliza para supervisar patrones de uso, pero los datos que envía también aparecen en eventos personalizados en búsqueda de diagnóstico. Se denominan eventos y puede llevar a cabo propiedades de cadena y numéricos mediciones en el que puede [filtrar las búsquedas de diagnósticos][diagnostic].
* [TrackTrace()](app-insights-api-custom-events-metrics.md#track-trace) permite enviar más datos como información de entrada.
* [TrackException()](#exceptions) envía seguimientos de la pila. [Más información acerca de las excepciones](#exceptions).
* Si ya usa un marco de registro como Log4Net o NLog, puede [capturar esos registros] [ netlogs] y verlos en diagnóstico búsqueda junto con los datos de solicitud y excepción.

Para ver estos eventos, abrir [búsqueda][diagnostic], Abrir filtro y luego seleccione Custom Event, seguimiento o excepción.


![Obtención de detalles](./media/app-insights-asp-net-exceptions/viewCustomEvents.png)


> [AZURE.NOTE] Si su aplicación genera una gran cantidad de telemetría, el módulo de muestreo adaptación reducirá automáticamente el volumen que se envía al portal enviando sólo una fracción representante de eventos. Eventos que forman parte de la misma operación se activarse o desactivarse como un grupo, para que pueda desplazarse entre los eventos relacionados. [Obtenga información sobre muestreo.](app-insights-sampling.md)

### <a name="how-to-see-request-post-data"></a>Cómo ver los datos de entrada de solicitud

Detalles de la solicitud no incluyen los datos enviados a su aplicación en una llamada de entrada. Tener estos datos notificados:

* [Instalar el SDK] [ greenbrown] en el proyecto de la aplicación.
* Insertar código en la aplicación llame a [Microsoft.ApplicationInsights.TrackTrace()][api]. Enviar los datos de entrada en el parámetro de mensaje. Hay un límite de tamaño permitido, por lo que deberá intentar enviar solo los datos esenciales.
* Al investigar una solicitud errónea, busque los seguimientos asociados.  

![Obtención de detalles](./media/app-insights-asp-net-exceptions/060-req-related.png)


## <a name="exceptions"></a>Captura de excepciones y datos relacionados de diagnósticos

En primer lugar, no verá en el portal de todas las excepciones que se produzcan errores en la aplicación. Verá las excepciones de explorador (si está usando el [SDK de JavaScript] [ client] en sus páginas web). Pero se detecta la mayoría de las excepciones de servidor IIS y tiene que escribir un fragmento de código para verlos.

Puedes:

* **Registrar excepciones explícitamente** insertando código de controladores de excepciones para informar de las excepciones.
* **Capturar excepciones automáticamente** al configurar el marco de trabajo ASP.NET. Las adiciones necesarias son diferentes para diferentes tipos de marco.

## <a name="reporting-exceptions-explicitly"></a>Informar de excepciones explícitamente

La manera más sencilla es insertar una llamada a TrackException() en un controlador de excepciones.

JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

Los parámetros de medidas y propiedades son opcionales, pero son útiles para [filtrar y agregar] [ diagnostic] información adicional. Por ejemplo, si tiene una aplicación que se puede ejecutar varios juegos, puede buscar todos los informes de excepción relacionados con un juego determinado. Puede agregar todos los elementos que desee en cada diccionario.

## <a name="browser-exceptions"></a>Excepciones de explorador

Se muestran la mayoría de las excepciones de explorador.

Si su página web incluye archivos de script de redes de entrega de contenido o de otros dominios, asegúrese de que la etiqueta de script tiene el atributo ```crossorigin="anonymous"```, y que el servidor envía [encabezados CORS](http://enable-cors.org/). Esto le permitirá obtener un seguimiento de la pila y detalles de las excepciones de JavaScript no controladas de estos recursos.

## <a name="web-forms"></a>Formularios Web

Para los formularios de web, el módulo HTTP podrán recopilar las excepciones cuando no hay ningún redireccionamientos configurados con CustomErrors.

Pero si tiene redireccionamientos activos, agregue las siguientes líneas a la función Application_Error en Global.asax.cs. (Agregar un archivo Global.asax si todavía no tiene uno).

*C#*

    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError  () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }


## <a name="mvc"></a>MVC

Si la configuración de [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) es `Off`, excepciones estarán disponibles para el [Módulo HTTP](https://msdn.microsoft.com/library/ms178468.aspx) recopilar. Sin embargo, si es `RemoteOnly` (predeterminado), o `On`, entonces, será la excepción desactivada y no está disponible para la aplicación perspectivas recopilar automáticamente. Puede corregir reemplaza la [clase System.Web.Mvc.HandleErrorAttribute](http://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx)y aplicando la clase reemplazada tal como se muestra para las distintas versiones MVC debajo ([origen de github](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)] 
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above  
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                } 
            }
            base.OnException(filterContext);
        }
      }
    }

#### <a name="mvc-2"></a>MVC 2

Reemplazar el atributo HandleError con su nuevo atributo en los controladores.

    namespace MVC2App.Controllers
    {
       [AiHandleError]
       public class HomeController : Controller
       {
    ...

[Ejemplo](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3

Registrar `AiHandleErrorAttribute` como un filtro global en Global.asax.cs:

    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...

[Ejemplo](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)



#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5

Registrar AiHandleErrorAttribute como un filtro global en FilterConfig.cs:

    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }

[Ejemplo](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api-1x"></a>Web API 1.x


Reemplazar System.Web.Http.Filters.ExceptionFilterAttribute:

    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above 
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);    
            }
            base.OnException(actionExecutedContext);
        }
      }
    }

Puede agregar este atributo reemplazado a determinados controladores o agregar a la configuración de filtro global de la clase WebApiConfig: 

    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }

[Ejemplo](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

Existen varios casos en los que no pueden controlar los filtros de excepciones. Por ejemplo:

* Excepciones de constructores controlador. 
* Excepciones de los controladores de mensajes. 
* Excepciones durante el enrutamiento. 
* Excepciones durante la serialización de contenido de respuesta. 

## <a name="web-api-2x"></a>Web API 2.x

Agregar una implementación de IExceptionLogger:

    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above 
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }

Para los servicios de WebApiConfig, agregue lo siguiente:

    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger()); 
        }
      }
  }

[Ejemplo](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Como alternativa, puede:

2. Reemplazar el ExceptionHandler solo con una implementación personalizada de IExceptionHandler. Sólo se llama cuando el marco de trabajo está todavía puede elegir qué mensaje de respuesta a enviar (no cuando la conexión se cancela por ejemplo) 
3. Filtros de excepciones (como se describe en la sección de controladores de Web API 1.x anteriores) - no llamados en todos los casos.


## <a name="wcf"></a>WCF

Agregar una clase que extiende atributo e implementa IErrorHandler y IServiceBehavior.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription, 
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription, 
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above 
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error, 
            System.ServiceModel.Channels.MessageVersion version, 
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Agregar el atributo a las implementaciones de servicio:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1 
        { 
         ...

[Ejemplo](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Contadores de rendimiento de excepción

Si tiene [instalado el Monitor de estado] [ redfield] en el servidor, puede obtener un gráfico de la tasa de excepciones, medido por .NET. Esto incluye tanto excepciones controladas y .NET.

Abrir un módulo de explorador métrica, agregar un nuevo gráfico y seleccione el **tipo de excepción**, que aparece en contadores de rendimiento. 

.NET framework calcula la tasa contar el número de excepciones en un intervalo y dividiendo por el intervalo de tiempo. 

Tenga en cuenta que será diferente del recuento 'Excepciones' calculado el portal de aplicación perspectivas contando TrackException informes. Los intervalos de muestreo son diferentes, y el SDK no enviar informes de TrackException para todos los administran y las excepciones no controlan.

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[netlogs]: app-insights-asp-net-trace-logs.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

 
