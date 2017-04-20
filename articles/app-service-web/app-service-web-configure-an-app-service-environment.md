<properties
    pageTitle="Cómo configurar un entorno de servicio de la aplicación | Microsoft Azure"
    description="Configuración, administración y supervisión de entornos de aplicación de servicio"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="ccompy"/>


# <a name="configuring-an-app-service-environment"></a>Configurar un entorno de servicio de aplicaciones #

## <a name="overview"></a>Información general ##

En un alto nivel, un entorno de servicio de la aplicación de Azure consta de varios componentes principales:

- Recursos de cálculo que se ejecutan en el entorno de servicios de aplicación de servicio alojado
- Almacenamiento de información
- Una base de datos
- Una red Virtual Classic(V1) o recursos Manager(V2) Azure (VNet) 
- Una subred con el servicio de entorno de servicio de aplicación alojado que se ejecuta en él

### <a name="compute-resources"></a>Calcular recursos

Use los recursos de cálculo para los grupos de cuatro recursos.  Cada entorno de servicio de la aplicación (ASE) tiene un conjunto de front-end y tres grupos de trabajo posibles. No es necesario utilizar todos los grupos de trabajo de tres--si lo desea, puede usar solo uno o dos.

Los hosts de los grupos de recursos (front-end y los trabajadores) no sean accesibles directamente a los inquilinos. No puede utilizar el protocolo de escritorio remoto (RDP) para conectarse a ellas, cambiar su aprovisionamiento o actúe como administrador en ellos.

Puede establecer el tamaño y la cantidad de grupo de recursos. En un ASE tiene cuatro opciones de tamaño, las etiquetan P1 mediante P4. Para obtener más información acerca de los tamaños y sus precios, consulte [precios de aplicación de servicio](../app-service/app-service-value-prop-what-is.md).
Cambiar el tamaño o la cantidad se denomina una operación de escala.  Operación de solo escala puede ser en curso en un momento.

**Parte frontal finaliza**: front-end es los extremos HTTP/HTTPS para las aplicaciones que se encuentran en su ASE. No ejecute cargas de trabajo en el front-end.

- Un ASE comienza con dos P2s, que es suficiente para cargas de trabajo de desarrollo o prueba y las cargas de trabajo de bajo nivel de producción. Recomendamos encarecidamente P3s para moderado a cargas de trabajo de producción pesados.
- Para moderado a cargas de trabajo de producción pesado, le recomendamos que tenga al menos cuatro P3s para asegurarse de que hay suficiente front-end que se ejecuta cuando se produce el mantenimiento programado. Las actividades de mantenimiento programada lleva front-end de uno a la vez. Esto reduce general capacidad de front-end disponible durante las actividades de mantenimiento.
- No puede agregar una nueva instancia de front-end al instante. Puede tardar entre 2 y 3 horas aprovisionar.
- Para realizar ajustes adicionales en escala, debe supervisar el porcentaje de CPU, el porcentaje de memoria y métricas solicitudes activas para el grupo de servidores front-end. Si el porcentaje de CPU o de memoria por encima del 70% al ejecutar P3s, agregue más front-end. Si el valor de solicitudes activas medias a 15.000 a 20.000 solicitudes por front-end, también deberá agregar más front-end. El objetivo general es mantener porcentajes de CPU y la memoria siguiente 70% y, a continuación, Active solicitudes promedio a debajo de 15.000 solicitudes por frente Finalizar cuando está ejecutando P3s.  

**Los trabajadores**: los trabajadores tienen que realmente ejecutan sus aplicaciones. Cuando ajustar los planes de servicio de aplicaciones, que utiliza los trabajadores en el grupo de trabajo asociado.

- No puede agregar al instante los trabajadores. Puede tomar de 2 a 3 horas aprovisionar, independientemente de cuántos se agregan.
- El tamaño de un recurso de cálculo para cualquier grupo de ajuste de escala tardará 2 a 3 horas por cada dominio de actualización. Hay 20 de actualización de dominios en un ASE. Si escala el tamaño de cálculo de un grupo de trabajo con 10 instancias, podría tardar entre 20 a 30 horas en completarse.
- Si cambia el tamaño de los recursos de cálculo que se usan en un grupo de trabajo, hará que el inicio y de las aplicaciones que se ejecutan en ese grupo de trabajo.

La manera más rápida de cambiar el tamaño de recursos de cálculo de un grupo de trabajo que no tiene ninguna aplicación es:

- Reducir el número de instancia a 0. Tardará unos 30 minutos para desasignar las instancias.
- Seleccione el nuevo tamaño de cálculo y el número de instancias. Desde aquí, tardará entre 2 y 3 horas en completarse.

Si las aplicaciones requieren un tamaño de recursos de cálculo, no puede aprovechar las ventajas de las directrices anteriores. En lugar de cambiar el tamaño del grupo de trabajo que aloja las aplicaciones, puede rellenar otro grupo de trabajo con los trabajadores, el tamaño deseado y mover las aplicaciones a ese grupo.

- Crear las instancias adicionales del tamaño necesarios de cálculo en otro grupo de trabajo. Esto le llevará de 2 a 3 horas en completarse.
- Reasignar los planes de servicio de aplicaciones que alojan las aplicaciones que tenga un tamaño mayor al grupo de trabajo recién configurado. Se trata de una operación rápida que debe tener menos de un minuto en completarse.  
- Ajustar el primer grupo de trabajo si ya no necesita esas instancias sin usar. Esta operación tardará unos 30 minutos en completarse.

**Ajuste automático**: una de las herramientas que pueden ayudarle a administrar su consumo de recursos de cálculo es ajusta automáticamente. Puede usar el ajuste automático de front-end o grupos de trabajo. Puede hacer cosas como aumentar las instancias de cualquier tipo de grupo por la mañana y reducir en la noche. O tal vez puede agregar instancias cuando el número de trabajadores que están disponibles en un grupo de trabajo baje un determinado umbral.

Si desea establecer reglas de Autoescala alrededor de métricas de grupo de recursos de cálculo, a continuación, tenga en cuenta el tiempo que requiere de aprovisionamiento. Para obtener más detalles acerca de ajuste automático entornos de aplicación de servicio, consulte [cómo configurar Autoescala en un entorno de servicio de la aplicación][ASEAutoscale].

### <a name="storage"></a>Almacenamiento de información

Cada ASE está configurado con 500 GB de almacenamiento. Este espacio se utiliza en todas las aplicaciones de la ASE. Este espacio de almacenamiento es parte de la ASE y actualmente no se pueden cambiar para usar el espacio de almacenamiento. Si está realizando ajustes en el enrutamiento de red virtual o la seguridad, debe seguir permitiendo acceso al almacenamiento de Azure--o la ASE no funciona.

### <a name="database"></a>Base de datos

La base de datos contiene la información que define el entorno, así como los detalles acerca de las aplicaciones que se estén ejecutando dentro de ella. Esto también es parte de la suscripción mantenidos en Azure. No es algo que tiene una capacidad directa manipular. Si está realizando ajustes en el enrutamiento de red virtual o la seguridad, debe seguir permitiendo el acceso a SQL Azure, o no funciona el ASE.

### <a name="network"></a>Red

El VNet que se utiliza con su ASE puede ser una que haya hecho cuando creó el ASE o una que haya hecho con anterioridad. Cuando se crea la subred durante la creación de ASE, fuerza la ASE estén en el mismo grupo de recursos como la red virtual. Si necesita el grupo de recursos utilizado por su ASE sea distinto de su VNet, debe crear su ASE usando una plantilla de administrador de recursos.

Hay algunas restricciones de la red virtual que se usa para una ASE:
 
- La red virtual debe ser una red virtual regional.
- Debe haber una subred con 8 o más direcciones donde se implementa el ASE.
- Después de una subred se utiliza para hospedar una ASE, no se puede cambiar el intervalo de direcciones de la subred. Por este motivo, recomendamos que la subred contiene al menos 64 direcciones para acomodar el aumento futuro del ASE.
- Puede haber nada más en la subred pero el ASE.

A diferencia del servicio hospedado que contiene la ASE, la [red virtual] [ virtualnetwork] y subred están bajo control de usuario.  Puede administrar su red virtual a través de la interfaz de usuario de red Virtual o PowerShell.  Un ASE se puede implementar en un clásico o VNet del Administrador de recursos.  El portal y API experiencias son ligeramente diferentes entre clásico y VNets Administrador de recursos, pero la experiencia de ASE es la misma.

El VNet que se usa para hospedar una ASE puede usar cualquier direcciones IP RFC1918 privadas o puede utilizar las direcciones IP públicas.  Si desea usar un intervalo IP que no está cubierto por RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), a continuación, deberá crear sus VNet y subred ser usada por el ASE por delante de la creación de ASE.

Debido a esta capacidad coloca el servicio de aplicación de Azure en la red virtual, significa que las aplicaciones que se hospedan en su ASE ahora pueden acceder a los recursos que están disponibles a través de ExpressRoute o redes privadas virtuales (VPN) de sitio a sitio directamente. Las aplicaciones que se encuentran dentro de su entorno de servicio de aplicación no requieren características de redes adicionales para tener acceso a los recursos disponibles para la red virtual que aloja el entorno del servicio de aplicación. Esto significa que no necesita usar la integración de VNET o conexiones híbrido para acceder a los recursos conectados a su red virtual o en. Aún puede usar ambas de estas características aunque acceso a los recursos en redes que no están conectadas a su red virtual.

Por ejemplo, puede usar VNET integración integrar con una red virtual que está en la suscripción, pero no está conectada a la red virtual que se encuentra su ASE en. Aún también puede usar conexiones híbrido acceso a los recursos que se encuentran en otras redes, como ocurre normalmente.  

Si tiene la red virtual configurada con una VPN ExpressRoute, debe tener en cuenta algunas de las necesidades de enrutamiento que tiene un ASE. Existen algunas configuraciones de ruta definidas por el usuario (UDR) que no son compatibles con un ASE. Para más información sobre cómo ejecutar un ASE en una red virtual con ExpressRoute, vea [ejecutar un entorno de servicio de la aplicación en una red virtual con ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Proteger el tráfico entrante

Existen dos métodos principales para controlar el tráfico a su ASE.  Puede usar Groups(NSGs) de seguridad de red para controlar qué IP direcciones pueden tener acceso a su ASE tal como se describe a continuación [cómo controlar el tráfico de entrada en un entorno de servicio de la aplicación](app-service-app-service-environment-control-inbound-traffic.md) y también puede configurar su ASE con un Balancer(ILB) de carga interno.  Estas características también pueden utilizarse juntos si desea restringir el acceso con NSGs a su ASE ILB.

Cuando se crea un ASE, creará a una VIP en su VNet.  Existen dos tipos de VIP, internos y externos.  Cuando se crea un ASE con una VIP externo sus aplicaciones en su ASE serán accesibles mediante una dirección IP enrutable de internet. Cuando se selecciona interno su ASE se configurarán con un ILB y no se directamente internet accesibles.  Un ASE ILB aún requiere a una VIP externo, pero solo se utiliza para obtener acceso de administración y el mantenimiento de Azure.  

Durante la creación de ILB ASE proporcionar el subdominio usado por la ASE ILB y tendrá que administre DNS para el subdominio que especifique.  Puesto que establecer el nombre del subdominio debe administrar el certificado utilizado para el acceso HTTPS.  Después de la creación de ASE le pide que proporcione el certificado.  Para obtener más información sobre cómo crear y usar un ASE ILB leer [mediante un equilibrador de carga interno con un entorno de servicio de la aplicación][ILBASE]. 


## <a name="portal"></a>Portal

Puede administrar y supervisar su entorno de servicio de la aplicación mediante la interfaz de usuario en el portal de Azure. Si tiene un ASE, es probable que vea el símbolo de servicio de aplicaciones en la barra lateral. Este símbolo se utiliza para representar los entornos de aplicación de servicio en el portal de Azure:

![Símbolo de entorno de servicio de aplicación][1]

Para abrir la interfaz de usuario que enumera todos los entornos de aplicación de servicio, puede usar el icono o seleccione las comillas angulares (">" símbolo) en la parte inferior de la barra lateral para seleccionar los entornos de aplicación de servicio. Al seleccionar uno de los ASEs enumerados, abra la interfaz de usuario que se utiliza para supervisar y administrar.

![Interfaz de usuario para supervisar y administrar su entorno de aplicación de servicio][2]

Este módulo primera muestra algunas de las propiedades de su ASE, junto con un gráfico métrico por grupo de recursos. Algunas de las propiedades que se muestran en el bloque de **Essentials** también son hipervínculos que se abrirán la placa que está asociado a ella. Por ejemplo, puede seleccionar el nombre de **Red Virtual** para abrir la interfaz de usuario asociado a la red virtual que se está ejecutando el ASE en. **Planes de servicio de aplicaciones** y **aplicaciones** abren aspas que estos elementos que están en su ASE de la lista.  

### <a name="monitoring"></a>Supervisión

Los gráficos permiten ver una amplia variedad de indicadores de rendimiento de cada grupo de recursos. Para el grupo de servidores front-end, puede supervisar la CPU y la memoria promedio. Para grupos de trabajo, puede supervisar la cantidad que se usa y la cantidad que está disponible.

Servicio de aplicación varios planes para hacer el uso de los trabajadores de un grupo de trabajo. La carga de trabajo no se distribuye de la misma manera como con los servidores front-end, así que el uso de CPU y la memoria no proporcionan un alto nivel de información útil. Es más importante realizar un seguimiento de cuántos trabajadores que ha utilizado y están disponibles, especialmente si administra este sistema para que otras personas usar.  

También puede usar todas las mediciones que pueden realizar un seguimiento de los gráficos para configurar alertas. Configurar alertas aquí funciona del mismo como cualquier otro sitio de la aplicación de servicio. Puede establecer una alerta de la parte de interfaz de usuario de **alertas** o de taladro en cualquier métrica de interfaz de usuario y seleccionando **Agregar alerta**.

![Métrica de interfaz de usuario][3]

Las medidas que acaba de tratar son las métricas del entorno de servicio de aplicación. También hay métricas que están disponibles en el nivel del plan de servicio de aplicación. Esto es donde supervisión CPU y la memoria convierte una gran cantidad de sentido.

En un ASE, todos los planes de servicio de aplicación son los planes de servicio de aplicación dedicados. Esto significa que las aplicaciones sólo que se ejecutan en los hosts asignados a que plan de servicios de aplicación son las aplicaciones de ese plan de servicio de aplicación. Para ver los detalles de su plan de servicio de aplicación, mostrar su plan de servicios de aplicación desde cualquiera de las listas en la interfaz de usuario ASE o desde **planes de servicio de aplicación examinar** (que se enumeran todos ellos).   

### <a name="settings"></a>Configuración

En el módulo ASE, hay una sección de **configuración** que contiene varias funciones importantes:

**Configuración de** > **Propiedades**: el módulo de **configuración** se abre automáticamente cuando aparezca el módulo ASE. En la parte superior es **Propiedades**. Hay un número de elementos de aquí que son redundantes lo que ve en **Essentials**, pero ¿qué es muy útil es la **Dirección IP Virtual**, así como **Las direcciones IP salientes**.

![Módulo de configuración y propiedades][4]

**Configuración de** > **Direcciones IP**: cuando se crea una aplicación de IP Secure Sockets Layer (SSL) en su ASE, necesita una dirección IP SSL. Para obtener uno, su ASE necesita direcciones IP SSL posee que se pueden asignar. Cuando se crea un ASE, tiene una dirección IP SSL para ello, pero puede agregar más. Hay un cargo para direcciones adicionales SSL IP, tal como se muestra en el [Servicio de aplicación precios] [ AppServicePricing] (en la sección de conexiones SSL). El precio adicional es el precio de IP SSL.

**Configuración de** > **Grupo de servidores Front-End** / **Grupos de trabajo**: cada uno de estos módulos de grupo de recursos ofrece la capacidad de ver información únicamente en ese grupo de recursos, además de proporcionar controles para escalar completamente ese grupo de recursos.  

El módulo base para cada grupo de recursos proporciona un gráfico con métricas para ese grupo de recursos. Al igual que con los gráficos desde el módulo ASE, puede ir en el gráfico y configure alertas como desee. Configuración de una alerta desde el módulo de ASE para un grupo de recursos específico hace lo mismo que hacerlo desde el fondo de recursos. En el módulo de **configuración** de grupo de trabajo, que tiene acceso a todas las aplicaciones o planes de servicio de la aplicación que se ejecutan en este grupo de trabajo.

![Interfaz de usuario de configuración de grupo de trabajo][5]

### <a name="portal-scale-capabilities"></a>Capacidades de escala de portal  

Hay tres operaciones de escala:

- Cambiar el número de direcciones IP en el ASE que están disponibles para el uso de IP SSL.
- Cambiar el tamaño de los recursos de cálculo que se usan en un grupo de recursos.
- Cambiar el número de recursos de cálculo que se usan en un grupo de recursos, manualmente o a través de ajuste automático.

En el portal, hay tres maneras de controlar cuántos servidores que tiene en los grupos de recursos:

- Una operación de escala de la hoja de ASE principal en la parte superior. Puede realizar cambios de configuración de escala de varios grupos de front-end y trabajo. Todo se aplican como una única operación.
- Una operación manual escala desde el módulo de **escala** de grupo de recursos individuales, que se encuentra en **configuración**.
- Ajuste automático, que configura desde el módulo de **escala** del grupo de recursos individuales.

Para usar la operación de escala en el módulo ASE, arrastre el control deslizante a la cantidad que desee y guarda. Esta interfaz de usuario también permite cambiar el tamaño.  

![Escala de interfaz de usuario][6]

Para usar las capacidades manual o Autoescala en un grupo de recursos específico, vaya a **configuración** > **Grupo de servidores Front-End** / **Grupos de trabajo** , según corresponda. A continuación, abra el grupo que desea cambiar. Vaya a **configuración** > **escalado** o **configuración** > **escalar**. El módulo de **Escala fuera** le permiten controlar la cantidad de instancia. **Escala de** le permiten controlar el tamaño del recurso.  

![Configuración de escala de interfaz de usuario][7]

## <a name="fault-tolerance-considerations"></a>Consideraciones de tolerancia a errores

Puede configurar un entorno de servicio de la aplicación para usar hasta 55 recursos de cálculo total. De los recursos de cálculo 55, 50 solo se puede utilizar para las cargas de trabajo de host. El motivo es doble. Hay un mínimo de 2 recursos de cálculo front-end.  Que deja hasta 53 para admitir la asignación de grupo de trabajo. Para proporcionar tolerancia a errores, debe tener un recurso de cálculo adicionales que se asigna según las reglas siguientes:

- Cada grupo de trabajo necesita al menos 1 recurso de cálculo adicionales que no está disponible para asignarse una carga de trabajo.
- Cuando la cantidad de recursos de cálculo en un grupo de trabajo supera un valor determinado, a continuación, otro recurso de cálculo se requiere para tolerancia a errores. No es el caso en el grupo de servidores front-end.

Dentro de un grupo de trabajo, los requisitos de tolerancia a errores son un valor determinado de X recursos asignados a un grupo de trabajo:

- Si X es entre 2 y 20, la cantidad de recursos de cálculo utilizable que puede usar para cargas de trabajo es X-1.
- Si X es entre 21 y 40, la cantidad de recursos de cálculo utilizable que puede usar para cargas de trabajo es X-2.
- Si X es entre 41 y 53, la cantidad de recursos de cálculo utilizable que puede usar para cargas de trabajo es X-3.

El espacio mínimo tiene 2 servidores front-end y 2 trabajadores.  Con las instrucciones anteriores, a continuación, aquí tiene algunos ejemplos para aclarar:  

- Si tiene los 30 trabajadores en un solo grupo, 28 de ellos puede usarse para cargas de trabajo de host.
- Si tiene los 2 trabajadores en un solo grupo, 1 puede usarse para cargas de trabajo de host.
- Si tiene 20 trabajadores en un solo grupo, puede usarse 19 a cargas de trabajo de host.  
- Si tiene los 21 trabajadores en un solo grupo, aún después 19 solo puede usarse para cargas de trabajo de host.  

El aspecto de tolerancia a errores es importante, pero debe tener en cuenta como escalar encima determinados umbrales. Si desea agregar más capacidad de 20 instancias, a continuación, vaya a 22 o superior porque 21 no agregar cualquier más capacidad. El mismo es true va encima de 40, donde el siguiente número que agrega capacidad es 42.  

## <a name="deleting-an-app-service-environment"></a>Eliminación de un entorno de servicio de aplicaciones ##

Si desea eliminar un entorno de servicio de la aplicación, simplemente use la acción **Eliminar** en la parte superior del módulo de entorno de servicio de aplicación. Cuando hace esto, le pedirá que escriba el nombre de su entorno de servicio de aplicación para confirmar que realmente desea hacerlo. Tenga en cuenta que cuando se elimina un entorno de servicio de la aplicación, elimina todo el contenido dentro de ella.  

![Eliminar un entorno de servicio de la aplicación de interfaz de usuario][9]  

## <a name="getting-started"></a>Introducción

Para empezar con la aplicación de servicio entornos, consulte [cómo crear un entorno de servicio de aplicación](app-service-web-how-to-create-an-app-service-environment.md).

Para obtener más información acerca de la plataforma de servicio de la aplicación de Azure, vea [Servicio de aplicación de Azure](../app-service/app-service-value-prop-what-is.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
