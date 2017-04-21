<properties
   pageTitle="Soluciones de la serie de administración de operaciones (OMS) | Microsoft Azure"
   description="Soluciones amplían la funcionalidad de conjunto de aplicaciones de administración de operaciones (OMS) proporcionando escenarios de administración empaquetado de los clientes pueden agregar a su área de trabajo OMS.  Este artículo proporciona detalles sobre cómo personalizadas soluciones creadas por los clientes y socios."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="bwren" />

# <a name="management-solutions-in-operations-management-suite-oms-preview"></a>Soluciones de administración de operaciones Management Suite (OMS) (vista preliminar)

>[AZURE.NOTE]Esta es una documentación preliminar para soluciones de administración de OMS que actualmente están en la vista previa.    

Soluciones de administración de amplían la funcionalidad de conjunto de aplicaciones de administración de operaciones (OMS) proporcionando escenarios de administración empaquetado de los clientes pueden agregar a su entorno.  Además de [soluciones proporcionan por Microsoft](../log-analytics/log-analytics-add-solutions.md), socios y clientes pueden crear soluciones de administración para usarlas en su propio entorno o a los clientes a través de la Comunidad.

## <a name="finding-and-installing-management-solutions"></a>Buscar e instalar las soluciones de administración
Hay varios métodos para buscar e instalar las soluciones de administración, como se describe en las secciones siguientes.

### <a name="azure-marketplace"></a>Azure Marketplace
Soluciones de administración proporcionan por Microsoft y socios de confianza se pueden instalar desde el catálogo de soluciones de Azure en el portal de Azure.

1. Inicie sesión en el portal de Azure.
2. En el panel izquierdo, seleccione **más servicios**.
3. Desplácese hacia abajo hasta **soluciones** o escriba *soluciones* en el cuadro de diálogo **filtro** .
4. Haga clic en el botón **+ Agregar** .
5. Buscar soluciones que le interesa de exploración, haga clic en el botón **filtro** o escribiendo en el cuadro de **Búsqueda todo** .
6. Haga clic en un elemento del catálogo de soluciones para ver su información detallada.
4. Haga clic en **crear** para abrir el panel **Agregar solución** .
5. Se le pedirá que la información requerida, como el [área de trabajo OMS y cuenta con la automatización](#oms-workspace-and-automation-account) además de los valores de los parámetros de la solución.
6. Haga clic en **crear** para instalar la solución.

### <a name="oms-portal"></a>Portal OMS
Soluciones de administración de Microsoft se pueden instalar desde la Galería de soluciones en el portal OMS.

1. Inicie sesión en el portal OMS.
2. Haga clic en el mosaico de la **Galería de soluciones** .
2. En la página de la Galería de soluciones de OMS, obtenga información sobre cada solución disponible. Haga clic en el nombre de la solución que desea agregar a OMS.
3. En la página para la solución que elija, se muestra información detallada sobre la solución. Haga clic en **Agregar**.
4. Un nuevo mosaico para la solución que ha agregado aparece en la información general de página de OMS y puede empezar a usar después de que el servicio OMS procesa los datos.

### <a name="azure-quickstart-templates"></a>Tutorial rápido Azure plantillas
Los miembros de la Comunidad pueden enviar soluciones de administración de plantillas de Azure tutorial rápido.  Puede descargar estas plantillas para la instalación más adelante o inspeccionar ellos para obtener información sobre cómo [crear sus propias soluciones](#creating-a-solution).

1. Siga el proceso descrito en [el área de trabajo OMS y cuenta con la automatización](#oms-workspace-and-automation-account) para vincular una cuenta y el área de trabajo.
2. Vaya a [plantillas de Azure tutorial](https://azure.microsoft.com/documentation/templates/).  
3. Buscar una solución que le interesa.
4. Seleccione la solución en los resultados para ver sus detalles.
5. Haga clic en el botón de **implementar en Azure** .
6. Se le pedirá que proporcione información como el grupo de recursos y la ubicación además de los valores de los parámetros de la solución.
7. Haga clic en **comprar** para instalar la solución.

### <a name="deploy-azure-resource-manager-template"></a>Implementar la plantilla de administrador de recursos de Azure
Soluciones que recibe de la Comunidad o que [Cree usted mismo](#creating-a-solution) se implementan como una plantilla de administrador de recursos y puede utilizar cualquiera de los métodos estándares para [implementar una plantilla](../resource-group-template-deploy-portal.md).  Tenga en cuenta que antes de instalar la solución, debe crear y vincular el [área de trabajo OMS y cuenta con la automatización](#oms-workspace-and-automation-account).

## <a name="oms-workspace-and-automation-account"></a>Área de trabajo OMS y cuenta con la automatización
Soluciones de administración de la mayoría requieren un [área de trabajo OMS](../log-analytics/log-analytics-manage-access.md) contienen vistas y una [cuenta de automatización](../automation/automation-security-overview.md#automation-account-overview) que contenga runbooks y recursos relacionados. El área de trabajo y la cuenta deben cumplir los siguientes requisitos.

- Solo puede usar una solución de un área de trabajo OMS y una cuenta de automatización.  
- El área de trabajo OMS y cuenta con la automatización que utiliza una solución deben estar vinculados entre sí. Un área de trabajo OMS sólo puede estar vinculada a una cuenta de automatización y una cuenta de automatización sólo puede estar vinculada a un área de trabajo OMS.
- Para vincular, el área de trabajo OMS y la cuenta de automatización deben estar en el mismo grupo de recursos y de región.  La excepción es un área de trabajo OMS en la región de EE oriental y y cuenta con la automatización en Estados Unidos oriental 2.

### <a name="creating-a-link-between-an-oms-workspace-and-automation-account"></a>Crear un vínculo entre un área de trabajo OMS y cuenta con la automatización
Cómo especificar el área de trabajo OMS y cuenta con la automatización depende del método de instalación para la solución.

- Al instalar una solución de Microsoft a través del portal OMS, está instalado en el área de trabajo OMS actual y no se requiere ninguna cuenta de automatización.

- Al instalar una solución a través de Azure Marketplace, se le pedirá un área de trabajo OMS y cuenta con la automatización, y se creará el vínculo entre ellas.  

- Para las soluciones fuera de Azure Marketplace, debe vincular el área de trabajo OMS y la cuenta de automatización antes de instalar la solución.  Para ello, seleccione cualquier solución en Azure Marketplace y seleccione el área de trabajo OMS y la cuenta de automatización.  No tienes que realmente instalar la solución porque el vínculo se creará en cuanto se seleccionan el área de trabajo OMS y la cuenta de automatización.  Una vez creado el vínculo, puede usar dicha área de trabajo OMS y cuenta con la automatización para cualquier solución. 

### <a name="verifying-the-link-between-an-oms-workspace-and-automation-account"></a>Comprobar el vínculo entre un área de trabajo OMS y cuenta con la automatización
Puede comprobar el vínculo entre un área de trabajo OMS y una cuenta de automatización mediante el procedimiento siguiente.

1. Seleccione la cuenta de automatización en el portal de Azure.
2. Desplácese hasta la parte inferior del panel de **configuración** .
3. Si hay una sección denominada **OMS recursos** en el panel de **configuración** , esta cuenta está conectada a un área de trabajo OMS.
4. Seleccione el **área de trabajo** para ver los detalles del área de trabajo OMS vinculadas a esta cuenta de automatización.


## <a name="listing-management-solutions"></a>Soluciones de administración de entrada
Utilice el procedimiento siguiente para ver las soluciones de administración en las áreas de trabajo vinculados a la suscripción de Azure.

1. Inicie sesión en el portal de Azure.
2. En el panel izquierdo, seleccione **más servicios**.
3. Desplácese hacia abajo hasta **soluciones** o escriba *soluciones* en el cuadro de diálogo **filtro** .
4. Se mostrarán las soluciones instaladas en todas las áreas de trabajo.

Tenga en cuenta que puede ver solo las soluciones de Microsoft instaladas en el área de trabajo actual con el portal OMS.

## <a name="removing-a-management-solution"></a>Quitar una solución de administración
Cuando se quita una solución de administración, también se quitan todos los recursos de la solución.  

1. Busque la solución en el portal de Azure mediante el procedimiento descrito en el [listado de soluciones](#listing-solutions).
2. Seleccione la solución que desea quitar.
3. Haga clic en el botón **Eliminar** .

## <a name="creating-a-management-solution"></a>Crear una solución de administración
Una guía completa sobre la creación de soluciones de administración de están disponibles en la [creación de soluciones en conjunto de aplicaciones de administración de operaciones (OMS)](operations-management-suite-solutions-creating.md). 


## <a name="next-steps"></a>Pasos siguientes

- Buscar [Plantillas de Azure tutorial rápido](https://azure.microsoft.com/documentation/templates) para obtener ejemplos de plantillas de administrador de recursos diferentes.
- Crear sus propias [soluciones de administración](operations-management-suite-solutions-creating.md).
 