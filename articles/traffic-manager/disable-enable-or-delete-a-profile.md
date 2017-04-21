<properties
   pageTitle="Deshabilitar, habilitar o eliminar un perfil de administrador de tráfico | Microsoft Azure"
   description="En este artículo le ayudará a trabajar con los perfiles de administrador de tráfico."
   services="traffic-manager"
   documentationCenter="na"
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

# <a name="disable-enable-or-delete-a-profile"></a>Deshabilitar, habilitar o eliminar un perfil


Puede deshabilitar un perfil de administrador de tráfico existente para que no hará referencia solicitudes de usuario a sus extremos configurados. Cuando se deshabilita un perfil de administrador de tráfico, el propio perfil y la información contenida en el perfil permanecerá intacta y se puede modificar en la interfaz de administrador de tráfico. Cuando desee volver a habilitar el perfil, puede fácilmente hacerlo en la Azure continuará portal y referencias. Cuando se crea un perfil de administrador de tráfico en el portal de Azure, se activa automáticamente.

## <a name="to-disable-a-profile"></a>Para deshabilitar un perfil

1. Modificar el registro de recursos DNS en su servidor DNS de Internet para usar el tipo de registro correspondiente y el puntero a otro nombre o la dirección IP de una ubicación específica en Internet. En otras palabras, cambie el registro de recursos DNS de su servidor DNS de Internet para que ya no utiliza un registro de recursos CNAME que señala al nombre de dominio de su perfil de administrador de tráfico.
1. Deja de tráfico que se dirige a los extremos a través de la configuración del perfil de administrador de tráfico.
1. Seleccione el perfil que desee deshabilitar. Para seleccionar el perfil, en la página Administrador de tráfico, resalte el perfil, haga clic en la columna situada junto al nombre del perfil. No haga clic en el nombre del perfil o en la flecha junto al nombre, esto le llevará a la página de configuración del perfil.
1. Después de seleccionar el perfil, haga clic en desactivar en la parte inferior de la página.

## <a name="to-enable-a-profile"></a>Para habilitar un perfil

1. Seleccione el perfil que desea habilitar. Para seleccionar el perfil, en la página Administrador de tráfico, resalte el perfil, haga clic en la columna situada junto al nombre del perfil. No haga clic en el nombre del perfil o en la flecha junto al nombre, esto le llevará a la página de configuración del perfil.
1. Después de seleccionar el perfil, haga clic en habilitar en la parte inferior de la página.
1. Modificar el registro de recursos DNS de su servidor DNS de Internet para usar el tipo de registro CNAME, que asigna el nombre de dominio de su compañía en el nombre de dominio de su perfil de administrador de tráfico. Para obtener más información, consulte [punto de un dominio de Internet de empresa a un dominio de administrador de tráfico](traffic-manager-point-internet-domain.md).
1. Iniciará el tráfico que se dirigen a los extremos de nuevo.

## <a name="delete-a-profile"></a>Eliminar un perfil


1. Asegúrese de que el registro de recursos DNS de su servidor DNS de Internet ya no utiliza un registro de recursos CNAME que señala al nombre de dominio de su perfil de administrador de tráfico.
1. Seleccione el perfil que desee eliminar. Para seleccionar el perfil, en la página Administrador de tráfico, resalte el perfil por
1. Haga clic en la columna situada junto al perfil. No haga clic en el nombre del perfil o en la flecha junto al nombre, esto le llevará a la página de configuración del perfil.
1. Después de seleccionar el perfil, haga clic en Eliminar en la parte inferior de la página.

## <a name="next-steps"></a>Pasos siguientes

[Administrador de tráfico - deshabilitar o habilitar un punto final](disable-or-enable-an-endpoint.md)

[Configurar método de enrutamiento de migración tras error](traffic-manager-configure-failover-routing-method.md)

[Configurar método de enrutamiento de turnos](traffic-manager-configure-round-robin-routing-method.md)

[Configurar método de enrutamiento de rendimiento](traffic-manager-configure-performance-routing-method.md)

[Estado de degradado de solución de problemas de tráfico de administrador](traffic-manager-troubleshooting-degraded.md)

