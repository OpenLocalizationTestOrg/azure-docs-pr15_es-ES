<properties 
   pageTitle="Azure compromiso móvil guía - inserción/alcance de la solución de problemas" 
   description="Solución de problemas de interacción y la notificación de usuario en Azure Mobile compromiso" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-push-and-reach-issues"></a>Guía para la solución de inserción y llegar a problemas

Los siguientes son posibles problemas que puede encontrarse con cómo Azure Mobile compromiso envía información a los usuarios.
 
## <a name="push-failures"></a>Errores de inserción

### <a name="issue"></a>Problema
- Push no funciona (en la aplicación, fuera de la aplicación, o ambos).

### <a name="causes"></a>Causas
- Un error de inserción muchas veces es una indicación que Azure Mobile compromiso, alcance u otra característica avanzada de Azure Mobile contratación no está correctamente integrada o que se requiere una actualización en el SDK para solucionar un problema conocido con una nueva plataforma de sistema operativo o dispositivo.
- Probar solo una inserción en la aplicación y una inserción de fuera de la aplicación para determinar si algo es un problema en la aplicación o fuera de la aplicación.
- Prueba de la interfaz de usuario y la API como un paso de solución de problemas para ver qué información de error adicional está disponible ambos lugares.
- En la aplicación Push no funcionará a menos Azure Mobile compromiso y alcance se integren en el SDK.
- Push no funcionará si certificados no son válidos, o usan ord frente desarrollo correctamente (solo en iOS). (**Nota:** "fuera de la aplicación" notificaciones de inserción pueden no entregarán a iOS, si tiene el desarrollo (desarrollo) y versiones de producción (Ord) de la aplicación instalada en el mismo dispositivo, ya que el token de seguridad asociada con el certificado que se pueden invalidar por Apple. Para resolver este problema, desinstale las versiones de producción y de desarrollo de la aplicación y volver a instalar sólo la versión en el dispositivo.)
- Fuera de inserción de aplicación recuentos son distintas en distintas plataformas (iOS muestra menos información que Android si inserta nativa está deshabilitado en un dispositivo, la API puede proporcionar más información de la interfaz de usuario en estadísticas de inserción).
- En la aplicación Push puede estar bloqueado por los clientes en el nivel de sistema operativo (iOS y Android).
- En la aplicación Push se mostrará como deshabilitada en la interfaz de usuario de Azure Mobile compromiso si no se integra correctamente, pero puede fallar silenciosamente desde la API.
- En la aplicación Push no funcionará a menos Azure Mobile compromiso y alcance se integren en el SDK.
- Inserta GCM y ADM no funcionará a menos que se integran compromiso de Azure móvil y el servidor específico en el SDK (solo Android).
- En la aplicación y fuera de la aplicación inserta debe comprobarse por separado para determinar si se trata de un problema de inserción o alcance.
- En la aplicación inserta requiere que la aplicación estén abiertos para recibir.
- En la aplicación Push es a menudo configurar una etiqueta de información de la aplicación opcionales u optar por no va a filtrar.
- Si utiliza una categoría personalizada en alcance para mostrar las notificaciones de la aplicación, debe seguir el ciclo de vida correcto de la notificación, o bien no se puede borrar la notificación cuando el usuario descartarla.
- Si inicia una campaña sin fecha final y un dispositivo recibe la aplicación en notificación pero no no mostrar que todavía, el usuario seguirán recibir la notificación la próxima vez que inicie sesión en la aplicación, incluso si manualmente finalizar la campaña.
- Problemas con la API de inserción, confirme que realmente desea usar la API de inserción en lugar de la API alcance (desde la API alcance se usa con más frecuencia) y que no son confundir los parámetros "carga" y "notificador".
- Pruebe su campaña de inserción con ambos un dispositivo conectado a través de Wi-Fi y 3G para eliminar la conexión de red como un origen de posible problemas.

## <a name="push-testing"></a>Inserción de pruebas

### <a name="issue"></a>Problema
- Inserta puede enviarse a un dispositivo específico en función de un identificador de dispositivo.

### <a name="causes"></a>Causas

- Prueba dispositivos están configurados diferente para cada plataforma, pero deberían funcionar causando un evento en la aplicación en un dispositivo de prueba y busca el identificador de dispositivo en el portal para encontrar el identificador de dispositivo de todas las plataformas.
- Dispositivos de prueba funcionan de forma diferente con IDFA frente a IDFV (solo en iOS).


## <a name="push-customization"></a>Personalización de inserción

### <a name="issue"></a>Problema
- Avanzadas de inserción de contenido de elemento no funcionará (identificación, llamar, vibración, imagen, etcetera.).
- Vínculos de Push no funcionan (fuera de la aplicación, en la aplicación a un sitio Web, a una ubicación de la aplicación).
- Estadísticas de inserción muestran que push no se envió a tantas personas como se esperaba (demasiados o no es suficiente).
- Insertar duplicado y recibido dos veces.
- No se puede registrar el dispositivo de prueba de Azure Mobile compromiso inserta (con su propia aplicación de producción o desarrollo).

### <a name="causes"></a>Causas

- Para establecer un vínculo a una ubicación específica de la aplicación requiere "categorías" (solo Android).
- Combinaciones de vinculación profundas para redirigir a los usuarios a una ubicación alternativa al hacer clic en una notificación de inserción que necesite creado en y administrado por la aplicación y el dispositivo OS no por compromiso Mobile directamente. (**Nota:** en la aplicación de las notificaciones no se pueden vincular directamente a en ubicaciones de la aplicación con iOS con Android.)
- Servidores de imágenes externas deben ser capaces de utilizar HTTP "GET" y "" para panorama inserta trabajar (solo Android).
- En el código, puede desactivar al agente de Azure Mobile compromiso cuando se abre el teclado y tiene su código volver a activar al agente de Azure Mobile compromiso cuando se cierra el teclado para que el teclado no afectan a la apariencia de la notificación (solo en iOS).
- Algunos elementos no funcionan en simulaciones de prueba, pero sólo las campañas reales (identificación, llamar, vibración, imagen, etcetera.).
- No hay datos en el servidor está conectados al usar el botón "probar" Push. Datos solo se registran para las campañas de inserción real.
- Para ayudar a aislar el problema, solucionar problemas con: prueba, simular y una campaña real, ya que cada uno de ellos funciona de forma ligeramente diferente.
- La longitud de tiempo que su "en la aplicación" y campañas de "cualquier hora" están programadas para ejecutarse puede afectar a los números de entrega desde una campaña sólo se entregarán a los usuarios que están "en la aplicación" mientras se ejecuta la campaña (y los usuarios que tienen su configuración de dispositivo configurado para recibir notificaciones de "fuera de la aplicación").
- Las diferencias entre cómo controlar los Android y iOS de notificaciones de la aplicación dificulta comparar directamente las estadísticas de inserción entre la versión de iOS y Android a de la aplicación. Android proporciona más información de notificación de nivel de sistema operativo que no iOS. Android informes cuando una notificación nativa recibida, hacer clic o eliminada en el centro de notificaciones, pero iOS no enviar esta información a menos que se hace clic en la notificación. 
- El motivo principal que números "presionados" son diferentes diferentes de números "entregado" para llegar a las campañas que "en la aplicación" y "fuera de la aplicación" notificaciones se cuentan diferente. "En la aplicación" notificaciones se administran por compromiso de móvil, pero "fuera de la aplicación" notificaciones se administran por el centro de la notificación en el sistema operativo de su dispositivo.

## <a name="push-targeting"></a>Identificación de inserción

### <a name="issue"></a>Problema
- Integrado en identificación no funciona como se esperaba.
- Identificación de la etiqueta en la información de la aplicación no funciona como se esperaba.
- Identificación de la ubicación geográfica no funciona como se esperaba.
- Opciones de idioma no funcionan como se esperaba.

### <a name="causes"></a>Causas

- Asegúrese de que ha cargado etiquetas de información de aplicación a través de la interfaz de usuario de Azure Mobile compromiso o API.
- Limitación de la velocidad de inserción o la cuota de inserción en el nivel de aplicación o la limitación de la audiencia en el nivel de campaña puede impedir que a una persona recibe una inserción específicos incluso si se cumplen los criterios de identificación. 
- Establecer un "idioma" es diferente de la identificación basada en país o configuración regional, que también es diferente de identificación según ubicación geográfica basada en una ubicación de teléfono o GPS.
- El mensaje en el idioma de"predeterminado" se envía a los clientes que no tienen su dispositivo establecido en uno de los idiomas alternativos que especifique.


## <a name="push-scheduling"></a>Programación de inserción

### <a name="issue"></a>Problema
- Programación de inserción no funciona como se esperaba (envía demasiado anticipado o retrasada).

### <a name="causes"></a>Causas

- Zonas horarias puede problemas con la programación, especialmente cuando se usa la zona horaria de los usuarios finales.
- Características avanzadas de inserción pueden retrasar Push.
- Identificación basado en teléfono puede retrasar la configuración (en lugar de etiquetas de la información de aplicación) inserta desde Azure Mobile compromiso deba solicitar datos desde el tiempo real de teléfono antes de enviar una inserción.
- Campañas creadas sin una fecha de finalización almacenan la inserción localmente en el dispositivo y mostrar la próxima vez que se abre la aplicación incluso si manualmente se finaliza la campaña.
- Iniciar campaña de más de una al mismo tiempo puede tardar más tiempo para analizar su base de usuarios (intente solo iniciar una campaña a la vez con un máximo de cuatro, también destino solo a los usuarios activos para que los usuarios antiguos no tienen que analizar).
- Si utiliza la opción "Omitir audiencia, inserción se enviarán a los usuarios a través de la API" en la sección "Campaña" de una campaña de alcance de la campaña no enviará automáticamente, debe enviar de forma manual a través de la API de alcance.
- Si utiliza una categoría personalizada en alcance para mostrar las notificaciones de la aplicación, es necesario que siga el ciclo de vida correcto de una notificación, o bien no se puede borrar la notificación cuando el usuario descartarla.

 
