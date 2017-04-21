<properties
   pageTitle="Conectarse a un clúster de servicio del contenedor de Azure | Microsoft Azure"
   description="Conectarse a un clúster de servicio del contenedor de Azure mediante un túnel SSH."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contenedores, Micro-services, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>


# <a name="connect-to-an-azure-container-service-cluster"></a>Conectarse a un clúster de servicio del contenedor de Azure

DC/sistema operativo y clústeres enjambre Docker que se implementan el servicio del contenedor de Azure exponen extremos de resto. No obstante, estos extremos no están abiertos al exterior. Para administrar estos extremos, debe crear un túnel de Shell seguro (SSH). Después de un SSH túnel se ha establecido, puede ejecutar comandos en los extremos de clúster y ver la interfaz de usuario de clúster a través de un explorador en su propio sistema. Este documento le guiará a través de la creación de un túnel SSH de Linux, OS X y Windows.

>[AZURE.NOTE] Puede crear una sesión SSH con un sistema de administración de clúster. Sin embargo, no se recomienda esto. Trabaje directamente en un sistema de administración expone el riesgo para los cambios de configuración accidental.   

## <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>Crear un túnel SSH en Linux o OS X

Lo primero que hay que hacer cuando se crea un túnel SSH en Linux o OS X es localizar el nombre DNS público de patrones de equilibrio de carga. Para ello, expanda el grupo de recursos para cada recurso que se va a mostrar. Busque y seleccione la dirección IP pública del patrón. Se abrirá una placa que contiene información sobre la dirección IP pública, que incluye el nombre DNS. Guardar este nombre para su uso posterior. <br />


![Nombre DNS público](media/pubdns.png)

Ahora, abra un shell y ejecute el siguiente comando donde:

**Puerto** es del extremo de los que desea exponer. En conjunto, es 2375. Para DC/OS, utilice el puerto 80.  
**Nombre de usuario** es el nombre de usuario que se proporcionó cuando se implementa el clúster.  
**DNSPREFIX** es el prefijo DNS que proporciona cuando se implementa el clúster.  
**Zona** es la región donde se encuentra el grupo de recursos.  
**PATH_TO_PRIVATE_KEY** [Opcional] es la ruta de acceso a la clave privada que corresponde a la clave pública proporcionada al crear el clúster de servicio de contenedor. Use esta opción con la i - marcar.

```bash
ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
```
> El puerto de conexión SSH es 2200--no el puerto estándar 22.

## <a name="dcos-tunnel"></a>Túnel de DC/OS

Para abrir un túnel a los extremos relacionados con DC/OS, ejecutar un comando similar al siguiente:

```bash
sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Ahora puede acceder a los extremos relacionados con DC/OS en:

- CONTROLADOR DE DOMINIO O SISTEMA OPERATIVO:`http://localhost/`
- Maratón:`http://localhost/marathon`
- Mesos:`http://localhost/mesos`

Asimismo, puede ponerse en el resto de la API para cada aplicación a través de este túnel.

## <a name="swarm-tunnel"></a>Túnel de conjunto

Para abrir un túnel hasta el extremo de conjunto, ejecutar un comando que tiene un aspecto similar al siguiente:

```bash
ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Ahora puede establecer la variable de entorno DOCKER_HOST como sigue. Aún puede usar su Docker de línea de comandos interfaz como normal.

```bash
export DOCKER_HOST=:2375
```

## <a name="create-an-ssh-tunnel-on-windows"></a>Crear un túnel SSH en Windows

Existen varias opciones para crear túneles SSH en Windows. Este documento describe cómo usar PuTTY para hacerlo.

Descargue PuTTY a su sistema de Windows y ejecute la aplicación.

Escriba un nombre de host está formado por el nombre de usuario de administración de clúster y el nombre DNS público del primer patrón en el clúster. El **Nombre de Host** tendrá este aspecto: `adminuser@PublicDNS`. Escriba 2200 para el **puerto**.

![Configuración masilla 1](media/putty1.png)

Seleccione **SSH** y la **autenticación**. Agregue el archivo de clave privada para la autenticación.

![Configuración masilla 2](media/putty2.png)

Seleccione **túneles** y configurar la siguiente reenviado puertos:
- **Puerto de origen:** Sus preferencias: usar 80 para DC/OS o 2375 de conjunto.
- **Destino:** Usar localhost: 80 para DC/OS o localhost:2375 para conjunto.

En el ejemplo siguiente se está configurado para DC/OS, pero tendrá un aspecto similar para Docker enjambre.

>[AZURE.NOTE] Puerto 80 no debe estar en uso cuando se crea este túnel.

![Configuración masilla 3](media/putty3.png)

Cuando haya terminado, guarde la configuración de conexión y conectar la sesión masilla. Cuando se conecte, puede ver la configuración de puerto en el registro de eventos masilla.

![Registro de eventos masilla](media/putty4.png)

Cuando haya configurado el túnel para DC/OS, puede tener acceso el extremo relacionado en:

- CONTROLADOR DE DOMINIO O SISTEMA OPERATIVO:`http://localhost/`
- Maratón:`http://localhost/marathon`
- Mesos:`http://localhost/mesos`

Cuando haya configurado el túnel para enjambre Docker, puede tener acceso al clúster de conjunto mediante la CLI Docker. En primer lugar debe configurar una variable de entorno de Windows denominada `DOCKER_HOST` con un valor de ` :2375`.

## <a name="next-steps"></a>Pasos siguientes

Implementar y administrar contenedores con DC/OS o conjunto:

- [Trabajar con el servicio de contenedor Azure y DC/OS](container-service-mesos-marathon-rest.md)
- [Trabajar con el servicio de contenedor Azure y un conjunto de Docker](container-service-docker-swarm.md)
