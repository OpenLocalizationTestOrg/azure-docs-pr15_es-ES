<properties
    pageTitle="Web SDK API de Azure compromiso móvil | Microsoft Azure"
    description="Las últimas actualizaciones y procedimientos para el SDK de Web para Azure Mobile contratación"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="piyushjo" />

# <a name="use-the-azure-mobile-engagement-api-in-a-web-application"></a>Usar la API de compromiso de Azure Mobile en una aplicación web

Este documento es una adición al documento que le indica cómo [integrar compromiso de móvil en una aplicación web](mobile-engagement-web-integrate-engagement.md). Proporciona información detallada sobre cómo usar la API de compromiso de Azure Mobile para informar de las estadísticas de la aplicación.

La API de contratación Mobile proporcionada por la `engagement.agent` objeto. Predeterminado Azure Mobile compromiso Web SDK alias es `engagement`. Puede volver a definir este alias de la configuración de SDK.

## <a name="mobile-engagement-concepts"></a>Conceptos de compromiso de móvil

Los siguientes elementos perfeccionar comunes [Mobile compromiso conceptos](mobile-engagement-concepts.md) para la plataforma de web.

### <a name="session-and-activity"></a>`Session`y`Activity`

Si el usuario permanezca inactivo durante más de unos pocos segundos entre dos actividades, secuencia del usuario de actividades se divide en dos sesiones distintas. Estos pocos segundos se denominan el tiempo de espera de sesión.

Si la aplicación web no declara final de actividades de usuario por sí mismo (llamando el `engagement.agent.endActivity` función), el servidor Mobile compromiso expira automáticamente en tres minutos después de la página de la aplicación se cierra la sesión de usuario. Esto se denomina el tiempo de espera de sesión de servidor.

### `Crash`

Informes automatizados de las excepciones de JavaScript no se crean de forma predeterminada. Sin embargo, puede informar bloqueos manualmente mediante la `sendCrash` función (consulte la sección en el informe de errores).

## <a name="reporting-activities"></a>Actividades de informes

Informes de actividad de usuario incluye cuando un usuario inicia una nueva actividad y cuando el usuario finaliza la actividad actual.

### <a name="user-starts-a-new-activity"></a>Usuario inicia una nueva actividad

    engagement.agent.startActivity("MyUserActivity");

Tendrá que llamar al `startActivity()` cambios de actividad de los usuarios cada vez. La primera llamada a esta función inicia una nueva sesión de usuario.

### <a name="user-ends-the-current-activity"></a>Usuario finaliza la actividad actual

    engagement.agent.endActivity();

Tendrá que llamar al `endActivity()` al menos una vez cuando el usuario termina su última actividad. Así se informa el SDK de Mobile compromiso Web que el usuario está actualmente inactivo y que la sesión de usuario debe cerrarse después de que expire el tiempo de espera de sesión. Si se llama `startActivity()` antes de que expire el tiempo de espera de sesión, simplemente se reanude la sesión.

Porque no hay ninguna llamada confiable para cuando se cierra la ventana de navegador, suele ser difícil o imposible detectar el final de las actividades de usuario en un entorno web. Que es el motivo por el servidor de Mobile compromiso expira automáticamente en tres minutos después de la página de la aplicación se cierra la sesión de usuario.

## <a name="reporting-events"></a>Informe de eventos

Informar de eventos trata sobre los eventos de sesión e independiente.

### <a name="session-events"></a>Eventos de sesión

Normalmente, los eventos de sesión se utilizan para informar de las acciones realizadas por un usuario durante la sesión del usuario.

**Ejemplo sin datos adicionales:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login');
      // [...]
    }

**Ejemplo con datos adicionales:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login', {user: 'alice'});
      // [...]
    }

### <a name="standalone-events"></a>Eventos de programa independiente

A diferencia de los eventos de sesión, pueden producirse eventos independiente fuera del contexto de una sesión.

Para ello, use ``engagement.agent.sendEvent`` en lugar de ``engagement.agent.sendSessionEvent``.

## <a name="reporting-errors"></a>Informes de errores

Informe de errores trata sobre errores de sesión e independiente.

### <a name="session-errors"></a>Errores de sesión

Errores de sesión normalmente se usan para informar de los errores que tienen un gran impacto en el usuario durante la sesión del usuario.

**Ejemplo sin datos adicionales:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short');
      }
      // [...]
    }

**Ejemplo con datos adicionales:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short', {length: 4});
      }
      // [...]
    }

### <a name="standalone-errors"></a>Errores de independiente

A diferencia de los errores de sesión, pueden producirse errores de independiente fuera del contexto de una sesión.

Para ello, use `engagement.agent.sendError` en lugar de `engagement.agent.sendSessionError`.

## <a name="reporting-jobs"></a>Trabajos de informes

Informes en trabajos portadas errores y eventos que se producen durante un trabajo de elaboración de informes y se bloquea.

**Ejemplo:**

Si desea supervisar una solicitud de AJAX, usaría lo siguiente:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
      // [...]
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-errors-during-a-job"></a>Informes de errores durante un trabajo

Errores pueden deberse a un trabajo en ejecución en lugar de a la sesión de usuario actual.

**Ejemplo:**

Si desea notificar un error si se produce un error en una solicitud de AJAX:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        // [...]
        if (xhr.status == 0 || xhr.status >= 400) {
          engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
        }
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-events-during-a-job"></a>Informe de eventos durante un trabajo

Pueden relacionados con los eventos a un trabajo en ejecución en lugar de a la sesión de usuario actual, gracias a la `engagement.agent.sendJobEvent` función.

Esta función funciona exactamente como `engagement.agent.sendJobError`.

### <a name="reporting-crashes"></a>Informe de errores

Usar el `sendCrash` función al informe se bloquea manualmente.

La `crashid` argumento es una cadena que identifica el tipo de bloqueo.
La `crash` argumento normalmente es el seguimiento de pila del bloqueo como una cadena.

    engagement.agent.sendCrash(crashid, crash);

## <a name="extra-parameters"></a>Parámetros adicionales

Puede adjuntar datos arbitrarios a un evento, el error, la actividad o el trabajo.

Los datos pueden ser cualquier objeto JSON (pero no una matriz o tipo simple).

**Ejemplo:**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### <a name="limits"></a>Límites

Límites que se aplican a parámetros adicionales se encuentran en las áreas de expresiones regulares de teclas, tipos de valor y el tamaño.

#### <a name="keys"></a>Teclas

Cada clave en el objeto debe coincidir con la siguiente expresión regular:

    ^[a-zA-Z][a-zA-Z_0-9]*

Esto significa que las claves deben comenzar con al menos una letra seguida de letras, dígitos o caracteres de subrayado (\_).

#### <a name="values"></a>Valores

Valores están limitados a cadena, número y tipos de tipo Boolean.

#### <a name="size"></a>Tamaño

Extras están limitados a 1.024 caracteres por llamada (después de que el SDK de Web Mobile compromiso codifica en JSON).

## <a name="reporting-application-information"></a>Información de la aplicación de generación de informes

Manualmente puede informar de seguimiento de información (o cualquier otra información específica de la aplicación) mediante el uso de la `sendAppInfo()` función.

Tenga en cuenta que esta información se puede enviar incrementalmente. Se mantienen sólo el último valor de una clave específica de un dispositivo específico.

Como evento adicionales, puede usar cualquier objeto JSON para resumir información de la aplicación. Tenga en cuenta que las matrices u objetos secundarios se tratan como cadenas planas (con serialización JSON).

**Ejemplo:**

Este es un ejemplo de código para enviar el género del usuario y la fecha de nacimiento:

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### <a name="limits"></a>Límites

Límites que se aplican a la información de la aplicación se encuentran en las áreas de expresiones regulares para claves y tamaño.

#### <a name="keys"></a>Teclas

Cada clave en el objeto debe coincidir con la siguiente expresión regular:

    ^[a-zA-Z][a-zA-Z_0-9]*

Esto significa que las claves deben comenzar con al menos una letra seguida de letras, dígitos o caracteres de subrayado (\_).

#### <a name="size"></a>Tamaño

Información de la aplicación se limita a 1.024 caracteres por llamada (después de que el SDK de Web Mobile compromiso codifica en JSON).

En el ejemplo anterior, la JSON enviado al servidor es 44 caracteres:

    {"birthdate":"1983-12-07","gender":"female"}
