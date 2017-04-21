<properties 
    pageTitle="Tutoriales de flujo de trabajo de resultados Media Encoder Premium" 
    description="Este documento contiene tutoriales que muestran cómo llevar a cabo tareas avanzadas con el flujo de trabajo de Media Encoder Premium y crear flujos de trabajo complejos con el Diseñador de flujo de trabajo." 
    services="media-services" 
    documentationCenter="" 
    authors="xstof" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016"  
    ms.author="xstof;xpouyat;juliako"/>

#<a name="advanced-media-encoder-premium-workflow-tutorials"></a>Tutoriales de flujo de trabajo de Media Encoder Premium avanzados

##<a name="overview"></a>Información general 

Este documento contiene tutoriales que muestran cómo personalizar flujos de trabajo con el **Diseñador de flujo de trabajo**. Puede encontrar los archivos de flujo de trabajo real [aquí](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

##<a name="toc"></a>TABLA DE CONTENIDO

Se tratan los siguientes temas:

- [Codificación MXF en una sola velocidad MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
    - [Iniciar un nuevo flujo de trabajo](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new) 
    - [Uso de la entrada del archivo multimedia](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
    - [Inspección de flujos de medios](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
    - [Agregar un vídeo codificador para. Generación de archivo MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
    - [Codificación de la secuencia de audio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
    - [Multiplexión de secuencias de Audio y vídeo en un contenedor MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
    - [Escribir en el archivo MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
    - [Creación de un activo de servicios multimedia desde el archivo de salida](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
    - [Probar el flujo de trabajo terminado localmente](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
- [Codificación MXF en multibitrate MP4s - embalaje dinámica habilitada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
    - [Agregar uno o varios resultados MP4 adicionales](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
    - [Configurar los nombres de salida de archivo](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
    - [Agregar una pista de Audio independiente](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
    - [Agregar el. Archivo SMIL](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
- [Codificación MXF en multibitrate MP4 - guía mejorada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
    - [Información general del flujo de trabajo para mejorar](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_overview)
    - [Convenciones de nomenclatura de archivos](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
    - [Propiedades de componente en la raíz del flujo de trabajo de publicación](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
    - [Ha generado nombres se basan en los valores de propiedad publicado el archivo de salida](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
- [Agregar miniaturas a multibitrate salida MP4](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
    - [Información general del flujo de trabajo para agregar las miniaturas](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to_multibitrate_MP4_overview)
    - [Agregar codificación JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
    - [Relacionados con la conversión de espacio de Color](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
    - [Escribir las miniaturas](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
    - [Detectar errores en un flujo de trabajo](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
    - [Flujo de trabajo finalizado](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
- [Recorte de tiempo de salida multibitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
    - [Información general del flujo de trabajo para empezar a agregar el recorte de](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
    - [Usar el optimizador de secuencia](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
    - [Flujo de trabajo finalizado](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
- [Introducir el componente de secuencias de comandos](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
    - [Secuencias de comandos en un flujo de trabajo: Hola a todos](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
- [Basado en el marco de recorte de salida multibitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
    - [Guía de introducción para empezar a agregar el recorte de](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
    - [Uso de la lista de clips XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
    - [Modificar la lista de clips de un componente de secuencias de comandos](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
    - [Agregar una propiedad de comodidad ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

##<a id="MXF_to_MP4"></a>Codificación MXF en una sola velocidad MP4
 
En este tutorial crearemos una velocidad de bits única. Archivo MP4 con HE-AAC codifica audio desde un. Archivo de entrada de MXF. 

###<a id="MXF_to_MP4_start_new"></a>Iniciar un nuevo flujo de trabajo 

Abrir el Diseñador de flujo de trabajo y seleccione "Plano de archivo"-"área de trabajo nueva"-"transformar" 

El nuevo flujo de trabajo mostrará 3 elementos: 

- Archivo de origen principal
- Lista de clips XML
- Archivo de salida y activos  

![Nuevo flujo de trabajo de codificación](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Nuevo flujo de trabajo de codificación*

###<a id="MXF_to_MP4_with_file_input"></a>Uso de la entrada del archivo multimedia

Para aceptar el archivo multimedia de entrada, uno se inicia con la adición de un componente de entrada de archivo de medios. Para agregar un componente de flujo de trabajo, busque en el cuadro de búsqueda de repositorio y arrastre la entrada que desee en el panel del diseñador. Hacer esto para la entrada de archivo multimedia y conectar el componente principal archivo de origen con el pin de entrada de nombre de archivo de la entrada de archivo de medios.

![Entrada de archivos multimedia conectada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Entrada de archivos multimedia conectada*

Antes de que podemos hacer mucho más, primero se deberá indicar al diseñador de flujo de trabajo que desea usar para diseñar el flujo de trabajo con qué archivo de ejemplo. Para ello, haga clic en el fondo del panel del diseñador y busque la propiedad de archivo de origen principal en el panel derecho de propiedad. Haga clic en el icono de carpeta y seleccione el archivo que desee probar el flujo de trabajo con. Tan pronto como se hace así, el componente de entrada del archivo multimedia se inspeccionar el archivo y rellenar su PIN de salida para reflejar el archivo que inspeccionen.

![Entrada de archivos multimedia llena](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Entrada de archivos multimedia llena*

Mientras se especifica con qué entrada le gustaría trabajar con, no indica todavía donde el resultado codificado debe ir a. Se configuró similar a cómo el archivo de origen principal, ahora configurar la propiedad variables de carpeta de salida, justo debajo de ella.

![Configuradas propiedades de entrada y salida](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Configuradas propiedades de entrada y salida*

###<a id="MXF_to_MP4_streams"></a>Inspección de flujos de medios

A menudo se lo desea saber cómo la secuencia es de esa flujos mediante el flujo de trabajo. Para examinar una secuencia en cualquier punto del flujo de trabajo, haga clic en un resultado o el pin de entrada en cualquiera de los componentes. En este caso, intente hacer clic en el pin de salida de vídeo sin comprimir de nuestra entrada del archivo multimedia. Se abrirá un cuadro de diálogo hacia arriba que permite inspeccionar el vídeo saliente.

![Inspeccionar el pin de salida de vídeo sin comprimir](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Inspeccionar el pin de salida de vídeo sin comprimir*

En nuestro caso, nos dice por ejemplo que estamos trabajando con una entrada de 1920 x 1080 de 24 cuadros por segundo en 4:2:2 muestreo para ver un vídeo de casi 2 minutos.

###<a id="MXF_to_MP4_file_generation"></a>Agregar un vídeo codificador para. Generación de archivo MP4

Observe que ahora, un vídeo sin comprimir y varios PIN de salida de Audio sin comprimir están disponibles para su uso en nuestra entrada del archivo multimedia. Para codificar el vídeo entrante, necesitamos un componente de codificación: en este caso para generar. Archivos MP4.

Para codificar la secuencia de vídeo H.264, agregue el componente de codificador de vídeo AVC a la superficie del diseñador. Este componente acepta una secuencia de vídeo de descomprimir como entrada y ofrece una secuencia de vídeo comprimida AVC en su pin de salida.

![Desconectada codificador AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Desconectada codificador AVC*

Sus propiedades determinan cómo se pasa a la codificación exactamente. Echemos un vistazo a algunos de los ajustes más importantes:

- Ancho de salida y el alto de salida: determinan la resolución del vídeo codificado. En nuestro caso volvamos con 640 x 360
- Tasa de marco: cuando se establece en el paso simplemente adoptará la velocidad de origen, es posible reemplazar este aunque. Tenga en cuenta que dicha conversión de velocidad no es compensado por movimiento.
- Perfil y nivel: determinan el nivel y el perfil AVC. Para cómodamente obtener más información sobre los distintos niveles y perfiles, haga clic en el icono de signo de interrogación en el componente de codificador de vídeo AVC y la página de ayuda se muestra más detalles acerca de cada uno de los niveles. Para nuestro ejemplo, vamos con perfil principales en el nivel 3,2 (opción predeterminada).
- Tasa de controlar el modo y la velocidad de bits (kbps): en este escenario se optar por una velocidad de bits constante (CBR) kbps 1200 de salida
- Formato de vídeo: trata sobre VUI (información de uso de vídeo) que se escribe en la secuencia H.264 (información del lado que podría ser utilizados por un descodificador para mejorar la presentación pero no esencial a descodificar correctamente):
- NTSC (normal para los Estados Unidos o Japón, usando 30fps)
- PAL (típico de Europa, con 25 fps)
- Modo de tamaño GOP: se configurará el tamaño fijo de GOP nuestros fines con un intervalo de clave de 2 segundos con GOP cerrado. Así se garantiza que proporciona compatibilidad con embalaje dinámico Azure Media Services.

Para fuente nuestra codificador AVC, conecte el pin de salida de vídeo sin comprimir desde el componente de entrada de archivo multimedia la conexión de entrada de vídeo sin comprimir desde el codificador AVC.

![Codificador de AVC conectada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Conectada codificador AVC principales*

###<a id="MXF_to_MP4_audio"></a>Codificación de la secuencia de audio

En este momento, nos hemos codificado vídeo, pero la secuencia de audio sin comprimir original todavía debe estar comprimido. Para ello vaya con codificación AAC por el componente de codificador AAC (Dolby). Agregarlo al flujo de trabajo.

![Desconectada codificador AVC](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Desconectada codificador AAC*

Ahora existe una incompatibilidad: hay solo un único sin comprimir audio entrado pin desde el codificador AAC mientras más probable es que la entrada de archivo multimedia tendrá dos diferentes sin comprimir secuencia de audio disponibles: uno para el canal de audio izquierdo y otro para la derecha. (Si está trabajando con sonido envolvente, es 6 canales). Así que no es posible conectarse directamente el audio del origen de entrada del archivo multimedia en el codificador de audio AAC. El componente AAC espera una secuencia de audio "intercalada" llamada: una sola secuencia que tiene la izquierda y los canales derecho intercalados entre sí. Una vez que sabemos nuestro archivo de origen multimedia qué pistas de audio están en la posición en el origen, podemos generar dicha intercalada secuencia de audio con las posiciones de altavoz asignados correctamente para izquierda y derecha.

En primer lugar uno desea genera una secuencia intercalada de los canales de audio de origen necesarios. El componente Interleaver de secuencia de Audio controlará para nosotros. Agregar al flujo de trabajo y conectar los resultados del audio de la entrada del archivo multimedia en él.

![Conectado Interleaver secuencia de Audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Conectado Interleaver secuencia de Audio*

Ahora que tenemos una secuencia de audio intercalada, aún no se especifica dónde desea asignar a las posiciones de altavoz derecho o izquierdo. Para especificar esto, podemos utilizar al asignador de posición del orador.

![Agregar a un asignador de posición del orador](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Agregar a un asignador de posición del orador*

Configurar al asignador de posición de orador para usarlas con una secuencia de entrada estéreo a través de un filtro de codificador preestablecidos de "Personalizada" y el canal preestablecidos denominado "2.0 (L, R)". (Esto asignará la posición de altavoz izquierdo canal 1 y la posición de altavoz de derecha a canal 2.)

Conecte la salida de la asignador de posición del orador a la entrada del codificador AAC. A continuación, indique el codificador AAC para trabajar con un "2.0 (L, R)" preestablecidos de canal, para que sepa que para tratar con audio estéreo como entrada.

###<a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexión de secuencias de Audio y vídeo en un contenedor MP4

Dado que nuestra AVC flujo de vídeo codificado y nuestra AAC codificado secuencia de audio, podemos capturar dos en una. Contenedor de MP4. El proceso de mezcla de secuencias de distintos en uno solo se denomina "multiplexación" (o "muxing"). En este caso, estamos intercalación el audio y las secuencias de vídeo en una sola coherente. Paquete de MP4. El componente que las coordenadas esto con una. Contenedor MP4 se denomina multiplexor de ISO MPEG-4. Agregue uno a la superficie del diseñador y conecte el codificador de vídeo AVC y el codificador AAC con sus entradas.

![MPEG4 conectada multiplexor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*MPEG4 conectada multiplexor*

###<a id="MXF_to_MP4_writing_mp4"></a>Escribir en el archivo MP4

Al escribir un archivo de salida, se utiliza el componente de salida de archivo. Se conecten esto a la salida de la multiplexor ISO MPEG-4 para que sus resultados se escriben en el disco. Para ello, conecte el pin de salida de contenedor (MPEG-4) con el pin de entrada de escritura de la salida de archivo.

![Conectado salida de archivo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Conectado salida de archivo*

El nombre de archivo que va a utilizar depende de la propiedad del archivo. Propiedad puede ser codificado en un valor determinado, probablemente una desee establecer mediante una expresión.

Para que el flujo de trabajo determinar automáticamente el resultado de una expresión de la propiedad del nombre de archivo, haga clic en el botón junto al nombre de archivo (junto al icono de carpeta). En el menú desplegable y seleccione "Expression". Aparecerá el editor de expresiones. Borrar el contenido del editor en primer lugar.

![Editor de expresiones vacío](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Editor de expresiones vacío*

El editor de expresiones permite especificar cualquier valor literal, combinado con una o más variables. Variables comience con un signo de dólar. Como presionar la tecla $, el editor mostrará un cuadro de lista desplegable con una opción de las variables disponibles. En nuestro caso usaremos una combinación de la variable del directorio de salida y la variable de nombre de archivo de entrada base:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Rellenar fuera del Editor de expresiones](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Rellenar fuera del Editor de expresiones*

>[AZURE.NOTE]Para poder ver ver un archivo de salida de su trabajo de codificación en Azure, debe proporcionar un valor en el editor de expresiones. 

Cuando se confirma la expresión presionando Aceptar, obtendrá una vista previa de la ventana de propiedades para qué valor la propiedad de archivo se resuelva en este momento.

![Expresión de archivo resuelve dir de salida](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Expresión de archivo resuelve dir de salida*

###<a id="MXF_to_MP4_asset_from_output"></a>Creación de un activo de servicios multimedia desde el archivo de salida

Mientras se graba un archivo de salida MP4, es necesario indicar que pertenece este archivo en el que los servicios de multimedia se generan como resultado de ejecutar este flujo de trabajo activo de salida. Para ello, se utiliza el nodo de archivo de salida y activos en el lienzo de flujo de trabajo. Todos los archivos entrantes a este nodo hará que parte del activo Azure Media Services resultante.

Conecte el componente de salida de archivo para el componente de archivo de salida y activos para finalizar el flujo de trabajo.

![Flujo de trabajo finalizado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Flujo de trabajo finalizado*

###<a id="MXF_to_MP4_test"></a>Probar el flujo de trabajo terminado localmente

Para probar el flujo de trabajo localmente, presione el botón de reproducción en la barra de herramientas en la parte superior. Cuando el flujo de trabajo finalizado la ejecución, inspeccionar la salida generada en la carpeta de salida configurado. Verá el archivo de salida MP4 terminado que se ha codificado en el archivo de origen de entrada de MXF.

##<a id="MXF_to_MP4_with_dyn_packaging"></a>Codificación MXF en MP4 - multibitrate embalaje dinámica habilitada

En este tutorial crearemos un conjunto de varios archivos MP4 de velocidad con AAC codificar audio desde una única. Archivo de entrada de MXF.

Cuando se desea un resultado de activos de velocidad múltiple para su uso en combinación con las características de empaquetado dinámico ofrecidas por Azure Media Services, varios archivos MP4 alineado GOP de cada una velocidad diferente y resolución deberá generarse. Para ello, el tutorial de [Codificación MXF en una sola velocidad MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) nos proporciona un buen punto de partida.

![Iniciar flujo de trabajo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Iniciar flujo de trabajo*

###<a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Agregar uno o varios resultados MP4 adicionales

Todos los archivos MP4 en nuestros activos de Azure Media Services resultante será compatible con una velocidad diferente y resolución. Vamos a agregar uno o más archivos de salida MP4 al flujo de trabajo.

Para asegurarse de que tenemos todos nuestros codificadores de vídeo creados con la misma configuración, es más adecuado duplicar el codificador de vídeo AVC ya existente y configurar otra combinación de resolución y velocidad de bits (vamos a agregar uno de 960 x 540 en 25 cuadros por segundo a 2,5 Mbps). Para duplicar el codificador existente, copiar pegarlo en la superficie del diseñador.

Conecte el pin de salida de vídeo sin comprimir de la entrada del archivo multimedia en nuestro nuevo componente AVC.

![Segunda codificador de AVC conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Segunda codificador de AVC conectado*

Adaptar ahora la configuración de nuestra nueva codificador AVC 960 x 540 Mbps 2,5 de salida. (Use sus propiedades "ancho de salida", "Alto de salida" y "Velocidad de bits (kbps)" para esto).

Dado queremos utilizar activo resultante junto con embalaje dinámico de Azure Media Services, el extremo streaming debe ser capaces de generar desde estos archivos MP4 fragmentos HLS/Fragmented MP4 o guión exactamente alineados a otras de manera que los clientes que se va a cambiar entre diferentes velocidades de bits bajas obtienen una experiencia de audio y vídeo continuo único. Para hacerlo, es necesario garantizar que, en las propiedades de codificadores AVC ("grupo de imágenes") grupo tamaño para ambos archivos MP4 se establece a 2 segundos, lo que puede hacer por:

- establecer el modo de tamaño de GOP en tamaño fijo GOP y
- el intervalo de cuadros clave a dos segundos.
- establecer también el Control de IDR GOP GOP cerrado para asegurarse de todos los GOP se permanente en sus propias sin dependencias

Para facilitar la comprender nuestro flujo de trabajo, cambiar el nombre de la primera codificador AVC "vídeo AVC codificador 640 x 360 1200kbps" y la segundo codificador AVC "vídeo AVC codificador 960 x 540 kbps 2500".

Ahora, agregue segunda ISO MPEG-4 multiplexor y un segundo resultado del archivo. Conecte el multiplexor al codificador AVC nuevo y asegúrese de que sus resultados se dirigen a la salida de archivo. A continuación, también conectar entrada AAC codificador de audio que el resultado nuevo de multiplexor. El resultado del archivo por separado, a continuación, se pueden conectar al nodo de archivo de salida y activos para agregar al activo servicios multimedia que se creará.

![Segunda Muxer y salida de archivo conectado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Segunda Muxer y salida de archivo conectado*

Compatibilidad con embalaje dinámico de Azure Media Services, configurar el modo de fragmentos de multiplexor recuento GOP o la duración y establezca la GOP por fragmento a 1. (Debe ser el predeterminado).

![Modos del fragmento de Muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Modos del fragmento de Muxer*

Nota: es podrán que desee repetir el proceso para otras combinaciones velocidad y la resolución que desea agregar a la salida de activos.

###<a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Configurar los nombres de salida de archivo

Tenemos más de un solo archivo agregado a los activos de salida. Esto proporciona una necesidad para asegurarse de que los nombres de archivo para cada uno de los archivos de salida son diferentes entre sí y quizás incluso aplicar una convención de nomenclatura para convertirlo claro en el nombre de archivo que está tratando con.

Pueden controlar la salida de nombres de archivo a través de expresiones en el diseñador. Abra el panel de propiedades para uno de los componentes de salida de archivo y abrir el editor de expresiones para la propiedad. Nuestro primer archivo de salida se configuró mediante la expresión siguiente (consulte el tutorial para pasar de [MXF a una velocidad de bits única salida MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Esto significa que el nombre de archivo depende de dos variables: escribir el directorio de salida y el nombre de base de archivo de origen. Los primeros se exponen como una propiedad en la raíz del flujo de trabajo y se determina el archivo entrante. Observe que el directorio de salida se usa para realizar pruebas local; Esta propiedad se va a reemplazar por el motor de flujo de trabajo cuando se ejecuta el flujo de trabajo por el procesador de medios en la nube en Azure Media Services.
Para dar a los archivos de salida un resultado coherente de nombres, cambie el primer expresión a los nombres de archivo:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

y la segunda para:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Ejecute la una prueba intermedia para asegurarse de que ambos archivos de salida MP4 se generan correctamente.

###<a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Agregar una pista de Audio independiente

Como veremos más adelante cuando se genera un archivo .ism con nuestros archivos de salida MP4, nos se requiere un archivo MP4 solo de audio como la pista de audio para nuestra transmisión adaptación. Para crear este archivo, agregue un muxer adicional para el flujo de trabajo (multiplexor ISO-MPEG-4) y conecte pin de salida del codificador AAC con su pin de entrada para pista 1.

![Audio Muxer agregado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Audio Muxer agregado*

Crear un tercer componente de salida de archivo para el flujo de salida de la muxer de salida y configurar el archivo de nomenclatura expresión como:
    
    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Audio Muxer crear archivo de salida](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Audio Muxer crear archivo de salida*

###<a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Agregar el. Archivo SMIL

En el embalaje dinámico trabajar en combinación con MP4 (tanto los archivos MP4 de solo de audio) en nuestros activos de Media Services, debemos también un archivo manifiesto (también conocido como archivo "SMIL": lenguaje de integración Multimedia sincronizados). Este archivo indica a Azure Media Services qué archivos MP4 están disponibles para empaquetar dinámico y cuáles para tener en cuenta para la transmisión de audio. Un archivo típico manifiesto para un conjunto de MP4 con una sola secuencia de audio es similar a esta:
    
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>

El archivo .ism contiene dentro de una instrucción switch, una referencia a cada uno de los archivos de vídeo MP4 individuales y además de las referencias de archivo de audio también uno (o más) a un MP4 que solo contiene el audio.

Generar el archivo manifiesto nuestro conjunto de MP4 puede hacerse mediante un componente denominado el "escritor de manifiestos de AMS". Para usarlo, arrastre a la superficie y conectar las clavijas de salida "Escribir completa" de los tres componentes de salida de archivo a la entrada de AMS manifiestos escritor. A continuación, asegúrese de conectar el resultado del AMS manifiestos escritor para el archivo y activos de salida.

Al igual que con nuestro otros componentes de salida archivo, configurar el nombre de salida de archivo .ism con una expresión:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

El flujo de trabajo terminado es similar a la a continuación:

![MXF terminado el flujo de trabajo de multibitrate MP4](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*MXF terminado el flujo de trabajo de multibitrate MP4*

##<a id="MXF_to__multibitrate_MP4"></a>Codificación MXF en multibitrate MP4 - guía mejorada

En el [Tutorial de flujo de trabajo anterior](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) hemos visto cómo se puede convertir un solo activo de entrada de MXF en un activo de salida con archivos de velocidad múltiple MP4, un archivo MP4 solo de audio y un archivo manifiesto para su uso en combinación con embalaje dinámico de Azure Media Services.

Este tutorial le mostrará cómo algunos de los aspectos pueden mejorado y realizado más conveniente.

###<a id="MXF_to_multibitrate_MP4_overview"></a>Información general del flujo de trabajo para mejorar

![Mejorar el flujo de trabajo en Multibitrate MP4](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Mejorar el flujo de trabajo en Multibitrate MP4*

###<a id="MXF_to__multibitrate_MP4_file_naming"></a>Convenciones de nomenclatura de archivos

En el flujo de trabajo anterior se especifica una expresión simple como base para generar los nombres de archivo de salida. Tenemos repetido aunque: todos los los componentes del archivo de salida individuales especificado como expresión.

Por ejemplo, el componente de salida de archivo para el primer archivo de vídeo está configurado con esta expresión:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Mientras que para la segunda salida de vídeo, tenemos una expresión similar:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

¿No sería más limpio menos error susceptible y más práctico si podemos quitar algunas de esta repetición y hacer cosas más configurable en su lugar? Afortunadamente podemos: capacidades de expresión del diseñador en combinación con la capacidad de crear propiedades personalizadas en la raíz del flujo de trabajo nos proporcionará un nivel de comodidad adicional.

Supongamos que deberá configuración de la unidad nombre de archivo de las velocidades de bits bajas de los archivos MP4 individuales. Estas velocidades de bits bajas que queremos configurar en un lugar central (en la raíz de nuestro gráfico), desde donde podrá obtenerse para configurar y generación de nombre de archivo de unidad. Para ello, empezaremos por la propiedad de velocidad de codificadores AVC en la raíz de nuestro flujo de trabajo de publicación para que sea accesible desde ambos la raíz a partir de los codificadores AVC. (Incluso si se muestra en dos lugares diferentes, hay un único valor subyacente.)

###<a id="MXF_to__multibitrate_MP4_publishing"></a>Propiedades de componente en la raíz del flujo de trabajo de publicación

Abra el primer codificador AVC, vaya a la propiedad de velocidad (kbps) y en la lista desplegable, elija Publicar.

![La propiedad de velocidad de publicación](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*La propiedad de velocidad de publicación*

Configurar el cuadro de diálogo Publicar para publicar en la raíz de nuestro gráfico de flujo de trabajo con un nombre publicado de "video1bitrate" y un nombre para mostrar legible de "Velocidad de vídeo 1". Configurar una personalizada denominada "Transmisión velocidades de bits bajas" y presionar publicar el nombre del grupo.

![La propiedad de velocidad de publicación](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Cuadro de diálogo publicación de velocidad (propiedad)*

Repita el mismo procedimiento para la propiedad de velocidad del segundo codificador AVC y asígnele el nombre "video2bitrate" con un nombre para mostrar de "Vídeo 2 velocidad", en el mismo grupo personalizado "Transmisión velocidades de bits bajas".

Si ahora se inspeccionar las propiedades de raíz del flujo de trabajo, veremos nuestra grupo personalizado con las dos propiedades publicadas mostrarse. Ambas son que refleja el valor de su velocidad de codificador AVC respectiva.

![Propiedades de velocidad publicado en la raíz del flujo de trabajo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Siempre que desea tener acceso a estas propiedades desde el código o desde una expresión, que podemos hacer similar a esta:

- desde el código en línea de un componente justo debajo de la raíz: node.getPropertyAsString('.. / video1bitrate', null)
- dentro de una expresión: ${ROOT_video1bitrate}
 
Vamos a completar el grupo "Transmisión velocidades de bits bajas" mediante la publicación de nuestra velocidad pista de audio en él. En las propiedades del codificador AAC, busque la velocidad de bits y seleccione Publicar en la lista desplegable situada junto a él. Publicar en la raíz del gráfico con el nombre "audio1bitrate" y mostrar el nombre "Audio 1 velocidad" dentro de nuestro grupo personalizado "Transmisión velocidades de bits bajas".

![Cuadro de diálogo publicación de velocidad de audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Cuadro de diálogo publicación de velocidad de audio*

![Propiedades de audio y vídeo resultantes en raíz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Propiedades de audio y vídeo resultantes en raíz*

Tenga en cuenta que el cambio de cualquiera de las tres valores también configura volver a y cambie los valores de los componentes correspondientes están vinculadas con (y dónde publicados desde).

###<a id="MXF_to__multibitrate_MP4_output_files"></a>Ha generado nombres se basan en los valores de propiedad publicado el archivo de salida

En lugar de codificar nuestros nombres de archivo generado, podemos cambiar ahora nuestra expresión de nombre de archivo en cada uno de los componentes de salida de archivo que se basan en las propiedades de velocidad que acaba de publicar en la raíz del gráfico. A partir de nuestra primera salida de archivo, busque la propiedad de archivo y editar la expresión similar a esta:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Los distintos parámetros en esta expresión pueden obtener acceso y especificados por alcance el signo de dólar en el teclado mientras se encuentra en la ventana de la expresión. Uno de los parámetros disponibles es nuestra propiedad video1bitrate que se ha publicado una versión anterior.

![Acceso a parámetros dentro de una expresión](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Acceso a parámetros dentro de una expresión*

Haga lo mismo para la salida de archivo para nuestro vídeo de la segunda:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

y para la salida de archivo solo de audio:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Si cambiar la velocidad de bits por cualquiera de los archivos de audio o vídeo, se configurarse el codificador respectivo y la convención de nombre de archivo basado en velocidad admitirán automático.

##<a id="thumbnails_to__multibitrate_MP4"></a>Agregar miniaturas a multibitrate salida MP4

A partir de un flujo de trabajo que genera [una salida MP4 desde un MXF multibitrate de entrada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), ahora se explica en Agregar miniaturas en el resultado.

###<a id="thumbnails_to__multibitrate_MP4_overview"></a>Información general del flujo de trabajo para agregar las miniaturas

![Iniciar desde el flujo de trabajo en Multibitrate MP4](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Iniciar desde el flujo de trabajo en Multibitrate MP4*

###<a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Agregar codificación JPG

El centro de nuestra generación miniatura será el componente de codificador JPG, capaz de archivos JPG de salida.

![JPG codificador](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG codificador*

Sin embargo directamente nos podemos conecte nuestra secuencia de vídeo sin comprimir de la entrada del archivo multimedia en el codificador JPG. En su lugar, espera entregada marcos individuales. Esto, podemos hacer a través del componente de puerta de marco de vídeo.

![Conectar una puerta de marco al codificador JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Conectar una puerta de marco al codificador JPG*

La puerta de marco cada tantos segundos o marcos permite pasar un marco de vídeo. El intervalo y la hora de desplazamiento con la que esto suceda es configurable en las propiedades.

![Propiedades de puerta de marco de vídeo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Propiedades de puerta de marco de vídeo*

Vamos a crear una miniatura de cada minuto estableciendo el modo de tiempo (segundos) y el intervalo de 60.

###<a id="thumbnails_to__multibitrate_MP4_color_space"></a>Relacionados con la conversión de espacio de Color

Aunque parece lógico que ahora se pueden conectar ambos PIN sin comprimir vídeo de la puerta del marco y la entrada del archivo multimedia, se obtendría una advertencia si desea hacerlo.

![Error de espacio de color de entrada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Error de espacio de color de entrada*

Esto es porque la forma en qué color información está representada en nuestra original sin formato sin comprimir secuencia de vídeo, procedentes de nuestro MXF es diferente de lo que espera el codificador JPG. Más concretamente, un llamados "espacio de color" de "RGB" o "Escala de grises" se espera flujo. Esto significa que secuencia de vídeo entrante de puerta marco de vídeo tendrán que tener una conversión que primero se aplicarán sobre su espacio de color.

Arrastre el flujo de trabajo convertidor de espacio de Color - Intel y conéctela a nuestra puerta de marco.

![Conectar un convertidor de espacio de Color](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Conectar un convertidor de espacio de Color*

En la ventana Propiedades, seleccione la entrada de BGR de 24 de la lista de valores predeterminados.

###<a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Escribir las miniaturas

A diferencia de nuestro vídeo MP4, el componente de codificador JPG dará como resultado más de un archivo. Para solucionar esto, puede usarse un componente escritor de archivos de escena búsqueda JPG: tomará las miniaturas JPG entrantes y escribirlos, cada nombre de archivo sufijo de un número diferente. (El número normalmente indica el número de segundos/unidades en la secuencia que se dibuja la miniatura).


![Introducir al escritor de archivo escena JPG de búsqueda](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Introducir al escritor de archivo escena JPG de búsqueda*

Configurar la propiedad de ruta de la carpeta de salida con la expresión: ${ROOT_outputWriteDirectory} 

y la propiedad pref con: 

    ${ROOT_sourceFileBaseName}_thumb_

El prefijo determinará la miniatura que se nombre a los archivos. Se sufijo de con un número que indica la posición del control de posición en la secuencia.


![Propiedades de búsqueda JPG archivo escritor de escena](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Propiedades de búsqueda JPG archivo escritor de escena*

Conectar el escritor de archivo de escena búsqueda JPG con el nodo de archivo de salida y activos.

###<a id="thumbnails_to__multibitrate_MP4_errors"></a>Detectar errores en un flujo de trabajo

Conectar la entrada del convertidor de espacio de color a la salida de vídeo sin comprimir sin formato. Ejecutar ahora una prueba local del flujo de trabajo. Hay una buena posibilidad el flujo de trabajo se detenga la ejecución repente y para indicar con un contorno de color rojo en el componente que ha encontrado un error:

![Error de convertidor de espacio de color](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Error de convertidor de espacio de color*

Haga clic en el icono "E" rojo en la esquina superior derecha de esquina del componente de convertidor de espacio de Color para ver qué es el motivo por el intento de codificación de error.

![Cuadro de diálogo de error de convertidor de espacio de color](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Cuadro de diálogo de error de convertidor de espacio de color*

Resulta, como puede ver, que el espacio de color entrantes estándar para el convertidor de espacio de color tiene que ser rec601 para nuestra conversión solicitada de YUV en RGB. Aparentemente nuestra secuencia no indica su rec601. (Rec 601 es un estándar de codificación entrelazadas señales de vídeo analógico en formato de vídeo digital. Especifica una región activa que cubre 720 luminosidad muestras y 360 crominancia muestras por línea. El sistema de codificación de color se conoce como YCbCr 4:2:2.)

Para corregir este error, indicamos en los metadatos de nuestra secuencia que estamos trabajando con el contenido de rec601. Para ello, usaremos un componente actualizador de tipo de datos de vídeo, que se va a poner entre el origen original y el componente de conversión de espacio de color. Esta actualización del tipo de datos permite la actualización manual de determinados datos vídeo propiedades tipo. Configurar para indicar un espacio de Color estándar de "Rec 601". Esto hará que el actualizador de tipo de datos de vídeo etiquetar la secuencia con el espacio de color "Rec 601" si se ha producido sin espacio de color definido todavía. (No reemplaza los metadatos existentes, a menos que se ha activado la casilla de verificación de reemplazo.)

![Actualización de Color estándar de espacio en el actualizador de tipo de datos](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Actualización de Color estándar de espacio en el actualizador de tipo de datos*

###<a id="thumbnails_to__multibitrate_MP4_finish"></a>Flujo de trabajo finalizado

Ahora que nuestros finalice el flujo de trabajo, realice otra ejecutar para verlo pasar.

![Flujo de trabajo terminado de salida de múltiples mp4 con miniaturas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Flujo de trabajo terminado de salida de múltiples mp4 con miniaturas*

##<a id="time_based_trim"></a>Recorte de tiempo de salida multibitrate MP4

A partir de un flujo de trabajo que genera [una salida MP4 desde un MXF multibitrate de entrada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), ahora se explica en Recortar el vídeo de origen en función de marcas de tiempo.

###<a id="time_based_trim_start"></a>Información general del flujo de trabajo para empezar a agregar el recorte de

![Iniciar flujo de trabajo para agregar el recorte de](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Iniciar flujo de trabajo para agregar el recorte de*

###<a id="time_based_trim_use_stream_trimmer"></a>Usar el optimizador de secuencia

El componente de flujo recortador permite recortar del principio y el final de una secuencia de entrada base en registra los intervalos de información (segundos, minutos,...). El optimizador no admite el recorte de marco.

![Recortador de secuencia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Recortador de secuencia*

En lugar de vincular directamente los codificadores AVC y asignador de posición del orador a la entrada del archivo multimedia, deberá incluimos entre los que el optimizador de secuencia. (Uno para la señal de vídeo y otra para la señal de audio intercalada).

![Poner Recortador de secuencia entre](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Poner Recortador de secuencia entre*

Vamos a configurar el optimizador de modo que nos solo procesará vídeo y audio entre 15 y 60 segundos en el vídeo.

Vaya a las propiedades de la Recortador de secuencia de vídeo y configure la hora de comienzo (15s) y propiedades de la hora de finalización (60s). Para asegurarse de que tanto nuestro Recortador de audio y vídeo siempre está configurado para los mismo valores iniciar y finalizar, se publicará aquellos en la raíz del flujo de trabajo.

![Publicar la propiedad de tiempo de inicio de optimizador de secuencia](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publicar la propiedad de tiempo de inicio de optimizador de secuencia*

![Publicar el cuadro de diálogo Propiedades de hora de inicio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Publicar el cuadro de diálogo Propiedades de hora de inicio*

![Publicar el cuadro de diálogo Propiedades de la hora de finalización](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Publicar el cuadro de diálogo Propiedades de la hora de finalización*


Si ahora se inspecciona la raíz de nuestro flujo de trabajo, ambas propiedades será perfectamente mostrado y configurable desde allí.

![Publicados propiedades disponibles en raíz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Publicados propiedades disponibles en raíz*

Ahora abre las propiedades de recorte desde el optimizador de audio y configurar horas de inicio y finalización con una expresión que hace referencia a las propiedades publicadas en la raíz de nuestro flujo de trabajo.

Para recortar audio de la hora de inicio:

    ${ROOT_TrimmingStartTime}

y para su hora de finalización:

    ${ROOT_TrimmingEndTime}

###<a id="time_based_trim_finish"></a>Flujo de trabajo finalizado

![Flujo de trabajo finalizado](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Flujo de trabajo finalizado*


##<a id="scripting"></a>Introducir el componente de secuencias de comandos

Componentes de secuencias de comandos pueden ejecutar secuencias de comandos arbitrarios durante la fase de ejecución de nuestro flujo de trabajo. Hay cuatro secuencias de comandos diferentes que se pueden ejecutar, cada una con características específicas y su propios lugar en el ciclo de vida de flujo de trabajo:

- **commandScript**
- **realizeScript**
- **processInputScript**
- **lifeCycleScript**

La documentación del componente de secuencias de comandos que se trata con más detalle para cada uno de los anteriores. En [la sección siguiente](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), se utiliza el componente de secuencias de comandos de **realizeScript** para construir un xml cliplist sobre la marcha cuando se inicia el flujo de trabajo. Esta secuencia de comandos se denomina durante la instalación del componente, lo que sucede solo una vez en su ciclo de vida.


###<a id="scripting_hello_world"></a>Secuencias de comandos en un flujo de trabajo: Hola a todos

Arrastre un componente de secuencias de comandos de la superficie del diseñador y cambiarle el nombre (por ejemplo, "SetClipListXML").

![Agregar un componente de secuencias de comandos](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Agregar un componente de secuencias de comandos*

Al inspeccionar las propiedades del componente de secuencias de comandos, los cuatro tipos diferentes de secuencia de comandos estarán se muestran, cada puede configurar una secuencia de comandos diferente.

![Propiedades del componente de secuencias de comandos](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propiedades del componente de secuencias de comandos*

Desactive el processInputScript y abra el editor para el realizeScript. Ahora nos estamos configurados y listo para empezar el scripting.

Las secuencias de comandos se escriben en Groovy, un lenguaje de secuencias de comandos compilado dinámicamente para la plataforma Java que conserva la compatibilidad con Java. En realidad, la mayoría del código Java código es válido estupendo.

Vamos a escribir una secuencia de comandos simple Hola mundo estupendo en el contexto de nuestra realizeScript. En el editor, escriba lo siguiente:

    node.log("hello world");

A continuación, ejecute una ejecución de pruebas local. Después de la ejecución, inspeccionar la propiedad registros (a través de la pestaña del sistema en el componente de secuencias de comandos).

![Resultados del registro de llamadas internacionales de Hola a todos](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Resultados del registro de llamadas internacionales de Hola a todos*

El objeto de nodo llamamos el método de registro, se refiere a nuestra "nodo" actual o el componente que estamos scripting dentro. Todos los componentes como tal tienen la capacidad de datos de registro de salida, disponibles a través de la pestaña del sistema. En este caso, mostrar la cadena literal "Hola a todos". Importante aquí es que esto puede resultar una valiosa herramienta de depuración, que le proporciona información sobre lo que realmente está haciendo la secuencia de comandos.

Desde dentro de su entorno de secuencias de comandos, también tenemos acceso a las propiedades en otros componentes. Intente lo siguiente:


    //inspect current node: 
    def nodepath = node.getNodePath(); 
    node.log("this node path: " + nodepath);
    
    //walking up to other nodes: 
    def parentnode = node.getParentNode(); 
    def parentnodepath = parentnode.getNodePath(); 
    node.log("parent node path: " + parentnodepath);
    
    //read properties from a node: 
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null ); 
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null); 
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);

La ventana de registro, veremos los siguientes:

![Resultados del registro para tener acceso a rutas de nodo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Resultados del registro para tener acceso a rutas de nodo*


##<a id="frame_based_trim"></a>Basado en el marco de recorte de salida multibitrate MP4

A partir de un flujo de trabajo que genera [una salida MP4 desde un MXF multibitrate de entrada](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), ahora se explica en el vídeo de origen basado en los recuentos de marco de recorte.

###<a id="frame_based_trim_start"></a>Guía de introducción para empezar a agregar el recorte de

![Para empezar a agregar el recorte de flujo de trabajo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Para empezar a agregar el recorte de flujo de trabajo*

###<a id="frame_based_trim_clip_list"></a>Uso de la lista de clips XML

En todos los tutoriales de flujo de trabajo anterior, hemos usado el componente de entrada de archivo multimedia como el origen de entrada de vídeo. Para esta situación específica, usaremos el componente de origen de la lista de imágenes en su lugar. Tenga en cuenta que no debe ser la mejor forma de trabajar; usar solo el Clip de origen de lista cuando hay un motivo real para hacerlo (igual que en el debajo de los casos, donde que estamos haciendo uso de las capacidades de recorte de clip lista).

Para cambiar de nuestra entrada de archivo multimedia para el origen de la lista de imágenes, arrastre el componente de origen de la lista de imágenes a la superficie de diseño y conectar el pin de XML de la lista de imágenes con el nodo XML de la lista de imágenes del Diseñador de flujo de trabajo. El origen de lista de imágenes con clavijas de salida, este debe rellenar según nuestro vídeo de entrada. Conectar ahora las clavijas sin comprimir vídeo y Audio sin comprimir desde el el Clip de la lista origen al respectivas AVC codificadores y Interleaver de secuencia de Audio. Ahora, quite la entrada del archivo multimedia.

![Reemplaza la entrada del archivo multimedia con el origen de la lista de imágenes](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Reemplaza la entrada del archivo multimedia con el origen de la lista de imágenes*

El componente de origen de la lista de imágenes toma como su entrada "XML de lista de imágenes". Al seleccionar el archivo de origen para probar con localmente, este xml de la lista de imágenes está rellena automáticamente para usted.

![Propiedad de Clip lista XML rellena automáticamente](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Propiedad de Clip lista XML rellena automáticamente*

Buscando un poco más cercana a xml, este es el aspecto como:

![Editar cuadro de diálogo de lista de imágenes](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Editar cuadro de diálogo de lista de imágenes*

Esto no obstante no refleja las capacidades del clip lista xml. Una opción que tenemos es agregar un elemento de "Recortar" en ambas origen audio y vídeo, similar a esta:

![Agregar un elemento de recorte a la lista de clips](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Agregar un elemento de recorte a la lista de clips*

Si modifica el clip lista xml así encima y ejecutar una prueba local, verá el vídeo correctamente sido recortado entre 10 y 20 segundos en el vídeo.

Al contrario de lo que sucede cuando realice una ejecución local no obstante, este mismo xml cliplist no tendría el mismo efecto cuando se aplica en un flujo de trabajo que se ejecuta en Azure Media Services. Cuando se inicia el codificador de Azure Premium, se genera el xml cliplist cada vez nuevo, basado en el archivo de entrada que se ha proporcionado el trabajo de codificación. Esto significa que los cambios que realice en el xml Desgraciadamente, podrían reemplazarse.

Para combatir el xml cliplist barrido cuando se inicia un trabajo de codificación, podemos volver a generar, sobre la marcha justo después del inicio de nuestro flujo de trabajo. Las acciones personalizadas pueden tomarse por lo que se denomina "Componente secuencias de comandos". Para obtener más información, vea [presentar el componente de secuencias de comandos](media-services-media-encoder-premium-workflow-tutorials.md#scripting).


Arrastre un componente de secuencias de comandos de la superficie del diseñador y cambiarle el nombre a "SetClipListXML".

![Agregar un componente de secuencias de comandos](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Agregar un componente de secuencias de comandos*

Al inspeccionar las propiedades del componente de secuencias de comandos, los cuatro tipos diferentes de secuencia de comandos estarán se muestran, cada puede configurar una secuencia de comandos diferente.

![Propiedades del componente de secuencias de comandos](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Propiedades del componente de secuencias de comandos*


###<a id="frame_based_trim_modify_clip_list"></a>Modificar la lista de clips de un componente de secuencias de comandos

Antes de que podemos volver a escribir el xml cliplist que se genera durante el inicio de flujo de trabajo, debemos tener acceso a la propiedad de cliplist xml y el contenido. Podemos hacerlo similar a esta:

    // get cliplist xml: 
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);

![Lista de clips entrantes que se registra](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Lista de clips entrantes que se registra*

En primer lugar se necesita una manera de determinar en qué momento hasta qué punto desea recortar el vídeo. Para realizar este cómodo al usuario menos técnica del flujo de trabajo, publicar dos propiedades en la raíz del gráfico. Para ello, haga clic con el botón secundario del mouse en la superficie del diseñador y seleccione "Agregar propiedad":

- Primera propiedad: "ClippingTimeStart" de tipo: "Código de tiempo"
- Segundo propiedad: "ClippingTimeEnd" de tipo: "Código de tiempo"

![Agregar el cuadro de diálogo Propiedades de hora de inicio de recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Agregar el cuadro de diálogo Propiedades de hora de inicio de recorte*

![Publicar propiedades de tiempo de recorte en la raíz del flujo de trabajo](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Publicar propiedades de tiempo de recorte en la raíz del flujo de trabajo*

Configurar las propiedades en un valor adecuado:

![Configurar el inicio de recorte y finalice las propiedades](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Configurar el inicio de recorte y finalice las propiedades*

Ahora, desde dentro de la secuencia de comandos, para poder tener acceso ambas propiedades, similar a esta:

    
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();
    
    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);

![Ventana de registro que muestra inicial y final de recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Ventana de registro que muestra inicial y final de recorte*

Vamos a analizar las cadenas de código de tiempo en un formulario mediante una expresión regular simple de usar más cómodo:
    
    //parse the start timing: 
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart); 
    startregresult.matches(); 
    def starttimecode = startregresult.group(1); 
    node.log("timecode start is: " + starttimecode); 
    def startframerate = startregresult.group(2); 
    node.log("framerate start is: " + startframerate);
    
    //parse the end timing: 
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend); 
    endregresult.matches(); 
    def endtimecode = endregresult.group(1); 
    node.log("timecode end is: " + endtimecode); 
    def endframerate = endregresult.group(2); 
    node.log("framerate end is: " + endframerate);

![Ventana de registro con el resultado del código de tiempo analizada](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Ventana de registro con el resultado del código de tiempo analizada*

Con esta información a mano, ahora se puede modificar el xml cliplist para reflejar las horas de inicio y final para el que desee recortar precisa del marco de la película.

![Código de secuencia de comandos para agregar elementos de recortes](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Código de secuencia de comandos para agregar elementos de recortes*

Esto se realiza a través de operaciones de manipulación de cadenas normal. La imagen prediseñada modificada lista xml resultante se vuelve a escribir la propiedad clipListXML en la raíz del flujo de trabajo a través del método de "DefinirPropiedad". La ventana de registro después de ejecutar otra prueba ¿nos muestre lo siguiente:

![Registro de la lista de clips resultante](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Registro de la lista de clips resultante*

Hacer una ejecución de prueba para ver cómo se han recortado las secuencias de vídeo y audio. Como puede hacer más de una ejecución de prueba con distintos valores para los puntos de recorte, habrá observado que los que no se tendrán en cuenta no obstante! El motivo consiste en que el diseñador, a diferencia de tiempo de ejecución Azure, no reemplazar el xml cliplist cada ejecutar. Esto significa que solo la primera vez que ha establecido la inicial y final de puntos, hará que el xml transformar, todas las otras veces, nuestra cláusula guardia (si (clipListXML.indexOf ("<trim>") == -1)) impedir que el flujo de trabajo agregar otro elemento de recorte cuando ya hay uno presente.

Para facilitar el flujo de trabajo probar localmente, es mejor agregar código y mantener el enrutamiento que inspecciona si ya existe un elemento de recorte. Si es así, que podemos quitar antes de continuar modificando el xml con los nuevos valores. En lugar de usar la manipulación de cadenas sin formato, es probable más seguro hacerlo a través del modelo de objetos de xml real de análisis.

Para poder agregar código aunque, debemos agregar un número de instrucciones de importación al comienzo de la secuencia de comandos primero:
    
    import javax.xml.parsers.*; 
    import org.xml.sax.*; 
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*; 
    import javax.xml.transform.*; 
    import javax.xml.transform.stream.*; 
    import javax.xml.transform.dom.*;

Después de esto, podemos agregar el código de limpieza necesario:

    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML)); 
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already: 
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim"; 
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection 
    Set<Element> elementsToDelete = new HashSet<Element>(); 
    for (int i = 0; i < trimelems.getLength(); i++) { 
        Element e = (Element)trimelems.item(i); 
        elementsToDelete.add(e); 
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes: 
    elementsToDelete.each{ 
        e -> e.getParentNode().removeChild(e);
    }; 
    node.log("deleted any existing trim nodes");
    
    //serialize the modified clip list xml dom into a string: 
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result); 
    clipListXML = result.getWriter().toString();
    
Este código va justo encima del punto en el que se agregue los elementos de espacios para el xml cliplist.

En este momento, podemos ejecutar y modificar el flujo de trabajo como tiempo que queremos teniendo nunca aplicar los cambios.    

###<a id="frame_based_trim_clippingenabled_prop"></a>Agregar una propiedad de comodidad ClippingEnabled

Como no siempre desee recortar para ocurrir, vamos a concluir nuestro flujo de trabajo agregando cómodo indicador booleano que indica si desea habilitar recortar y recorte.

Al igual que antes, publique una nueva propiedad en la raíz de nuestro flujo de trabajo denominada "ClippingEnabled" de tipo "booleano".

![Publicar una propiedad para habilitar recorte](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publicar una propiedad para habilitar recorte*

Con la debajo de la cláusula de protección simple, podemos comprobar si es necesario recorte y decidir si nuestra lista de clips como tales debe modificarse o no.

    //check if clipping is required: 
    def clippingrequired = node.getProperty("../ClippingEnabled"); 
    node.log("clipping required: " + clippingrequired.toString()); 
    if(clippingrequired == null || clippingrequired == false) 
    {
        node.setProperty("../clipListXml",clipListXML); 
        node.log("no clipping required"); 
        return; 
    }


###<a id="code"></a>Código completo

    import javax.xml.parsers.*; 
    import org.xml.sax.*; 
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*; 
    import javax.xml.transform.*; 
    import javax.xml.transform.stream.*; 
    import javax.xml.transform.dom.*;
    
    // get cliplist xml: 
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping: 
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();
    
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart); 
    startregresult.matches(); 
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);
    
    //parse the end timing: 
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches(); 
    def endtimecode = endregresult.group(1); 
    node.log("timecode end is: " + endtimecode); 
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);
    
    //for local testing: delete any pre-existing trim elements 
    //from the clip list xml by parsing the xml into a DOM:
    
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder(); 
    InputSource is=new InputSource(new StringReader(clipListXML)); 
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath(); 
    String findAllTrimElements = "//trim"; 
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection 
    Set<Element> elementsToDelete = new HashSet<Element>(); 
    for (int i = 0; i < trimelems.getLength(); i++) { 
        Element e = (Element)trimelems.item(i); 
        elementsToDelete.add(e); 
    }
    
    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e -> 
        e.getParentNode().removeChild(e); 
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString()); 
    if(clippingrequired == null || clippingrequired == false) 
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return; 
    }

    //add trim elements to cliplist xml 
    if ( clipListXML.indexOf("<trim>") == -1 ) 
    {
        //trim video 
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+ 
            startframerate +"\">" + starttimecode + 
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode + 
            " </outPoint>\n </trim> \n"); 
        //trim audio 
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+ 
            startframerate +"\">" + starttimecode + 
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" + 
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML ); 
        node.setProperty("../clipListXml",clipListXML); 
    }


##<a name="also-see"></a>Vea también 

[Introducción a Premium codificación en Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Cómo usar Premium codificación en Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Codificación de contenido a petición con servicios multimedia de Azure](media-services-encode-asset.md#media_encoder_premium_workflow)

[Códecs y formatos de flujo de trabajo de Media Encoder Premium](media-services-premium-workflow-encoder-formats.md)

[Archivos de flujo de trabajo de ejemplo](http://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Herramienta de explorador de servicios multimedia de Azure](http://aka.ms/amse)

##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
