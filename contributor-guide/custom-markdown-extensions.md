<properties
    title="required"
    pageTitle="Extensiones de descuento personalizado utilizadas en nuestros artículos técnicos"
    description="Enumera las extensiones de descuento personalizado que permiten vídeos incrustados, notas y sugerencias, contenido reutilizable y otros elementos de artículos técnicos de azure.microsoft.com."
    services=""
    solutions=""
    documentationCenter=""
    authors="tysonn"
    manager="carolz"
    editor=""/>

<tags
    ms.service="contributor-guide"
    ms.devlang=""
    ms.topic="article"
    ms.tgt_pltfrm=""
    ms.workload=""
    ms.date="01/22/2015"
    ms.author="tysonn"/>

## <a name="markdown-for-azuremicrosoftcom"></a>Descuento para Azure.microsoft.com

Para obtener sugerencias de descuento general, consulte [Conceptos básicos de descuento](https://help.github.com/articles/markdown-basics/) y nuestros [datos básicos de descuento](./media/documents/markdown-cheatsheet.pdf?raw=true). Si necesita crear vínculos cruzados de artículo en descuento, vea [orientación vinculación] (. / create-links-markdown.md#markdown-syntax-for-acom-relative-links.md/).

Es compatible con Azure.Microsoft.com [bloqueado bloques de código](https://help.github.com/articles/github-flavored-markdown/#fenced-code-blocks) y [resaltado de sintaxis](https://help.github.com/articles/github-flavored-markdown/#syntax-highlighting). Sin embargo, ACOM admite solo una combinación de colores, independientemente del idioma que especifique en un bloque de código para resaltar la sintaxis.

## <a name="custom-markdown-extensions-used-in-our-technical-articles"></a>Extensiones de descuento personalizado utilizadas en nuestros artículos técnicos

Nuestros artículos usar GitHub saborizado descuento para la mayor parte del artículo formato - párrafos, vínculos, listas, encabezados, etcetera. Pero usamos extensiones descuento personalizado donde se necesita formato más rica en las páginas representadas en azure.microsoft.com. Le presentamos las extensiones que actualmente utilizamos:

+ [Notas y sugerencias]
+ [Incluye]
+ [Vídeos incrustados]
+ [Selectores de plataforma y tecnología]

## <a name="notes-and-tips"></a>Notas y sugerencias

Puede elegir entre 4 tipos de notas y sugerencias:

- AZURE. NOTA
- AZURE. ADVERTENCIA
- AZURE. TIPss
- AZURE. IMPORTANTE

###<a name="usage"></a>Uso
En general, use notas y sugerencias con moderación a lo largo de su artículos. Cuando usarlos, elija el tipo de nota o sugerencia adecuado:

- Use AZURE. Nota para resaltar información neutral o positiva que destaca o complementos de puntos esenciales del texto principal. Una nota proporciona información que se aplica solo en casos especiales.

  ![](./media/custom-markdown-extensions/Notes-note.PNG)

- Use AZURE. ADVERTENCIA para avisar al usuario a una condición que podría provocar un problema en el futuro. Por ejemplo, seleccionar una opción de determinadas o realizar una determinada opción podría permanentemente bloquear en un escenario determinado.

  ![](./media/custom-markdown-extensions/Notes-warning.PNG)

- Use AZURE. Sugerencia para ayudar a los usuarios aplicar las técnicas y los procedimientos descritos en el texto a sus necesidades específicas. Sugerencia También puede sugerir otros métodos que pueden no ser obvios. Sin embargo, las sugerencias, no son esenciales para la comprensión básica del texto.

  ![](./media/custom-markdown-extensions/Notes-tip.PNG)

- Use AZURE. IMPORTANTE para proporcionar información esencial para la finalización de una tarea.

  ![](./media/custom-markdown-extensions/Notes-important.PNG)

Mientras estas notas y sugerencias admiten vínculos, imágenes, listas y bloques de código, intente mantener sus notas y sugerencias de forma muy sencilla. Si se encuentra crear notas complejas con muchos de formato, que puede ser un inicio de sesión ¿necesita otra sección en el texto principal de este artículo. Y pueden ser demasiadas notas en un artículo que distraigan y difíciles de analizar o leer.

###<a name="sample-markdown"></a>Descuento de ejemplo

Los ejemplos todos muestran un AZURE. TENGA EN CUENTA. Para usar una sugerencia, advertencia o importante, reemplace "Nota" en el descuento:

    > [AZURE.TIP]

    > [AZURE.WARNING]

    > [AZURE.IMPORTANT]

Solo párrafo:

    > [AZURE.NOTE] Para completar este tutorial, debe tener una cuenta de Microsoft Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos.

Multiparagraph:

    > [AZURE.NOTE] Para completar este tutorial, debe tener una cuenta de Microsoft Azure active.
    >
    > Si no tiene una cuenta, puede [crear una cuenta de prueba gratuita](http://www.windowsazure.com/pricing/free-trial/) de dos minutos.

## <a name="includes"></a>Incluye

Texto reutilizable en nuestro repositorio GitHub reside en archivos que llamamos "incluye". Cuando tenga el texto que debe usarse en varios artículos, incluir una referencia a ese archivo de información reutilizable. Incluir propio es un archivo de descuento simple (.md). Puede contener cualquier descuento válido, incluyendo texto, vínculos e imágenes. Todos incluyen descuento archivos deben estar en [la incluye directorio](https://github.com/Azure/azure-content/tree/master/includes) en la raíz del repositorio. Cuando se publique el artículo, se integra perfectamente incluir texto en el tema publicado.

- Usamos una sintaxis específica para hacer referencia a incluir.

- Archivos multimedia que ponga en incluir deben crearse en una carpeta de medios específicos para incluir. Carpetas de medios para incluye pertenecen a [la carpeta de azure contenido/incluye/medios](https://github.com/Azure/azure-content/tree/master/includes/media). El directorio media no debe contener las imágenes en su raíz. Si no tiene la incluir imágenes, no se requiere un directorio de medios correspondiente.

###<a name="usage"></a>Uso

- Utilice incluye siempre que lo necesite el mismo texto que aparezca en varios artículos.

- Incluye están diseñados para usarse con grandes cantidades de contenido - un párrafo o dos, un procedimiento compartido o una sección compartida. No se utilice para nada más pequeño que una oración; **son no para nombres de producto**.

- Asegúrese de que todo el texto en un incluir escrito en frases completas o frases que no dependen de texto anterior o siguiente en el artículo que hace referencia la incluir. Omitir esta guía, crea una cadena de traducir en el artículo que se interrumpe la experiencia localizada. 

- No incrustar incluye dentro de otras incluye. No admiten por el sistema de publicación de DPS.

- No compartir medios entre archivos. Usar un archivo independiente con un nombre único para cada incluir y el artículo. Almacenar el archivo multimedia en la carpeta de medios asociada con la incluir.

- No use incluir como sólo el contenido de un artículo.  Incluye están diseñados para ser complementaria al contenido en el resto de este artículo.

- Porque todos incluye debe estar en la incluye directorio, incluir la ruta de un artículo es siempre

    .. incluye

- No repetir una referencia de nombre de archivo de imagen o vínculo en el artículo e incluir. Agregar "-incluir" al vínculo referencia o media nombre de archivo para evitar la repetición de la referencia:

 **Referencia de vínculo**

 Cambiar: odata.org a: incluir odata.org

 **Referencia de la imagen**

 Cambiar: table.png a: include.png de tabla

###<a name="sample-markdown"></a>Descuento de ejemplo
La sintaxis para agregar una inclusión a un artículo de la documentación es:

    [AZURE.INCLUDE [include-short-name](../includes/include-file-name.md)]

Ejemplo

    [AZURE.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

La primera parte de la incluir es el nombre de incluir sin la ruta de acceso y sin la extensión .md. La segunda parte es la ruta de acceso relativa para incluir en la incluye directorio, con la extensión .md.

###<a name="rendering"></a>Representación

En la página de GitHub representada la incluir se representará como sigue:

 [AZURE. INCLUIR almacenamiento de blobs de cómo]

En el HTML presentado en azure.microsoft.com, el código HTML de la incluye se combina con el resto de HTML del documento. Sin embargo, el código HTML contendrá un HTML comentario con el original incluyen el nombre de archivo de descuento y el hash de confirmación de GitHub. Este comentario se incluye para solucionar problemas para que el contenido de origen puedan fácilmente identificado y que se encuentra en GitHub:

  ![](./media/custom-markdown-extensions/include.png)


## <a name="embedded-videos"></a>Vídeos incrustados

Nuestros artículos técnicos admiten embeddeded vídeos en artículos técnicos como los vídeos se encuentran en el sitio de [canal 9](http://channel9.msdn.com/) de Microsoft. Los vídeos de canal 9 deben estar integrados con [el centro de vídeo azure.microsoft.com](http://azure.microsoft.com/documentation/videos/home/). Actualmente no se admiten vídeos de YouTube incrustados; Si es un colaborador de la Comunidad, pueden vincular a YouTube si el vídeo que desea destacar se publica allí. Los colaboradores de Microsoft deben usar canal 9 y el centro de vídeo.

### <a name="usage"></a>Uso

- Asegúrese de que el vídeo se encuentra en el centro de vídeo.

- Copie el identificador de vídeo desde la dirección URL descriptiva del vídeo en canal 9 o desde el centro de vídeo de Azure. Por ejemplo, el identificador de vídeo para el vídeo en [http://azure.microsoft.com/documentation/videos/azure-scheduler-unusual-schedules/](http://azure.microsoft.com/documentation/videos/azure-scheduler-unusual-schedules/) es **azure programador-inusual programaciones**.

### <a name="syntax"></a>Sintaxis

    > [AZURE.VIDEO video-id-string]

### <a name="rendering"></a>Representación

En GitHub: [https://github.com/Azure/azure-content-pr/blob/master/articles/web-sites-backup.md](https://github.com/Azure/azure-content-pr/blob/master/articles/web-sites-backup.md)

Artículo publicado: [http://azure.microsoft.com/documentation/articles/web-sites-backup/](http://azure.microsoft.com/documentation/articles/web-sites-backup/)


## <a name="technology-and-platform-selectors"></a>Selectores de plataforma y tecnología

Use conmutadores plataforma y tecnología de artículos técnicos al crear varias versiones del mismo artículo diferencias de dirección en la implementación a través de tecnologías o plataformas. Esto es normalmente más aplicable a nuestro contenido plataforma móvil para programadores. Hay dos tipos de selectores, [selectores simples](#simple-selectors) y [selectores bidireccionales](#two-way-selectors).

Ya que el mismo descuento de selector en cada tema en la selección, se recomienda colocar el selector de su tema en incluir, a continuación, hacer referencia a ese incluir en todos los temas que usan el mismo selector.

###<a id="simple-selectors"></a>Selectores simples

Selectores (unidireccionales) simples representarán como un conjunto de botones de opción justo debajo del título. Utilice estos botones cuando los clientes necesitan para elegir entre temas en un único conjunto de plataforma o tecnología, como .NET, Node.js y Java.  Usar la extensión de descuento personalizado para cualquier selectores: no utilizar HTML para selectores.  

Consulte [Introducción a Hubs de notificación](http://azure.microsoft.com/documentation/articles/notification-hubs-windows-phone-get-started/) para ver cómo el autor creó 8 versiones del mismo artículo pero selectores usados para habilitar la navegación en todos.

![Ejemplo de selector simple](./media/custom-markdown-extensions/selectors.PNG)

####<a name="syntax"></a>Sintaxis

    > [AZURE.SELECTOR]
    - [Vincular etiqueta 1 #](link #1 url)
    - [etiqueta vínculo #2](link #2 url)

Ejemplo:

    > [AZURE.SELECTOR]
    - [Windows universal](../articles/notification-hubs-windows-store-dotnet-get-started/)
    - [Windows Phone](../articles/notification-hubs-windows-phone-get-started/)
    - [iOS](../articles/notification-hubs-ios-get-started/)
    - [Android](../articles/notification-hubs-android-get-started/)
    - [Kindle](../articles/notification-hubs-kindle-get-started/)
    - [Baidu](../articles/notification-hubs-baidu-get-started/)
    - [Xamarin.iOS](../articles/partner-xamarin-notification-hubs-ios-get-started/)
    - [Xamarin.Android](../articles/partner-xamarin-notification-hubs-android-get-started/)

#### <a name="rendering"></a>Representación

La imagen anterior muestra la representación en azure.microsoft.com. En las páginas de GitHub representadas, los selectores de representarán como una lista con viñetas de vínculos.

###<a id="two-way-selectors"></a>Selectores bidireccionales

Selectores bidireccionales permite a los usuarios seleccionar una temas de una matriz de dos formas. Esto es esencial cuando una tecnología de Azure, como servicios móviles, admite varias plataformas de back-end, así como varios clientes. Tenga en cuenta lo siguiente:

- Mientras se ha diseñado como `(Platform | Backend)`, ahora se puede personalizar el texto dropwdown.
- No necesita un elemento de lista de cada punto de la matriz, pero solo tiene un elemento en una dirección URL de tema existe y no es un duplicado.
- El vínculo puede ser cualquier dirección URL, aunque es generalmente otro tema de GitHub.

Para ver cómo el autor crea 15 versiones del mismo artículo (9 plataformas de clientes móviles y plataformas de back-end 2), pero selectores usados para habilitar la exploración en todos ellos, vea [Introducción a servicios para dispositivos móviles](http://azure.microsoft.com/en-us/documentation/articles/mobile-services-ios-get-started/) . Tenga en cuenta que los 3 artículos no tienen ambas versiones de back-end.

![Ejemplo de selectores bidireccional](./media/custom-markdown-extensions/selector-list.png)

####<a name="syntax"></a>Sintaxis

    > [AZURE. SELECTOR de lista (Dropdown1 | Dropdown2)]     -  [(Dropdown1Text1 | Dropdown2Text1)](../articles/dropdown1-text1-dropdown2-text1.md)
    - [(Dropdown1Text1 | Dropdown2Text2)](../articles/dropdown1-text1-dropdown2-text1.md)
    - [(Dropdown1Text2 | Dropdown2Text3)](../articles/dropdown1-text1-dropdown2-text1.md)
    - [(Dropdown1Text3 | Dropdown2Text4)](../articles/dropdown1-text1-dropdown2-text1.md)

Ejemplo:

    > [AZURE. SELECTOR de lista (plataforma | Back-end)]     -  [(iOS | .NET)](./mobile-services-dotnet-backend-ios-get-started-push.md)
    - [(iOS | JavaScript)](./mobile-services-javascript-backend-ios-get-started-push.md)
    - [(Windows universal C# | .NET)](./mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md)
    - [(Windows universal C# | JavaScript)](./mobile-services-javascript-backend-windows-universal-dotnet-get-started-push.md)
    - [(Windows Phone | .NET)](./mobile-services-dotnet-backend-windows-phone-get-started-push.md)
    - [(Windows Phone | JavaScript)](./mobile-services-javascript-backend-windows-phone-get-started-push.md)
    - [(Android | .NET)](./mobile-services-dotnet-backend-android-get-started-push.md)
    - [(Android | JavaScript)](./mobile-services-javascript-backend-android-get-started-push.md)
    - [(iOS Xamarin | JavaScript)](./partner-xamarin-mobile-services-ios-get-started-push.md)
    - [(Xamarin Android | JavaScript)](./partner-xamarin-mobile-services-android-get-started-push.md)

#### <a name="rendering"></a>Representación

La imagen anterior muestra la representación en azure.microsoft.com. En las páginas de GitHub representadas, los selectores de representarán como una lista con viñetas de vínculos.

<!--Anchors-->
[Notas y sugerencias]: #notes-and-tips
[Incluye]: #includes
[Vídeos incrustados]: #embedded-videos
[Selectores de plataforma y tecnología]: #technology-and-platform-selectors

###<a name="contributors-guide-links"></a>Vínculos de la Guía de colaboradores

- [Artículo de información general](./../README.md)
- [Índice de artículos de guía](./contributor-guide-index.md)
