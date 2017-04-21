# <a name="pull-request-etiquette-and-best-practices-for-microsoft-contributors-to-azure-documentation"></a>Extraer la etiqueta de la solicitud y los procedimientos recomendados para colaboradores de Microsoft Azure documentación

Para publicar los cambios en la documentación, envíe solicitudes de extracción de la horquilla. Cada solicitud de extracción debe revisarse antes de que se combinan. Lea este artículo para comprender cómo debe trabajar con los revisores de solicitud de extracción y cómo puede crear solicitudes de extracción son más fácil y rápido revisar para que la cola de solicitudes de extracción funciona mejor para todos los usuarios.

## <a name="working-with-pull-request-reviewers"></a>Trabajar con los revisores de solicitud de extracción

Aquí tiene los conceptos básicos que debe saber sobre cómo trabajar con los revisores de solicitud de extracción. 

- <b>Comprender la función del revisor de solicitud de extracción. El revisor:</b>
  - Garantiza la calidad básica del contenido
  - Impide que reducciones en el repositorio
  - Proporciona comentarios antes de combinar

  Revisores de solicitud de extracción se encuentran en una función de control de contenido. La finalidad principal no es simplemente combinar todo lo que se envía lo antes posible. Espere comentarios que requieren que se va a realizar actualizaciones, especialmente para los artículos nuevos y revisados intensamente.

- <b>Planificar con antelación con el revisor de solicitud de extracción:</b>
  - Para las solicitudes de extracción de alta prioridad
  - Solicitudes de extracción lanzamientos sincronizó de fecha
  - Solicitudes de extracción que cambiarán o agregar una gran cantidad de archivos

- <b>SLA extracción solicitar revisión</b>

  En el repositorio privado, cada vez que su solicitud de extracción entra en la cola de solicitud de extracción con la etiqueta listo para combinar, el equipo intenta revisar la solicitud de extracción dentro de 12 horas de trabajo (lunes a viernes, 8 AM a 5 PM) y proporcionar comentarios o combinar si se requiere ningún comentario. Este acuerdo se aplica a la ley de revisar la PR, no la combinación. Se combinarán pr cuando se cumplen [los criterios para la combinación](contributor-guide-pr-criteria.md). 

## <a name="make-the-pull-request-queue-work-better-for-everyone"></a>Hacer que la cola de solicitudes de extracción funcionan mejor para todos los usuarios

Hay dos realidades básicas en la cola de PR:

- Las solicitudes de extracción que son pequeños en el ámbito y que contienen cambios muy similares tardar menos tiempo para revisar. 
- Las solicitudes de extracción que están grande en el ámbito o que contienen tipos diferentes, mezcla de cambios llevar más tiempo para revisar.

Puede ayudar a que la cola de solicitud de extracción funcionan mejor siguiendo estos procedimientos recomendados:

- Actualizaciones secundarias independientes artículos existentes de artículos nuevos o reescribe principales. Trabajar en estos cambios en ramas distintas de trabajo. 

- Al eliminar artículos o imágenes, no mezclar las eliminaciones con contenido incorporaciones o actualizaciones. Gestionar el contenido nuevo o cambios en una rama trabajo independiente.

- Para las versiones o refactorización del contenido, planear con antelación con el revisor PR. Puede que tenga su ayuda para crear una bifurcación de la versión o para coordinar tiempos de combinación de correspondencia con los tiempos de publicación para que su contenido se publica en el momento adecuado.

- Si está intentando coordinar las actualizaciones realizadas en el repo ACOM (es decir, cambios de navegación izquierdo, mapas de aprendizaje, redireccionamientos o páginas de inicio) con los cambios que se realiza en el repositorio de pr de contenido de azure, debe coordinar ese trabajo por adelantado con el revisor PR. En caso contrario, corre el riesgo de tener una gran cantidad de vínculos rotos.

## <a name="criteria-for-expedited-pull-requests"></a>Criterios para las solicitudes de extracción urgente

- Póngase en contacto con azdocprs para acelerar pr cuando estrictamente necesarias. Puede solicitar acelerado PR tratamiento de zona rojo, privacidad, legal y problemas de seguridad; para la experiencia del cliente realmente roto; y para ejecutivos extensiones. 
- Contenido para las versiones de característica no cumple los requisitos para el control urgente: contenido de la versión de característica requiere una planificación previa o se debe administrar mediante la cola de prioridad estándar.


## <a name="in-a-hurry-submit-prs-that-can-be-accepted-automatically"></a>¿Tiene prisa? Enviar pr que se aceptan automáticamente

Usar las reglas de automatización PRMerger para obtener más información de su día a día pr combinan automáticamente.

PRMerger puede aceptar la PR automáticamente, si:
* Afecta a 10 archivos o menos.
* Contiene 15 confirmaciones o menos.
* Inferior al 20% de los cambios de texto.
* No se actualizan selectores/conmutadores.
* No se archivos eliminados o agregados.
* No hay imágenes son nuevas, modificado o eliminado.

Si la solicitud de extracción no cumple estos criterios, la etiqueta "No se puede combinar PRmerger" se asigna automáticamente para que sepa que requiere revisión por un revisor PR humano.

### <a name="need-to-make-a-lot-of-little-changes"></a>¿Necesita realizar una gran cantidad de pequeños cambios?

Tomar la referencia de las reglas de automatización PRMerger anteriores y realice lo siguiente:
* Enviar artículos con los cambios de luz juntos en un PR con archivos de 10 o menos.
* Crear un PR independiente para artículos en qué imágenes o selectores de cambio. Esto requiere revisión humana.
* Crear un PR independiente para artículos nuevos o eliminados. Esto requiere revisión humana.

## <a name="related"></a>Relacionados

- [Revisión los criterios de calidad de la solicitud de extracción](contributor-guide-pr-criteria.md)

- [Automatización de comentario de solicitud de extracción](contributor-guide-pull-request-comments.md)
