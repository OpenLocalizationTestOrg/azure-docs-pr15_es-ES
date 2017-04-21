<properties
    pageTitle="Administrar los extremos en el administrador del tráfico de Azure | Microsoft Azure"
    description="En este artículo le ayudará a agregar, quitar, habilitar y deshabilitar extremos desde el administrador del tráfico de Azure."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="add-disable-enable-or-delete-endpoints"></a>Agregar, deshabilitar, habilitar o eliminar extremos

La característica de aplicaciones Web de servicio de la aplicación de Azure ya proporciona migración tras error y la funcionalidad de enrutamiento de tráfico de turnos para sitios Web dentro de un centro de datos, independientemente del modo de sitio Web. Administrador de tráfico Azure le permite especificar migración tras error y el enrutamiento de tráfico de turnos para sitios Web y nube de servicios en centros de datos distintos. El primer paso es necesario para proporcionar esa funcionalidad es agregar el extremo de servicio o sitio Web de nube al administrador de tráfico.

>[AZURE.NOTE]  En este artículo se explica cómo usar el portal clásico. El portal clásico Azure solo es compatible con la creación y la asignación de aplicaciones Web y servicios de nube como extremos. El nuevo [portal de Azure](https://portal.azure.com) es la interfaz preferida.

También puede deshabilitar extremos individuales que forman parte de un perfil de administrador de tráfico. Deshabilitar un extremo deja como parte del perfil, pero el perfil que se comporta como si el extremo no está incluido en él. Esta acción es útil para quitar temporalmente un punto final que esté en modo de mantenimiento o se implemente de nuevo. Una vez que el extremo es nuevo en funcionamiento, puede estar habilitado.

>[AZURE.NOTE] Deshabilitar un extremo no tiene ninguna relación con su estado de implementación de Azure. Un buen estado extremo permanece hacia arriba y puede recibir tráfico incluso cuando deshabilitado en el Administrador de tráfico. Además, deshabilitar un extremo de un perfil no afecta a su estado en otro perfil.

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>Para agregar un extremo de servicio o sitio Web de nube

1. En el panel de administrador de tráfico en el portal de clásico Azure, busque el perfil de administrador de tráfico que contiene la configuración de extremo que desee modificar. Para abrir la página Configuración, haga clic en la flecha situada a la derecha del nombre del perfil.
2. En la parte superior de la página, haga clic en **extremos** para ver los extremos que ya forman parte de la configuración.
3. En la parte inferior de la página, haga clic en **Agregar** para obtener acceso a la página **Agregar extremos de servicio** . De forma predeterminada, la página muestra los servicios de nube en **Los extremos de servicio**.
4. Para los servicios de nube, seleccione los servicios de nube de la lista para agregarlos como extremos para este perfil. Borrar el nombre de servicio de nube, quita de la lista de extremos.
5. Sitios Web, haga clic en la lista desplegable **Tipo de servicio** y, a continuación, seleccione la **aplicación Web**.
6. Seleccione los sitios Web en la lista para agregarlo como extremos para este perfil. Borrar el nombre del sitio Web, quita de la lista de extremos. Puede seleccionar sólo un sitio Web por el centro de datos de Azure (también conocido como una región). Cuando se selecciona el primer sitio Web, no estarán disponibles para la selección de los otros sitios Web en el mismo centro de datos. Tenga en cuenta que se enumeran los sitios Web estándar.
7. Después de seleccionar los extremos para este perfil, haga clic en la marca de verificación en la esquina inferior derecha para guardar los cambios.

>[AZURE.NOTE] Después de agregar o quitar un punto final de un perfil con el método de enrutamiento de tráfico de *migración tras error* , la lista de prioridad de migración tras error no se puede solicitar como desee. Puede ajustar el orden de la lista de prioridad de migración tras error en la página de configuración. Para obtener más información, vea [configurar de migración tras error enrutamiento de tráfico](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Para deshabilitar un punto final

1. En el panel de administrador de tráfico en el portal de clásico Azure, busque el perfil de administrador de tráfico que contiene la configuración de extremo que desee modificar. Para abrir la página Configuración, haga clic en la flecha situada a la derecha del nombre del perfil.
2. En la parte superior de la página, haga clic en **extremos** para ver los extremos que se incluyen en la configuración.
3. Haga clic en el extremo que desea deshabilitar y, a continuación, haga clic en **Deshabilitar** en la parte inferior de la página.
4. Los clientes continuarán para enviar tráfico al extremo de la duración del tiempo de vida (TTL). Puede cambiar el período de vida en la página Configuración del perfil de administrador de tráfico.

## <a name="to-enable-an-endpoint"></a>Para habilitar un punto final

1. En el panel de administrador de tráfico en el portal de clásico Azure, busque el perfil de administrador de tráfico que contiene la configuración de extremo que desee modificar. Para abrir la página Configuración, haga clic en la flecha situada a la derecha del nombre del perfil.
2. En la parte superior de la página, haga clic en **extremos** para ver los extremos que se incluyen en la configuración.
3. Haga clic en el extremo que desea habilitar y, a continuación, haga clic en **Habilitar** en la parte inferior de la página.
4. Los clientes se dirigen al extremo habilitado como se indica en el perfil.

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>Para eliminar un extremo de servicio o sitio Web de nube

1. En el panel de administrador de tráfico en el portal de clásico Azure, busque el perfil de administrador de tráfico que contiene la configuración de extremo que desee modificar. Para abrir la página Configuración, haga clic en la flecha situada a la derecha del nombre del perfil.
2. En la parte superior de la página, haga clic en **extremos** para ver los extremos que ya forman parte de la configuración.
3. En la página puntos finales, haga clic en el nombre del extremo de los que desea eliminar del perfil.
4. En la parte inferior de la página, haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

* [Administrar perfiles de administrador de tráfico](traffic-manager-manage-profiles.md)
* [Configurar métodos de enrutamiento](traffic-manager-configure-routing-method.md)
* [Estado de degradado de solución de problemas de tráfico de administrador](traffic-manager-troubleshooting-degraded.md)
* [Consideraciones de rendimiento del Administrador de tráfico](traffic-manager-performance-considerations.md)
* [Operaciones en el Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)
