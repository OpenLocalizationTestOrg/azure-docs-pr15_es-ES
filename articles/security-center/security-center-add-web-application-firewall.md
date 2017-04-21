<properties
   pageTitle="Agregar un servidor de seguridad de la aplicación web en el centro de seguridad de Azure | Microsoft Azure"
   description="Este documento muestra cómo implementar las recomendaciones de centro de seguridad de Azure **Agregar un servidor de aplicaciones web** y **protección de la aplicación de Finalize**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Agregar un servidor de seguridad de la aplicación web en el centro de seguridad de Azure

Centro de seguridad de Azure puede recomendar agregar un servidor de aplicaciones web (WAFS) de un asociado de Microsoft para proteger sus aplicaciones web. Este documento le guiará a través de un ejemplo de cómo hacerlo.

> [AZURE.NOTE] Este documento presenta el servicio mediante el uso de una implementación de ejemplo.  No es una guía paso a paso.

## <a name="implement-the-recommendation"></a>Implementar la recomendación

1. En el módulo de **recomendaciones** , seleccione **proteger la aplicación web mediante el servidor de aplicaciones web**.
![Aplicación de web segura][1]

2. En el módulo **proteger aplicaciones web mediante el servidor de aplicaciones web** , seleccione una aplicación web. Se abre el módulo de **Agregar un servidor de aplicaciones Web** .
![Agregar un servidor de aplicaciones web][2]
3. Puede usar un servidor de aplicaciones web existente si está disponible, o puede crear uno nuevo. En este ejemplo no existen WAFs existentes para que vamos a crear un nuevo WAFS.

4. Para crear un nuevo WAFS, seleccione una solución en la lista de socios integrados. En este ejemplo seleccionamos **Firewall de aplicación Web de Barracuda**.
5. El módulo de **Servidor de aplicaciones Web de Barracuda** abre proporcionar información sobre la solución de partner. Seleccione **crear** en el módulo de información.
![Módulo de información del Firewall][3]

6. Se abre el módulo de **Servidor de aplicaciones Web nuevo** , donde puede llevar a cabo los pasos de **Configuración de la máquina** y proporcione **Información WAFS**. Seleccione la **configuración de la máquina virtual**.

7. En el módulo de **Configuración de la máquina** se introduce información necesaria para el control de la máquina virtual que se ejecutará la WAFS.
![Configuración de la máquina virtual][4]
8. Vuelva a módulo de **Servidor de aplicaciones Web nueva** y seleccione **WAFS información**. En el módulo **WAFS información** para configurar la WAFS propiamente dicho. Paso 7 le permite configurar la máquina virtual en el que se ejecutarán la WAFS y paso 8 le permite aprovisionar la WAFS propiamente dicho.

## <a name="finalize-application-protection"></a>Finalizar la protección de la aplicación

1. Volver a la hoja de **recomendaciones** . Una vez creado el WAFS, denominado **Finalize protección de la aplicación**, se genera una nueva entrada. Esta entrada permite saber que necesita completar el proceso de cableado realmente hacia arriba la WAFS dentro de la red Virtual de Azure para que puede proteger la aplicación.
![Finalizar la protección de la aplicación][5]

2. Seleccione la **protección de la aplicación de Finalize**. Se abrirá un nuevo módulo. Puede ver que hay una aplicación web que debe tener el tráfico que se transfiere.
3. Seleccione la aplicación web. Se abre un módulo que proporciona pasos para finalizar la configuración de firewall de la aplicación web. Complete los pasos y, a continuación, seleccione **restringir tráfico**. Centro de seguridad se siga cableado telefónico por usted.
![Restringir el tráfico][6]

> [AZURE.NOTE] Puede proteger varias aplicaciones web en el centro de seguridad agregando estas aplicaciones para las implementaciones WAFS existentes. WAFS (creados con el modelo de implementación de administrador de recursos) tendrán que implementará en una red virtual independiente. Dispositivos WAFS (creados con el modelo de implementación clásico) están limitados a usar un grupo de seguridad de la red. Esta compatibilidad se extiende a una implementación completamente personalizada de un dispositivo WAFS (clásico) en el futuro. Más información sobre el [estándar y los modelos de implementación de administrador de recursos](../azure-classic-rm.md) para los recursos de Azure.

Los registros de ese WAFS ahora se integran completamente. Centro de seguridad puede iniciar automáticamente recopilar y analizar los registros para que los que puede tener las alertas de seguridad importantes para usted.

## <a name="see-also"></a>Vea también

Este documento le mostramos cómo implementar la recomendación del centro de seguridad "Agregar una aplicación web". Para obtener más información acerca de cómo configurar un servidor de aplicaciones web, consulte lo siguiente:

- [Configurar un servidor de aplicaciones Web (WAFS) en el entorno de servicio de aplicaciones](../app-service-web/app-service-app-service-environment-web-application-firewall.md)

Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md) : Obtenga información sobre cómo configurar directivas de seguridad para suscripciones de Azure y grupos de recursos.
- [Supervisión del estado de seguridad del centro de seguridad de Azure](security-center-monitoring.md) : Obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Recomendaciones de seguridad de la administración de centro de seguridad de Azure](security-center-recommendations.md) : Descubra cómo recomendaciones ayudar a proteger los recursos de Azure.
- [Preguntas más frecuentes sobre centro de seguridad de azure](security-center-faq.md) --buscar con frecuencia preguntas más frecuentes sobre el uso del servicio.
- Entradas de [blog de seguridad de azure](http://blogs.msdn.com/b/azuresecurity/) --buscar blog sobre cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
