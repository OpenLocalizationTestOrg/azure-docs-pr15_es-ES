# <a name="azure-technical-documentation-contributor-guide"></a>Guía de colaborador de Azure documentación técnica

Haya encontrado el repositorio de GitHub que aloja el origen de la documentación técnica que se publica en el centro de documentación de Azure en [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation).

Este repositorio también contiene instrucciones para ayudarle a colaborar en nuestra documentación técnica.  Para obtener una lista de artículos de la Guía de los colaboradores, consulte [el índice](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).

## <a name="contribute-to-azure-documentation"></a>Contribuir a la documentación de Azure

Gracias por su interés en la documentación de Azure.

* [Formas de colaboración](#ways-to-contribute)
* [Código de conducta](#code-of-conduct)
* [Acerca de sus contribuciones a contenido de Azure](#about-your-contributions-to-azure-content)
* [Organización de repositorio](#repository-organization)
* [Usar GitHub, Git y este repositorio](#use-github-git-and-this-repository)
* [Cómo usar descuento para dar formato a su tema](#how-to-use-markdown-to-format-your-topic)
* [Comentarios, comentarios y soporte técnico](./contributor-guide/feedback-and-comments.md)
* [Más recursos](#more-resources)
* [Índice de artículos de la Guía de todos los colaboradores](./contributor-guide/contributor-guide-index.md) (se abre la página nuevo)

## <a name="ways-to-contribute"></a>Formas de colaboración 

Pueden contribuir a [Azure documentación](http://azure.microsoft.com/documentation/) de varias maneras diferentes:

* Contribuir a un [foro de debate](http://social.msdn.microsoft.com/Forums/windowsazure/home).
* Enviar comentarios Disqus en la parte inferior de artículos.
* Fácilmente pueden contribuir a artículos técnicos en la interfaz de usuario de GitHub. Buscar el artículo en este repositorio, o visite el artículo en [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) y haga clic en el vínculo del artículo que va a la fuente de GitHub para el artículo.
* Para realizar cambios importantes a un artículo existente, agregar o cambiar imágenes o que contribuyen a un nuevo artículo, debe se bifurcan este repositorio, instalar Git fiesta, descuento teclado y obtenga información sobre algunos comandos git.

##<a name="code-of-conduct"></a>Código de conducta

Este proyecto ha adoptado el [Microsoft Abrir origen de código de conducta](https://opensource.microsoft.com/codeofconduct/). Para obtener más información, vea [El código de realizar preguntas más frecuentes](https://opensource.microsoft.com/codeofconduct/faq/) o contacto [opencode@microsoft.com](mailto:opencode@microsoft.com) con las otras preguntas o comentarios.

##<a name="about-your-contributions-to-azure-content"></a>Acerca de sus contribuciones a contenido de Azure

###<a name="minor-corrections"></a>Correcciones secundarias

Correcciones menores o aclaraciones que enviar para documentación y ejemplos de código en esta repo ocultan el [Azure términos de uso del sitio Web (condiciones de uso)](http://azure.microsoft.com/support/legal/website-terms-of-use/).


###<a name="larger-submissions"></a>Envíos más grandes

Si envía una solicitud de extracción con los cambios nuevos o significativas documentación y ejemplos de código, le enviaremos un comentario en GitHub que le pide que envíe un contrato de licencia de contribución en línea (analizador) si se encuentra en uno de estos grupos:

* Miembros del grupo Open tecnologías de Microsoft.
* Colaboradores que no trabajan para Microsoft.

Es necesario completar el formulario en línea antes de que podemos aceptar la solicitud de extracción.

Detalles completos están disponibles en [http://azure.github.io/guidelines/#cla](http://azure.github.io/guidelines/#cla).

## <a name="repository-organization"></a>Organización de repositorio

El contenido en el repositorio de contenido de azure sigue la organización de documentación en [Azure.Microsoft.com](http://azure.microsoft.com). Este repositorio contiene dos carpetas raíz:

### <a name="articles"></a>\articles

La carpeta *\articles* contiene los artículos de documentación con formato de archivos de descuento con la extensión *.md* .

Artículos en el directorio raíz se publican en Azure.Microsoft.com en la ruta de acceso *http://azure.microsoft.com/documentation/articles/ {artículo-nombre-sin-md} /*.

* **Artículo de nombres de archivo:** Vea [nuestro archivo directrices de nomenclatura](./contributor-guide/file-names-and-locations.md).

Artículos dentro de su propia carpeta de servicio se publican en Azure.Microsoft.com en la ruta de acceso *http://azure.microsoft.com/documentation/articles/service-folder/ {artículo-nombre-sin-md} /*

* **Subcarpetas de medios:** La carpeta *\articles* contiene la carpeta *\media* archivos multimedia de raíz directory artículo, dentro de que son subcarpetas con las imágenes de cada artículo.  Las carpetas de servicio contienen una carpeta de medios independientes para los artículos dentro de cada carpeta de servicio. Las carpetas de la imagen de artículo con el mismo nombre en el archivo de artículo menos la extensión de archivo *.md* .

### <a name="includes"></a>\includes

Puede crear secciones de contenido reutilizables para incluirse en uno o más artículos. Vea [extensiones personalizados utilizadas en nuestro contenido técnico](./contributor-guide/custom-markdown-extensions.md).

### <a name="markdown-templates"></a>plantillas de \markdown

Esta carpeta contiene nuestra plantilla descuento estándar con el formato de descuento básico que necesita para un artículo.

### <a name="contributor-guide"></a>\contributor-Guide

Esta carpeta contiene artículos que forman parte de la Guía de nuestros colaboradores.  

## <a name="use-github-git-and-this-repository"></a>Usar GitHub, Git y este repositorio

Para obtener información sobre cómo contribuir, cómo usar la UI GitHub contribuir pequeños cambios y cómo se bifurcan y clonar el repositorio de aportaciones más importantes, consulte [instalar y configurar las herramientas de creación en GitHub](./contributor-guide/tools-and-setup.md).

Si instalar GitBash y trabajar de forma local, los pasos para crear una nueva bifurcación de trabajo local, realizar cambios y enviar de que nuevo los cambios en la bifurcación principal se muestran en [Git comandos para crear un nuevo artículo o actualizar un artículo existente](./contributor-guide/git-commands-for-master.md)

### <a name="branches"></a>Ramas

Le recomendamos que cree local bifurcaciones de trabajo que un ámbito específico de cambio de destino. Ramas deben limitarse a un único concepto/artículo tanto para simplificar el flujo de trabajo y reducir la posibilidad de conflictos de combinación.  Los esfuerzos siguientes son del ámbito apropiado para una nueva bifurcación:

* Un nuevo artículo (y las imágenes asociadas)
* Modificaciones de ortografía y gramática en un artículo.
* Aplicar un cambio de formato solo a través de un amplio conjunto de artículos (por ejemplo, nuevo copyright pie de página).

## <a name="how-to-use-markdown-to-format-your-topic"></a>Cómo usar descuento para dar formato a su tema

Todos los artículos de este repositorio use GitHub saborizado descuento.  Aquí tiene una lista de recursos.

- [Conceptos básicos de descuento](https://help.github.com/articles/markdown-basics/)

- [Hoja de referencia de descuento imprimible](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

- Para nuestra lista de editores de descuento, vea el [tema instalación y herramientas](./contributor-guide/tools-and-setup.md#install-a-markdown-editor).

## <a name="article-metadata"></a>Metadatos de artículo

Metadatos de artículo permite determinadas funcionalidades en el sitio web de azure.microsoft.com, como atribución de autor, atribución colaborador, rutas de navegación, las descripciones de artículo y SEO optimizaciones, así como Microsoft informes se usa para evaluar el rendimiento del contenido. ¡Por lo tanto, los metadatos están importante! [Aquí las directrices para asegurarse de que los metadatos se realiza hacia la derecha](./contributor-guide/article-metadata.md).

### <a name="labels"></a>Etiquetas

Etiquetas automatizadas están asignadas a extraer solicitudes para ayudar a administrar el flujo de trabajo de solicitud de extracción y ayudar a que sepa lo que ocurre con la solicitud de extracción:

* Contrato de licencia de contribución relacionados
    * analizador no requeridos: el cambio es relativamente pequeños y no requiere que inicie sesión un analizador.
    * requiere analizador: el ámbito del cambio es relativamente grande y requiere que inicie sesión un analizador.
    * firmado por el analizador: colaborador firmado el analizador para que la solicitud de extracción ahora puede desplazarse hacia delante para su revisión.
* Pilares etiquetas: etiquetas como PnP, aplicaciones modernas y TDC ayudan clasificar las solicitudes de extracción por la organización interna que necesita para revisar la solicitud de extracción.
* Cambiar enviado a autor: ha sido notificado el autor de la solicitud de extracción pendiente.

## <a name="more-resources"></a>Más recursos

Consulte el [índice de guía de nuestro colaborador](./contributor-guide/contributor-guide-index.md) para todos los temas de nuestra guía.
