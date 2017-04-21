<properties
   pageTitle="Configurar el proyecto de Azure con varias configuraciones de servicio | Microsoft Azure"
   description="Obtenga información sobre cómo configurar un proyecto de servicio de nube Azure cambiando los archivos ServiceDefinition.csdef y ServiceConfiguration.cscfg."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="configuring-your-azure-project-using-multiple-service-configurations"></a>Configurar el proyecto de Azure con varias configuraciones de servicio

Un proyecto de servicio de nube Azure incluye dos archivos de configuración: ServiceDefinition.csdef y ServiceConfiguration.cscfg. Estos archivos se incluye con la aplicación de servicio de nube de Azure e implementados en Azure.

- El archivo **ServiceDefinition.csdef** contiene los metadatos que se requieren para los requisitos de la aplicación de servicio de nube, incluidos cuáles son las funciones que contiene el entorno de Azure. Este archivo también contiene valores de configuración que se aplican a todas las instancias. Estas opciones de configuración se pueden leer en tiempo de ejecución con la API de Azure servicio de hospedaje en tiempo de ejecución. No se puede actualizar este archivo mientras se ejecuta el servicio en Azure.

- El archivo de **ServiceConfiguration.cscfg** conjuntos de valores para la configuración definida en el archivo de definición de servicio y especifica el número de instancias de ejecución de cada rol. Este archivo se puede actualizar mientras se ejecuta el servicio de nube en Azure.

Las herramientas de Azure para Microsoft Visual Studio proporcionan páginas de propiedades que puede usar para configurar los valores almacenados en estos archivos. Para obtener acceso a las páginas de propiedades, haga doble clic en la referencia de la función debajo del proyecto de servicio de nube Azure en el Explorador de soluciones, o haga clic en la referencia de la función y elija **Propiedades**, como se muestra en la siguiente ilustración.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

Para obtener información sobre los esquemas subyacentes para la definición del servicio y los archivos de configuración de servicio, vea la [Referencia del esquema](https://msdn.microsoft.com/library/azure/dd179398.aspx). Para obtener más información sobre la configuración del servicio, consulte [cómo configurar los servicios de nube](./cloud-services/cloud-services-how-to-configure.md).

## <a name="configuring-role-properties"></a>Configurar propiedades de la función

Las páginas de propiedades para una función web y una función de trabajador son similares, aunque existen algunas diferencias, señaladas en las secciones siguientes.

Desde la página de **almacenamiento en caché** , puede configurar el almacenamiento en caché de los servicios de Azure.

### <a name="configuration-page"></a>Página de configuración

En la página **configuración** , puede establecer estas propiedades:

**Instancias**

Establezca la propiedad de recuento de **instancia** en el número de instancias que debe ejecutarse el servicio para esta función.

Establezca la propiedad **tamaño de memoria virtual** para **Pequeña adicional**, **pequeño**, **mediano**, **grande**o **Extra grande**.  Para obtener más información, vea [tamaños de servicios en la nube](./cloud-services/cloud-services-sizes-specs.md).

**Acción de inicio** (Solo en función de web)

Establecer esta propiedad para especificar que Visual Studio debe iniciar un explorador web para los extremos HTTP, los extremos HTTPS o ambos al iniciar la depuración.

La opción de extremo HTTPS solo está disponible si ya ha definido un extremo HTTPS para su rol. Puede definir un extremo HTTPS en la página de propiedades de **extremos** .

Si ya ha agregado un extremo HTTPS, la opción de extremo HTTPS está habilitada de forma predeterminada y Visual Studio se iniciará un explorador para este extremo al iniciar la depuración, además de un explorador para el extremo HTTP. Se supone que están habilitadas las opciones de inicio.

**Diagnósticos**

De forma predeterminada, los diagnósticos está habilitado para el rol de Web. La cuenta de project y almacenamiento del servicio de nube de Azure se establecen para usarlo de almacenamiento local. Cuando esté listo para implementar Azure, puede seleccionar el botón generador (**...**) para actualizar la cuenta de almacenamiento para usar el almacenamiento de Azure en la nube. Puede transferir los datos de diagnóstico a la cuenta de almacenamiento a petición o en intervalos programadas automáticamente. Para obtener más información acerca de diagnósticos de Azure, vea [Habilitar diagnósticos en servicios de nube de Azure y máquinas virtuales de Windows](./cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="settings-page"></a>Página de configuración

En la página **configuración** , puede agregar valores de configuración de su servicio. Opciones de configuración son pares de valor de nombre. Código que se ejecuta en el rol puede leer los valores de las opciones de configuración en tiempo de ejecución utilizando clases proporcionadas por la [Biblioteca de Azure administrada](http://go.microsoft.com/fwlink?LinkID=171026). Más concretamente, el método [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) devuelve el valor de una configuración con nombre en tiempo de ejecución.

### <a name="configuring-a-connection-string-to-a-storage-account"></a>Configurar una cadena de conexión a una cuenta de almacenamiento

Una cadena de conexión es un valor de configuración que proporciona información de conexión y la autenticación para el emulador de almacenamiento o para una cuenta de almacenamiento de Azure. Siempre que el código debe tener acceso a los datos de servicios de almacenamiento de Azure – es decir, blob, cola o datos de la tabla: desde el código que se ejecuta en una función, debe definir una cadena de conexión para la cuenta de almacenamiento.

Una cadena de conexión que apunta a una cuenta de almacenamiento de Azure debe usar un formato definido. Para obtener información sobre cómo crear cadenas de conexión, vea [Configurar las cadenas de conexión de almacenamiento de Azure](./storage/storage-configure-connection-string.md).

Cuando esté listo para probar el servicio en los servicios de almacenamiento de Azure, o cuando esté listo para implementar el servicio de nube de Azure, puede cambiar el valor de las cadenas de conexión para que apunten a su cuenta de almacenamiento de Azure. Seleccione (**...**), seleccione **las credenciales de cuenta de almacenamiento de entrar**. Escriba la información de cuenta que incluya el nombre de la cuenta y la clave de cuenta. En el cuadro de diálogo de **Cadena de conexión de cuenta de almacenamiento** , también puede indicar si desea usar los extremos HTTPS predeterminada (opción predeterminada), los extremos HTTP predeterminado o extremos personalizados. Decide usar extremos personalizadas si ha registrado un nombre de dominio personalizado para el servicio, como se describe en [configurar un nombre de dominio personalizado para datos blob en una cuenta de almacenamiento de Azure](./storage/storage-custom-domain-name.md).

>[AZURE.IMPORTANT] Debe modificar las cadenas de conexión para que apunten a una cuenta de almacenamiento de Azure antes de implementar el servicio. Si no lo puede hacer que la función no se inicie o para recorrer los Estados al iniciar, detener y ocupados.

## <a name="endpoints-page"></a>Página extremos

Una función de trabajo puede tener cualquier número de extremos HTTP, HTTPS o TCP. Extremos pueden ser extremos de entrada, que están disponibles para los clientes externos, o extremos internos, que están disponibles para otras funciones que se están ejecutando en el servicio.

- Para hacer que un extremo HTTP disponibles para exploradores Web y clientes externos, cambie el tipo de extremo de entrada y especifique un nombre y un número de puerto público.

- Para hacer que un extremo HTTPS disponibles para exploradores Web y clientes externos, cambie el tipo de extremo a **entrada**y especifique un nombre, un número de puerto público y el nombre de un certificado de administración.

    Tenga en cuenta que, para poder especificar un certificado de administración, debe definir el certificado en la página de propiedades de **certificados** .

- Para disponer de un punto final para el acceso interno por otras funciones en el servicio de nube, cambie el tipo de extremo interno y especifique un nombre y puertos privados posibles para este extremo.

## <a name="local-storage-page"></a>Página de almacenamiento local

Puede usar la página de propiedades de **Almacenamiento Local** para reservar uno o más recursos de almacenamiento local para un rol. Un recurso de almacenamiento local es un directorio reservado en el sistema de archivos de la máquina virtual Azure en el que se está ejecutando una instancia de una función.

## <a name="certificates-page"></a>Página de certificados

En la página de **certificados** , puede asociar certificados con su rol. Los certificados que agregue pueden usarse para configurar los extremos HTTPS en la página de propiedades de **extremos** .

La página de propiedades de **certificados** agrega información sobre sus certificados para la configuración del servicio. Tenga en cuenta que los certificados no se formatean con el servicio. debe cargar sus certificados por separado en Azure a través del [portal clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

Para asociar un certificado con su rol, proporcione un nombre para el certificado. Utilice este nombre para hacer referencia al certificado cuando configura un extremo HTTPS en la página de propiedades de **extremos** . A continuación, especifique si el almacén de certificados es el **Equipo Local** o **Usuario actual** y el nombre de la tienda. Por último, escriba la huella del certificado. Si el certificado está en la actual\Personal tienda (mi), puede escribir la huella del certificado, seleccione el certificado de una lista rellenada. Si reside en cualquier otra ubicación, escriba el valor de huella digital a mano.

Al agregar un certificado del almacén de certificados, todos los certificados intermedios se agregan automáticamente a las opciones de configuración para usted. Estos certificados intermedios también deben cargarse en Azure para configurar correctamente el servicio para SSL.

Todos los certificados de administración asociar con el servicio se aplican a su servicio solo cuando se ejecuta en la nube. Cuando se está ejecutando el servicio en el entorno de desarrollo local, se usa un certificado estándar que se administra mediante el emulador de cálculo.

## <a name="configuring-the-azure-cloud-service-project"></a>Configurar el proyecto de servicio de nube de Azure

Para configurar los valores que se aplican a un proyecto de servicio de nube de Azure todo, abrir el menú contextual para ese nodo de proyecto por primera vez y elija Propiedades para abrir las páginas de propiedades. La siguiente tabla muestra las páginas de propiedades.

|Página de propiedades|Descripción|
|---|---|
|Aplicación|Desde esta página, puede mostrar información sobre la versión de herramientas de Azure que usa este proyecto de servicio de nube y, a continuación, puede actualizar a la versión actual de las herramientas.|
|Generar eventos|Desde esta página, puede configurar eventos anteriores y posteriores a la compilación.|
|Desarrollo|Desde esta página, puede especificar las instrucciones de configuración de compilación y las condiciones en las que se ejecutan los eventos posteriores a la compilación.|
|Web|Desde esta página, puede configurar valores relacionados con el servidor web.|
