<properties
    pageTitle="Servicios de administración pública de Azure | Microsoft Azure"
    description="Información sobre cómo administrar su suscripción en la administración pública de Azure"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="zakramer"
    manager="liki"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/21/2016"
    ms.author="zakramer" />


#  <a name="managing-and-connecting-to-your-subscription-in-azure-government"></a>Administrar y conectarse a su suscripción en la administración pública de Azure

Gobierno Azure tiene direcciones URL y extremos para administrar su entorno único. Es importante utilizar las conexiones adecuadas para administrar su entorno a través del portal o PowerShell. Cuando se haya conectado al entorno de administración pública de Azure, el funcionamiento normal de un servicio de administración funciona si se ha implementado el componente.

## <a name="connecting-via-the-portal"></a>Conectarse a través del portal
El portal de la forma principal que se conectan mayoría de las personas a la administración pública de Azure.  Para conectar, desplácese hasta el portal en [https://portal.azure.us](https://portal.azure.us).  La versión del portal de Azure heredada se puede acceder a través de [https://manage.windowsazure.us](https://manage.windowsazure.us).

Pueden crear suscripciones para su cuenta mediante una conexión a [https://account.windowsazure.us](https://account.windowsazure.us).

## <a name="connecting-via-powershell"></a>Conectarse a través de PowerShell

Si va a usar Azure PowerShell para administrar una suscripción grande a través de la secuencia de comandos o acceso a características que no ya están disponibles en el portal de Azure que debe conectarse a la administración pública de Azure en lugar de público de Azure.  Si ha utilizado PowerShell en Azure público, principalmente es la misma.  Las diferencias en Azure gobierno son:

+ Conectarse a su cuenta
+ Nombres de región

>[AZURE.NOTE] Si aún no ha utilizado PowerShell, consulte la [Introducción a Azure PowerShell](../powershell-install-configure.md).

Cuando empiece a PowerShell, debe indicar a Azure PowerShell para conectarse a la administración pública de Azure especificando un parámetro de entorno.  El parámetro garantiza que PowerShell se está conectando a los extremos correctos.  La colección de extremos se determina cuando se conecte a registro en su cuenta.  API diferentes requieren diferentes versiones del cambio de entorno:

Tipo de conexión | Comando
---|----
Comandos de [Administración de servicios](https://msdn.microsoft.com/library/dn708504.aspx) | `Add-AzureAccount -Environment AzureUSGovernment`
Comandos de [Administración de recursos](https://msdn.microsoft.com/library/mt125356.aspx) | `Add-AzureRmAccount -EnvironmentName AzureUSGovernment`
Comandos de [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) | `Connect-MsolService -AzureEnvironment UsGovernment`
[V2 de comando de Azure Active Directory](https://msdn.microsoft.com/library/azure/mt757189.aspx) | `Connect-AzureAD -AzureEnvironmentName AzureUSGovernment`

También puede utilizar el modificador de entorno al conectarse a una cuenta de almacenamiento mediante AzureStorageContext de nuevo y especifique AzureUSGovernment.

### <a name="determining-region"></a>Determinar la región

Cuando se haya conectado, hay una diferencia adicional: las regiones que se utiliza para apuntar a un servicio.  Cada nube Azure tiene diferentes regiones.  Puede ver una lista en la página de disponibilidad del servicio.  Normalmente, se utiliza la región en el parámetro de la ubicación de un comando.

Hay un problema.  Las áreas de la administración pública de Azure necesitan dar formato de forma diferente a sus nombres comunes:

Nombre común | Comando
---|----
Estados Unidos Gov Virginia | Virginia USGov
Estados Unidos Gov Iowa | USGov Iowa

>[AZURE.NOTE] No hay ningún espacio entre los Estados Unidos y Gov al usar el parámetro de ubicación.

Si desea validar las regiones disponibles en la administración pública de Azure, puede ejecutar los siguientes comandos e imprimir la lista actual:

    Get-AzureLocation

Si siente curiosidad acerca de los entornos disponibles a través de Azure, puede ejecutar:

    Get-AzureEnvironment

## <a name="next-steps"></a>Pasos siguientes

Si desea obtener más información, puede comprobar:

+ [Documentos de PowerShell en GitHub](https://github.com/Azure/azure-powershell)
+ [Instrucciones paso a paso acerca de cómo conectarse a la administración de recursos](https://blogs.msdn.microsoft.com/azuregov/2015/10/08/configuring-arm-on-azure-gc/)
+ [Documentos de PowerShell de Azure en MSDN](https://msdn.microsoft.com/library/mt619274.aspx)

Para obtener información adicional y actualizaciones suscribirse al [Microsoft Azure gobierno Blog] (https://blogs.msdn.microsoft.com/azuregov/)
