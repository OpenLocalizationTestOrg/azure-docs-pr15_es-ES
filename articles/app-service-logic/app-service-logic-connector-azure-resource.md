<properties
   pageTitle="Mediante el conector de Azure recursos en las aplicaciones de lógica | Servicio de aplicaciones de Microsoft Azure"
   description="Cómo crear y configurar la aplicación conector de recursos de Azure o API y usarla en una aplicación de la lógica de servicio de la aplicación de Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="stepsic-microsoft-com"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="09/01/2016"
   ms.author="stepsic"/>

# <a name="get-started-with-the-azure-resource-connector-and-add-it-to-your-logic-app"></a>Empezar a trabajar con el conector de recursos de Azure y agregue a su aplicación lógica
>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2014 12-01-vista previa de aplicaciones de lógica.

Usar el conector de recursos de Azure para administrar fácilmente los recursos de Azure dentro de la aplicación de la lógica.

## <a name="create-the-azure-resource-connector"></a>Crear el conector de recursos de Azure
Para usar la aplicación de la API de conector de recursos de Azure, debe crear una instancia de ella. Esto puede hacerse en línea al crear una aplicación de lógica o seleccionando la aplicación de la API de conector de administrador de recursos de Azure de Azure Marketplace.

Para configurarlo, tiene necesita establecer el Principal de un servicio con permisos para realizar lo que desea hacer en Azure. Todas las llamadas se realizan en nombre de esta entidad de seguridad de servicio que haya configurado. Esto permite definir el ámbito del conector para usar solo exactamente lo que desea hacer y nada más.

David Ebbo ha escrito [una entrada de blog excelentes](http://blog.davidebbo.com/2014/12/azure-service-principal.html) sobre cómo configurar esto. Siga todas las instrucciones y obtener el **Identificador del inquilino**, **ID de cliente** y **secreto**. Estos tres campos, además de los **Id. de suscripción**, son lo que son necesarios para configurar el conector.

## <a name="using-the-azure-resource-connector-in-logic-apps-designer"></a>Usar el conector de recursos de Azure en el Diseñador de aplicaciones de lógica
### <a name="trigger"></a>Desencadenador
Hay dos desencadenadores que son compatibles con el conector:

Nombre | Descripción
---- | -----------
Se produce el evento | Desencadenador cuando se produce un evento a un recurso en la suscripción.
Métrica cruza umbral |  Desencadenador cuando una métrica cumpla con un determinado umbral.

### <a name="action"></a>Acción

Asimismo, puede proporcionar un gran número de acciones dentro de la suscripción de Azure:

**Grupos** de recursos puede:

Nombre | Descripción
---- | -----------
Grupos de recursos de lista | Lista de todos los grupos de recursos en la suscripción.
Obtener el grupo de recursos | Obtener un grupo de recursos por su id.
Crear grupo de recursos | Crear o actualizar un grupo de recursos.
Eliminar grupo de recursos | Eliminar un grupo de recursos.

Para los **recursos** , puede:

Nombre | Descripción
---- | -----------
Recursos de lista | Recursos de la lista de la suscripción por distintos tipos de filtros.
Obtener recursos | Obtener un único recurso por su Id. de recurso
Crear o actualizar recursos | Crear un recurso o actualizar un recurso existente. Debe proporcionar todas las propiedades para ese recurso.
Acción de recursos |  Realizar ninguna otra acción en un recurso. Debe conocer el nombre de acción y la carga que esta acción toma (si existe).
Eliminar recurso | Eliminar un recurso.

Para **Los proveedores de recursos** puede:

Nombre | Descripción
---- | -----------
Proveedores de recursos de lista | Lista de todos los proveedores de recursos disponibles en la suscripción.

Para **Implementaciones de grupo de recursos** puede:

Nombre | Descripción
---- | -----------
Implementaciones de lista | Lista de todas las implementaciones en un grupo de recursos.
Obtener la implementación | Para obtener una implementación de la plantilla, su id.
Crear la implementación | Proporcionar una plantilla para crear una nueva implementación de grupo de recursos.

Para los **eventos** acerca de los recursos puede:

Nombre | Descripción
---- | -----------
Obtenga los eventos | Obtenga los eventos en una suscripción o de un recurso.

Para **métricas** acerca de los recursos puede:

Nombre | Descripción
---- | -----------
Obtener métricas | Obtener una métrica para un recurso de Id.

## <a name="do-more-with-your-connector"></a>Hacer más cosas con el conector
Una vez creado el conector, puede agregar a un flujo de negocio con una aplicación de lógica. Consulte [¿qué aplicaciones de lógica?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Si desea empezar a trabajar con aplicaciones de Azure lógica antes de suscribirse para una cuenta de Azure, vaya a la [aplicación intente lógica](https://tryappservice.azure.com/?appservice=logic), donde puede crear inmediatamente una aplicación de la lógica de inicio de corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

Ver la referencia de la API de REST Swagger en [conectores y API aplicaciones referencia](http://go.microsoft.com/fwlink/p/?LinkId=529766).

<!--References -->

<!--Links -->
[Creating a Logic app]: app-service-logic-create-a-logic-app.md
