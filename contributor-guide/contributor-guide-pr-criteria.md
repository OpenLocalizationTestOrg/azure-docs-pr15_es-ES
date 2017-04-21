# <a name="quality-criteria-for-pull-request-review"></a>Revisión los criterios de calidad de la solicitud de extracción

Estos criterios están pensados para los autores que crean y mantienen los artículos técnicos y para los revisores de la solicitud de extracción que revisión las solicitudes de extracción de contenido. Si la solicitud de extracción no cumple los requisitos para [la combinación automática](contributor-guide-pull-request-etiquette.md#in-a-hurry-submit-prs-that-can-be-accepted-automatically), será revisado por un revisor de solicitud de extracción humana. Extracción solicitud generalmente revisores solo novedades nuevas o modificadas. Extraer solicitud revisores evaluación los cambios en una solicitud de extracción según los elementos de revisión de calidad y no bloqueo mencionados en este artículo.

## <a name="blocking-content-quality-items"></a>Bloqueo de los elementos de la calidad del contenido

Las actualizaciones en la solicitud de extracción deben cumplir los siguientes criterios para combinar. Revisores de solicitud de extracción proporcionan comentarios de los comentarios de la solicitud de extracción para estos elementos y el tipo de `#hold-off` en la solicitud de extracción para volver a usted (el autor) con comentarios.

| Categoría | Elemento de la revisión de calidad |
|----------|---------------------|
|Requisitos previos| La "listo para-combinación" y las etiquetas "validación correcta" están asignadas a la PR.|
|Requisitos previos| La solicitud de extracción no puede estar bloqueada por un conflicto de combinación.|
|Integridad repo|    Solicitud de extracción no contiene retrocesos contenidos obvios.|
|Integridad repo|    Solicitud de extracción no incluye un repo incrustado o cualquier archivo extraño, extraño.|
|Integridad repo |Solicitud de extracción contiene menos de 100 archivos modificados a menos que PR deliberadamente está actualizando una bifurcación de la versión de patrón. (Realmente, un PR debe contener menos distancia que pero después de 100 archivos modificados, GitHub no muestre la diffs).|
|Los nombres |Nombres de archivo de los archivos nuevos sigan las [instrucciones de nomenclatura de archivos](file-names-and-locations.md).|
|Los nombres |Carpetas introducen en el repo, siga las [instrucciones de nomenclatura de carpeta](file-names-and-locations.md#folder-names-in-the-repo).|
|Contenido    |El artículo es un documento técnico y por tanto en el canal de contenido correcto. Consulte la [destino de las instrucciones](content-channel-guidance.md).|
|Contenido    |El asunto en el documento técnico es adecuado para un artículo técnico. Consulte la [destino de las instrucciones](content-channel-guidance.md).|
|Contenido    |El artículo contiene un párrafo de introducción y un bloque de procedimientos o conceptual de contenido. El artículo debe contener contenido completo suficiente para destacar su propia como un artículo. No debe ser un pequeño fragmento de información. (Excepción: un tema "Límites" si se encuentra en el contexto de un artículo de gran tamaño que enumera todos los límites de un servicio.)|
|Contenido| Elementos que deben listas numeradas se numeran, elementos que deben ser desordenados listas con viñetas. Se trata de facilidad de uso básico.|
|Contenido| Inusuales o nuevos gráficos, arquitectura de la información o estructuras o diseños evidentes no estándar que necesite ser puestas a prueba con el revisor PR de cliente potencial. Equipos que están experimentando con cosas nuevas deben tener un plan o lienzo y solución de problemas en lugar de evaluación de experimentación.|
|Diseño del sitio y funcionalidad| Conmutadores sólo se utilizan para cambiar entre varias versiones del mismo artículo.|
|Diseño del sitio y funcionalidad| Los títulos de los artículos de switchered contienen información que diferencia cada artículo desde otros artículos en el conjunto de switchered.|
|Diseño del sitio y funcionalidad| No se permite creados manualmente TDC. El artículo debe confiar en H2s para su tabla de contenido en la página.|
|Diseño del sitio y funcionalidad| Si H2 encabezados están presentes, el artículo contiene al menos dos encabezados de H2. Utilizar un encabezado H2, crea un artículo de un solo elemento TDC. H2 encabezados se deben utilizar antes H3 títulos para asegurarse de que se crea una tabla de contenido.|
|Descuento| HTML: Contenido de origen no contiene HTML en el nivel de bloque – menor en línea está permitido HTML: por ejemplo, superíndice, subíndice, caracteres especiales y otras cosas secundarias que no se puede realizar con descuento. Tablas HTML permitidas solo si la tabla contiene listas con viñetas o numeradas, pero que normalmente es una indicación que el contenido debe ser simplificado para que el origen puede programarse en descuento.|
|Descuento   |Elementos de descuento personalizado se utilizan donde corresponda. Ex: Notas están codificadas mediante la AZURE. Extensión de nota, no como texto sin formato.|
|SEO    |La "& #124; Se requiere Microsoft Azure"identificador de sitio|
|SEO    |El título de H1 contiene suficiente información para describir el contenido de este artículo, para diferenciarlo de otros artículos de Azure y asignar a las palabras clave de cliente es muy probable que. Por ejemplo, "Descripción general" como título H1 es un error.|
|Terminología| El uso del BRAZO acrónimo, V1 o V2 como referencias a clásico y modelos de implementación de administrador de recursos es un elemento de la terminología de bloqueo.|
|Imágenes |GIF animados no se aceptan en el repo.|
|Imágenes | Imágenes tienen resolución de borrar, libres de las palabras mal escritas y no contienen ninguna información privada | 
|Ensayo|La vista previa del artículo debe estar limpia en ensayo. No puede contener los problemas de formato obvios: <br><br>-Una lista numerada o con viñetas que aparece como un párrafo <br> -Código de un bloque de código aparecen parcialmente en el bloque de código y parcialmente fuera de la misma <br>-Pasos numerados número incorrecto debido a defectuoso sangría|

## <a name="non-blocking-content-quality-items"></a>Sin bloqueo elementos de calidad de contenido

Para estos elementos, los revisores de solicitud de extracción proporcionar comentarios e instrucciones para su seguimiento con revisiones en una solicitud de extracción posterior al autor. Sin embargo, este comentario bloquear la decisión de combinar. Autores deben realizar un seguimiento en 3 días laborables con correcciones.

| Categoría | Elemento de la revisión de calidad |
|----------|---------------------|
|Contenido|Artículos deberían tener un "pasos siguientes" al final con los siguientes pasos de 1 a 3 relevantes y convincentes. Se debe incluir texto breve que permite a los clientes a entender por qué los pasos siguientes son relevantes. (Artículos nuevos sólo). Ejemplo: <https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-install/><br>![](media/contributor-guide-pr-criteria/nextstepsexample.PNG)|
|Contenido|Ortografía, gramática y otros problemas de escritura: revisores de solicitud de extracción pueden proporcionar comentarios sobre algunos problemas poco importantes sin bloqueo comentarios. Si hay más de algunos problemas editoriales, revisores iniciar una solicitud de edición para el artículo para una edición posterior a la publicación.|
|Imágenes|Imágenes usan el estilo de llamada correcta y el color y capturas de pantalla utilicen el estilo de borde y el marcador de posición correcto. [Consulte la Guía de la imagen](https://github.com/Azure/azure-content/blob/master/contributor-guide/create-images-markdown.md).|
|Imágenes|Imágenes de incluyen texto alternativo. [Consulte la Guía de la imagen](https://github.com/Azure/azure-content/blob/master/contributor-guide/create-images-markdown.md).|
|Diseño del sitio y funcionalidad|Los encabezados de H2, cuando se representa en la tabla de contenido en la página, lo ideal es que deberían ajustar a no más de 2 líneas. Más encabezados dificultar el artículo de la tabla de contenido analizar.|
|Convenciones de estilo|Todos los títulos y los títulos son tipo oración, por el estilo de Azure.|
|Proceso|Si la solicitud de extracción se han fácilmente volvió a para beneficiarse de automatización PRmerger, revisores de solicitud de extracción proporcionan comentarios al autor sobre cómo usar ramas para que los cambios se combinarán automáticamente. Vea [el artículo de la etiqueta de PR](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-pull-request-etiquette.md#in-a-hurry-submit-prs-that-can-be-accepted-automatically).|
|Proceso|Al eliminar o cambiar el nombre de un artículo, asegúrese de que seguir el proceso. Extraer solicitud revisores deben agregar el siguiente comentario y el vínculo en un comentario:<br><br>*Compruebe que ha seguido del proceso de guía de los colaboradores para eliminar artículos: <https://github.com/Azure/azure-content/blob/master/contributor-guide/retire-or-rename-an-article.md> .*|

## <a name="related"></a>Relacionados

- [Etiqueta de la solicitud de extracción y los procedimientos recomendados para colaboradores de Microsoft](contributor-guide-pull-request-etiquette.md)

- [Automatización de comentario de solicitud de extracción](contributor-guide-pull-request-comments.md)
