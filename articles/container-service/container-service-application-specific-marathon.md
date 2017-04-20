<properties
   pageTitle="Aplicación o servicio de maratón específica del usuario | Microsoft Azure"
   description="Crear una aplicación o servicio de maratón específica del usuario"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contenedores, maratón, Micro-services, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/12/2016"
   ms.author="rogardle"/>

# <a name="create-an-application-or-user-specific-marathon-service"></a>Crear una aplicación o servicio de maratón específica del usuario

Servicio de contenedor Azure proporciona un conjunto de servidores maestros en el que se preconfigurado Apache Mesos y maratón. Pueden utilizarse para organizar sus aplicaciones en el clúster, pero es mejor no usar los servidores maestros para ello. Por ejemplo, ajustar la configuración de maratón requiere iniciar sesión en los servidores de patrón y realizar cambios, esto promueva únicos servidores maestros que son algo diferentes de lo estándar y que necesite atención y administra de forma independiente. Además, la configuración de un equipo no sea la configuración óptima para otro equipo.

En este artículo, explicaremos cómo agregar una aplicación o servicio de maratón específica del usuario.

Dado que este servicio pertenecerá a un solo usuario o grupo, son gratuitas configurar de forma que deseen. Además, se asegurará de servicio del contenedor de Azure que sigue ejecutando el servicio. Si se produce un error en el servicio, servicio de contenedor de Azure se reinicie por usted. La mayoría de las veces incluso no Observe que tenía el tiempo de inactividad.

## <a name="prerequisites"></a>Requisitos previos

[Implementar una instancia de servicio del contenedor de Azure](container-service-deployment.md) con tipo orchestrator DC/OS y [Asegúrese de que el cliente puede conectarse a su clúster](container-service-connect.md). Además, realice los pasos siguientes.

[AZURE.INCLUDE [install the DC/OS CLI](../../includes/container-service-install-dcos-cli-include.md)]

## <a name="create-an-application-or-user-specific-marathon-service"></a>Crear una aplicación o servicio de maratón específica del usuario

Empiece por crear un archivo de configuración de JSON que define el nombre del servicio de aplicación que desea crear. Aquí usamos `marathon-alice` como nombre del marco. Guarde el archivo como algo parecido a `marathon-alice.json`:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

A continuación, use la CLI DC/OS para instalar la instancia de maratón con las opciones que se establecen en el archivo de configuración:

```bash
dcos package install --options=marathon-alice.json marathon
```

Ahora debe ver la `marathon-alice` servicio se ejecuta en la pestaña servicios de la interfaz de usuario de DC/OS. La interfaz de usuario será `http://<hostname>/service/marathon-alice/` si desea obtener acceso a ella directamente.

## <a name="set-the-dcos-cli-to-access-the-service"></a>Establecer la CLI DC/OS para obtener acceso al servicio

Opcionalmente, puede configurar su CLI DC/OS para obtener acceso a este nuevo servicio estableciendo la `marathon.url` propiedad para que apunten a la `marathon-alice` instancia como sigue:

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

Puede comprobar qué instancia de maratón con los que la CLI trabaja con la `dcos config show` comando. Puede volver a utilizar el servicio de maratón maestro con el comando `dcos config unset marathon.url`.
