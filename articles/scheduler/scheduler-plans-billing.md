<properties
 pageTitle="Planes y facturación en el programador de Azure"
 description="Planes y facturación en el programador de Azure"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="plans-and-billing-in-azure-scheduler"></a>Planes y facturación en el programador de Azure

## <a name="job-collection-plans"></a>Planes de recopilación de trabajo

Colecciones de trabajo son la entidad facturable programador de Azure. Colecciones de trabajo contienen un número de tareas y estar formados por tres planes: libre, estándar y Premium, que se describen a continuación.

|**Plan de recopilación de trabajo**|**Nº máximo de tareas para cada colección de trabajo**|**Max periodicidad**|**Colecciones de trabajo max por suscripción**|**Límites**|
|:---|:---|:---|:---|:---|
|**Liberar**|5 trabajos por colección de trabajo|Una vez cada hora. No se puede ejecutar trabajos con más frecuencia que una vez cada hora|Una suscripción se permite hasta 1 colección gratuita de trabajo|No se puede usar el [objeto de salida de autorización de HTTP](scheduler-outbound-authentication.md)
|**Estándar**|50 trabajos por colección de trabajo|Una vez por minuto. No se puede ejecutar trabajos con más frecuencia que una vez por minuto|Se permite una suscripción hasta 100 colecciones de trabajo estándar|Acceso al conjunto completo de características del programador|
|**P10 Premium**|50 trabajos por colección de trabajo|Una vez por minuto. No se puede ejecutar trabajos con más frecuencia que una vez por minuto|Una suscripción se permite hasta 10.000 colecciones de trabajo P10 Premium. Para obtener más información, <a href="mailto:wapteams@microsoft.com">póngase en contacto con nosotros</a> .|Acceso al conjunto completo de características del programador|
|**P20 Premium**|trabajos de 1000 por colección de trabajo|Una vez por minuto. No se puede ejecutar trabajos con más frecuencia que una vez por minuto|Una suscripción se permite hasta 10.000 colecciones de trabajo P20 Premium. Para obtener más información, <a href="mailto:wapteams@microsoft.com">póngase en contacto con nosotros</a> .|Acceso al conjunto completo de características del programador|

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>Las actualizaciones y se convierte de planes de la colección de trabajo

Puede actualizar o degradar un plan de recopilación de trabajo en cualquier momento entre los planes libre, estándar y Premium. Sin embargo, cuando el cambio a una colección de trabajo libre, descenso puede fallar por uno de los motivos siguientes:

- Una colección de trabajo libre ya existe en la suscripción
- Un trabajo en la colección de trabajo tiene una periodicidad mayor de lo permitido para trabajos en colecciones de trabajo gratuita. La periodicidad máxima permitida en una colección de trabajo libre es una vez cada hora
- Hay más de 5 trabajos en la colección de trabajo
- Un trabajo en la colección de trabajo tiene una acción de HTTP o HTTPS que utiliza un [objeto de salida de autorización de HTTP](scheduler-outbound-authentication.md)

## <a name="billing-and-azure-plans"></a>Planes de facturación y Azure

Las suscripciones no se cargan de forma gratuita colecciones de trabajo. Si tiene más de 100 colecciones de trabajo estándar (unidades de facturación estándares 10), es más conveniente para que todas las colecciones de trabajo en el plan de premium.

Si tiene una colección de trabajo estándar y una colección de trabajo premium, son facturado una facturación unidad _y_ uno premium facturación unidad estándar. Los efectos de servicio programador, en función del número de colecciones de trabajo activo que se establecen a estándar o premium; Esto se explica más adelante en las dos secciones siguientes.

## <a name="standard-billable-units"></a>Unidades facturables estándar

Una unidad facturable estándar puede incluir hasta 10 colecciones de trabajo estándar. Dado que un conjunto estándar de trabajo puede tener hasta 50 trabajos por colección de trabajo, una unidad de facturación estándar permite una suscripción tener trabajos de hasta 500: hasta casi 22 millones de ejecuciones de trabajos por mes.

Si tiene entre 1 y 10 colecciones de trabajo estándar, se le facturará 1 unidad de facturación estándar. Si tiene entre 11 y 20 colecciones de trabajo estándar, se le facturará 2 unidades de facturación estándares. Si tiene entre colecciones de trabajo estándar 30 y 21, deberá facturará 3 unidades estándares de facturación y así sucesivamente.

## <a name="p10-premium-billable-units"></a>P10 Unidades facturable Premium

Una unidad facturable de P10 premium puede incluir hasta 10.000 colecciones de trabajo de P10 premium. Puesto que una colección de trabajo P10 premium puede tener hasta 50 trabajos por colección de trabajo, una unidad de facturación premium permite una suscripción a tener hasta 500.000 trabajos: hasta casi 22 millones de ejecuciones de trabajo por mes.

Si tiene entre 1 y 10.000 colecciones de trabajo premium, se le facturará 1 unidad de facturación de prima P10. Si tiene entre 10,001 y colecciones de trabajo de 20.000 premium, deberá facturará 2 unidades de facturación de premium P10 y así sucesivamente.

Por lo tanto, colecciones de trabajo premium P10 tienen la misma funcionalidad que las colecciones de trabajo estándar, pero proporcionan un salto de precio en caso de que la aplicación requiere una gran cantidad de colecciones de trabajo.

## <a name="p20-premium-billable-units"></a>P20 Unidades facturable Premium

Una unidad facturable de P20 premium puede incluir hasta 5.000 colecciones de trabajo de P20 premium. Puesto que una colección de trabajo P20 premium puede tener hasta 1.000 trabajos por colección de trabajo, una unidad de facturación premium permite una suscripción a tener hasta 5.000.000 trabajos: hasta casi 220 millones de ejecuciones de trabajo por mes.

Colecciones de trabajo de P20 premium proporciona las mismas capacidades como colecciones de trabajo de P10 premium, pero también admite un mayor número los trabajos por colección de trabajo y un mayor número total de trabajos generales que premium P10 permitiéndole tener más escalabilidad.

## <a name="billing-and-active-status"></a>Estado de facturación y activo

Colecciones de trabajo siempre están activas a menos que toda su suscripción ha sido en algunos estado deshabilitado temporal debido a problemas de facturación. La única manera de asegurarse de que no se ha facturado una colección de trabajo es: establecer para el plan _libre_ o eliminar la colección de trabajo.

Aunque puede deshabilitar todos los trabajos dentro de una colección de trabajo en una única operación, no cambia el estado de facturación de la colección de trabajo, tendrá la colección de trabajo _que_ tenga que pagar. Asimismo, colecciones de trabajo vacía se consideran activas y se cargará.

## <a name="pricing"></a>Precios

Para detalles de los precios, consulte [Programador de precios](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="see-also"></a>Vea también


 [¿Qué es programador?](scheduler-intro.md)

 [Azure programador conceptos, terminología y la jerarquía de entidad](scheduler-concepts-terms.md)

 [Empezar a usar a planificador en el portal de Azure](scheduler-get-started-portal.md)

 [Referencia de API de REST de programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell de programador Azure](scheduler-powershell-reference.md)

 [Azure programador de alta disponibilidad y confiabilidad](scheduler-high-availability-reliability.md)

 [Límites de programador Azure, valores predeterminados y códigos de error](scheduler-limits-defaults-errors.md)

 [Autenticación de salida de Azure programador](scheduler-outbound-authentication.md)
