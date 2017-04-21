<properties
 pageTitle="Límites de programador y valores predeterminados"
 description="Límites de programador y valores predeterminados"
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

# <a name="scheduler-limits-and-defaults"></a>Límites de programador y valores predeterminados

## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>Límites, límites, valores predeterminados y las cuotas de programador

[AZURE.INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>El encabezado x-ms-solicitud de Id.

Cada solicitud realizada contra el servicio programador devuelve un encabezado de respuesta denominado**x-ms-solicitud-id**. Este encabezado contiene un valor opaco que identifica la solicitud.

Si una solicitud de forma uniforme tiene errores y haya comprobado que la solicitud se formula correctamente, puede usar este valor para informar del error a Microsoft. En el informe, incluya el valor de x-ms-solicitud-id, el tiempo aproximado que se realizó la solicitud, el identificador de la suscripción, colección de trabajo, o trabajo y el tipo de operación que se intentó realizar la solicitud.

## <a name="see-also"></a>Vea también


 [¿Qué es programador?](scheduler-intro.md)

 [Azure programador conceptos, terminología y la jerarquía de entidad](scheduler-concepts-terms.md)

 [Empezar a usar a planificador en el portal de Azure](scheduler-get-started-portal.md)

 [Planes y facturación en el programador de Azure](scheduler-plans-billing.md)

 [Referencia de API de REST de programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell de programador Azure](scheduler-powershell-reference.md)

 [Azure programador de alta disponibilidad y confiabilidad](scheduler-high-availability-reliability.md)

 [Autenticación de salida de Azure programador](scheduler-outbound-authentication.md)
