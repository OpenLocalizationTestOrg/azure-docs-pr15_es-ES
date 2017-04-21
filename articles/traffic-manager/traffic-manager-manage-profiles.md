<properties
    pageTitle="Administrar perfiles de administrador de tráfico de Azure | Microsoft Azure"
    description="Este artículo le ayudará a crear, deshabilitar, habilitar, eliminar y ver el historial de un perfil de administrador de tráfico de Azure."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="manage-an-azure-traffic-manager-profile"></a>Administrar un perfil de administrador de tráfico de Azure

Perfiles de tráfico Manager usan enrutamiento de tráfico métodos para controlar la distribución de tráfico a los servicios de nube o extremos de sitio Web. En este artículo se explica cómo crear y administrar estos perfiles.

## <a name="create-a-traffic-manager-profile-using-quick-create"></a>Crear un perfil de administrador de tráfico mediante la creación rápida

Puede crear rápidamente un perfil de administrador de tráfico utilizando creación rápida en el portal de clásico de Azure. Crear rápido le permite crear perfiles con opciones de configuración básicas. Sin embargo, no puede usar la creación rápida para la configuración como el conjunto de los extremos (servicios en la nube y sitios Web), el orden de migración tras error para el método de enrutamiento de tráfico de migración tras error o configuración de supervisión. Después de crear su perfil, puede configurar estas opciones en el portal de clásico de Azure. Administrador de tráfico admite extremos de hasta 200 por perfil. Sin embargo, la mayoría de los escenarios de uso requiere sólo algunos de los extremos.

### <a name="to-create-a-traffic-manager-profile"></a>Para crear un perfil de administrador de tráfico

1. **Implementar los servicios en la nube y sitios Web en su entorno de producción.** Para obtener más información acerca de los servicios de nube, consulte [Servicios en la nube](http://go.microsoft.com/fwlink/p/?LinkId=314074). Para obtener más información sobre los sitios Web, consulte los [sitios Web](http://go.microsoft.com/fwlink/p/?LinkId=393327).

2. **Inicie sesión en el portal de clásico de Azure.** Haga clic en **nuevo** en la esquina inferior izquierda del portal, haga clic en **Servicios de red > Administrador de tráfico**y, a continuación, haga clic en **Crear rápido** para empezar a configurar su perfil.
3. **Configurar el prefijo DNS.** Dar a su perfil de administrador de tráfico DNS único nombre de prefijo. Puede especificar solo el prefijo para un nombre de dominio de administrador de tráfico.
4. **Seleccione la suscripción.** Seleccione la suscripción de Azure adecuada. Cada perfil está asociado con una sola suscripción. Si solo tiene una suscripción, esta opción no aparece.
5. **Seleccione el método de enrutamiento de tráfico.** Seleccione el método de enrutamiento de tráfico en la **Directiva de enrutamiento del tráfico**. Para obtener más información acerca de los métodos de enrutamiento de tráfico, consulte [métodos de enrutamiento de tráfico sobre el Administrador de tráfico](traffic-manager-routing-methods.md).
6. **Haga clic en "Crear" para crear el perfil**. Cuando haya finalizado la configuración de perfil, puede encontrar su perfil en el panel de administrador de tráfico en el portal de clásico de Azure.
7. **Configurar extremos, supervisión y opciones adicionales en el portal de clásico de Azure.** Solo usando Creación rápida configura la configuración básica. Es necesario configurar otras opciones como la lista de extremos y el orden de migración tras error de punto final.


## <a name="disable-enable-or-delete-a-profile"></a>Deshabilitar, habilitar o eliminar un perfil

Puede deshabilitar un perfil existente para que el Administrador de tráfico no hace referencia solicitudes de usuario a los extremos configurados. Cuando se deshabilita un perfil de administrador de tráfico, el perfil y la información contenida en el perfil permanecen intactos y se pueden modificar en la interfaz de administrador de tráfico.  Referencias a reanudar cuando vuelva a activar el perfil. Cuando se crea un perfil de administrador de tráfico en el portal de clásico Azure, se activa automáticamente. Si decide que ya no necesita un perfil, puede eliminarlo.

### <a name="to-disable-a-profile"></a>Para deshabilitar un perfil

1. Si está utilizando un nombre de dominio personalizado, cambie el registro CNAME en el servidor DNS de Internet para que ya no apunta a su perfil de administrador de tráfico.
2. Deja de tráfico que se dirige a los extremos a través de la configuración del perfil de administrador de tráfico.
3. Seleccione el perfil que desee deshabilitar. En la página Administrador de tráfico, resalte el perfil, haga clic en la columna situada junto al nombre del perfil. Nota, haga clic en el nombre del perfil o en la flecha situada junto al nombre, abrirá la página de configuración del perfil.
4. Después de seleccionar el perfil, haga clic en **Deshabilitar** en la parte inferior de la página.

### <a name="to-enable-a-profile"></a>Para habilitar un perfil

1. Seleccione el perfil que desee deshabilitar. En la página Administrador de tráfico, resalte el perfil, haga clic en la columna situada junto al nombre del perfil. Nota, haga clic en el nombre del perfil o en la flecha situada junto al nombre, abrirá la página de configuración del perfil.
2. Después de seleccionar el perfil, haga clic en **Habilitar** en la parte inferior de la página.
3. Si está utilizando un nombre de dominio personalizado, cree un registro de recursos CNAME en su servidor DNS de Internet para que apunte al nombre de dominio de su perfil de administrador de tráfico.
4. El tráfico se dirige a los extremos de nuevo.

### <a name="to-delete-a-profile"></a>Para eliminar un perfil

1. Asegúrese de que el registro de recursos DNS de su servidor DNS de Internet ya no utiliza un registro de recursos CNAME que señala al nombre de dominio de su perfil de administrador de tráfico.
2. Seleccione el perfil que desee deshabilitar. En la página Administrador de tráfico, resalte el perfil, haga clic en la columna situada junto al nombre del perfil. Nota, haga clic en el nombre del perfil o en la flecha situada junto al nombre, abrirá la página de configuración del perfil.
3. Después de seleccionar el perfil, haga clic en **Eliminar** en la parte inferior de la página.

## <a name="view-traffic-manager-profile-change-history"></a>Historial de cambios de perfil de administrador de tráfico de vistas

Puede ver el historial de cambios para su perfil de administrador de tráfico en el portal de clásico Azure en servicios de administración.

### <a name="to-view-your-traffic-manager-change-history"></a>Para ver el historial de cambios de administrador de tráfico

1. En el panel izquierdo del portal Azure clásico, haga clic en **Servicios de administración**.
2. En la página Servicios de administración, haga clic en **Registros de operación**.
3. En la página operación registros, puede filtrar para ver el historial de cambios para su perfil de administrador de tráfico. Después de seleccionar las opciones de filtrado, haga clic en la marca de verificación para ver los resultados.

   - Para ver los cambios de todos los perfiles, seleccione la suscripción y el intervalo de tiempo y, a continuación, seleccione **Administrador de tráfico** en el menú contextual de **tipo** .
   - Para filtrar por el nombre del perfil, escriba el nombre del perfil en el campo **Nombre de servicio** o selecciónelo en el menú contextual.
   - Para ver los detalles de cada cambio individual, seleccione la fila con el cambio que desea ver y, a continuación, haga clic en **Detalles** en la parte inferior de la página. En la ventana de **Detalles de la operación** , puede ver la representación XML del objeto API que se creó o se actualiza como parte de la operación.

## <a name="next-steps"></a>Pasos siguientes

- [Agregar un punto final](traffic-manager-endpoints.md)
- [Configurar método de enrutamiento de migración tras error](traffic-manager-configure-failover-routing-method.md)
- [Configurar método de enrutamiento de turnos](traffic-manager-configure-round-robin-routing-method.md)
- [Configurar método de enrutamiento de rendimiento](traffic-manager-configure-performance-routing-method.md)
- [Seleccione un dominio de Internet de la compañía a un nombre de dominio de administrador de tráfico](traffic-manager-point-internet-domain.md)
- [Estado de degradado de solución de problemas de tráfico de administrador](traffic-manager-troubleshooting-degraded.md)