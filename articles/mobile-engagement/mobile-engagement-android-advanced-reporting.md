<properties
    pageTitle="Opciones avanzadas de generación de informes para Azure Mobile compromiso Android SDK"
    description="Describe cómo hacer reporting avanzado para capturar el análisis de Azure Mobile compromiso Android SDK"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-reporting-with-engagement-on-android"></a>Informes con compromiso en Android avanzados

> [AZURE.SELECTOR]
- [Windows universal](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-reporting.md)

Este tema describen los escenarios de informes adicionales en la aplicación de Android. Puede aplicar estas opciones a la aplicación que creó en el tutorial de [Introducción](mobile-engagement-android-get-started.md) .

## <a name="prerequisites"></a>Requisitos previos

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

El tutorial que completado era deliberadamente directo y simple, pero son avanzadas de opciones que puede elegir.

## <a name="modifying-your-activity-classes"></a>Modificar su `Activity` clases

En el [tutorial de introducción](mobile-engagement-android-get-started.md), tenía que fue que su `*Activity` subclases heredan de los correspondientes `Engagement*Activity` clases. Por ejemplo, si su actividad heredado extendido `ListActivity`, le resulte extender `EngagementListActivity`.

> [AZURE.IMPORTANT] Cuando se usa `EngagementListActivity` o `EngagementExpandableListActivity`, asegúrese de que cualquier llamada a `requestWindowFeature(...);` se realiza antes de la llamada a `super.onCreate(...);`, en caso contrario, se produce un error.

Puede encontrar estas clases en la `src` carpeta y puede copiarlos en su proyecto. Las clases también están en el **JavaDoc**.

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Método alternativo: llamar `startActivity()` y `endActivity()` manualmente

Si no puede o no desea sobrecarga su `Activity` clases, en su lugar puede iniciar y terminar las actividades llamando el `EngagementAgent`de métodos directamente.

> [AZURE.IMPORTANT] El SDK Android nunca llama la `endActivity()` método, incluso cuando se cierra la aplicación (en Android, aplicaciones nunca se cierran). Por tanto, es *muy* recomendada llamar a la `startActivity()` método en la `onResume` devolución de llamada de *todos los* sus actividades y la `endActivity()` método en la `onPause()` devolución de llamada de *todos los* sus actividades. Esta es la única manera de asegurarse de que no se pierden sesiones. Si se pierde una sesión, el servicio de contratación nunca se desconecta del servidor de contratación (ya que el servicio permanece conectado mientras está pendiente una sesión).

Aquí tenemos un ejemplo:

    public class MyActivity extends Some3rdPartyActivity
    {
      @Override
      protected void onResume()
      {
        super.onResume();
        String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
        EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
      }

      @Override
      protected void onPause()
      {
        super.onPause();
        EngagementAgent.getInstance(this).endActivity();
      }
    }

En este ejemplo es similar a la `EngagementActivity` clase y sus variantes, cuyo código fuente está disponible en la `src` carpeta.

## <a name="using-applicationoncreate"></a>Usar Application.onCreate()

Cualquier código que se colocan en `Application.onCreate()` y en otra aplicación devoluciones de llamada se ejecuta para los procesos de toda la aplicación, incluido el servicio de contratación. Puede tener efectos secundarios no deseados, como las asignaciones de memoria innecesarias y subprocesos en el proceso de contratación, o receptores de difusión duplicados o servicios.

Si se reemplaza `Application.onCreate()`, se recomienda agregar el fragmento de código siguientes al principio de la `Application.onCreate()` función:

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

Puede hacer lo mismo para `Application.onTerminate()`, `Application.onLowMemory()`, y `Application.onConfigurationChanged(...)`.

También puede ampliar `EngagementApplication` en lugar de ampliación `Application`: la devolución de llamada `Application.onCreate()` realiza la comprobación de proceso y llamadas `Application.onApplicationProcessCreate()` solo si el proceso actual no es el servicio de contratación de hospedaje, se aplican las mismas reglas de otras devoluciones de llamada.

## <a name="tags-in-the-androidmanifestxml-file"></a>Etiquetas en el archivo AndroidManifest.xml

En la etiqueta de servicio en el archivo AndroidManifest.xml, el `android:label` atributo le permite elegir el nombre del servicio de contratación tal y como aparece a los usuarios finales de la pantalla "Ejecutar servicios" de su teléfono. Se recomienda establecer este atributo en `"<Your application name>Service"` (por ejemplo, `"AcmeFunGameService"`).

Especificar el `android:process` atributo garantiza que se ejecuta el servicio de contratación en su propio proceso (ejecución compromiso en el mismo proceso como la aplicación hace que el subproceso de la interfaz de usuario principal responde potencialmente menos).

## <a name="building-with-proguard"></a>Creación con ProGuard

Si crea el paquete de aplicación con ProGuard, debe conservar algunas clases. Puede usar el siguiente fragmento de configuración:

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
    }
