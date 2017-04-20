<properties
   pageTitle="Instalar DC/OS CLI | Microsoft Azure"
   description="Instale el controlador de dominio/OS CLI."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contenedores, servicios de Micro, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/10/2016"
   ms.author="rogardle"/>

>[AZURE.NOTE] Este es para trabajar con clústeres basados en DC/OS ACS. No es necesario hacer esto para clústeres ACS basado en conjunto.

En primer lugar, [conectarse a su clúster basados en DC/OS ACS](../articles/container-service/container-service-connect.md). Una vez que termine, puede instalar la CLI DC/OS en el equipo cliente con los siguientes comandos:

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Si está utilizando una versión antigua de Python, verá algunas "InsecurePlatformWarnings". Puede ignorar estos.

Para empezar sin tener que reiniciar su shell, ejecute:

```bash
source ~/.bashrc
```

Este paso no será necesario cuando se inicia depósitos nuevos.

Ahora puede confirmar que la CLI está instalada:

```bash
dcos --help
```