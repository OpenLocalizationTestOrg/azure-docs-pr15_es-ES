<properties
   pageTitle="Explorador de recursos de Azure | Microsoft Azure"
   description="Describe el Explorador de recursos de Azure y cómo puede utilizarse para ver y actualizar las implementaciones a través del Administrador de recursos de Azure"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="stuartleeks"
   manager="ankodu"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="stuartle;tomfitz"/>

# <a name="use-azure-resource-explorer-to-view-and-modify-resources"></a>Utilizar el Explorador de recursos de Azure para ver y modificar los recursos
El [Explorador de recursos de Azure](https://resources.azure.com) es una excelente herramienta para mirar recursos que ya ha creado en la suscripción. Con esta herramienta, podrá comprender cómo los recursos están estructurados y ven las propiedades asignadas a cada recurso. Puede obtener información acerca de las operaciones de la API de REST y los cmdlets de PowerShell que están disponibles para un tipo de recurso y, a continuación, puede emitir comandos a través de la interfaz. Explorador de recursos puede resultar útil al crear plantillas de administrador de recursos porque le permite ver las propiedades de los recursos existentes.

El origen de la herramienta de explorador de recursos está disponible en [github](https://github.com/projectkudu/ARMExplorer), que proporciona una referencia valiosa si necesita implementar un comportamiento similar en sus propias aplicaciones.

## <a name="view-resources"></a>Ver recursos
Vaya a [https://resources.azure.com](https://resources.azure.com) e inicie sesión con las mismas credenciales que usaría para el [Portal de Azure](https://portal.azure.com).

Una vez cargado, la vista de árbol de la izquierda le permite profundizar en las suscripciones y grupos de recursos:

![TreeView](./media/resource-manager-resource-explorer/are-01-treeview.png)

Al explorar en profundidad un grupo de recursos verá los proveedores que hay recursos de ese grupo:

![proveedores](./media/resource-manager-resource-explorer/are-02-treeview-providers.png)

Desde allí puede iniciar taladro en las instancias de recursos. En la siguiente captura de pantalla puede ver el `sltest` instancia de SQL Server en la vista de árbol. En el lado derecho, puede ver información sobre las solicitudes de API de REST que puede usar con ese recurso. Desplazándose hasta el nodo de un recurso, el Explorador de recursos realiza automáticamente la solicitud GET para recuperar información sobre el recurso. En el área de texto grande debajo de la dirección URL, verá la respuesta de la API. 

Familiarizarse con las plantillas de administrador de recursos el contenido del cuerpo empieza a estar familiarizado! La sección de **Propiedades** de la respuesta coincide con los valores que se pueden proporcionar en la sección **Propiedades** de la plantilla.

![SQL server](./media/resource-manager-resource-explorer/are-03-sqlserver-with-response.png)

Explorador de recursos le permite conservar desglose para explorar los recursos secundarios, en el caso de la base de datos de SQL Server, hay recursos secundarios para cosas como bases de datos y las reglas de firewall.

Explorar una base de datos nos muestra las propiedades de base de datos. En la siguiente captura de pantalla podemos ver que la base de datos `edition` es `Standard` y `serviceLevelObjective` (o nivel de base de datos) es `S1`.

![base de datos SQL](./media/resource-manager-resource-explorer/are-04-database-get.png)

## <a name="change-resources"></a>Cambiar recursos

Una vez que se ha desplazado a un recurso, puede seleccionar el botón Editar para poder modificar el contenido JSON. A continuación, puede usar Explorador de recursos para editar el JSON y enviar una solicitud de SUPERPONER para cambiar el recurso. Por ejemplo, la imagen siguiente muestra el nivel de base de datos cambiarlo a `S0`:

![base de datos: SUPERPONER solicitud](./media/resource-manager-resource-explorer/are-05-database-put.png)

Seleccionando **poner** enviar la solicitud. 

Una vez que se ha enviado la solicitud Resource Explorer vuelve a emitir la solicitud GET para actualizar el estado. En este caso podemos ver que la `requestedServiceObjectiveId` se ha actualizado y es diferente de la `currentServiceObjectiveId` que indica que está en curso una operación de escala. Puede hacer clic en el botón obtener para actualizar el estado de forma manual.

![base de datos: GET Solicitud2](./media/resource-manager-resource-explorer/are-06-database-get2.png)

## <a name="performing-actions-on-resources"></a>Realizar acciones en los recursos

La pestaña **acciones** le permite ver y realizar operaciones de resto adicionales. Por ejemplo, cuando haya seleccionado un recurso de sitio web, la pestaña acciones presenta una lista larga de las operaciones disponibles, algunos de los cuales se muestran a continuación.

![Web - solicitud de publicación](./media/resource-manager-resource-explorer/are-web-post.png)

## <a name="invoking-the-api-via-powershell"></a>Invocar la API a través de PowerShell
La pestaña de PowerShell en el Explorador de recursos muestra los cmdlets utilizar para interactuar con el recurso que actualmente se explorar. Según el tipo de recurso que se ha seleccionado, la secuencia de comandos de PowerShell mostrado puede variar de cmdlets simple (como `Get-AzureRmResource` y `Set-AzureRmResource`) a los cmdlets más complicados (como intercambiar ranuras en un sitio web). 

![PowerShell](./media/resource-manager-resource-explorer/are-07-powershell.png)

Para obtener más información sobre los cmdlets de PowerShell de Azure el, vea [con Azure con el Administrador de recursos de Azure](powershell-azure-resource-manager.md)

## <a name="summary"></a>Resumen
Cuando se trabaja con el Administrador de recursos, el Explorador de recursos puede ser una herramienta muy útil. Es una excelente manera de buscar formas de usar PowerShell para la consulta y realizar cambios. Si está trabajando con la API de REST es una excelente forma de introducción y probar rápidamente las llamadas a la API antes de empezar a escribir código. Y si escribe plantillas puede ser una excelente forma de conocer la jerarquía de recursos y encontrar dónde desea ubicar configuración - puede hacer un cambio en el Portal y, a continuación, busque las entradas correspondientes en el Explorador de recursos.

Para obtener más información, vea el [vídeo con Scott Hanselman y David Ebbo 9 de canal](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo)


