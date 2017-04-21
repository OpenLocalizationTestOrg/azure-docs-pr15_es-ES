<properties
    pageTitle="Usar varios archivos de entrada y propiedades del componente con codificador Premium | Microsoft Azure"
    description="Este tema explica cómo usar setRuntimeProperties para usar varios archivos de entrada y pasar datos personalizados al procesador de media de flujo de trabajo de Media Encoder Premium."
    services="media-services"
    documentationCenter=""
    authors="xpouyat"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"  
    ms.author="xpouyat;anilmur;juliako"/>

# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Usar varios archivos de entrada y propiedades del componente con codificador Premium

## <a name="overview"></a>Información general

Hay escenarios en los que se necesita para personalizar las propiedades de componente, especificar contenido XML de la lista de imágenes, o enviar varios archivos de entrada cuando envíe una tarea con el procesador de media de **Flujo de trabajo de Media Encoder Premium** . Algunos ejemplos son:

- Texto superpuesto en vídeo y establecer el valor de texto (por ejemplo, la fecha actual) en tiempo de ejecución para cada entrada vídeo.
- Personalizar la lista de Galería XML (para especificar uno o varios archivos de origen, con o sin recorte, etcetera.).
- Superponer una imagen de logotipo en el vídeo de entrada mientras se codifica el vídeo.

Para permitir que el **Flujo de trabajo de Media Encoder Premium** sabe que va a cambiar algunas propiedades del flujo de trabajo cuando se crea la tarea o envíen varios archivos de entrada, debe usar una cadena de configuración contiene **setRuntimeProperties** o **transcodeSource**. En este tema se explica cómo usarlas.


## <a name="configuration-string-syntax"></a>Sintaxis de la cadena de configuración

La cadena de configuración para establecer en la tarea de codificación usa un documento XML que es similar a esta:

    <?xml version="1.0" encoding="utf-8"?>
    <transcodeRequest>
      <transcodeSource>
      </transcodeSource>
      <setRuntimeProperties>
        <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      </setRuntimeProperties>
    </transcodeRequest>


El siguiente es el código de C# que lee la configuración de XML desde un archivo y pasa a la tarea en una tarea:

    XDocument configurationXml = XDocument.Load(xmlFileName);
    IJob job = _context.Jobs.CreateWithSingleTask(
                                                  "Media Encoder Premium Workflow",
                                                  configurationXml.ToString(),
                                                  myAsset,
                                                  "Output asset",
                                                  AssetCreationOptions.None);


## <a name="customizing-component-properties"></a>Personalizar las propiedades de componente  

### <a name="property-with-a-simple-value"></a>Propiedad con un valor simple
En algunos casos, es útil personalizar una propiedad de componente junto con el archivo de flujo de trabajo que se va a ejecutar el flujo de trabajo de Media Encoder Premium.

Supongamos que había diseñado un flujo de trabajo que el texto se superpone sobre los vídeos y, a continuación, se supone que el texto (por ejemplo, la fecha actual) se establece en tiempo de ejecución. Puede hacer esto enviando el texto que se establecerá como el nuevo valor para la propiedad de texto del componente de superposición de la tarea de codificación. Puede usar este mecanismo para cambiar otras propiedades de un componente del flujo de trabajo (por ejemplo, la posición o color de la superposición, la velocidad del codificador AVC, etcetera.).

**setRuntimeProperties** se usa para reemplazar una propiedad en los componentes del flujo de trabajo.

Ejemplo:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Optional Overlay/Overlay/filename" value="MyLogo.png"/>
          <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
      </setRuntimeProperties>
    </transcodeRequest>


### <a name="property-with-an-xml-value"></a>Propiedad con un valor XML

Para establecer una propiedad que espera un valor XML, encapsulados usando `<![CDATA[ and ]]>`.

Ejemplo:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

>[AZURE.NOTE]Asegúrese de no colocar un carro devuelto inmediatamente posterior `<![CDATA[`.


### <a name="propertypath-value"></a>valor de propertyPath

En los ejemplos anteriores, era la propertyPath "archivo multimedia/nombre de archivo de entrada" o "/ inactiveTimeout" o "clipListXml".
En general, este es el nombre del componente y, a continuación, el nombre de la propiedad. La ruta puede tener más o menos niveles, como "/ primarySourceFile" (como la propiedad está en la raíz del flujo de trabajo) o "Video y procesamiento gráfico superpuesto/opacidad" (porque la superposición se encuentra en un grupo).    

Para comprobar la ruta de acceso y nombre de propiedad, utilice el botón de acción que está inmediatamente al lado de cada propiedad. Puede haga clic en este botón de acción y seleccione **Editar**. Se mostrará el nombre real de la propiedad o inmediatamente anterior, el espacio de nombres.

![Acción o editar](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![(Propiedad)](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Varios archivos de entrada

Cada tarea que envíe para el **Flujo de trabajo de Media Encoder Premium** requiere dos activos:

- El primero es un *Flujo de trabajo activo* que contiene un archivo de flujo de trabajo. Puede diseñar los archivos de flujo de trabajo mediante el [Diseñador de flujo de trabajo](media-services-workflow-designer.md).
- La segunda es un *Recurso multimedia* que contiene los archivos multimedia que desea codificar.

Cuando va a enviar varios archivos multimedia con el codificador de **Flujo de trabajo de Media Encoder Premium** , se aplican las siguientes restricciones:

- Todos los archivos multimedia deben estar en el mismo *Recurso multimedia*. No se admite el uso de varios activos de medios.
- Debe establecer el archivo principal en este activos multimedia (lo ideal es el archivo de vídeo principal que se solicita el codificador para procesar).
- Es necesario pasar datos de configuración que incluye el elemento **setRuntimeProperties** o **transcodeSource** al procesador.
  - **setRuntimeProperties** se usa para invalidar la propiedad del nombre de archivo u otra propiedad de los componentes del flujo de trabajo.
  - **transcodeSource** se usa para especificar el contenido XML de la lista de imágenes.

Conexiones del flujo de trabajo:

 - Si usa uno o varios componentes de entrada del archivo multimedia y plan para usar **setRuntimeProperties** para especificar el nombre de archivo, a continuación, no conecte el pin de componente del archivo principal en ellos. Asegúrese de que no hay ninguna conexión entre el objeto de archivo principal y la entrada del archivo multimedia.
 - Si prefiere usar Clip lista XML y un componente de origen de los medios, a continuación, se pueden conectar ambos.

![Sin conexión desde el archivo de origen primario a la entrada del archivo multimedia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*No hay ninguna conexión desde el archivo primario a componentes de entrada del archivo multimedia si utiliza setRuntimeProperties para establecer la propiedad del nombre de archivo.*

![Conexión de XML para recortar el origen de la lista de la lista de imágenes](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Puede conectar Clip lista XML origen multimedia y utilizar transcodeSource.*


### <a name="clip-list-xml-customization"></a>Galería de personalización de la lista XML
Puede especificar el XML de lista de imágenes en tiempo de ejecución del flujo de trabajo mediante **transcodeSource** en la cadena de configuración XML. Es necesario que el pin de XML de la lista de imágenes que estar conectado al componente de origen de los medios del flujo de trabajo.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <transcodeSource>
          <clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
          </clipList>
        </transcodeSource>
        <setRuntimeProperties>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Si desea especificar /primarySourceFile para usar esta propiedad para asignar un nombre a los archivos de salida utilizando 'Expresiones', se recomienda pasando el XML de la lista de imágenes como una propiedad *después de* la propiedad /primarySourceFile, para evitar que la lista de clips reemplazarse mediante la configuración de /primarySourceFile.

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Con recorte precisos adicional:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
               <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


## <a name="example"></a>Ejemplo

Considere la posibilidad de ejemplo en el que desea superponer una imagen de logotipo en el vídeo de entrada mientras se codifica el vídeo. En este ejemplo, el vídeo de entrada se denomina "MyInputVideo.mp4" y el logotipo se denomina "MyLogo.png". Debe realizar los siguientes pasos:

- Cree un activo de flujo de trabajo con el archivo de flujo de trabajo (vea el ejemplo siguiente).
- Crear un recurso multimedia, que contiene dos archivos: MyInputVideo.mp4 como archivo principal y MyLogo.png.
- Enviar una tarea al procesador de media de flujo de trabajo de Media Encoder Premium con los activos de entrada anteriores y especifique la cadena de configuración siguiente.

Configuración:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="MyLogo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


En el ejemplo anterior, se envía el nombre del archivo de vídeo en el componente de entrada de archivo multimedia y la propiedad de primarySourceFile. El nombre del archivo de logotipo se envía a otra entrada de archivo multimedia que se conecta al componente gráfico superpuesto.

>[AZURE.NOTE]El nombre de archivo de vídeo se envía a la propiedad primarySourceFile. El motivo es usar esta propiedad en el flujo de trabajo para crear el nombre de archivo de salida correcta con expresiones, por ejemplo.


### <a name="step-by-step-workflow-creation-that-overlays-a-logo-on-top-of-the-video"></a>Creación de paso a paso de flujo de trabajo que se superpone un logotipo en la parte superior del vídeo     

Estos son los pasos para crear un flujo de trabajo que tiene dos archivos como entrada: un vídeo y una imagen. Se superposiciones de la imagen en la parte superior del vídeo.

Abrir el **Diseñador de flujo de trabajo** y seleccione **archivo** > **Área de trabajo nueva** > **Transformar guía**.

El nuevo flujo de trabajo muestra los tres elementos:

- Archivo de origen principal
- Lista de clips XML
- Archivo de salida y activos  

![Nuevo flujo de trabajo de codificación](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Nuevo flujo de trabajo de codificación*


Para aceptar el archivo multimedia de entrada, iniciar con la adición de un componente de entrada de archivo de medios. Para agregar un componente de flujo de trabajo, busque en el cuadro de búsqueda de repositorio y arrastre la entrada que desee en el panel del diseñador.

A continuación, agregue el archivo de vídeo que se usará para diseñar un flujo de trabajo. Para ello, haga clic en el panel de fondo en el Diseñador de flujo de trabajo y busque la propiedad de archivo de origen principal en el panel derecho de propiedad. Haga clic en el icono de carpeta y seleccione el archivo de vídeo apropiado.

![Origen de archivo principal](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Origen de archivo principal*


A continuación, especifique el archivo de vídeo en el componente de entrada del archivo multimedia.   

![Origen de entrada de archivos multimedia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Origen de entrada de archivos multimedia*


Tan pronto como se hace así, el componente de entrada del archivo multimedia se inspeccionar el archivo y rellenar su PIN de salida para reflejar el archivo que inspeccionen.

El siguiente paso es agregar un "Actualizador de tipo de vídeo datos" para especificar el espacio de color a Rec.709. Agregar un "convertidor de formatos de vídeo" que se establece en el tipo de diseño de datos o diseño = Configurable plana. Esto convertirá la secuencia de vídeo a un formato que puede tomar como un origen del componente de superposición.

![Actualizador de tipo de datos y convertidor de formato de vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Actualizador de tipo de datos de vídeo y convertidor de formato*

![Tipo de diseño = Configurable plana](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Tipo de diseño es Configurable plana*

A continuación, agregue un componente de vídeo superpuesto y conectar el pin de vídeo (sin comprimir) con el pin de vídeo (sin comprimir) de la entrada del archivo multimedia.

Agregar otra entrada de archivo multimedia (para cargar el archivo del logotipo), haga clic en este componente y cambie el nombre "Logotipo de entrada de archivo multimedia" y seleccione una imagen (un archivo .png por ejemplo) en la propiedad del archivo. Conecte el pin sin comprimir imagen con el pin sin comprimir imagen de la superposición.

![Origen de archivo de imagen y del componente de superposición](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Origen de archivo de imagen y del componente de superposición*


Si desea modificar la posición del logotipo en el vídeo (por ejemplo, podría desea colocar en 10 por ciento fuera de la esquina superior izquierda del vídeo), desactive la casilla de verificación "Entrada Manual". Puede hacerlo porque está utilizando una entrada desde archivo multimedia para proporcionar el archivo del logotipo al componente superposición.

![Posición de superposición](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Posición de superposición*


Para codificar la secuencia de vídeo H.264, agregue los componentes de codificador codificador de vídeo AVC y AAC a la superficie del diseñador. Conecte el PIN.
Configurar el codificador AAC y seleccione formato de Audio conversión/predefinido: 2.0 (L, R).

![Codificadores de audio y vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Codificadores de audio y vídeo*


Ahora, agregue los componentes **ISO Mpeg-4 multiplexor** y **Archivo de salida** y conectar las clavijas tal como se muestra.

![MP4 multiplexor y salida de archivo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplexor y salida de archivo*


Es necesario configurar el nombre del archivo de salida. Haga clic en el componente de **Archivo de salida** y editar la expresión para el archivo:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Nombre de salida de archivo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Nombre de salida de archivo*

Puede ejecutar el flujo de trabajo localmente para comprobar que se está ejecutando correctamente.

Al finalizar, se puede ejecutar en Azure Media Services.

En primer lugar, preparar un activo en Azure Media Services con dos archivos: el archivo de vídeo y el logotipo. Puede hacerlo con .NET o API de REST. También puede hacer esto mediante el portal de Azure o el [Explorador de servicios multimedia de Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

En este tutorial se muestra cómo administrar activos con AMSE. Hay dos formas de agregar archivos a un activo:

- Crear una carpeta local, copie los dos archivos y arrastrar y colocar la carpeta a la pestaña **activo** .
- Cargar el archivo de vídeo como un activo, mostrar la información de activos, vaya a la pestaña archivos y cargar un archivo adicional (logotipo).

>[AZURE.NOTE]Asegúrese de configurar un archivo principal en el activo (el archivo de vídeo principal).

![Archivos de activos en AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Archivos de activos en AMSE*


Seleccione el recurso y elija codificar con codificador Premium. Cargar el flujo de trabajo y selecciónelo.

Haga clic en el botón para pasar datos al procesador y agregue el siguiente código XML para establecer las propiedades de tiempo de ejecución:

![Codificador Premium en AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Codificador Premium en AMSE*


A continuación, pegue los siguientes datos XML. Debe especificar el nombre del archivo de vídeo para la entrada del archivo multimedia y la primarySourceFile. Especifique el nombre del nombre de archivo del logotipo de demasiado.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*


Si usa el SDK de .NET para crear y ejecutar la tarea, estos datos XML se pasarán como la cadena de configuración.

    public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);

Una vez completada la tarea, el archivo MP4 en el activo de salida muestra la superposición!

![Superposición de vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Superposición de vídeo*


Puede descargar el flujo de trabajo de ejemplo de [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).


## <a name="see-also"></a>Vea también

- [Introducción a Premium codificación en Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

- [Cómo usar la codificación Premium en Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

- [Codificación de contenido a petición con Azure Media Services](media-services-encode-asset.md#media_encoder_premium_workflow)

- [Formatos de flujo de trabajo de Media Encoder Premium y códecs](media-services-premium-workflow-encoder-formats.md)

- [Archivos de flujo de trabajo de ejemplo](https://github.com/AzureMediaServicesSamples/Encoding-Presets/tree/master/VoD/MediaEncoderPremiumWorkfows)

- [Herramienta de explorador de servicios multimedia de Azure](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
