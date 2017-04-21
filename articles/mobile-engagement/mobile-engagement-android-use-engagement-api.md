<properties
    pageTitle="Cómo usar la API de contratación en Android"
    description="Última SDK Android: cómo usar la API de contratación en Android"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="piyushjo;ricksal" />

#<a name="how-to-use-the-engagement-api-on-android"></a>Cómo usar la API de contratación en Android

Este documento es un complemento para el documento [avanzada Reporting opciones para Android SDK de contratación Mobile](mobile-engagement-android-advanced-reporting.md). Proporciona en profundidad obtener información detallada sobre cómo usar la API de contratación para informar de las estadísticas de la aplicación.

Tenga en cuenta que si solo desea compromiso para informar de su aplicación sesiones, actividades, se bloquea y obtener información técnica, a continuación, la forma más sencilla es que todas las su `Activity` clases secundarias heredan de los correspondientes `EngagementActivity` clase.

Si desea hacer algo más, por ejemplo, si necesita informar de eventos específico de la aplicación, los errores y tareas, o si tienes que informar de las actividades de la aplicación de forma distinta de la ha implementado en la `EngagementActivity` clases, a continuación, debe usar la API de contratación.

La API de contratación proporcionada por la `EngagementAgent` clase. Se puede recuperar una instancia de esta clase llamando el `EngagementAgent.getInstance(Context)` método estático (tenga en cuenta que el `EngagementAgent` objeto devuelto es un singleton).

##<a name="engagement-concepts"></a>Conceptos de contratación

Los siguientes elementos perfeccionar la comunes [Mobile compromiso conceptos](mobile-engagement-concepts.md)para la plataforma de Android.

### <a name="session-and-activity"></a>`Session`y`Activity`

Si el usuario permanece inactivo entre dos *actividades*de más de unos pocos segundos, su secuencia de *actividades* se divide en dos distintas *sesiones*. Estos pocos segundos se denominan el "tiempo de espera de sesión".

Una *actividad* es suele estar asociado a una pantalla de la aplicación, es decir la *actividad* comienza cuando la pantalla se muestra y se detiene cuando se cierra la pantalla: este es el caso cuando se integra el SDK de contratación utilizando la `EngagementActivity` clases.

Pero *actividades* también se pueden controlar manualmente mediante la API de contratación. Esto permite dividir una pantalla dada en varias partes de sub para obtener más detalles sobre el uso de esta pantalla (por ejemplo, para conocidos con qué frecuencia y cuánto tiempo se usan los cuadros de diálogo dentro de esta pantalla).

##<a name="reporting-activities"></a>Actividades de informes

> [AZURE.IMPORTANT] No es necesario informe actividades, como se describe en esta sección, si está utilizando la `EngagementActivity` clase y sus variantes como se explica en cómo integrar compromiso en documento Android.

### <a name="user-starts-a-new-activity"></a>Usuario inicia una nueva actividad

            EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
            // Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

Tendrá que llamar al `startActivity()` cada vez que los cambios de la actividad de usuario. La primera llamada a esta función inicia una nueva sesión de usuario.

Es el mejor lugar para llamar a esta función en cada actividad `onResume` devolución de llamada.

### <a name="user-ends-his-current-activity"></a>Usuario finaliza su actividad actual

            EngagementAgent.getInstance(this).endActivity();

Tendrá que llamar al `endActivity()` al menos una vez cuando el usuario termina su última actividad. Esto informa el SDK de contratación que el usuario está inactivo y que la sesión de usuario es necesario cerrar una vez el tiempo de espera de sesión caducará (si se llama `startActivity()` antes de que expire el tiempo de espera de sesión, simplemente se reanude la sesión).

Es el mejor lugar para llamar a esta función en cada actividad `onPause` devolución de llamada.

##<a name="reporting-events"></a>Informe de eventos

### <a name="session-events"></a>Eventos de sesión

Eventos de sesión se usan normalmente para informar de las acciones realizadas por un usuario durante su sesión.

**Ejemplo sin datos adicionales:**

            public MyActivity extends EngagementActivity {
               [...]
               @Override
               public boolean onPrepareOptionsMenu(Menu menu) {
                  getEngagementAgent().sendSessionEvent("menu_shown", null);
               }
               [...]
            }

**Ejemplo con datos adicionales:**

            public MyActivity extends EngagementActivity {
              [...]
              @Override
              public boolean onMenuItemSelected(int featureId, MenuItem item) {
                Bundle extras = new Bundle();
                extras.putInt("id", item.getItemId());
                getEngagementAgent().sendSessionEvent("menu_selected", extras);
              }
              [...]
            }

### <a name="standalone-events"></a>Eventos de programa independiente

Contraria a eventos de sesión, pueden producirse eventos independiente fuera del contexto de una sesión.

**Ejemplo:**

Suponga que desea que se producen cuando se activa un difusión receptor de eventos de informe:

            /** Triggered by Intent.ACTION_BATTERY_LOW */
            public BatteryLowReceiver extends BroadcastReceiver {
              [...]
              @Override
              public void onReceive(Context context, Intent intent) {
                EngagementAgent.getInstance(context).sendEvent("battery_low", null);
              }
              [...]
            }

##<a name="reporting-errors"></a>Informes de errores

### <a name="session-errors"></a>Errores de sesión

Errores de sesión se usan normalmente para informar de los errores que afectan a los usuarios durante su sesión.

**Ejemplo:**

            /** The user has entered invalid data in a form */
            public MyActivity extends EngagementActivity {
              [...]
              public void onMyFormSubmitted(MyForm form) {
                [...]
                /* The user has entered an invalid email address */
                getEngagementAgent().sendSessionError("sign_up_email", null);
                [...]
              }
              [...]
            }

### <a name="standalone-errors"></a>Errores de independiente

Contra errores de sesión, pueden producirse errores de independiente fuera del contexto de una sesión.

**Ejemplo:**

En el ejemplo siguiente se muestra cómo notificar un error cuando la memoria se baja en el teléfono mientras se ejecuta el proceso de aplicación.

            public MyApplication extends EngagementApplication {

              @Override
              protected void onApplicationProcessLowMemory() {
                EngagementAgent.getInstance(this).sendError("low_memory", null);
              }
            }

##<a name="reporting-jobs"></a>Trabajos de informes

### <a name="example"></a>Ejemplo

Suponga que desea notificar la duración de un proceso de inicio de sesión:

            [...]
            public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has started */
              engagementAgent.startJob("sign_in", null);

              [... sign in ...]

              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="report-errors-during-a-job"></a>Informe de errores durante un trabajo

Errores pueden deberse a un trabajo en ejecución en lugar de relacionadas a la sesión de usuario actual.

**Ejemplo:**

Suponga que desea notificar un error durante el iniciar sesión proceso:

[...] inicio de sesión de anular público (contexto contexto,...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has been started */
              engagementAgent.startJob("sign_in", null);

              /* Try to sign in */
              while(true)
                try {
                  trySignin();
                  break;
                }
                catch(Exception e) {
                  /* Report the error to Engagement */
                  engagementAgent.sendJobError("sign_in_error", "sign_in", null);

                  /* Retry after a moment */
                  sleep(2000);
                }
              [...]
              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="reporting-events-during-a-job"></a>Informe de eventos durante un trabajo

Eventos pueden deberse a un trabajo en ejecución en lugar de relacionadas a la sesión de usuario actual.

**Ejemplo:**

Supongamos que tenemos una red social y usamos una tarea al informe de tiempo total durante el que el usuario está conectado al servidor. El usuario puede estar conectado en segundo plano incluso cuando está utilizando otra aplicación o cuando el teléfono está inactivo, lo que no hay ninguna sesión.

El usuario puede recibir mensajes de sus amigos, este es un evento de trabajo.

            [...]
            public void signin(Context context, ...) {
              [...Sign in code...]
              EngagementAgent.getInstance(context).startJob("connection", null);
            }
            [...]
            public void signout(Context context) {
              [...Sign out code...]
              EngagementAgent.getInstance(context).endJob("connection");
            }
            [...]
            public void onMessageReceived(Context context) {
              [...Notify in status bar...]
              EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
            }
            [...]

##<a name="extra-parameters"></a>Parámetros adicionales

Datos arbitrarios pueden estar asociados a tareas, errores, las actividades y eventos.

Estos datos pueden ser estructurados, utiliza clase de paquete de Android (en realidad, funciona como los parámetros adicionales de color Android). Tenga en cuenta que puede contener un paquete matrices o instancias de otro paquete.

> [AZURE.IMPORTANT] Si coloca en parcelable o serializables parámetros, asegúrese de que sus `toString()` método se implementa para devolver una cadena legible. Clases serializables que contienen los campos no transitorios que no son serializables hará Android bloqueo al llamará`bundle.putSerializable("key",value);`

> [AZURE.WARNING] No se admiten matrices dispersas en parámetros adicionales, es decir, no serie como una matriz. Debe convertirlos en matrices estándares antes de utilizar en parámetros adicionales.

### <a name="example"></a>Ejemplo

            Bundle extras = new Bundle();
            extras.putString("video_id", 123);
            extras.putString("ref_click", "http://foobar.com/blog");
            EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### <a name="limits"></a>Límites

#### <a name="keys"></a>Teclas

Cada clave de la `Bundle` debe coincidir con la siguiente expresión regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Significa que teclas deben comenzar con al menos una letra, seguida de letras, dígitos o caracteres de subrayado (\_).

#### <a name="size"></a>Tamaño

Extras están limitados a **1024** caracteres por llamada (codificada en JSON una vez por el servicio de contratación).

En el ejemplo anterior, la JSON enviado al servidor es 58 caracteres:

            {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##<a name="reporting-application-information"></a>Información de la aplicación de generación de informes

Puede informar de seguimiento de información (o cualquier otra información específica de aplicación) usando manualmente el `sendAppInfo()` función.

Tenga en cuenta que estas información puede enviarse incrementalmente: solo el último valor de una clave determinada se mantendrán para un dispositivo dado.

Como adicionales de evento, la clase de paquete se usa para resumir información de la aplicación, tenga en cuenta que matrices o paquetes secundarias se tratará como cadenas planas (con serialización JSON).

### <a name="example"></a>Ejemplo

Este es un ejemplo de código para enviar la fecha de nacimiento y sexo del usuario:

            Bundle appInfo = new Bundle();
            appInfo.putString("status", "premium");
            appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
            EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### <a name="limits"></a>Límites

#### <a name="keys"></a>Teclas

Cada clave de la `Bundle` debe coincidir con la siguiente expresión regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Significa que teclas deben comenzar con al menos una letra, seguida de letras, dígitos o caracteres de subrayado (\_).

#### <a name="size"></a>Tamaño

Información de la aplicación están limitados a **1024** caracteres por llamada (codificada en JSON una vez por el servicio de contratación).

En el ejemplo anterior, la JSON enviado al servidor es 44 caracteres:

            {"expiration":"2016-12-07","status":"premium"}
