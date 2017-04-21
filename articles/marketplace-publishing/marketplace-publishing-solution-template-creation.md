<properties
   pageTitle="Guía para crear una plantilla de solución para el catálogo de soluciones | Microsoft Azure"
   description="Instrucciones detalladas sobre cómo crear, certificar e implementar una plantilla de solución de imagen entre varios VM para comprar en Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="07/27/2016"
      ms.author="hascipio; v-divte" />

# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Guía para crear una plantilla de solución de Azure Marketplace
Después de completar el paso 1, [creación de la cuenta y el registro][link-acct-creation], nos guiada por la creación de una plantilla de solución de Azure compatible en [requisitos previos técnicos para crear una plantilla de solución](marketplace-publishing-solution-template-creation-prerequisites.md)de. Ahora se le guiará por los pasos para crear una plantilla de solución para varios VM en el [Portal de publicación] [ link-pubportal] de Azure Marketplace.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>Crear su oferta de plantilla de la solución en el Portal de publicación
Vaya a [https://publish.windowsazure.com](http://publish.windowsazure.com). Al iniciar sesión por primera vez en el [Portal de publicación](https://publish.windowsazure.com/), use la misma cuenta con la que se registró el perfil de vendedor de su empresa. Más adelante, puede agregar a cualquier empleado de su empresa como un administrador de co en el Portal de publicación.

### <a name="1-select-solution-templates"></a>1. Seleccione "Plantillas de solución"

  ![dibujo][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. crear una nueva plantilla de solución

  ![dibujo][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. comenzar con topologías
Una plantilla de solución es "padre" a todos sus topologías. Puede definir varias topologías en una plantilla de oferta o solución. Cuando se inserta una oferta a ensayo, se inserta con todos sus topologías. Siga los pasos siguientes para definir la oferta:     

- Crear una topología: "Identificador de topología" suele ser el nombre de la topología de la plantilla de solución. El identificador de topología se usa en la dirección URL tal como se muestra a continuación:

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/ {PublisherNamespace} / {OfferIdentifier} {TopologyIdentifier}

  Portal de Azure: https://portal.azure.com/#gallery/ {PublisherNamespace}. {OfferIdentifier} {TopologyIdentifier}

- Agregar una nueva versión.

### <a name="4-get-your-topology-versions-certified"></a>4. obtener las versiones de topología certificadas
Cargar un archivo zip que contiene todos los archivos necesarios para proporcionar esa versión de la topología. Este archivo zip debe contener lo siguiente:

- archivo de *mainTemplate.json* y *createUiDefinition.json* en su directorio raíz.
- Cualquier vinculadas plantillas y todos los scripts.

  > [AZURE.TIP] Mientras los desarrolladores trabajan en crear topologías de plantilla de la solución y las obtiene certificados, el negocio, los departamentos de marketing o legales de su empresa pueden trabajar en el contenido de marketing y legal.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha creado la plantilla de solución y carga el archivo zip, siga las instrucciones de la [Guía de contenido de marketing de Marketplace](marketplace-publishing-push-to-staging.md) antes de enviar la oferta de ensayo. Para ver el conjunto completo de artículos de la publicación del catálogo de soluciones, visite [Introducción: cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md).

También es posible que le interese en estos artículos relacionados:

- Imágenes de máquina virtual: [Acerca de imágenes de máquina Virtual de Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)
- Extensiones VM: [agente de VM e Introducción a las extensiones VM](https://msdn.microsoft.com/library/azure/dn832621.aspx) y [extensiones de Azure VM y características](https://msdn.microsoft.com/library/azure/dn606311.aspx)
- [Ejemplos de plantillas de Simple ARM](https://github.com/rjmax/ArmExamples) y [ARM Azure habilitados](../resource-group-authoring-templates.md) Azure Administrador de recursos:
- Cuenta de almacenamiento limita: [cómo Monitor para el límite de cuenta de almacenamiento](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) y [almacenamiento Premium](../storage/storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
