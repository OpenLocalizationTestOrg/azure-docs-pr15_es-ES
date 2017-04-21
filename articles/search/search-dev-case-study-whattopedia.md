<properties 
    pageTitle="Azure búsqueda programador caso práctico: Cómo WhatToPedia genera un portal de infomedia en Microsoft Azure | Microsoft Azure | Servicio de nube hospedado de búsqueda" 
    description="Obtenga información sobre cómo crear un información portal y meta motor de búsqueda con búsqueda de Azure, un servicio de búsqueda de nube hospedado para desarrolladores." 
    services="search, sql-database,  storage, web-sites" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="jhubbard"/>

<tags 
    ms.service="search" 
    ms.devlang="NA" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="search" 
    ms.date="08/29/2016" 
    ms.author="heidist"/>

# <a name="azure-search-developer-case-study"></a>Caso práctico de búsqueda Azure programador

## <a name="how-whattopediacomhttpwhattopediacom-built-an-infomedia-portal-on-microsoft-azure"></a>Cómo [WhatToPedia.com](http://whattopedia.com/) crea un portal de infomedia en Microsoft Azure

 ![][6]  &nbsp;&nbsp;&nbsp;  <font size="9">La idea grande</font> 


Nuestra idea es crear un portal de información que ayuda a los compradores a conectar con los distribuidores mediante una experiencia muy relevantes, el ámbito de búsqueda, similar a cómo viajar turistas de coincidencia de portales hacia arriba con hoteles, restaurantes y entretenimiento en inexploradas. 

El portal que visualizamos proporcionará una experiencia de búsqueda muy alta calidad sobre los datos de un distribuidor en un mercado determinado, ayuda a los compradores a encontrar stores según ubicación y otros elementos el distribuidor proporciona. Se va a iniciar el motor de búsqueda con un conjunto de datos inicial, pero se generará mayor valor con el tiempo, con la Ayuda de los suscriptores de un distribuidor que se debe publicar información sobre su negocio. Promociones, nuevos productos, marcas populares, especialistas internos-– todos son ejemplos de datos que agrega valor a nuestro sitio. Estos datos se informaron automática e integrados en el cuerpo de la búsqueda, una vez que el distribuidor inscribe como suscriptor. Publicidad, el modelo de suscripción, además de proporciona el flujo de ingresos nuestros nuevos negocios.

Búsqueda será el modelo de interacción de usuario principal, en una plataforma de nube puros. Con fines de escala y costos mínimos, casi todo lo que hacer de la experiencia del portal para control de código fuente será a través de un servicio en línea. Con un motor de búsqueda que proporciona las características que necesitamos fuera del cuadro, podemos crear una aplicación de búsqueda rápidamente, sin tener que crear y administrar un motor de búsqueda nosotros.

## <a name="what-we-built"></a>¿Qué creamos

WhatToPedia es una empresa de infomedia de inicio. Creamos [WhatToPedia.com](http://whattopedia.com/) – - actualmente en el mercado de prueba en Europa del Norte con una fecha de poner en marcha de 2 de febrero de 2015. Nuestra base de clientes es tradicionales de bloque de principalmente departamentos que necesitan una presencia en línea asequible que sea fácil de administrar y mantener.

Nuestra tarea es atraer compradores mediante una experiencia de búsqueda en línea excelente, aumentar resultados basados en ciudad o entorno, marcas, almacenar los nombres o almacenar tipos. Atraer compradores tiene un efecto encadenado motivación distribuidores suscribirse a nuestro sitio de portal. Las suscripciones están basadas en pagos, con una tasa asequible.

 ![][7] 

Después de registrarse para una suscripción, un distribuidor tiene sobre su perfil existente (creado inicialmente nos de datos comprados), actualizar con datos adicionales sobre promociones, marcas destacadas o anuncios. Capacidades internas, como los idiomas hablados, monedas aceptado, compras exentas de impuestos, puede ser automática notificado mejor atraer compradores que buscan los servicios.

## <a name="who-we-are"></a>Quiénes somos

Mi nombre es Thomas Segato (Microsoft Consulting) y trabajado con Jesper Boelling, desarrollador jefe en WhatToPedia para diseñar la solución. 

WhatToPedia es un inicio, prueba de marketing de su empresa nuevo portal en Suecia, donde la mayoría de los 60.000 distribuidores son PYME bloque tradicionales (pequeñas y medianas empresas). Porque sabemos que una persona de la compra de Europa habla varios idiomas y lleva a cabo varias monedas, creamos soluciones que adaptan un comprador multilingüe. Que es necesario y encontrar un motor de búsqueda que admite nuestras necesidades multilingües en búsqueda de Azure.

Búsqueda de Azure era un cambiador de juego para que el proyecto. Antes de la disponibilidad de búsqueda de Azure, hemos dedicado mucho energía trabajar a través de los inconvenientes de la creación de nuestro propio motor de búsqueda. Que la búsqueda de Azure como un servicio en línea, quita la mayor obstáculos técnicos y administrativos de nuestra solución, que pretende Introducción al mercado con mayor rapidez y una experiencia de búsqueda más eficaz.  

## <a name="how-we-did-it"></a>Cómo se hizo

Nuestra visión era crear una completa infraestructura basada en servicios de nube solo. Microsoft se ha elegido como la plataforma estratégica porque es el proveedor que ofrece el necesario servicios (para colaboración y desarrollo), escalar a petición y precios asequible.
 
### <a name="high-level-components"></a>Componentes de alto nivel

Creamos una empresa, no solo un sitio. Compatibilidad con el esfuerzo necesaria una gama completa de herramientas y aplicaciones. Hemos adoptado Visual Studio y Visual Studio Team Services para la administración de scrum, Office 365 para la comunicación y colaboración y, por supuesto Microsoft Azure para todas las operaciones relacionadas con el sitio y el almacenamiento y de desarrollo, servicio de Team Foundation (TFS) Online para control de código fuente. Con Visual Studio, el IDE proporciona directas de aprovisionamiento en Azure, con integración de TFS Online proporcionar un aumento de productividad adicionales.

El diagrama siguiente muestra los componentes de alto nivel utilizados en la infraestructura de WhatToPedia.

   ![][8]

### <a name="how-we-use-microsoft-azure"></a>¿Cómo utilizamos Microsoft Azure

Buscando los cuadros verdes en el diagrama anterior, verá que la solución de WhatToPedia se basa en los servicios siguientes:

- [Búsqueda de Azure](https://azure.microsoft.com/services/search/)
- [Sitios Web Azure con MVC 4](https://azure.microsoft.com/services/websites/)
- [Azure WebJobs para las tareas programadas](../app-service-web/websites-webjobs-resources.md)
- [Base de datos SQL Azure](https://azure.microsoft.com/services/sql-database/)
- [Almacenamiento de blobs de Windows Azure](https://azure.microsoft.com/services/storage/)
- [Entrega de correo electrónico SendGrid](https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/)

La esencia de la solución es datos y búsqueda. A continuación se muestra el flujo de datos del proveedor de distribuidor para el cliente final:

  ![][9]

Almacenamiento de datos principal es el distribuidor y los datos de contabilidad de base de datos de SQL Azure. Consiste en el conjunto de datos inicial, además de datos específicos de un distribuidor agregados a lo largo del tiempo. Usaremos una WebJob de Azure para publicar actualizaciones de base de datos de SQL en el cuerpo de la búsqueda en la búsqueda de Azure.

### <a name="presentation-layer"></a>Capa de presentación

El portal es un sitio Web de Azure, implementado en MVC 4 y [Twitter inicio](http://en.wikipedia.org/wiki/Bootstrap_%28front-end_framework%29). Elegimos MVC porque ofrece un enfoque mucho más limpio a HTML de desarrollo de ASP.NET basada en formularios. Para evitar tener que crear aplicaciones para varios dispositivos y mantener varias plataformas móviles, inicio de Twitter eligió a todas las plataformas y dispositivos de soporte técnico.

### <a name="authentication-permissions-and-sensitive-data"></a>Autenticación, permisos y datos confidenciales

Compradores exploran el sitio de forma anónima. Por lo tanto, no existen requisitos para compradores inicio de sesión ni hacen se almacena los datos de consumidores. 

Los distribuidores son algo diferente. A continuación, se almacenan información del perfil público, información de facturación y contenido multimedia que desea exponer en el sitio. Cada distribuidor suscritos al sitio obtener un inicio de sesión de usuario, utilizado para autenticar al usuario antes de realizar actualizaciones en el perfil del suscriptor.  Nos almacenar de forma segura todos los datos de suscriptor en el almacenamiento de blobs de Windows Azure y de base de datos de SQL Azure.
Hemos optado por un modelo de autenticación basado en .NET la autenticación basada en formularios. Elegimos este enfoque para su simplificar; no necesitamos los roles, soporte técnico de la interfaz de usuario y otras características extraños que vienen con otros enfoques. 

Para asegurarse de que los distribuidores ven solo los datos que pertenecen a ellas, que se creó un distribuidor ID para cada distribuidor que se utiliza posteriormente en todas las operaciones lectura y escritura que implican datos específicos del distribuidor. Con este método, hemos encontrado que no necesitamos conceder permisos de base de datos a los distribuidores individuales. Todos los distribuidores interactúan con el sistema en una función de base de datos única, con el ID de un distribuidor que nuestra técnica de aislamiento de datos.

Dado que nuestra empresa es todo sobre los efectos descendentes (impulsar negocios más a los distribuidores, crear incentivos para anunciar y suscribirse), podemos dibujar la línea en la gestión de compras en la web. Por lo tanto, no encontrará el carro de la compra en nuestro sitio, lo que simplifica los requisitos de seguridad. 

Simplificar otro que se emplea era subcontratación nuestras operaciones de pagos de cuentas y facturación. Información de pago de cliente enrutamiento directamente a un tercero ([SveaWebPay](http://www.sveawebpay.se/)), se reducen los riesgos asociar a almacenar y proteger datos confidenciales en nuestra almacena datos. 

### <a name="search-engine"></a>Motor de búsqueda

El centro de nuestra solución es el motor de búsqueda integrado en el servicio de búsqueda de Azure. Inicialmente, creamos un motor de búsqueda personalizada, pero durante este proceso, hemos realizado la complejidad fue muy alta hecho esfuerzo y que se le pida que considere otras alternativas. 

Características básicas que estaban más importantes que nos incluyen:

- Filtros
- Navegación facetas
- Incremento de resultados
- Paginar a través de AJAX

Una búsqueda de internet dio lugar a nosotros para el siguiente vídeo, que inspiraron nos probar búsqueda de Azure: [Análisis detallado en TechEd Europe](http://channel9.msdn.com/events/TechEd/Europe/2014/DBI-B410) 

Después de ver el vídeo, hemos listos para crear un prototipo en función de lo que observamos. Debido a ya tiene un modelo de datos en MVC, crear el prototipo era sencillo porque los datos contenidos términos de búsqueda y ya había trabajado los requisitos para cómo deseamos ordenar, faceta y filtrar los datos. 

Se trata de cómo se creó el prototipo.

**Configurar el servicio de búsqueda de Azure**

1. Inicie sesión en el Portal clásico de Azure y agrega el servicio de búsqueda a la suscripción. Hemos usado la versión compartida (gratis con la suscripción).
2. Crear un índice. Para el prototipo, hemos usado el portal de interfaz de usuario para definir los campos de búsqueda y crear los perfiles de puntuación. El perfil de puntuación basado en los datos de ubicación: país | Ciudad | dirección (consulte: agregar perfiles de puntuación).
3. Copie la dirección URL del servicio y la administración clave api nuestros archivos de configuración. Esta clave se encuentra en la página de servicio de búsqueda en el portal y se usa para autenticar al servicio.
    
**Desarrollar un trabajo de indizador de búsqueda: consola de Windows**

1. Leer todos los distribuidores de base de datos.
2. Llamar a la API del servicio de búsqueda de Azure para cargar los distribuidores uno por uno (consulte: http://msdn.microsoft.com/library/azure/dn798930.aspx).
3. Establecer una propiedad de base de datos que se indiza de distribuidor para la indización incremental. Esto se hizo al agregar un campo 'indizador' que almacena el estado del índice de cada perfil (indexado o no). 

Consulte el apéndice del fragmento de código que genera el trabajo del indizador.

**Desarrollar un Portal Web de búsqueda: MVC**

1. Llame al servicio de búsqueda de Azure para obtener todos los documentos de la búsqueda (vea: http://msdn.microsoft.com/library/azure/dn798927.aspx)
2. Extraer siguiendo desde la respuesta del servicio de búsqueda (a través de json.net http://james.newtonking.com/json)
   - Resultados
   - Facetas
   - Recuento de resultados
   - Desarrollar una interfaz de usuario para mostrar los resultados de búsqueda, facetas y recuentos (ya tenemos esto).

Este es el código que hemos usado para obtener los resultados de búsqueda de Azure:

    string requestUrl = 
    string.Format("https://{0}.search.windows.net/indexes/profiles/docs?searchMode=all&$count=true&search={1}&facet=city,count:20&facet=category&$top=10&$skip={2}&api-version=2014-07-31-Preview{3}", Config.SearchServiceName, EscapeODataString(q), skip, filter);
      var response = client.GetAsync(requestUrl).Result; //  + Uri.EscapeDataString("hennes")
      response.EnsureSuccessStatusCode();
     dynamic json = JsonConvert.DeserializeObject(response.Content.ReadAsStringAsync().Result);

**Aumentar la ubicación**

Probablemente el requisito más importante para comprobar en el prototipo incluido agregar una palabra clave de búsqueda de ubicación a la consulta. Es fundamental nuestro portal que si un usuario escribe un nombre de la ciudad en la búsqueda de la consulta, que los distribuidores en la ciudad determinada podrían estar por encima de distribuidores con la palabra clave de la ciudad en la descripción. Para ello, hemos usado un perfil de puntuación clasificar el campo Ciudad superior a otros campos.

**Compatibilidad con varios idiomas**

Necesitamos mostrar resultados correctos en idiomas correctos y a continuación, proporcionar una opción para obtener los mismos resultados en diferentes idiomas. Fueron los dos lados para este problema: 

- Buscar palabras en varios idiomas
- Mostrar resultados de búsqueda en el idioma correcto.

El elemento de la presentación se soluciona mediante la adición de un documento para cada idioma con texto localizado y una propiedad con el idioma. Cuando un usuario escribe un término de búsqueda, se usuario `$filter` expresiones para filtrar en el idioma que el usuario ha elegido.

Cada uno de los documentos tiene una propiedad oculta denominada "ciudades" integradas en el tipo de la colección. Esta propiedad almacena nombres de ciudad en todos los idiomas, lo que permite al usuario buscar en varios idiomas.

###<a name="data-storage"></a>Almacenamiento de datos

Todos los datos (perfil, suscripción y contabilidad) se almacena en la base de datos de SQL. Todos los archivos multimedia se almacenan en el almacenamiento de blobs de Windows Azure, incluidas las imágenes y vídeos proporcionadas por el distribuidor. Usando el almacenamiento de blobs independiente aísla los efectos de carga de archivos; archivos nunca se mezclados con el sitio Web, por lo que no es necesario volver a crear el sitio cuando se agrega archivos.

Una ventaja importante de nuestro diseño de almacenamiento es que varios desarrolladores pueden compartir un almacenamiento de desarrollo único. Uno de los requisitos para el proyecto WhatToPedia era poder crear un entorno de desarrollo dentro de 15 minutos, como vídeos, imágenes y datos de un distribuidor. Obtener los datos más recientes de TFS en línea, ejecutando una secuencia de comandos SQL, y el trabajo de importación, un entorno completo puede ser superado hacia arriba en ningún tiempo en absoluto. Esta práctica también mejora el proceso de ensayo.

###<a name="webjobs"></a>WebJobs

Usamos Azure WebJobs para actualizar los datos en el índice. Mediante la creación de una tarea del indizador de búsqueda, el elemento de indización era muy fácil de integrar en nuestra solución. El cambio de código solo hemos realizados fue acomodar el trabajo indizador era agregar una `Indexed` campo a nuestro modelo de datos para indicar el estado de índice. Cuando se agrega un nuevo perfil o se actualiza, el `Indexed` campo está establecido en falso. Lo mismo se aplica si el distribuidor cambia sus datos de perfil a través del portal.  

El trabajo busca todas las filas que tengan `Indexed` establecida en falso. Cuando encuentre la fila, el documento se registra en búsqueda de Azure y, a continuación, el `Indexed` campo se establece en true. No hay que planear para agregar y actualizar los datos porque el servicio de búsqueda de Azure realmente se encarga de ello. Si agrega un documento que ya está presente, el servicio va a hacer una actualización automáticamente.

Todos los trabajos web se ha desarrollado como aplicaciones de consola que pueden cargadas en Azure sitios web como archivos ZIP, descomprimir y, a continuación, se ha programado.

La tarea está programada para que se ejecute cada 5 minutos como una tarea programada de web. Se calcula el servicio tarda aproximadamente tres minutos para cargar 3.000 documentos, que estaba dentro de los requisitos. 

> [AZURE.NOTE] Hay una característica de indizador de prototipo que se introdujo recientemente en búsqueda de Azure. Esta característica ha suministrado demasiado tarde para que poder usarla en nuestra primera versión, pero aparece para solucionar el mismo problema, que hemos usado nuestro trabajo indizador, que es automatizar las operaciones de carga de datos.


###<a name="backup-strategy"></a>Estrategia de copia de seguridad

Hemos diseñado una estrategia de copia de seguridad de varios niveles para recuperar de un rango de casos de error grave, hacia abajo hasta la recuperación de transacciones individuales. Los activos proteger incluyen tres tipos de datos (sitio web, datos de suscriptores y archivos multimedia). 

En primer lugar, al mantener el código de origen del sitio web en TFS en línea, sabemos que si el sitio se desconecta, nos podemos generarlo publicándolo de TFS. 

Datos de suscriptor en la base de datos de SQL Azure están el activo más confidencial. Se vuelve este proceso usando los integrados función (consulte la [copia de seguridad de SQL Azure y restaurar](http://msdn.microsoft.com/library/azure/jj650016.aspx)). La programación de copia de seguridad es la copia de seguridad de base de datos completa una vez por semana, copias de seguridad de base de datos diferencial una vez al día y copias de seguridad del registro de transacciones cada 5 minutos.  Dado que el tamaño de los datos, esta solución es más adecuada para nuestros volúmenes de datos inmediato y previsto.

Tercero, nos almacenar archivos de imagen y vídeo en el almacenamiento de blobs de Windows Azure. Todavía estamos evaluando el plan de copia de seguridad definitivo para estos datos, considere Cloudberry Explorer para Azure como una posible solución. Por ahora, usamos una WebJob para copiar imágenes y vídeos a otra ubicación.

##<a name="what-we-learned"></a>¿Qué se ha aprendido

Debido a que ha tenido datos, era fácil establecer prueba de concepto. En horas, tenemos un prototipo con facetas y perfiles de rango contadores, paginación, y los resultados de búsqueda. Los resultados de búsqueda fueron tan precisos, que hemos decidido quitar algunos de los filtros que se presentan al cliente final. 

Las mejores sorpresas nos fue la velocidad podríamos aprendemos de búsqueda de Azure, y cuánto se devolvió. Literalmente, prueba de concepto se estableció en pocas horas (vea la nota siguiente), reemplazando 500 líneas de código con 3 líneas de código en la aplicación front-end (más de un nuevo WebJob) y cómo mejorar los resultados. 

Anteriormente, nuestro código implementar paginación, recuentos y otros comportamientos que son estándar para buscar. Con la búsqueda de Azure, los resultados, obtenemos incluirán coincidencias encontradas, aspectos, paginar datos, recuentos: todo lo que necesita y tuvieran proporcionar nosotros. También se incluyen exploración facetas aumentar e integrado, que no tenemos en nuestra solución original.

El mayor desafío durante la implementación fue que era una versión de vista previa y encontrar información y experiencias compartidos era difícil. Una vez se conecta algunos puntos, hemos encontrado que usa el servicio de búsqueda de Azure era bastante sencilla debido a su formato de datos API de REST y JSON. ¿Podríamos llamamos el marco de trabajo directamente desde los complementos de origen más abiertos como JQuery JSON.Net y podríamos usar herramientas como Fiddler para experimentación rápida y depurar. 

> [AZURE.NOTE] Además de tener los datos preparados, lo contribuido que los usuarios crear el prototipo ya entienden cómo funciona la tecnología, nos lo más productiva y sabe más de las características integradas de la búsqueda. Si necesita ponerse en construcción de consulta de búsqueda, navegación facetas, filtros, etc. debe esperar prototipos a tardar más. 

###<a name="controlling-facets-in-the-search-presentation-page"></a>Controlar aspectos en la página de presentación de búsqueda

Uno de nuestros conocimientos durante la prueba de concepto era planear facetas cuidadosamente por adelantado. Después de cargar una gran cantidad de datos en la solución, observamos que el gran volumen de facetas era demasiado alto para presentar a los usuarios. 

Esto se soluciona al restringir el parámetro de recuento de faceta. El parámetro count impone un límite del número de facetas devuelve al usuario. Puede encontrar un vínculo que incluye una explicación del parámetro recuento [aquí](search-faceted-navigation.md).

###<a name="webjobs-for-scheduling-tasks"></a>WebJobs para programar las tareas

Búsqueda de Azure no solo amable sorpresa para nosotros. Hemos descubierto que uso WebJobs para automatizar nuestras operaciones de carga de datos para la búsqueda de Azure era considerablemente superior a nuestro enfoque anterior, que se incluye con una máquina virtual dedicada ejecutando programador de Windows, con las tareas programadas para actualizar el índice de búsqueda. WebJobs era más fácil de configurar y fáciles de depuración y, por supuesto mucho más económico que tener que pagar por una máquina virtual dedicada.

###<a name="azure-blob-storage-explorer-for-updating-images"></a>Explorador de almacenamiento de blobs de Windows Azure para actualizar imágenes

Hemos encontrado que con el [Explorador de almacenamiento de blobs de Windows Azure](https://azurestorageexplorer.codeplex.com/) (disponible en codeplex) a ser muy útil para administrar el vídeo e imagen actualizaciones del sitio. Nos usarlo como una herramienta para desarrolladores actualizar manualmente imágenes y vídeos que forman parte de nuestro sitio principal. Hemos encontrado que sea más flexible que implementar cambios en el portal y elimina una iteración de prueba completo cada vez que se necesita actualizar una imagen. 

##<a name="a-few-final-words"></a>Algunas palabras finales

Gracias a la gente excelente de WhatToPedia para lo que nos permite compartir su historia  

Esperamos que encontró en este caso práctico útil. Si va a usar la búsqueda de Azure, se recomienda algunos recursos para facilitar a lo largo de:

- [Foro MSDN dedicado a búsqueda de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)
- [StackOverflow también tiene una etiqueta](http://stackoverflow.com/questions/tagged/azure-search)
- [Página de documentación en Azure.com](https://azure.microsoft.com/documentation/services/search/)
- [Azure documentación de búsqueda en MSDN](http://msdn.microsoft.com/library/azure/dn798933.aspx)


##<a name="appendix-search-indexer-webjob"></a>Apéndice: Buscar indizador WebJob

El código siguiente genera el indizador mencionado en la sección sobre la creación de prototipo.

        static void Main(string[] args)
        {
            int success = 0;
            int errors = 0;

            Log.Write("Starting job","", System.Diagnostics.TraceLevel.Info);

            var serviceName = ConfigurationManager.AppSettings["SearchServiceName"];
            var serviceKey = ConfigurationManager.AppSettings["SearchServiceKey"];

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("api-key", serviceKey);

            var db = new DB(Config.ConectionString);

            var recreateIndex = false;
            Boolean.TryParse(ConfigurationManager.AppSettings["RecreateIndex"], out recreateIndex);

            if(recreateIndex)
            {
                Log.Write("Recreating index and set all to no index", "", System.Diagnostics.TraceLevel.Info);
                db.SetAllToNotIndexed();
                RecreateIndex(serviceName, client);
            }            
            
            var profiles = db.Profiles.Where(p=>!p.Indexed).ToList();

            Log.Write(string.Format("Indexing {0} profiles",profiles.Count),"", System.Diagnostics.TraceLevel.Info);

            var cities = db.Cities.ToList();
            var categories = db.Tags.Where(p=>p.ParentId==null).ToList();            

            foreach (var profile in profiles)
            {
                Log.Write(string.Format("Indexing profile {0}", profile.Name),"",profile.ProfileId,0,System.Diagnostics.TraceLevel.Verbose);

                try
                {
                    var city = cities.Where(p => p.CityId == profile.CityId);
                    var category = categories.Where(p => p.TagId == profile.CategoryId);

                    var cityse = city.Where(p => p.Lang == "se").FirstOrDefault();
                    var cityen = city.Where(p => p.Lang == "en").FirstOrDefault();
                    var categoryse = category.Where(p => p.Lang == "se").FirstOrDefault();
                    var categoryen = category.Where(p => p.Lang == "en").FirstOrDefault();

                    var citysename = cityse == null ? "" : cityse.Name;
                    var cityenname = cityen == null ? "" : cityen.Name;
                    var categorysename = categoryse == null ? "" : categoryse.Name;
                    var categoryenname = categoryen == null ? "" : categoryen.Name;

                    var tags = db.GetTagsFromProfile(profile.ProfileId);

                    var batch = new
                    {
                        value = new[] 
                    { 
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_en",
                            profileid = profile.ProfileId.ToString(),
                            city = cityenname,
                            category = categoryenname,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "en",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        },
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_se",
                            profileid = profile.ProfileId.ToString(),
                            city = citysename,
                            category = categorysename,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "se",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        }
                    },
                    };

                    var response = client.PostAsync("https://" + serviceName + ".search.windows.net/indexes/profiles/docs/index?api-version=2014-10-20-Preview", new StringContent(JsonConvert.SerializeObject(batch), Encoding.UTF8, "application/json")).Result;
                    response.EnsureSuccessStatusCode();

                    db.Entry(profile).State = System.Data.Entity.EntityState.Modified;
                    profile.Indexed = true;
                    db.SaveChanges();
                    success++;
                }
                catch(Exception ex)
                {
                    Log.Write("Error indexing profile", ex.Message, profile.ProfileId, 0, System.Diagnostics.TraceLevel.Verbose);
                    errors++;
                }
            }
            if(errors > 0)
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Error);
            }
            else
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Info);
            }
            
        }

        static void RecreateIndex( string ServiceName, HttpClient client)
        {
            var index = new
            {
                name = "profiles",
                fields = new[] 
                { 
                    new { name = "id",              type = "Edm.String",         key = true,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "profileid",       type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "cityid",          type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "city",            type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = true,  facetable = true, retrievable = true,  suggestions = true  },
                    new { name = "category",        type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = false, facetable = true, retrievable = true,  suggestions = false  },
                    new { name = "address",         type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "email",           type = "Edm.String",         key = false, searchable = true,  filterable = false, sortable = true, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "name",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true, suggestions = true },
                    new { name = "lang",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = false,  facetable = false,  retrievable = true, suggestions = false },
                    new { name = "brands",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descen",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descse",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "orgnumber",       type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "phone",           type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "zip",             type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "cities",          type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                   new { name = "categories",      type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                    new { name = "tags",            type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false }
                    
                }
            };

            var url = "https://" + ServiceName + ".search.windows.net/indexes/?api-version=2014-10-20-Preview";

            var deleteUrl = "https://" + ServiceName + ".search.windows.net/indexes/profiles?api-version=2014-10-20-Preview";

            try
            {
                var deleteResponseIndex = client.DeleteAsync(deleteUrl).Result;
                deleteResponseIndex.EnsureSuccessStatusCode();
            }
            catch (Exception ex)
            {

            }

            var responseIndex = client.PostAsync(url, new StringContent(JsonConvert.SerializeObject(index), Encoding.UTF8, "application/json")).Result;
            responseIndex.EnsureSuccessStatusCode();            
          


<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Subheading 4]: #subheading-4
[Subheading 5]: #subheading-5
[Next steps]: #next-steps

<!--Image references-->
[6]: ./media/search-dev-case-study-whattopedia/lightbulb.png
[7]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Search-Bageri.png
[8]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Stack.png
[9]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Archicture.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
 
