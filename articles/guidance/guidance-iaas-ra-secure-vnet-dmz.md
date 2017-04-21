<properties
   pageTitle="Referencia de arquitectura Azure - IaaS: Implementar una DMZ entre Azure e Internet | Microsoft Azure"
   description="Cómo implementar una arquitectura de red seguro híbrido con acceso a Internet en Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-dmz-between-azure-and-the-internet"></a>Implementación de una DMZ entre Azure e Internet

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Este artículo describe los procedimientos recomendados para implementar una red híbrida seguro que se extiende la red local que acepta tráfico de la red de internet en Azure. Esta arquitectura de referencia extiende la arquitectura se describe en el artículo de la [implementación de una DMZ entre Azure y su centro de datos local][implementing-a-secure-hybrid-network-architecture]. Se recomienda que lea ese documento y comprender que hacen referencia a arquitectura antes de leer este documento.

> [AZURE.NOTE] Azure tiene dos diferentes modelos de implementación: [El Administrador de recursos] [ resource-manager-overview] y clásica. Esta arquitectura de referencia utiliza el Administrador de recursos, que Microsoft recomienda para implementaciones nuevas. 

Casos de uso común para esta arquitectura incluyen:

- Aplicaciones híbridas donde ejecutar las cargas de trabajo en parte local y en parte de Azure.

- Infraestructura de Azure que enruta el tráfico de internet y local.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

El siguiente diagrama resalta los componentes importantes en esta arquitectura:

> Un documento de Visio que incluye este diagrama de arquitectura está disponible para su descarga en el [Centro de descarga de Microsoft][visio-download]. Este diagrama muestra en la página "DMZ – público".

[! [0]][0]

- **Dirección IP pública (puntos).** Esta es la dirección IP del extremo del público. Los usuarios externos conectados a Internet pueden acceder al sistema desde esta dirección.

- **Dispositivo virtual de red (NVA).**  NVA es un término genérico que describa una máquina virtual realizar tareas como permitir o denegar el acceso como un firewall, optimizadas operaciones WAN (incluida la compresión de red), la distribución personalizada u otras funciones de red.

- **Equilibrador de carga de Azure.** Pasar a través de este equilibrador de carga de todas las solicitudes entrantes de internet y se distribuyen a NVAs en la DMZ pública subred entrante.

- **DMZ pública entrada subred.** Esta subred acepta solicitudes de equilibrador de carga de Azure. Las solicitudes entrantes se pasan a uno de los NVAs en la DMZ.

- **Subred saliente DMZ pública.** Solicitudes aprobados por la NVA pasan a través de esta subred equilibrador de carga interno para el nivel de web.

## <a name="recommendations"></a>Recomendaciones

Azure ofrece numerosos recursos distintos y tipos de recursos, por lo que puede ser esta arquitectura de referencia se aprovisione varias formas diferentes. Proporcionamos una plantilla de administrador de recursos de Azure para instalar la arquitectura de referencia que sigue estas recomendaciones. Si elige crear su propia arquitectura de referencia debería seguir estas recomendaciones a menos que tenga un requisito específico que no se ajusta una recomendación.

### <a name="nva-recommendations"></a>Recomendaciones de NVA

Implementar un conjunto de NVAs para el tráfico que provienen de internet y otro para el tráfico originado en local. Es un riesgo de seguridad para usar un único conjunto de NVAs ambas porque este diseño no proporciona ninguna perímetro de seguridad entre los dos conjuntos de tráfico de red. Es una ventaja de usar este diseño porque reduce la complejidad de la comprobación de las reglas de seguridad y hace que resulte más claro las reglas que corresponden a cada solicitud entrante de red. Por ejemplo, un conjunto de NVAs implementa reglas para el tráfico de internet solo al otro conjunto de reglas de implementación de NVAs para solo el tráfico local.

Incluir una capa 7 NVA para terminar de conexiones de la aplicación en el nivel NVA y mantener la afinidad con los niveles de back-end. Así garantiza simétrica conectividad en el que se devuelve el tráfico de respuesta de los niveles de back-end a través de la NVA.  

### <a name="public-load-balancer-recommendations"></a>Recomendaciones de equilibrador de carga público ###

Para mantener la disponibilidad y escalabilidad, implementar la DMZ pública entrada NVAs en un [conjunto de disponibilidad] [ availability-set] y utilizar una [internet opuestas equilibrador de carga] [ load-balancer] para distribuir las solicitudes de internet a través de la NVAs en el conjunto de disponibilidad.  

Configurar el equilibrador de carga para aceptar solicitudes únicamente en los puertos necesarios para el tráfico de internet. Por ejemplo, restringir las solicitudes entrantes de HTTP al puerto 80 y solicitudes HTTPS entrantes al puerto 443.

## <a name="scalability-considerations"></a>Consideraciones de escalabilidad

Diseño de la infraestructura con una internet opuestas equilibrador de carga delante de la subred DMZ pública entrante desde el principio. Incluso si su initally arquitectura requiere una sola NVA, será más fácil escalar a varias NVAs en el futuro si ya se ha implementado internet opuestas equilibrador de carga.

## <a name="availability-considerations"></a>Consideraciones de disponibilidad

Internet opuestas equilibrador de carga requiere cada NVA en la DMZ pública subred entrante para implementar un [sondeo de mantenimiento][lb-probe]. Se considera un sondeo de mantenimiento que deja de responder en este extremo no está disponible y el equilibrador de carga dirigen las solicitudes a otros NVAs en el mismo conjunto de disponibilidad. Tenga en cuenta que si todos los NVAs no responde, la aplicación se producirá un error por lo que es importante tener la supervisión configurado para DevOps alerta cuando el número de instancias NVA correcto está por debajo de un umbral.

## <a name="manageability-considerations"></a>Consideraciones de capacidad de administración

Restringir la funcionalidad de administración y supervisión de la entrada público DMZ NVA responder a solicitudes en el cuadro salto solo la subred de administración. Como se describe en la [implementación de una DMZ entre Azure y su centro de datos local] [ implementing-a-secure-hybrid-network-architecture] documento, definir una ruta de red de la red local a través de la puerta de enlace en el cuadro saltar en la subred de administración para restringir el acceso.

Si la conectividad de puerta de enlace de su red local a Azure es hacia abajo, aún puede ponerse en el cuadro saltar al implementar una puntos, agregar al cuadro saltar y acceso remoto en desde internet.

## <a name="security-considerations"></a>Consideraciones de seguridad

Esta arquitectura de referencia implementa varios niveles de seguridad:

- Internet opuestas equilibrador de carga dirige las peticiones a la NVAs en la entrada público DMZ subred sólo y únicamente en los puertos necesarios para la aplicación.

- Las reglas de GSN para la subred DMZ pública entrante y saliente impiden que la NVAs se ve comprometida por bloquear solicitudes que se encuentren fuera de las reglas de GSN.

- La configuración de enrutamiento NAT para la NVAs dirige las solicitudes entrantes en los puertos 80 y 443 al equilibrador de carga de nivel de web, pero pasa por alto las solicitudes en todos los demás puertos.

Tenga en cuenta que debe iniciar todas las solicitudes entrantes en todos los puertos. Auditar periódicamente los registros, preste atención a las solicitudes que superen los parámetros esperados como estos pueden indicar intentos de intrusión.

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a>Usar NSGs para bloquear/pass tráfico entre niveles de la aplicación

Cada uno de los niveles de web, business y datos restringen el tráfico entre ellos mediante NSGs. Es decir, el nivel de empresa utiliza un GSN para bloquear todo el tráfico que no se ha originado en el nivel de web y el nivel de datos utiliza un GSN para bloquear todo el tráfico que no se ha originado en el nivel empresarial. Si tiene un requisito para expandir las reglas de GSN para permitir el acceso más amplio a estos niveles, sopese estos requisitos frente a los riesgos de seguridad. Cada nuevo camino entrante representa una oportunidad accidentales o intencionados daños de datos fuga o aplicación.

## <a name="solution-deployment"></a>Implementación de soluciones

Una implementación de una arquitectura de referencia que implementa estas recomendaciones está disponible en Github. Esta arquitectura de referencia incluye una red virtual (VNet), grupo de seguridad de la red (GSN), equilibrador de carga y dos máquinas virtuales (VM).

La arquitectura de referencia se puede implementar con Windows o Linux VM siguiendo las instrucciones siguientes: 

1. Haga clic con el botón secundario del mouse en el botón de abajo y seleccione uno "Abrir vínculo en una nueva pestaña" o "Abrir vínculo en ventana nueva":  
[![Implementar en Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2FvirtualNetwork.azuredeploy.json)

2. Una vez que ha abierto el vínculo en el portal de Azure, debe especificar valores para algunas de las opciones: 
    - El nombre del **grupo de recursos** ya está definido en el archivo de parámetros, así que seleccione **Crear nuevo** y escriba `ra-public-dmz-network-rg` en el cuadro de texto.
    - Seleccione la región en el cuadro desplegable de **ubicación** .
    - No modifique la **Plantilla raíz Uri** o los cuadros de texto de **Uri raíz del parámetro** .
    - Seleccione el **Tipo de sistema operativo** en la lista desplegable cuadro, **windows** o **linux**.
    - Revise los términos y condiciones y luego haga clic en la casilla de verificación **que acepto los términos y condiciones indicadas arriba** .
    - Haga clic en el botón de **compra** .

3. Espere a que la implementación completar.

4. Haga clic con el botón secundario del mouse en el botón de abajo y seleccione uno "Abrir vínculo en una nueva pestaña" o "Abrir vínculo en ventana nueva":  
[![Implementar en Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2Fworkload.azuredeploy.json)

5. Una vez que ha abierto el vínculo en el portal de Azure, debe especificar valores para algunas de las opciones: 
    - El nombre del **grupo de recursos** ya está definido en el archivo de parámetros, así que seleccione **Crear nuevo** y escriba `ra-public-dmz-wl-rg` en el cuadro de texto.
    - Seleccione la región en el cuadro desplegable de **ubicación** .
    - No modifique la **Plantilla raíz Uri** o los cuadros de texto de **Uri raíz del parámetro** .
    - Revise los términos y condiciones y luego haga clic en la casilla de verificación **que acepto los términos y condiciones indicadas arriba** .
    - Haga clic en el botón de **compra** .

6. Espere a que la implementación completar.

7. Haga clic con el botón secundario del mouse en el botón de abajo y seleccione uno "Abrir vínculo en una nueva pestaña" o "Abrir vínculo en ventana nueva":  
[![Implementar en Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2Fsecurity.azuredeploy.json)

8. Una vez que ha abierto el vínculo en el portal de Azure, debe especificar valores para algunas de las opciones: 
    - El nombre del **grupo de recursos** ya está definido en el archivo de parámetros, así que seleccione **Usar existente** y escriba `ra-public-dmz-network-rg` en el cuadro de texto.
    - Seleccione la región en el cuadro desplegable de **ubicación** .
    - No modifique la **Plantilla raíz Uri** o los cuadros de texto de **Uri raíz del parámetro** .
    - Revise los términos y condiciones y luego haga clic en la casilla de verificación **que acepto los términos y condiciones indicadas arriba** .
    - Haga clic en el botón de **compra** .

9. Espere a que la implementación completar.

10. Los archivos de parámetro incluyen nombre de usuario administrador modificable y la contraseña para todas las máquinas virtuales y se recomienda cambiar inmediatamente ambas. Para cada máquina virtual de la implementación, selecciónelo en el portal de Azure y, a continuación, haga clic en **Restablecer la contraseña** en el módulo de **compatibilidad + solución de problemas** . Seleccione **Restablecer la contraseña** en el cuadro de lista desplegable de **modo** , seleccione un nuevo **nombre de usuario** y **contraseña**. Haga clic en el botón **Actualizar** para conservar.


<!-- links -->

[availability-set]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-3-tier-vm.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[iptables]: https://help.ubuntu.com/community/IptablesHowTo
[lb-probe]: ../load-balancer/load-balancer-custom-probe-overview.md
[load-balancer]: ../load-balancer/load-balancer-internet-overview.md
[network-security-group]: ../virtual-network/virtual-networks-nsg.md
[ra-vpn]: ./guidance-hybrid-network-vpn.md
[ra-expressroute]: ./guidance-hybrid-network-expressroute.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[0]: ./media/blueprints/hybrid-network-secure-vnet-dmz.png "Arquitectura de red híbrida segura"
