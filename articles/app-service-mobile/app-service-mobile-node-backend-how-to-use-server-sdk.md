<properties
    pageTitle="Cómo trabajar con el servidor de back-end Node.js SDK para las aplicaciones móviles | Servicio de aplicaciones de Azure"
    description="Obtenga información sobre cómo trabajar con el servidor de back-end Node.js SDK para las aplicaciones de Azure aplicación de servicio móvil."
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="node"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-azure-mobile-apps-nodejs-sdk"></a>Cómo usar el SDK de Node.js aplicaciones de Azure Mobile

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

En este artículo se proporciona información detallada y ejemplos que muestran cómo trabajar con un back-end de Node.js en las aplicaciones de Azure aplicación de servicio móvil.

## <a name="Introduction"></a>Introducción

Aplicaciones de Azure aplicación servicio Mobile proporciona la capacidad para agregar un acceso móvil optimizada datos API Web a una aplicación web.  El SDK de aplicaciones de Azure aplicación de servicio móvil se proporciona para aplicaciones web de ASP.NET y Node.js.  El SDK proporciona las siguientes operaciones:

- Operaciones de tabla (leer, insertar, actualizar, eliminar) para el acceso a datos
- Operaciones de personalizado API

Ambas operaciones proporcionan para la autenticación para todos los proveedores de identidades permitidos por el servicio de aplicación de Azure, incluidos proveedores de identidades social como Facebook, Twitter, Google y Microsoft Azure Active Directory de identidad de la empresa.

Puede encontrar ejemplos para cada caso de uso en el [directorio de ejemplos en GitHub].

## <a name="supported-platforms"></a>Plataformas admitidas

El SDK de nodo de aplicaciones de Azure Mobile es compatible con la versión actual de LTADOS de nodo y versiones posteriores.  A partir de escritura, la última versión de LTADOS es nodo v4.5.0.  Otras versiones de nodo pueden funcionar, pero no son compatibles.

El SDK de nodo de aplicaciones de Azure Mobile es compatible con dos controladores de base de datos: el controlador de nodo mssql admite SQL Azure y las instancias de SQL Server locales.  El controlador de sqlite3 admite SQLite bases de datos en una sola instancia.

### <a name="howto-cmdline-basicapp"></a>Cómo: crear un Node.js básica back-end mediante la línea de comandos

Cada back-end de Azure aplicación Servicio móvil aplicación Node.js se inicia como una aplicación de ExpressJS.  ExpressJS es el marco del servicio web más popular disponible para Node.js.  Puede crear un basic [Express] aplicación como sigue:

1. En una ventana de PowerShell o comando, cree un directorio para el proyecto.

        mkdir basicapp

2. Ejecutar npm inicialización para inicializar la estructura de paquete.

        cd basicapp
        npm init

    El comando de inicialización npm solicita un conjunto de preguntas iniciar el proyecto.  Ver el resultado de ejemplo:

    ![El resultado de inicialización npm][0]

3. Instalar las bibliotecas express y aplicaciones de azure mobile del repositorio npm.

        npm install --save express azure-mobile-apps

4. Crear un archivo de app.js para implementar el servidor móvil básico.

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Define a TodoItem table
        mobile.tables.add('TodoItem');

        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);

Esta aplicación crea una WebAPI móvil optimizada con un solo extremo (`/tables/TodoItem`) que proporciona acceso no autenticado a un almacén de datos SQL subyacente mediante un esquema dinámico.  Es adecuado para seguir el cliente biblioteca de inicio rápido:

- [Tutorial de cliente Android]
- [Tutorial de Apache Cordova cliente]
- [iOS tutorial de cliente]
- [Tutorial de cliente de la tienda Windows]
- [Tutorial de cliente Xamarin.iOS]
- [Tutorial de cliente Xamarin.Android]
- [Tutorial de cliente Xamarin.Forms]

Puede encontrar el código para esta aplicación básica de la [muestra basicapp en GitHub].

### <a name="howto-vs2015-basicapp"></a>Cómo: crear un nodo de back-end con Visual Studio de 2015

2015 de Visual Studio requiere una extensión para desarrollar aplicaciones Node.js dentro del IDE.  Para empezar, instale el [Node.js herramientas 1.1 para Visual Studio].  Una vez que están instaladas las herramientas de Node.js para Visual Studio, cree una aplicación de 4.x Express:

1. Abrir el cuadro de diálogo **Nuevo proyecto** ( **archivo** > **nuevo** > **proyecto...**).

2. Expanda **plantillas** > **JavaScript** > **Node.js**.

3. Seleccione la **aplicación básica Azure Node.js Express 4**.

4. Escriba el nombre del proyecto.  Haga clic en *Aceptar*.

    ![Nuevo proyecto de Visual Studio de 2015][1]

5. Haga clic en el nodo **npm** y seleccione **instalar nuevos paquetes de npm...**.

6. Debe actualizar el catálogo npm acerca de cómo crear su primera aplicación Node.js.  Si es necesario, haga clic en **Actualizar** .

7. En el cuadro Buscar, escriba _aplicaciones de azure mobile_ .  Haga clic en el paquete de **aplicaciones de mobile azure 2.0.0** y luego haga clic en **Instalar paquete**.

    ![Instalar paquetes de npm nuevos][2]

8. Haga clic en **Cerrar**.

9. Abra el archivo _app.js_ para agregar compatibilidad con el SDK de aplicaciones móviles de Azure.  En línea de la parte inferior de la biblioteca de 6 at requieren instrucciones, agregue el siguiente código:

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

    En línea aproximadamente 27 después de las otras instrucciones app.use, agregue el siguiente código:

        app.use('/users', users);

        // Azure Mobile Apps Initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use(mobile);

    Guarde el archivo.

10. Ejecute la aplicación localmente (se sirve la API http://localhost:3000) o publicar en Azure.

### <a name="create-node-backend-portal"></a>Cómo: crear un Node.js back-end con el portal de Azure

Puede crear un derecho de back-end de la aplicación móvil en el [portal de Azure]. Puede seguir los siguientes pasos o crear un servidor y cliente juntos siguiendo el tutorial de [crear una aplicación móvil](app-service-mobile-ios-get-started.md) . El tutorial contiene una versión simplificada de estas instrucciones y es la mejor para prueba de proyectos de concepto.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

En el módulo de _Introducción_ , en **crear una tabla API**, elija **Node.js** como el **idioma de back-end**. Active la casilla "**confirmar que esto sobrescribirá todos los contenidos del sitio.**" y luego haga clic en **crear TodoItem tabla**.

### <a name="download-quickstart"></a>Cómo: descargar el proyecto de código del tutorial rápido de back-end Node.js con Git

Cuando se crea un aplicación móvil de Node.js de back-end mediante el módulo de **Inicio rápido** de portal, un Node.js creará e implementar proyecto a su sitio. Puede agregar tablas y las API y editar archivos de código para el back-end de Node.js en el portal. También puede usar varias herramientas de implementación para descargar el proyecto de back-end para que puede agregar o modificar tablas y las API de y después vuelva a publicar el proyecto. Para obtener más información, consulte la [Guía de implementación de servicios de aplicación de Azure]. el procedimiento siguiente utiliza un repositorio Git para descargar el código del proyecto Tutorial.

1. Instale Git, si aún no lo ha hecho. Los pasos necesarios para instalar Git varían entre sistemas operativos. Vea [Instalar Git](http://git-scm.com/book/en/Getting-Started-Installing-Git) para distribuciones específicos del sistema operativo e instrucciones de instalación.

2. Siga los pasos de [Habilitar el repositorio de aplicación de servicio de aplicación](../app-service-web/app-service-deploy-local-git.md#Step3) para habilitar el repositorio Git de su sitio de back-end, realizar una nota de la implementación nombre de usuario y la contraseña.

3. En el módulo de la aplicación móvil de back-end, tome nota de la configuración de la **dirección URL de clonar Git** .

4. Ejecutar la `git clone` comando mediante la Git clona dirección URL, escribir la contraseña cuando sea necesario, como en el ejemplo siguiente:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Vaya al directorio local, que en el ejemplo anterior es /todolist y observe que se han descargado archivos de proyecto. Busque la `todoitem.json` de archivo de la `/tables` directorio.  Este archivo define permisos en la tabla.  También encontrará la `todoitem.js` secuencias de comandos de archivo en el mismo directorio, que define la operación CRUD para la tabla.

6. Después de realizar cambios en archivos de proyecto, ejecute los siguientes comandos para agregar, confirmar, cargue los cambios en el sitio:

        $ git commit -m "updated the table script"
        $ git push origin master

    Al agregar archivos nuevos al proyecto, primero debe ejecutar el `git add .` comando.

Cada vez que se inserta un nuevo conjunto de confirmaciones al sitio, se vuelva a publicar el sitio.

### <a name="howto-publish-to-azure"></a>Cómo: publicar su Node.js de back-end en Azure

Microsoft Azure proporciona muchos mecanismos para publicar su Node.js de aplicaciones de Azure aplicación Servicio móvil de back-end del servicio de Azure.  Estos incluyen el uso de herramientas de implementación integradas en Visual Studio, herramientas de línea de comandos y opciones de implementación continua en función de control de código fuente.  Para obtener más información sobre este tema, consulte la [Guía de implementación de servicios de aplicación de Azure].

Servicio de aplicaciones de Azure tiene consejos específicos para la aplicación de Node.js que debe revisar antes de implementar:

- Cómo [especificar la versión de nodo]
- Cómo [utilizar módulos de nodo]

### <a name="howto-enable-homepage"></a>Cómo: habilitar una página de inicio de la aplicación

Muchas aplicaciones son una combinación de web y aplicaciones móviles y el marco de trabajo de ExpressJS le permite combinar los dos aspectos.  A veces, sin embargo, puede implementar solo una interfaz móvil.  Resulta útil proporcionar una página de inicio para garantizar el servicio de la aplicación está funcionando.  Puede proporcionar su propia página de inicio o habilitar una página de inicio temporal.  Para habilitar una página de inicio temporal, utilice lo siguiente para crear una instancia de aplicaciones de Azure Mobile:

    var mobile = azureMobileApps({ homePage: true });

Si solo desea que esta opción está disponible al desarrollar localmente, puede agregar este valor para su `azureMobile.js` archivo.

## <a name="TableOperations"></a>Operaciones de tabla 

El SDK de servidor de aplicaciones de azure mobile Node.js proporciona mecanismos para exponer tablas de datos almacenadas en la base de datos de Azure SQL como un WebAPI.  Se proporcionan cinco operaciones.

| Operación | Descripción |
| --------- | ----------- |
| OBTENER /tables/_nombre de tabla_ | Obtener todos los registros de la tabla |
| OBTENER /tables/ /:id de_nombre de tabla_ | Obtener un registro específico de la tabla |
| PUBLICAR /tables/_nombre de tabla_ | Crear un registro en la tabla |
| REVISIÓN /tables/ /:id de_nombre de tabla_ | Actualizar un registro en la tabla |
| ELIMINAR /tables/ /:id de_nombre de tabla_ | Eliminar un registro en la tabla |

Este WebAPI es compatible con [OData] y extiende el esquema de la tabla para admitir la [sincronización de datos sin conexión].

### <a name="howto-dynamicschema"></a>Cómo: definir tablas mediante un esquema dinámico

Antes de poder usar una tabla, deben definirse.  Pueden definirse tablas con un esquema estático (donde el desarrollador define las columnas dentro del esquema) o dinámica (donde el SDK controla el esquema basado en las solicitudes entrantes). Además, el desarrollador puede controlar aspectos específicos de la WebAPI al agregar código Javascript a la definición.

Como práctica recomendada, debe definir cada tabla en un archivo de Javascript en el directorio de tablas y luego use el método tables.import() para importar las tablas.  Extender la aplicación de basic, se ajustará el archivo app.js:

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define the database schema that is exposed
    mobile.tables.import('./tables');

    // Provide initialization of any tables that are statically defined
    mobile.tables.initialize().then(function () {
        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);
    });

Definir la tabla. / tables/TodoItem.js:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here

    module.exports = table;

Las tablas usan esquema dinámicas de forma predeterminada.  Para desactivar globalmente esquema dinámico, establezca la configuración de la aplicación **MS_DynamicSchema** en falso en el portal de Azure.

Puede encontrar un ejemplo completo en el [ejemplo todo en GitHub].

### <a name="howto-staticschema"></a>Cómo: definir tablas mediante un esquema estático

Puede definir explícitamente las columnas que se expone a través de la WebAPI.  El SDK de Node.js aplicaciones de azure mobile agrega automáticamente las columnas adicionales necesarias para la sincronización de datos sin conexión a la lista que proporciona.  Por ejemplo, las aplicaciones cliente de tutorial rápido requieren una tabla con dos columnas: texto (cadena) y complete (un valor boolean).  
La tabla puede definirse en la tabla definición archivo JavaScript (que se encuentra en el directorio de tablas) como sigue:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    module.exports = table;

Si define tablas estática, también debe llamar al método de tables.initialize() para crear el esquema de la base de datos en el inicio.  El método tables.initialize() devuelve una [promesa] para que el servicio web no servir solicitudes antes de la base de datos que se ha inicializado.

### <a name="howto-sqlexpress-setup"></a>Cómo: usar SQL Express como un almacén de datos de desarrollo en el equipo local

El Azure Mobile aplicaciones la AzureMobile aplicaciones nodo SDK proporciona tres opciones para servir datos fuera de la caja: SDK ofrece tres opciones para servir datos fuera del cuadro:

- Usar el controlador de **memoria** para proporcionar un almacén de ejemplo no persistente
- Usar el controlador de **mssql** para proporcionar un almacén de datos SQL Express desarrollo
- Usar el controlador de **mssql** para proporcionar un almacén de datos de la base de datos de SQL Azure producción

El SDK de Node.js aplicaciones de Azure Mobile usa el [paquete de Node.js mssql] establecer y usar una conexión de SQL Express y base de datos de SQL.  Este paquete requiere que habilite las conexiones TCP en su instancia de SQL Express.

> [AZURE.TIP]El controlador de memoria no proporciona un conjunto completo de servicios para probar.  Si desea probar su servidor local, se recomienda el uso de un almacén de datos SQL Express y el controlador de mssql.

1. Descargue e instale [Microsoft SQL Server 2014 Express].  Asegúrese de que instalar SQL Server 2014 Express con la edición de herramientas.  A menos que explícitamente necesita asistencia de 64 bits, la versión de 32 bits consume menos memoria cuando se ejecuta.

2. Ejecute el Administrador de configuración de SQL Server de 2014.

  1. Expanda el nodo de **Configuración de red de SQL Server** en el menú de árbol de la izquierda.
  2. Haga clic en **protocolos de SQLEXPRESS**.
  3. Haga clic en **TCP/IP** y seleccione **Habilitar**.  En el cuadro de diálogo emergente, haga clic en **Aceptar** .
  4. Haga clic en **TCP/IP** y seleccione **Propiedades**.
  5. Haga clic en la ficha **Direcciones IP** .
  6. Busque el nodo **IPAll** .  En el campo **Puerto TCP** , escriba **1433**.

         ![Configure SQL Express for TCP/IP][3]

  7. Haga clic en **Aceptar**.  En el cuadro de diálogo emergente, haga clic en **Aceptar** .
  8. En el menú de árbol de la izquierda, haga clic en **Servicios de SQL Server** .
  9. Haga clic en **SQL Server (SQLEXPRESS)** y seleccione **reiniciar**
  10. Cierre el Administrador de configuración de SQL Server de 2014.

3. Ejecutar la 2014 de SQL Server Management Studio y conectarse a la instancia de SQL Express local

  1. Haga clic en la instancia en el Explorador de objetos y seleccione **Propiedades**
  2. Seleccione la página de **seguridad** .
  3. Asegúrese de que está seleccionado el **modo de autenticación de Windows y de SQL Server**
  4. Haga clic en **Aceptar**

        ![Configurar la autenticación de SQL Express][4]

  5. Expanda **seguridad** > **inicios de sesión** en el Explorador de objetos
  6. Haga clic en **inicios de sesión** y seleccione **Nuevo inicio de sesión...**
  7. Escriba un nombre de inicio de sesión.  Seleccione **autenticación de SQL Server**.  Escriba una contraseña y luego escriba la misma contraseña en **Confirmar contraseña**.  La contraseña debe cumplir los requisitos de complejidad de Windows.
  8. Haga clic en **Aceptar**

        ![Agregar un nuevo usuario a SQL Express][5]

  9. Haga clic en el nuevo inicio de sesión y seleccione **Propiedades**
  10. Seleccione la página de **Roles del servidor**
  11. Active la casilla situada junto a la función de servidor **dbcreator**
  12. Haga clic en **Aceptar**
  13. Cierre SQL Server 2015 Management Studio

Asegúrese de que registrar el nombre de usuario y la contraseña que seleccionó.  Debe asignar roles de servidor adicionales o permisos según las necesidades específicas de la base de datos.

La aplicación de Node.js lee la variable de entorno **SQLCONNSTR_MS_TableConnectionString** para la cadena de conexión para esta base de datos.  Puede establecer esta variable dentro de su entorno.  Por ejemplo, puede usar PowerShell para establecer esta variable de entorno:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Acceso a la base de datos a través de una conexión de TCP/IP y proporcionar un nombre de usuario y una contraseña para la conexión.

### <a name="howto-config-localdev"></a>Cómo: configurar el proyecto para el desarrollo local

Aplicaciones de Azure Mobile lee un archivo de JavaScript denominado _azureMobile.js_ desde el sistema de archivos local.  No utilice este archivo para configurar el SDK de aplicaciones móviles de Azure en producción: usar la configuración de la aplicación en el [portal de Azure] en su lugar.  El archivo _azureMobile.js_ debe exportar un objeto de configuración.  Los valores más comunes son:

- Configuración de la base de datos
- Configuración de registro de diagnóstico
- Configuración de CORS alternativo

A continuación se muestra un ejemplo de archivo de _azureMobile.js_ implementar la configuración de la base de datos anterior:

    module.exports = {
        cors: {
            origins: [ 'localhost' ]
        },
        data: {
            provider: 'mssql',
            server: '127.0.0.1',
            database: 'mytestdatabase',
            user: 'azuremobile',
            password: 'T3stPa55word'
        },
        logging: {
            level: 'verbose'
        }
    };

Le recomendamos que agregue _azureMobile.js_ al archivo _.gitignore_ (u otro control de código fuente Omitir archivo) para evitar que las contraseñas no pueden almacenarse en la nube.  Configure siempre la configuración de producción en configuración de la aplicación en el [portal de Azure].

### <a name="howto-appsettings"></a>Cómo: Establecer la configuración de la aplicación de la aplicación móvil

La mayoría de configuración en el archivo _azureMobile.js_ tiene una configuración equivalente de la aplicación en el [portal de Azure].  Use la siguiente lista para configurar la aplicación de configuración de la aplicación:

| Configuración de la aplicación                 | Configuración de _azureMobile.js_  | Descripción                               | Valores válidos                                |
| :-------------------------- | :------------------------ | :---------------------------------------- | :------------------------------------------ |
| **MS_MobileAppName**        | nombre                      | El nombre de la aplicación                       | cadena                                      |
| **MS_MobileLoggingLevel**   | Logging.Level             | Nivel de registro mínimo de mensajes en el registro      | error, advertencia, información, detallado, depura, tonto |
| **MS_DebugMode**            | depurar                     | Habilitar o deshabilitar el modo de depuración              | Verdadero, Falso                                 |
| **MS_TableSchema**          | Data.Schema               | Nombre del esquema predeterminado para las tablas SQL        | cadena (predeterminado: dbo)                       |
| **MS_DynamicSchema**        | data.dynamicSchema        | Habilitar o deshabilitar el modo de depuración              | Verdadero, Falso                                 |
| **MS_DisableVersionHeader** | versión (establecida a sin definir)| Deshabilita el encabezado X-ZUMO-versión de servidor | Verdadero, Falso                                 |
| **MS_SkipVersionCheck**     | skipversioncheck          | Deshabilita la comprobación de versión de la API de cliente     | Verdadero, Falso                                 |

Para establecer una configuración de aplicación:

1. Inicie sesión en el [portal de Azure].
2. Seleccione **todos los recursos** o los **Servicios de aplicación** , a continuación, haga clic en el nombre de la aplicación móvil.
3. Se abre el módulo de la configuración predeterminada. Si no aparece, haga clic en **configuración**.
4. En el menú GENERAL, haga clic en **configuración de la aplicación** .
5. Desplácese hasta la sección de configuración de la aplicación.
6. Si su aplicación establece ya existe, haga clic en el valor de la configuración de la aplicación para editar el valor.
7. Si no existe la configuración de la aplicación, escriba la configuración de la aplicación en el cuadro clave y el valor en el cuadro valor.
8. Cuando haya finalizado, haga clic en **Guardar**.

Cambiar la configuración de aplicación mayoría requiere un reinicio del servicio.

### <a name="howto-use-sqlazure"></a>Cómo: usar la base de datos de SQL como el almacén de datos de producción

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Usar la base de datos de SQL Azure como un almacén de datos es idéntico en todos los tipos de aplicación de servicio de aplicaciones de Azure. Si aún no lo ha hecho, siga estos pasos para crear un aplicación móvil de back-end.

1. Inicie sesión en el [portal de Azure].

2. En la parte superior izquierda de la ventana, haga clic en el botón **+ nuevo** > **Web + Mobile** > **Aplicación móvil**, a continuación, proporcione un nombre para la aplicación móvil de back-end.

3. En el cuadro de **Grupo de recursos** , escriba el mismo nombre que la aplicación.

4. Se selecciona el plan de servicio de aplicación predeterminado.  Si desea cambiar su plan de servicios de aplicación, puede hacerlo haciendo clic en el Plan de servicios de aplicación > **+ Crear nuevo**.  Proporcione un nombre de nuevo plan de servicio de aplicaciones y seleccione una ubicación apropiada.  Haga clic en el nivel de precios y seleccione un nivel de precio adecuado para el servicio. Seleccione **Ver todos** para ver más opciones de precios, como **Free** y **compartido**.  Una vez que haya seleccionado el nivel de precios, haga clic en el botón **Seleccionar** .  En el módulo de **plan de servicios de aplicación** , haga clic en **Aceptar**.

5. Haga clic en **crear**. Un aplicación móvil de back-end de aprovisionamiento puede tardar un par de minutos.  Una vez que se aprovisiona la aplicación móvil de back-end, el portal abre el módulo de **configuración** de la aplicación móvil de back-end.

Una vez creado la aplicación móvil de back-end, puede conectarse a una base de datos SQL de la aplicación móvil de back-end o crear una nueva base de datos SQL.  En esta sección, creamos una base de datos SQL.

> [AZURE.NOTE]Si ya tiene una base de datos en la misma ubicación que el servidor de la aplicación móvil, puede elegir en su lugar de **usar una base de datos existente** y, a continuación, seleccione dicha base de datos. No se recomienda el uso de una base de datos en una ubicación diferente debido a la latencia superior.

6. En el servidor nuevo de la aplicación móvil, haga clic en **configuración de** > **Aplicación Mobile** > **datos** > **+ Agregar**.

7. En el módulo de **Agregar conexión de datos** , haga clic en **Base de datos de SQL: configurar valores necesarios** > **crear una nueva base de datos**.  En el campo **nombre** , escriba el nombre de la nueva base de datos.

8. Haga clic en el **servidor**.  En el módulo de **servidor de nuevo** , escriba un nombre de servidor únicos en el campo **nombre de servidor** y proporcione una **contraseña**y adecuado de **Inicio de sesión de administrador de servidor** .  Asegúrese de **que permitir azure servicios para tener acceso a servidor** está activada.  Haga clic en **Aceptar**.

    ![Crear una base de datos SQL Azure][6]

9. En el módulo de la **nueva base de datos** , haga clic en **Aceptar**.

10. En el módulo de **Agregar conexión de datos** , seleccione la **cadena de conexión**, especifique el inicio de sesión y la contraseña que proporcionan al crear la base de datos.  Si usa una base de datos existente, proporcione las credenciales de inicio de sesión para dicha base de datos.  Una vez introducida, haga clic en **Aceptar**.

11. Vuelva a hacer en el módulo de **Agregar conexión de datos** , haga clic en **Aceptar** para crear la base de datos.

<!--- END OF ALTERNATE INCLUDE -->

Creación de la base de datos puede tardar unos minutos.  Use el área de **notificaciones** para supervisar el progreso de la implementación.  No de progreso hasta que la base de datos se ha implementado correctamente.  Una vez implementada correctamente, se crea una cadena de conexión para la instancia de base de datos SQL en la configuración de la aplicación de back-end móvil.  Puede ver la configuración de esta aplicación en la **configuración de** > **configuración de la aplicación** > **cadenas de conexión**.

### <a name="howto-tables-auth"></a>Cómo: requerir autenticación para el acceso a tablas

Si desea usar la aplicación de servicio de autenticación con el extremo de tablas, primero debe configurar autenticación de servicio de aplicación en el [portal de Azure] .  Para obtener más detalles sobre la configuración de autenticación en un servicio de aplicación de Azure, revise a la Guía de configuración para el proveedor de identidades que se va a utilizar:

- [Cómo configurar la autenticación de Azure Active Directory]
- [Cómo configurar la autenticación de Facebook]
- [Cómo configurar la autenticación de Google]
- [Cómo configurar Microsoft Authentication]
- [Cómo configurar la autenticación de Twitter]

Cada tabla tiene una propiedad de access que se pueden usar para controlar el acceso a la tabla.  En el ejemplo siguiente se muestra una tabla estática definida con requiere autenticación.

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

La propiedad de access puede realizar uno de estos tres valores

  - *anónimo* indica que la aplicación cliente se permite leer datos sin autenticación
  - *se han autenticado* indica que la aplicación cliente debe enviar un símbolo de autenticación válido con la solicitud
  - *deshabilitado* indica que esta tabla está actualmente deshabilitada

Si está definida la propiedad de access, se permite el acceso no autenticado.

### <a name="howto-tables-getidentity"></a>Cómo: usar notificaciones de autenticación con las tablas

Puede configurar notificaciones distintos que se solicitan cuando se configura la autenticación.  Estas notificaciones normalmente no están disponibles a través de la `context.user` objeto.  Sin embargo, se pueden recuperar utilizando la `context.user.getIdentity()` método.  La `getIdentity()` método devuelve una promesa que se resuelve en un objeto.  El objeto es de clave con el método de autenticación (facebook, google, twitter, microsoftaccount o aad).

Por ejemplo, si configurar autenticación de Account de Microsoft y solicitud de que las direcciones de correo electrónico de notificación, puede agregar la dirección de correo electrónico al registro con el controlador de la tabla siguiente:

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    table.columns = {
        "emailAddress": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;
    table.access = 'authenticated';

    /**
    * Limit the context query to those records with the authenticated user email address
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function queryContextForEmail(context) {
        return context.user.getIdentity().then((data) => {
            context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
            return context.execute();
        });
    }

    /**
    * Adds the email address from the claims to the context item - used for
    * insert operations
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function addEmailToContext(context) {
        return context.user.getIdentity().then((data) => {
            context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
            return context.execute();
        });
    }

    // Configure specific code when the client does a request
    // READ - only return records belonging to the authenticated user
    table.read(queryContextForEmail);

    // CREATE - add or overwrite the userId based on the authenticated user
    table.insert(addEmailToContext);

    // UPDATE - only allow updating of record belong to the authenticated user
    table.update(queryContextForEmail);

    // DELETE - only allow deletion of records belong to the authenticated uer
    table.delete(queryContextForEmail);

    module.exports = table;

Para ver qué notificaciones están disponibles, utilice un explorador web para ver el `/.auth/me` extremo del sitio.

### <a name="howto-tables-disabled"></a>Cómo: deshabilitar el acceso a operaciones específicas de una tabla

Además de los que aparecen en la tabla, la propiedad de access puede usarse para controlar las operaciones individuales.  Hay cuatro operaciones:

  - *leer* es la operación obtener REST de la tabla
  - *Insertar* es la operación de publicación de REST de la tabla
  - *Actualizar* es la operación de REST de revisiones de la tabla
  - *Eliminar* es la operación REST eliminar de la tabla

Por ejemplo, desea proporcionar una tabla no autenticada sólo lectura:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-Only table - only allow READ operations
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>Cómo: ajustar la consulta que se utiliza con operaciones de tabla

Un requisito común para operaciones de tabla es proporcionar una vista restringida de los datos.  Por ejemplo, puede proporcionar una tabla que esté etiquetada con el identificador de usuario autenticado tal que solo puede leer o actualizar sus propios registros.  La definición de la tabla siguiente proporciona esta funcionalidad:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define a static schema for the table
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;

    // Require authentication for this table
    table.access = 'authenticated';

    // Ensure that only records for the authenticated user are retrieved
    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

    // When adding records, add or overwrite the userId with the authenticated user
    table.insert(function (context) {
        context.item.userId = context.user.id;
        return context.execute();
    });

    module.exports = table;

Las operaciones que normalmente ejecutan una consulta tienen una propiedad de consulta que puede ajustar con where cláusula. La propiedad query es un objeto [QueryJS] que se usa para convertir una consulta de OData en algo que puede procesar lo datos de back-end.  En caso de igualdad simple (como el anterior), puede usarse un mapa. También puede agregar cláusulas específicas de SQL:

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>Cómo: configurar eliminar suave en una tabla

Eliminar suave no elimina registros.  En su lugar marca como eliminados dentro de la base de datos mediante la configuración de la columna eliminada true.  El SDK de aplicaciones móviles de Azure automáticamente quita registros eliminados suaves resultados a menos que el SDK de cliente móvil usa IncludeDeleted().  Para configurar una tabla para eliminar suave, establezca la `softDelete` propiedad en el archivo de definición de tabla:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Turn on Soft Delete
    table.softDelete = true;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

Debe establecer un mecanismo para purgar registros - desde una aplicación de cliente, mediante una WebJob, en función de Azure o a través de una API personalizada.

### <a name="howto-tables-seeding"></a>Cómo: iniciar la base de datos con datos

Al crear una nueva aplicación, puede iniciar una tabla con los datos.  Esto se puede hacer en el archivo de JavaScript de definición de tabla como sigue:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };
    table.seed = [
        { text: 'Example 1', complete: false },
        { text: 'Example 2', complete: true }
    ];

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

Inicialización de datos se realiza solo cuando la tabla se crea mediante el SDK de aplicaciones móviles de Azure.  Si la tabla ya existe en la base de datos, datos no se insertan en la tabla.  Si está activado el esquema dinámico, se deduce el esquema de los datos inicializados.

Se recomienda llamar explícitamente la `tables.initialize()` método para crear la tabla cuando se inicia.

### <a name="Swagger"></a>Cómo: habilitar la compatibilidad con Swagger

Aplicaciones de Azure aplicación de servicio móvil incluye integrado [Swagger] soporte.  Para habilitar la compatibilidad de Swagger, instale primero la interfaz de usuario swagger como dependencia:

    npm install --save swagger-ui

Una vez instalado, puede habilitar la compatibilidad de Swagger en el constructor de aplicaciones de Azure Mobile:

    var mobile = azureMobileApps({ swagger: true });

Usted probablemente sólo desea habilitar la compatibilidad con Swagger en las ediciones de desarrollo.  Puede hacerlo mediante el uso de la `NODE_ENV` configuración de aplicación:

    var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });

El extremo de swagger se encuentra en http://_su sitio_.azurewebsites.net/swagger.  Puede tener acceso a la interfaz de usuario Swagger a través de la `/swagger/ui` punto final.  Si decide requerir autenticación en toda la aplicación, Swagger genera un error.  Para obtener los mejores resultados, elija Permitir las solicitudes no autenticadas a través de la autenticación de servicio de aplicación de Azure / configuración de autorización, a continuación, control de autenticación mediante el `table.access` propiedad.

También puede agregar la opción Swagger para su `azureMobile.js` archivo si solo desea Swagger soporte al desarrollar localmente.

## <a name="a-namepushpush-notifications"></a><a name="push">Notificaciones de inserción

Aplicaciones móviles se integra con Azure Hubs de notificación para que pueda enviar notificaciones de inserción destino a millones de dispositivos en todas las plataformas principales. Usando Hubs de notificación, puede enviar notificaciones de inserción a iOS, Android y Windows dispositivos. Para obtener que más información acerca de todo lo que pueden hacer con Hubs de notificación, vea [Información general de Hubs de notificación](../notification-hubs/notification-hubs-push-notification-overview.md).

### </a><a name="send-push"></a>Cómo: enviar notificaciones de inserción

El código siguiente muestra cómo usar el objeto de inserción para enviar una notificación de inserción de difusión para dispositivos iOS registrados:

    // Create an APNS payload.
    var payload = '{"aps": {"alert": "This is an APNS payload."}}';

    // Only do the push if configured
    if (context.push) {
        // Send a push notification using APNS.
        context.push.apns.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

Mediante la creación de un registro de inserción de plantilla desde el cliente, en su lugar puede enviar un mensaje de inserción de plantilla para dispositivos en todas las plataformas compatibles. El código siguiente muestra cómo enviar una notificación de plantilla:

    // Define the template payload.
    var payload = '{"messageParam": "This is a template payload."}';

    // Only do the push if configured
    if (context.push) {
        // Send a template notification.
        context.push.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }


###<a name="push-user"></a>Cómo: enviar notificaciones de inserción a un usuario autenticado con etiquetas

Cuando un usuario autenticado se registra para las notificaciones de inserción, una etiqueta de ID de usuario se agrega automáticamente al registro. Mediante el uso de esta etiqueta, puede enviar notificaciones de inserción a todos los dispositivos registrados por un usuario específico. El código siguiente obtiene al SID del usuario que hace la solicitud y envía una notificación de inserción de plantilla para cada registro de dispositivo para ese usuario:

    // Only do the push if configured
    if (context.push) {
        // Send a notification to the current user.
        context.push.send(context.user.id, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

Cuando se registra para las notificaciones de inserción de un cliente autenticado, asegúrese de que la autenticación es completa antes de intentar el registro.

## <a name="CustomAPI"></a>API personalizadas

###  <a name="howto-customapi-basic"></a>Cómo: definir una API personalizada


Además de la API de acceso datos a través del extremo de /tables, aplicaciones de Azure Mobile puede proporcionar cobertura de API personalizado.  Personalizado API se definen de manera similar a las definiciones de tabla y puede tener acceso a las mismas instalaciones, incluida la autenticación.

Si desea usar la aplicación de servicio de autenticación con una API personalizado, primero debe configurar autenticación de servicio de aplicación en el [portal de Azure] .  Para obtener más detalles sobre la configuración de autenticación en un servicio de aplicación de Azure, revise a la Guía de configuración para el proveedor de identidades que se va a utilizar:

- [Cómo configurar la autenticación de Azure Active Directory]
- [Cómo configurar la autenticación de Facebook]
- [Cómo configurar la autenticación de Google]
- [Cómo configurar Microsoft Authentication]
- [Cómo configurar la autenticación de Twitter]

API personalizadas se definen en la misma manera que la API de tablas.

1. Crear un directorio de la **api**
2. Crear un archivo de JavaScript API definición en el directorio de la **api** .
3. Use el método de importación para importar el directorio de la **api** .

Aquí es la definición de la api de prototipo basada en el ejemplo de aplicación basic que hemos usado anteriormente.

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Import the Custom API
    mobile.api.import('./api');

    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

Veamos un ejemplo API que devuelve la fecha de servidor con el método _Date.now()_ .  Éste es el archivo api/date.js:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };

    module.exports = api;

Cada parámetro es uno de los verbos estándares REST - GET, POST, revisión o eliminar.  El método es una función de [Software intermedio ExpressJS] estándar que envía el resultado esperado.

### <a name="howto-customapi-auth"></a>Cómo: requerir autenticación para tener acceso a una API personalizado

Azure SDK de aplicaciones móviles implementa la autenticación en la misma forma por el extremo de tablas y la API personalizadas.  Para agregar autenticación a la API desarrollada en la sección anterior, agregue una propiedad de **access** :

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };
    // All methods must be authenticated.
    api.access = 'authenticated';

    module.exports = api;

También puede especificar la autenticación en operaciones específicas:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        }
    };
    // The GET methods must be authenticated.
    api.get.access = 'authenticated';

    module.exports = api;

El mismo token que se usa para el extremo de tablas deben usarse para API personalizadas que requieren autenticación.

### <a name="howto-customapi-auth"></a>Cómo: controlar cargas de gran tamaño de archivo

SDK de aplicaciones de Azure Mobile usa el [software intermedio de analizador de cuerpo](https://github.com/expressjs/body-parser) Aceptar y descodificar el contenido del cuerpo en la presentación.  Puede configurar previamente analizador de cuerpo para aceptar la carga de archivo más grande:

    var express = require('express'),
        bodyParser = require('body-parser'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Set up large body content handling
    app.use(bodyParser.json({ limit: '50mb' }));
    app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

    // Import the Custom API
    mobile.api.import('./api');

    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

El archivo está codificado antes de la transmisión de base 64.  Esto aumenta el tamaño de la carga real (y, por tanto, el tamaño debe tener en cuenta para).

### <a name="howto-customapi-sql"></a>Cómo: ejecutar instrucciones SQL personalizadas

El SDK de aplicaciones de Azure Mobile permite el acceso a todo el contexto a través del objeto de la solicitud, lo que le permite ejecutar instrucciones SQL parametrizadas para el proveedor de datos definido fácilmente:

    var api = {
        get: function (request, response, next) {
            // Check for parameters - if not there, pass on to a later API call
            if (typeof request.params.completed === 'undefined')
                return next();

            // Define the query - anything that can be handled by the mssql
            // driver is allowed.
            var query = {
                sql: 'UPDATE TodoItem SET complete=@completed',
                parameters: [{
                    completed: request.params.completed
                }]
            };

            // Execute the query.  The context for Azure Mobile Apps is available through
            // request.azureMobile - the data object contains the configured data provider.
            request.azureMobile.data.execute(query)
            .then(function (results) {
                response.json(results);
            });
        }
    };

    api.get.access = 'authenticated';
    module.exports = api;

## <a name="Debugging"></a>Depurar, fáciles tablas y las API de fáciles

### <a name="howto-diagnostic-logs"></a>Cómo: depurar, diagnosticar y solucionar problemas de aplicaciones móviles de Azure

El servicio de aplicación de Azure proporciona varias depuración y técnicas para aplicaciones de Node.js de solución de problemas.
Consulte los artículos siguientes para empezar en su móvil Node.js de back-end de solución de problemas:

- [Supervisar el servicio de una aplicación de Azure]
- [Habilitar el registro de diagnóstico en Azure de aplicación de servicio]
- [Solucionar problemas de un servicio de aplicación de Azure en Visual Studio]

Aplicaciones de Node.js tienen acceso a una amplia variedad de herramientas de registro de diagnóstico.  Internamente, el SDK de Node.js aplicaciones de Azure Mobile utiliza [Winston] para el registro de diagnóstico.  Registro se habilita automáticamente al habilitar el modo de depuración o estableciendo la configuración de la aplicación **MS_DebugMode** en true en el [portal de Azure]. Registros generados aparecen en los registros de diagnóstico en el [portal de Azure].

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Cómo: trabajar con tablas fácil en el portal de Azure

Fáciles tablas en el portal le permiten crear y trabajar con directamente de tablas en el portal. Incluso puede editar las operaciones de tabla mediante el Editor del servicio de aplicación.

Al hacer clic en **tablas fáciles** en la configuración del sitio de back-end, puede agregar, modificar o eliminar una tabla. También puede ver datos de la tabla.

![Trabajar con tablas fáciles](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Los comandos siguientes están disponibles en la barra de comandos para una tabla:

+ **Cambiar permisos** - modificar el permiso de lectura, insertar, actualizar y eliminar operaciones en la tabla. 
  Opciones son permitir el acceso anónimo, requerir autenticación, o deshabilitar todo el acceso a la operación. 
+ **Editar script** - el archivo de comandos de la tabla se abre en el Editor del servicio de aplicación.
+ **Administrar esquema** - agregar o eliminar columnas o cambiar el índice de la tabla.
+ **Borrar tabla** - trunca una tabla existente se eliminen todas las filas de datos y dejar el esquema sin cambios.
+ **Eliminar filas** : eliminar filas individuales de datos.
+ **Ver registros de transmisión** : se conecta al servicio de registro de transmisiones de su sitio.

###<a name="work-easy-apis"></a>Cómo: trabajar con las API de fácil en el portal de Azure

Las API de fáciles en el portal le permiten crear y trabajar con derecho API personalizado en el portal. Puede editar las secuencias de comandos de la API mediante el Editor del servicio de aplicación.

Al hacer clic en **Las API de fácil** en la configuración del sitio de back-end, puede agregar, modificar o eliminar un extremo de la API personalizado.

![Trabajar con las API de fáciles](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

En el portal, puede cambiar los permisos de acceso para una acción determinada de HTTP, editar el archivo de comandos de la API en el Editor del servicio de aplicación o ver los registros de transmisión por secuencias.

###<a name="online-editor"></a>Cómo: editar código en el Editor del servicio de aplicación

El portal de Azure le permite modificar los archivos de script de back-end de Node.js en el Editor del servicio de aplicación sin tener que descargar el proyecto en el equipo local. Para editar archivos de secuencia de comandos en el editor en línea:

1. En el módulo de back-end de aplicación móvil, haga clic en **toda la configuración** > **fáciles tablas** o **Las API de fácil**, haga clic en una tabla o API y luego haga clic en **Editar script**. En el Editor del servicio de aplicación, se abre el archivo de script.

    ![Editor de servicios de aplicación](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)

2. Realice los cambios en el archivo de código en el editor en línea. Cambios se guardan automáticamente mientras escribe.


<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Tutorial de cliente Android]: app-service-mobile-android-get-started.md
[Tutorial de Apache Cordova cliente]: app-service-mobile-cordova-get-started.md
[iOS tutorial de cliente]: app-service-mobile-ios-get-started.md
[Tutorial de cliente Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
[Tutorial de cliente Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Tutorial de cliente Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Tutorial de cliente de la tienda Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[HTML/Javascript Client QuickStart]: app-service-html-get-started.md
[sincronización de datos sin conexión]: app-service-mobile-offline-data-sync.md
[Cómo configurar la autenticación de Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Cómo configurar la autenticación de Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Cómo configurar la autenticación de Google]: app-service-mobile-how-to-configure-google-authentication.md
[Cómo configurar Microsoft Authentication]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Cómo configurar la autenticación de Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md
[Guía de implementación de Azure de aplicación de servicio]: ../app-service-web/web-sites-deploy.md
[Supervisar el servicio de una aplicación de Azure]: ../app-service-web/web-sites-monitor.md
[Habilitar el registro de diagnóstico en Azure de aplicación de servicio]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Solucionar problemas de un servicio de aplicación de Azure en Visual Studio]: ../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md
[especificar la versión de nodo]: ../nodejs-specify-node-version-azure-apps.md
[utilizar módulos de nodo]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service-web/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/
[Swagger]: http://swagger.io/

[Portal de Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promesa]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[ejemplo de basicapp en GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[ejemplo de todo en GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[directorio de ejemplos de GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Herramientas de Node.js 1.1 para Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[paquete de Node.js MSSQL]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[Software de ExpressJS intermedio]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
