<properties
    pageTitle="Controlar el tráfico de aplicación web de Azure con el administrador del tráfico de Azure"
    description="Este artículo proporciona información de resumen para el administrador del tráfico de Azure en relación con aplicaciones web de Azure."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    writer="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/25/2016"
    ms.author="cephalin"/>

# <a name="controlling-azure-web-app-traffic-with-azure-traffic-manager"></a>Controlar el tráfico de aplicación web de Azure con el administrador del tráfico de Azure

> [AZURE.NOTE] En este artículo se proporciona información de resumen para el Administrador de tráfico de Microsoft Azure en relación con aplicaciones de Azure aplicación de servicio Web. Encontrará más información sobre el administrador del tráfico de Azure propio visitando los vínculos al final de este artículo.

## <a name="introduction"></a>Introducción
Puede usar el Administrador de tráfico de Azure para controlar cómo se distribuyen las solicitudes de los clientes web para las aplicaciones web de servicio de aplicaciones de Azure. Cuando los extremos de aplicación web se agregan a un perfil de administrador de tráfico de Azure, Administrador de tráfico de Azure realiza un seguimiento del estado de las aplicaciones web (en ejecución, detenido o eliminados) para que pueda decidir cuál de los extremos debe recibir el tráfico.

## <a name="load-balancing-methods"></a>Métodos de equilibrio de carga
Administrador de tráfico de Azure usa tres métodos de equilibrio de carga diferente. Estos se describen en la siguiente lista se relacionan con aplicaciones de Azure web.

* **Migración tras error**: si tiene clones de aplicación web de diferentes regiones, puede usar este método para configurar una aplicación web para todo el tráfico de web cliente del servicio y configurar otra aplicación web en una región distinta para que el tráfico del servicio en caso de que la primera aplicación web no está disponible.

* **Turnos**: si tiene clones de aplicación web de diferentes regiones, puede usar este método para distribuir uniformemente el tráfico en todas las aplicaciones web de diferentes regiones.

* **Rendimiento**: método el rendimiento distribuye tráfico basándose en el tiempo de ida y vuelta más corta a los clientes. El método de rendimiento se puede utilizar para las aplicaciones de web dentro de la misma región o en diferentes regiones.

##<a name="web-apps-and-traffic-manager-profiles"></a>Perfiles de administrador de tráfico y aplicaciones Web
Para configurar el control de tráfico de aplicación web, cree un perfil en Azure tráfico Manager que usa uno de los tres carga métodos de equilibrio descritos anteriormente y, a continuación, agregue los extremos (en este caso, aplicaciones web) que desee para controlar el tráfico al perfil. El estado de aplicación web (en ejecución, detenido o eliminado) se comunica regularmente al perfil para que el Administrador de tráfico de Azure puede dirigir el tráfico según corresponda.

Al utilizar el Administrador de tráfico de Azure con Azure, tenga en cuenta los siguientes puntos:

* Para las implementaciones sólo de aplicación de la web dentro de la misma región, aplicaciones Web ya ofrece migración tras error y la funcionalidad de turnos sin tener en cuenta en el modo de aplicación web.

* Para implementaciones de la misma región que utilizan aplicaciones Web junto con otro servicio de nube de Azure, puede combinar ambos tipos de extremos para habilitar escenarios híbrido.

* Solo puede especificar un extremo de la aplicación web por región en un perfil. Al seleccionar una aplicación web como un extremo de una región específica, las aplicaciones web restantes en esa región no estarán disponibles para selección de ese perfil.

* Los extremos de aplicación web que se especifican en un perfil de administrador de tráfico de Azure aparecerán en la sección de **Nombres de dominio** en la página Configurar la aplicación web en el perfil, pero no irá configurables.

* Después de agregar una aplicación web a un perfil, la **Dirección URL del sitio** en el panel de la página del portal de la aplicación web mostrará la dirección URL de dominio personalizado de la aplicación web si ha configurado una. En caso contrario, se mostrará la dirección URL de perfil de administrador de tráfico (por ejemplo, `contoso.trafficmgr.com`). El nombre de dominio directo de la aplicación web y la URL del Administrador de tráfico estará visible en página de configuración de la aplicación web en la sección de **Nombres de dominio** .

* Los nombres de dominio personalizado funciona según lo esperado, pero además de agregarlas a sus aplicaciones web, también debe configurar su mapa DNS para que apunten a la URL del Administrador de tráfico. Para obtener información sobre cómo configurar un dominio personalizado para una aplicación web de Azure, consulte [configurar un nombre de dominio personalizado para un sitio web de Azure](web-sites-custom-domain-name.md).

* Solo puede agregar aplicaciones web que están en modo estándar para un perfil de administrador de tráfico de Azure.

## <a name="next-steps"></a>Pasos siguientes

Para ver una introducción técnica y conceptual del Administrador de tráfico de Azure, vea [Introducción al administrador de tráfico](../traffic-manager/traffic-manager-overview.md).

Para obtener más información acerca del uso de administrador de tráfico con Web Apps, vea que el blog de publicaciones [Mediante el Administrador de tráfico de Azure con los sitios Web Azure](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) y [Azure tráfico administrador ahora puede integrar con los sitios Web Azure](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).
