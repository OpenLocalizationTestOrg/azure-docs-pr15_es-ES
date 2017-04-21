<properties
    pageTitle="Configurar métodos de enrutamiento de tráfico Manager | Microsoft Azure"
    description="En este artículo se explica cómo se configura en el Administrador de tráfico de diferentes métodos de enrutamiento"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="sewhee" />
<!-- repub for nofollow -->

# <a name="configure-traffic-manager-routing-methods"></a>Configurar métodos de enrutamiento de tráfico administrador

Administrador de tráfico Azure proporciona tres métodos de enrutamiento que controlan cómo se enruta el tráfico a extremos de servicio disponible. El método de enrutamiento de tráfico se aplica a cada consulta DNS recibido para determinar qué extremo se debe devolver en la respuesta DNS.

Existen tres métodos de enrutamiento de tráfico disponibles en el Administrador de tráfico:

- **Prioridad:** Seleccione 'Priority' cuando desee utilizar un extremo de servicio principal y proporcionar las copias de seguridad en caso de que el primario no está disponible.
- **Ponderado:** SELECT '' cuando desea distribuir el tráfico a través de un conjunto de puntos finales, ponderada uniformemente o según grosores, que defina.
- **Rendimiento:** Seleccione 'Rendimiento' cuando tiene extremos en diferentes ubicaciones geográficas y quiere que los usuarios finales utilizar el extremo "más próximo" en términos de la latencia de red más bajo.

## <a name="configure-priority-routing-method"></a>Configurar método de enrutamiento de prioridad

Independientemente del modo de sitio Web, sitios Web de Azure proporcionar funcionalidad de migración tras error para sitios Web dentro de un centro de datos (también conocido como una región). Administrador de tráfico proporciona migración tras error para sitios Web en los centros de datos distintos.

Un modelo común de migración tras error del servicio es enviar tráfico a un servicio principal y proporcionar un conjunto de servicios de copia de seguridad idéntico de migración tras error. Los siguientes pasos explican cómo configurar esta migración tras error prioridades con sitios Web y servicios de nube de Azure:

1. En el portal de clásico Azure, en el panel izquierdo, haga clic en el icono de **Tráfico administrador** para abrir el panel de administrador de tráfico.
2. En el panel de administrador de tráfico en el portal de clásico Azure, busque el perfil de administrador de tráfico que contiene la configuración que desea modificar. Para abrir la página de configuración de perfil, haga clic en la flecha situada a la derecha del nombre del perfil.
3. En la página de perfil, haga clic en **extremos de** la parte superior de la página. Compruebe que existen los servicios en la nube y sitios Web que desea incluir en la configuración.
4. En la parte superior para abrir la página de configuración, haga clic en **Configurar** .
5. **Configuración de método de enrutamiento de tráfico**, compruebe que el método de enrutamiento de tráfico es **migración tras error**. Si no es así, haga clic en **migración tras error** en la lista desplegable.
6. Para ver **La lista de prioridad de migración tras error**, ajuste el orden de migración tras error en los extremos. Cuando se selecciona el método de enrutamiento de tráfico de **migración tras error** , es importante el orden de los extremos seleccionados. El extremo principal está en la parte superior. Use las flechas y abajo para cambiar el orden según sea necesario. Para obtener información sobre cómo configurar la prioridad de migración tras error mediante Windows PowerShell, vea [Establecer AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Compruebe que la **Configuración de supervisión** están configurados correctamente. Supervisión garantiza que los extremos de sin conexión no se envían tráfico. Para supervisar extremos, debe especificar una ruta de acceso y nombre de archivo. Barra diagonal "/" es una entrada válida para la ruta de acceso relativa a implica que el archivo se encuentra en el directorio raíz (predeterminado).
8. Después de completar los cambios de configuración, haga clic en **Guardar** en la parte inferior de la página.
9. Probar los cambios en la configuración.
10. Una vez que el perfil de administrador de tráfico está trabajando, edite el registro DNS de su servidor DNS autorizado para que apunte el nombre de dominio de su compañía en el nombre de dominio de administrador de tráfico.

## <a name="configure-weighted-routing-method"></a>Configurar método de enrutamiento ponderada

Un modelo de método enrutamiento de tráfico común es proporcionar un conjunto de extremos idénticos, que incluyen servicios en la nube y sitios Web, y enviar tráfico a cada una en una forma circular. Los pasos siguientes describen cómo configurar este tipo de método de enrutamiento de tráfico.

>[AZURE.NOTE] Sitios Web Azure proporciona funcionalidad para sitios Web dentro de un centro de datos (también conocido como una región) de equilibrio de carga de turnos. Administrador de tráfico le permite especificar el método de enrutamiento de tráfico de turnos para sitios Web en centros de datos distintos.

1. En el portal de clásico Azure, en el panel izquierdo, haga clic en el icono de **Tráfico administrador** para abrir el panel de administrador de tráfico.
2. En el panel de administrador de tráfico, busque el perfil de administrador de tráfico que contiene la configuración que desea modificar. Para abrir la página de configuración de perfil, haga clic en la flecha situada a la derecha del nombre del perfil.
3. En la página de su perfil, haga clic en los **extremos de** la parte superior de la página y compruebe que están presentes los extremos de servicio que desea incluir en la configuración.
4. En la página de perfil, haga clic en **Configurar** en la parte superior para abrir la página de configuración.
5. Para el **método de enrutamiento de tráfico configuración**, compruebe que el método de enrutamiento de tráfico es **turnos**. Si no es así, haga clic en **turnos** en la lista desplegable.
6. Compruebe que la **Configuración de supervisión** están configurados correctamente. Supervisión garantiza que los extremos de sin conexión no se envían tráfico. Para supervisar extremos, debe especificar una ruta de acceso y nombre de archivo. Barra diagonal "/" es una entrada válida para la ruta de acceso relativa a implica que el archivo se encuentra en el directorio raíz (predeterminado).
7. Después de completar los cambios de configuración, haga clic en **Guardar** en la parte inferior de la página.
8. Probar los cambios en la configuración.
9. Una vez que el perfil de administrador de tráfico está trabajando, edite el registro DNS de su servidor DNS autorizado para que apunte el nombre de dominio de su compañía en el nombre de dominio de administrador de tráfico.

## <a name="configure-performance-traffic-routing-method"></a>Configurar método de enrutamiento de tráfico de rendimiento

El método de enrutamiento de tráfico de rendimiento le permite dirigir el tráfico al extremo con la menor latencia de la red del cliente. Normalmente, el centro de datos con la menor latencia es la más cercana en distancia geográfica. Este método de enrutamiento de tráfico no puede contabilizar los cambios en tiempo real en la configuración de red o cargar.

1. En el portal de clásico Azure, en el panel izquierdo, haga clic en el icono de **Tráfico administrador** para abrir el panel de administrador de tráfico.
2. En el panel de administrador de tráfico, busque el perfil de administrador de tráfico que contiene la configuración que desea modificar. Para abrir la página de configuración de perfil, haga clic en la flecha situada a la derecha del nombre del perfil.
3. En la página de su perfil, haga clic en los **extremos de** la parte superior de la página y compruebe que están presentes los extremos de servicio que desea incluir en la configuración.
4. En la página de su perfil, haga clic en **Configurar** en la parte superior para abrir la página de configuración.
5. **Configuración de método de enrutamiento de tráfico**, compruebe que el método de enrutamiento de tráfico es * *rendimiento*. Si no es así, haga clic en * *rendimiento** en la lista desplegable.
6. Compruebe que la **Configuración de supervisión** están configurados correctamente. Supervisión garantiza que los extremos de sin conexión no se envían tráfico. Para supervisar extremos, debe especificar una ruta de acceso y nombre de archivo. Barra diagonal "/" es una entrada válida para la ruta de acceso relativa a implica que el archivo se encuentra en el directorio raíz (predeterminado).
7. Después de completar los cambios de configuración, haga clic en **Guardar** en la parte inferior de la página.
8. Probar los cambios en la configuración.
9. Una vez que el perfil de administrador de tráfico está trabajando, edite el registro DNS de su servidor DNS autorizado para que apunte el nombre de dominio de su compañía en el nombre de dominio de administrador de tráfico.

## <a name="next-steps"></a>Pasos siguientes

* [Administrar perfiles de administrador de tráfico](traffic-manager-manage-profiles.md)
* [Métodos de enrutamiento de tráfico administrador](traffic-manager-routing-methods.md)
* [Probar la configuración de administrador de tráfico](traffic-manager-testing-settings.md)
* [Seleccione un dominio de Internet de la compañía a un dominio de administrador de tráfico](traffic-manager-point-internet-domain.md)
* [Administrar los extremos de tráfico administrador](traffic-manager-manage-endpoints.md)
* [Estado de degradado de solución de problemas de tráfico de administrador](traffic-manager-troubleshooting-degraded.md)
