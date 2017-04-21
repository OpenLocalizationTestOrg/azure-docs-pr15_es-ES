<properties
    pageTitle="Introducción a la búsqueda de Azure en Java | Microsoft Azure | Servicio de nube hospedado de búsqueda"
    description="Cómo crear una aplicación de búsqueda de nube hospedado en Azure con Java como lenguaje de programación."
    services="search"
    documentationCenter=""
    authors="EvanBoyle"
    manager="pablocas"
    editor="v-lincan"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.date="07/14/2016"
    ms.author="evboyle"/>

# <a name="get-started-with-azure-search-in-java"></a>Introducción a la búsqueda de Azure en Java
> [AZURE.SELECTOR]
- [Portal](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Obtenga información sobre cómo crear una aplicación de búsqueda personalizada de Java que utiliza la búsqueda de Azure para su experiencia de búsqueda. En este tutorial, se utiliza la [API de REST de servicio de búsqueda de Azure](https://msdn.microsoft.com/library/dn798935.aspx) para construir los objetos y las operaciones que se utiliza en este ejercicio.

Para ejecutar este ejemplo, debe tener un servicio de búsqueda de Azure, puede firmar para en el [Portal de Azure](https://portal.azure.com). Para obtener instrucciones detalladas, vea [crear un servicio de búsqueda de Azure en el portal](search-create-service-portal.md) .

Hemos usado el software siguiente para crear y probar este ejemplo:

- [Eclipse IDE para desarrolladores de Java EE](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar). Asegúrese de descargar la versión EE. Uno de los pasos de comprobación requiere una característica que se encuentra sólo en esta edición.

- [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

## <a name="about-the-data"></a>Acerca de los datos

Esta aplicación de ejemplo utiliza datos de los [Estados Unidos geológicas servicios (USG)](http://geonames.usgs.gov/domestic/download_data.htm), filtrada en el estado de Rhode isla para reducir el tamaño del conjunto de datos. Usaremos estos datos para crear una aplicación de búsqueda que devuelve edificios puntos como hospitales y escuelas, así como características geológicas como secuencias, lagos y cumbres.

En esta aplicación, el programa de **SearchServlet.java** crea y carga el índice mediante una construcción [indizador](https://msdn.microsoft.com/library/azure/dn798918.aspx) , recuperar el conjunto de datos de USG filtrado desde una base de datos de SQL Azure público. Información de conexión al origen de datos en línea y credenciales predefinidas se proporcionan en el código del programa. En términos de acceso a datos, no es necesaria realizar ninguna configuración adicional.

> [AZURE.NOTE] Se aplica un filtro en este conjunto de datos para permanecer en el límite de 10.000 documentos del nivel de precio gratuito. Si utiliza el nivel estándar, no aplicar este límite y se puede modificar este código para usar un conjunto de datos más grande. Para obtener más información acerca de la capacidad para cada nivel de precios, consulte [las restricciones y límites](search-limits-quotas-capacity.md).

## <a name="about-the-program-files"></a>Acerca de los archivos de programa

La siguiente lista describe los archivos que son relevantes para este ejemplo.

- Search.jsp: Proporciona la interfaz de usuario
- SearchServlet.java: Proporciona métodos (similares a un controlador de MVC)
- SearchServiceClient.java: Las solicitudes de controladores HTTP
- SearchServiceHelper.java: Una clase auxiliar que proporciona métodos estáticos
- Document.Java: Proporciona el modelo de datos
- config.Properties: establece la dirección URL del servicio de búsqueda y la clave de api
- Pom.XML: Una dependencia de experto

<a id="sub-2"></a>
## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Buscar el nombre de servicio y la clave de api del servicio de búsqueda de Azure

Todas las llamadas de API de REST en Azure búsqueda requieren que le proporcione la dirección URL del servicio y una clave de api. 

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la barra de salto, haga clic en el **servicio de búsqueda** para obtener una lista de los servicios de búsqueda de Azure aprovisionados para la suscripción.
3. Seleccione el servicio que desea usar.
4. En el panel de servicio, verá los mosaicos para obtener información esencial, así como el icono de llave para tener acceso a las claves de administración.

    ![][3]

5. Copie la dirección URL del servicio y una tecla de administrador. Se necesita más adelante, cuando agregue el archivo **config.properties** .

## <a name="download-the-sample-files"></a>Descargar los archivos de ejemplo

1. Vaya a [AzureSearchJavaDemo](https://github.com/AzureSearch/AzureSearchJavaIndexerDemo) en Github.

2. Haga clic en **Descargar ZIP**, guarde el archivo .zip en el disco y, a continuación, extraer todos los archivos que contiene. Considere la posibilidad de extraer los archivos en el área de trabajo de Java para que sea más fácil encontrar el proyecto más adelante.

3. Los archivos de ejemplo son de solo lectura. Haga clic en Propiedades de carpeta y desactive el atributo de sólo lectura.

Todas las modificaciones de archivo posterior y ejecución de instrucciones se realizará con archivos en esta carpeta.  

## <a name="import-project"></a>Importar proyecto

1. En Eclipse, elija **archivo** > **Importar** > **General** > **Proyectos existentes en el área de trabajo**.

    ![][4]

2. En **Seleccionar directorio de raíz**, vaya a la carpeta que contiene los archivos de ejemplo. Seleccione la carpeta que contiene la carpeta .project. El proyecto debería aparecer en la lista de **proyectos** como un elemento seleccionado.

    ![][12]

3. Haga clic en **Finalizar**.

4. Use el **Explorador de proyectos** para ver y editar los archivos. Si no está ya abierto, haga clic en **ventana** > **Mostrar vista** > **Explorador de proyectos** o utilice el método abreviado para abrirlo.

## <a name="configure-the-service-url-and-api-key"></a>Configurar la dirección URL del servicio y la clave de la api

1. En el **Explorador de proyectos**, haga doble clic en **config.properties** para editar las opciones de configuración que contiene el nombre del servidor y la clave de la api.

2. Consulte los pasos anteriores de este artículo, donde se encuentran la dirección URL del servicio y la clave de la api en el [Portal de Azure](https://portal.azure.com), para obtener los valores que se introduce ahora en **config.properties**.

3. En **config.properties**, reemplace "Clave Api" con la api de clave para el servicio. A continuación, el nombre de servicio (el primer componente de la dirección URL de http://servicename.search.windows.net) reemplaza "nombre de servicio" en el mismo archivo.

    ![][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>Configurar los entornos de proyecto, generación y tiempo de ejecución

1. En Eclipse, en el Explorador de proyectos, haga clic en proyecto > **Propiedades** > **Aspectos del proyecto**.

2. Seleccione el **módulo de Web dinámicos**, **Java**y **JavaScript**.

    ![][6]

3. Haga clic en **Aplicar**.

4. Seleccione la **ventana** > **Preferencias** > **Server** > **entornos de tiempo de ejecución** > **Agregar..**.

5. Expanda Apache y seleccione la versión del servidor Apache Tomcat que ya instalado. En nuestro sistema, se instala versión 8.

    ![][7]

6. En la página siguiente, especifique el directorio de instalación de Tomcat. En un equipo Windows, probablemente será C:\Program Files\Apache Software Foundation\Tomcat *versión*.

6. Haga clic en **Finalizar**.

7. Seleccione la **ventana** > **Preferencias** > **Java** > **instaladas versiones de JRE** > **Agregar**.

8. En **Agregar JRE**, seleccione **VM estándar**.

10. Haga clic en **siguiente**.

11. En definición de JRE, JRE inicio, haga clic en el **directorio**.

12. Vaya a **Archivos de programa** > **Java** y seleccione el JDK previamente instalado. Es importante seleccionar el JDK como JRE.

13. En versiones de JRE instalado, elija el **JDK**. La configuración debe ser similar a la siguiente captura de pantalla.

    ![][9]

14. Opcionalmente, seleccione **ventana** > **Explorador Web** > **Internet Explorer** para abrir la aplicación en una ventana del explorador externo. Utilizar un explorador externo le ofrece una mejor experiencia de la aplicación Web.

    ![][8]

Se han completado las tareas de configuración. A continuación, deberá crear y ejecutar el proyecto.

## <a name="build-the-project"></a>Generar el proyecto

1. En el Explorador de proyectos, haga clic en el nombre del proyecto y elija **Ejecutar como** > **Maven crear...** para configurar el proyecto.

    ![][10]

8. En Editar configuración de objetivos, escriba "instalación limpia" y, a continuación, haga clic en **Ejecutar**.

Mensajes de estado son resultados en la ventana de consola. Debería ver generar éxito que indica que el proyecto que se generan sin errores.

## <a name="run-the-app"></a>Ejecute la aplicación

En este último paso, se ejecutará la aplicación en un entorno de tiempo de ejecución de servidor local.

Si aún no ha especificado un entorno de tiempo de ejecución de servidor en Eclipse, debe primero.

1. En el Explorador de proyectos, expanda **ContenidoWeb**.

5. Haga clic con el botón **Search.jsp** > **Ejecutar como** > **ejecutar en el servidor**. Seleccione el servidor Apache Tomcat y, a continuación, haga clic en **Ejecutar**.

> [AZURE.TIP] Si utiliza un área de trabajo no predeterminada para almacenar su proyecto, debe modificar **La configuración de ejecución** para que apunten a la ubicación del proyecto para evitar un error de inicio del servidor. En el Explorador de proyectos, haga clic con el botón **Search.jsp** > **Ejecutar como** > **Configuraciones de ejecución**. Seleccione el servidor Apache Tomcat. Haga clic en **argumentos**. Haga clic en **área de trabajo** o el **Sistema de archivos** para establecer la carpeta que contiene el proyecto.

Cuando se ejecuta la aplicación, debería ver una ventana del explorador, lo que proporciona un cuadro de búsqueda para escribir términos.

Espere aproximadamente un minuto antes de hacer clic en **Buscar** para dar a la hora de servicio para crear y cargar el índice. Si recibe un error 404 de HTTP, ¿necesita esperar un poco más tiempo para volver a intentarlo.

## <a name="search-on-usgs-data"></a>Buscar en los datos de USG

El conjunto de datos USG incluye registros relevantes para el estado de la isla Rhode. Si hace clic en **búsqueda** en un cuadro de búsqueda vacío, obtendrá las entradas de 50 superiores, que es el valor predeterminado.

Escribir un término de búsqueda dará el motor de búsqueda que vaya en algo. Pruebe a escribir un nombre regional. "Roger Williams" fue el primer administrador de Rhode isla. Numerosos parques, edificios y escuelas se denominan después de él.

![][11]

También puede intentar cualquiera de estos términos:

- Pawtucket
- Pembroke
- OCA + Chalet

## <a name="next-steps"></a>Pasos siguientes

Este es el primer tutorial de Azure búsqueda basado en Java y el conjunto de datos de USG. Con el tiempo, más adelante ampliaremos este tutorial para demostrar que podría querer usar en soluciones personalizadas de características de búsqueda adicionales.

Si ya tiene algunas fondo en búsqueda de Azure, puede utilizar este ejemplo como un springboard más experimentación, tal vez aumentar la [página de búsqueda](search-pagination-page-layout.md)o la implementación de [navegación facetas](search-faceted-navigation.md). También puede mejorar la página de resultados de búsqueda agregando recuentos y procesamiento por lotes de documentos para que los usuarios pueden pasar los resultados de la página.

¿Nueva búsqueda Azure? Se recomienda probar otros tutoriales para desarrollar una comprensión de lo que puede crear. Visite nuestra [página de documentación](https://azure.microsoft.com/documentation/services/search/) para buscar más recursos. También puede ver los vínculos en nuestra [lista Tutorial y vídeo](search-video-demo-tutorial-list.md) para acceder a más información.

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png
