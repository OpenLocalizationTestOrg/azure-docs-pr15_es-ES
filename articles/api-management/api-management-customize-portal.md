<properties
    pageTitle="Personalizar el portal de programadores en administración de la API de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo personalizar el portal de programadores en administración de la API de Azure."
    services="api-management"
    documentationCenter=""
    authors="steved0x"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="customize-the-developer-portal-in-azure-api-management"></a>Personalizar el portal de programadores en administración de la API de Azure

Esta guía le muestra cómo modificar la apariencia del portal programador en la administración de la API de Azure por coherencia con su marca.

## <a name="change-page-headers"> </a>Cambiar el texto o el logotipo en el encabezado de página

Uno de los aspectos clave de personalización portal va a reemplazar el texto en la parte superior de todas las páginas con el nombre de la compañía o un logotipo.

Se modifica el contenido en el portal de programadores a través del portal de publisher, que se tiene acceso a través del Portal de clásico de Azure. Para alcanzar el portal de publisher API, haga clic en **Administrar** en el Portal de Azure clásico de su servicio de administración de la API.

![Portal de Publisher][api-management-management-console]

El portal de programadores se basa en un sistema de administración de contenido o CMS. El encabezado que aparece en cada página es un tipo especial de contenido que se conoce como un widget. Para editar el contenido de dicha widget, haga clic en **Widgets** desde el menú de **Portal del desarrollador** de la izquierda y, a continuación, seleccione el widget de **encabezado** de la lista.

![Encabezado de widgets][api-management-widgets-header]

El contenido del encabezado es editable desde dentro del campo de **cuerpo** . Cambiar el texto "Fabrikam al Portal para desarrolladores" y, a continuación, haga clic en **Guardar** en la parte inferior de la página.

Ahora debería poder ver el nuevo encabezado en cada página en el portal de programadores.

> Para abrir el portal de programadores mientras está en el portal de publisher, haga clic en el **portal de programadores** en la barra superior.

## <a name="change-headers-styling"> </a>Modificar el estilo de los encabezados

Los colores, fuentes, tamaños, espaciados y otros elementos relacionados con el estilo de cualquier página en el portal están definidos por las reglas de estilo. Para modificar los estilos, haga clic en **apariencia** , en el menú de **portal de programadores** en el portal de publisher y, a continuación, haga clic en **comenzar la personalización** para habilitar al editor de estilo.

El explorador cambia a una página oculta en el portal de programadores que contiene ejemplos de contenido, con ejemplos para todas las reglas de estilo utilizadas en cualquier lugar en el sitio. Para abrir el editor de estilo, mueva el cursor sobre la línea vertical gris fina en la parte izquierda de la página. Debe aparecer la barra de herramientas del editor.

![Barra de herramientas de personalización][api-management-customization-toolbar]

Existen dos modos principales de edición de reglas de estilo - **Editar todas las reglas de** muestra una lista de todas las reglas de estilo que se utiliza en cualquier lugar, mientras que **Elija elemento** le permite seleccionar un elemento de la página que se encuentra en y muestra los estilos sólo para ese elemento.

En esta sección, queremos modificar el estilo de solo los encabezados. Haga clic en la opción **Seleccione el elemento** de la barra de herramientas del editor de estilo y, a continuación, haga clic en **Seleccionar un elemento para personalizar**. Elementos se convierten en resaltado mantener el mouse sobre ellos con el mouse para indicar los estilos de qué elemento comience edición si hizo clic en. Mueva el mouse sobre el texto que representa el nombre de la compañía en el encabezado ("Fabrikam Developer Portal" Si sigue las instrucciones de la sección anterior) y, a continuación, haga clic en él. Un conjunto de reglas de estilo con nombre y ordenados por categoría aparece en el editor de estilo.

Cada regla representa una propiedad de estilo del elemento seleccionado. Por ejemplo, para el texto del encabezado seleccionado anteriormente, el tamaño del texto es en @font-size-h1 mientras el nombre de la fuente con alternativas está en @headings-font-family.

> Si ya está familiarizado con [Inicio][], estas reglas son en realidad [menos variables][] en el tema de arranque utilizado en el portal de programadores.

Vamos a cambiar el color del texto del título. Seleccione la entrada en el **@headings-color** de campo y escriba **#000000**. Este es el código hexadecimal para el color negro. Cuando hace esto, verá que aparece un indicador de cuadrado de color al final del cuadro de texto. Si hace clic en este indicador, un selector de color permite elegir un color.

![Selector de color][api-management-customization-toolbar-color-picker]

Cuando haya terminado de realizar los cambios en los estilos del elemento seleccionado, haga clic en **Vista previa de cambios** para ver los resultados en la pantalla. En este momento, son visibles únicamente a los administradores. Para hacer visible para todos estos cambios, haga clic en el botón **Publicar** en el editor de estilo y confirme los cambios.

![Menú publicar][api-management-customization-toolbar-publish-form]

> Para cambiar las reglas de estilo que se aplican a cualquier otro elemento en la página, siga el mismo procedimiento que utilizó para el encabezado. Haga clic en **Seleccionar un elemento** desde el editor de estilo, seleccione el elemento que le interesa y modifique los valores de las reglas de estilo que se muestra en la pantalla de inicio.

## <a name="edit-page-contents"> </a>Modifica el contenido de una página

El portal de programadores consta de páginas generadas automáticamente como API, productos, aplicaciones, problemas y contenido escrito manualmente. Porque se basa en un sistema de administración de contenido, puede crear este tipo de contenido según sea necesario.

Para ver la lista de todas las páginas de contenido existentes, haga clic en **contenido** en el menú de **portal de programadores** en el portal de publisher.

![Administrar contenido][api-management-customization-manage-content]

Haga clic en la página de **bienvenida** para modificar lo que se muestra en la página principal del portal del programador. Realice los cambios que desee, obtener una vista previa si es necesario y, a continuación, haga clic en **Publicar ahora** para que sean visibles para todos los usuarios.

> La página de inicio utiliza un diseño especial que le permite mostrar una pancarta en la parte superior. Esta pantalla no es editable de la sección de **contenido** . Para editar esta pantalla, haga clic en **Widgets** desde el menú de **portal de programadores** , seleccione la **página de inicio** de la lista desplegable de **Capa actual** y, a continuación, abra el elemento de **título** en la **sección de portada**. El contenido de este widget es editable igual que cualquier otra página.

## <a name="next-steps"> </a>Pasos siguientes

-   Obtenga información sobre cómo personalizar el contenido de las páginas de portal de desarrollador con [plantillas del portal de desarrollador](api-management-developer-portal-templates.md).

[Change the text/logo in the page headers]: #change-page-headers
[Change the styling of the headers]: #change-headers-styling
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-portal/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-customize-portal/api-management-widgets-header.png
[api-management-customization-toolbar]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
[api-management-customization-manage-content]: ./media/api-management-customize-portal/api-management-customization-manage-content.png


[Inicio]: http://getbootstrap.com/
[MENOS variables]: http://getbootstrap.com/css/
