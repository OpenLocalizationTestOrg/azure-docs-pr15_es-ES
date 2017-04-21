<properties 
   pageTitle="Ver y modificar nombres de host | Microsoft Azure"
   description="Cómo ver y cambiar los nombres de host para máquinas virtuales de Windows Azure, web y las funciones de trabajo para la resolución de nombres"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="viewing-and-modifying-hostnames"></a>Ver y modificar nombres de host

Para permitir que las instancias de la función que hace referencia el nombre de host, debe establecer el valor del nombre de host en el archivo de configuración de servicio para cada rol. Para ello agregando el nombre de host deseado para el atributo **vmName** del elemento de **función** . El valor del atributo **vmName** se usa como base para el nombre de host de cada instancia de rol. Por ejemplo, si **vmName** es *webrole* y hay tres instancias de esa función, los nombres de host de las instancias será *webrole0*, *webrole1*y *webrole2*. No debe especificar un nombre de host para equipos virtuales en el archivo de configuración, porque el nombre de host para una máquina virtual se rellena basándose en el nombre de la máquina virtual. Para obtener más información acerca de cómo configurar un servicio de Microsoft Azure, vea [Esquema de configuración de servicio de Azure (.cscfg archivo)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Ver los nombres de host

Puede ver los nombres de host de máquinas virtuales y las instancias de función en un servicio de nube con cualquiera de las siguientes herramientas.

### <a name="azure-portal"></a>Portal de Azure

Puede usar el [portal de Azure](http://portal.azure.com) para ver los nombres de host para equipos virtuales en el módulo de introducción para una máquina virtual. Tenga en cuenta que el módulo muestra un valor de **nombre** y el **Nombre de Host**. Aunque inicialmente son los mismos, cambiar el nombre de host no cambiará el nombre de la máquina virtual o una instancia de la función.

Las instancias de función también se pueden visualizar en el portal de Azure, pero cuando se muestran las instancias de un servicio de nube, no se muestra el nombre de host. Verá un nombre para cada instancia, pero ese nombre no representa el nombre de host.

### <a name="service-configuration-file"></a>Archivo de configuración de servicio

Puede descargar el archivo de configuración de servicio para un servicio implementado desde el módulo de **Configurar** del servicio en el portal de Azure. A continuación, puede buscar el atributo **vmName** para el elemento de **nombre de la función** ver el nombre de host. Tenga en cuenta que el nombre de host se utiliza como base para el nombre de host de cada instancia de rol. Por ejemplo, si **vmName** es *webrole* y hay tres instancias de esa función, los nombres de host de las instancias será *webrole0*, *webrole1*y *webrole2*.

### <a name="remote-desktop"></a>Escritorio remoto

Después de habilitar Escritorio remoto (Windows), Windows PowerShell remoto (Windows) o conexiones de SSH (Linux y Windows) a su máquinas virtuales o las instancias de la función, puede ver el nombre de host de una conexión a Escritorio remoto activa de varias formas:

- Escriba el nombre de host en el símbolo o SSH terminal.

- Escriba ipconfig/todo en el comando preguntar (sólo Windows).

- Ver el nombre del equipo en la configuración del sistema (sólo Windows).

### <a name="azure-service-management-rest-api"></a>Administración del servicio de Azure API de REST

Desde un cliente resto, siga estas instrucciones:

1. Asegurarse de que tiene un certificado de cliente para conectarse al portal de Azure. Para obtener un certificado de cliente, siga los pasos que se presentan en [Cómo: descargar y la configuración de publicación de importación y la información de la suscripción](https://msdn.microsoft.com/library/dn385850.aspx). 

1. Establecer una entrada de encabezado denominada x-ms-versión con un valor de 2013-11-01.

1. Enviar una solicitud en el siguiente formato: https://management.core.windows.net/\<identificador de opción\>/servicios/hostedservices/\<nombre de servicio\>?embed detalle = verdadero

1. Busque el elemento de **nombre de host** para cada elemento **RoleInstance** .

>[AZURE.WARNING] También puede ver el sufijo de dominio interno para un servicio de nube de respuesta de llamada resto comprobando el elemento **InternalDnsSuffix** , o bien ejecutando ipconfig/todo desde un símbolo de una sesión de escritorio remoto (Windows) o mediante ejecución /etc/resolv.conf gato desde un terminal SSH (Linux).

## <a name="modifying-a-hostname"></a>Modificar un nombre de host

Puede modificar el nombre de host de máquina virtual o instancia del rol de cargar un archivo de configuración de servicio modificado o cambiarles el nombre del equipo desde una sesión de escritorio remoto.

## <a name="next-steps"></a>Pasos siguientes

[Resolución de nombres (de dominio DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Esquema de configuración de servicio de Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Esquema de configuración de red Virtual Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Especificar la configuración de DNS con archivos de configuración de red](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)
