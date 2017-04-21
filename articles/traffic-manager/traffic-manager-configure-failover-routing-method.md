<properties
   pageTitle="Configurar método de enrutamiento de tráfico de migración tras error del Administrador de tráfico | Microsoft Azure"
   description="En este artículo le ayudará a configurar el método de enrutamiento de tráfico de migración tras error en el Administrador de tráfico"
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="sewhee" />
<!-- repub for nofollow -->

# <a name="configure-failover-routing-method"></a>Configurar método de enrutamiento de migración tras error

A menudo una organización desea proporcionar confiabilidad para sus servicios. Para ello, lo que proporciona servicios de copia de seguridad en caso de que su servicio principal deja de funcionar. Un modelo común de migración tras error del servicio es proporcionar un conjunto de servicios idénticos y enviar tráfico a un servicio principal, mientras mantiene una lista configurada de uno o más servicios de copia de seguridad. Puede configurar este tipo de copia de seguridad con los servicios de nube de Azure y sitios Web siguiendo los procedimientos siguientes.

Observe que sitios Web de Azure ya proporciona enrutamiento funcionalidad método para sitios Web dentro de un centro de datos (también conocido como una región), el tráfico de migración tras error independientemente del modo de sitio Web. Administrador de tráfico le permite especificar el método de enrutamiento de tráfico de migración tras error para sitios Web en centros de datos distintos.

## <a name="to-configure-failover-traffic-routing-method"></a>Para configurar el método de enrutamiento de tráfico de migración tras error:

1. En el portal de clásico Azure, en el panel izquierdo, haga clic en el icono de **Tráfico administrador** para abrir el panel de administrador de tráfico. Si aún no ha creado su perfil de administrador de tráfico, vea [Administrar perfiles de tráfico administrador](traffic-manager-manage-profiles.md) para conocer los pasos para crear un perfil de administrador de tráfico básico.
2. En el panel de administrador de tráfico en el portal de clásico Azure, busque el perfil de administrador de tráfico que contiene la configuración que desea modificar y, a continuación, haga clic en la flecha situada a la derecha del nombre del perfil. Se abrirá la página de configuración del perfil.
3. En la página de perfil, haga clic en **extremos de** la parte superior de la página y compruebe que la ambos servicios de nube y sitios Web (extremos) que desea incluir en la configuración está presentes. Para que conocer los pasos agregar o quitar extremos, vea [Administrar extremos en el Administrador de tráfico](traffic-manager-endpoints.md).
4. En la página de perfil, haga clic en **Configurar** en la parte superior para abrir la página de configuración.
5. **Configuración de método de enrutamiento de tráfico**, compruebe que el método de enrutamiento de tráfico es **migración tras error**. Si no es así, haga clic en **migración tras error** en la lista desplegable.
6. Para ver **La lista de prioridad de migración tras error**, ajuste el orden de migración tras error en los extremos. Cuando se selecciona el método de enrutamiento de tráfico de **migración tras error** , es importante el orden de los extremos seleccionados. El extremo principal está en la parte superior. Use las flechas y abajo para cambiar el orden según sea necesario. Para obtener información sobre cómo configurar la prioridad de migración tras error mediante Windows PowerShell, vea [Establecer AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Compruebe que la **Configuración de supervisión** están configurados correctamente. Supervisión garantiza que los extremos de sin conexión no se envían tráfico. Fin de controlar extremos, debe especificar una ruta de acceso y nombre de archivo. Tenga en cuenta que una barra diagonal "/" es una entrada válida para la ruta de acceso relativa e implica que el archivo se encuentra en el directorio raíz (predeterminado). Para obtener más información acerca de cómo supervisar, vea [Supervisión de tráfico de administrador](traffic-manager-monitoring.md).
8. Después de completar los cambios de configuración, haga clic en **Guardar** en la parte inferior de la página.
9. Probar los cambios en la configuración. Para obtener más información, vea [Configuración del Administrador de tráfico de pruebas](traffic-manager-testing-settings.md) .
10. Una vez que el perfil de administrador de tráfico es el programa de instalación y su funcionamiento, edite el registro DNS de su servidor DNS autorizado para que apunte el nombre de dominio de su compañía en el nombre de dominio de administrador de tráfico. Para obtener más información acerca de cómo hacerlo, consulte [punto de un dominio de Internet de empresa a un dominio de administrador de tráfico](traffic-manager-point-internet-domain.md).

## <a name="next-steps"></a>Pasos siguientes

[Seleccione un dominio de Internet de la compañía a un dominio de administrador de tráfico](traffic-manager-point-internet-domain.md)

[Métodos de enrutamiento de tráfico administrador](traffic-manager-routing-methods.md)

[Configurar método de enrutamiento de turnos](traffic-manager-configure-round-robin-routing-method.md)

[Configurar método de enrutamiento de rendimiento](traffic-manager-configure-performance-routing-method.md)

[Estado de degradado de solución de problemas de tráfico de administrador](traffic-manager-troubleshooting-degraded.md)

[Habilitar administrador - deshabilitar, el tráfico o eliminar un perfil](disable-enable-or-delete-a-profile.md)

[Administrador de tráfico - deshabilitar o habilitar un punto final](disable-or-enable-an-endpoint.md)

