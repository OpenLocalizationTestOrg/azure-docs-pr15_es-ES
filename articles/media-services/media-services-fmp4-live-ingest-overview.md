<properties 
    pageTitle="Especificación de recopilación de Azure Media Services MP4 fragmentados live | Microsoft Azure" 
    description="Esta especificación describe el protocolo y el formato para fragmentar MP4 en función de recopilación streaming live para Microsoft Azure Media Services. Microsoft Azure Media Services proporciona servicio de transmisión directo que permite a los clientes para transmitir en vivo eventos y difundir contenido en tiempo real con Microsoft Azure como la plataforma de nube. Este documento trata también mejores prácticas en la creación de live altamente redundantes y sólidas recopilación mecanismos." 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"     
    ms.author="cenkdin;juliako"/>

#<a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Especificación de recopilación de Azure Media Services MP4 fragmentados live

Esta especificación describe el protocolo y el formato para fragmentar MP4 en función de recopilación streaming live para Microsoft Azure Media Services. Microsoft Azure Media Services proporciona servicio de transmisión directo que permite a los clientes para transmitir en vivo eventos y difundir contenido en tiempo real con Microsoft Azure como la plataforma de nube. Este documento trata también mejores prácticas en la creación de live altamente redundantes y sólidas recopilación mecanismos.


##<a name="1-conformance-notation"></a>1. notación de conformidad

Las palabras clave "debe", "No debe", "Obligatorio", "Se tendrá que", "No deberá", "Se debe", "No debería", "Recomendado", "Puede" y "Opcional" en este documento son se interpreta como se describe en RFC 2119.

##<a name="2-service-diagram"></a>2. diagrama de servicio de 

El diagrama siguiente muestra la arquitectura de alto nivel de servicio de transmisión de live en Microsoft Azure Media Services:

1.  Codificador directo inserta fuentes directo a los canales que se crean y se aprovisione mediante el SDK de Microsoft Azure Media Services.
2.  Canales, programas y transmisión extremo en el controlador de Microsoft Azure Media Services todas las funciones streaming directo incluidos ingesta, formato, en la nube DVR, seguridad, escalabilidad y redundancia.
3.  Los clientes pueden optar por implementar una capa CDN entre el extremo de transmisión y los extremos de cliente.
4.  Secuencia de extremos de cliente desde el extremo de transmisión mediante los protocolos de transmisión adaptación de HTTP (por ejemplo, Smooth Streaming, guión, HDS o HLS).

![Image1][image1]


##<a name="3-bit-stream-format--iso-14496-12-fragmented-mp4"></a>3. formato de secuencia de bits: ISO 14496-12 fragmentar MP4

El formato de conexión para la transmisión directo recopilar que se describe en este documento se basa en [ISO-14496-12]. Por favor, consulte [[MS SSTR]](http://msdn.microsoft.com/library/ff469518.aspx) para una explicación detallada de formato MP4 fragmentar y extensiones para ambos archivos de vídeo a la carta y live transmisión recopilación.

###<a name="live-ingest-format-definitions"></a>Live recopilar las definiciones de formato 

A continuación encontrará una lista de formato especiales definiciones de aplicarán a live recopilación en Microsoft Azure Media Services:

1. El cuadro 'moov', 'ftyp' y LiveServerManifestBox deben enviarse con cada solicitud (HTTP POST).  DEBE enviarse al principio de la secuencia y siempre debe volver a conectar el codificador para reanudar la secuencia recopilar.  Consulte la sección 6 en [1] para obtener más detalles.
2. Sección 3.3.2 en [1] define un cuadro opcional denominado live StreamManifestBox para recopilar. Debido a la lógica de enrutamiento del equilibrador de carga de Microsoft Azure, uso de este cuadro está desusado y no debe estar presente cuando se recopila en el servicio de Microsoft Azure Media. Si esta casilla está presente, Azure Media Services silenciosamente omite.
3. El TrackFragmentExtendedHeaderBox definido en 3.2.3.2 en [1] debe estar presente para cada fragmento.
4. La versión 2 de la TrackFragmentExtendedHeaderBox debe usarse con el fin de generar los segmentos de medios con direcciones URL idénticos en varios centros de datos. El campo de índice de fragmento es necesario para formatos de migración tras error del centro de datos cruzados de transmisión de índice como Apple HTTP Live transmisión (HLS) y MPEG-guión basada en índices.  Para habilitar el centro de datos cruzados migración tras error, el índice de fragmento debe estar sincronizado entre distintas codificadores y aumentar en 1 para cada fragmento sucesivos multimedia, incluso entre codificador reinicia o errores.
5. Sección 3.3.6 en [1] define el cuadro denominado MovieFragmentRandomAccessBox ('mfra') que puede enviarse al final de recopilación directo para indicar el fin de soporte (final de la secuencia) para el canal. Debido a la lógica de ingesta de Azure Media Services, desusado de fin de soporte (final de la secuencia) y el cuadro 'mfra' para recopilación directo no debe enviarse. Si envía, Azure Media Services silenciosamente omite. Se recomienda usar [Canal restablecer](https://msdn.microsoft.com/library/azure/dn783458.aspx#reset_channels) para restablecer el estado del punto de ingesta y también se recomienda usar el [Programa dejar](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) para finalizar una presentación y la secuencia.
6. La duración del fragmento MP4 debería constante, con el fin de reducir el tamaño de los manifiestos de cliente y mejorar heurística de descarga del cliente a través del uso de etiquetas de repetición.  La duración puede fluctuar para compensa la para velocidades no entero.
7. La duración del fragmento MP4 debe estar entre aproximadamente 2 y 6 segundos.
8. Índices (TrackFragmentExtendedHeaderBox fragment_ absolute_ tiempo y fragment_index) y las marcas de tiempo del fragmento de MP4 llegarán en orden ascendente.  Aunque Azure Media Services es y son resistentes a fragmentos duplicados, es muy limitada su capacidad para reordenar los fragmentos de acuerdo con la escala de tiempo multimedia.

##<a name="4-protocol-format--http"></a>4. formato de protocolo: HTTP

Recopilar ISO MP4 fragmentar basado en vivo para solicitar un estándar largas en ejecución HTTP POST para transmitir datos multimedia codificado empaquetados en formato MP4 fragmentar al servicio de usos de Microsoft Azure Media Services. Cada HTTP POST envía una completa fragmentar MP4 secuencia de bits ("secuencia") a partir de comenzando con encabezado cuadros ('moov', 'ftyp' y "Live manifiestos" cuadro servidor) y continuando con una secuencia de fragmentos (cuadros 'moof' y 'mdat'). Consulte la sección 9.2 en [1] para la sintaxis de la dirección URL de solicitud de HTTP POST. Un ejemplo de la dirección URL de entrada es: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

###<a name="requirements"></a>Requisitos

Estos son los requisitos detallados:

1. Codificador debe empezar la difusión mediante el envío de una solicitud de HTTP POST con un "cuerpo vacío" (longitud de contenido cero) con la misma dirección URL de recopilación. Esto puede ayudar a detectar rápidamente si el extremo de recopilación directo es válido y si no hay ninguna autenticación u otras condiciones necesarios. Por el protocolo HTTP, el servidor no podrá enviar respuesta HTTP atrás hasta que se recibe la solicitud completa incluyendo el cuerpo de la publicación. Dada la naturaleza larga ejecución del evento en directo, sin este paso, el codificador no puede detectar cualquier error hasta que finalice enviar todos los datos.
2. Codificador debe controlar los errores o los desafíos de autenticación como resultado (1). Si (1) se realiza correctamente con una respuesta 200, continuar.
3. Codificador debe comenzar una nueva solicitud de HTTP POST con la secuencia MP4 fragmentada.  La carga debe comenzar con los cuadros de encabezado seguidos de fragmentos.  Nota el cuadro 'moov', 'ftyp' y "Live manifiestos" cuadro servidor (en este orden) debe enviarse con cada solicitud, incluso si el codificador debe volver a conectar porque la solicitud anterior se canceló antes del final de la secuencia. 
4. Codificador debe usar segmentado transferir codificación para cargar, ya que es imposible predecir la longitud del contenido completa del evento directo.
5. Cuando el evento finaliza, después de enviar el último fragmento, el codificador debe terminar correctamente la secuencia de mensaje segmentado transferir codificación (mayoría pilas de cliente HTTP controlen automáticamente). Codificador debe esperar a que el servicio devolver el código de respuesta final y, a continuación, finalizar la conexión. 
6. Codificador no debe utilizar el sustantivo Events() tal como se describe en 9.2 [1] para recopilación live en Microsoft Azure Media Services.
7. Si la solicitud HTTP POST termina o agota el tiempo antes del final de la secuencia con un error TCP, el codificador debe emitir una nueva solicitud de publicación con una nueva conexión y seguir los requisitos encima de los requisitos adicionales que el codificador debe volver a enviar los fragmentos MP4 dos anterior para cada control en la secuencia y reanudar sin introducir discontinuidades en la escala de tiempo multimedia.  Reenviar a los dos últimos fragmentos MP4 para cada control garantiza que no hay ninguna pérdida de datos.  En otras palabras, si una secuencia contiene un control de audio y vídeo, y se produce un error en la solicitud de entrada actual, el codificador debe volver a conectar y volver a enviar los dos últimos fragmentos para la pista de audio que previamente ha enviado correctamente, y los dos últimos fragmentos para la pista de vídeo, que anteriormente envió correctamente, con el fin de asegurarse de que no hay ninguna pérdida de datos.  El codificador debe mantener un "Reenviar" búfer de fragmentos de medios, que se vuelve a enviar cuando vuelva a conectarse.

##<a name="5-timescale"></a>5. escala temporal 

[[MS SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) describe el uso de "Escala temporal" para SmoothStreamingMedia (sección 2.2.2.1), StreamElement (sección 2.2.2.3), StreamFragmentElement(2.2.2.6) y LiveSMIL (sección 2.2.7.3.1). Si no hay ningún valor de la escala temporal, el valor predeterminado utilizado es 10.000.000 (10 MHz). Aunque la especificación de formato de transmisión por secuencias suave no bloquear el uso de otros valores de escala de tiempo, la mayoría de los usos de implementaciones codificador este valor predeterminado (10 MHz) para generar Smooth Streaming recopilación de datos. Debido a la característica de [Azure Media dinámico embalaje](media-services-dynamic-packaging-overview.md) , es recomendable usar escala temporal kHz 90 secuencias de vídeo y 48.1 o 44,1 kHz para secuencias de audio. Si se usan los valores de escala de tiempo diferente para diferentes secuencias, deberá enviarse la escala de tiempo de nivel de secuencia. Consulte [[MS SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

##<a name="6-definition-of-stream"></a>6. definición de "Secuencia"  

"Secuencia" es la unidad básica de la operación de recopilación directo para redactar presentación en directo, tratamiento streaming migración tras error y escenarios de redundancia. "Secuencia" se define como un único fragmentar MP4 secuencia de bits que puede contener una sola pista o varias pistas. Una presentación en directo completa podría contener uno o varios flujos según la configuración de la encoder(s) directo. Los ejemplos siguientes muestran distintas opciones de uso de secuencia para crear una presentación en directo.

**Ejemplo:** 

Cliente desea crear una presentación de streaming directo que incluye las siguientes velocidades de bits bajas de audio y vídeo:

Vídeo: 3000kbps, 1500kbps, 750kbps

Audio: 128kbps

###<a name="option-1-all-tracks-in-one-stream"></a>Opción 1: Todas las pistas de una secuencia

En esta opción, un único codificador genera todas las pistas de audio y vídeo y empaquetar ellos en una fragmentar MP4 secuencia de bits que, a continuación, se envía a través de una única conexión de HTTP POST. En este ejemplo, hay sólo una secuencia de esta presentación directo:

![imagen2][image2]

###<a name="option-2-each-track-in-a-separate-stream"></a>Opción 2: Cada control en una secuencia independiente

En esta opción, el encoder(s) sólo colocar una realizar un seguimiento en cada flujo de bits fragmento MP4 y registra todas las secuencias sobre varias conexiones HTTP independientes. Esto puede hacerse con codificadores uno o varios codificadores. Desde el punto de vista de live recopilación esta presentación directo se compone de cuatro secuencias.

![image3][image3]

###<a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Opción 3: Empaquetar pista de audio con la pista de vídeo de velocidad de bits más baja en una secuencia

En esta opción, el cliente elige empaquetar la pista de audio con la pista de vídeo de velocidad de bits más baja en un flujo de bits de fragmento MP4 uno y dejar que las otras dos pistas de vídeo se está su propia secuencia. 


![image4][image4]


###<a name="summary"></a>Resumen

Lo que se muestra encima no es una lista exhaustiva de todas las opciones de recopilación posibles para este ejemplo. De hecho, cualquier agrupación de pistas en secuencias es compatible con la recopilación directo. Los clientes y proveedores de codificador pueden elegir sus propias implementaciones basados en complejidad de ingeniería, capacidad de codificador, redundancia y consideraciones de migración tras error. Sin embargo se debe tener en cuenta que en la mayoría de los casos hay solo una pista de audio para toda la presentación directo por lo que es importante para garantizar el buen estado de la secuencia de ingesta que contiene la pista de audio. Esta consideración a menudo el resultado de poner pista de audio en su propia secuencia (como en la opción 2) o agrupación con la pista de vídeo de velocidad inferior (como en la opción 3). También mejor redundancia y tolerancia a errores, enviando un seguimiento el mismo audio en dos secuencias diferentes (opción 2 con pistas de audio redundantes) o agrupación la pista de audio al menos dos de las más bajos pistas de vídeo de velocidad de bits (opción 3 con audio en al menos dos secuencias de vídeo) es muy recomendable para live recopilar en Microsoft Azure Media Services.

##<a name="7-service-failover"></a>7. migración tras error del servicio de 

Dada la naturaleza de transmisión directo, capacidad de recuperación buena es fundamental para garantizar la disponibilidad del servicio. Microsoft Azure Media Services está diseñado para controlar los distintos tipos de errores, incluidos errores de red, errores del servidor, problemas de almacenamiento, etcetera. Cuando se utiliza junto con la lógica de migración tras error apropiada desde el lado de codificador directo, lograr un servicio de transmisión directo confiable de la nube.

En esta sección, se describen los escenarios de error de servicio. En este caso, el error sucede en algún lugar dentro del servicio y se presenta como un error de red. Estas son algunas recomendaciones para la implementación de codificador para controlar la migración tras error de servicio:


1. Utilice el tiempo de espera de la segunda 10 para establecer la conexión de TCP.  Si un intento de establecer la conexión tarda más de 10 segundos, anular la operación e inténtelo de nuevo. 
2. Utilizar un tiempo de espera breve para enviar la solicitud HTTP fragmentos de mensaje.  Si la duración del fragmento de destino MP4 N segundos, use un tiempo de espera de envío entre N y 2N segundos; Por ejemplo, use un tiempo de espera de 6 a 12 segundos si la duración de fragmento MP4 es de 6 segundos.  Si se produce un tiempo de espera, restablecer la conexión, abra una nueva conexión y reanudar la secuencia recopilar en la nueva conexión. 
3. Mantener búfer graduales que contiene los dos últimos fragmentos, para cada control que han enviado por completo y correctamente al servicio.  Si la solicitud de HTTP POST de una secuencia finaliza o agota el tiempo antes de al final de la secuencia de abrir una nueva conexión y comience otra solicitud de HTTP POST, volver a enviar los encabezados de secuencia, volver a enviar los dos últimos fragmentos para cada control y reanudar la secuencia sin introducir una discontinuidad en la escala de tiempo multimedia.  Esto reduce la probabilidad de pérdida de datos.
4. Se recomienda que el codificador no limita el número de reintentos para establecer una conexión o reanudar la transmisión después de que se produce un error TCP.
5. Después de un error TCP:
    1. La conexión actual, debe cerrarse y se debe crear una nueva conexión para una nueva solicitud de HTTP POST.
    2. El nuevo debe de dirección URL de HTTP POST ser la misma que la URL de publicación inicial.
    3. La nueva entrada de HTTP debe incluir encabezados de secuencia (cuadro 'ftyp', "Live manifiestos" cuadro servidor y 'moov') idénticos a los encabezados de flujo en la publicación inicial.
    4. DEBEN enviarse de los dos últimos fragmentos enviados para cada control y, a continuación, transmisión reanuda sin introducir una discontinuidad en la escala de tiempo multimedia.  Las marcas de tiempo del fragmento MP4 deben aumentar continuamente, incluso en las solicitudes de HTTP POST.
6. El codificador debe terminar la solicitud HTTP POST si los datos no se envían a una velocidad en proporcional a la duración del fragmento MP4.  Una solicitud de HTTP POST que no se envían datos puede impedir que Azure Media Services desconectarse rápidamente el codificador en caso de una actualización de servicio.  Por este motivo, el HTTP POST para disperso pistas (señal de ad) deben ser cortas duración, tan pronto como se envía el fragmento disperso de terminación.

##<a name="8-encoder-failover"></a>8. migración tras error de codificador

Codificador migración tras error es el segundo tipo de escenario de migración tras error que debe solucionarse para el envío directo transmisiones de llevar a cabo. En este escenario, la condición de error ocurrió en el lado de codificador. 

![image5][image5]


A continuación se muestran las expectativas del extremo de recopilación directo cuando se produzca un error de codificador:

1. DEBE crear una nueva instancia de codificador para continuar con la transmisión por secuencias, como se muestra en el diagrama anterior (secuencia de vídeo con línea discontinua de 3000 k).
2. El codificador nuevo debe usar la misma dirección URL para las solicitudes de HTTP POST como la instancia de error.
3. Solicitud POST del codificador nuevo debe incluir los cuadros de encabezado MP4 fragmentados mismos como la instancia de error.
4. El codificador nuevo debe sincronizarse correctamente con todos los otros codificadores ejecución de la misma presentación directo para generar muestras de audio y vídeo sincronizadas con los límites de fragmento alineados.
5. La nueva secuencia debe ser semántica equivalente con la secuencia anterior e intercambiables en el nivel de encabezado y fragmento.
6. El codificador nuevo debe intentar reducir la pérdida de datos.  DEBEN aumentar la fragment_absolute_time y fragment_index de fragmentos de medios desde el punto donde el codificador detuvo la última vez.  El fragment_absolute_time y fragment_index deben aumentar de forma continua, pero resulta permitida para introducir una discontinuidad si es necesario.  Azure Media Services omitirá fragmentos que ya ha recibido y procesado, por lo que es mejor pensar reenviar fragmentos que al introducir discontinuidades en la escala de tiempo multimedia. 

##<a name="9-encoder-redundancy"></a>9. redundancia de codificador 

Críticas para determinados eventos petición incluso mayor disponibilidad y la calidad de la experiencia, se recomienda emplear activo activo codificadores redundantes para lograr failover transparente sin pérdida de datos en directo.

![image6][image6]

Como se muestra en el diagrama anterior, hay dos grupo de codificadores presión dos copias de cada secuencia simultáneamente en el servicio live. Esta configuración se admite porque Microsoft Azure Media Services tiene la capacidad de filtrar los fragmentos duplicados basadas en secuencia ID y fragmento de marca de tiempo. La secuencia de live resultante y el archivo será una única copia de todas las secuencias que es la agregación posible mejor de los dos orígenes. Por ejemplo, en el caso extremo hipotético, siempre y cuando hay un codificador (no tiene que ser el mismo) se ejecuta en un momento dado en el tiempo para cada secuencia, la secuencia resultante en directo desde el servicio será continua sin pérdida de datos. 

El requisito de este escenario es muy similar a los requisitos en caso de codificador migración tras error con la excepción de que el segundo conjunto de codificadores se ejecutan en el mismo momento que los codificadores principales.

##<a name="10-service-redundancy"></a>10. redundancia de servicio de  

Para distribución global altamente redundante, a veces es necesario tener entre región copia de seguridad para controlar desastres regionales. Expandir la topología de "Codificador redundancia", los clientes pueden elegir tener una implementación de servicio redundantes en una región diferente a la que está conectada con el conjunto de 2 º de codificadores. Los clientes también pueden trabajar con un proveedor CDN para implementar un GTM (Administrador Global de tráfico) delante de las distribuciones de dos servicio para enrutar el tráfico de cliente sin problemas. Los requisitos para los codificadores son el mismo como caso "Codificador redundancia" con la única excepción de que el segundo conjunto de codificadores deben apuntar a otro live recopilar punto final. El siguiente diagrama muestra esta configuración:

![image7][image7]

##<a name="11-special-types-of-ingestion-formats"></a>11. tipos de formatos de recopilación especiales 

Esta sección describen algunos tipo especial de formatos de recopilación directo que están diseñados para controlar algunos escenarios específicos.

###<a name="sparse-track"></a>Control disperso

Cuando se realiza una presentación de streaming directo con experiencia de cliente enriquecido, suele ser necesario para transmitir eventos sincronizados o señales de banda con los datos de medios principal. Un ejemplo de esto es dinámico inserción de anuncios directo. Este tipo de evento señalización es diferente de transmisión debido a su naturaleza disperso regular audio y vídeo. En otras palabras, los datos de señalización normalmente no ocurra continuamente y el intervalo puede ser difícil de predecir. El concepto de control disperso se ha diseñado específicamente para recopilar y difundir datos de señalización en banda.

A continuación, es una implementación recomendada para recopila control disperso:

1. Crear una independiente fragmentar MP4 secuencia de bits que solo contiene disperso pista sin pistas de audio y vídeo.
2. En el "Live Server manifiestos cuadro" según se define en la sección 6 en [1], usar el parámetro de "parentTrackName" para especificar el nombre de la pista principal. Consulte la sección 4.2.1.2.1.2 en [1] para obtener más detalles.
3. En la "Live manifiestos cuadro servidor", se debe establecer manifestOutput en "true".
4. Dada la naturaleza del evento señalización dispersa, se recomienda:
    1. Al principio del evento directo, codificador envía los cuadros encabezado inicial al servicio que permitirá que el servicio registrar el control disperso en el manifiesto del cliente.
    2. El codificador debe terminar la solicitud HTTP POST cuando no se está enviando datos.  Larga ejecución HTTP POST que no se envían datos puede impedir que Azure Media Services desconectarse rápidamente el codificador en caso de un reinicio de actualización o servidor de servicio, como el servidor de medios se bloqueará temporalmente en una operación de recepción en el socket. 
    3. Durante el tiempo cuando los datos de señalización no están disponibles, el codificador debe cerrar HTTP POST solicitar.  Mientras esté activa la solicitud de publicación, el codificador debe enviar datos 
    4. Al enviar fragmentos dispersos, codificador puede establecer explícita encabezado de longitud del contenido si está disponible.
    5. Al enviar fragmento disperso con una nueva conexión, codificador debe empezar a enviar desde los cuadros de encabezado seguidos de los fragmentos de nuevos. Esto es para controlar el caso de migración tras error ocurrió entre y se establece la nueva conexión dispersa a un nuevo servidor que no se ha visto la pista dispersa antes.
    6. El fragmento disperso control estarán disponible para el cliente cuando el elemento primario correspondiente un seguimiento de fragmento que tiene igual o mayor valor de marca de tiempo estará disponible para el cliente. Por ejemplo, si el fragmento disperso tiene una marca de tiempo de t = 1000, se espera después de que el cliente ve vídeo (suponiendo que el nombre del control principal es vídeo) fragmentar marca de tiempo 1000 o más, puede descargar el fragmento disperso t = 1000. Tenga en cuenta que la señal real podría muy bien utilizarse para una posición diferente en la escala de tiempo de presentación para su finalidad designado. En el ejemplo anterior, es posible que el fragmento disperso de t = 1000 tiene una carga XML que es para insertar un anuncio en una posición que unos segundos más tarde.
    7. La carga del fragmento disperso control puede estar en varios formatos diferentes (por ejemplo, XML o texto o binario, etc.) según diferentes escenarios. 


###<a name="redundant-audio-track"></a>Pista de Audio redundante

En un típico HTTP adaptación transmisión escenario (por ejemplo, Smooth Streaming o guión), a menudo es solo una pista de audio en toda la presentación. A diferencia de pistas de vídeo que tienen varios niveles de calidad para el cliente para elegir en condiciones de error, la pista de audio puede ser un único punto de error si se rompe la recopilación de la secuencia que contiene la pista de audio. 

Para solucionar este problema, Microsoft Azure Media Services es compatible con la recopilación directo redundantes de pistas de audio. La idea es que la misma pista de audio se puede enviar varias veces en diferentes secuencias. Mientras el servicio sólo registrará la pista de audio de una vez en el manifiesto del cliente, es posible usar redundantes pistas de audio como copias de seguridad para recuperar fragmentos de audio si la pista de audio principal está teniendo problemas. Para recopilar redundantes pistas de audio, el codificador necesita:

1. Crear la misma pista de audio de varias secuencias bit de fragmento MP4. Las pistas de audio redundantes deben ser semántica equivalente con exactamente el mismo fragmento las marcas de tiempo e intercambiables en el nivel de encabezado y fragmento.
2. Asegúrese de que "audio" entrada la Live Server manifiestos (6 de sección en [1]) ser el mismo para todas las pistas de audio redundantes.

A continuación se muestra una implementación recomendada para redundantes pistas de audio:

1. Enviar cada único pista de audio en una secuencia por sí mismo.  Enviar una secuencia redundante para cada una de estas secuencias de pista de audio, donde la secuencia 2 º es distinto de la 1 º solo por el identificador de la URL de HTTP POST: {protocolo} :// {dirección del servidor de} / {path}/Streams({identifier}) de punto de publicación.
2. Use secuencias independientes para enviar los dos más bajos vídeo velocidades de bits bajas. Cada una de estas secuencias debe contener también una copia de cada pista de audio único.  Por ejemplo, cuando se admiten varios idiomas, estas secuencias deben contener pistas de audio para cada idioma.
3. Utilizar instancias de servidor independiente (codificador) para codificar y enviar las secuencias redundantes mencionadas en (1) y (2). 



##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png

 