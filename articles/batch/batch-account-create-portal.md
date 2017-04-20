<properties
    pageTitle="Crear una cuenta de Azure lote | Microsoft Azure"
    description="Obtenga información sobre cómo crear una cuenta de Azure lote en el portal de Azure para ejecutar las cargas de trabajo paralelas a gran escala en la nube"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/21/2016"
    ms.author="marsma"/>

# <a name="create-an-azure-batch-account-using-the-azure-portal"></a>Crear una cuenta de Azure lote con el portal de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](batch-account-create-portal.md)
- [Administración de lote .NET](batch-management-dotnet.md)

Obtenga información sobre cómo crear una cuenta de Azure lote en el [portal de Azure][azure_portal]y dónde encontrar importantes de propiedades de la cuenta, como las teclas de acceso y las direcciones URL de la cuenta. También trataremos lote precios y vincular una cuenta de Azure almacenamiento a su cuenta de proceso por lotes para que pueda usar [paquetes de aplicaciones](batch-application-packages.md) y [conservar los resultados de la tarea y el trabajo](batch-task-output.md).

## <a name="create-a-batch-account"></a>Crear una cuenta de proceso por lotes

1. Inicie sesión en el [portal de Azure][azure_portal].

2. Haga clic en **nuevo** > **calcular** > **lote servicio**.

    ![Lote en el catálogo de soluciones][marketplace_portal]

3. Se muestra el módulo de la **Nueva cuenta de lote** . Vea elementos *un* a *e* a continuación para obtener una descripción de cada elemento de la hoja.

    ![Crear una cuenta de proceso por lotes][account_portal]

    una. **Nombre de cuenta**: un nombre único para la cuenta de proceso por lotes. Este nombre debe ser único dentro de la región de Azure que se crea la cuenta (consulte la *ubicación* ). Puede contener solo caracteres en minúsculas, números y debe ser 3-24 caracteres de longitud.

    b. **Suscripción**: una suscripción en la que desea crear la cuenta de proceso por lotes. Si tiene sólo una suscripción, se selecciona de forma predeterminada.

    c. **Grupo de recursos**: un recurso existente agrupar de su nueva cuenta de lote o, si lo desea, cree una nueva.

    d. **Ubicación**: región un Azure en la que desea crear la cuenta de proceso por lotes. Solo las regiones compatibles con la suscripción y el grupo de recursos se muestran como opciones.

    e. **Cuenta de almacenamiento** (opcional): una cuenta de almacenamiento de **Propósito General** asociar (vincular) a la nueva cuenta de lote. Para obtener más detalles, vea [cuenta de almacenamiento de Azure vinculado](#linked-azure-storage-account) a continuación.

4. Haga clic en **crear** para crear la cuenta.

  El portal indica que resulta **implementar** la cuenta y, al finalizar, aparece una notificación de **implementaciones se realizó correctamente** en *las notificaciones*.

## <a name="view-batch-account-properties"></a>Ver propiedades de la cuenta de proceso por lotes

Una vez creada la cuenta, puede abrir el **módulo de la cuenta de proceso por lotes** para tener acceso a sus propiedades y configuración. Puede acceder a todas las propiedades y configuración de la cuenta usando el menú izquierdo del módulo de cuenta por lotes.

![Módulo de lote cuenta en el portal de Azure][account_blade]

* **Dirección URL de cuenta por lotes**: aplicaciones creadas con el [desarrollo de lote API](batch-technical-overview.md#batch-development-apis) necesitan una dirección URL de cuenta para administrar recursos y ejecutar trabajos en la cuenta. Una dirección URL de cuenta de lote tiene el siguiente formato:

    `https://<account_name>.<region>.batch.azure.com`

![Dirección URL de la cuenta de lote de portal][account_url]

* **Teclas de acceso**: las aplicaciones también necesitan una tecla de acceso al trabajar con recursos en su cuenta por lotes. Para ver o volver a crear las teclas de acceso de la cuenta de proceso por lotes, escriba `keys` en el cuadro de **búsqueda** del menú de la izquierda en el módulo de cuenta por lotes, a continuación, seleccione **las teclas**.

    ![Teclas de cuenta por lotes en el portal de Azure][account_keys]

## <a name="pricing"></a>Precios

Cuentas de lote se ofrecen sólo en un "nivel gratuita", lo que significa que no se carga para la cuenta del lote. Se cargan para los recursos de proceso de Azure subyacentes que consumen las soluciones por lotes así como los recursos utilizados por otros servicios al ejecutan las cargas de trabajo. Por ejemplo, se aplican a los nodos de cálculo de los grupos y para los datos que se guardan en el almacenamiento de Azure como entrada o salida para las tareas. Del mismo modo, si usa la característica de [paquetes de aplicaciones](batch-application-packages.md) del lote, se cargan los recursos de almacenamiento de Azure usados para almacenar los paquetes de aplicaciones. Ver el [lote de precios] [ batch_pricing] para obtener más información.

## <a name="linked-azure-storage-account"></a>Cuenta vinculada de almacenamiento de Azure

Como se mencionó anteriormente, puede vincular una cuenta de almacenamiento de **Propósito General** (opcionalmente) a su cuenta de lote. La característica de [paquetes de aplicaciones](batch-application-packages.md) del lote usa almacenamiento de blobs en un cuenta de almacenamiento, de propósito General vinculado al igual que la biblioteca de [.NET de convenciones de archivo por lotes](batch-task-output.md) . Estas características opcionales le ayudarán a implementar las aplicaciones que ejecutarán las tareas por lotes y conservar los datos que generan.

Lote actualmente admite *únicamente* el tipo de cuenta de almacenamiento de **Propósito General** como se describe en el paso 5, [crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account), en [Azure sobre cuentas de almacenamiento](../storage/storage-create-storage-account.md). Al vincular una cuenta de Azure almacenamiento a su cuenta de proceso por lotes, seguro vínculo *solo* ser una cuenta de almacenamiento de **Propósito General** .

![Crear una cuenta de almacenamiento de "Propósito General"][storage_account]

Recomendamos que cree una cuenta de almacenamiento para uso exclusivo con su cuenta de lote.

>[AZURE.WARNING] Tenga cuidado al regenerar las teclas de acceso de una cuenta de almacenamiento vinculada. Regenerar solo una clave de cuenta de almacenamiento y haga clic en **Las teclas de sincronización** en el módulo de cuenta vinculado de almacenamiento. Espere cinco minutos para permitir que las teclas de propagarse a los nodos de cálculo de los grupos, a continuación, volver a crear y sincronizar la otra clave si es necesario. Si regenerar las dos claves al mismo tiempo, los nodos de cálculo no podrá sincronizar cualquier tecla y se pierde el acceso a la cuenta de almacenamiento.

  ![Volver a generar claves de la cuenta de almacenamiento][4]

## <a name="batch-service-quotas-and-limits"></a>Los límites y las cuotas de servicio por lotes

Tenga en cuenta que como con la suscripción de Azure y otros servicios de Azure, determinadas [cuotas y los límites](batch-quota-limit.md) se aplican a las cuentas por lotes. Las cuotas de actuales de una cuenta de lote aparecen en el portal de **Propiedades**de la cuenta.

![Cuotas de la cuenta de lote en el portal de Azure][quotas]

Tener estas cuotas en cuenta al diseñar y escalado de las cargas de trabajo por lotes. Por ejemplo, si su grupo no alcanzar el número de destino de nodos de cálculo que haya especificado, se haya alcanzado el límite de cuota de núcleo para su cuenta de lote.

Tenga en cuenta que no está restringido para una sola cuenta de lote para la suscripción de Azure. Puede ejecutar múltiples cargas de trabajo por lotes en una sola cuenta de lote o distribuir las cargas de trabajo entre cuentas de lote en la misma suscripción, pero en diferentes regiones de Azure.

Muchos de estos cuotas pueden aumentar simplemente con una solicitud de soporte técnico de producto gratuito enviada en el portal de Azure. Vea [las cuotas y límites para el servicio de Azure lote](batch-quota-limit.md) para obtener más información sobre cómo se solicita aumentar la cuota.

## <a name="other-batch-account-management-options"></a>Otras opciones de administración de la cuenta de proceso por lotes

Además de usar el portal de Azure, también puede crear y administrar cuentas de lote con los siguientes elementos:

* [Cmdlets de PowerShell lote](batch-powershell-cmdlets-get-started.md)
* [CLI de Azure](../xplat-cli-install.md)
* [Administración de lote .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Pasos siguientes

* Vea la [Introducción a las características Azure lote](batch-api-basics.md) para obtener más información sobre las características y los conceptos de servicio de lote. El artículo describe los recursos por lotes principales como grupos, nodos de cálculo, tareas y tareas y ofrece una descripción general de las características del servicio que permitir la ejecución de carga de trabajo de cálculo a gran escala.

* Aprenda los conceptos básicos de desarrollo de una aplicación habilitada para el lote mediante la [biblioteca de cliente .NET lote](batch-dotnet-get-started.md). El [artículo Introducción](batch-dotnet-get-started.md) le guía a través de una aplicación de trabajo que utiliza el servicio de lote para ejecutar una carga de trabajo en varios nodos de cálculo y a continuación, incluye el uso de almacenamiento de Azure para ensayo de archivo de carga de trabajo y recuperación.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Volver a generar claves de la cuenta de almacenamiento"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
