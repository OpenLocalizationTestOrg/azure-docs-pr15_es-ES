<properties
   pageTitle="Configurar método de enrutamiento de tráfico de rendimiento | Microsoft Azure"
   description="En este artículo le ayudará a configurar el método de enrutamiento de tráfico de rendimiento en el Administrador de tráfico"
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

# <a name="configure-performance-traffic-routing-method"></a>Configurar método de enrutamiento de tráfico de rendimiento

Para enrutar el tráfico de servicios en la nube y sitios Web (extremos) que se encuentra en los centros de datos distintos en todo el mundo (también conocido como regiones), puede dirigir el tráfico entrante al extremo con la menor latencia desde el cliente solicita. Normalmente, el centro de datos con la menor latencia corresponde a la más cercana en distancia geográfica. El método de enrutamiento de tráfico de rendimiento le permitirá distribuir en función de latencia más baja, pero no puede tener en cuenta la evolución en tiempo real en la configuración de red o de carga. Para obtener más información sobre los métodos de enrutamiento de tráfico diferentes que proporciona el administrador del tráfico de Azure, consulte [métodos de enrutamiento de tráfico sobre el Administrador de tráfico](traffic-manager-routing-methods.md).

## <a name="route-traffic-based-on-lowest-latency-across-a-set-of-endpoints"></a>Enrutar el tráfico en función de latencia más baja de un conjunto de puntos finales:

1. En el portal de clásico Azure, en el panel izquierdo, haga clic en el icono de **Tráfico administrador** para abrir el panel de administrador de tráfico. Si aún no ha creado su perfil de administrador de tráfico, vea [Administrar perfiles de tráfico administrador](traffic-manager-manage-profiles.md) para ver los pasos para crear un perfil de administrador de tráfico básico.
2. En el portal de clásico Azure, en el panel Administrador de tráfico, busque el perfil de administrador de tráfico que contiene la configuración que desea modificar y, a continuación, haga clic en la flecha situada a la derecha del nombre del perfil. Se abrirá la página de configuración del perfil.
3. En la página de su perfil, haga clic en los **extremos de** la parte superior de la página y compruebe que están presentes los extremos de servicio que desea incluir en la configuración. Para que conocer los pasos agregar o quitar extremos de su perfil, vea [Administrar extremos en el Administrador de tráfico](traffic-manager-endpoints.md).
4. En la página de su perfil, haga clic en **Configurar** en la parte superior para abrir la página de configuración.
5. **Configuración de método de enrutamiento de tráfico**, compruebe que el método de enrutamiento de tráfico es * *rendimiento*. Si no es así, haga clic en * *rendimiento** en la lista desplegable.
6. Compruebe que la **Configuración de supervisión** están configurados correctamente. Supervisión garantiza que los extremos de sin conexión no se envían tráfico. Fin de controlar extremos, debe especificar una ruta de acceso y nombre de archivo. Tenga en cuenta que una barra diagonal "/" es una entrada válida para la ruta de acceso relativa e implica que el archivo se encuentra en el directorio raíz (predeterminado). Para obtener más información acerca de cómo supervisar, vea [Acerca de supervisión Administrador de tráfico](traffic-manager-monitoring.md).
7. Después de completar los cambios de configuración, haga clic en **Guardar** en la parte inferior de la página.
8. Probar los cambios en la configuración. Para obtener más información, vea [Configuración del Administrador de tráfico de pruebas](traffic-manager-testing-settings.md).
9. Una vez que el perfil de administrador de tráfico es el programa de instalación y su funcionamiento, edite el registro DNS de su servidor DNS autorizado para que apunte el nombre de dominio de su compañía en el nombre de dominio de administrador de tráfico. Para obtener más información acerca de cómo hacerlo, consulte [punto de un dominio de Internet de empresa a un dominio de administrador de tráfico](traffic-manager-point-internet-domain.md).

## <a name="next-steps"></a>Pasos siguientes


[Seleccione un dominio de Internet de la compañía a un dominio de administrador de tráfico](traffic-manager-point-internet-domain.md)

[Métodos de enrutamiento de tráfico administrador](traffic-manager-routing-methods.md)

[Configurar método de enrutamiento de migración tras error](traffic-manager-configure-failover-routing-method.md)

[Configurar método de enrutamiento de turnos](traffic-manager-configure-round-robin-routing-method.md)

[Estado de degradado de solución de problemas de tráfico de administrador](traffic-manager-troubleshooting-degraded.md)

[Habilitar administrador - deshabilitar, el tráfico o eliminar un perfil](disable-enable-or-delete-a-profile.md)

[Administrador de tráfico - deshabilitar o habilitar un punto final](disable-or-enable-an-endpoint.md)

