<properties 
    pageTitle="Recursos relacionados y vinculados en la Galería de mosaico" 
    description="Obtenga información sobre recursos relacionados y vinculados que se muestran en la Galería de mosaico del portal de vista previa de Azure." 
    services="azure-portal" 
    documentationCenter="" 
    authors="adamabdelhamed" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="azure-portal" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/16/2015" 
    ms.author="adamab"/>

# <a name="related-and-linked-resources-in-the-tile-gallery"></a>Recursos relacionados y vinculados en la Galería de mosaico

La Galería de mosaico le permite buscar mosaicos para un recurso determinado y arrástrelos hasta el módulo actual. Uso de la Galería de mosaico, puede crear vistas de administración que abarcan recursos. Para cualquier recurso especificado, los recursos relacionados incluyen todos los recursos que comparten el mismo grupo de recursos que el recurso y los recursos que se vinculan a o desde el recurso.

## <a name="linked-resources-in-azure-resource-manager"></a>Recursos vinculados en el Administrador de recursos de Azure

La vinculación es una característica del Administrador de recursos de Azure.  Le permite declarar las relaciones entre los recursos, incluso si no residen en el mismo grupo de recursos. Vinculación tiene ningún efecto en el tiempo de ejecución de los recursos, sin impacto en facturación y sin impacto en el acceso basado en roles.  Es simplemente un mecanismo que puede usar para representar relaciones para que las herramientas como la Galería de mosaico puede proporcionar una administración enriquecido experiencia.  Las herramientas pueden inspeccionar los vínculos con la API de vínculos y proporcionar experiencias de administración de relación personalizada también. 

## <a name="how-do-i-link-my-resources"></a>¿Cómo puedo vincular Mis recursos?

Cuando crea recursos a través del portal o mediante la implementación de una plantilla a través de Azure PowerShell o CLI de Azure, se crean automáticamente vínculos para algunos recursos dependientes. Puede vincular recursos mediante programación utilizando la [API de REST de recursos vinculado](https://msdn.microsoft.com/library/azure/mt238499.aspx) o declarar las relaciones en la plantilla. Para obtener más información sobre cómo trabajar con recursos vinculados, vea [vinculación de recursos en el Administrador de recursos de Azure](../resource-group-link-resources.md).

## <a name="next-steps"></a>Pasos siguientes

- Si necesita una introducción sobre cómo escribir plantillas de administrador de recursos de Azure, consulte [plantillas de edición](../resource-group-authoring-templates.md).
- Para profundizar más en detalle sobre la creación de vínculos entre recursos, vea [recursos de vinculación en el Administrador de recursos de Azure](../resource-group-link-resources.md).
- Para conocer más acerca de cómo trabajar con grupos de recursos a través del portal de vista previa, vea [con el Portal de vista previa de Azure para administrar los recursos de Azure](resource-group-portal.md).
