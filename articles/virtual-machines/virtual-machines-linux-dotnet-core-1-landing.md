<properties
   pageTitle="Azure Máquina Virtual DotNet Core Tutorial 1 | Microsoft Azure"
   description="Tutorial de DotNet principales de Azure Máquina Virtual"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# <a name="automating-application-deployments-to-azure-virtual-machines"></a>Automatización de implementación de aplicaciones para Azure máquinas virtuales de Windows

Detalles de esta serie de cuatro partes implementar y configurar recursos Azure y Azure recursos administrar plantillas de aplicaciones. En esta serie, se implementa una plantilla de ejemplo y examina la plantilla de implementación. El objetivo de esta serie es instruir a en la relación entre los recursos de Azure y proporcionar manos en experiencia de implementación de plantillas de administrador de recursos de Azure completamente integrado. Este documento supone un nivel básico de conocimiento con el Administrador de recursos de Azure, antes de comenzar este tutorial familiarizarse con los conceptos básicos de administrador de recursos de Azure.

## <a name="music-store-application"></a>Aplicación de almacenamiento de música

El ejemplo utilizado en esta serie es un .net aplicación principal simular una experiencia de compra de la tienda de música. Se puede implementar esta aplicación al sistema virtual un Linux o Windows, implementaciones de ejemplo se han creado para ambos. La aplicación incluye una aplicación web y una base de datos SQL. Antes de leer los artículos de esta serie, implementar la aplicación con el botón de implementación que se encuentra en esta página. Cuando está completamente distribuido, la arquitectura de aplicación / Azure es similar a la siguiente diagrama. 

La plantilla de administrador de recursos de almacenamiento de música se encuentran aquí, [Música almacén Linux plantilla]( https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db)

![Aplicación de almacenamiento de música](./media/virtual-machines-linux-dotnet-core/music-store.png)

Cada uno de estos componentes, incluida la plantilla asociar JSON se describe en los siguientes artículos de cuatro.

- [**Arquitectura de la aplicación**](./virtual-machines-linux-dotnet-core-2-architecture.md) : componentes de la aplicación, como sitios web y las bases de datos necesita estar hospedadas en recursos de equipo Azure como máquinas virtuales y las bases de datos de SQL Azure. Este documento se recorre necesidad de cálculo de asignación, a recursos de Azure e implementar estos recursos con una plantilla de administrador de recursos de Azure. 

- [**Acceso y seguridad**](./virtual-machines-linux-dotnet-core-3-access-security.md) : cuando se hospedan aplicaciones en Azure, es necesario tener en cuenta cómo se accede a la aplicación y cómo los diferentes componentes de acceso de aplicación entre sí. Este documento detalla lo que proporciona y proteger el acceso de internet a una aplicación y entre los componentes de la aplicación.

- [**Disponibilidad y escala**](./virtual-machines-linux-dotnet-core-4-availability-scale.md) : disponibilidad y escala hacen referencia a la capacidad de aplicaciones para mantenerse ejecución durante el tiempo de inactividad de infraestructura y la capacidad de recursos de cálculo para satisfacer la demanda de la aplicación. Este documento detalles los componentes necesarios para implementar un equilibrio de carga y la aplicación altamente disponible.

- [**Implementación de aplicaciones**](./virtual-machines-linux-dotnet-core-5-app-deployment.md) - al implementar aplicaciones en Azure máquinas virtuales de Windows, debe tener en cuenta el método que se instalan los archivos binarios de la aplicación en la máquina Virtual. Este documento detalla automatizar la instalación de aplicaciones utilizando las extensiones de Script de Azure Máquina Virtual personalizada.

El objetivo al desarrollar plantillas de administrador de recursos de Azure es automatizar la implementación de infraestructura de Azure y la instalación y configuración de las aplicaciones que se hospeda en esta infraestructura de Azure. Trabajar con estos artículos proporciona un ejemplo de esta experiencia.

## <a name="deploy-the-music-store-application"></a>Implementar la aplicación de almacenamiento de música

La aplicación de almacenamiento de música se puede implementar mediante este botón.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fdotnet-core-sample-templates%2Fmaster%2Fdotnet-core-music-linux%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

La plantilla de administrador de recursos de Azure requiere los siguientes valores de parámetro.

|Nombre de parámetro |Descripción   |
|---|---|
|SSHKEYDATA   | Datos de clave SSH utilizados para proteger el acceso a la máquina Virtual. Para obtener información sobre la creación de un par de claves SSH, consulte [crear SSH teclas para máquinas virtuales de Linux en Azure](virtual-machines-linux-mac-create-ssh-keys.md).  |
|ADMINUSERNAME   | Nombre de usuario de administración que se usa en la máquina virtual y la base de datos de SQL Azure.  |
|SQLADMINPASSWORD | Contraseña que se usa en la base de datos de SQL Azure.  |
|NUMBEROFINSTANCES | El número de máquinas virtuales de Windows que debe crearse. Cada una de estas máquinas virtuales hospedar la aplicación web de la tienda de música y todo el tráfico se cargan equilibradas en ellos. |
|PUBLICIPADDRESSDNSNAME | Nombre DNS asociado con la dirección IP pública globalmente único. |

Cuando haya finalizado la implementación de plantilla, vaya a la dirección IP pública mediante cualquier explorador de internet. .Net se presentará Core música sitio.

## <a name="next-steps"></a>Pasos siguientes

<hr>

[Paso 1: arquitectura de la aplicación con las plantillas de administrador de recursos de Azure](./virtual-machines-linux-dotnet-core-2-architecture.md)

[Paso 2: acceso y seguridad en Azure plantillas de administrador de recursos](./virtual-machines-linux-dotnet-core-3-access-security.md)

[Paso 3: disponibilidad y escala en Azure plantillas de administrador de recursos](./virtual-machines-linux-dotnet-core-4-availability-scale.md)

[Paso 4: implementación de la aplicación con las plantillas de administrador de recursos de Azure](./virtual-machines-linux-dotnet-core-5-app-deployment.md)


