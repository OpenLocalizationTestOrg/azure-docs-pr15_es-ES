<properties
    pageTitle="Crear imágenes en descuento"
    description="Se explica cómo crear imágenes en descuento según las directrices establecidas para los repositorios de Azure."
    services=""
    solutions=""
    documentationCenter=""
    authors="kenhoff"
    manager="ilanas"
    editor="tysonn"/>

<tags
    ms.service="contributor-guide"
    ms.devlang=""
    ms.topic="article"
    ms.tgt_pltfrm=""
    ms.workload=""
    ms.date="06/25/2015"
    ms.author="kenhoff" />

# <a name="create-images-in-markdown"></a>Crear imágenes en descuento

## <a name="image-folder-creation-and-link-syntax"></a>Sintaxis de creación y vínculo de la carpeta de imagen

Para un nuevo artículo, debe crear una carpeta en la siguiente ubicación:

    /articles/<service-directory>/media/<article-name>/

Por ejemplo:

    /articles/app-service/media/app-service-enterprise-multichannel-apps/

Después de crear la carpeta y las imágenes agregadas a él, use la siguiente sintaxis para crear imágenes en su artículo:

```
![Alt image text](./media/article-name/your-image-filename.png)
```
Ejemplo:

Vea [la plantilla de descuento](../markdown%20templates/markdown-template-for-new-articles.md) para obtener un ejemplo.  Los vínculos de referencia de la imagen en esta plantilla de descuento están diseñados para ser en la parte inferior de la plantilla.

## <a name="guidelines-specific-to-azuremicrosoftcom"></a>Directrices específicas de azure.microsoft.com

Capturas de pantalla actualmente se recomienda si no es posible incluir pasos de reproducción. Escribir el contenido para que el contenido se puede sustituir sin las capturas de pantalla si es necesario.

Utilice las siguientes directrices al crear y archivos de imágenes prediseñadas, incluidos:
- No compartir archivos de imágenes prediseñadas en los documentos. Copie el archivo que necesita y agregarlos a la carpeta de medios para el tema específico. No se recomienda compartir entre archivos porque es fácil de quitar descartado contenido y las imágenes que se mantiene la repo limpiar.

- Formatos de archivo: usar archivos PNG - están mejor calidad y mantener su calidad durante el proceso de localización. Otros formatos de archivo no mantienen también su calidad. El formato .jpeg está permitido, pero no preferido.  No hay archivos GIF animados.

- Usar un cuadrado rojo del ancho predeterminado que se proporcionan en Paint (5 px) para llamar la atención sobre determinados elementos en capturas de pantalla.  

    Ejemplo:

    ![Este es un ejemplo de un cuadrado rojo que se usa como una llamada.](./media/create-images-markdown/gs13noauth.png)

- Cuando tiene sentido, no dude en Recortar imágenes para que los elementos de interfaz de usuario se mostrará en tamaño completo. Asegúrese de que el contexto está desactivado para los usuarios, aunque.

- Evite los espacios en blanco de bordes de capturas de pantalla. Si recortar una captura de pantalla de modo que deja el fondo blanco en los bordes, agregar un borde de un píxel gris alrededor de la imagen.  Si usa Paint, utilice el gris más claro en la paleta de colores predeterminado (0xC3C3C3). Si utiliza algunas otras aplicaciones de gráficos, el color RGB es R195, G195, 195. Puede agregar fácilmente un borde gris alrededor de una imagen en Visio: para ello, seleccione la imagen, seleccione línea y garantizar la se establece el color correcto y, a continuación, cambie el grosor de línea a 1 1/2 pto.  Capturas de pantalla deberían tener un borde de color gris 1 píxeles de ancho para que las áreas blancas de la captura de pantalla no desenfoque en la página web.

    Ejemplo:

    ![Este es un ejemplo de un borde gris alrededor de un espacio en blanco.](./media/create-images-markdown/agent.png)
    
    Para que una herramienta ayudar a automatizar el proceso de agregar el borde necesario a imágenes, vea [AddACOMBorder herramienta - cómo automatizar el proceso de agregar el borde de 1 píxel necesarios gris imágenes ACOM](https://github.com/Azure/Azure-CSI-Content-Tools/tree/master/Tools/AddACOMImageBorder).

- Imágenes conceptuales con espacios en blanco no es necesario un borde gris.  

    Ejemplo:

    ![Este es un ejemplo de una imagen conceptual con espacios en blanco y sin borde gris.](./media/create-images-markdown/ic727360.png)

- No intente hacer una imagen demasiado amplia.  Las imágenes se ajustará automáticamente si son demasiado amplia. Sin embargo, a veces el cambio de tamaño hace tolerancia, por lo que recomendamos limitar el ancho de las imágenes a 780 px y manualmente las imágenes de tamaño antes de su envío si es necesario.

- Mostrar resultados de comando en capturas de pantalla.  Si su artículo incluye pasos donde el usuario está trabajando en un shell, es útil mostrar el resultado del comando en capturas de pantalla. En este caso, restringir el ancho de shell a aproximadamente 72 caracteres generalmente garantiza que la imagen permanecerá en el criterio de ancho px 780. Antes de tomar una captura de pantalla de salida, cambiar el tamaño de la ventana de modo que se muestre la comando relevante y el resultado (opcionalmente, con una línea en blanco a ambos lados).

- Tomar todo capturas de pantalla de windows cuando sea posible. Al tomar una captura de pantalla de una ventana del explorador, cambiar el tamaño de la ventana del explorador a 780 px o menos de ancho y mantener la altura de la ventana del explorador como breve como sea posible tal que quepa la aplicación en la ventana.

    Ejemplo:

    ![Este es un ejemplo de una captura de pantalla de ventana de explorador.](./media/create-images-markdown/helloworldlocal.png)

- Tenga cuidado con la información que se muestra en capturas de pantalla.  No mostrar información de la compañía interna o información personal.

- Arte conceptual o diagramas, utilice los iconos oficiales en la nube y Enterprise símbolo y conjunto de iconos. Un conjunto público está disponible en http://aka.ms/CnESymbols.

- Reemplazar información personal o privada en capturas de pantalla con texto de marcador de posición entre corchetes angulares. Esto incluye los nombres de usuario, los identificadores de suscripción y otra información relacionada. Nombres de personas pueden sustituirse con un [aprobado nombre fictious](https://aka.ms/ficticiousnames)(vínculo solo empleado). No use la punta del lápiz o marcador en Paint para ocultar o desenfoque información personal o privada.

  La siguiente imagen se ha actualizado correctamente para reemplazar el **identificador de suscripción** de real con la información de marcador de posición:

  ![Información privada reemplazado con marcador de posición](./media/create-images-markdown/placeholder-in-screenshot-correct.png)

### <a name="contributors-guide-links"></a>Vínculos de la Guía de colaboradores

- [Artículo de información general](./../README.md)
- [Índice de artículos de guía](./contributor-guide-index.md)
