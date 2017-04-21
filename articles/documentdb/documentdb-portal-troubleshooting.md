<properties
    pageTitle="Solución de problemas de portal DocumentDB | Microsoft Azure"
    description="Obtenga información para resolver problemas en el portal de DocumentDB Azure." 
    services="documentdb"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="mimig"/>

# <a name="azure-documentdb-portal-troubleshooting-tips"></a>Portal de DocumentDB Azure consejos para solucionar problemas

En este artículo se describe cómo solucionar problemas de DocumentDB en el portal de Azure. 

## <a name="resources-are-missing"></a>Faltan recursos

**Síntoma**: bases de datos o colecciones que faltan desde los portal módulos.

**Solución**: reduzca el uso de la aplicación para funcionar con la cuota de rendimiento máximo para la colección. 

**Explicación**: el portal es una aplicación como cualquier otra, realizar llamadas a su base de datos de DocumentDB y colección. Si las solicitudes actualmente se está limitando debido a que se realizan desde una aplicación independiente de llamadas, el portal puede también reducirá, causando recursos que no aparezca en el portal. Para resolver el problema, resolver la causa del uso de alto rendimiento y, a continuación, actualice el módulo portal. Obtener información sobre cómo medir y el uso de rendimiento inferior puede encontrarse en la sección [rendimiento](documentdb-performance-tips.md#throughput) del artículo [sugerencias de rendimiento](documentdb-performance-tips.md) .
 
## <a name="pages-or-blades-wont-load"></a>No cargar páginas o aspas

**Síntoma**: no mostrar páginas y módulos en el portal.

**Solución**: reduzca el uso de la aplicación para funcionar con la cuota de rendimiento máximo para la colección. 

**Explicación**: el portal es una aplicación como cualquier otra, realizar llamadas a su base de datos de DocumentDB y colección. Si las solicitudes actualmente se está limitando debido a que se realizan desde una aplicación independiente de llamadas, el portal puede también reducirá, causando recursos que no aparezca en el portal. Para resolver el problema, resolver la causa del uso de alto rendimiento y, a continuación, actualice el módulo portal. Obtener información sobre cómo medir y el uso de rendimiento inferior puede encontrarse en la sección [rendimiento](documentdb-performance-tips.md#throughput) del artículo [sugerencias de rendimiento](documentdb-performance-tips.md) .

## <a name="add-collection-button-is-disabled"></a>Agregar botón de la colección está deshabilitado

**Síntoma**: en el módulo de base de datos, se desactiva el botón **Agregar colección** .

**Explicación**: si su suscripción de Azure está asociada con créditos de beneficio, como créditos gratuitas ofrecen de una suscripción a MSDN y ha usado todas sus créditos para el mes, es no se puede crear cualquier colección adicional en DocumentDB.

**Solución**: quitar el límite de gastos de su cuenta.

1. En el portal de Azure en la Jumpbar, haga clic en **suscripciones**, haga clic en la suscripción asociada con la base de datos de DocumentDB y, a continuación, en el módulo de la **suscripción** , haga clic en **Administrar**. 
    ![DocumentDB ofrece varias, bien definidos modelos de coherencia (flexible) para elegir](./media/documentdb-portal-troubleshooting/documentdb-change-billing.png)

2. En la ventana del explorador, verá que no tiene créditos restante. Haga clic en el botón **Quitar límite de gastos** para quitar el gasto solo el período de facturación actual o indefinidamente. A continuación, complete el Asistente para agregar o confirmar la información de su tarjeta de crédito. 
    ![DocumentDB ofrece varias, bien definidos modelos de coherencia (flexible) para elegir](./media/documentdb-portal-troubleshooting/documentdb-remove-spending-limit.png)

 
## <a name="query-explorer-completes-with-errors"></a>Explorador de consulta se completa con errores

Vea [solucionar problemas de explorador de consulta](documentdb-query-collections-query-explorer.md#troubleshoot).

## <a name="no-data-available-in-monitoring-tiles"></a>No hay datos disponibles en la supervisión de mosaicos

Vea [solucionar problemas de supervisión mosaicos](documentdb-monitor-accounts.md#troubleshooting).

## <a name="no-documents-returned-in-document-explorer"></a>No hay documentos devueltos en el Explorador de documento

Vea [solución de problemas de Document Explorer](documentdb-view-json-document-explorer.md#troubleshoot).

## <a name="next-steps"></a>Pasos siguientes

Si todavía experimenta problemas en el portal, correo electrónico [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) para obtener asistencia o archivo solicitar soporte técnico en el portal de haciendo clic en **Examinar**, **ayuda + soporte técnico**y haga clic en **Crear solicitud de soporte técnico**.
