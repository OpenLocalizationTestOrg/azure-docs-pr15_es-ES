<properties
   pageTitle="Administrar los extremos en el administrador del tráfico de Azure | Microsoft Azure"
   description="En este artículo le ayudará a agregar, quitar, habilitar y deshabilitar extremos desde el administrador del tráfico de Azure."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />

# <a name="add-disable-enable-or-delete-endpoints"></a>Agregar, deshabilitar, habilitar o eliminar extremos

La característica de aplicaciones Web de servicio de la aplicación de Azure ya proporciona migración tras error y la funcionalidad de enrutamiento de tráfico de turnos para sitios Web dentro de un centro de datos, independientemente del modo de sitio Web. Administrador de tráfico Azure le permite especificar migración tras error y el enrutamiento de tráfico de turnos para sitios Web y nube de servicios en centros de datos distintos. El primer paso es necesario para proporcionar esa funcionalidad es agregar el extremo de servicio o sitio Web de nube al administrador de tráfico.

>[AZURE.NOTE] No puede agregar ubicaciones externas o administrador de tráfico perfiles como extremos con el portal de clásico de Azure. Debe utilizar la API de REST [Definición de crear](http://go.microsoft.com/fwlink/p/?LinkId=400772) o [Agregar AzureTrafficManagerEndpoint](http://go.microsoft.com/fwlink/p/?LinkId=400774)de Windows PowerShell.

También puede deshabilitar extremos individuales que forman parte de un perfil de administrador de tráfico. Extremos incluyen servicios en la nube y sitios Web. Deshabilitar un extremo deja como parte del perfil, pero el perfil que se comporta como si el extremo no está incluido en él. Esta acción es muy útil para quitar temporalmente un punto final que esté en modo de mantenimiento o se implemente de nuevo. Una vez que el extremo es nuevo en funcionamiento, puede estar habilitado.

>[AZURE.NOTE] Deshabilitar un extremo no tiene ninguna relación con su estado de implementación de Azure. Un buen estado extremo permanecerán hacia arriba y puede recibir tráfico incluso cuando deshabilitado en el Administrador de tráfico. Además, deshabilitar un extremo de un perfil no afecta a su estado en otro perfil.

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>Para agregar un extremo de servicio o sitio Web de nube


1. En el panel de administrador de tráfico en el portal de clásico Azure, busque el perfil de administrador de tráfico que contiene la configuración de extremo que desea modificar y, a continuación, haga clic en la flecha situada a la derecha del nombre del perfil. Se abrirá la página de configuración del perfil.
2. En la parte superior de la página, haga clic en **extremos** para ver los extremos que ya forman parte de la configuración.
3. En la parte inferior de la página, haga clic en **Agregar** para obtener acceso a la página **Agregar extremos de servicio** . De forma predeterminada, la página muestra los servicios de nube en **Los extremos de servicio**.
4. Para los servicios de nube, seleccione los servicios de nube en la lista para habilitarlos como extremos para este perfil. Borrar el nombre de servicio de nube, quita de la lista de extremos.
5. Sitios Web, haga clic en la lista desplegable **Tipo de servicio** y, a continuación, seleccione la **aplicación Web**.
6. Seleccione los sitios Web en la lista para agregarlo como extremos para este perfil. Borrar el nombre del sitio Web, quita de la lista de extremos. Tenga en cuenta que solo puede seleccionar un solo sitio Web por el centro de datos de Azure (también conocido como una región). Si selecciona un sitio Web en un centro de datos que hospeda varios sitios Web, cuando se selecciona el primer sitio Web, otras personas en el mismo centro de datos no estarán disponibles para su selección. Tenga en cuenta que se enumeran los sitios Web estándar.
7. Después de seleccionar los extremos para este perfil, haga clic en la marca de verificación en la esquina inferior derecha para guardar los cambios.

>[AZURE.NOTE] Si está utilizando el método de enrutamiento de tráfico de *migración tras error* , después de agregar o quitar un punto final, asegúrese de ajustar la lista de prioridad de migración tras error en la página configuración para reflejar el orden de migración tras error que desee para la configuración. Para obtener más información, vea [configurar de migración tras error enrutamiento de tráfico](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Para deshabilitar un punto final

1. En el panel de administrador de tráfico en el portal de clásico Azure, busque el perfil de administrador de tráfico que contiene la configuración de extremo que desea modificar y, a continuación, haga clic en la flecha situada a la derecha del nombre del perfil. Se abrirá la página de configuración del perfil.
2. En la parte superior de la página, haga clic en **extremos** para ver los extremos que se incluyen en la configuración.
3. Haga clic en el extremo que desea deshabilitar y, a continuación, haga clic en **Deshabilitar** en la parte inferior de la página.
4. Deja de tráfico que fluye al extremo basado en el DNS tiempo de vida (TTL) configurado para el nombre de dominio de administrador de tráfico. Puede cambiar el valor de TTL desde la página Configuración del perfil de administrador de tráfico.

## <a name="to-enable-an-endpoint"></a>Para habilitar un punto final

1. En el panel de administrador de tráfico en el portal de clásico Azure, busque el perfil de administrador de tráfico que contiene la configuración de extremo que desea modificar y, a continuación, haga clic en la flecha situada a la derecha del nombre del perfil. Se abrirá la página de configuración del perfil.
2. En la parte superior de la página, haga clic en **extremos** para ver los extremos que se incluyen en la configuración.
3. Haga clic en el extremo que desea habilitar y, a continuación, haga clic en **Habilitar** en la parte inferior de la página.
4. Iniciará el tráfico que fluye en el servicio nuevo como dictado por el perfil.

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>Para eliminar un extremo de servicio o sitio Web de nube


1. En el panel de administrador de tráfico en el portal de clásico Azure, busque el perfil de administrador de tráfico que contiene la configuración de extremo que desea modificar y, a continuación, haga clic en la flecha situada a la derecha del nombre del perfil. Se abrirá la página de configuración del perfil.
2. En la parte superior de la página, haga clic en **extremos** para ver los extremos que ya forman parte de la configuración.
3. En la página puntos finales, haga clic en el nombre del extremo de los que desea eliminar del perfil.
4. En la parte inferior de la página, haga clic en **Eliminar**.

>[AZURE.NOTE] No se puede eliminar ubicaciones externas o administrador de tráfico perfiles como extremos con el portal de clásico de Azure. Debe usar Windows PowerShell. Para obtener más información, vea [Quitar AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/dn690251.aspx).

## <a name="next-steps"></a>Pasos siguientes

- [Configurar método de enrutamiento de migración tras error](traffic-manager-configure-failover-routing-method.md)
- [Configurar método de enrutamiento de turnos](traffic-manager-configure-round-robin-routing-method.md)
- [Configurar método de enrutamiento de rendimiento](traffic-manager-configure-performance-routing-method.md)
- [Estado de degradado de solución de problemas de tráfico de administrador](traffic-manager-troubleshooting-degraded.md)
- [Operaciones en el Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)
