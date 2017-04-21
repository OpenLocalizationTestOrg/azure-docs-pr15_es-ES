<properties
    pageTitle="Tipos de extremo de administrador de tráfico | Microsoft Azure"
    description="Este artículo explican los diferentes tipos de extremos que se pueden usar con el administrador del tráfico de Azure"
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
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="traffic-manager-endpoints"></a>Administrador de tráfico extremos

Administrador de tráfico de Microsoft Azure le permite controlar cómo se distribuye el tráfico de red para las implementaciones de aplicaciones que se ejecuta en centros de datos distintos. Configurar cada aplicación de la implementación como un extremo de' ' en el Administrador de tráfico. Al administrador de tráfico recibe una solicitud DNS, elige un extremo que se devolverán en la respuesta DNS. Administrador de tráfico basa la opción en el estado actual de punto final y el método de enrutamiento de tráfico. Para obtener más información, vea [Cómo un administrador de tráfico funciona](traffic-manager-how-traffic-manager-works.md).

Hay tres tipos de extremo compatible con el Administrador de tráfico:

- **Extremos de Azure** se utilizan para servicios alojados en Azure.
- **Extremos externos** se usan para servicios hospedados fuera de Azure, ya sea en modo local o con otro proveedor de hospedaje.
- **Extremos anidadas** se utilizan para combinar perfiles de administrador de tráfico para crear combinaciones de enrutamiento de tráfico más flexibles para satisfacer las necesidades de implementaciones más grandes y complejas.

No hay ninguna restricción en cómo se combinan los extremos de los diferentes tipos en un único perfil de administrador de tráfico. Cada perfil puede incluir cualquier combinación de tipos de extremo.

Las secciones siguientes describen cada tipo de extremo en mayor profundidad.

## <a name="azure-endpoints"></a>Extremos de Azure

Extremos de Azure se utilizan para servicios basados en Azure en el Administrador de tráfico. Se admiten los siguientes tipos de recursos de Azure:

- Servicios de nube 'Clásica' VM IaaS y PaaS.
- Aplicaciones Web
- Recursos de PublicIPAddress (que se pueden conectar a máquinas virtuales directamente o a través de un equilibrador de carga de Azure). El publicIpAddress debe tener un nombre DNS asignado a usar en un perfil de administrador de tráfico.

Recursos de PublicIPAddress son el Administrador de recursos de Azure. No existen en el modelo de implementación clásico. Por lo tanto son experiencias de administrador de recursos de Azure solo admite en el tráfico del administrador. Otros tipos de extremo son compatibles mediante el Administrador de recursos y el modelo de implementación clásica.

Cuando se usa extremos Azure, Administrador de tráfico detecta cuando se detiene y se inicia una 'Estándar' IaaS VM, servicio de nube o una aplicación Web. Este estado se refleja en el estado de extremo. Para obtener más información, vea [supervisión de extremo de administrador de tráfico](traffic-manager-monitoring.md#endpoint-and-profile-status) . Cuando se detiene el servicio subyacente, Administrador de tráfico no realiza comprobaciones de estado del extremo o dirigir el tráfico al extremo. Administrador de tráfico no se producen eventos de facturación de la instancia ha dejado. Cuando se reinicia el servicio, currículos de facturación y el extremo es elegible para recibir tráfico. Esta detección no se aplica a los extremos de PublicIpAddress.

## <a name="external-endpoints"></a>Extremos externos

Extremos externos se usan para servicios fuera de Azure. Por ejemplo, un servicio hospedado en local o con otro proveedor. Extremos externos pueden utilizarse individualmente o combinados con extremos de Azure en el mismo perfil de administrador de tráfico. Combinar extremos Azure con extremos externos permite diversas situaciones:

- En un modelo de activo / activo o activo pasivo por, utilice Azure para proporcionar una mayor redundancia para una existente aplicación local.
- Para reducir la latencia de la aplicación para los usuarios de todo el mundo, extender una aplicación existente de local a otras ubicaciones geográficas en Azure. Para obtener más información, vea [tráfico administrador 'Rendimiento' enrutamiento de tráfico](traffic-manager-routing-methods.md#performance-traffic-routing-method).
- Uso de Azure para proporcionar capacidad adicional para una existente local aplicación, continuamente o como una solución de ráfaga en nube para satisfacer un aumento en la demanda.

En algunos casos, es muy útil para usar extremos externos para hacer referencia a los servicios de Azure (para obtener ejemplos, consulte las [preguntas más frecuentes](#faq)). En este caso, se cargarán comprobaciones de estado a la tasa de Azure extremos, no la tasa de extremos externos. Sin embargo, a diferencia de los extremos de Azure, si detener o eliminar el servicio subyacente, estado comprobar facturación continúa hasta deshabilitar o eliminar el extremo en el Administrador de tráfico.

## <a name="nested-endpoints"></a>Extremos anidadas

Extremos anidadas combinan varios perfiles de administrador de tráfico para crear combinaciones de enrutamiento de tráfico flexibles y satisfacer las necesidades de implementaciones grandes y complejas. Con extremos anidadas, se agrega un perfil 'child' como un punto final a un perfil 'principal'. Perfiles de primarios y secundarios pueden contener otros extremos de cualquier tipo, incluidos otros perfiles anidadas. Para obtener más información, vea [anidadas perfiles de administrador de tráfico](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Aplicaciones como extremos de Web

Al configurar aplicaciones Web como extremos en el Administrador de tráfico, se aplican algunas consideraciones adicionales:

1. Solo Web Apps en el SKU 'Estándar' o superior son válidas para su uso con el Administrador de tráfico. Un error si intenta agregar una aplicación Web de un SKU inferior. Bajar el nivel de la SKU de una aplicación Web existente de resultados en el Administrador de tráfico ya no enviar tráfico a esa aplicación Web.

2. Cuando un extremo recibe una solicitud HTTP, utiliza el encabezado 'host' en la solicitud para determinar qué aplicación Web debe la solicitud de servicio. El encabezado de host contiene el nombre DNS usado para iniciar la solicitud, por ejemplo 'contosoapp.azurewebsites.net'. Para usar un nombre DNS diferente con la aplicación Web, el nombre DNS debe estar registrado como un nombre de dominio personalizado para la aplicación. Cuando se agrega un extremo de la aplicación Web como un extremo de Azure, el nombre DNS del perfil de administrador de tráfico se registra automáticamente para la aplicación. Este registro se elimina automáticamente cuando se elimina el extremo.

3. Cada perfil de administrador de tráfico puede tener al menos un extremo de la aplicación Web de cada región de Azure. Para evitar para esta restricción, puede configurar una aplicación Web como un extremo externo. Para obtener más información, consulte las [preguntas más frecuentes](#faq).

## <a name="enabling-and-disabling-endpoints"></a>Habilitar y deshabilitar extremos

Deshabilitar un extremo en el Administrador de tráfico puede ser útil para quitar temporalmente el tráfico de un extremo que esté en modo de mantenimiento o se implemente de nuevo. Una vez que se está ejecutando el punto final de nuevo, puede volver a habilitar.

Extremos pueden habilitar y deshabilitar a través del portal de administrador de tráfico, PowerShell, CLI o API de REST, que son compatibles con el Administrador de recursos y el modelo de implementación clásica.

>[AZURE.NOTE] Deshabilitar un extremo Azure no tiene ninguna relación con su estado de implementación de Azure. Un servicio de Azure (como una máquina virtual o la aplicación Web permanece ejecutando y puede recibir el tráfico aunque deshabilitado en el Administrador de tráfico. Puede solucionar tráfico directamente a la instancia de servicio, en lugar de mediante el nombre DNS del perfil de administrador de tráfico. Para obtener más información, consulte [funcionamiento del Administrador de tráfico](traffic-manager-how-traffic-manager-works.md).

La elegibilidad actual de cada extremo para recibir tráfico depende de los siguientes factores:

- El estado de perfil (habilitado o deshabilitado)
- El estado de extremo (habilitado o deshabilitado)
- Comprueba los resultados de la salud de dicho extremo

Para obtener más información, vea [supervisión de extremo de administrador de tráfico](traffic-manager-monitoring.md#endpoint-and-profile-status).

>[AZURE.NOTE] Dado que el Administrador de tráfico funciona en el nivel DNS, es no se puede influir en las conexiones existentes a cualquier extremo. Cuando un extremo no está disponible, el Administrador de tráfico dirige nuevas conexiones con el otro extremo. Sin embargo, el host tras el extremo deshabilitado o mal estado puede continuar recibiendo tráfico a través de conexiones existentes hasta que terminan las sesiones. Aplicaciones deben limitar la duración de sesión para permitir el tráfico se filtre conexiones existentes.

Si se deshabilitan todos los extremos de un perfil, o si se deshabilita el propio perfil, el tráfico administrador envía una respuesta de 'NXDOMAIN' a una nueva consulta DNS.

## <a name="faq"></a>Preguntas más frecuentes

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>¿Puedo usar el Administrador de tráfico con extremos de varias suscripciones?

Usar extremos de varias suscripciones no es posible con aplicaciones Web de Azure. Aplicaciones Web de Azure requiere que cualquier nombre de dominio personalizado que se utiliza con aplicaciones Web solo se usa dentro de una sola suscripción. No es posible usar las aplicaciones Web de varias suscripciones con el mismo nombre de dominio.

Para otros tipos de extremo, es posible usar el Administrador de tráfico con extremos de más de una suscripción. Cómo configurar el Administrador de tráfico depende de si está utilizando el modelo de implementación clásico o la experiencia del Administrador de recursos.

- En el Administrador de recursos, puntos finales de cualquier suscripción se pueden agregar al administrador de tráfico, siempre y cuando la persona que configura el perfil de administrador de tráfico tiene acceso de lectura al extremo. Pueden conceder estos permisos con [control de acceso basado en roles de administrador de recursos de Azure (RBAC)](../active-directory/role-based-access-control-configure.md).
- En la interfaz de modelo de implementación clásica, Administrador de tráfico requiere que los servicios de nube o aplicaciones Web está configurado como extremos Azure residen en la misma suscripción como el perfil de administrador de tráfico. Extremos de servicio de nube de otras suscripciones pueden agregarse al administrador de tráfico como extremos 'externos'. Estos extremos externos se cargarán como puntos finales de Azure en lugar de la tasa externa.

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>¿Puedo usar el Administrador de tráfico con ranuras de servicio de nube 'Provisional'?

Sí. Servicio de nube 'provisional' ranuras se puede configurar en el Administrador de tráfico como extremos externos. Comprobaciones de estado aún se cargará a la tasa de extremos de Azure. Dado que el tipo de extremo externo está en uso, cambios en el servicio subyacente no se recogen automáticamente. Con extremos externos, Administrador de tráfico no puede detectar cuando se detiene o se elimina del servicio de nube. Por lo tanto, el Administrador de tráfico continúa facturación para comprobaciones de estado hasta el extremo se deshabilita o elimina.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>¿El Administrador de tráfico admite IPv6 extremos?

Administrador de tráfico no ofrece actualmente IPv6 addressible servidores de nombres. Sin embargo, el Administrador de tráfico aún puede ser usado por IPv6 clientes que se conectan a los extremos de IPv6. Un cliente no realiza solicitudes DNS directamente al administrador de tráfico. En su lugar, el cliente utiliza un servicio DNS recursiva. Un cliente sólo IPv6 envía solicitudes al servicio DNS recursiva a través de IPv6. A continuación, el servicio recursiva debería poder ponerse en contacto con los servidores de nombres del Administrador de tráfico usando IPv4.

Administrador de tráfico responde con el nombre DNS del extremo. Para admitir un extremo IPv6, debe existir un registro DNS AAAA señalando el nombre del extremo de la dirección IPv6. Comprobaciones de estado de administrador de tráfico solo admiten direcciones IPv4. El servicio debe exponer un extremo de IPv4 en el mismo nombre DNS.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>¿Puedo usar el Administrador de tráfico con más de una aplicación Web de la misma región?

Normalmente, el Administrador de tráfico se utiliza para dirigir el tráfico a aplicaciones implementado en diferentes regiones. Sin embargo, también se puede utilizar en una aplicación tiene más de una implementación de la misma región. Los extremos de Azure tráfico Manager no permitir más de un extremo de la aplicación Web de la misma región Azure agregarse al mismo perfil de administrador de tráfico.

Los pasos siguientes proporcionan una solución a esta restricción:

1. Compruebe que los extremos están en distintos web app 'unidades de escala'. Un nombre de dominio debe asignar a un único sitio en una unidad de escala. Por lo tanto, las dos aplicaciones Web en la misma unidad de escala no puede compartir un perfil de administrador de tráfico.
2. Agregue el nombre de dominio de cortesía como un nombre de host personalizado para cada aplicación Web. Cada aplicación Web debe estar en una unidad de escala diferente. Todas las aplicaciones Web debe pertenecer a la misma suscripción.
3. Agregar una (y sólo una) extremo de aplicación Web a su perfil de administrador de tráfico, como un extremo de Azure.
4. Agregar cada extremo de la aplicación Web adicional a su perfil de administrador de tráfico como un extremo externo. Extremos externos pueden agregarse utiliza el modelo de implementación de administrador de recursos.
5. Crear un registro CNAME de DNS en su dominio de cortesía que apunta a su nombre DNS del perfil de administrador de tráfico (<> …. trafficmanager.net).
6. Obtener acceso a su sitio mediante el nombre de dominio de cortesía, no el nombre del DNS de perfil de administrador de tráfico.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre el [funcionamiento del Administrador de tráfico](traffic-manager-how-traffic-manager-works.md).
- Obtenga información sobre el Administrador de tráfico [migración tras error automática y la supervisión de extremo](traffic-manager-monitoring.md).
- Obtenga información acerca del Administrador de tráfico de [métodos de enrutamiento de tráfico](traffic-manager-routing-methods.md).
