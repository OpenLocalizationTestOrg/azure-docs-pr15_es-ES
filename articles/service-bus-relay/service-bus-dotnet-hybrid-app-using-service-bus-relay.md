<properties
    pageTitle="Aplicación de en local y la nube híbrida (. NET) | Microsoft Azure"
    description="Obtenga información sobre cómo crear una aplicación de híbrido en local y la nube de .NET mediante la retransmisión de Bus de servicio de Azure."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>

# <a name="net-on-premisescloud-hybrid-application-using-azure-service-bus-relay"></a>Aplicación de .NET en local y la nube híbrida mediante retransmisión de Bus de servicio de Azure

## <a name="introduction"></a>Introducción

En este artículo se describe cómo crear una aplicación de nube híbrida con Microsoft Azure y Visual Studio. El tutorial, se supone que no tiene experiencia previa con Azure. En menos de 30 minutos, tendrá una aplicación que usa varios recursos Azure hacia arriba y en la nube.

Aprenderá:

-   Cómo crear o adaptar un servicio web existente para su consumo en una solución de web.
-   Cómo usar el servicio de retransmisión de Bus de servicio de Azure para compartir datos entre una aplicación de Azure y un servicio web alojado en otro sitio.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="how-the-service-bus-relay-helps-with-hybrid-solutions"></a>Cómo ayuda la retransmisión de Bus de servicio con soluciones híbridas

Soluciones empresariales suelen constar de una combinación de código personalizado escrito para tratar los requisitos empresariales nuevos y únicos y la funcionalidad existente de soluciones y sistemas que ya están en su lugar.

Arquitectos de soluciones están empezando a usar la nube para administración más fácil de requisitos de escala y reducir los costos de operaciones. De esta manera, encontrará que activos de servicio existente que le gustaría aprovechar como bloques de creación para sus soluciones están dentro del firewall corporativo y de fácil llegar a acceso a la solución de la nube. Muchos servicios internos no integrados u hospedados de manera que se puedan fácilmente exponer en el borde de la red corporativa.

La retransmisión de Bus de servicio está diseñada para el caso de uso de toma de servicios web de Windows Communication Foundation (WCF) existentes y hacer que los servicios de acceso seguro a soluciones que residen fuera del perímetro corporativo sin requerir cambios intrusivos en la infraestructura de red corporativa. Dichos servicios de retransmisión de Bus de servicio aún se hospedan dentro de su entorno existente, pero delegar la escucha entrante sesiones y solicitudes al Bus de servicio hospedada en la nube. Bus de servicio protege también los servicios de acceso no autorizado mediante la autenticación de [Firma de acceso compartido](../service-bus-messaging/service-bus-sas-overview.md) (SA).

## <a name="solution-scenario"></a>Escenario de solución

En este tutorial, creará un sitio Web ASP.NET que le permite ver una lista de productos en la página de inventario de productos.

![][0]

El tutorial se supone que tiene información de producto en un sistema local existente y utiliza la retransmisión de Bus de servicio para llegar a ese sistema. Esto simulada por un servicio web que se ejecuta en una aplicación de consola simple y copia por un conjunto de memoria de productos. Podrá ejecutar esta aplicación de consola en su propio equipo e implementar la función web en Azure. De este modo, se muestra cómo el rol de web que se ejecuta en el centro de datos de Azure hecho llamará al equipo, aunque el equipo se guardarán seguramente detrás del firewall al menos una y una capa de traducción (NAT) de la dirección de red.

A continuación se muestra una captura de pantalla de la página de inicio de la aplicación web completada.

![][1]

## <a name="set-up-the-development-environment"></a>Configurar el entorno de desarrollo

Antes de empezar a desarrollar aplicaciones de Azure, obtenga las herramientas y configurar el entorno de desarrollo.

1.  Instale el SDK de Azure para .NET desde la página [obtener herramientas y SDK][] .

2.  Haga clic en **instalar el SDK** de la versión de Visual Studio que está utilizando. Los pasos de este tutorial usan Visual Studio de 2015.

4.  Cuando se le solicite que ejecute o guarde el programa de instalación, haga clic en **Ejecutar**.

5.  En el **Instalador de plataforma Web**, haga clic en **instalar** y continuar con la instalación.

6.  Una vez completada la instalación, tendrá todo lo necesario para comenzar a desarrollar la aplicación. El SDK incluye herramientas que le permiten desarrollar fácilmente aplicaciones de Azure en Visual Studio. Si no tiene instalado Visual Studio, el SDK también instala la versión Visual Studio Express gratuita.

## <a name="create-a-namespace"></a>Crear un espacio de nombres

Para empezar a usar las funciones de Bus de servicio de Azure, primero debe crear un espacio de nombres de servicio. Un espacio de nombres proporciona un contenedor de ámbito para el direccionamiento de recursos de Bus de servicio de la aplicación.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Crear un servidor local

En primer lugar, va a crear un sistema de catálogo de productos en local (falso). Es bastante sencillo; puede ver esto como que representa un sistema de catálogo de producto real local con una superficie de servicio completo que estamos intentando integrar.

Este proyecto es una aplicación de consola de Visual Studio y usa el [paquete NuGet de Bus de servicio de Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) para incluir las bibliotecas de Bus de servicio y los valores de configuración.

### <a name="create-the-project"></a>Crear el proyecto

1.  Con privilegios de administrador, inicie Microsoft Visual Studio. Para iniciar Visual Studio con privilegios de administrador, haga clic en el icono de programa de **Visual Studio** y, a continuación, haga clic en **Ejecutar como administrador**.

2.  En Visual Studio, en el menú **archivo** , haga clic en **nuevo**y, a continuación, haga clic en **proyecto**.

3.  En **Plantillas instaladas**, en **Visual C#**, haga clic en **Aplicación de consola**. En el cuadro **nombre** , escriba el nombre **ProductsServer**:

    ![][11]

4.  Haga clic en **Aceptar** para crear el proyecto **ProductsServer** .

7.  Si ya ha instalado al administrador de paquetes de NuGet para Visual Studio, vaya al paso siguiente. En caso contrario, visite [NuGet][] y haga clic en [Instalar NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Siga las indicaciones para instalar al administrador de paquetes de NuGet y luego vuelva a iniciar Visual Studio.

7.  En el Explorador de soluciones, haga clic en el proyecto **ProductsServer** y luego haga clic en **Administrar paquetes de NuGet**.

8.  Haga clic en la ficha **Examinar** , busque `Microsoft Azure Service Bus`. Haga clic en **instalar**y acepte los términos de uso.

    ![][13]

    Observe que ahora se hace referencia a los ensamblados necesarios de cliente.

9.  Agregar una nueva clase para el contrato de producto. En el Explorador de soluciones, haga clic en el proyecto **ProductsServer** y haga clic en **Agregar**y, a continuación, haga clic en **clase**.

10. En el cuadro **nombre** , escriba el nombre **ProductsContract.cs**. A continuación, haga clic en **Agregar**.

11. En **ProductsContract.cs**, reemplace la definición de espacio de nombres con el siguiente código, que define el contrato de servicio.

    ```
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;
    
        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }
    
        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();
    
        }
    
        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

12. En Program.cs, sustituya la definición de espacio de nombres con el código siguiente, que agrega el servicio de perfil y el host para el mismo.

    ```
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;
    
        // Implement the IProducts interface.
        class ProductsService : IProducts
        {
    
            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };
    
            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }
    
        }
    
        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();
    
                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();
    
                sh.Close();
            }
        }
    }
    ```

13. En el Explorador de soluciones, haga doble clic en el archivo **App.config** para abrirlo en el editor de Visual Studio. En la parte inferior de la ** &lt;sistema. ServiceModel&gt; ** elemento (pero aún dentro &lt;sistema. ServiceModel&gt;), agregue el siguiente código XML. Asegúrese de reemplazar *yourServiceNamespace* con el nombre de su espacio de nombres y a continuación, *yourKey* con la clave de asociaciones de seguridad que ha recuperado anteriormente desde el portal:

    ```
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```
14. Aún en App.config, en la ** &lt;appSettings&gt; ** elemento, Reemplazar valor de cadena de la conexión con la cadena de conexión que obtuvo desde el portal. 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

14. Presione **Ctrl + Mayús + B** o en el menú **Generar** , haga clic en **Generar solución** para generar la aplicación y comprobar la exactitud de su trabajo hasta ese momento.

## <a name="create-an-aspnet-application"></a>Crear una aplicación de ASP.NET

En esta sección creará una aplicación de ASP.NET sencilla que muestra datos recuperados de su servicio de producto.

### <a name="create-the-project"></a>Crear el proyecto

1.  Asegúrese de que Visual Studio se está ejecutando con privilegios de administrador.

2.  En Visual Studio, en el menú **archivo** , haga clic en **nuevo**y, a continuación, haga clic en **proyecto**.

3.  En **Plantillas instaladas**, en **Visual C#**, haga clic en **Aplicación Web de ASP.NET**. Nombre del proyecto **ProductsPortal**. A continuación, haga clic en **Aceptar**.

    ![][15]

4.  En la lista **Seleccionar una plantilla** , haga clic en **MVC**. 

6.  Active la casilla de **Host en la nube**.

    ![][16]

5. Haga clic en el botón de **Cambio de la autenticación** . En el cuadro de diálogo **Cambiar autenticación** , haga clic en **Sin autenticación**y, a continuación, haga clic en **Aceptar**. En este tutorial, vaya a implementar una aplicación que no es necesario un inicio de sesión de usuario.

    ![][18]

6.  En la sección de **Microsoft Azure** del cuadro de diálogo **Nuevo proyecto de ASP.NET** , asegúrese de que está seleccionada la opción **alojar en la nube** y que el **Servicio de aplicación** está seleccionado en la lista desplegable.

    ![][19]

7. Haga clic en **Aceptar**. 

8. Ahora debe configurar Azure recursos para una nueva aplicación web. Siga los pasos de la sección [Configurar Azure recursos para una nueva aplicación web](../app-service-web/web-sites-dotnet-get-started.md#configure-azure-resources-for-a-new-web-app). A continuación, vuelva a este tutorial y continúe con el paso siguiente.

5.  En el Explorador de soluciones, haga clic en **modelos** y, a continuación, haga clic en **Agregar**, haga clic en **clase**. En el cuadro **nombre** , escriba el nombre **Product.cs**. A continuación, haga clic en **Agregar**.

    ![][17]

### <a name="modify-the-web-application"></a>Modificar la aplicación web

1.  En el archivo Product.cs en Visual Studio, sustituya la definición de espacio de nombres existente con el código siguiente.

    ```
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
        public class Product
        {
            public string Id { get; set; }
            public string Name { get; set; }
            public string Quantity { get; set; }
        }
    }
    ```

2.  En el Explorador de soluciones, expanda la carpeta de **controladores** y luego haga doble clic en el archivo **HomeController.cs** para abrirlo en Visual Studio.

3. En **HomeController.cs**, reemplace la definición de espacio de nombres existente con el código siguiente.

    ```
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;
    
        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

3.  En el Explorador de soluciones, expanda la carpeta Views\Shared y luego haga doble clic en **_Layout.cshtml** para abrirlo en el editor de Visual Studio.

5.  Cambiar todas las apariciones de **Mi aplicación de ASP.NET** **de LITWARE**productos.

6. Quitar los vínculos de ** **Inicio**y **contacto** **. En el ejemplo siguiente, elimine el código resaltado.

    ![][41]

7.  En el Explorador de soluciones, expanda la carpeta Views\Home y luego haga doble clic en **Index.cshtml** para abrirlo en el editor de Visual Studio.
    Reemplazar todo el contenido del archivo con el código siguiente.

    ```
    @model IEnumerable<ProductsWeb.Models.Product>
    
    @{
            ViewBag.Title = "Index";
    }
    
    <h2>Prod Inventory</h2>
    
    <table>
            <tr>
                <th>
                    @Html.DisplayNameFor(model => model.Name)
                </th>
                  <th></th>
                <th>
                    @Html.DisplayNameFor(model => model.Quantity)
                </th>
            </tr>
    
    @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Name)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Quantity)
                </td>
            </tr>
    }
    
    </table>
    ```

9.  Para comprobar la exactitud de su trabajo hasta ese momento, puede presionar **Ctrl + Mayús + B** para generar el proyecto.


### <a name="run-the-app-locally"></a>Ejecute la aplicación localmente

Ejecute la aplicación para comprobar que funciona.

1.  Asegúrese de que **ProductsPortal** es el proyecto activo. Haga clic en el nombre del proyecto en el Explorador de soluciones y seleccione **Establecer como proyecto de inicio**.
2.  En Visual Studio, presione F5.
3.  La aplicación debería aparecer en ejecución en un explorador.

    ![][21]

## <a name="put-the-pieces-together"></a>Juntar la partes

El siguiente paso es enlazar el servidor de productos en local con la aplicación ASP.NET.

1.  Si no está ya abierto, en Visual Studio, vuelva a abrir el proyecto **ProductsPortal** que creó en la sección [crear una aplicación ASP.NET](#create-an-aspnet-application) .

2.  Similar al paso en la sección "Crear un servidor local activado", agregue el paquete de NuGet las referencias de proyecto. En el Explorador de soluciones, haga clic en el proyecto **ProductsPortal** y luego haga clic en **Administrar paquetes de NuGet**.

3.  Buscar "Bus de servicio" y seleccione el elemento de **Bus de servicio de Microsoft Azure** . A continuación, completar la instalación y cierre este cuadro de diálogo.

4.  En el Explorador de soluciones, haga clic en el proyecto **ProductsPortal** y luego haga clic en **Agregar**, a continuación, el **Elemento existente**.

5.  Vaya al archivo **ProductsContract.cs** del proyecto de consola **ProductsServer** . Haga clic para resaltar ProductsContract.cs. Haga clic en la flecha abajo situada junto a **Agregar**y luego haga clic en **Agregar como vínculo**.

    ![][24]

6.  Ahora, abra el archivo **HomeController.cs** en el editor de Visual Studio y reemplace la definición de espacio de nombres con el siguiente código. Asegúrese de reemplazar *yourServiceNamespace* con el nombre de su espacio de nombres de servicio y *yourKey* con la clave de SA. Esto le permitirá el cliente llamar al servicio local, que devuelve el resultado de la llamada.

    ```
    namespace ProductsWeb.Controllers
    {
        using System.Linq;
        using System.ServiceModel;
        using System.Web.Mvc;
        using Microsoft.ServiceBus;
        using Models;
        using ProductsServer;
    
        public class HomeController : Controller
        {
            // Declare the channel factory.
            static ChannelFactory<IProductsChannel> channelFactory;
    
            static HomeController()
            {
                // Create shared access signature token credentials for authentication.
                channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                    "sb://yourServiceNamespace.servicebus.windows.net/products");
                channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                    TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                        "RootManageSharedAccessKey", "yourKey") });
            }
    
            public ActionResult Index()
            {
                using (IProductsChannel channel = channelFactory.CreateChannel())
                {
                    // Return a view of the products inventory.
                    return this.View(from prod in channel.GetProducts()
                                     select
                                         new Product { Id = prod.Id, Name = prod.Name,
                                             Quantity = prod.Quantity });
                }
            }
        }
    }
    ```

7.  En el Explorador de soluciones, haga clic en la solución de **ProductsPortal** (asegúrese de haga la solución, no el proyecto). Haga clic en **Agregar**y luego haga clic en **Proyecto existente**.

8.  Desplácese al proyecto **ProductsServer** , a continuación, haga doble clic en el archivo de solución **ProductsServer.csproj** para agregarlo.

9.  **ProductsServer** debe estar ejecutándose para mostrar los datos en **ProductsPortal**. En el Explorador de soluciones, haga clic en la solución **ProductsPortal** y haga clic en **Propiedades**. Se muestra el cuadro de diálogo **Páginas de propiedades** .

10. En el lado izquierdo, haga clic en **Proyecto de inicio**. En el lado derecho, haga clic en **varios proyectos de inicio**. Asegurarse de que **ProductsServer** y **ProductsPortal** aparecen, en ese orden, con **Inicio** establecer como la acción para ambos.

      ![][25]

11. Aún en el cuadro de diálogo **Propiedades** , haga clic en **Dependencias del proyecto** en el lado izquierdo.

12. En la lista de **proyectos** , haga clic en **ProductsServer**. Asegúrese de que **ProductsPortal** está **desactivada** .

14. En la lista de **proyectos** , haga clic en **ProductsPortal**. Asegúrese de que está seleccionado **ProductsServer** . 

    ![][26]

15. Haga clic en **Aceptar** en el cuadro de diálogo de **Páginas de propiedades** .

## <a name="run-the-project-locally"></a>Ejecute el proyecto localmente

Para probar la aplicación localmente, en Visual Studio, presione **F5**. El servidor local (**ProductsServer**) debe empezar la primera y la aplicación de **ProductsPortal** debe empezar en una ventana del explorador. En este momento, verá que el inventario de productos muestra datos recuperados desde el sistema local de servicio de producto.

![][10]

Presione **Actualizar** en la página **ProductsPortal** . Cada vez actualice la página, verá que la aplicación de servidor mostrar un mensaje cuando `GetProducts()` de **ProductsServer** se denomina.

Cerrar ambos aplicaciones antes de continuar con el paso siguiente.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Implementar el proyecto ProductsPortal en una aplicación web de Azure

El siguiente paso es convertir el front-end de **ProductsPortal** en una aplicación web de Azure. En primer lugar, implemente el proyecto **ProductsPortal** , todos los pasos de la sección [implementar el proyecto web para la aplicación web de Azure](../app-service-web/web-sites-dotnet-get-started.md#deploy-the-web-project-to-the-azure-web-app). Una vez completada la implementación, vuelva a este tutorial y continúe con el paso siguiente.

> [AZURE.NOTE] Es posible que vea un mensaje de error en la ventana del explorador al proyecto web **ProductsPortal** se inicia automáticamente después de la implementación. Esto es normal y se produce porque la aplicación **ProductsServer** aún no está en ejecución.

Copie la dirección URL de la aplicación web implementada, como necesite la dirección URL en el siguiente paso. También puede obtener esta dirección URL de la ventana de actividad de servicio de aplicación de Azure en Visual Studio:

![][9] 

### <a name="set-productsportal-as-web-app"></a>Establecer ProductsPortal como aplicación web

Antes de ejecutar la aplicación en la nube, debe asegurarse de que se inicia **ProductsPortal** desde dentro de Visual Studio como una aplicación web.

1. En Visual Studio, haga clic en el proyecto **ProjectsPortal** y, a continuación, haga clic en **Propiedades**.

3. En la columna izquierda, haga clic en **Web**.

5. En la sección **Acción de inicio** , haga clic en el botón de la **Dirección URL de inicio** y, en el cuadro de texto, escriba la dirección URL de la aplicación web implementada anteriormente; Por ejemplo, `http://productsportal1234567890.azurewebsites.net/`.

    ![][27]

6. En el menú **archivo** , en Visual Studio, haga clic en **Guardar todo**.

7. En el menú de generación de Visual Studio, haga clic en **Volver a generar solución**.

## <a name="run-the-application"></a>Ejecute la aplicación

2.  Presione F5 para generar y ejecutar la aplicación. El servidor local (la aplicación de consola **ProductsServer** ) debe empezar la primera y la aplicación de **ProductsPortal** debe empezar en una ventana del explorador, como se muestra en la siguiente captura de pantalla. Observe de nuevo que muestra datos recuperados desde el sistema local de servicio de producto inventario de productos y muestra estos datos en la aplicación web. Compruebe la dirección URL para asegurarse de que se está ejecutando **ProductsPortal** en la nube, como una aplicación web de Azure. 

    ![][1]

    > [AZURE.IMPORTANT] La aplicación de consola **ProductsServer** debe ejecutarse y dar servicio a los datos a la aplicación **ProductsPortal** . Si el explorador muestra un error, espere unos segundos más **ProductsServer** cargar y mostrar el siguiente mensaje. A continuación, presione **Actualizar** en el explorador.

    ![][37]

3. En el explorador, presione **Actualizar** en la página **ProductsPortal** . Cada vez actualice la página, verá que la aplicación de servidor mostrar un mensaje cuando `GetProducts()` de **ProductsServer** se denomina.

    ![][38]

## <a name="next-steps"></a>Pasos siguientes  

Para obtener más información acerca de Bus de servicio, consulte los siguientes recursos:  

* [Bus de servicio de Azure][sbwacom]  
* [Cómo usar colas de Bus de servicio][sbwacomqhowto]  


  [0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Obtenga herramientas y SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  
  [11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
  [18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
  [19]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-6.png
  [9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
  [10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

  [21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png
  
  [36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
  [41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png


  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

