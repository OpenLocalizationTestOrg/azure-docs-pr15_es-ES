<properties
    pageTitle="Ejemplo de MyDriving Azure IoT: inicio rápido | Microsoft Azure"
    description="Introducción a una aplicación que es una demostración completa de cómo diseñar un sistema IoT usando Microsoft Azure, incluidos el análisis de flujo, el aprendizaje y Hubs de evento."
    services=""
    documentationCenter=".net"
    suite=""
    authors="harikmenon"
    manager="douge"/>

<tags
    ms.service="multiple"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="03/25/2016"
    ms.author="harikm"/>

# <a name="mydriving-iot-system-quick-start"></a>Sistema MyDriving IoT: inicio rápido

MyDriving es un sistema que muestra el diseño e implementación de una solución típica de [Internet de cosas](iot-suite-overview.md) (IoT) que reúne telemetría desde dispositivos, procesa esos datos en la nube y a continuación, se aplica a proporcionar una respuesta adaptación de aprendizaje. La demostración de registros de datos sobre los viajes en coche, con los datos desde su teléfono móvil y un adaptador que recopila información del sistema de control de un automóvil. Utiliza estos datos para proporcionar comentarios en el estilo de conducción en comparación con otros usuarios.

El propósito de MyDriving real es empezar a crear su propia solución IoT. Pero antes, vamos a continuar con la aplicación MyDriving propio--como miembro de nuestro equipo de usuario de prueba. Esto le ofrece una experiencia de la aplicación y el sistema detrás como consumidor, antes de seguir con la arquitectura. También presenta HockeyApp, una manera interesante de administración de las distribuciones alfa y beta de las aplicaciones a los usuarios de prueba.

## <a name="use-the-mobile-experience"></a>Utilice la experiencia móvil

Puede usar la aplicación MyDriving si tiene un dispositivo Android, iOS o Windows 10.

### <a name="android-and-windows-10-mobile-installation"></a>Instalación de Windows 10 Mobile y Android

En el dispositivo:

1.  Permitir que las aplicaciones de desarrollo:

    -   Android: En la **configuración de** > de**seguridad**, permitir aplicaciones desde **orígenes desconocidos**.

    -   Windows 10: **Configuración** > **actualizaciones** > **Para desarrolladores**, establezca el **modo para programadores**.

2.  Unirse a nuestro equipo de prueba beta contratar o iniciar sesión en, [HockeyApp](https://rink.hockeyapp.net). HockeyApp facilita la distribuir primeras versiones de la aplicación a los usuarios de prueba.

    Si usa Windows 10, use el Explorador de borde.

    Si se encuentra un asistente de compilación 2016, inicie sesión con el mismo Microsoft cuenta de correo electrónico registrada para la conferencia mediante uno de los botones de Microsoft. Ya está registrado con HockeyApp.

    ![Pantalla de inicio de sesión HockeyApp](./media/iot-solution-get-started/image1.png)

3.  Descargar e instalar la aplicación desde aquí:

    -   [Android](http://rink.io/spMyDrivingAndroid)

    -   [Windows 10](http://rink.io/spMyDrivingUWP)

    Existen dos elementos. Instalar el certificado de **Personas de confianza**. A continuación, instale la aplicación.

*¿Problemas de inicio de la aplicación en Windows 10 Mobile?* El teléfono puede ser una actualización o dos detrás. Asegúrese de que tiene las últimas actualizaciones o instalar:

 - [Microsoft.NET.Native.Framework.1.2.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Framework.1.2.appx) 

 - [Microsoft.NET.Native.Runtime.1.1.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Runtime.1.1.appx) 

 - [Microsoft.VCLibs.ARM.14.00.appx](https://download.hockeyapp.net/packages/win10/Microsoft.VCLibs.ARM.14.00.appx)


### <a name="ios-installation"></a>instalación de iOS

Si había asistido generar 2016, descargue la aplicación como un miembro de nuestro equipo de prueba en HockeyApp:

1.  En su dispositivo iOS, inicie sesión en [HockeyApp](https://rink.hockeyapp.net).
    Use uno de los botones de inicio de sesión de Microsoft e inicie sesión con el mismo Microsoft cuenta de correo electrónico que ha registrado con la conferencia. (No use los campos de correo electrónico y contraseña).

    ![Pantalla de inicio de sesión HockeyApp](./media/iot-solution-get-started/image1.png)

2.  En el panel de HockeyApp, seleccione MyDriving y descargarla.

3.  Autorizar la versión beta de HockeyApp:

    una. Vaya a **configuración** > **General** > **perfiles y administración de dispositivos.**

    b. Confiar en el certificado de **Bits estadio GmbH** .

Si no ha asistir a generar 2016, puede crear e implementar la aplicación usted mismo:

1.   Descargar el código [de GitHub].

2.   Compile e implemente mediante [Xamarin].

Encontrará más detalles en la [Guía de referencia de MyDriving](http://aka.ms/mydrivingdocs).

## <a name="get-an-obd-adapter-optional"></a>Obtener un adaptador DAB (opcional)

Es el elemento que lo hace un sistema de Internet de cosas real. Puede usar la aplicación sin uno, pero es más divertido lo verdadero y no sean muy costosas.

Diagnóstico a bordo (DAB) es la característica de su automóvil que usa el garaje para optimizar su coche y diagnosticar ruidos impares y luces de advertencia. A menos que su coche es de antigüedad de excelente, encontrará un socket en algún lugar de la cabina, normalmente detrás de un paso de la rueda en el panel. Con el conector correcto, puede obtener métricas de rendimiento del motor y realizar algunos ajustes. Puede adquirir un conector DAB económica de los lugares habituales. Conecta mediante Wi-Fi o Bluetooth para una aplicación en su teléfono.

En este caso, vamos a conectar su coche en la nube. La conexión directa desde el DAB es el teléfono, pero nuestra aplicación funciona como una retransmisión. Telemetría del coche se envía directamente al concentrador MyDriving IoT, donde se procesan para iniciar su viajes y evaluar su estilo de conducción.

Para conectar un dispositivo de DAB:

1.  Compruebe que su coche tiene un socket DAB.

2.  Obtener un adaptador DAB:

    -   Si está usando un teléfono Android o Windows, necesita un adaptador Bluetooth habilitado DAB II. Se usa la [Herramienta de análisis de productos de BAFX 34t5 Bluetooth OBDII].

    -   Si está usando un teléfono de iOS, necesita un adaptador de red Wi-Fi y DAB. Hemos usado [ScanTool OBDLink MX Wi-Fi: DAB adaptador/diagnóstico escáner].

3.  Siga las instrucciones que vienen con el adaptador DAB para conectarse a su teléfono. Tenga en cuenta lo siguiente:

    -   Un adaptador Bluetooth debe estar emparejado con el teléfono, en la página de **configuración** .

    -   Un adaptador de red Wi-Fi debe tener una dirección en el rango de 192.168.

4.  Si tiene varias automóviles, puede obtener un adaptador independiente para cada (máximo de tres).

Si no tiene un adaptador de DAB, la aplicación aún enviarán datos de ubicación y la velocidad de receptor GPS del teléfono al back-end y le preguntará si desea simular una DAB.

Puede obtener más información acerca de cómo la aplicación usa datos desde el adaptador DAB y opciones para crear su propio dispositivo DAB en sección 2.1, "Dispositivos IoT" en la [Guía de referencia de MyDriving](http://aka.ms/mydrivingdocs).

## <a name="use-the-app"></a>Usar la aplicación

Inicie la aplicación. Hay un tutorial inicial para mostrarle cómo funciona.

### <a name="track-your-trips"></a>Realizar un seguimiento de los viajes

Puntee en el botón de registro (grande círculo rojo en la parte inferior de la pantalla) para iniciar un viaje y vuelva a pulsar para finalizar.

![Ilustración del botón de registro para seguimiento de ida y vuelta](./media/iot-solution-get-started/image2.png)

Cada vez que inicie un viaje, si no hay ningún dispositivo DAB, se le pedirá si desea usar el simulador.

Al final de un viaje, pulse el botón detener y obtener un resumen.

![Ejemplo de un viaje de resumen](./media/iot-solution-get-started/image3.png)

### <a name="review-your-trips"></a>Revise los viajes

![Ejemplo de un viaje anteriores](./media/iot-solution-get-started/image4.png)

### <a name="review-your-profile"></a>Revise su perfil

![Ejemplo de un perfil de estilo de conducción](./media/iot-solution-get-started/image5.png)

## <a name="send-us-your-test-feedback"></a>Envíenos sus comentarios de prueba

Debido a que se creó MyDriving para ayudarle a comenzar a sus propios sistemas IoT, por supuesto queremos conocer su opinión sobre cómo funciona. Díganos si:

- Ejecutar en dificultades o desafíos.

- Hay un punto de extensión que le resulte más adecuado para su situación.

- Buscar una forma más eficaz para llevar a cabo determinadas necesidades.

- Tiene cualquier otras sugerencias para mejorar MyDriving o esta documentación.

Dentro de la aplicación MyDriving Sí, puede usar el mecanismo de comentarios HockeyApp integrado: iOS y Android, simplemente dar una interrupción de su teléfono, o use el comando de menú de **comentarios** . Automáticamente adjuntará una captura de pantalla para que podemos saber lo está hablando. Y si hay cualquier bloqueos negativa, HockeyApp recopila los registros de bloqueo para decirnos acerca de ellos. También puede enviar comentarios a través del [portal de HockeyApp].

También puede archivar un [problema en GitHub]o deja un comentario siguiente (en-us edition).

¡Esperamos su audiencia!

## <a name="next-steps"></a>Pasos siguientes

-   Explorar la [Guía de referencia de MyDriving](http://aka.ms/mydrivingdocs) para comprender cómo hemos diseñado y creado todo el sistema MyDriving.

-   [Crear e implementar un sistema de su propio](iot-solution-build-system.md) usando las secuencias de comandos del Administrador de recursos de Azure. La [Guía de referencia de MyDriving](http://aka.ms/mydrivingdocs) también le guiará áreas donde deberá realizar la mayoría de las personalizaciones.

  [desde GitHub]: https://github.com/Azure-Samples/MyDriving
  [usar Xamarin]: https://developer.xamarin.com/guides/ios/getting_started/installation/
  [Herramienta de análisis BAFX productos 34t5 Bluetooth OBDII]: http://www.amazon.com/gp/product/B005NLQAHS
  [Analizador de adaptador/diagnóstico DAB ScanTool OBDLink MX Wi-Fi:]: http://www.amazon.com/gp/product/B00OCYXTYY/ref=s9_simh_gw_g263_i1_r?pf_rd_m=ATVPDKIKX0DER&pf_rd_s=desktop-2&pf_rd_r=1MWRMKXK4KK9VYMJ44MP
  [Portal de HockeyApp]: https://rink.hockeyapp.org
  [problema en GitHub]: https://github.com/Azure-Samples/MyDriving/issues
