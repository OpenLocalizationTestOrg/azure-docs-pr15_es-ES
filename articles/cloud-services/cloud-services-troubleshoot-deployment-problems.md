<properties
 pageTitle="Solucionar problemas de implementación de servicio de nube | Microsoft Azure"
 description="Hay algunos problemas comunes que puede surgir al implementar un servicio de nube en Azure. Este artículo proporciona soluciones a algunas de ellas."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="troubleshoot-cloud-service-deployment-problems"></a>Solucionar problemas de implementación de servicio de nube

Cuando se implementa un paquete de aplicación de servicio de nube en Azure, puede obtener información acerca de la implementación desde el panel de **Propiedades** en el portal de Azure. Puede usar los detalles de este panel para ayudar a solucionar problemas con el servicio de nube y puede proporcionar esta información para admitir Azure al abrir una nueva solicitud de soporte técnico.

Puede encontrar el panel **Propiedades** como sigue:

* En el portal de Azure, haga clic en la implementación de su servicio de nube, haga clic en **toda la configuración**y, a continuación, haga clic en **Propiedades**.
* En el portal de Azure clásico, haga clic en la implementación de su servicio de nube, haga clic en **paneles**, situado en la esquina inferior derecha de la página (en **vistazo rápido**). Tenga en cuenta que no hay ninguna etiqueta "Propiedades de" en este panel.

> [AZURE.NOTE] Puede copiar el contenido del panel de **Propiedades** en el Portapapeles, haga clic en el icono en la esquina superior derecha del panel.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problema: no tengo acceso a Mi sitio Web, pero se inicia mi implementación y todas las instancias de rol están listos

El vínculo de dirección URL del sitio Web que se muestra en el portal no incluye el puerto. El puerto predeterminado para sitios Web es 80. Si la aplicación está configurada para ejecutar en un puerto diferente, debe agregar el número de puerto correcto para la dirección URL al acceso al sitio Web.

1. En el portal de Azure, haga clic en la implementación de su servicio de nube.
2. En el panel de **Propiedades** del portal de Azure, compruebe los puertos de las instancias de la función (en **Puntos finales de entrada**).
3. Si el puerto no es 80, agregue el valor de puerto correcto para la dirección URL cuando se tiene acceso a la aplicación. Para especificar un puerto, escriba la dirección URL, seguida de dos puntos (:), seguido por el número de puerto, sin espacios.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problema: Las instancias de función reciclan sin hacer nada de mí

Servicio de resolución de problemas se produce automáticamente cuando Azure detecta nodos del problema y por tanto mueve las instancias de función a nuevos nodos. Cuando ocurre esto, es posible que vea reciclaje automáticamente las instancias de la función. Para averiguar la resolución de problemas de servicio se ha producido si:

1. En el portal de Azure, haga clic en la implementación de su servicio de nube.
2. En el panel de **Propiedades** del portal de Azure, revise la información y determinar si la resolución de problemas de servicio se ha producido durante el tiempo que observan las funciones de reciclaje.

Funciones se también reciclaje aproximadamente una vez al mes durante el host de sistema operativo y actualizaciones del sistema operativo de invitado.  
Para obtener más información, consulte la entrada de blog [Rol instancia reinicia debido a actualizaciones de sistema operativo](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problema: no puedo hacer un intercambio VIP y recibe un error

No se permite un intercambio VIP si hay una actualización de implementación en curso. Distribución de actualizaciones puede producirse automáticamente cuando:

* Un nuevo sistema operativo de invitado está disponible y que se haya configurado para actualizaciones automáticas.
* Se produce la resolución de problemas de servicio.

Para averiguar si una actualización automática impide realiza un cambio de la dirección VIP:

1. En el portal de Azure, haga clic en la implementación de su servicio de nube.
2. En el panel de **Propiedades** del portal de Azure, observe el valor de **estado**. Si está **Listo**, compruebe la **última operación** para ver si recientemente sucedido que pueden impedir la permutación VIP.
3. Repita los pasos 1 y 2 para la implementación de producción.
4. Si es una actualización automática en proceso, espere a que finalice antes de intentar realizar el intercambio VIP.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problema: Una instancia de funciones es bucle entre iniciado, inicializando, ocupado y detenido

Esta condición podría indicar un problema con el archivo de código, el paquete o la configuración de la aplicación. En ese caso, debería poder ver el estado de cambio de cada pocos minutos y el portal de Azure puede decir algo como **reciclaje**, **ocupado**o **Inicializando**. Esto indica que hay algún problema con la aplicación que mantiene la instancia del rol de ejecución.

Para obtener más información sobre cómo solucionar este problema, consulte el blog [Datos de Azure PaaS calcular diagnósticos](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) y [problemas comunes que causan roles de reciclaje](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problema: Mi aplicación ha dejado de funcionar

1. En el portal de Azure, haga clic en la instancia del rol.
2. En el panel de **Propiedades** del portal de Azure, tenga en cuenta las siguientes condiciones para resolver el problema:
   * Si recientemente se ha detenido la instancia del rol (puede comprobar el valor de **Anular recuento**), podría estar actualizando la implementación. Espere para ver si la instancia de rol reanuda funciona en su propia.
   * Si la instancia de rol está **ocupado**, compruebe el código de la aplicación para ver si se controla el evento de [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck) . Posible que necesite agregar o solucionar el código que controla este evento.
   * Vaya a través de los datos de diagnóstico y escenarios de solución de problemas en la entrada de blog [Datos de Azure PaaS calcular diagnósticos](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

>[AZURE.WARNING] Si reciclaje su servicio de nube, restablecer las propiedades de la implementación eficaz borrar la información para el problema original.

## <a name="next-steps"></a>Pasos siguientes

Ver más [artículos de solución de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) de servicios en la nube.

Para obtener información sobre cómo solucionar problemas de rol del servicio de nube con los datos de diagnósticos de Azure PaaS equipo, consulte [serie de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
