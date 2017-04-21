# <a name="steps-to-follow-when-you-retire-or-change-the-name-of-an-acom-technical-article"></a>Pasos para seguir al retirar o cambiar el nombre de un artículo técnico de ACOM

Esta guía es para las PYME que aparecen como autor de un artículo que debe ser retirado de la sección de documentación técnica de azure.microsoft.com. Los pasos también se aplica si se cambia el nombre de un archivo.

Si es un miembro de nuestra comunidad de Azure y cree un artículo que se retirará por cualquier motivo, deje un comentario en la secuencia de comentario de Disqus para el artículo comunicar el autor que algo va mal con el artículo.

Autores SME tiene que seguir varios pasos para retirar correctamente el contenido para que los usuarios del sitio Web no tienen una mala experiencia cuando se retira contenido del sitio. Eliminar el artículo o cambiar su nombre debe ser lo último que ocurre!

## <a name="step-1-set-the-article-to-no-indexno-follow-and-republish-it-recommended"></a>Paso 1: Establecer el artículo no índice o no seguir y publicarlo (recomendado)

Lo primero que debe hacer es volver a publicar el artículo como no índice o no seguir unas semanas antes de eliminarlo realmente. Esto se considera el mejor práctica "trabajo previo" para retirar contenido. Realizar esta acción quita el artículo índices de motor de búsqueda para que personas no encontrará el artículo en la búsqueda. [Vea el wiki interno para obtener más detalles.](https://microsoft.sharepoint.com/teams/azurecontentguidance/wiki/Pages/Remove%20published%20pages%20and%20request%20redirects.aspx)

## <a name="step-2-manage-inbound-links-required"></a>Paso 2: Administrar vínculos entrantes (obligatorio)

Determinar si existen los vínculos entrantes no es de Microsoft a su contenido. Con frecuencia, blogs, foros y otro contenido en la web muestra artículos. Con frecuencia, puede trabajar con los propietarios del blog para cambiar estos vínculos y puede quitar o actualizar los vínculos de publicaciones de foro. Herramientas de análisis Web pueden saber si hay los vínculos entrantes tráfico alto posible que necesite administrar de esta manera.

## <a name="step-3-remove-all-crosslinks-to-the-article-from-the-technical-content-repository-required"></a>Paso 3: Quitar todos los vínculos cruzados el artículo del repositorio de contenido técnico (obligatorio)

No confiar en redireccionamientos ocuparse de vínculos cruzados de otros artículos. Actualizar o quitar la cruz hace referencia el artículo que va a eliminar o cambiar el nombre, incluidos en los artículos pertenecen a otras personas.

1. Asegúrese de que está trabajando con una rama local actualizada: ejecutar `git pull upstream master` (o la variación apropiada en este comando.

2.  Analizar la carpeta de azure-contenido-pr/artículos y la carpeta azure-contenido-pr/incluye cualquiera artículos e incluye dicho vínculo para el artículo que desea retirar y a continuación, quite los vínculos cruzados o reemplazarlas con un nuevo vínculos de cruzados adecuado. Puede utilizar una búsqueda y reemplace utilidad para buscar los vínculos cruzados si tiene uno instalado. Si no lo hace, puede usar Windows PowerShell de forma gratuita! Aquí le mostramos cómo usar PowerShell para buscar los vínculos cruzados:

 una. Inicie Windows PowerShell.

 b. En el símbolo del sistema de PowerShell, cambie a la carpeta pr\articles de contenido de azure:

 `cd azure-content-pr\articles`

 c. Escriba este comando, que se mostrará una lista de todos los archivos que contienen una referencia al artículo que va a eliminar:

 `Get-ChildItem -Recurse -Include *.md* | Select-String "<the name of the topic you are deleting>" | group path | select name`

  Si prefiere enviar la lista de nombres de archivo a un archivo de texto (en este psoutput.txt caso, con nombre), puede:

  `Get-ChildItem -Recurse -Include *.md* | Select-String "<the name of the topic you are deleting>" | group path | select name | Out-File C:\Users\<your account>\psoutput.txt`

3. Agregar y confirmar todos los cambios, insertarlos a su horquilla y crear una solicitud de extracción para mover los cambios de la bifurcación a la rama principal del repositorio principal.

## <a name="step-4-update-the-fwlink-tool-required"></a>Paso 4: Actualizar la herramienta de vínculo de redirección (obligatoria)

Compruebe la herramienta de vínculo de redirección para los vínculos que pueden señalar el artículo. Elija los vínculos a contenido de reemplazo. Si no está en el alias que posee el vínculo, unirse a ella. Si los propietarios no actualizarán el vínculo, archivo de un vale de MSCOM para que el vínculo ha cambiado. Más información: [wiki interno](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Manage%20inbound%20links%20to%20retired%20topics.aspx).

## <a name="step-5-remove-all-crosslinks-to-the-article-from-other-pages-on-azuremicrosoftcom-and-create-a-redirect-for-the-retired-page-if-appropriate-required"></a>Paso 5: Quitar todos los vínculos cruzados el artículo a otras páginas en azure.microsoft.com y crear una página de redirección de la página retirada, si adecuado (obligatorio)

Debe trabajar con la persona que mantiene y actualice la página de inicio de la documentación de su servicio de este elemento. Si no sabe quién es de esa persona, póngase en contacto con su asociado de contenido de grupo. La persona que mantiene y actualice la página de inicio del documento tendrán que hacer dos cosas:

1. En Visual Studio, observe **toda** la ACOM web solución para referencia cruzada con el archivo para retirar. Quitar las referencias cruzadas o reemplácelos con una referencia cruzada actualizada. Debe quitar los vínculos HTML, así como las cadenas de recursos relacionados para los vínculos HTML. Más información: ver el [wiki interno](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Create%20or%20edit%20a%20service%20landing%20page%20or%20left%20nav.aspx)

2. Si existe un artículo de reemplazo, cree una redirección. Más información: ver el [wiki interno](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Remove%20published%20pages%20and%20request%20redirects.aspx).

3. Comprobar los cambios en el repositorio.

## <a name="step-6-retire-the-article"></a>Paso 6: Retirar el artículo

Una vez haya completado los pasos anteriores y dichos cambios están activos, puede eliminar el artículo del repositorio. 

**Importante:** Cuando elimina un archivo, debe usar la `git add --all` comando.

## <a name="step-7-remove-links-from-msdn-required"></a>Paso 7: Quitar vínculos de MSDN (obligatorio)

Revise la herramienta de q & a contenido vínculos rotos al tema retirado o ha cambiado el nombre y quitar o corregir los vínculos en todos los temas MSDN afectados.

## <a name="step-8-remove-cached-pages-from-search-engines-only-if-absolutely-necessary"></a>Paso 8: Quitar páginas de la caché de motores de búsqueda (solo si es necesario absoluta)

Realice este solo si el contenido debe quitar rápidamente debido a problemas de cliente legal o graves. Según las mejores prácticas de Google, eliminaciones de página de prioridad normal solo deben corregirse procesos del motor de búsqueda natural. Vaya a estas páginas web quitar páginas web en caché de motores de búsqueda:

[Bing](https://www.bing.com/webmaster/tools/content-removal?rflid=1)
[Google](https://www.google.com/webmasters/tools/removals?pli=1)


### <a name="contributors-guide-links"></a>Vínculos de la Guía de colaboradores

- [Artículo de información general](./../README.md)
- [Índice de artículos de guía](./contributor-guide-index.md)
