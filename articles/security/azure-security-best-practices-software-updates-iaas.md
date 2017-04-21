<properties
   pageTitle="Procedimientos recomendados para actualizaciones de Software de Microsoft Azure IaaS | Microsoft Azure"
   description="Artículo proporciona una colección de prácticas recomendadas para actualizaciones de software en un entorno de Microsoft Azure IaaS.  Está pensado para profesionales de TI y analistas de seguridad que trabajan con Cambiar control, administración de activos y actualización de software diariamente, los responsables de los esfuerzos de seguridad y cumplimiento de la organización incluidos."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="best-practices-for-software-updates-on-microsoft-azure-iaas"></a>Prácticas recomendadas para actualizaciones de software de Microsoft Azure IaaS

Antes de comenzar con cualquier tipo de discusión en las mejores prácticas para un entorno de Azure [IaaS](https://azure.microsoft.com/overview/what-is-iaas/) , es importante comprender cuáles son los escenarios que tendrá administrar las actualizaciones de software y las responsabilidades. El diagrama siguiente le ayudarán a comprender estos límites:

![Modelos de nube y responsabilidades](./media/azure-security-best-practices-software-updates-iaas/sec-cloudstack-new.png)

La columna situada más a la izquierda muestra siete responsabilidades (definidas en las secciones siguientes) que debe considerar las organizaciones, que contribuyen a la seguridad y privacidad de un entorno informático.
 
Clasificación de datos y responsabilidad y protección de cliente y el punto final son las responsabilidades que son únicamente en el dominio de los clientes y responsabilidades física, Host y red están en el dominio de proveedores de servicios de nube en los modelos de PaaS y SaaS. 

Las responsabilidades restantes se comparten entre los clientes y proveedores de servicios de nube. Algunas responsabilidades requieren el CSP y el cliente administrar la responsabilidad juntas, incluyendo auditoría de sus dominios. Por ejemplo, considere la posibilidad de identidad y acceder a administración cuando se usa servicios de Azure Active Directory; cliente es la configuración de servicios, como la autenticación multifactor, pero la funcionalidad eficaz de asegurarse de que es la responsabilidad de Microsoft Azure.

> [AZURE.NOTE] Para obtener más información acerca de las responsabilidades compartidas en la nube, lea [Compartido responsabilidades de la informática en nube](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf) 

Estos mismos principios se aplican en un escenario híbrido donde su empresa utiliza máquinas virtuales de IaaS de Azure comunicarse con los recursos locales como se muestra en el diagrama siguiente.

![Escenario típico híbrida con Microsoft Azure](./media/azure-security-best-practices-software-updates-iaas/sec-azconnectonpre.png)

## <a name="initial-assessment"></a>Evaluación inicial

Incluso si su empresa ya usa un sistema de administración de la actualización y ya tiene las directivas de actualización de software en su lugar, es importante frecuentemente visitar evaluaciones de directiva anterior y actualizarlos según sus necesidades actuales. Esto significa que debe estar familiarizado con el estado actual de los recursos de la empresa. Para llegar a este estado, tiene que saber:

-   Los equipos virtuales y físicos de la empresa.

-   Sistemas operativos y versiones que se ejecutan en cada uno de esos equipos físicos y virtuales.

-   Actualizaciones de software instaladas actualmente en cada equipo (versiones de service pack, las actualizaciones de software y otras modificaciones).

-   La función que realiza cada equipo de la empresa.

-   Las aplicaciones y los programas que se ejecutan en cada equipo.

-   Información de propiedad y de contacto para cada equipo.

-   Presentan los activos en su entorno y su valor relativo para determinar qué áreas necesitan más atención y la protección.

-   Problemas conocidos de seguridad y los procesos de la empresa tiene en lugar de identificación nuevos problemas de seguridad o cambios en el nivel de seguridad.

-   Medidas que se han implementado para proteger su entorno.

Debe actualizar esta información con regularidad y debe estar disponible para los implicados en el proceso de administración de la actualización de software.

## <a name="establish-a-baseline"></a>Establecer una línea base

Una parte importante del proceso de administración de actualización de software es crear iniciales instalaciones estándares de versiones de sistemas operativos, aplicaciones y hardware para equipos de la empresa; se denominan líneas base. Una línea base es la configuración de un producto o sistema establecido en un punto específico en el tiempo. Una aplicación o una línea de base de sistema operativo, por ejemplo, proporciona la capacidad de volver a crear un equipo o un servicio a un estado específico.

Líneas base proporcionan la base para buscar y corregir problemas potenciales y simplifican el proceso de administración de actualización de software, reduciendo el número de actualizaciones de software que se debe implementar en su empresa y aumentar su capacidad para supervisar el cumplimiento.

Después de realizar la auditoría inicial de su empresa, debe usar la información que se obtiene de la auditoría para definir una línea de base operativa para los componentes de TI dentro de su entorno de producción. Un número de líneas de base podría ser necesario, dependiendo de los diferentes tipos de hardware y software implementado en producción.

Por ejemplo, algunos servidores requieren una actualización de software para impedir que la dependencia cuando entra en el proceso de cierre cuando se ejecuta Windows Server 2012. Una línea base para estos servidores debe incluir esta actualización de software.

En grandes organizaciones, suele ser útil dividir los equipos de la empresa en categorías de activos y mantener cada categoría en una línea de base estándar mediante el uso de las mismas versiones de software y actualizaciones de software. A continuación, puede usar estas categorías de activos de prioridades a una distribución de actualizaciones de software.

## <a name="subscribe-to-the-appropriate-software-update-notification-services"></a>Suscribirse a los servicios de notificación de actualización de software adecuado

Después de realizar una auditoría inicial del software en uso de la empresa, debe determinar el mejor método para recibir notificaciones de nuevas actualizaciones de software para cada producto de software y la versión. Según el producto de software, podría ser el mejor método de notificación notificaciones por correo electrónico, sitios Web o publicaciones del equipo.

Por ejemplo, el centro de respuesta de seguridad de Microsoft (MSRC) responde a todos los problemas relacionados con la seguridad acerca de los productos de Microsoft y proporciona el servicio de boletín de seguridad de Microsoft, una notificación de correo electrónico gratuito de vulnerabilidad identificadas recientemente y actualizaciones de software que se publican para abordar estas vulnerabilidad. Puede suscribirse a este servicio en http://www.microsoft.com/technet/security/bulletin/notify.mspx.

## <a name="software-update-considerations"></a>Consideraciones de actualización de software

Después de realizar una auditoría inicial del software en uso de la empresa, debe determinar los requisitos de instalación de su sistema de administración de actualización de software, que depende de que el sistema de administración de actualización de software que esté utilizando. Para WSUS lea [Prácticas recomendadas con servicios de actualización de Windows Server](https://technet.microsoft.com/library/Cc708536), centro de sistema lea [planear actualizaciones de Software en el Administrador de configuración](https://technet.microsoft.com/library/gg712696).

Sin embargo, hay algunas consideraciones generales y los procedimientos recomendados que se pueden aplicar independientemente de la solución que se usa como se muestra en las secciones que sigue.

### <a name="setting-up-the-environment"></a>Configurar el entorno

Tenga en cuenta las siguientes prácticas cuando vaya a configurar el software actualización el entorno de administración:

-   **Crear colecciones de actualización de software de producción basan en los criterios estables**: en general, uso de criterios estables para crear colecciones de su inventario de actualizaciones de software y distribución le ayudará a simplificar todas las etapas del proceso de administración de actualización de software. Los criterios estables pueden incluir la versión del sistema operativo cliente instalado y nivel de service pack, función del sistema u organización de destino.

-   **Crear colecciones de producción previo que incluyan equipos de referencia**: la colección de producción preliminar debe incluir configuraciones representantes de las versiones de sistema operativo, línea de software de empresa y otro software que se ejecuta en su empresa.

También debe tener en cuenta que el servidor de actualización de software se encuentran, si está en la infraestructura de IaaS de Azure en la nube o si va a ser local. Esta es una decisión importante porque se debe evaluar la cantidad de tráfico entre los recursos locales y la infraestructura de Azure. Para obtener más información sobre cómo conectar su infraestructura locales con Azure, lea [Conectar una red local a una red virtual de Microsoft Azure](https://technet.microsoft.com/library/Dn786406.aspx) .

Las opciones de diseño que determinarán donde se encuentran el servidor de actualización también varían según la infraestructura actual y el sistema de actualización de software que se utiliza actualmente. Para WSUS lea [Implementar los servicios de actualización de Windows Server en su organización](https://technet.microsoft.com/library/hh852340.aspx) y [Planear sitios y jerarquías en el Administrador de configuración](https://technet.microsoft.com/library/Gg712681.aspx)de lectura para el Administrador de configuración de System Center.

### <a name="backup"></a>Copia de seguridad

Copias de seguridad periódicas son importantes no solo para la actualización de administración plataforma de software propio sino también para los servidores que se actualizará. Requieren las organizaciones que tienen un [proceso de administración](https://technet.microsoft.com/library/cc543216.aspx) de TI a justificar las razones para ¿por qué es necesario actualizar el servidor, el tiempo de inactividad estimado y posible impacto. Para asegurarse de que tiene una configuración de deshacer en su lugar en caso de que se produce un error en una actualización, asegúrese de hacer copia de seguridad del sistema con regularidad.

Algunas opciones de copia de seguridad de Azure IaaS incluyen:

-   [Protección de carga de trabajo de IaaS Azure con el Administrador de protección de datos](https://azure.microsoft.com/blog/2014/09/08/azure-iaas-workload-protection-using-data-protection-manager/)

-   [Realizar copias de seguridad máquinas virtuales de Windows Azure](../backup/backup-azure-vms.md)

### <a name="monitoring"></a>Supervisión

Debe ejecutar informes normales para supervisar el número de falta o instalar actualizaciones, o con estado incompleto, para cada actualización de software que está autorizado. Del mismo modo, informes de actualizaciones de software que todavía no autorización pueden facilitar las decisiones de implementación sea más fáciles.

También debe tener en cuenta las siguientes tareas:

-   Llevar a cabo una auditoría de actualizaciones de seguridad aplicables e instaladas para todos los equipos de su empresa.

-   Autorizar e implementar las actualizaciones a los equipos apropiados.

-   Realizar un seguimiento de inventario y actualizar el estado de la instalación y el progreso de todos los equipos de su compañía.

Consideraciones generales además hasta que se explica en este artículo, también debe tener en cuenta cada producto está mejor práctica, por ejemplo: si tiene una máquina virtual en Azure con SQL Server, asegúrese de seguir la recomendación de actualizaciones de software de ese producto.

## <a name="next-steps"></a>Pasos siguientes

Utilice las instrucciones descritas en este artículo para ayudarle a determinar las mejores opciones para actualizaciones de software para máquinas virtuales dentro de Azure IaaS. Hay muchas similitudes entre las mejores prácticas de actualización de software en un centro de datos tradicional frente a Azure IaaS, por lo tanto, se recomienda evaluar las directivas de actualización de software actuales para incluir máquinas virtuales de Azure e incluir las mejores prácticas relevantes de este artículo en el proceso de actualización de software general.
