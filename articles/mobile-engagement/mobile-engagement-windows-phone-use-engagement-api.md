<properties 
    pageTitle="Cómo usar la API de contratación en Windows Phone Silverlight" 
    description="Cómo usar la API de contratación en Windows Phone Silverlight"    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede"
    editor="" /> 

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="how-to-use-the-engagement-api-on-windows-phone-silverlight"></a>Cómo usar la API de contratación en Windows Phone Silverlight

Este documento es un complemento para el documento [cómo integrar compromiso Mobile en su aplicación de Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md). Proporciona en profundidad obtener información detallada sobre cómo usar la API de contratación para informar de las estadísticas de la aplicación.

Si solo desea compromiso para informar de su aplicación sesiones, actividades, se bloquea y obtener información técnica, la forma más sencilla es realizar todas las su `PhoneApplicationPage` clases subsitios que heredan de la `EngagementPage` clase.

Si desea hacer algo más, por ejemplo, si necesita informar de eventos específico de la aplicación, los errores y tareas, o si tienes que informar de las actividades de la aplicación de forma distinta de la ha implementado en la `EngagementPage` clases, a continuación, debe usar la API de contratación.

La API de contratación proporcionada por la `EngagementAgent` clase. Puede acceder a dichos métodos a través de `EngagementAgent.Instance`.

Incluso si no se ha inicializado el módulo de agente, cada llamada a la API se ha aplazado y se vuelve a ejecutar cuando el agente está disponible.

##<a name="engagement-concepts"></a>Conceptos de contratación

Los siguientes elementos perfeccionar los conceptos de compromiso móvil para la plataforma de Windows Phone.

### <a name="session-and-activity"></a>`Session`y`Activity`

Una *actividad* es normalmente asociada a una página de la aplicación, es decir la *actividad* comienza cuando la página se muestra y se detiene cuando se cierra la página: este es el caso cuando se integra el SDK de contratación utilizando la `EngagementPage` clase.

Pero *actividades* también se pueden controlar manualmente mediante la API de contratación. Esto permite dividir una página determinada en varias partes de sub para obtener más detalles sobre el uso de esta página (por ejemplo, para conocidos con qué frecuencia y cuánto tiempo se usan los cuadros de diálogo dentro de esta página).

##<a name="reporting-activities"></a>Actividades de informes

### <a name="user-starts-a-new-activity"></a>Usuario inicia una nueva actividad

#### <a name="reference"></a>Referencia

            void StartActivity(string name, Dictionary<object, object> extras = null)

Tendrá que llamar al `StartActivity()` cada vez que los cambios de la actividad de usuario. La primera llamada a esta función inicia una nueva sesión de usuario.

> [AZURE.IMPORTANT] El SDK de llamar automáticamente al método de actividad final cuando se cierra la aplicación. Por lo tanto, se recomienda llamar al método de StartActivity siempre que la actividad de cambio de usuario y nunca llamar al método de actividad final, puesto que llamar a este método fuerza la sesión actual se va a finalizar.

#### <a name="example"></a>Ejemplo

            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>Usuario finaliza su actividad actual

#### <a name="reference"></a>Referencia

            void EndActivity()

Tendrá que llamar al `EndActivity()` al menos una vez cuando el usuario termina su última actividad. Esto informa el SDK de contratación que el usuario está inactivo y que la sesión de usuario es necesario cerrar una vez el tiempo de espera de sesión caducará (si se llama `StartActivity()` antes de que expire el tiempo de espera de sesión, la sesión continúa simplemente).

#### <a name="example"></a>Ejemplo

            EngagementAgent.Instance.EndActivity();

##<a name="reporting-jobs"></a>Trabajos de informes

### <a name="start-a-job"></a>Iniciar un trabajo

#### <a name="reference"></a>Referencia

            void StartJob(string name, Dictionary<object, object> extras = null)

Puede usar el trabajo para realizar un seguimiento de las tareas de ciertos durante un período de tiempo.

#### <a name="example"></a>Ejemplo

            // An upload begins...
            
            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");
            
            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>Finalizar una tarea

#### <a name="reference"></a>Referencia

            void EndJob(string name)

Tan pronto como una tarea que un seguimiento de una tarea ha finalizado, debe llamar al método de EndJob para este trabajo, proporcionando el nombre del trabajo.

#### <a name="example"></a>Ejemplo

            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends
            
            EngagementAgent.Instance.EndJob("uploadData");

##<a name="reporting-events"></a>Informe de eventos

Hay tres tipos de eventos:

-   Eventos de programa independiente
-   Eventos de sesión
-   Eventos de la tarea

### <a name="standalone-events"></a>Eventos de programa independiente

#### <a name="reference"></a>Referencia

            void SendEvent(string name, Dictionary<object, object> extras = null)

Eventos independiente pueden ocurrir fuera del contexto de una sesión.

#### <a name="example"></a>Ejemplo

            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>Eventos de sesión

#### <a name="reference"></a>Referencia

            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Eventos de sesión se usan normalmente para informar de las acciones realizadas por un usuario durante su sesión.

#### <a name="example"></a>Ejemplo

**Sin datos:**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");
            
            // or
            
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Con los datos:**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>Eventos de la tarea

#### <a name="reference"></a>Referencia

            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Eventos de la tarea se usan normalmente para informar de las acciones realizadas por un usuario durante un trabajo.

#### <a name="example"></a>Ejemplo

            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

##<a name="reporting-errors"></a>Informes de errores

Hay tres tipos de errores:

-   Errores de independiente
-   Errores de sesión
-   Errores de trabajo

### <a name="standalone-errors"></a>Errores de independiente

#### <a name="reference"></a>Referencia

            void SendError(string name, Dictionary<object, object> extras = null)

Contra errores de sesión, pueden producirse errores de independiente fuera del contexto de una sesión.

#### <a name="example"></a>Ejemplo

            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>Errores de sesión

#### <a name="reference"></a>Referencia

            void SendSessionError(string name, Dictionary<object, object> extras = null)

Errores de sesión se usan normalmente para informar de los errores que afectan a los usuarios durante su sesión.

#### <a name="example"></a>Ejemplo

            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>Errores de trabajo

#### <a name="reference"></a>Referencia

            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Errores pueden deberse a un trabajo en ejecución en lugar de relacionadas a la sesión de usuario actual.

#### <a name="example"></a>Ejemplo

            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

##<a name="reporting-crashes"></a>Informe de errores

El agente proporciona dos métodos para tratar los bloqueos.

### <a name="send-an-exception"></a>Enviar una excepción

#### <a name="reference"></a>Referencia

            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>Ejemplo

Puede enviar una excepción en cualquier momento llamando:

            EngagementAgent.Instance.SendCrash(aCatchedException);

También puede usar un parámetro opcional para terminar la sesión de contratación al mismo tiempo que enviar el bloqueo. Para ello, llame al:

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Si lo hace, se cerrará la sesión y trabajos justo después de enviar el bloqueo.

### <a name="send-an-unhandled-exception"></a>Enviar una excepción no controlada

#### <a name="reference"></a>Referencia

            void SendCrash(ApplicationUnhandledExceptionEventArgs e)

Compromiso también proporciona un método para enviar las excepciones no controladas. Esto es especialmente útil cuando se utiliza en el controlador de eventos de silverlight UnhandledException.

Esta se método **siempre** finalizar la sesión de contratación y trabajos después de que se llama.

#### <a name="example"></a>Ejemplo

Puede usarlo para implementar su propio controlador UnhandledException (especialmente si ha deshabilitado la característica de contratación de informes de bloqueos automática). Por ejemplo, en la `Application_UnhandledException` método de la `App.xaml.cs` archivo:

            // In your App.xaml.cs file
            
            // Code to execute on Unhandled Exceptions
            private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
            {
              // your own code
            
              EngagementAgent.Instance.SendCrash(e);
            }

##<a name="onactivated"></a>OnActivated

### <a name="reference"></a>Referencia

            void OnActivated(ActivatedEventArgs e)

Cuando el usuario se desplaza hacia delante, lejos de una aplicación, cuando se produce el evento desactivado, el sistema operativo intentará colocar la aplicación en un estado inactivo. A continuación, la aplicación es desechar. En este proceso se termina una aplicación pero se conservan algunos datos sobre el estado de la aplicación y las páginas individuales dentro de la aplicación.

Deberá insertar `EngagementAgent.Instance.OnActivated(e)` en la `Application_Activated` método desde el archivo App.xaml.cs para restablecer el agente de contratación cuando la aplicación se ha desechado.

### <a name="example"></a>Ejemplo

            // Inside your App.xaml.cs file
            
            // Code to execute when the application is activated (brought to foreground)
            // This code will not execute when the application is first launched
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
              EngagementAgent.Instance.OnActivated(e);
            }

##<a name="device-id"></a>Id. de dispositivo

            String GetDeviceId()

Puede obtener el identificador de dispositivo de contratación llama a este método.

##<a name="extras-parameters"></a>Parámetros adicionales

Datos arbitrarios pueden estar asociados a un evento, un error, una actividad o una tarea. Estos datos pueden ser estructurados mediante un diccionario. Claves y valores pueden ser de cualquier tipo.

Datos adicionales están serie para que si desea insertar su propio tipo en adicionales que tenga que agregar un contrato de datos para este tipo.

### <a name="example"></a>Ejemplo

Crear una nueva clase "Persona".

            using System.Runtime.Serialization;
            
            namespace Engagement.Agent
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }
            
                // Properties
            
                [DataMember]
                public int Age
                {
                  get;
                  set;
                }
            
                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

A continuación, vamos a agregar un `Person` instancia a un archivo extra.

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);
            
            EngagementAgent.Instance.SendEvent("Event", extras);

> [AZURE.WARNING] Si coloca otros tipos de objetos, asegúrese de que su método ToString() se implementa para devolver una cadena legible.

### <a name="limits"></a>Límites

#### <a name="keys"></a>Teclas

Cada clave en el objeto debe coincidir con la siguiente expresión regular:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Significa que teclas deben comenzar con al menos una letra, seguida de letras, dígitos o caracteres de subrayado (\_).

#### <a name="size"></a>Tamaño

Extras están limitados a **1024** caracteres por llamada.

##<a name="reporting-application-information"></a>Información de la aplicación de generación de informes

### <a name="reference"></a>Referencia

            void SendAppInfo(Dictionary<object, object> appInfos)

Puede informar manualmente función seguimiento utilizando la SendAppInfo() información (o cualquier otra información específica de aplicación).

Tenga en cuenta que estas información puede enviarse incrementalmente: solo el último valor de una clave determinada se mantendrán para un dispositivo dado. Como evento adicionales, utilizar un diccionario\<objeto, objeto\> adjuntar información.

### <a name="example"></a>Ejemplo

            Dictionary<object, object> appInfo = new Dictionary<object, object>()
            {
               {"subscription", "2013-12-07"},
               {"premium", "true"}
            };
            
            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>Límites

#### <a name="keys"></a>Teclas

Cada clave en el objeto debe coincidir con la siguiente expresión regular:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Significa que teclas deben comenzar con al menos una letra, seguida de letras, dígitos o caracteres de subrayado (\_).

#### <a name="size"></a>Tamaño

Información de la aplicación están limitados a **1024** caracteres por llamada.

En el ejemplo anterior, la JSON enviado al servidor es 44 caracteres:

            {"subscription":"2013-12-07","premium":"true"}
 
##<a name="logging"></a>Registro de información
###<a name="enable-logging"></a>Habilitar el registro

El SDK se puede configurar para generar los registros de prueba en la consola IDE.
Estos registros no están activados de forma predeterminada. Para personalizar esto, actualice la propiedad `EngagementAgent.Instance.TestLogEnabled` a uno de los valores de la `EngagementTestLogLevel` enumeración, por ejemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();
