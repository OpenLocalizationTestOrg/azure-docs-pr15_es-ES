<properties
    pageTitle="Introducción a la búsqueda de Azure en NodeJS | Microsoft Azure | Servicio de nube hospedado de búsqueda"
    description="Repase la creación de una aplicación de búsqueda en un servicio de búsqueda de nube hospedado en Azure utilizando NodeJS como el lenguaje de programación."
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

# <a name="get-started-with-azure-search-in-nodejs"></a>Introducción a la búsqueda de Azure en NodeJS
> [AZURE.SELECTOR]
- [Portal](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Obtenga información sobre cómo crear una aplicación de búsqueda personalizada de NodeJS que utiliza la búsqueda de Azure para su experiencia de búsqueda. En este tutorial, se utiliza la [API de REST de servicio de búsqueda de Azure](https://msdn.microsoft.com/library/dn798935.aspx) para construir los objetos y las operaciones que se utiliza en este ejercicio.

Hemos usado [NodeJS](https://nodejs.org) y NPM, [3 fundamentales de texto](http://www.sublimetext.com/3)y Windows PowerShell en Windows 8.1 para desarrollar y probar este código.

Para ejecutar este ejemplo, debe tener un servicio de búsqueda de Azure, puede firmar para en el [Portal de Azure](https://portal.azure.com). Para obtener instrucciones detalladas, vea [crear un servicio de búsqueda de Azure en el portal](search-create-service-portal.md) .

## <a name="about-the-data"></a>Acerca de los datos

Esta aplicación de ejemplo utiliza datos de los [Estados Unidos geológicas servicios (USG)](http://geonames.usgs.gov/domestic/download_data.htm), filtrada en el estado de Rhode isla para reducir el tamaño del conjunto de datos. Usaremos estos datos para crear una aplicación de búsqueda que devuelve edificios puntos como hospitales y escuelas, así como características geológicas como secuencias, lagos y cumbres.

En esta aplicación, el programa de **DataIndexer** crea y carga el índice mediante una construcción [indizador](https://msdn.microsoft.com/library/azure/dn798918.aspx) , recuperar el conjunto de datos de USG filtrado desde una base de datos de SQL Azure público. Conexión y credenciales en el código del programa se proporciona información al origen de datos en línea. No es necesaria realizar ninguna configuración adicional.

> [AZURE.NOTE] Se aplica un filtro en este conjunto de datos para permanecer en el límite de 10.000 documentos del nivel de precio gratuito. Si utiliza el nivel estándar, este límite no se aplica. Para obtener más información acerca de la capacidad para cada nivel de precios, consulte [límites de servicio de búsqueda](search-limits-quotas-capacity.md).


<a id="sub-2"></a>
## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Buscar el nombre de servicio y la clave de api del servicio de búsqueda de Azure

Después de crear el servicio, vuelva al portal para obtener la dirección URL o `api-key`. Las conexiones con el servicio de búsqueda requieren que tiene la dirección URL y un `api-key` para autenticar la llamada.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En la barra de salto, haga clic en el **servicio de búsqueda** para obtener una lista de los servicios de búsqueda de Azure aprovisionados para la suscripción.
3. Seleccione el servicio que desea usar.
4. En el panel de servicio, verá los mosaicos para obtener información esencial, así como el icono de llave para tener acceso a las claves de administración.

    ![][3]

5. Copie la dirección URL del servicio, una clave de administración y una clave de consulta. Necesitará los tres más adelante, cuando agregue el archivo config.js.

## <a name="download-the-sample-files"></a>Descargar los archivos de ejemplo

Use uno de los siguientes métodos para descargar los datos de ejemplo.

1. Vaya a [AzureSearchNodeJSIndexerDemo](https://github.com/AzureSearch/AzureSearchNodeJSIndexerDemo).
2. Haga clic en **Descargar ZIP**, guarde el archivo .zip y, a continuación, extraer todos los archivos que contiene.

Todas las modificaciones de archivo posterior y ejecución de instrucciones se realizará con archivos en esta carpeta.


## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>Actualizar la config.js. con la dirección URL del servicio de búsqueda y la clave de api

Usando la dirección URL y la clave de api que copió anteriormente, especifique la dirección URL, clave de administración y la clave de la consulta en el archivo de configuración.

Teclas de administración concesión el control total sobre las operaciones de servicio, como crear o eliminar un índice y cargar documentos. Por el contrario, las claves de consulta son para operaciones de sólo lectura, que normalmente se usan las aplicaciones cliente que se conectan a la búsqueda de Azure.

En este ejemplo, se incluye la clave de consulta para ayudar a Refuerce el método recomendado de uso de la clave de consulta en aplicaciones cliente.

La siguiente captura de pantalla muestra **config.js** abierto en un texto editor, con las entradas relevantes delimitadas para que puedan ver dónde desea actualizar el archivo con los valores que son válidos para el servicio de búsqueda.

![][5]


## <a name="host-a-runtime-environment-for-the-sample"></a>Host de un entorno de tiempo de ejecución de la muestra

El ejemplo requiere un servidor HTTP, que puede instalar mediante globalmente npm.

Utilizar una ventana de PowerShell para los siguientes comandos.

1. Vaya a la carpeta que contiene el archivo **package.json** .
2. Tipo de `npm install`.
2. Tipo de `npm install -g http-server`.

## <a name="build-the-index-and-run-the-application"></a>Crear el índice y ejecute la aplicación

1. Tipo de `npm run indexDocuments`.
2. Tipo de `npm run build`.
3. Tipo de `npm run start_server`.
4. Dirigir el explorador en`http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>Buscar en los datos de USG

El conjunto de datos USG incluye registros relevantes para el estado de la isla Rhode. Si hace clic en **búsqueda** en un cuadro de búsqueda vacío, obtendrá las entradas de 50 superiores, que es el valor predeterminado.

Escribir un término de búsqueda dará el motor de búsqueda que vaya en algo. Pruebe a escribir un nombre regional. "Roger Williams" fue el primer administrador de Rhode isla. Numerosos parques, edificios y escuelas se denominan después de él.

![][9]

También puede intentar cualquiera de estos términos:

- Pawtucket
- Pembroke
- OCA + Chalet


## <a name="next-steps"></a>Pasos siguientes

Este es el primer tutorial de búsqueda de Azure basándose en NodeJS y el conjunto de datos de USG. Con el tiempo, más adelante ampliaremos este tutorial para demostrar que podría querer usar en soluciones personalizadas de características de búsqueda adicionales.

Si ya tiene algunas fondo en búsqueda de Azure, puede utilizar este ejemplo como un springboard para intentar suggesters (consultas con escritura anticipada o Autocompletar), filtros y navegación facetas. También puede mejorar la página de resultados de búsqueda agregando recuentos y procesamiento por lotes de documentos para que los usuarios pueden pasar los resultados de la página.

¿Nueva búsqueda Azure? Se recomienda probar otros tutoriales para desarrollar una comprensión de lo que puede crear. Visite nuestra [página de documentación](https://azure.microsoft.com/documentation/services/search/) para buscar más recursos. También puede ver los vínculos en nuestra [lista Tutorial y vídeo](search-video-demo-tutorial-list.md) para acceder a más información.

<!--Image references-->
[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png
