<properties 
    pageTitle="Suavizados transmisión Tutorial de aplicación de la tienda Windows | Microsoft Azure" 
    description="Aprenda a usar Azure Media Services para crear una aplicación de la tienda de Windows C# con un control MediaElement XML en secuencia de reproducción suave contenido." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="juliako"/>



#<a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Cómo crear un transmisión por secuencias de aplicación de la tienda Windows de suave

La Smooth Streaming cliente SDK para Windows 8 permite a los desarrolladores crear aplicaciones de la tienda de Windows que pueden reproducir contenido Smooth Streaming directo y a petición. Además de la reproducción básica de Smooth Streaming contenido, que el SDK también proporciona características de texto enriquecido, como Microsoft PlayReady protección, calidad de audio de restricción, DVR Live, transmita cambio, escucha de eventos de error y las actualizaciones de estado (como los cambios de nivel de calidad) y así sucesivamente. Para obtener más información de las características compatibles, vea las [notas de la versión](http://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Para obtener más información, vea [Framework el Reproductor de Windows 8](http://playerframework.codeplex.com/). 

Este tutorial contiene cuatro lecciones:

1. Crear una aplicación de almacenamiento de secuencias suave básico
2. Agregar una barra deslizante para controlar el progreso de medios
3. Seleccione Smooth Streaming de secuencias
4. Seleccione Smooth Streaming pistas

##<a name="prerequisites"></a>Requisitos previos

- Windows 8 de 32 bits o 64 bits. Puede obtener la [Evaluación de Windows 8 Enterprise](http://msdn.microsoft.com/evalcenter/jj554510.aspx) de MSDN.
- Visual Studio 2012 o Visual Studio 2012 Express (o una versión posterior). Puede obtener la versión de prueba de [aquí](http://www.microsoft.com/visualstudio/11/downloads).
- [Microsoft suave transmisión cliente SDK para Windows 8](http://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Homehttp://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).


La solución completa de cada lección puede descargarse desde ejemplos de código de desarrolladores de MSDN (Galería de código): 

- [Lección 1](http://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) - un suave Simple Windows 8 Streaming Media Player, 
- Controlar la [lección 2](http://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) - Simple 8 Smooth Streaming Reproductor de Windows Media con una barra deslizante, 
- [Lección 3](http://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) - un suave de Windows 8 transmisión Reproductor multimedia con selección de secuencia  
- [Lección 4](http://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) - un suave de Windows 8 transmisión Reproductor multimedia con selección de control.

##<a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Lección 1: Crear una aplicación de almacenamiento de secuencias suave básico

En esta lección, creará una aplicación de almacenamiento de Windows con un control MediaElement reproducir secuencia suave contenido.  La aplicación en ejecución tiene el siguiente aspecto:

![Ejemplo de la aplicación de la tienda de Windows de transmisión por secuencias suave][PlayerApplication]
 
Para obtener más información sobre el desarrollo de aplicaciones de la tienda Windows, vea [desarrollar excelentes aplicaciones para Windows 8](http://msdn.microsoft.com/windows/apps/br229512.aspx). Esta lección contiene los procedimientos siguientes:

1.  Crear un proyecto de la tienda Windows
2.  Diseño de la interfaz de usuario (XAML)
3.  Modificar el archivo de código subyacente
4.  Compilar y probar la aplicación

**Para crear un proyecto de la tienda Windows**

1.  Ejecutar Visual Studio 2012 o posterior.
2.  En el menú **archivo** , haga clic en **nuevo**y, a continuación, haga clic en **proyecto**.
3.  Desde el cuadro de diálogo nuevo proyecto, escriba o seleccione los valores siguientes:

Nombre|Valor
---|---
Grupo de plantillas|Instalado, plantillas o Visual C# o de la tienda Windows
Plantilla|Aplicación en blanco (XAML)
Nombre|SSPlayer
Ubicación|C:\SSTutorials
Nombre de la solución|SSPlayer
Crear directorio para la solución|(seleccionada)

4.  Haga clic en **Aceptar**.

**Para agregar una referencia en el SDK de cliente de transmisión suave**

1.  Desde el Explorador de soluciones, haga clic con el botón **SSPlayer**y, a continuación, haga clic en **Agregar referencia**.
2.  Escriba o seleccione los valores siguientes:

Nombre|Valor
---|---
Grupo de referencia|Windows/extensiones
Referencia|Seleccione suave Microsoft transmisión a SDK Client para Windows 8 y paquete de Microsoft Visual C++ Runtime
    
3.  Haga clic en **Aceptar**. 

Después de agregar las referencias, debe seleccionar la plataforma de destino (x64 o x86), agregar referencias no funcionará para la configuración de la plataforma de cualquier CPU.  En el Explorador de soluciones, verá interrogación amarillo de advertencia para estos agregado referencias.

**Diseño de la interfaz de usuario del Reproductor**

1.  Desde el Explorador de soluciones, haga doble clic en **MainPage.xaml** para abrirlo en la vista Diseño.
2.  Busque la ** &lt;cuadrícula&gt; ** y ** &lt;/Grid&gt; ** el archivo XAML las etiquetas y a continuación, pegue el código siguiente entre las etiquetas de dos:

        <Grid.RowDefinitions>
            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
        </Grid.RowDefinitions>
        
        <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="http://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
        </StackPanel>

        <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
        </StackPanel>

        <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
        </StackPanel>

    El control MediaElement se usa para la media de reproducción. El control deslizante denominado sliderProgress se utilizará en la lección siguiente para controlar el progreso de medios.

3.  Presione **CTRL+s** para guardar el archivo.

El control MediaElement no admite Smooth Streaming contenido de fábrica. Para habilitar la compatibilidad con Smooth Streaming, debe registrar el controlador de secuencia de bytes Smooth Streaming por la extensión de nombre de archivo y el tipo MIME.  Para registrar, use el método MediaExtensionManager.RegisterByteStremHandler del espacio de nombres Windows.Media.

En este archivo XAML, algunos controladores de eventos están asociados a los controles.  Debe definir los controladores de eventos.

**Para modificar el archivo de código subyacente**

1.  Desde el Explorador de soluciones, haga clic en **MainPage.xaml**y, a continuación, haga clic en **Ver código**.
2.  En la parte superior del archivo, agregue la siguiente instrucción using:

        using Windows.Media;

3.  Al principio de la clase **MainPage** , agregue al siguiente miembro de datos:

        private MediaExtensionManager extensions = new MediaExtensionManager();

4.  Al final del constructor **MainPage** , agregue las dos líneas siguientes:

        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
        
5.  Al final de la clase **MainPage** , más allá del siguiente código:

        #region UI Button Click Events
        private void btnPlay_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Play();
            txtStatus.Text = "MediaElement is playing ...";
        }
        
        private void btnPause_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Pause();
            txtStatus.Text = "MediaElement is paused";
        }
        
        private void btnSetSource_Click(object sender, RoutedEventArgs e)
        {
            sliderProgress.Value = 0;
            mediaElement.Source = new Uri(txtMediaSource.Text);
        
            if (chkAutoPlay.IsChecked == true)
            {
                txtStatus.Text = "MediaElement is playing ...";
            }
            else
            {
                txtStatus.Text = "Click the Play button to play the media source.";
            }
        }
        
        private void btnStop_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Stop();
            txtStatus.Text = "MediaElement is stopped";
        }
        
        private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
        {
            txtStatus.Text = "Seek to position " + sliderProgress.Value;
            mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
        }
        #endregion

    Aquí se define el controlador de eventos sliderProgress_PointerPressed.  Hay más works para que funcione, que se tratará en la siguiente lección de este tutorial.
6.  Presione **CTRL+s** para guardar el archivo.

El archivo de código subyacente deberá este aspecto:

![CodeView en la aplicación de Visual Studio de Smooth Streaming de la tienda Windows][CodeViewPic]

**Para compilar y probar la aplicación**

1.  En el menú **Generar** , haga clic en **Administrador de configuración**.
2.  Cambiar la **plataforma de soluciones activas** para que coincida con la plataforma de desarrollo.
3.  Presione **F6** para compilar el proyecto. 
4.  Presione **F5** para ejecutar la aplicación.
5.  En la parte superior de la aplicación, puede usar la dirección URL de transmisión suave predeterminada o escriba uno diferente. 
6.  Haga clic en **Establecer origen**. Porque **Reproducción automática** está activada de forma predeterminada, multimedia se reproducen automáticamente.  Puede controlar el medio utilizando los botones **Pausar** y **Detener** , **Reproducir**.  Puede controlar el volumen de medios con el control deslizante vertical.  Sin embargo el control deslizante horizontal para controlar el progreso de medios todavía no está completamente implementado. 

Se ha completado lesson1.  En esta lección, use un control MediaElement para reproducir contenido Smooth Streaming.  En la siguiente lección, se agregará un control deslizante para controlar el progreso del contenido Smooth Streaming.


##<a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Lección 2: Agregar una barra deslizante para controlar el progreso de medios
En la lección 1, ha creado una aplicación de almacenamiento de Windows con un control MediaElement XAML para reproducir contenido de medios Smooth Streaming.  Se trata de algunas funciones multimedia básicas como iniciar, detener y pausar.  En esta lección, agregará un control de barra deslizante a la aplicación.

En este tutorial, se usará un temporizador para actualizar la posición del control deslizante basándose en la posición actual del control MediaElement.  El control deslizante comienzo y fin tiempo también deben actualizarse en caso de contenido en directo.  Esto puede corregirse mejor en el evento de actualización de adaptación de origen.

Orígenes de contenido multimedia son objetos que generan datos multimedia.  La resolución de origen acepta una secuencia de dirección URL o bytes y crea el origen de medios adecuado para ese contenido.  La resolución de origen es la manera estándar para las aplicaciones crear orígenes de contenido multimedia. 

Esta lección contiene los procedimientos siguientes:

1.  Registrar el controlador Smooth Streaming 
2.  Agregar los controladores de eventos de nivel de administrador de origen adaptación
3.  Agregar los controladores de eventos de nivel de adaptación de origen
4.  Agregar controladores de eventos de MediaElement
5.  Agregar un control deslizante relacionada con código de barras
6.  Compilar y probar la aplicación

**Registrar el controlador de secuencia de bytes Smooth Streaming y pasar el propertyset**

1.  Desde el Explorador de soluciones, haga clic con el botón secundario del mouse en **MainPage.xaml**y, a continuación, haga clic en **Ver código**.
2.  Al principio del archivo, agregue la siguiente instrucción using:

        using Microsoft.Media.AdaptiveStreaming;

3.  Al principio de la clase MainPage, agregue a los miembros de datos siguientes:

        private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
        private IAdaptiveSourceManager adaptiveSourceManager;
    
4.  Dentro del constructor **MainPage** , agregue el código siguiente después de la **esto. Inicializar Components();** línea y las líneas de código de registro escritas en la lección anterior:
    
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
    
5.  En el constructor **MainPage** , modifique los dos métodos RegisterByteStreamHandler para agregar la cuarta parámetros:

        // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
        // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
        // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
        extensions.RegisterByteStreamHandler(
            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
        extensions.RegisterByteStreamHandler(
            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
        propertySet);

6.  Presione **CTRL+s** para guardar el archivo.

**Para agregar el controlador de eventos de nivel de administrador de origen adaptación**

1.  Desde el Explorador de soluciones, haga clic con el botón secundario del mouse en **MainPage.xaml**y, a continuación, haga clic en **Ver código**.
2.  Dentro de la clase **MainPage** , agregue al siguiente miembro de datos:

        private AdaptiveSource adaptiveSource = null;

3.  Al final de la clase **MainPage** , agregue el controlador de eventos siguiente:
    
        #region Adaptive Source Manager Level Events
        private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
        {
            adaptiveSource = args.AdaptiveSource;
        }
        #endregion Adaptive Source Manager Level Events

4.  Al final del constructor **MainPage** , agregue la línea siguiente para suscribirse al evento open adaptación de origen:
    
    adaptiveSourceManager.AdaptiveSourceOpenedEvent += AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened) nuevo;

5.  Presione **CTRL+s** para guardar el archivo.

**Para agregar los controladores de eventos de nivel de adaptación de origen**

1.  Desde el Explorador de soluciones, haga clic con el botón secundario del mouse en **MainPage.xaml**y, a continuación, haga clic en **Ver código**.
2.  Dentro de la clase **MainPage** , agregue al siguiente miembro de datos:
    
        private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
        private Manifest manifestObject;
    
3.  Al final de la clase **MainPage** , agregue los controladores de eventos siguientes:

        #region Adaptive Source Level Events
        private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
        {
            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
        }
        
        private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
        {
            adaptiveSourceStatusUpdate = args;
        }
        
        private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
        {
            txtStatus.Text = "Error: " + args.HttpResponse;
        }
        #endregion Adaptive Source Level Events

4.  Al final del método **mediaElement AdaptiveSourceOpened** , agregue el código siguiente para suscribirse a los eventos:
    
        adaptiveSource.ManifestReadyEvent +=
                    mediaElement_ManifestReady;
        adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 
            mediaElement_AdaptiveSourceStatusUpdated;
        adaptiveSource.AdaptiveSourceFailedEvent += 
            mediaElement_AdaptiveSourceFailed;
    
5.  Presione **CTRL+s** para guardar el archivo.

Los mismos eventos están disponibles en origen adaptación administrador nivel, que se puede utilizar para controlar la funcionalidad común a todos los elementos multimedia en la aplicación. Cada AdaptiveSource incluye sus propios eventos y se pueden conectar en cascada todos los eventos de AdaptiveSource en AdaptiveSourceManager.

**Para agregar los controladores de eventos de elemento multimedia**

1.  Desde el Explorador de soluciones, haga clic con el botón secundario del mouse en **MainPage.xaml**y, a continuación, haga clic en **Ver código**.
2.  Al final de la clase **MainPage** , agregue los controladores de eventos siguientes:
    
        #region Media Element Event Handlers 
        private void MediaOpened(object sender, RoutedEventArgs e)
        {
            txtStatus.Text = "MediaElement opened";
        }
        
        private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
        {
            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
        }
        
        private void MediaEnded(object sender, RoutedEventArgs e)
        {
            txtStatus.Text ="MediaElement ended.";
        }
        #endregion Media Element Event Handlers

3.  Agregue el siguiente código al final del constructor **MainPage** a subíndice a los eventos:
    
        mediaElement.MediaOpened += MediaOpened;
        mediaElement.MediaEnded += MediaEnded;
        mediaElement.MediaFailed += MediaFailed;

4.  Presione **CTRL+s** para guardar el archivo.

**Para agregar el control deslizante código relacionado**

1.  Desde el Explorador de soluciones, haga clic con el botón secundario del mouse en **MainPage.xaml**y, a continuación, haga clic en **Ver código**.
2.  Al principio del archivo, agregue la siguiente instrucción using:
    
        using Windows.UI.Core;

3.  Dentro de la clase **MainPage** , agregue a los miembros de datos siguientes:
    
        public static CoreDispatcher _dispatcher;
        private DispatcherTimer sliderPositionUpdateDispatcher;
    
4.  Al final del constructor **MainPage** , agregue el siguiente código:

        _dispatcher = Window.Current.Dispatcher;
        PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
        sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
    
5.  Al final de la clase **MainPage** , agregue el código siguiente:
    
        #region sliderMediaPlayer
        private double SliderFrequency(TimeSpan timevalue)
        {
            long absvalue = 0;
            double stepfrequency = -1;
        
            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }
        
            TimeSpan totalDVRDuration = new TimeSpan(absvalue);
        
            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }
        
            return stepfrequency;
        }
        
        void updateSliderPositionoNTicks(object sender, object e)
        {
            sliderProgress.Value = mediaElement.Position.TotalSeconds;
        }
        
        public void setupTimer()
        {
            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
        }

        public void startTimer()
        {
            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
        }
        
        // Slider start and end time must be updated in case of live content
        public async void setSliderStartTime(long startTime)
        {
            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
        }
        
        // Slider start and end time must be updated in case of live content
        public async void setSliderEndTime(long startTime)
        {
            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
        }
        #endregion sliderMediaPlayer

    **Nota:** CoreDispatcher se usa para realizar cambios en el subproceso de interfaz de usuario desde el subproceso de interfaz de usuario no. En caso de botella de subproceso distribuidor, el desarrollador puede usar distribuidor de elemento de interfaz de usuario que tiene la intención de actualizar.  Por ejemplo:
    
        await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 
          timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
        double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 
          sliderProgress.Maximum = absvalue; }); 
        

6.  Al final del método **mediaElement_AdaptiveSourceStatusUpdated** , agregue el siguiente código:
    
        setSliderStartTime(args.StartTime);
        setSliderEndTime(args.EndTime);

7.  Al final del método **MediaOpened** , agregue el siguiente código:
    
    sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan); sliderProgress.Width = mediaElement.Width; setupTimer();

8.  Presione **CTRL+s** para guardar el archivo.

**Para compilar y probar la aplicación**

1. Presione **F6** para compilar el proyecto. 
2.  Presione **F5** para ejecutar la aplicación.
3.  En la parte superior de la aplicación, puede usar la dirección URL de transmisión suave predeterminada o escriba uno diferente. 
4.  Haga clic en **Establecer origen**. 
5.  Probar el control deslizante.

Se ha completado la lección 2.  En esta lección agregó un control deslizante a la aplicación. 

##<a name="lesson-3-select-smooth-streaming-streams"></a>Lección 3: Seleccione Smooth Streaming de secuencias
Smooth Streaming es capaz para transmitir contenido con varias pistas de audio de idioma que se pueden seleccionar por los visores de Office.  En esta lección, le permitirá visores seleccionar secuencias. Esta lección contiene los procedimientos siguientes:

1. Modificar el archivo XAML
2. Modificar el archivo de código behand
3. Compilar y probar la aplicación


**Para modificar el archivo XAML**

1. Desde el Explorador de soluciones, contextual **MainPage.xaml**y, a continuación, haga clic en **Ver diseñador**.
2. Busque &lt;Grid.RowDefinitions&gt;y modificar la RowDefinitions para que sea igual que:

        <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
        </Grid.RowDefinitions>

3. Dentro de la &lt;cuadrícula&gt;&lt;/Grid&gt; etiquetas, agregue el código siguiente para definir un control de cuadro de lista, para que los usuarios puedan ver la lista de secuencias disponibles y seleccione secuencias de:

        <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
        </Grid>

4. Presione **CTRL+s** para guardar los cambios.


**Para modificar el archivo de código subyacente**

1. Desde el Explorador de soluciones, haga clic en **MainPage.xaml**y, a continuación, haga clic en **Ver código**.
2. En el espacio de nombres SSPlayer, agregue una nueva clase: clase #region secuencia
    
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
    
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
    
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
    
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
    
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream

3. Al principio de la clase MainPage, agregue las siguientes definiciones de variable:

        private List<Stream> availableStreams;
        private List<Stream> availableAudioStreams;
        private List<Stream> availableTextStreams;
        private List<Stream> availableVideoStreams;

4. Dentro de la clase MainPage, agregue la región siguiente:

        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
        
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
        
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
        
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
        
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
        
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
        
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
        
            // Select the frist video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
        
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
        
            // Select the frist audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
        
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
        
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection

5. Busque el método mediaElement_ManifestReady, anexe el código siguiente al final de la función:
    
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();

    Así que cuando MediaElement manifiesto esté listo, el código obtiene una lista de las secuencias disponibles y rellena el cuadro de lista de la interfaz de usuario con la lista.

6. Dentro de la clase MainPage, busque la interfaz de usuario botones haga clic en área de eventos y, a continuación, agregue la siguiente definición de la función:

        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Para compilar y probar la aplicación**

1. Presione **F6** para compilar el proyecto. 
2.  Presione **F5** para ejecutar la aplicación.
3.  En la parte superior de la aplicación, puede usar la dirección URL de transmisión suave predeterminada o escriba uno diferente. 
4.  Haga clic en **Establecer origen**. 
5.  El idioma predeterminado es audio_eng. Pruebe a cambiar entre audio_eng y audio_es. Cada vez, seleccione una nueva secuencia, debe hacer clic en el botón Enviar.

Se ha completado la lección 3.  En esta lección, agregue la funcionalidad para elegir secuencias.

##<a name="lesson-4-select-smooth-streaming-tracks"></a>Lección 4: Seleccionar pistas Smooth Streaming
Una presentación Smooth Streaming puede contener varios archivos de vídeo codificados con distintos niveles de calidad (velocidades de bits) y resoluciones. En esta lección, le permitirá los usuarios puedan seleccionar pistas. Esta lección contiene los procedimientos siguientes:

1. Modificar el archivo XAML
2. Modificar el archivo de código behand
3. Compilar y probar la aplicación

**Para modificar el archivo XAML**

1. Desde el Explorador de soluciones, contextual **MainPage.xaml**y, a continuación, haga clic en **Ver diseñador**.
2. Busque la &lt;cuadrícula&gt; etiquetar con el nombre **gridStreamAndBitrateSelection**, anexe el código siguiente al final de la etiqueta:

        <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
        </StackPanel>

3. Presione **CTRL+s** para guardar los cambios de


**Para modificar el archivo de código subyacente**

1. Desde el Explorador de soluciones, haga clic en **MainPage.xaml**y, a continuación, haga clic en **Ver código**.
2. En el espacio de nombres SSPlayer, agregue una nueva clase:
    
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
    
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
    
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
    
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
    
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track

3. Al principio de la clase MainPage, agregue las siguientes definiciones de variable:
    
        private List<Track> availableTracks;

4. Dentro de la clase MainPage, agregue la región siguiente:
    
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>

        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();

            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;

            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;

                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }

        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }

        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }

        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }

        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection

5. Busque el método mediaElement_ManifestReady, anexe el código siguiente al final de la función:

        getTracks(manifestObject);
        refreshAvailableTracksListBoxItemSource();

6. Dentro de la clase MainPage, busque la interfaz de usuario botones haga clic en área de eventos y, a continuación, agregue la siguiente definición de la función:

        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Para compilar y probar la aplicación**

1. Presione **F6** para compilar el proyecto. 
2.  Presione **F5** para ejecutar la aplicación.
3.  En la parte superior de la aplicación, puede usar la dirección URL de transmisión suave predeterminada o escriba uno diferente. 
4.  Haga clic en **Establecer origen**. 
5.  De forma predeterminada, se seleccionan todas las pistas de la secuencia de vídeo. Para probar los cambios de tasa de bits, puede seleccionar la velocidad de bits más baja y, a continuación, seleccione la velocidad de bits máxima disponible. Debe hacer clic en enviar después de cada cambio.  Puede ver los cambios de la calidad de vídeo.

Se ha completado la lección 4.  En esta lección, agregue la funcionalidad para elegir pistas.


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="other-resources"></a>Otros recursos:
- [Cómo crear una aplicación de Smooth Streaming Windows 8 JavaScript con características avanzadas](http://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
- [Smooth Streaming Technical Overview](http://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png
 
