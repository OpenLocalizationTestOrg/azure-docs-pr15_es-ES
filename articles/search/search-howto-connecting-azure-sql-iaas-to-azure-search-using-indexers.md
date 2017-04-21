<properties 
    pageTitle="Configurar una conexión de un indizador de búsqueda de Azure a SQL Server en una máquina virtual Azure | Microsoft Azure | Indizadores" 
    description="Habilitar las conexiones cifradas y configurar el firewall para permitir conexiones a SQL Server en una máquina virtual Azure (VM) de un indizador en búsqueda de Azure." 
    services="search" 
    documentationCenter="" 
    authors="jack4it" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="09/26/2016" 
    ms.author="jackma"/>

# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Configurar una conexión de un indizador de búsqueda de Azure a SQL Server en una máquina virtual de Azure

Como se indicó [Conexión Azure SQL base de datos](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md#frequently-asked-questions)de búsqueda de Azure utilizar indizadores, crear indizadores frente a **SQL Server en máquinas virtuales de Azure** (o **Máquinas virtuales de SQL Azure** para abreviar) es compatible con la búsqueda de Azure, pero hay algunos requisitos previos relacionados con la seguridad ocuparse de la primera. 

**Duración de la tarea:** Unos 30 minutos, suponiendo que ya había instalado un certificado en la máquina virtual.

## <a name="enable-encrypted-connections"></a>Habilitar las conexiones cifradas

Búsqueda de Azure requiere un canal cifrado para todas las solicitudes de indizador sobre una conexión pública a internet. En esta sección se enumera los pasos para que esto funcione.

1. Compruebe las propiedades del certificado para comprobar que el nombre de asunto es el nombre de dominio completo (FQDN) de la máquina virtual de Azure. Puede usar una herramienta como CertUtils o el complemento de certificados para ver las propiedades. Puede obtener el FQDN de sección de Essentials del módulo de servicio VM, en el campo **etiqueta de nombre de dirección de IP pública/DNS** , en el [portal de Azure](https://portal.azure.com/).

    - Para máquinas virtuales creadas con la plantilla más reciente del **Administrador de recursos** , tiene el formato FQDN `<your-VM-name>.<region>.cloudapp.azure.com`. 

    - Para las versiones anteriores VM creadas como un **clásico** VM, tiene el formato FQDN `<your-cloud-service-name.cloudapp.net>`. 

2. Configurar SQL Server para usar el certificado mediante el Editor del registro (regedit). 

    Aunque el Administrador de configuración de SQL Server con qué frecuencia se utiliza para esta tarea, no puede usar para este escenario. No encontrará el certificado importado porque el nombre completo de la máquina virtual en Azure no coincide con el FQDN según lo determinado por la máquina virtual (identifica el dominio como el equipo local o en el dominio de red al que se une). Cuando los nombres no coinciden, utilice regedit para especificar el certificado.

    - En regedit, vaya a esta clave del registro: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
    La `[MSSQL13.MSSQLSERVER]` elemento varía en función de nombre de instancia y versión. 

    - Establezca el valor de la clave de **certificado** para la **huella digital** del certificado SSL importado la máquina virtual.

    Hay varias maneras de obtener la huella digital, algunos mejor que otros. Si se copia desde el complemento **certificados** de MMC, se recoger probablemente un invisible llevando carácter [tal como se describe en este artículo de soporte técnico](https://support.microsoft.com/kb/2023869/), que genera un error cuando intenta una conexión. Existen varias soluciones para corregir este problema. La más sencilla es retroceso sobre y, a continuación, vuelva a escribir el primer carácter de la huella digital para eliminar el carácter inicial en el campo de valor de clave en regedit. Como alternativa, puede usar una herramienta diferente para copiar la huella digital.

3. Conceder permisos a la cuenta de servicio. 

    Asegúrese de que la cuenta de servicio de SQL Server se concede permiso adecuado en la clave privada del certificado SSL. Si se pasan por alto este paso, SQL Server no se iniciará. Puede usar el complemento **certificados** o **CertUtils** para esta tarea.

4. Reinicie el servicio SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configurar la conectividad de SQL Server en la máquina virtual

Después de configurar la conexión cifrada requerida por la búsqueda de Azure, hay pasos de configuración adicionales intrínsecos a SQL Server en máquinas virtuales de Azure. Si aún no lo ha hecho, el siguiente paso es Finalizar configuración mediante uno de estos artículos:

- Un **Administrador de recursos** VM, vea [conectarse a una máquina Virtual de SQL Server en Azure con el Administrador de recursos](../virtual-machines/virtual-machines-windows-sql-connect.md). 

- Para un **clásico** VM, vea [conectarse a una máquina Virtual de SQL Server en Azure clásico](../virtual-machines/virtual-machines-windows-classic-sql-connect.md).

En particular, revise la sección de cada artículo para "conectarse a través de internet".

## <a name="configure-the-network-security-group-nsg"></a>Configurar el grupo de seguridad de la red (NSG)

No es inusual configurar GSN y extremo Azure correspondiente o la lista de Control de acceso (ACL) para hacer que su máquina virtual de Azure accesibles a otras partes. Lo más probable es que ha hecho esto antes de permitir su propia lógica de la aplicación para conectarse a su VM de SQL Azure. Es no diferente para una conexión de búsqueda de Azure su VM de SQL Azure. 

Los siguientes vínculos proporcionan instrucciones sobre la configuración de GSN para implementaciones de máquina virtual. Siga estas instrucciones para ACL un extremo de Azure búsqueda en función de su dirección IP.

> [AZURE.NOTE] Para obtener información general, vea [¿Qué es un grupo de seguridad de red?](../virtual-network/virtual-networks-nsg.md)

- Un **Administrador de recursos** VM, vea [cómo crear NSGs para implementaciones de ARM](../virtual-network/virtual-networks-create-nsg-arm-pportal.md). 

- Para un **clásico** VM, vea [cómo crear NSGs para implementaciones clásico](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

Direcciones IP pueden suponer algunos retos que son superar fácilmente si conoce el problema y posibles soluciones alternativas. Las secciones restantes proporcionan recomendaciones para tratar problemas relacionados con las direcciones IP de las ACL.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Restringir el acceso a la dirección IP de servicio de búsqueda

Se recomienda restringir el acceso a la dirección IP de su servicio de búsqueda en la ACL en lugar de hacer que su las máquinas virtuales de SQL Azure abierto para las solicitudes de conexión. Puede averiguar fácilmente la dirección IP haga ping en el nombre de dominio completo (por ejemplo, `<your-search-service-name>.search.windows.net`) de su servicio de búsqueda.

#### <a name="managing-ip-address-fluctuations"></a>Administrar las variaciones de direcciones IP

Si su servicio de búsqueda contiene solo una unidad de búsqueda (es decir, una réplica y una partición), cambiará la dirección IP durante el reinicio del servicio rutinaria, invalidar una ACL existente con la dirección IP de su servicio búsqueda.

Una manera de evitar el error de conectividad subsiguientes es usar más de una réplica y una partición de búsqueda de Azure. Si lo hace, aumenta el costo, pero también soluciona el problema de la dirección IP. En búsqueda de Azure, las direcciones IP no cambian cuando tiene más de una unidad de búsqueda.

Un segundo enfoque es permitir la conexión a un error y, a continuación, vuelva a configurar las ACL de la GSN. En promedio, puede esperar direcciones IP para cambiar cada pocas semanas. Para los clientes que realice la indización controlado con poca frecuencia, este enfoque podría ser viable.

Es un tercer enfoque viable (pero no especialmente seguro) especificar el intervalo de direcciones IP de la región de Azure donde se le proporciona el servicio de búsqueda. La lista de intervalos de IP desde la que se asignan direcciones IP públicas a recursos de Azure se publica en [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Incluir las direcciones IP portal de búsqueda de Azure

Si está utilizando el portal de Azure para crear un indizador, lógica de portal de la búsqueda de Azure también necesita acceso a sus VM de SQL Azure durante la hora de creación. Direcciones IP de portal Azure búsqueda pueden encontrarse haciendo ping `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Pasos siguientes

Con la configuración de forma, ahora puede especificar un servidor SQL Server en máquina virtual de Azure como origen de datos para un indizador de búsqueda de Azure. Para obtener más información, consulte [Conectar el base de datos de SQL a Azure a utilizar indizadores de búsqueda de Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) .
