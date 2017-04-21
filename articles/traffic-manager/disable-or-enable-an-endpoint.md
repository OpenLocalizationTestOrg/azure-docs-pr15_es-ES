<properties
   pageTitle="Deshabilitar o habilitar un extremo de tráfico Manager | Microsoft Azure"
   description="En este artículo le ayudará a deshabilitar o habilitar los extremos de perfil de administrador de tráfico."
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

# <a name="disable-or-enable-a-traffic-manager-endpoint"></a>Deshabilitar o habilitar un extremo de tráfico de administrador

También puede deshabilitar extremos individuales que forman parte de un perfil de administrador de tráfico. Extremos incluyen servicios en la nube y sitios Web. Deshabilitar un extremo deja como parte del perfil, pero el perfil que se comporta como si el extremo no está incluido en él. Esta acción es muy útil para quitar temporalmente un punto final que esté en modo de mantenimiento o se implemente de nuevo. Una vez que el extremo es nuevo en funcionamiento, puede estar habilitado

>[AZURE.NOTE] **Deshabilitar un extremo no tiene ninguna relación con su estado de implementación de Azure. Un buen estado extremo permanecerán hacia arriba y puede recibir tráfico incluso cuando deshabilitado en el Administrador de tráfico. Además, deshabilitar un extremo de un perfil no afecta a su estado en otro perfil.**

## <a name="to-disable-an-endpoint"></a>Para deshabilitar un punto final

1. En el panel de administrador de tráfico en el portal de clásico Azure, busque el perfil de administrador de tráfico que contiene la configuración de extremo que desea modificar y, a continuación, haga clic en la flecha situada a la derecha del nombre del perfil. Se abrirá la página de configuración del perfil.
1. En la parte superior de la página, haga clic en **extremos** para ver los extremos que se incluyen en la configuración.
1. Haga clic en el extremo que desea deshabilitar y, a continuación, haga clic en **Deshabilitar** en la parte inferior de la página.
1. Deja de tráfico que fluye al extremo basado en el DNS tiempo de vida (TTL) configurado para el nombre de dominio de administrador de tráfico. Puede cambiar el valor de TTL desde la página Configuración del perfil de administrador de tráfico.

## <a name="to-enable-an-endpoint"></a>Para habilitar un punto final


1. En el panel de administrador de tráfico en el portal de clásico Azure, busque el perfil de administrador de tráfico que contiene la configuración de extremo que desea modificar y, a continuación, haga clic en la flecha situada a la derecha del nombre del perfil. Se abrirá la página de configuración del perfil.
1. En la parte superior de la página, haga clic en **extremos** para ver los extremos que se incluyen en la configuración.
1. Haga clic en el extremo que desea habilitar y, a continuación, haga clic en **Habilitar** en la parte inferior de la página.
1. Iniciará el tráfico que fluye en el servicio nuevo como dictado por el perfil.

## <a name="next-steps"></a>Pasos siguientes

[Habilitar administrador - deshabilitar, el tráfico o eliminar un perfil](disable-enable-or-delete-a-profile.md)

[Estado de degradado de solución de problemas de tráfico de administrador](traffic-manager-troubleshooting-degraded.md)

[Consideraciones de rendimiento del Administrador de tráfico](traffic-manager-performance-considerations.md)