<properties 
    pageTitle="Usar el portal de Azure para implementar recursos Azure | Microsoft Azure" 
    description="Utilizar el portal de Azure y Azure recursos administrar para implementar los recursos." 
    services="azure-resource-manager,azure-portal" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Implementar recursos a las plantillas de administrador de recursos y portal de Azure

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI de Azure](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [API DE REST](resource-group-template-deploy-rest.md)

Este tema muestra cómo usar el [portal de Azure](https://portal.azure.com) con [El Administrador de recursos de Azure](azure-resource-manager/resource-group-overview.md) para implementar los recursos de Azure. Para obtener información sobre la administración de los recursos, vea [Administrar Azure recursos a través del portal](./azure-portal/resource-group-portal.md).

Actualmente, no todos los servicios es compatible con el portal o el Administrador de recursos. Para estos servicios, debe usar el [portal clásico](https://manage.windowsazure.com). Para el estado de cada servicio, consulte [el gráfico de disponibilidad de portal de Azure](https://azure.microsoft.com/features/azure-portal/availability/).

## <a name="create-resource-group"></a>Crear grupo de recursos

1. Para crear un grupo de recursos vacío, seleccione **nuevo** > **administración** > **Grupo de recursos**.

    ![Crear grupo de recursos vacío](./media/resource-group-template-deploy-portal/create-empty-group.png)

2. Darle un nombre y una ubicación y, si es necesario, seleccione una suscripción. Debe proporcionar una ubicación para el grupo de recursos, porque el grupo de recursos almacena metadatos acerca de los recursos. Por motivos de cumplimiento, desea especificar dónde se almacenan metadatos. En general, recomendamos que especifique una ubicación donde se guardarán la mayoría de los recursos. Uso de la misma ubicación, puede simplificar la plantilla.

    ![establecer los valores de grupo](./media/resource-group-template-deploy-portal/set-group-properties.png)

## <a name="deploy-resources-from-marketplace"></a>Implementar recursos de Marketplace

Después de crear un grupo de recursos, puede implementar recursos a él desde el catálogo de soluciones. El catálogo de soluciones proporciona soluciones predefinidas para escenarios comunes.

1. Para iniciar una implementación, seleccione **nuevo** y el tipo de recurso que le gustaría implementar. A continuación, busque la versión concreta del recurso que le gustaría implementar.

    ![implementar recursos](./media/resource-group-template-deploy-portal/deploy-resource.png)

2. Si no ve la solución en particular que le gustaría implementar, puede buscar el catálogo de soluciones para el mismo.

    ![catálogo de soluciones de búsqueda](./media/resource-group-template-deploy-portal/search-resource.png)

3. Según el tipo de recurso seleccionado, tiene una colección de propiedades pertinentes para establecer antes de la implementación. Estas opciones no se muestran aquí, como varían según el tipo de recurso. Para todos los tipos, debe seleccionar un grupo de recursos de destino. La imagen siguiente muestra cómo crear una aplicación web e implementar al grupo de recursos que ha creado.

    ![Crear grupo de recursos](./media/resource-group-template-deploy-portal/select-existing-group.png)

    Como alternativa, puede decidir crear un grupo de recursos al implementar los recursos. Seleccione **Crear nuevo** y asigne un nombre al grupo de recursos.

    ![Crear nuevo grupo de recursos](./media/resource-group-template-deploy-portal/select-new-group.png)

4. La implementación comienza. La implementación podría tardar unos minutos. Cuando haya terminado la implementación, vea una notificación.

    ![notificación de vista](./media/resource-group-template-deploy-portal/view-notification.png)

5. Después de implementar los recursos, puede agregar más recursos al grupo de recursos mediante el comando **Agregar** en el módulo de grupo de recursos.

    ![Agregar recursos](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Implementar recursos de la plantilla personalizada

Si desea ejecutar una implementación sin utilizar cualquiera de las plantillas en el mercado, puede crear una plantilla personalizada que define la infraestructura para la solución. Para obtener información sobre la creación de plantillas, vea [plantillas de administrador de recursos de Azure de creación](resource-group-authoring-templates.md).

1. Para implementar una plantilla personalizada a través del portal, seleccione **nuevo**y empezar a buscar para **Su implementación de la plantilla** hasta que puede seleccionar las opciones.

    ![implementación de la plantilla de búsqueda](./media/resource-group-template-deploy-portal/search-template.png)

2. Seleccione **Plantilla de implementación** de los recursos disponibles.

    ![Seleccione implementación de plantilla](./media/resource-group-template-deploy-portal/select-template.png)

3. Después de iniciar la implementación de plantilla, abra la plantilla en blanco que está disponible para personalizar.

    ![Crear plantilla](./media/resource-group-template-deploy-portal/show-custom-template.png)

    En el editor, agregue la sintaxis JSON que define los recursos que desea distribuir. Seleccione **Guardar** cuando haya terminado. Para obtener instrucciones sobre cómo escribir la sintaxis JSON, vea [Tutorial de plantilla de administrador de recursos](resource-manager-template-walkthrough.md).

    ![Editar plantilla](./media/resource-group-template-deploy-portal/edit-template.png)

4. O bien, puede seleccionar una plantilla existente en las [plantillas de Azure tutorial](https://azure.microsoft.com/documentation/templates/). Reciben contribuciones estas plantillas de la Comunidad. Carta de muchos escenarios comunes y, a continuación, alguien puede haber agregado una plantilla que sea similar a lo está intentando implementar. Puede buscar las plantillas para encontrar algo que coincida con su situación.

    ![Seleccionar plantilla de tutorial rápido](./media/resource-group-template-deploy-portal/select-quickstart-template.png)

    Puede ver la plantilla seleccionada en el editor.

5. Después de proporcionar todos los valores, seleccione **crear** para implementar la plantilla. 

    ![implementar plantilla](./media/resource-group-template-deploy-portal/create-custom-deploy.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Implementar recursos desde una plantilla guardada a su cuenta

El portal permite guardar una plantilla en su cuenta de Azure y a continuación, volver a implementar más adelante. Para obtener más información sobre cómo trabajar con estos guardado plantillas, [empezar a trabajar con plantillas privadas en el portal de Azure](./marketplace-consumer/mytemplates-getstarted.md).

1. Para buscar las plantillas guardadas, seleccione **Examinar** > **plantillas**.

    ![Examinar plantillas](./media/resource-group-template-deploy-portal/browse-templates.png)

2. En la lista de plantillas de guardado en su cuenta, seleccione el que desea trabajar en.

    ![plantillas de guardado](./media/resource-group-template-deploy-portal/saved-templates.png)

3. Seleccione **implementar** a implementar esta plantilla guardada.

    ![implementar plantilla guardada](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Pasos siguientes

- Para ver los registros de auditoría, consulte [con el Administrador de recursos de operaciones de auditoría](resource-group-audit.md).
- Para solucionar errores de implementación, vea [implementaciones de grupo de recursos de solución de problemas con el portal de Azure](resource-manager-troubleshoot-deployments-portal.md).
- Para recuperar una plantilla de una implementación o grupo de recursos, vea [exportar un administrador de recursos de Azure plantilla de los recursos existentes](resource-manager-export-template.md).





