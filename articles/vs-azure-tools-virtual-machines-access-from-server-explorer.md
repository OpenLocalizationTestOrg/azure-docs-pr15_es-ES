<properties
   pageTitle="Obtener acceso a máquinas virtuales de Windows Azure desde el Explorador de servidor | Microsoft Azure"
   description="Obtener información general sobre cómo ver, crear y administrar Azure máquinas virtuales (VM) en el Explorador de servidor de Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>Obtener acceso a máquinas virtuales de Windows Azure desde el Explorador de servidores

Usando el Explorador de servidores de Visual Studio, puede mostrar información sobre sus máquinas virtuales alojados en Azure.

## <a name="accessing-virtual-machines-in-server-explorer"></a>Obtener acceso a máquinas virtuales en el Explorador de servidores

Si tiene máquinas virtuales alojados en Azure, puede acceder a ellos en el Explorador de servidores. Primero debe iniciar una sesión su suscripción de Azure para ver los servicios móviles. Para iniciar sesión, abra el menú contextual para el nodo de Azure en el Explorador de servidor y elija **Conectar con Microsoft Azure**.

### <a name="to-get-information-about-your-virtual-machines"></a>Para obtener información sobre sus máquinas virtuales

1. En el Explorador de servidor, elija una máquina virtual y, a continuación, elija la tecla F4 para mostrar la ventana de propiedades.

    La siguiente tabla muestra qué propiedades están disponibles, pero son todas de sólo lectura. Para cambiarlas, utilice el [portal clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

  	|(Propiedad)|Descripción|
  	|---|---|
  	|Nombre DNS|La dirección URL con la dirección de Internet de la máquina virtual.|
  	|Entorno|Para una máquina virtual, el valor de esta propiedad es siempre producción.|
  	|Nombre|El nombre de la máquina virtual.|
  	|Tamaño|El tamaño de la máquina virtual, que refleja la cantidad de memoria y espacio en disco disponible. Para obtener más información, vea Cómo: configurar tamaños de máquina Virtual.|
  	|Estado|Valores son Inicio, introducción, detener, detenido y recuperar estado. Si aparece el estado de recuperación, se desconoce el estado actual. Los valores para esta propiedad difieren de los valores que se usan en el [portal de clásico de Azure](http://go.microsoft.com/fwlink/?LinkID=213885).|
  	|SubscriptionID|El identificador de la suscripción de su cuenta de Azure. Puede mostrar esta información en el [portal de clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885) viendo las propiedades de una suscripción.|

1. Elija un nodo de extremo y, a continuación, ver la ventana de **Propiedades** .

1. La siguiente tabla describe las propiedades disponibles de extremos, pero son de solo lectura. Para agregar o editar los extremos de una máquina virtual, use el [portal clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885). 

  	|(Propiedad)|Descripción|
  	|---|---|
  	|Nombre|Identificador del extremo.|
  	|Puerto privado|El puerto de acceso a la red interna a la aplicación.|
  	|Protocolo|El protocolo que usa la capa de transporte para este extremo, TCP o UDP.|
  	|Puerto público|El puerto que se usa para el acceso del público a la aplicación.|

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo usar las funciones de Azure en Visual Studio, consulte [Uso de escritorio remoto con las funciones de Azure](vs-azure-tools-remote-desktop-roles.md).
