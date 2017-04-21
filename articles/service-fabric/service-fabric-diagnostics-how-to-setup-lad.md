<properties
   pageTitle="Recopilar registros mediante los diagnósticos de Azure Linux | Microsoft Azure"
   description="En este artículo se describe cómo configurar diagnósticos de Azure para recopilar registros de un clúster de servicio tela Linux que se ejecuta en Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="subramar"/>


# <a name="collect-logs-by-using-azure-diagnostics"></a>Recopilar registros mediante los diagnósticos de Azure

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Cuando utiliza un clúster de Azure servicio tela, es una buena idea recopilar los registros de todos los nodos en una ubicación central. Con los registros en una ubicación central facilita analizar y solucionar problemas, independientemente de si están en los servicios, la aplicación o el clúster propiamente dicho. Una forma de cargar y recopilar registros es usar la extensión de diagnósticos de Azure, que carga los registros para el almacenamiento de Azure. Puede leer los eventos de almacenamiento y colóquelos en un producto como [Búsqueda elástico](service-fabric-diagnostic-how-to-use-elasticsearch.md) u otra solución de análisis de registro.

## <a name="log-sources-that-you-might-want-to-collect"></a>Orígenes de registro que desea recopilar
- **Registros de tela de servicio**: emite desde la plataforma a través de [LTTng](http://lttng.org) y cargado en su cuenta de almacenamiento. Registros pueden ser operativos eventos o eventos de tiempo de ejecución que emite la plataforma. Estos registros se almacenan en la ubicación que especifica el manifiesto de clúster. (Para obtener los detalles de la cuenta de almacenamiento, busque la etiqueta **AzureTableWinFabETWQueryable** y busque **StoreConnectionString**.)
- **Eventos de aplicación**: emitido desde el código del servicio. Puede usar cualquier solución de registro que escribe los archivos de registro basado en texto, por ejemplo, LTTng. Para obtener más información, consulte la documentación de LTTng en seguimiento de la aplicación.  


## <a name="deploy-the-diagnostics-extension"></a>Implementar la extensión de diagnóstico
Es el primer paso para recopilar registros implementar la extensión de diagnóstico en cada una de las máquinas virtuales en el clúster de servicio tela. La extensión de diagnósticos recopila registros en cada VM y ellos cargarán en la cuenta de almacenamiento que especifique. Los pasos varían en función de si se utiliza el portal de Azure o el Administrador de recursos de Azure.

Para implementar la extensión de diagnósticos en las máquinas virtuales en el clúster como parte de la creación de un clúster, establezca **los diagnósticos** en **activado**. Después de crear el clúster, no puede cambiar esta configuración a través del portal.

A continuación, configure diagnósticos de Azure Linux (LAD) para recopilar los archivos y ponerlos en su cuenta de almacenamiento. Este proceso se explica como escenario 3 ("cargar sus propios archivos de registro") en el artículo [Usando LAD para supervisar y diagnosticar máquinas virtuales de Linux](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md). Este proceso, obtiene acceso a los seguimientos. Puede cargar los seguimientos a un visualizador de su elección.

También puede implementar la extensión de diagnóstico mediante el Administrador de recursos de Azure. El proceso es similar para Windows y Linux y documentado para clústeres de Windows en [cómo recopilar los registros de diagnósticos de Azure](service-fabric-diagnostics-how-to-setup-wad.md).

También puede usar el conjunto de aplicaciones de administración de operaciones, como se describe en [Las operaciones de administración de conjunto registro análisis con Linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/).

Cuando termine de esta configuración, el agente LAD supervisa los archivos de registro especificado. Siempre que se agrega una nueva línea al archivo, crea una entrada de registro del sistema que se envía al almacenamiento de información que ha especificado.


## <a name="next-steps"></a>Pasos siguientes
Para conocer con más detalle qué eventos debe examinar durante la solución de problemas, vea [LTTng documentación](http://lttng.org/docs) y [LAD usando](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md).
