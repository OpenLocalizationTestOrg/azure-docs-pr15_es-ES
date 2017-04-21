<properties 
    pageTitle="Compromiso móvil Azure Guía de introducción con las mejores prácticas"
    description="Introducción de Azure Mobile compromiso y los procedimientos recomendados para integrado" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="wesmc7777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="10/04/2016"
    ms.author="wesmc;ricksal"/>

# <a name="azure-mobile-engagement---getting-started-guide-with-best-practices"></a>Azure compromiso móvil - Guía de inicio rápido con las mejores prácticas

## <a name="overview"></a>Información general

**La pantalla móvil es un espacio muy lleno:** En 2013, un práctico encontró que el dispositivo móvil promedio tenía 27 aplicaciones instaladas. Los usuarios normalmente dedicaban 30 horas al mes a sus aplicaciones. La mayoría de esta vez se ha invertido en redes sociales y juegos (alrededor de 20 horas). 2014, Android market tenía alrededor de 1,5 millones de aplicaciones para los usuarios que elegir. El almacén de Apple contenido alrededor de 1,2 millones aplicaciones. Uso de la aplicación móvil todavía aumenta a medida que los desarrolladores compitan en este mercado en crecimiento. 

El usuario móvil promedio instalar y desinstalar aplicaciones con alta frecuencia dependiendo de intereses y experiencias de la aplicación. Para determinar el éxito de una aplicación es fundamental saber más que simplemente cuántos usuarios instalan la aplicación. Es importante saber cómo útil la aplicación esté y si está cambiando esa tendencia de uso. Se convierten en las siguientes preguntas importantes:

- ¿Son los usuarios desde el principio hasta encontrar la aplicación obsoleta o no le interesen? 
- ¿Cuántos usuarios han dejado de usar la aplicación en absoluto? 
- ¿En la aplicación de compras tendencia se hacia arriba o hacia abajo?
- ¿No se los usuarios pueden completar los flujos de trabajo debido a problemas con la aplicación o la falta de interés? 
- ¿Puede mantener su aplicación útiles y pertinentes presionando contenido actualizado a la base de usuario? 
- ¿Debería este contenido actualizado que ser el mismo para todos los usuarios o centrado en segmentos de usuarios en función de comportamiento de la aplicación? 
 
Respuestas a preguntas similares a éstos podrían ayudan a ampliar la duración y los ingresos desde la aplicación. También pueden ayudarle definir y conservar su base de usuarios. 

Medios relacionados con aplicaciones suelen tener algunos de la retención mayor entre los usuarios. Uno de los motivos es constantemente proporcionan contenido actualizado a los usuarios. Adopción temprana de las notificaciones de inserción útil dirigida a un segmento de usuarios tiende a tener un gran impacto en retención de aplicación. 

El programa de Azure Mobile compromiso está diseñado para ayudarle a ampliar la duración y retención de la aplicación proporcionando un método para recopilar y analizar información detallada sobre el uso de la aplicación. Le ayudará a clasificar su base de usuarios según el comportamiento y a continuación, crear campañas que tiene el foco para entregar notificaciones de inserción y mensajes de la aplicación a los segmentos de usuario identificado. Indicadores clave de rendimiento (KPI) medir cuán activo son de los usuarios con diferentes aspectos de la aplicación. Compromiso de Azure Mobile proporciona los métodos que necesita determinar estos KPI. Ayuda a aumentar el retorno de la inversión (ROI) al proporcionar la infraestructura que necesita para aumentar el compromiso con la aplicación móvil. 

Para obtener el máximo provecho de compromiso de móvil de Azure, debe comenzar con un plan de contratación bien diseñada. Su plan le ayudará a identificar los datos granulares que necesitará segmentar su base de usuario. Esto se puede basar en el comportamiento y experiencias de la aplicación. Fin de su plan para tener éxito, es recomendable definir claramente el KPI que mide los objetivos de la aplicación. Con indicadores de rendimiento borrar definidos, puede insertar fácilmente la lógica necesaria en su aplicación para recopilar datos minucioso que usará para analizar y evaluar los KPI. Este tema es una guía de prácticas recomendada para definir los KPI que desea usar con su plan de contratación. 


## <a name="step-1-define-your-kpis-to-fit-the-bet-model"></a>Paso 1: Definir los KPI para ajustar el modelo de opción


Definir correctamente KPI puede ser una tarea difícil para completar. Aplicaciones diseñadas para diferentes industrias tienen sus propias características específicas y los objetivos. Esto puede suelen confundir el enfoque. Para evitar esto, objetivos y KPI debe se pueden clasificar en tres categorías principales: **empresa**, **contratación**y **técnicos**. Esto es lo que llamamos el **modelo de opción**.

Un buen plan generalmente tendrán los objetivos con los KPI que miden el éxito de cada una de las siguientes categorías del modelo de opción.


#### <a name="business-kpis"></a>KPI de empresa

KPI de empresa debe ser el elemento más sencillo de crear. Probablemente ya definido en alguna forma al planificar la aplicación móvil. Estos KPI ayuda generalmente medida ingresos y ROI aplicación. La siguiente lista proporciona algunos KPI de empresas que pueden ayudarle a guían al definir los indicadores de rendimiento de ejemplo:

- KPI de empresa de medios
    - Hacer clic en número de anuncios
    - Número de página visitas por usuario
    - Número de suscripciones actuales
- KPI de empresa de juegos
    - Número de compras de la aplicación
    - Ingresos medios por usuario (ARPU)
    - Tiempo dedicado por sesión
    - Días reproduzca y actuales en el nivel de juegos
- KPI de empresa de comercio electrónico
    - Aplicación de días que se usan
    - Ingresos medios por usuario (ARPU)
    - Promedio de carro durante la desprotección
    - Categoría de producto para la mayoría de las vistas y compras
- KPI de empresas de seguros y banco
    - Número de cuentas
    - Características activadas
    - Oferta visitados
    - Alertas de hacer clic en él o activado      



#### <a name="engagement-kpis"></a>KPI de contratación

Un KPI de contratación es un indicador de rendimiento para medir la participación de los usuarios. Tendencias en esta área ayudan a determinar la retención de la aplicación. Estos son algunos indicadores de rendimiento de ejemplo para este tipo de KPI:

- Usuarios activos en los últimos 7 días
- Recuento de usuario inactivo durante los últimos 7 días
- Número de usuarios que no se ha utilizado la aplicación de 30 días.  

Algunos factores obvios externos que pueden influir indicadores en esta área. Por ejemplo, puede considerar un dispositivo móvil para ser con un usuario en cualquier momento. Esto puede o no puede ser true. Una aplicación de juegos podría suelen tener un mayor uso alrededor de días festivos cuando un jugador puede que se reproduzca más al día libre o fuera de la escuela.   

Bien definidos KPI en esta categoría le ayudarán a medir la relación entre la aplicación y sus clientes.



#### <a name="technical-kpis"></a>Técnica KPI

Indicadores de rendimiento en esta categoría ayudarle a determinar si la aplicación se comporta correctamente o se bloquea, bloquea. Estos indicadores pueden medir el estado de la aplicación y determinar los problemas de uso que pueden impedir que los usuarios con la aplicación. Información recopilada para esta categoría también podría contener información de rendimiento que sería relevante para equipos de marketing. Los datos también pueden ser útiles para solucionar problemas de TI y equipos para identificar los errores no notificados de soporte técnico. 
 
Estos son algunos ejemplos de técnicas de KPI:

- Información de excepción no controlada o controlado y recuento 
- Marca de hora de última bloqueo
- Haga clic en botón último o la última página visitada
- Uso de memoria de la aplicación
- Tasa de marco de aplicación
- Versión del sistema operativo que se está ejecutando la aplicación
- Versión de aplicación

Definir estos KPI para medir el rendimiento de aplicación y detectar posibles errores. Este indicadores deben ayudar a reducir el tiempo que necesita para ofrecer una solución para sus clientes. Podría también le permite definir un segmento de usuarios que ha encontrado un determinado problemas. Puede usar la segmentación de usuario para crear campañas para ofrecer notificaciones sobre promociones posibles y las soluciones disponibles para ayudar a recuperar la satisfacción del cliente. 


#### <a name="playbook-exercise-1-create-your-kpi-dashboard"></a>Guía de ejercicio 1: Crear el panel de KPI

Al definir la estrategia de marketing, los KPI deben presentar una vista para cada uno de los objetivos principales. Deben ser puntos de datos claramente definidas que le permitirá recopilar información esencial para supervisar la aplicación y el comportamiento del usuario final.

Crear un panel KPI que contiene la siguiente información

1.  ¿Cuáles son los KPI para la aplicación?
2.  ¿Los datos de destino tendrá usar para representar cada KPI?
3.  ¿Dónde se encuentran estos datos para mi aplicación (es decir, pantalla, configuración, sistema...)?
4.  ¿Puedo reproducir una secuencia de contratación para este KPI?

Puede usar la hoja de cálculo del **Generador de KPI** en nuestra [Plantilla de la Guía de medios] [ Media Playbook link] para obtener ejemplos e instrucciones.



## <a name="step-2-your-engagement-program"></a>Paso 2: El programa de contratación


Un programa de gran compromiso móvil se debe considerar un componente clave de la aplicación. Estrictamente debe incluir un programa Bienvenido excelente que se ejecuta para un usuario durante los primeros días de uso de la aplicación. Esto suele tener un efecto muy positivo de contratación y retención de la aplicación. Los estudios demuestran que la mayoría de los usuarios dejen de usar una aplicación los primeros días después de la instalación. Desea esfuércese por cumpla o supere el interés del cliente esperadas conducción temprano mientras el usuario aún tiene el foco en la aplicación. Asegúrese de que presentar el valor de clave y las ventajas de la aplicación a sus clientes. 


![](./media/mobile-engagement-getting-started-best-practices/unsegmented-push-notifications.png)

Notificaciones de inserción son el mejor método para compromisos anticipados con usuarios de dispositivos móviles. Sin embargo, excelente debería tener cuidado al segmentar usuarios para las notificaciones de inserción. Porque una vez que un usuario se siente como si recibe correo no deseado o notificaciones no le interesen, puede tener efecto grave. En pocos clics, un usuario puede eliminar la aplicación nunca para devolver. El usuario debe recibir altamente personalizada valor de la aplicación en lugar de genérico correo no deseado.

Una vez que los usuarios realizan conocidas, a continuación, el programa de contratación puede ayudar a otros aspectos de la aplicación de la unidad.

Por ejemplo, puede configurar una campaña que solicita a los usuarios para puntuar la aplicación activos. Dado que este segmento de usuarios es el más activo y tiene la experiencia con la la mayoría de aplicación, cabría esperar para dar a la clasificación más precisa. Una vez que tenga una clasificación de aplicación alta, puede ayudar a incrementar la descarga de la aplicación así reducir los costes de adquisición de cliente nuevo ecológica.



#### <a name="engagement-sequence"></a>Secuencia de contratación


Un programa de compromiso global incluye secuencias de contratación diferente. Cada secuencia útiles para llegar a varios objetivos.


###### <a name="life-push-sequence"></a>Secuencia de inserción de vida


Los objetivos de una secuencia de inserción de vida son diferentes según el ciclo de vida de contratación del usuario con la aplicación. Un usuario concreto puede ser nuevo, inactivo o muy activo. En diferentes etapas del ciclo de vida de un compromiso, los usuarios pueden beneficiarse de su contenido actualizado en el formulario de sugerencias o vínculos a documentación. 

Por ejemplo un nuevo usuario necesita ayuda introducción orientada a una aplicación o beneficiarse de un nuevo incentivos de usuario similar al siguiente la primera vez que inicien la aplicación...

*"Gusto tener incorporado! Recuerde que debe iniciar sesión para obtener el mes 1 gratuito."*


###### <a name="behavioral-push-sequence"></a>Secuencia de comportamiento de inserción

La secuencia de inserción comportamiento pretende aumentar uso en función de comportamiento de usuario recopilado para la aplicación.  

Por ejemplo, un usuario muy activo de una aplicación de fútbol fantásticos beneficiarse participar con la siguiente notificación de inserción...

*"Juan es un ventilador de fútbol graves! Inicie sesión en la sección NFL y ganar acceso gratuito a la SuperBowl!"*


###### <a name="alerting-push-sequence"></a>Secuencia de inserción de alertas

Los usuarios apreciarán noticias relevantes centradas en sus intereses. Una secuencia de inserción alerta mejora compromiso mediante el envío de alertas basadas en intereses claramente ha mostrado un usuario. Esto podría ser explícita cuando un usuario selecciona sus propios intereses en la aplicación. Podría también determinará implícitamente basándose en los datos recopilados durante la interacción del usuario con la aplicación.

Por ejemplo, el usuario de una aplicación de comercio electrónico con regularidad puede comprar una marca específica de café que ha capturado con un KPI de empresa. La siguiente alerta puede mejorar el compromiso de este usuario con la aplicación.
 
*"Hola Wes, uno de sus marcas favoritas de café estará en venta 25% desactivar la primera semana de septiembre de 2015. Agradecemos como un cliente y desea asegurarse de que eran conscientes".*

###### <a name="rentention-push-sequence"></a>Secuencia de inserción de limpieza

Esta secuencia pretende conservar los usuarios con un campañas de notificación de inserción repetitivas para ayudar a impulsar una costumbre normal de atractivos con la aplicación. Esto puede ayudar a aumentar la retención de aplicación si el usuario cuenta con las interacciones. 

Por ejemplo, el usuario de una aplicación relacionada de deportes podría recibir la notificación de inserción siguiente semanales basada en equipos favoritos del usuario:

*"Para que la oportunidad de ganar 200 puntos, vaya voto si New York Yankees se ganan su juego esta semana contra Toronto azul Jays!"*


#### <a name="the-3w-approach"></a>El enfoque de 3 w

Dominar las secuencias de inserción diferentes le ayudará a comunicarse con los usuarios finales. Sin embargo, deberá usar el método de 3 w para personalizar las notificaciones. El enfoque de 3 w debe dirigirse a quién, qué y cuándo para cada notificación. Si claramente cumplen estos tres preguntas que notificaciones debe centrados correctamente para el compromiso.

![](./media/mobile-engagement-getting-started-best-practices/who-what-when.png)



###### <a name="who-the-user-segment-that-will-receive-messages"></a>¿Quién: El usuario de segmento que recibirá mensajes

Notificaciones de inserción a los usuarios debe considerar un canal de comunicación muy confidencial. Asegúrese de que las notificaciones que desea enviar a un segmento de usuarios también se aplican a los intereses de ese segmento de usuarios. Notificación de incorrectamente enrutada es muy probable que tienen un efecto negativo sobre un usuario. Puede considerar lo que lleva a la aplicación que se haya desinstalado de correo no deseado. 

Use una combinación de criterios específicos de técnicos y comportamiento al definir segmentos de usuarios que recibirán notificaciones. Un ejemplo sencillo que define un segmento de usuarios podría ser similar a la siguiente instrucción:

"Todos los usuarios que inicia el una aplicación móvil para la primera vez que hace 3 días y ha visitado la página de inicio de sesión dos veces sin completando realmente un inicio de sesión".
 
Instrucción ayuda a identificar los datos que necesite recopilar para admitir una situación específica.


###### <a name="what-the-message-that-you-will-send"></a>¿Qué: El mensaje que se va a enviar

**Tono**

En los compromisos use un tono que es adecuado para su para sus usuarios segmentados. Definitivamente, esto es una buena manera de conectar con los usuarios finales y aumentar el nivel de interés de un usuario en la aplicación. 

**Redireccionamiento**

Una notificación de inserción puede usarse para abrir más de la aplicación. Si el mensaje de notificación proporciona un contexto, como la difusión de noticias o una promoción de producto, esta notificación puede vínculo profundo directamente al contenido adecuado dentro de la aplicación. Para ello, debe crear un esquema de la dirección URL para permitir que la aplicación a administrar el redireccionamiento. Al trabajar en las secuencias de contratación, esto es un paso importante que no se debe olvidar.

También se puede administrar el redireccionamiento para otros sistemas. Por ejemplo, con una dirección URL de la acción es posible redirigir a los usuarios finales a muchos otros sistemas, incluidas las siguientes:

- Un sitio Web
- Un buzón de correo electrónico que ha configurado el
- Un cuadro SMS
- Un servicio de acceso telefónico
- Directamente en el almacén de aplicación para la aplicación de clasificación. 

Esto ofrece muchas oportunidades para contratar a los usuarios finales y crear reglas para mejorar el rendimiento de forma automática.


**Formato y contenido**

Diferentes tipos y formatos de notificación de inserción:

1. **Anuncios** : le permite enviar mensajes de publicidad a los usuarios en momentos diferentes (en la aplicación, en la aplicación o siempre).
2. **Sondeos** : habilitado para recopilar información de los usuarios finales indicándole preguntas. Las respuestas están disponibles al crear criterios a los usuarios finales de destino.
3. **Inserta datos** : permite enviar un archivo de datos binarios o base 64 para actualizar la aplicación. La información contenida en Insertar datos se envía a la aplicación para personalizar la experiencia de los usuarios de la aplicación. La aplicación debe diseñarse para admitir los datos en Insertar datos.
4. **Mosaicos (solo en Windows Phone)** : habilitado para utilizar el servicio de notificación de inserción de Microsoft (MPNS) para enviar una notificación de inserción nativa que contiene datos XML (admitido desde SDK versión 0.9.0. La carga final para mosaicos no puede exceder 32 kilobytes.). Aparece el mensaje directamente en el mosaico de la placa.
5. **Vista Web** : una vista web es un emergente con contenido de web. Esta ventana emergente aparece cuando el usuario final hace clic en la notificación de inserción. Una vista web le permite tener más interacción con el usuario final.
 
>[AZURE.NOTE] Asegúrese de que el contenido que se va a enviar como las notificaciones de inserción cumpla con la plataforma respectiva (iOS, Android, Windows) directrices para desarrollar aplicaciones y enviar notificaciones de inserción.

 


###### <a name="when-the-timing-of-your-campaign"></a>Cuándo: Los intervalos de la campaña

¿Si es el mejor momento para activar una campaña desencadenante de notificaciones de inserción? ¿Debe ser manual o automática? ¿Debe lo periódico? Determinar el momento adecuado o frecuencia es fundamental para interactuar con los usuarios con los mejores resultados. Para cada secuencia de contratación y el escenario, debe especificar cuándo estará el mejor momento para enviar notificaciones de inserción. Estos son algunos ejemplos:

![](./media/mobile-engagement-getting-started-best-practices/campaign-timing-examples.png)

Si envía muchas notificaciones diariamente, debe tomar consideración seria que los usuarios que creen sus comunicaciones como correo no deseado. 

Compromiso de Azure Mobile proporciona dos maneras de ayudar a evitar las comunicaciones que se considera como correo no deseado. En primer lugar, use la segmentación preciso para asegurarse que no destino los mismos usuarios. Además, compromiso de Azure Mobile proporciona una característica de "cuota". Esta característica puede limitar las notificaciones enviadas para una campaña. Por ejemplo, establecer una cuota predeterminada en 5 por semana garantiza que un usuario que se incluye como parte del segmento de usuario de campaña recibe las notificaciones de no más de 5 para esa semana.





#### <a name="playbook-exercise-2-create-your-engagement-program"></a>Guía de ejercicio 2: Crear el programa de contratación

Dedique algún tiempo resumir los objetivos y definir las campañas que espera para reproducir usando secuencias específicas. Asegúrese de que aplicar el enfoque de 3 w para las notificaciones de las campañas. 

Usar la hoja de cálculo de **Programa de contratación** en la [Plantilla de la Guía de medios] [ Media Playbook link] para obtener ejemplos e instrucciones.


## <a name="step-3-app-integration"></a>Paso 3: Integración de aplicación


#### <a name="create-a-tag-plan"></a>Crear un plan de etiqueta

Para integrar el compromiso de Azure Mobile en su aplicación debe crear un plan de etiqueta. El plan de etiqueta es la primera piedra del proyecto. Define la relación entre las especificaciones de marketing, el flujo de trabajo de la aplicación y los datos de etiqueta real almacenados en la aplicación para medir KPI. Indica qué análisis podrá ver en el portal. También ayuda a definir segmentos de usuario y a continuación, enviar notificaciones de inserción que tiene el foco involucrar a los usuarios finales. Una vez haya el plan de etiqueta definido, agregando el código integrarlo en su aplicación es sencillo mediante el SDK de compromiso de Azure Mobile.

Un plan de etiqueta no debería etiqueta todo el contenido en una aplicación. Solo debe incluir datos de etiqueta que forma parte de la estrategia de contratación móvil. Es probable que será diverso entre aplicaciones. La [Plantilla de la Guía de medios] [ Media Playbook link] proporcionado por el compromiso de Azure móvil le ayuda a crear un plan de etiqueta con un método determinado. Use la hoja de cálculo del **Plan de la etiqueta** como guía para crear su plan de etiqueta.

Al definir una sección de la etiqueta en la hoja de cálculo, ser muy específicas. Esto es muy importante para evitar confusiones. Detalle cada esperamos escenario en el que se enviará cada etiqueta. Incluya el nombre de la actividad donde está insertada cada etiqueta. Todo esto debe incluirse en la parte de **Informative** de la hoja de cálculo. La hoja de cálculo del plan de etiqueta debe ser la referencia principal para la comprobación de la prueba. 

En la sección de **datos para recopilar** , el equipo de desarrollo debe buscar los tipos, nombres, valores y pares de clave/valor de información adicional necesarios para cada etiqueta que se insertará en la aplicación.

Se recomienda revisar el plan de etiqueta con todos los equipos asociados con el proyecto. Realizar correcciones necesarias y confirmar que todo está desactivada para equipos de desarrollo y marketing.

La hoja de cálculo de la **declaración de trabajo** puede utilizarse para ayudar a Guía de que todos los implicados en el proyecto.


#### <a name="data-types"></a>Tipos de datos

Estos son los tipos comunes de compatibilidad de los datos en Azure Mobile contratación.

###### <a name="devices-and-users"></a>Usuarios y dispositivos

Compromiso de Azure Mobile identifica usuarios por generar un identificador único para cada dispositivo. Este identificador se denomina el identificador del dispositivo (o deviceid). Se genera de manera que todas las aplicaciones que se ejecuten en ese mismo dispositivo comparten el mismo identificador de dispositivo.

###### <a name="sessions-and-activities"></a>Las sesiones y actividades

Una sesión es una instancia de la aplicación que se ejecuta un usuario. La sesión abarca desde el momento en que el usuario inicie la aplicación, hasta que se detenga.

Una actividad es una agrupación lógica de un conjunto de cosas que puede hacer la aplicación durante una sesión. Normalmente, es una pantalla en particular en la aplicación, pero puede ser que cualquier cosa definida por la lógica de la aplicación. Como mínimo debe etiquetar cada pantalla o la actividad de la aplicación. Esto le permitirá comprender la ruta de acceso de usuario.


###### <a name="events"></a>Eventos

Eventos se utilizan para informar de interacción del usuario con la aplicación. Pueden ser acciones instantáneas, como compartir contenido o iniciar un vídeo. Eventos de etiquetado le proporcionará con conjuntos de datos que muestran cómo los usuarios interactuar con la aplicación. 

###### <a name="jobs"></a>Trabajos

Trabajos se usan para informar de acciones que tienen una duración. Incluirían algunos ejemplos:

- Ejecución de llamadas API
- Tiempo de visualización de anuncios
- Duración de las tareas de fondo 
- Duración del proceso de compra
- Ver un vídeo


###### <a name="errors"></a>Errores

Errores se usan para informar de problemas detectados por la aplicación. Por ejemplo, acciones de usuario incorrectos o errores de llamada de API.

###### <a name="application-information"></a>Información de la aplicación

Información de la aplicación (información de la aplicación) se usa para etiquetar datos relacionados con una experiencia de usuario con una aplicación. Se genera por la interacción de un usuario con la aplicación. 

Para una clave de la información de aplicación determinado, compromiso de Azure Mobile sólo realiza un seguimiento del último valor (sin historial). Información de la aplicación revela el estado de la aplicación o los usuarios finales. Por ejemplo el estado de inicio de sesión o grupo de producto favoritos de un usuario.

###### <a name="crash-data"></a>Datos de bloqueo

Bloquee los datos recopilados automáticamente por los errores de aplicación de informes de Mobile compromiso SDK no controlados por la aplicación. Por ejemplo, una excepción no controlada que se produce.


###### <a name="extra-data"></a>Datos adicionales

Tareas, errores, las actividades y eventos pueden mejorarse con parámetros. Se trata de un desarrollador puede proporcionar como datos específicos de la aplicación de información adicional. Esto es importante para definir poseen segmentación. 

Por ejemplo, el valor de una etiqueta "artículo" le permitirá a los usuarios finales de segmento en función de quién ve ese artículo concreto. Sin embargo, puede que no sea suficiente. Puede ser mejor si esa misma etiqueta "artículo" también incluye información adicional, como "news_category" dentro de una actividad. Sería útil para determinar dinámicamente las categorías favoritas para el usuario. 

Información adicional se notifica como un par de clave/valor. En el ejemplo de esta aplicación media, la información adicional para "news_category" sería el valor de esa categoría. Por ejemplo, "deportes", "economía" o "y política".





#### <a name="tag-and-sdk-integration"></a>Integración de etiqueta y SDK 

Para obtener instrucciones paso a paso para integrar el SDK de compromiso de Azure Mobile en su aplicación, siga la documentación de [Integración de SDK de contratación](mobile-engagement-windows-store-integrate-engagement.md) en el sitio Web de Azure. Elija la plataforma de destino de los vínculos en la parte superior de la página.

Se recomienda la creación de proyectos para dos aplicaciones que se basa en Azure Mobile compromiso. Uno para desarrollo y prueba de ensayo y otro para ensayo de producción. Su equipo de TI puede promover después de ensayo de prueba a producción cuando las pruebas de aceptación de usuario son correcta.



#### <a name="user-acceptance-testing-uat"></a>Prueba (UAT) de aceptación de usuario

Prueba de aceptación de usuario (UAT) implica asegurarse de que todo funciona tal y como se ha diseñado. Flujos de trabajo pueden completar y recopilan todos los datos necesarios en función de su plan de etiqueta:
 
- Información de etiquetado debe realizarse in situ según documentados conceptos AZME
- Necesita toda la información se recopila (incluidos Extra info, valor aplicación info)
- Coincidencias de nomenclatura según el Plan de etiqueta de diseño
- No hay ningún duplicados etiquetas enviadas

Probar todos los tipos de comportamiento de notificación que se han incrustado en su aplicación

- Datos de anuncios, sondeos, inserta fuera de la aplicación y de la aplicación
- Vistas de texto o Web
- Notificación de la actualización, categorías



#### <a name="setup"></a>Programa de instalación

Configurar el compromiso de Azure Mobile es muy sencillo. Toda la documentación relacionada con la interfaz de usuario está disponible en el sitio Web de Azure Mobile compromiso, [cómo desplazarse por la interfaz de usuario](mobile-engagement-user-interface-home.md).

Es recomendable empezar con la configuración de las funciones derecha y las pertenencias a roles para los usuarios de su proyecto. Esto le ayuda a administrar el acceso adecuado a la plataforma para todos los usuarios. Pueden incluir los roles:

- Administradores
- Desarrolladores
- Lectores 

Después:
- Registrar su deviceID para probar en su propio dispositivo.
- Vaya a la configuración de su cuenta y configurar la zona horaria para que los gráficos y la hora de entrega de notificación establecida para su zona horaria.
- Vaya a la configuración de la aplicación y registrar la aplicación-"información" debe alcance usuario final de destino.

Para obtener más información sobre cómo ejecutar su primera campaña de notificación de inserción, revise [cómo empezar a usar y administrar Push para llegar a los usuarios finales](mobile-engagement-how-tos.md).



## <a name="conclusion"></a>Conclusión


Programas de contratación son iterativos y continuamente debería mejorar suya como experimentar con lo que funciona mejor para su aplicación. 

Inicialmente, al desarrollar experiencia con compromiso estrategias no intenta crear una estrategia de todo compromiso global. Realizar un enfoque paso a paso, identificar los KPI y cómo aprovecharlas. Estrategia de contratación será único para cada aplicación.

Una vez que ha desarrollado algunas experiencia considere la posibilidad de agregar los siguientes programas de compromiso:

- Seguimiento: Adquirir usuarios y probablemente definir orígenes de recopilación de datos. Compromiso móvil Azure se pueden vincular a orígenes de recopilación de datos. Permite supervisar prestaciones de cada origen. Esta información será interesante para maximizar la inversión de adquisición. 

- A y B pruebas: esta es una parte esencial de los programas de contratación. Cada aplicación tiene su propia información específica. Con A y B pruebas, puede mejorar el programa de contratación.

- Ubicación geográfica: Se trata de una gran oportunidad para las marcas. Gracias a esta característica puede ponerse en el lugar correcto y la hora. Se recomienda comprobar que ha recopilado suficientes datos de comportamiento de usuario final antes de empezar a usar la ubicación geográfica.

- Inserción de datos: inserción de datos es una inserción invisible. Insertar datos permite personalizar la aplicación basada en el comportamiento del usuario final. Por ejemplo, si un segmento de usuarios a menudo consulta productos de alta tecnología, el propietario de la aplicación puede enviar una inserción de datos que se utilizará para personalizar su página de inicio con el contenido de alta tecnología.



## <a name="next-steps"></a>Pasos siguientes

- [Crear una cuenta de Azure Mobile compromiso](mobile-engagement-create.md).
- Visite [definir la estrategia de contratación móvil](mobile-engagement-define-your-mobile-engagement-strategy.md) para obtener más información sobre cómo definir la estrategia de compromiso de móvil.



  

<!--Image references-->


<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
