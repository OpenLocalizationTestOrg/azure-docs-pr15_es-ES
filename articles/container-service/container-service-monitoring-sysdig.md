<properties
   pageTitle="Supervisar un clúster de servicio del contenedor de Azure con Sysdig | Microsoft Azure"
   description="Supervisar un clúster de servicio del contenedor de Azure con Sysdig."
   services="container-service"
   documentationCenter=""
   authors="rbitia"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contenedores, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/08/2016"
   ms.author="t-ribhat"/>

# <a name="monitor-an-azure-container-service-cluster-with-sysdig"></a>Monitor de un clúster de servicio del contenedor de Azure con Sysdig

En este artículo, se implementará a agentes Sysdig en todos los nodos de agente en el clúster de servicio del contenedor de Azure. Necesita una cuenta con Sysdig para esta configuración. 

## <a name="prerequisites"></a>Requisitos previos 

[Implementar](container-service-deployment.md) y [Conectar](container-service-connect.md) un clúster configurado el servicio de contenedor de Azure. Explorar la [maratón IU](container-service-mesos-marathon-ui.md). Vaya a [http://app.sysdigcloud.com](http://app.sysdigcloud.com) para configurar una cuenta de nube Sysdig. 

## <a name="sysdig"></a>Sysdig

Sysdig es un servicio de supervisión que le permite supervisar los contenedores en el clúster. Sysdig se conoce para ayudar a solucionar problemas, pero también tiene las métricas de supervisión básicas de CPU, redes, memoria e i/OS. Sysdig facilita la ver qué contenedores están trabajando los más difíciles o esencialmente con más memoria y la CPU. Esta vista está en la sección "Información general", que se encuentra actualmente en beta. 

![Interfaz de usuario de Sysdig](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Configurar una implementación de Sysdig con maratón

Estos pasos le muestran cómo configurar e implementar aplicaciones Sysdig a su clúster con maratón. 

Obtener acceso a la interfaz de usuario de DC/OS a través de [http://localhost: 80 /](http://localhost:80/) una vez en la interfaz de usuario de DC/OS vaya a "Universo", que se encuentra en la parte inferior izquierda y, a continuación, busque "Sysdig".

![Sysdig en DC/OS universo](./media/container-service-monitoring-sysdig/sysdig1.png)

Ahora para completar la configuración necesita una cuenta de nube Sysdig o una cuenta de prueba gratuita. Una vez que ha iniciado sesión el sitio Web de nube de Sysdig, haga clic en el nombre de usuario y, en la página verá su "tecla de acceso". 

![Clave API Sysdig](./media/container-service-monitoring-sysdig/sysdig2.png) 

A continuación, escriba la clave de acceso a la configuración de Sysdig dentro del universo de DC/OS. 

![Configuración de Sysdig en el universo DC/OS](./media/container-service-monitoring-sysdig/sysdig3.png)

Establecer ahora las instancias de 10000000 así siempre que se agrega un nuevo nodo al clúster Sysdig harán automáticamente desplegar un agente en ese nuevo nodo. Esta es una solución provisional para asegurarse de que se implementará Sysdig a todos los agentes nuevos en el clúster. 

![Configuración de Sysdig en DC, universo OS-instancias](./media/container-service-monitoring-sysdig/sysdig4.png)

Una vez que haya instalado el paquete de navegar a la UI Sysdig y podrá explorar los indicadores de uso diferentes para los contenedores en el clúster. 