<properties
    pageTitle="Cómo usar la API de contratación en iOS"
    description="Última iOS SDK: cómo usar la API de contratación en iOS"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />


#<a name="how-to-use-the-engagement-api-on-ios"></a>Cómo usar la API de contratación en iOS

Este documento es un complemento para el documento cómo integrar compromiso en iOS: proporciona en profundidad obtener información detallada sobre cómo usar la API de contratación para informar de las estadísticas de la aplicación.

Tenga en cuenta que si solo desea compromiso para informar de su aplicación sesiones, actividades, se bloquea y obtener información técnica, a continuación, la forma más sencilla es realizar todas las personalizado `UIViewController` objetos heredan de los correspondientes `EngagementViewController` clase.

Si desea hacer algo más, por ejemplo, si necesita informar de eventos específico de la aplicación, los errores y tareas, o si tienes que informar de las actividades de la aplicación de forma distinta de la ha implementado en la `EngagementViewController` clases, a continuación, debe usar la API de contratación.

La API de contratación proporcionada por la `EngagementAgent` clase. Se puede recuperar una instancia de esta clase llamando el `[EngagementAgent shared]` método estático (tenga en cuenta que el `EngagementAgent` objeto devuelto es un singleton).

Antes de las llamadas a API, el `EngagementAgent` objeto debe inicializarse llamando al método`[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];`

##<a name="engagement-concepts"></a>Conceptos de contratación

Los siguientes elementos perfeccionar la [Mobile compromiso conceptos](mobile-engagement-concepts.md) comunes para la plataforma de iOS.

### <a name="session-and-activity"></a>`Session`y`Activity`

Una *actividad* es suele estar asociado a una pantalla de la aplicación, es decir la *actividad* comienza cuando la pantalla se muestra y se detiene cuando se cierra la pantalla: este es el caso cuando se integra el SDK de contratación utilizando la `EngagementViewController` clases.

Pero *actividades* también se pueden controlar manualmente mediante la API de contratación. Esto permite dividir una pantalla dada en varias partes de sub para obtener más detalles sobre el uso de esta pantalla (por ejemplo, para conocidos con qué frecuencia y cuánto tiempo se usan los cuadros de diálogo dentro de esta pantalla).

##<a name="reporting-activities"></a>Actividades de informes

### <a name="user-starts-a-new-activity"></a>Usuario inicia una nueva actividad

            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

Tendrá que llamar al `startActivity()` cada vez que los cambios de la actividad de usuario. La primera llamada a esta función inicia una nueva sesión de usuario.

### <a name="user-ends-his-current-activity"></a>Usuario finaliza su actividad actual

            [[EngagementAgent shared] endActivity];

> [AZURE.WARNING] Se debe **nunca** esta función llamar por usted mismo, excepto si desea dividir un uso de la aplicación en varias sesiones: una llamada a esta función terminaría la sesión actual inmediatamente, por lo tanto, una llamada posterior al `startActivity()` desea iniciar una nueva sesión. Esta función se denomina automáticamente el SDK cuando se cierra la aplicación.

##<a name="reporting-events"></a>Informe de eventos

### <a name="session-events"></a>Eventos de sesión

Eventos de sesión se usan normalmente para informar de las acciones realizadas por un usuario durante su sesión.

**Ejemplo sin datos adicionales:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**Ejemplo con datos adicionales:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### <a name="standalone-events"></a>Eventos de programa independiente

Contraria a eventos de sesión, eventos independiente pueden utilizarse fuera del contexto de una sesión.

**Ejemplo:**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

##<a name="reporting-errors"></a>Informes de errores

### <a name="session-errors"></a>Errores de sesión

Errores de sesión se usan normalmente para informar de los errores que afectan a los usuarios durante su sesión.

**Ejemplo:**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### <a name="standalone-errors"></a>Errores de independiente

Contra errores de sesión, errores independiente pueden utilizarse fuera del contexto de una sesión.

**Ejemplo:**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

##<a name="reporting-jobs"></a>Trabajos de informes

**Ejemplo:**

Suponga que desea notificar la duración de un proceso de inicio de sesión:

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      [... sign in ...]

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### <a name="report-errors-during-a-job"></a>Informe de errores durante un trabajo

Errores pueden deberse a un trabajo en ejecución en lugar de relacionadas a la sesión de usuario actual.

**Ejemplo:**

Suponga que desea notificar un error durante el proceso de inicio de sesión:

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;

        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### <a name="events-during-a-job"></a>Eventos durante un trabajo

Eventos pueden deberse a un trabajo en ejecución en lugar de relacionadas a la sesión de usuario actual.

**Ejemplo:**

Supongamos que tenemos una red social y usamos una tarea al informe de tiempo total durante el que el usuario está conectado al servidor. El usuario puede recibir mensajes de sus amigos, este es un evento de trabajo.

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

##<a name="extra-parameters"></a>Parámetros adicionales

Datos arbitrarios pueden estar asociados a tareas, errores, las actividades y eventos.

Estos datos pueden ser estructurados, utiliza clase de NSDictionary de iOS.

Tenga en cuenta que pueden contener adicionales `arrays(NSArray, NSMutableArray)`, `numbers(NSNumber class)`, `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)` u otro `NSDictionary` instancias.

> [AZURE.NOTE] El parámetro adicional es serie en JSON. Si desea pasar objetos distintos a los que se describió anteriormente, debe implementar el método siguiente en la clase:
>
             -(NSString*)JSONRepresentation;
>
> El método debe devolver una representación JSON del objeto.

### <a name="example"></a>Ejemplo

    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### <a name="limits"></a>Límites

#### <a name="keys"></a>Teclas

Cada clave de la `NSDictionary` debe coincidir con la siguiente expresión regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Significa que teclas deben comenzar con al menos una letra, seguida de letras, dígitos o caracteres de subrayado (\_).

#### <a name="size"></a>Tamaño

Extras están limitados a **1024** caracteres por llamada (una vez codificada en JSON el agente de contratación).

En el ejemplo anterior, la JSON enviado al servidor es 58 caracteres:

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##<a name="reporting-application-information"></a>Información de la aplicación de generación de informes

Puede informar de seguimiento de información (o cualquier otra información específica de aplicación) usando manualmente el `sendAppInfo:` función.

Tenga en cuenta que estas información puede enviarse incrementalmente: solo el último valor de una clave determinada se mantendrán para un dispositivo dado.

Como adicionales de evento, la `NSDictionary` clase se usa para resumir información de la aplicación, tenga en cuenta que las matrices o diccionarios subcarpetas se tratará como cadenas planas (con serialización JSON).

**Ejemplo:**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### <a name="limits"></a>Límites

#### <a name="keys"></a>Teclas

Cada clave de la `NSDictionary` debe coincidir con la siguiente expresión regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Significa que teclas deben comenzar con al menos una letra, seguida de letras, dígitos o caracteres de subrayado (\_).

#### <a name="size"></a>Tamaño

Información de la aplicación están limitados a **1024** caracteres por llamada (una vez codificada en JSON el agente de contratación).

En el ejemplo anterior, la JSON enviado al servidor es 44 caracteres:

    {"birthdate":"1983-12-07","gender":"female"}
