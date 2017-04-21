<properties
   pageTitle="Crear vínculos de artículos de descuento" description="Se explica cómo codificar vínculos cruzados en descuento." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="carolz" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="02/03/2015" ms.author="tysonn" />

# <a name="linking-guidance-for-azure-technical-content"></a>Guía de vinculación de Azure contenido técnico

### <a name="links-from-one-acom-article-to-another"></a>Vínculos de un artículo ACOM a otro

Para crear un vínculo insertado de un artículo técnico de ACOM a otro artículo técnico de ACOM, use la siguiente sintaxis vínculo:  

- Un artículo en los vínculos de un directorio de servicio a otro artículo en el mismo directorio de servicio:

  `[link text](article-name.md)`

- Vínculos de un artículo desde un subdirectorio servicio a un artículo en el directorio raíz:

  `[link text](../article-name.md)`

- Un artículo en los vínculos del directorio de raíz a un artículo en un subdirectorio de servicio: 

  `[link text](./service-directory/article-name.md)`

- Un artículo de un servicio subdirectorio está vinculada a un artículo en otro subdirectorio de servicio:

  `[link text](../service-directory/article-name.md)`
 

## <a name="links-to-anchors"></a>Vínculos a los delimitadores

No tiene que crear los delimitadores: se generan automáticamente en tiempo de todos los títulos de H2 de publicación. Lo único que tiene que hacer es crear vínculos a las secciones H2.

- Para vincular a un encabezado en el mismo artículo:

  `[link](#the-text-of-the-H2-section-separated-by-hyphens)`  
  `[Create cache](#create-cache)`

- Para vincular a un anclaje en otro artículo en el mismo subdirectorio:

  `[link text](article-name.md#anchor-name)`
  `[Configure your profile](media-services-create-account.md#configure-your-profile)`

- Para vincular a un anclaje en otro subdirectorio de servicio:

  `[link text](../service-directory/article-name.md#anchor-name)`
  `[Configure your profile](../service-directory/media-services-create-account.md#configure-your-profile)`

Una manera de automatizar el proceso de creación de vínculos en los artículos a los vínculos de anclaje generado automáticamente es [MarkdownAnchorLinkGenerator - una herramienta para generar los vínculos de anclaje de ACOM en el formato correcto](https://github.com/Azure/Azure-CSI-Content-Tools/tree/master/Tools/ACOMMarkdownAnchorLinkGenerator).

## <a name="links-from-includes"></a>Incluyen vínculos desde

Dado que incluyen archivos se encuentran en otro directorio, debe usar rutas de acceso relativas ya como se muestra a continuación. Para establecer un vínculo a un artículo desde un archivo de inclusión, use este formato:

    [link text](../articles/service-folder/article-name.md)
    
Más información sobre cómo usar un archivo de inclusión en las [directrices de extensiones de descuento personalizado](custom-markdown-extensions.md#includes).

## <a name="links-in-selectors"></a>Vínculos de selectores

Si tiene selectores que están incrustados en una instrucción include, debería usar a este tipo de vinculación: 

    > [AZURE. SELECTOR de lista (Dropdown1 | Dropdown2)]     -  [(Texto1 | Ejemplo1)](../articles/service-folder/article-name1.md)
    - [(Texto1 | Example2)](../articles/service-folder/article-name2.md)
    - [(Texto2 | Ejemplo 3)](../articles/service-folder/article-name3.md)
    - [(Texto2 | Example4)](../articles/service-folder/article-name4.md)


## <a name="reference-style-links"></a>Vínculos de estilo de referencia

Puede usar los vínculos de estilo de referencia para facilitar la lectura de su contenido de origen. Los vínculos de estilo de referencia reemplazan la sintaxis de vínculo en línea con sintaxis simplificada que le permite mover las direcciones URL largas al final de este artículo. Este es el ejemplo de audacia bola de fuego:

Texto en línea:

    I get 10 times more traffic from [Google][1] than from [Yahoo][2] or [MSN][3].

Referencias de vínculo al final de este artículo:

    <!--Reference links in article-->
    [1]: http://google.com/
    [2]: http://search.yahoo.com/  
    [3]: http://search.msn.com/

Asegúrese de que incluir el espacio después de los dos puntos, antes del vínculo. Cuando se vincula a otros artículos técnicos, si olvida incluir el espacio, se rompe el vínculo en el artículo publicado. 

## <a name="link-to-acom-pages-that-are-not-part-of-the-technical-documentation-set"></a>Vincular a páginas ACOM que no forman parte del conjunto de documentación técnica

Para establecer un vínculo a una página de ACOM (por ejemplo, una página de precios, SLA página o nada que no es un artículo de la documentación), utilice una dirección URL absoluta, pero omite la configuración regional. El objetivo es que funcionan en GitHub y en el sitio representado de vínculos:

    [link text](http://azure.microsoft.com/pricing/details/virtual-machines/)


## <a name="link-to-msdn-or-technet"></a>Vínculo a MSDN o TechNet

Cuando necesite vincular a MSDN o TechNet, utilice el vínculo completo al tema y quite la en-us desde el vínculo Configuración regional. 

### <a name="use-friendly-link-text-for-all-links"></a>Usar texto de vínculo descriptivo para todos los vínculos

Las palabras que incluye un vínculo deben ser descriptivas: en otras palabras, debería palabras en inglés normales o el título de la página que desea vincular. No use "haga clic aquí". Es conveniente que SEO y no describen adecuadamente el destino.

**Corregir:**

- `For more information, see the [contributor guide index](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).`

- `For more details, see the [SET TRANSACTION ISOLATION LEVEL](https://msdn.microsoft.com/library/ms173763.aspx) reference.`

**Incorrecto:**

- `For more details, see [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).`

- `For more information, click [here](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).`


## <a name="fwlinks"></a>Vínculos

Evitar vínculos (nuestro sistema de redireccionamiento) en el contenido de azure.microsoft.com. Debe utilizarse sólo como último recurso cuando tenga que crear un vínculo a una página cuya dirección URL aún no se conoce. Casi nunca realmente son necesarios. Para ACOM, defina el nombre de archivo, por lo que puede saber qué está adelantado. Para un tema de la biblioteca que todavía no publicado, puede crear un vínculo que utiliza el tema GUID para que no tiene que utilizar un vínculo de redirección.

Si debe usar un vínculo de redirección en una página web, incluya el parámetro P para que sea una redirección permanente:

    http://go.microsoft.com/fwlink/p/?LinkId=389595

Cuando pegue la dirección URL de destino en la herramienta de vínculo de redirección, no olvide quitar la configuración regional si el vínculo de destino es biblioteca ACOM, el MSDN o TechNet.

## <a name="remember-the-azure-library-chrome"></a>Recuerde que el cromo biblioteca Azure.
Si desea establecer un vínculo a un tema de la biblioteca de Azure que reside en [este nodo](https://msdn.microsoft.com/library/azure), no olvide especificar el Azure chrome en el vínculo (/ azure /). El cromo Azure comparte las opciones de navegación de ACOM y muestra solo el contenido de la biblioteca MSDN de Azure. Un vínculo correctamente con ámbito es similar a esta:

    http://msdn.microsoft.com/library/azure/dd163896.aspx

En caso contrario, la página se representarán en la vista estándar de MSDN, con todo el árbol MSDN muestra.

### <a name="contributors-guide-links"></a>Vínculos de la Guía de colaboradores

- [Artículo de información general](./../README.md)
- [Índice de artículos de guía](./contributor-guide-index.md)

<!--image references-->
[1]: ./media/create-tables-markdown/table-markdown.png
[2]: ./media/create-tables-markdown/break-tables.png
