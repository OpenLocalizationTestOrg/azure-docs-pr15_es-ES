

#<a name="metadata-for-azure-technical-articles"></a>Metadatos de Azure artículos técnicos

Todos los artículos técnicos Azure contienen dos secciones de metadatos: una sección de propiedades y una sección de etiquetas. La sección Propiedades permite algunas automatización de sitio Web y elementos SEO, mientras que la sección etiquetas permite una gran cantidad de informes de contenido internos. Ambas secciones son necesarias.

- [Sintaxis]
- [Uso]
- [Atributos y valores de la sección Propiedades]
- [Atributos y valores de la sección de etiquetas]

##<a name="syntax"></a>Sintaxis

La sección Propiedades usa la siguiente sintaxis:

    <properties
       pageTitle="Page title that displays in search results and the browser tab | Microsoft Azure"
       description="Article description that will be displayed on landing pages and in most search results"
       services="service-name"
       documentationCenter="dev-center-name"
       authors="GitHub-alias-of-only-one-author"
       manager="manager-alias"
       editor=""
       tags="optional"
       keywords="For use by SEO champs only. Separate terms with commas. Check with your SEO champ before you change content in this article containing these terms."/>

La sección etiquetas usa la siguiente sintaxis:

    <tags
       ms.service="required"
       ms.devlang="may be required"
       ms.topic="article"
       ms.tgt_pltfrm="may be required"
       ms.workload="na"
       ms.date="mm/dd/yyyy"
       ms.author="Your MSFT alias or your full email address;semicolon separates two or more"/>

##<a name="usage"></a>Uso

- El nombre del elemento y los nombres de atributo distinguen mayúsculas de minúsculas.
- La <properties> sección debe ser la primera línea del archivo.
- Dejar una línea en blanco después de cada sección de metadatos y antes de su título de la página para asegurarse de que el título de página se convierten correctamente a HTML durante el proceso de publicación.

## <a name="attributes-and-values-for-the-properties-section"></a>Atributos y valores de la sección Propiedades

![](./media/article-metadata/checkmark-small.png)**pageTitle**: obligatorio; importante SEO. El texto para este atributo aparece en la pestaña del navegador y como título en un resultado de búsqueda. Usar 55 60 caracteres incluidos los espacios y el identificador del sitio *, incluidos | Microsoft Azure* (escrito como: espacio espacio de canalización Microsoft Azure).  La pageTitle debe ser diferente a los H1.

![](./media/article-metadata/checkmark-small.png)**Descripción**: obligatorio; importante para SEO (relevancia) y la funcionalidad del sitio. La descripción debe tener al menos 125 caracteres tarda en 155 caracteres máximos, incluidos los espacios. Describir el propósito de su contenido para que los clientes saben si debe elegir en una lista de resultados de búsqueda. El valor es:

- Este texto se mostrará como la descripción o párrafo abstracto en los resultados de búsqueda en Google.
- Este texto se muestra en [los resultados del índice de artículo](https://azure.microsoft.com/documentation/articles/).

![](./media/article-metadata/checkmark-small.png)**Servicios**: necesarios para artículos que tratan con un servicio. Este valor se denomina ofter "indicaciones de servicio". Lista de todos los servicios aplicables, separados por comas. El primer servicio lista conducir la navegación rutas de navegación de la página y el panel de navegación izquierdo que se muestra con la página.

En los artículos que especifican un valor de servicios y un valor de documentationCenter, el valor de servicios determinará la ruta de navegación. Valores adicionales que se lista aparecerán como etiquetas en el artículo publicado. Valores:

- Active directory
- Active-directory-b2c
- activo de directorio ds
- aplicación service\api
- administración de la API
- aplicación de servicio
- aplicación servic\mobile
- aplicación service\web
- aplicación service\logic
- puerta de enlace de aplicaciones
- información de la aplicación
- automatización
- portal de Azure
- Administrador de recursos de Azure
- pila de Azure
- copia de seguridad
- proceso por lotes
- mejores prácticas
- Servicios de BizTalk
- caché
- CDN
- Servicios de nube
- catálogo de datos
- Generador de datos
- análisis de datos lago
- almacén de datos lago
- laboratorio de devtest
- DNS
- documentdb
- expressroute
- evento-hubs
- hdinsight
- hub de IOT
- depósito de clave
- equilibrador de carga
- aprendizaje de equipo
- catálogo de soluciones
- Servicios de medios
- compromiso de móvil
- Servicios de móvil
- autenticación multifactor
- notificación hubs
- perspectivas operativas
- serie de administración de operaciones
- powerapps
- Administrador de recuperación
- caché Redis
- RemoteApp
- administración de derechos
- programador
- búsqueda
- Centro de seguridad
- bus de servicio
- tela de servicio
- recuperación de sitio
- base de datos de SQL
- almacén de datos de SQL
- informes de SQL
- almacenamiento de información
- almacenar
- storsimple
- análisis de secuencia
- Administrador de tráfico
- máquinas virtuales
- red virtual
- online visual studio
- puerta de enlace VPN
- sitios Web

![](./media/article-metadata/checkmark-small.png)**documentationCenter**: necesario para mejores centrado en el desarrollo de artículos destacados a través de un centro de desarrollo. Especificar el centro de desarrollo único o el idioma que se aplica el artículo. El valor de la lista que conducir la navegación rutas de navegación de la página. En los artículos que especifican un valor de servicios y un valor de documentationCenter, el valor de servicios determinará la ruta de navegación. Valores:

- **.NET**
- **nodejs**
- **Java**
- **PHP**
- **Python**
- **Ruby**
- **móvil**: descartado. Reemplazar con la plataforma móvil específica.
- **IOS**: comprobando este nuevo valor
- **Android**: comprobar este nuevo valor
- **Windows**: comprobar este nuevo valor
- **xamarin**: comprobar este nuevo valor

![](./media/article-metadata/checkmark-small.png)**autores**: requiere un único valor. Lista de la cuenta de GitHub para el autor principal o artículo SME. Este atributo unidades de la línea de autor en el artículo publicado. La lista solo uno a pesar del nombre del atributo plural.

![](./media/article-metadata/checkmark-small.png)**Manager**: requerido si es un colaborador de Microsoft. El alias de correo electrónico del Administrador de publicación de contenido para el área de tecnología de la lista. Si es un colaborador de la Comunidad, incluya el atributo pero deje vacío para que nos podemos rellenar.

![](./media/article-metadata/checkmark-small.png)**Editor**: no se utiliza. No use para otros fines.

![](./media/article-metadata/checkmark-small.png)**etiquetas**: opcional. Incluir solo si desea habilitar un vínculo en la ruta del artículo a la página de índice de artículo (http://azure.microsoft.com/documentation/articles/) a una lista de artículos que coincidan con uno de los valores aprobados prefiltered. Estos valores están diseñados para proporcionar una manera de agrupar contenido cuando la agrupación de contenido no es específico del servicio. Estas etiquetas también pueden proporcionar etiquetado que indica que el artículo se aplica a la pila de tecnología. Este valor **no** es compatible con etiquetas de forma libre o hashtags; las etiquetas deben estar habilitadas en el sitio. Puede proporcionar varios valores de las etiquetas a un artículo, separados por comas. Los valores aprobados son:

  - arquitectura
  - Administrador de recursos de Azure
  - administración del servicio de Azure
  - facturación
  - MySQL

![](./media/article-metadata/checkmark-small.png)**palabras clave**: opcional. Para uso SEO champs solo. Separa los términos con comas. **Consulte con su ámbito SEO antes de cambiar o eliminar el contenido de este artículo que contiene estos términos.** Este atributo registros palabras clave de ámbito SEO que se dirige y seguimiento para mejorar el rango de búsqueda. Las palabras clave no se procesan en el HTML publicado. Validación no requiere este atributo.

## <a name="attributes-and-values-for-the-tags-section"></a>Atributos y valores de la sección de etiquetas

![](./media/article-metadata/checkmark-small.png)**ms.Service**: obligatorio. Especifica el servicio de Azure, herramienta o característica que se aplica el artículo. Un valor para cada página.

 Si una página se aplica a varios servicios, seleccione el servicio al que se más directamente aplica; Por ejemplo, un artículo que usa una aplicación hospedada en los sitios web que muestran la funcionalidad de Bus de servicio debe tener el valor de **bus de servicio** , en lugar de **sitios web**. Si una página también se aplica a varios servicios, elija **varios**. Si una página no se aplica a los servicios (será poco frecuente), elija el **NA**.

 - **Active directory**
 - **Active-directory-b2c**
 - **activo de directorio ds**
 - **administración de la API**
 - **aplicación de servicio**: solo se aplica a material conceptual general en la aplicación de servicio
 - **api del servicio de aplicación**
 - **lógica de aplicación de servicio**
 - **servicio de aplicación de móvil**
 - **aplicación web de servicio**
 - **información de la aplicación**
 - **puerta de enlace de aplicaciones**
 - **automatización**
 - **Administrador de recursos de Azure**
 - **seguridad de Azure**
 - **pila de Azure**
 - **copia de seguridad**
 - **proceso por lotes**
 - **mejores prácticas**
 - **Servicios de BizTalk**
 - **facturación**
 - **caché**
 - **CDN**
 - **Servicios de nube**
 - **catálogo de datos**
 - **almacén de datos lago**
 - **análisis de datos lago**
 - **laboratorio de devtest**
 - **expressroute**
 - **hdinsight**
 - **Internet de cosas**
 - **hub de IOT**
 - **depósito de clave**
 - **aprendizaje de equipo**
 - **catálogo de soluciones**: artículos sobre Azure marketplace
 - **Servicios de medios**
 - **compromiso de móvil**
 - **Servicios de móvil**
 - **autenticación multifactor**
 - **varios**: la página también se aplica a varios servicios
 - **NOD**: la página no se aplica a los servicios (raros)
 - **notificación hubs**
 - **perspectivas operativas**
 - **powerapps**
 - **Administrador de recuperación**
 - **caché Redis**
 - **RemoteApp**
 - **administración de derechos**
 - **programador**
 - **Centro de seguridad**
 - **bus de servicio**
 - **tela de servicio**
 - **recuperación de sitio**: servicios de recuperación anteriormente
 - **base de datos de SQL**
 - **almacén de datos de SQL**
 - **informes de SQL**
 - **almacenamiento de información**
 - **almacenar**: artículos sobre los servicios disponibles a través de la tienda de Azure
 - **storsimple**
 - **Administrador de tráfico**
 - **máquinas virtuales**
 - **red virtual**
 - **online visual studio**
 - **puerta de enlace VPN**
 - **sitios Web**

![](./media/article-metadata/checkmark-small.png)**ms.DevLang**: obligatorio. Especifica el lenguaje de programación que se aplica el artículo. Valor único por página.

 Si una página también se aplica a dos lenguajes de programación, elija **varios**. Si una página es principalmente conceptual y su contenido es generalmente aplicable a varios lenguajes de programación, elija **varios**. Si una página no está dirigida a los desarrolladores y la aplicación de lenguaje de programación no es relevante, elija el **NA**. Use la **api de rest** para identificar los temas de referencia de la API de REST.

 - **CPP**
 - **dotnet**
 - **Java**
 - **JavaScript**
 - **varios**: la página también se aplica a varios lenguajes de programación.
 - **NOD**: no se dirige a los desarrolladores de la página y no es específica de los lenguajes de programación.
 - **nodejs**
 - **objetivo c**
 - **PHP**
 - **Python**
 - **api de REST**
 - **Ruby**


![](./media/article-metadata/checkmark-small.png)**ms.topic**: obligatorio. Escriba el tema de características específicas. Mayoría de las páginas nuevas creada por colaboradores será artículo o referencia.

 - **artículo**: un tema conceptual, tutorial, Guía de función u otro artículo sin referencia

 - **página de campaña**: Azure.com solo.  Una página que está diseñada específicamente como una página de inicio de campañas externas y no se incluye como parte del sitio primario IA.  No se recomienda para los artículos de documentación o documento normal de las páginas de inicio.  Ejemplos: azure.microsoft.com/develop/net/aspnet/; Azure.Microsoft.com/Develop/Mobile/IOS/

 - **desarrollo de centro de inicio página**: Azure.com solo.  Un desarrollo centrar la página principal, por ejemplo, / desarrollar/net /

 - **artículo iniciado Get**: asignar a artículos que se muestran en la sección Introducción o información general sobre el panel de navegación izquierdo de un servicio.

 - **artículo principal**: un tutorial de "imagen" está diseñado para proporcionar una introducción a un servicio o característica que obtiene los visitantes comienza a usar el servicio rápidamente y unidades de suscripciones de prueba gratuita y activaciones de MSDN. Asignar este valor solo a artículos que se muestran en la parte superior de la página de inicio de la documentación de su servicio.

 - **página principal**: página de inicio de la documentación de nivel superior. Sólo tenemos dos: azure.microsoft.com/documentation/ y msdn.microsoft.com/library/azure/

 - **página de índice**: las páginas de idiomas, servicios o características de programación de inicio de segundo nivel. Estos se extienden a través de Azure.com y la biblioteca y se usan como puntos de entrada para obtener información más específica, departamento. Ejemplos: http://azure.microsoft.com/develop/mobile/resources-wp8/, http://msdn.microsoft.com/library/azure/jj673460.aspx, http://msdn.microsoft.com/library/azure/hh689864.aspx

 - **página de infographic**: Azure.com solo. Una página que incluye una infographic puede examinar o póster, por ejemplo http://azure.microsoft.com/documentation/infographics/windows-azure/

 - **referencia**: API de una página de referencia (incluidas las API de REST) o PowerShell cmdlet referencia

 - **página de inicio de servicio**: Azure.com solo.  Una página de inicio del servicio de documento, por ejemplo, /documentation/services/virtual-machines /

 - **sitio de sección-página de inicio**: Azure.com solo. Una página de inicio de"" para un determinado tipo de contenido en azure.com ejemplos: http://azure.microsoft.com/documentation/infographics/, http://azure.microsoft.com/documentation/scripts/, http://azure.microsoft.com/documentation/videos/home/, http://azure.microsoft.com/downloads/

 - **página de vídeo**: Azure.com solo.  Una página que incluye un vídeo, por ejemplo http://azure.microsoft.com/documentation/videos/azure-webjobs-hosting-testing-net/

![](./media/article-metadata/checkmark-small.png)**ms.tgt_pltfrm**: obligatorio. Especifica la plataforma de destino, por ejemplo Windows, Linux, Windows Phone, iOS, Android o plataformas de caché especial. Un valor para cada página. Este valor será el **NA** para la mayoría de los temas excepto mobile y máquinas virtuales de Windows.

 - **rol de caché**
 - **múltiplo de caché**
 - **redis de caché**
 - **servicio de caché**
 - **compartido en caché**
 - **interfaz de comandos línea**
 - **Ibiza**: contenido que usa el portal de Ibiza. Use esta opción solo en casos donde está disponible la característica que se describe en el portal de Ibiza y el portal actual.
 - **android Mobile**: Azure.com solo ahora mismo
 - **Mobile html**: Azure.com solo ahora mismo
 - **ios Mobile**: Azure.com solo ahora mismo
 - **kindle Mobile**: Azure.com solo ahora mismo
 - **múltiplo de móvil**
 - **Mobile-nokia-x**: Azure.com solo ahora mismo
 - **phonegap Mobile**: Azure.com solo ahora mismo
 - **sencha Mobile**: Azure.com solo ahora mismo
 - **windows Mobile**: Azure.com solo ahora mismo; Universal de Windows
 - **móvil de windows**
 - **tienda de windows Mobile**
 - **xamarin Mobile**: Azure.com solo ahora mismo; Xamarin todas las plataformas
 - **Mobile-xamarin-android**: Azure.com solo ahora mismo
 - **ios de xamarin de móvil**: solo ahora Azure.com
 - **varios**: la página también se aplica a varias plataformas
 - **NOD**: un especificador de plataforma no es aplicable para esta página
 - **PowerShell**
 - **máquina virtual linux**
 - **varios VM**
 - **máquina virtual de windows**
 - **windows de máquina virtual de sharepoint**
 - **máquina virtual de windows sql server**
 - **VS--Introducción**: identifica el grupo de la página Introducción a VS. Etiqueta agrega 1/12/14.
 - **VS-qué-ocurrió**: identifica la página VS obtener iniciado ¿qué ha ocurrido. Etiqueta agrega 1/12/14.

![](./media/article-metadata/checkmark-small.png)**ms.Workload**: obligatorio. Especifica la carga de trabajo de Azure que se aplica la página. Un valor solo por artículo.

**Actualizar 8/6/15** Se asigna el valor de ms.workload por un xls, no el valor en el archivo .md. El valor de ms.workload sigue siendo necesario para la validación hasta que se puede actualizar la característica. Ahora se programa que funcionan.  Utilice **"NOD"** como el valor por ahora.

![](./media/article-metadata/checkmark-small.png)**ms.Date**: obligatorio. Especifica la fecha de que última revisión el artículo de relevancia, precisión, capturas de pantalla correcto y vínculos de trabajo. Escriba la fecha en formato de mm/dd/aaaa. Esta fecha también aparece en el artículo publicado como la última fecha actualizada.

![](./media/article-metadata/checkmark-small.png)**ms.author**: obligatorio. Especifica los autores asociado al tema. Informes internos (como actualización) usarán este valor para asociar autores derecha con el artículo. Para especificar varios valores debe separar con punto y coma. Alias de Microsoft o direcciones de correo electrónico completa son aceptables. La longitud puede no tener más de 200 caracteres.


###<a name="contributors-guide-links"></a>Vínculos de la Guía de colaboradores

- [Artículo de información general](./../README.md)
- [Índice de artículos de guía](./contributor-guide-index.md)


<!--Anchors-->
[Sintaxis]: #syntax
[Uso]: #usage
[Atributos y valores de la sección Propiedades]: #attributes-and-values-for-the-properties-section
[Atributos y valores de la sección de etiquetas]: #attributes-and-values-for-the-tags-section
