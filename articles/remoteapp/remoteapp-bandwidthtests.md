<properties 
    pageTitle="Azure RemoteApp - probar el uso de ancho de banda de red con algunos escenarios comunes | Microsoft Azure"
    description="Obtenga información sobre ¿qué escenarios comunes de uso que pueden ayudarle a calcular las necesidades de ancho de banda de red para RemoteApp de Azure."
    services="remoteapp"
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />
    
# <a name="azure-remoteapp---testing-your-network-bandwidth-usage-with-some-common-scenarios"></a>Azure RemoteApp - probar el uso de ancho de banda de red con algunos escenarios comunes

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Como se ha comentado en [estimación Azure RemoteApp el uso de ancho de banda de red](remoteapp-bandwidth.md), las pruebas de la mejor manera de saber cuál es el impacto de Azure RemoteApp a su red ejecutar algunas uso. Ejecutar estas pruebas durante un período de tiempo establecido y medir el ancho de banda necesario para cada escenario. Si tiene la capacidad de, también puede medir la variación de red y pérdida de paquetes de red para conocer los patrones de red que se creará en su entorno específico.

    
Al evaluar el uso de ancho de banda, recuerde que uso varía entre diferentes usuarios dentro de su empresa. Por ejemplo, los autores y los lectores de texto normalmente consuman menos ancho de banda que los usuarios que trabajen con vídeo. Para obtener los mejores resultados, estudie sus necesidades de usuario y crear una combinación de las siguientes situaciones que representa mejor los usuarios de su compañía. No olvide [Revisar los factores que experiencia de usuario y el uso de ancho de banda de impacto](remoteapp-bandwidthexperience.md) - que le sirva para identificar las pruebas ideales.

En primer lugar, lea acerca de las pruebas, elija su combinación y, a continuación, ejecutarlos. Puede usar la tabla siguiente para ayudar a realizar un seguimiento del rendimiento.

>[AZURE.NOTE] Si no puede realizar sus propias pruebas de red, o no tiene tiempo para ello, consulte nuestras [estimaciones/recomendaciones de ancho de banda de red básica](remoteapp-bandwidthguidelines.md). Sin embargo, puede variar el kilometraje por lo que *puede* ejecutar sus propias pruebas, que debería.


## <a name="the-usage-tests"></a>Las pruebas de uso
Cada una de estas pruebas ejecutar para diferentes cantidades de tiempo y probar diferentes funciones y características que consumen ancho de banda de red. Recuerde que debe elegir la combinación de prueba que mejor adapte a los usuarios individuales de la empresa.
 
### <a name="executivecomplex-powerpoint---run-for-900-1000-seconds"></a>PowerPoint ejecutivo/complejo: ejecutar 1000 900 segundos

Un usuario se presenta entre 45 y 50 diapositivas de alta fidelidad mediante Microsoft Office PowerPoint en modo de pantalla completa. Las diapositivas deben contener imágenes, transiciones (con animaciones) y fondos de degradado de color normal para su empresa. El usuario debe gastar al menos 20 segundos en cada diapositiva.
    
Este escenario crea tráfico ráfagas, cuando pasa de una diapositiva a la siguiente diapositiva de la presentación.
    
### <a name="simple-powerpoint---run-for-620-seconds"></a>PowerPoint simple: ejecutar para ~ 620 segundos

Un usuario presenta un archivo de PowerPoint sencillo con aproximadamente 30 diapositivas mediante Microsoft Office PowerPoint en modo de pantalla completa. Las diapositivas están más con mucho texto que en el caso de PowerPoint ejecutivo/complejo y más sencillo fondos e imágenes (diagramas negro). 
    
### <a name="internet-explorer---run-for-250-seconds"></a>Internet Explorer - ejecutar para ~ 250 segundos

Un usuario navega por Internet con Internet Explorer. El usuario examina y se desplaza por una mezcla de texto, imágenes naturales y algunos diagramas de esquemas. Las páginas web que se almacena en la unidad de disco local del servidor de escritorio remoto (RD Session Host) como un. Archivo MHT. El usuario se desplaza con RE PÁG, AV PÁG, hacia arriba y hacia abajo de teclas, con distintos intervalos para cada clave y el tipo de desplazamiento:
    
    - Hacia abajo - 250 pulsaciones muy 500 ms.
    - RE PÁG - 36 pulsaciones de teclas cada 1000 ms
    - Abajo - 75 pulsaciones de teclas cada 100 ms
    - AV PÁG - 20 pulsaciones de teclas cada 500 ms
    - Arriba - 120 pulsaciones cada 300 ms
    
### <a name="pdf-document---simple---run-for-610-seconds"></a>Documento PDF - simple: ejecutar para ~ 610 segundos
Un usuario lee y busca un documento PDF de varias maneras usando Adobe Acrobat Reader. El documento debe constar de varias fuentes de texto, tablas y gráficos sencillos. El documento es de 35-40 páginas. El usuario se desplaza hacia atrás dos tipos diferentes y reenvía cuatro tamaños de zoom diferente (ajustar a la página, que se ajuste al ancho, 100% y otro de su elección). El zoom garantiza que representa el texto (fuente) de diferentes tamaños. Es el desplazamiento por medio de RE PÁG, AV PÁG, hacia arriba y hacia abajo de teclas, con distintos intervalos para cada desplazamiento.

### <a name="pdf-document---mixed---run-for-320-seconds"></a>Ejecutar - mixto - para ~ 320 segundos de documentos PDF
Un usuario lee y busca un documento PDF de varias maneras usando Adobe Acrobat Reader. El documento consta de imágenes de alta calidad (incluidos fotografías), tablas, gráficos sencillos y varias fuentes de texto. El usuario se desplaza hacia atrás dos tipos diferentes y reenvía cuatro tamaños de zoom diferente (ajustar a la página, que se ajuste al ancho, 100% y otro de su elección). El zoom garantiza que representa el texto (fuente) de diferentes tamaños. Es el desplazamiento por medio de RE PÁG, AV PÁG, hacia arriba y hacia abajo de teclas, con distintos intervalos para cada desplazamiento.

### <a name="flash-video-playback---run-for-180-seconds"></a>Reproducción de vídeo Flash - ejecutar para ~ 180 segundos
Un usuario ve un vídeo de Adobe Flash codificada incrustado en una página web. La página web se almacena en el disco duro local del servidor Host de sesión de escritorio remoto. El vídeo se reproduce en Internet Explorer por un complemento de Reproductor incrustado.

Este escenario emula a los usuarios ven contenido las páginas web enriquecidas con multimedia. La mayoría de los datos debe a bo a través de VOBR.

### <a name="word-remote-typing---run-for-1800-seconds"></a>Escribir remoto de Word: ejecutar para ~ 1800 segundos
Un usuario escribe un documento a través de una sesión RDP. Pulsaciones de teclas se envían desde el cliente a través de la sesión RDP a un documento de Microsoft Word ejecutando en la sesión remota. La velocidad de escritura es un carácter cada 250 ms (total 7050 caracteres). 

Esta es una de las situaciones más comunes para los trabajadores de la información. Este escenario comprueba la capacidad de respuesta de un usuario escribe en un procesador de trabajo moderna. Este escenario es sensible a incluso los pequeños cambios en el uso de ancho de banda.

## <a name="tracking-the-test-results"></a>Seguimiento de los resultados de la prueba.

Puede usar la siguiente tabla para evaluar los escenarios en su entorno. Los datos que se proporcionan a continuación están únicamente para ilustración: puede ser considerablemente diferente de lo que observan. 

Para simplificar, se supone que todos los escenarios se probó con la misma resolución de pantalla de 1920 x 1080 píxeles y transportes TCP en una red con latencia (retardo) debajo de red y 200 ms vibración en la marca de 120 ms + del 1%.

Acerca de la tabla:
- **Experiencia promedio** contiene el ancho de banda de red donde no está afectada notablemente la productividad del usuario, pero no excluir ocasionales problemas de audio o vídeo. El sistema es capaz de recuperar rápidamente aprovechando la lógica dinámica. El intento de las estimaciones del ancho de banda de red para garantizar la calidad de la experiencia del usuario.
 - **Noticeable problemas (punto de interrupción)** contiene el ancho de banda de red donde los usuarios pueden observar problemas importantes en su experiencia y su productividad se ve afectada para períodos de tiempo mensurables. En este momento los algoritmos RDP se esfuerza por y no pueden garantizar la calidad de experiencia del usuario debido a suficiente ancho de banda.
 - **Se recomienda** contiene el ancho de banda de red recomendado para buena o excelente experiencia. Es normalmente un paso mayor que el valor en la columna correspondiente de la **experiencia de promedio** .
 - **Notas** incluyen observaciones y comentarios.
 
| Prueba                  | Experiencia promedio | Problemas evidentes (punto de interrupción) | Ancho de banda de red recomendada | Notas                                                              |
|-----------------------|--------------------|---------------------------------|-------------------------------|--------------------------------------------------------------------|
| PPT ejecutivo y complejas | 10 MB/s             | 1 MB/s                           | > 10 MB/s, 100 MB/s preferido    | A 1 MB/s se pierden muchas animaciones                                   |
| PPT simple            | 5 MB/s              | 256 KB/s                         | 10 MB/s                        | 256 KB/s las diapositivas cargan con retraso significativo                   |
| Internet Explorer     | 10 MB/s             | 1 MB/s                           | > 10 MB/s, 100 MB/s preferido    | A 1 MB/s, vídeos web son borrosos y rápidamente, con interrupciones desplazamiento tiene problemas |
| PDF simple            | 1 MB/s              | 256 KB/s                         | 5 MB/s                         | A 256 KB/s tardará un rato en cargar la página                       |
| PDF mixto             | 1 MB/s             | 256 KB/s                         | 5 MB/s                         | A 256 KB/s tarda mucho tiempo de carga de la página    |
| Reproducción de vídeo de Flash  | 10 MB/s             | 1 MB/s                           | > 10 MB/s, 100 MB/s preferido    | A 1 MB/s es grano el vídeo y se eliminan algunas tramas           |
| Escribir remoto de Word    | 256 KB/s            | 128 KB/s                         | 1 MB/s                         | A 256 KB/s usuario quizás observe el tiempo entre pulsaciones de teclas             |

Para evaluar el ancho de banda de red por usuario, cree una combinación de los casos anteriores y la parte correspondiente de ancho de banda de red requerido. Seleccione el número más alto que es necesario para los escenarios. Puesto que los usuarios no utilizan casi nunca el sistema por sí sola, considere la posibilidad de algunos reserva para los usuarios que trabajar simultáneamente en la misma red.
     
## <a name="learn-more"></a>Aprende más
- [Calcular el uso de ancho de banda de red de RemoteApp de Azure](remoteapp-bandwidth.md)

- [¿Azure RemoteApp - cómo ancho de banda de red y la calidad de la experiencia de trabajo juntos?](remoteapp-bandwidthexperience.md)

- [Azure RemoteApp ancho de banda - directrices generales (si no puede probar su propio)](remoteapp-bandwidthguidelines.md)