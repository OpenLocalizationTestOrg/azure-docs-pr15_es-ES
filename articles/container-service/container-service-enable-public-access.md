<properties
   pageTitle="Habilitar el acceso del público a una aplicación de ACS | Microsoft Azure"
   description="Cómo habilitar el acceso del público a un servicio del contenedor de Azure."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contenedores, Micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2016"
   ms.author="timlt"/>

# <a name="enable-public-access-to-an-azure-container-service-application"></a>Habilitar el acceso del público a una aplicación de servicio del contenedor de Azure

Cualquier contenedor DC/OS en el [grupo público agente](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) de ACS se expone automáticamente a internet. De forma predeterminada, los puertos **80**, **443**, se abren **8080** y cualquier contenedor (pública) escuchando en esos puertos sean accesibles. Este artículo le muestra cómo abrir más puertos para las aplicaciones de servicio del contenedor de Azure.

## <a name="open-a-port-portal"></a>Abrir un puerto (portal) 

En primer lugar, es necesario abrir el puerto que queremos.

1. Inicie sesión en el portal.
2. Busque el grupo de recursos que implementa el servicio del contenedor de Azure.
3. Seleccione el equilibrador de carga de agente (que se denomina similar a **XXXX-agente-libras-XXXX**).

    ![Equilibrador de carga del servicio de contenedor de Azure](media/container-service-dcos-agents/agent-load-balancer.png)

4. Haga clic en **sondeos** y, a continuación, en **Agregar**.

    ![Equilibrador de carga del servicio de Azure contenedor sondeos](media/container-service-dcos-agents/add-probe.png)

5. Rellene el formulario de sondeo y haga clic en **Aceptar**.

  	| Campo | Descripción |
  	| ----- | ----------- |
  	| Nombre  | Un nombre descriptivo del sondeo. |
  	| Puerto  | Puerto del contenedor de prueba. |
  	| Ruta de acceso  | (Cuando en modo HTTP) La ruta de acceso del sitio Web relativa a sondeo. HTTPS incompatibles. |
  	| Intervalo | Los intentos de la cantidad de tiempo entre el sondeo, en segundos. |
  	| Umbral de mal estado | Número de sondeos consecutivos intentos antes de considerar el contenedor en mal estado. | 
    

6. En las propiedades del equilibrador de carga de agente, haga clic en **reglas de equilibrio de carga** y, a continuación, en **Agregar**.

    ![Reglas del equilibrador de carga de servicio de contenedor de Azure](media/container-service-dcos-agents/add-balancer-rule.png)

7. Rellene el formulario del equilibrador de carga y haga clic en **Aceptar**.

  	| Campo | Descripción |
  	| ----- | ----------- |
  	| Nombre  | Un nombre descriptivo del equilibrador de carga. |
  	| Puerto  | El puerto entrante público. |
  	| Puerto de back-end | El puerto público interno del contenedor para enrutar el tráfico a. |
  	| Grupo de back-end | Los contenedores de este grupo será el destino de este equilibrador de carga. |
  	| Sondeo | El sondeo se utiliza para determinar si un destino en el **grupo de back-end** es correcto. |
  	| Persistencia de sesión | Determina cómo se debe gestionar el tráfico de un cliente de la duración de la sesión.<br><br>**Ninguno**: sucesivas solicitudes desde el mismo cliente pueden corregirse por cualquier contenedor.<br>**IP de cliente**: sucesivas solicitudes desde la misma IP del cliente se administran por el mismo contenedor.<br>**IP del cliente y protocolo**: sucesivas solicitudes desde la misma combinación de IP y protocolo de cliente se administran por el mismo contenedor. |
  	| Tiempo de espera inactivo | TCP (solo) En minutos, el tiempo que debe tener un cliente TCP/HTTP abrir sin depender *de mantenimiento* mensajes. |

## <a name="add-a-security-rule-portal"></a>Agregar una regla de seguridad (portal)

A continuación, es necesario agregar una regla de seguridad que enruta el tráfico desde nuestro puerto abierto a través del firewall.

1. Inicie sesión en el portal.
2. Busque el grupo de recursos que implementa el servicio del contenedor de Azure.
3. Seleccione el grupo de seguridad de red agente **público** (que se denomina similar a **XXXX agente público GSN XXXX**).

    ![Grupo de seguridad de red de servicio de contenedor de Azure](media/container-service-dcos-agents/agent-nsg.png)

4. Seleccione **las reglas de seguridad de entrada** y, a continuación, en **Agregar**.

    ![Reglas de grupo de seguridad de red de servicio de contenedor de Azure](media/container-service-dcos-agents/add-firewall-rule.png)

5. Rellene la regla de firewall para permitir el puerto público y haga clic en **Aceptar**.

  	| Campo | Descripción |
  	| ----- | ----------- |
  	| Nombre  | Un nombre descriptivo de la regla de firewall. |
  	| Prioridad | Jerarquía de prioridad para la regla. Cuanto menor sea el número mayor es la prioridad. |
  	| Origen | Restringir el intervalo de direcciones IP entrante para ser permitido o denegado por esta regla. Use **cualquier** para no especificar una restricción. |
  	| Servicio | Seleccione un conjunto de servicios predefinidos que es esta regla de seguridad. En caso contrario, usar **personalizada** para crear su propio. |
  	| Protocolo | Restringir el tráfico basado en **TCP** o **UDP**. Use **cualquier** para no especificar una restricción. |
  	| Intervalo de puertos | Cuando el **servicio** está **personalizado**, especifica el intervalo de puertos que afecta esta regla. Puede utilizar un único puerto, como **80**o un rango como **1024 1500**. |
  	| Acción | Permitir o denegar el tráfico que cumple los criterios. |

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre la diferencia entre [los agentes de DC/OS públicos y privados](container-service-dcos-agents.md).

Obtenga más información sobre la [administración de los contenedores de DC/OS](container-service-mesos-marathon-ui.md).