<properties
   pageTitle="Empezar a trabajar con plantillas privadas | Microsoft Azure"
   description="Agregar, administrar y compartir sus plantillas privadas con el portal de Azure, la CLI de Azure o PowerShell."
   services="marketplace-customer"
   documentationCenter=""
   authors="VybavaRamadoss"
   manager="asimm"
   editor=""
   tags="marketplace, azure-resource-manager"
   keywords=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2016"
   ms.author="vybavar"/>

# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Empezar a trabajar con plantillas privadas en el Portal de Azure

Un [Administrador de recursos de Azure](../resource-group-authoring-templates.md) es una plantilla descriptiva utilizada para definir la implementación. Puede definir los recursos para implementar una solución y especificar los parámetros y variables que le permiten valores de entrada para distintos entornos. La plantilla se compone de JSON y expresiones que puede utilizar para construir valores para su implementación.

Puede usar la nueva capacidad de **plantillas** en el [Portal de Azure](https://portal.azure.com) junto con el proveedor de recursos de **Microsoft.Gallery** como una extensión de la [Azure Marketplace](https://azure.microsoft.com/marketplace/) para permitir a los usuarios crear, administrar e implementar plantillas privadas de una biblioteca personal.

Este documento le guiará a través de agregar, administrar y compartir una privada **plantilla** con el Portal de Azure.

## <a name="guidance"></a>Orientación

Las siguientes sugerencias le ayudará a aprovechar al máximo **las plantillas** cuando se trabaja con las soluciones:

- Una **plantilla** es un recurso encapsulado que contiene una plantilla de administrador de recursos y metadatos adicionales. Comportamiento es muy similar a un elemento en el catálogo de soluciones La diferencia clave es que es un elemento privado en lugar de los elementos del catálogo de soluciones públicos.
- La biblioteca de **plantillas** funciona bien para usuarios que necesitan para personalizar sus instalaciones.
- **Las plantillas** funcionan bien para los usuarios que necesitan un repositorio simple dentro de Azure.
- Comience con un administrador de recursos de plantilla existente. Buscar plantillas en [github](https://github.com/Azure/azure-quickstart-templates) o [Exportar plantilla](../resource-manager-export-template.md) de un grupo de recursos existente.
- El usuario que publica dependen de **plantillas** . El nombre del editor está visible para todos aquellos que tiene acceso de lectura.
- **Plantillas de** recursos de administrador de recursos y no se puede cambiar una vez publicado.

## <a name="add-a-template-resource"></a>Agregar un recurso de plantilla

Hay dos formas de crear un recurso de **plantilla** en el portal de Azure.

### <a name="method-1--create-a-new-template-resource-from-a-running-resource-group"></a>Método 1: Crear un nuevo recurso de plantilla de un grupo de recursos de ejecución

1. Vaya a un grupo de recursos existente en el Portal de Azure. En **configuración**, seleccione **Exportar plantilla** .
2. Una vez que se exporta la plantilla de administrador de recursos, utilice el botón **Guardar plantilla** guardarlo en el repositorio de **plantillas** . Buscar detalles completos para exportar plantilla [aquí](../resource-manager-export-template.md).
<br /><br />
![Exportación de grupo de recursos](media/rg-export-portal1.PNG)  <br />

3. Seleccione el botón de comando **Guardar en la plantilla** .
<br /><br />

4. Escriba la información siguiente:

    - Nombre: nombre del objeto de plantilla (Nota: este es el nombre de un administrador de recursos de Azure según. Aplicarán todas las restricciones de nomenclatura y no se puede cambiar una vez creado).
    - Descripción: resumen rápido acerca de la plantilla.

    ![Guardar plantilla](media/save-template-portal1.PNG)  <br />

5. Haga clic en **Guardar**.

    > [AZURE.NOTE] El módulo de plantilla de exportación muestra notificaciones cuando la plantilla exportada del Administrador de recursos tiene errores, pero aún podrá guardar esta plantilla de administrador de recursos a las plantillas. Asegúrese de que comprobar y corregir problemas de la plantilla de cualquier administrador de recursos antes de implementar la plantilla de administrador de recursos exportado.

### <a name="b-method-2--add-a-new-template-resource-from-browse"></a>B. Método 2: Agregar un nuevo recurso de plantilla de examinar

También puede agregar una nueva **plantilla** de borrador usando la + Agregar botón de comando en **Examinar > plantillas de**. Debe proporcionar un nombre, descripción y la plantilla de administrador de recursos JSON.

![Agregar plantilla](media/add-template-portal1.PNG)  <br />

> [AZURE.NOTE] Microsoft.Gallery es que un inquilino según el proveedor de recursos de Azure. El recurso de plantilla está vinculado al usuario quién lo creó. No está vinculado a ninguna suscripción específica. Se debe elegir sólo para implementar una plantilla de una suscripción.

## <a name="view-template-resources"></a>Ver los recursos de plantilla

Se pueden ver todas las **plantillas** disponibles en **Examinar > plantillas de**. Esto incluye **plantillas** que haya creado, así como los que se han compartido con usted con diferentes niveles de permisos. Más detalles en la sección [control de acceso](#access-control-for-a-tenant-resource-provider) .

![Plantilla de vista](media/view-template-portal1.PNG)  <br />

Puede ver los detalles de una **plantilla** haciendo clic en un elemento de la lista.

![Plantilla de vista](media/view-template-portal2c.png)  <br />

## <a name="edit-a-template-resource"></a>Editar un recurso de plantilla

Puede iniciar el flujo de edición de una **plantilla** haciendo clic en el elemento de la lista de examinar o seleccionando el botón de comando Editar.

![Editar plantilla](media/edit-template-portal1a.PNG)  <br />

Puede editar la descripción o el texto de la plantilla de administrador de recursos. No se puede editar el nombre, ya que es un nombre de recurso de administrador de recursos. Cuando edita el Administrador de recursos plantilla JSON se valida para asegurarse de que es JSON válido. Seleccione **Aceptar** y luego en **Guardar** para guardar la plantilla actualizada.

![Editar plantilla](media/edit-template-portal2a.PNG)  <br />

Una vez guardada la **plantilla** se muestra una notificación de confirmación.

![Editar plantilla](media/edit-template-portal3b.png)  <br />

## <a name="deploy-a-template-resource"></a>Implementar un recurso de plantilla

Puede implementar cualquier **plantilla** que tiene permisos de **lectura** . El flujo de implementación inicia el módulo de implementación de Azure plantilla estándar. Rellene los valores para los parámetros de la plantilla de administrador de recursos continuar con la implementación.

![Implementar plantilla](media/deploy-template-portal1b.png)  <br />

## <a name="share-a-template-resource"></a>Compartir un recurso de plantilla

Un recurso de **plantilla** se puede compartir con sus compañeros. Uso compartido se comporta del mismo modo que [la asignación de roles para los recursos en Azure](../active-directory/role-based-access-control-configure.md). El propietario de la **plantilla** proporciona permisos a otros usuarios que pueden interactuar con un recurso de plantilla. La persona o grupo de personas a con que compartir la **plantilla** podrán ver la plantilla de administrador de recursos y sus propiedades de la galería.

### <a name="access-control-for-the-microsoftgallery-resources"></a>Control de acceso para los recursos Microsoft.Gallery

Función | Permisos
---|----
Propietario | Permite un control completo sobre el recurso de plantilla incluidos compartir
Lector | Permite la lectura y Execute(Deploy) en el recurso de plantilla
Colaborador | Permite editar y eliminar permisos en el recurso de plantilla. Usuario no puede compartir la plantilla con otros usuarios

Seleccione **Compartir** en el elemento Examinar haciendo clic en o en la hoja de vista de un elemento específico. Esto inicia una experiencia de compartir.

![Plantilla de compartir](media/share-template-portal1a.png)  <br />

 Ahora puede elegir un rol y un usuario o grupo para proporcionar acceso a una **plantilla**en particular. Las funciones disponibles son propietario, lector y colaborador. Más detalles en la sección [control de acceso](#access-control-for-a-tenant-resource-provider) .

![Plantilla de compartir](media/share-template-portal2b.png)  <br />

![Plantilla de compartir](media/share-template-portal3b.png)  <br />

Haga clic en **Aceptar**y **Seleccione** . Ahora puede ver los usuarios o grupos que haya agregado a los recursos.

![Plantilla de compartir](media/share-template-portal4b.png)  <br />

> [AZURE.NOTE] Una plantilla solo se compartirá con los usuarios y grupos en el mismo inquilino de Azure Active Directory. Si comparte una plantilla con una dirección de correo electrónico que no está en su inquilino, una invitación se enviará que pide al usuario para unirse al inquilino como invitado.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre cómo crear plantillas de administrador de recursos, consulte [plantillas de edición](../resource-group-authoring-templates.md)
- Para conocer las funciones que puede usar en una plantilla de administrador de recursos, vea [funciones de plantilla](../resource-group-template-functions.md)
- Para obtener instrucciones sobre el diseño de las plantillas, vea [procedimientos recomendados para diseñar plantillas de administrador de recursos de Azure](../best-practices-resource-manager-design-templates.md)
