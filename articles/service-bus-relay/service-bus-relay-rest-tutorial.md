<properties
    pageTitle="RESTO de Bus de servicio tutorial usando retransmisión mensajería | Microsoft Azure"
    description="Crear una aplicación simple de host de retransmisión de Bus de servicio que expone una interfaz basada en el resto."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-rest-tutorial"></a>Tutorial de REST de retransmisión de Bus de servicio

Este tutorial describe cómo crear una aplicación de host de Bus de servicio simple que expone una interfaz basada en el resto. RESTO permite a un cliente de web, como un explorador web tener acceso a las API de Bus de servicio a través de solicitudes HTTP.

En este tutorial se utiliza el resto de Windows Communication Foundation (WCF) modelo de programación para construir un servicio REST en Bus de servicio. Para obtener más información, vea el [Modelo de programación del resto de WCF](https://msdn.microsoft.com/library/bb412169.aspx) y [diseñar los servicios de implementación y](https://msdn.microsoft.com/library/ms729746.aspx) en la documentación de WCF.

## <a name="step-1-create-a-service-namespace"></a>Paso 1: Crear un espacio de nombres de servicio

El primer paso es crear un espacio de nombres y obtener una clave de firma de acceso compartido (SA). Un espacio de nombres proporciona un límite de aplicación para cada aplicación que se expone a través de Bus de servicio. El sistema genera automáticamente una clave de SA cuando se crea un espacio de nombres de servicio. La combinación de espacio de nombres de servicio y clave SA proporciona las credenciales de Bus de servicio autenticar el acceso a la aplicación.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="step-2-define-a-rest-based-wcf-service-contract-to-use-with-service-bus"></a>Paso 2: Definir un contrato de servicio WCF basado en REST para usar con Bus de servicio

Como con otros servicios de Bus de servicio, cuando se crea un servicio REST estilo, debe definir el contrato. El contrato especifica las operaciones que admite el host. Una operación de servicio puede considerarse como un método de servicio web. Contratos se crean al definir una interfaz de C++, C# o Visual Basic. Cada método de la interfaz corresponde a una operación de servicio específico. El atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) debe aplicarse a cada interfaz y el atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) debe aplicarse a cada operación. Si un método en una interfaz que tiene el [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) no tiene la [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), no se expone a ese método. El código utilizado para estas tareas se muestra en el ejemplo siguiente el procedimiento.

La diferencia entre un contrato básico de Bus de servicio y un contrato resto estilo principal es la adición de una propiedad para la [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx): [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx). Esta propiedad permite asignar un método de la interfaz a un método en el otro lado de la interfaz. En este caso, se usará [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) al que para vincular un método HTTP GET. Esto permite Bus de servicio recuperar e interpretar los comandos que se envía a la interfaz de forma precisa.

### <a name="to-create-a-service-bus-contract-with-an-interface"></a>Para crear un contrato de servicio Bus con una interfaz

1. Abra Visual Studio como administrador: haga clic en el programa en el menú **Inicio** y, a continuación, haga clic en **Ejecutar como administrador**.

2. Crear un nuevo proyecto de aplicación de consola. Haga clic en el menú **archivo** , seleccione **nuevo**y seleccione **proyecto**. En el cuadro de diálogo **Nuevo proyecto** , haga clic en **Visual C#**, seleccione la plantilla de **Aplicación de consola** y asígnele el nombre **ImageListener**. Utilice la **ubicación**predeterminada. Haga clic en **Aceptar** para crear el proyecto.

3. Para un proyecto de C#, Visual Studio crea una `Program.cs` archivo. Esta clase contiene una vacía `Main()` método, necesario para que un proyecto de aplicación de consola generar correctamente.

4. Agregar referencias a Bus de servicio y **System.ServiceModel.dll** al proyecto al instalar el paquete de NuGet Bus de servicio. Este paquete agrega automáticamente las referencias a las bibliotecas de Bus de servicio, así como la WCF **System.ServiceModel**. En el Explorador de soluciones, haga clic en el proyecto **ImageListener** y, a continuación, haga clic en **Administrar paquetes de NuGet**. Haga clic en la ficha **Examinar** , busque `Microsoft Azure Service Bus`. Haga clic en **instalar**y acepte los términos de uso.

5. Deberá agregar explícitamente una referencia a **System.ServiceModel.Web.dll** al proyecto:

    una. En el Explorador de soluciones, haga clic en la carpeta de **referencias** en la carpeta del proyecto y, a continuación, haga clic en **Agregar referencia**.

    b. En el cuadro de diálogo **Agregar referencia** , haga clic en la ficha de **marco** en el lado izquierdo y, en el cuadro **Buscar** , escriba **System.ServiceModel.Web**. Active la casilla de verificación **System.ServiceModel.Web** , a continuación, haga clic en **Aceptar**.

6. Agregue las siguientes `using` instrucciones en la parte superior del archivo Program.cs.

    ```
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) es el espacio de nombres que permite acceso mediante programación a las características básicas de WCF. Bus de servicio usa muchos de los objetos y atributos de WCF para definir contratos de servicio. Este espacio de nombres que usa en la mayoría de las aplicaciones de retransmisión de Bus de servicio. Del mismo modo, [System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx) le ayuda a definir el canal, que es el objeto que se comunica con Bus de servicio y el explorador web del cliente. Por último, [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) contiene los tipos que le permiten crear aplicaciones basadas en web.

7. Cambiar el nombre de la `ImageListener` **Microsoft.ServiceBus.Samples**espacio de nombres.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

8. Directamente después de la llave de apertura de la declaración de espacio de nombres y definir una nueva interfaz denominada **IImageContract** y aplicar el atributo **ServiceContractAttribute** a la interfaz con un valor de `http://samples.microsoft.com/ServiceModel/Relay/`. El valor de espacio de nombres difiere del espacio de nombres que usar en todo el ámbito de su código. El valor de espacio de nombres se utiliza como un identificador único para este contrato y debe disponer de información de versión. Para obtener más información, consulte [Control de versiones de servicio](http://go.microsoft.com/fwlink/?LinkID=180498). Especificar el espacio de nombres explícitamente impide que el valor de espacio de nombres predeterminado que se agrega el nombre de contrato.

    ```
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

9. Dentro de la `IImageContract` interfaz, declarar un método para la operación solo la `IImageContract` contrato expone en la interfaz y aplicar el `OperationContractAttribute` atributo al método que desea exponer como parte del contrato de Bus de servicio público.

    ```
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

10. En el atributo **OperationContract** , agregue el valor de **WebGet** .

    ```
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

    Esto permite Bus de servicio para enrutar las solicitudes HTTP GET a `GetImage`y para traducir los valores devueltos de `GetImage` en una respuesta HTTP GETRESPONSE. Más adelante en el tutorial, usará un explorador web para tener acceso a este método y para mostrar la imagen en el explorador.

11. Justo después del `IImageContract` definición, declarar un canal que herede de ambas la `IImageContract` y `IClientChannel` interfaces.

    ```
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

    Un canal es el objeto WCF que el servicio y el cliente pasan información entre sí. Más adelante, se creará el canal en la aplicación de host. Bus de servicio, a continuación, se utiliza este canal para pasar las solicitudes HTTP GET desde el explorador para su implementación **GetImage** . Bus de servicio también usa el canal para tomar el valor devuelto **GetImage** y traducir en un GETRESPONSE HTTP para el Explorador de cliente.

12. En el menú **Generar** , haga clic en **Generar solución** para confirmar la exactitud de su trabajo hasta ese momento.

### <a name="example"></a>Ejemplo

El código siguiente muestra una interfaz básica que define un contrato de Bus de servicio.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="step-3-implement-a-rest-based-wcf-service-contract-to-use-service-bus"></a>Paso 3: Implementar un contrato de servicio WCF basado en REST usar Bus de servicio

Creación de un servicio de Bus de servicio REST estilo requiere que cree primero el contrato, que se define mediante una interfaz. El siguiente paso es implementar la interfaz. Esto implica la creación de una clase denominada **ImageService** que implementa la interfaz de **IImageContract** definidas por el usuario. Después de implementar el contrato, a continuación, configure la interfaz mediante un archivo App.config. El archivo de configuración contiene la información necesaria para la aplicación, como el nombre del servicio, el nombre del contrato y el tipo de protocolo que se utiliza para comunicarse con Bus de servicio. El código utilizado para estas tareas se proporciona en el ejemplo siguiente el procedimiento.

Como ocurre con los pasos anteriores, hay muy poca diferencia entre implementar un contrato de estilo resto y un contrato de servicio Bus básico.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Implementar un contrato de Bus de servicio REST estilo

1. Crear una nueva clase denominada **ImageService** directamente después de la definición de la interfaz **IImageContract** . La clase **ImageService** implementa la interfaz **IImageContract** .

    ```
    class ImageService : IImageContract
    {
    }
    ```
    Similar a otras implementaciones de interfaz, puede implementar la definición en un archivo diferente. Sin embargo, para este tutorial, la aplicación aparece en el mismo archivo como la definición de interfaz y `Main()` método.

2. Aplique el atributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) a la clase **IImageService** para indicar que la clase es una implementación de un contrato de WCF.

    ```
    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Como se mencionó anteriormente, este espacio de nombres no es un espacio de nombres tradicional. En su lugar, es parte de la arquitectura WCF que identifica el contrato. Para obtener más información, vea el tema de [Nombres de contrato de datos](https://msdn.microsoft.com/library/ms731045.aspx) en la documentación de WCF.

3. Agregar una imagen .jpg a su proyecto.  

    Se trata de una imagen que muestra el servicio en el Explorador de recepción. Haga clic en su proyecto y luego haga clic en **Agregar**. A continuación, haga clic en **Elemento existente**. Use el cuadro de diálogo **Agregar elemento existente** para ir a un .jpg adecuada y, a continuación, haga clic en **Agregar**.

    Al agregar el archivo, asegúrese de que **Todos los archivos** está seleccionado en la lista desplegable situada junto a la **nombre de archivo:** campo. El resto de este tutorial, se supone que el nombre de la imagen es "imagen.jpg". Si tiene un archivo diferente, tendrá que cambiar el nombre de la imagen o cambie el código de compensación.

4. Para asegurarse de que el servicio en ejecución puede encontrar el archivo de imagen, en el **Explorador de soluciones** , haga clic en el archivo de imagen, a continuación, haga clic en **Propiedades**. En el panel **Propiedades** , establezca **Copiar en el directorio de salida** a **Copiar si es posterior**.

5. Agregue una referencia al ensamblado **System.Drawing.dll** al proyecto así como los siguientes asociados `using` instrucciones.  

    ```
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

6. En la clase **ImageService** , agregue el siguiente constructor que carga el mapa de bits y prepara para enviar al explorador del cliente.

    ```
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

7. Directamente después del código anterior, agregue el siguiente método **GetImage** en la clase **ImageService** para devolver un mensaje HTTP que contiene la imagen.

    ```
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);

        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

        return stream;
    }
    ```

    Esta implementación utiliza **MemoryStream** para recuperar la imagen y prepararlos para la transmisión al explorador. Inicia la posición de la secuencia en cero, declara el contenido de la secuencia como jpeg y transmite la información.

8. En el menú **Generar** , haga clic en **Generar solución**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Para definir la configuración de la ejecución del servicio web en Bus de servicio

1. En el **Explorador de soluciones**, haga doble clic en **App.config** para abrirlo en el editor de Visual Studio.

    El archivo **App.config** se asemeja a un archivo de configuración de WCF e incluye el nombre de servicio, extremo (es decir, la ubicación expone Bus de servicio para que los clientes y hosts para comunicarse con ellos) y enlace (el tipo de protocolo que se utiliza para comunicarse). La principal diferencia es que el extremo de servicio configuradas hace referencia a un enlace de [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) que no forma parte de .NET Framework.

2. La `<system.serviceModel>` XML es un elemento WCF que define uno o más servicios. En este caso, usarlo para definir el nombre de servicio y el extremo. En la parte inferior de la `<system.serviceModel>` elemento (pero aún en `<system.serviceModel>`), agregue un `<bindings>` elemento que tiene el siguiente contenido. Define los enlaces utilizados en la aplicación. Puede definir varios enlaces, pero para este tutorial se define solo uno.

    ```
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Este paso define un enlace de Bus de servicio [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) con **relayClientAuthenticationType** establecido en **Ninguno**. Esta configuración indica que un extremo con este enlace no requiere credenciales de cliente.

3. Después de la `<bindings>` elemento, agregue un `<services>` elemento. De forma similar a los enlaces, puede definir varios servicios en un solo archivo de configuración. Sin embargo, para este tutorial, se define sólo uno.

    ```
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    Este paso configura un servicio que utiliza el predeterminado definido anteriormente **webHttpRelayBinding**. También se usa el valor predeterminado **sbTokenProvider**, que se define en el siguiente paso.

4. Después de la `<services>` elemento, crear una `<behaviors>` elemento con el contenido siguiente, reemplazar "SAS_KEY" con la clave de *Firma de acceso compartido* (SA) que obtuvo desde el [portal de Azure][].

    ```
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

5. Aún en App.config, en la `<appSettings>` elemento, Reemplazar valor de cadena de toda la conexión con la cadena de conexión que obtuvo desde el portal. 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

6. En el menú **Generar** , haga clic en **Generar solución** para generar la solución completa.

### <a name="example"></a>Ejemplo

El código siguiente muestra la implementación de contrato y servicio de un servicio basado en REST que se ejecuta en Bus de servicio mediante el enlace **WebHttpRelayBinding** .

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

En el ejemplo siguiente se muestra el archivo App.config asociado con el servicio.

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="[SAS_KEY]" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app setings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
</configuration>
```

## <a name="step-4-host-the-rest-based-wcf-service-to-use-service-bus"></a>Paso 4: Hospedar el servicio WCF basado en REST para utilizar Bus de servicio

Este paso describe cómo ejecutar un servicio web mediante una aplicación de consola en Bus de servicio. En el ejemplo siguiente el procedimiento se proporciona una lista completa del código que escribió en este paso.

### <a name="to-create-a-base-address-for-the-service"></a>Para crear una dirección base para el servicio

1. En la `Main()` declaración de función, cree una variable para almacenar el espacio de nombres de su proyecto de Bus de servicio. Asegúrese de reemplazar `yourNamespace` con el nombre del espacio de nombres de servicio que creó anteriormente.

    ```
    string serviceNamespace = "yourNamespace";
    ```
    Bus de servicio, se utiliza el nombre del espacio de nombres para crear un URI único.

2. Crear un `Uri` instancia para la dirección base del servicio que se basa en el espacio de nombres.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Crear y configurar el host del servicio web

- Crear el host del servicio web, usando la dirección URI creada anteriormente en esta sección.

    ```
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    El host del servicio es el objeto WCF que crea una instancia de la aplicación de host. En este ejemplo se pasa el tipo de host que desea crear (un **ImageService**) así como la dirección a la que desea exponer la aplicación de host.

### <a name="to-run-the-web-service-host"></a>Para ejecutar el host del servicio web

1. Abra el servicio.

    ```
    host.Open();
    ```
    El servicio se está ejecutando.

2. Mostrar un mensaje que indica que el servicio se está ejecutando y cómo detener el servicio.

    ```
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

3. Cuando haya terminado, cierre el host del servicio.

    ```
    host.Close();
    ```

## <a name="example"></a>Ejemplo

En el ejemplo siguiente se incluye el contrato de servicio e implementación de los pasos anteriores en el tutorial y aloja el servicio en una aplicación de consola. Compile el código siguiente en un archivo ejecutable denominado ImageListener.exe.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

### <a name="compiling-the-code"></a>Compilando el código

Después de crear la solución, haga lo siguiente para ejecutar la aplicación:

1. Presione **F5**o busque la ubicación del archivo ejecutable (ImageListener\bin\Debug\ImageListener.exe) para ejecutar el servicio. Mantener la aplicación en ejecución, tal como se requiere para realizar el siguiente paso.

2. Copie y pegue la dirección de la línea de comandos en un explorador para ver la imagen.

3. Cuando haya terminado, presione **ENTRAR** en la ventana de símbolo del sistema para cerrar la aplicación.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha creado una aplicación que usa el servicio de retransmisión de Bus de servicio, consulte los artículos siguientes para obtener más información acerca de mensajería retransmitido:

- [Información general de arquitectura de Bus de servicio Azure](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#relays)

- [Cómo usar el servicio de retransmisión de Bus de servicio](service-bus-dotnet-how-to-use-relay.md)

[Portal de Azure]: https://portal.azure.com