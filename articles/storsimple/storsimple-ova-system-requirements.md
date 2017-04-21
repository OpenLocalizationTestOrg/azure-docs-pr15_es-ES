<properties
   pageTitle="Requisitos del sistema de matriz Virtual de StorSimple"
   description="Obtenga más información sobre el software y los requisitos de red de la matriz Virtual de StorSimple"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/17/2016"
   ms.author="alkohli"/>

# <a name="storsimple-virtual-array-system-requirements"></a>Requisitos del sistema de matriz Virtual de StorSimple

## <a name="overview"></a>Información general

Este artículo describe los requisitos del sistema importantes de la matriz Virtual de Microsoft Azure StorSimple (también conocido como StorSimple local virtual dispositivo o StorSimple virtual) y para los clientes de almacenamiento obtener acceso a la matriz. Se recomienda revisar la información detenidamente antes de implementar el sistema de StorSimple y, a continuación, consulte volver a él según sea necesario durante la implementación y las operaciones subsiguientes.

Se incluyen los requisitos del sistema:

-   **Requisitos de software para clientes de almacenamiento** - describe las plataformas de virtualización compatibles, los exploradores web, iniciadores iSCSI, clientes SMB, los requisitos mínimos dispositivo virtual y los requisitos adicionales para los sistemas operativos.

-   **Requisitos para el dispositivo StorSimple a redes** : proporciona información sobre los puertos que deben estar abiertos en el firewall para permitir el tráfico de administración, nube o iSCSI.

La información de requisitos del sistema de StorSimple publicada en este artículo se aplica solo a StorSimple Virtual matrices.

- Para dispositivos 8000 serie, vaya a los [requisitos del sistema para su dispositivo de la serie 8000 StorSimple](storsimple-system-requirements.md).
 
- Para dispositivos serie 7000, vaya a [requisitos del sistema para el dispositivo de serie 5000-7000 StorSimple](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).


## <a name="software-requirements"></a>Requisitos de software

Los requisitos de software incluyen la información de versiones de los exploradores web compatibles, SMB, plataformas de virtualización y los requisitos mínimos dispositivo virtual.

### <a name="supported-virtualization-platforms"></a>Plataformas de virtualización compatibles


| **Hipervisor** | **Versión**                          |
|----------------|--------------------------------------|
| Hyper-V        | Windows Server 2008 R2 SP1 y versiones posteriores |
| VMware ESXi    | 5,5 y posteriores                        |

### <a name="virtual-device-requirements"></a>Requisitos del dispositivo virtual

| **Componente**                                | **Requisito**            |
|----------------------------------------------|----------------------------|
| Número mínimo de procesadores virtuales (núcleos) | 4                          |
| Mínimo de memoria (RAM)                         | 8 GB                       |
| Espacio de disco<sup>1</sup>                       | Disco OS - 80 GB <br></br>Disco de datos - 500 GB a 8 TB|
| Número mínimo de interfaces de red       | 1                          |
| Mínimo de ancho de banda de Internet<sup>2</sup>       | 5 Mbps                     |

<sup>1</sup> - fino aprovisionado

<sup>2</sup> - los requisitos de red pueden variar según la tasa de cambio diaria de datos. Por ejemplo, si un dispositivo debe realizar copias de seguridad 10 GB o más cambios durante un día, la copia de seguridad diaria mediante una conexión de Mbps 5 podría tardar hasta 4,25 horas (si los datos no se pudieran comprimidos o desduplicados).

### <a name="supported-web-browsers"></a>Exploradores web

| **Componente**     | **Versión** | **Requisitos adicionales y notas** |
|-------------------|-----------------|-----------------------------------|
| Microsoft Edge    | Versión más reciente  |                                   |
| Internet Explorer | Versión más reciente  | Probado con Internet Explorer 11  |
| Google Chrome     | Versión más reciente  | Probado con Chrome 46             |

### <a name="supported-storage-clients"></a>Clientes de almacenamiento compatibles 

Son los siguientes requisitos de software para los iniciadores iSCSI que tienen acceso a la matriz Virtual StorSimple (configurado como un servidor de iSCSI).

| **Sistemas operativos compatibles** | **Versión requerida** | **Requisitos adicionales y notas** |
| --------------------------- | ---------------- | ------------- |
| Windows Server              | 2008R2 SP1, 2012, 2012R2 |Puede crear StorSimple thin preparación y totalmente aprovisionado volúmenes. No puede crear volúmenes aprovisionados parcialmente. StorSimple iSCSI volúmenes solo se admiten para: <ul><li>Volúmenes simples en discos básicos de Windows.</li><li>Windows NTFS para dar formato a un volumen.</li>|


Son los siguientes requisitos de software para los clientes SMB que tienen acceso a la matriz Virtual StorSimple (configurado como un servidor de archivos).

| **Versión SMB** |
|-------------|
| SMB 2.x     |
| SMB 3.0     |
| SMB 3.02    |

> [AZURE.IMPORTANT] No copiar o almacenar archivos protegidos por Windows archivo sistema CIFRADOS al servidor de archivos StorSimple Virtual matriz; se producirá una configuración no compatible. 

## <a name="networking-requirements"></a>Requisitos de red 

La siguiente tabla enumeran los puertos que deben estar abiertos en el firewall para permitir el tráfico de administración, nube, SMB o iSCSI. En esta tabla, *en* o *entrante* se refiere a la dirección desde la que las solicitudes entrantes de cliente, tener acceso a su dispositivo. *Out* o *saliente* hace referencia a la dirección en que el dispositivo StorSimple envía datos externamente, más allá de la implementación: por ejemplo, de salida a Internet.

| **Puerto Nº<sup>1</sup>** | **Entrada o de salida** | **Ámbito de puerto** | **Obligatorio**              | **Notas**                                                                                                            |
|--------------------------|---------------|----------------|---------------------------|----------------------------------------------------------------------------------------------------------------------|
| TCP 80 (HTTP)            | Hacia afuera           | WAN            | No                        | Puerto de salida se usa para el acceso a Internet para recuperar las actualizaciones. <br></br>El proxy web saliente es configurable por el usuario. |
| TCP 443 (HTTPS)          | Hacia afuera           | WAN            | Sí                       | Puerto de salida se usa para obtener acceso a datos en la nube. <br></br>El proxy web saliente es configurable por el usuario. |
| UDP 53 (DNS)             | Hacia afuera           | WAN            | En algunos casos; Consulte las notas. | Este puerto es necesario únicamente si usa un servidor DNS basado en Internet. <br></br> **Nota**: si implementar un servidor de archivos, se recomienda usar el servidor DNS local.|
| UDP 123 (NTP)            | Hacia afuera           | WAN            | En algunos casos; Consulte las notas. | Este puerto es necesario únicamente si usa un servidor NTP basados en Internet.<br></br> **Nota:** Si implementar un servidor de archivos, se recomienda sincronizar la hora con los controladores de dominio de Active Directory.  |
| TCP 80 (HTTP)           | En            | LAN            | Sí                       | Este es el puerto de entrada para la interfaz de usuario local en el dispositivo StorSimple para la administración local. <br></br> **Nota**: obtener acceso a la interfaz de usuario local a través de HTTP redirige automáticamente a HTTPS.|
| TCP 443 (HTTPS)          | En            | LAN            | Sí                       | Este es el puerto de entrada para la interfaz de usuario local en el dispositivo StorSimple para la administración local.|
| TCP 3260 (iSCSI)         | En            | LAN            | No                        | Este puerto se usa para tener acceso a datos sobre iSCSI.|

<sup>1</sup> hay puertos entrantes deben estar abiertos en Internet.

> [AZURE.IMPORTANT] Asegúrese de que el firewall no modificar o descifrar cualquier tráfico SSL entre el dispositivo de StorSimple y Azure.


### <a name="url-patterns-for-firewall-rules"></a>Modelos de dirección URL para las reglas de firewall 

Los administradores de red a menudo pueden configurar reglas de firewall avanzado basadas en modelos de dirección URL para filtrar la entrada y el tráfico saliente. La matriz virtual y el servicio Administrador de StorSimple dependen de otras aplicaciones de Microsoft como Bus de servicio de Azure, Control de acceso de Azure Active Directory, cuentas de almacenamiento y servidores de Microsoft Update. Los modelos de dirección URL asociados con estas aplicaciones pueden utilizarse para configurar las reglas de firewall. Es importante entender que pueden cambiar los patrones de URL asociados con estas aplicaciones. A su vez se necesita el Administrador de red supervisar y actualizar reglas de firewall para su StorSimple como y cuando sea necesario. 

Le recomendamos que configure las reglas de firewall para el tráfico saliente, en función de StorSimple fijado direcciones IP, libremente en la mayoría de los casos. Sin embargo, puede usar la información siguiente para establecer reglas de firewall avanzado que son necesarios para crear entornos seguros.

> [AZURE.NOTE] 
> 
> - El dispositivo (de origen) direcciones IP siempre debe establecerse a todas las interfaces de red habilitados para la nube. 
> - El destino que se debe establecer direcciones IP en [intervalos de IP del centro de datos de Azure](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


| Modelo de dirección URL                                                      | Funcionalidad del componente                                           |
|------------------------------------------------------------------|---------------------------------------------------------------|
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`   | Servicio de administrador de StorSimple<br>Servicio de Control de acceso<br>Bus de servicio de Azure|
|`http://*.backup.windowsazure.com`|Registro de dispositivo|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|Revocación de certificados |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Cuentas de almacenamiento de Azure y supervisión |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Servidores de Microsoft Update<br>                        |
| `http://*.deploy.akamaitechnologies.com`                         |CDN de Akamai |
| `https://*.partners.extranet.microsoft.com/*`                    | Paquete de compatibilidad                                                  |
| `http://*.data.microsoft.com `                   | Servicio de telemetría en Windows, consulte la [actualización de la experiencia del cliente y diagnóstico telemetría](https://support.microsoft.com/en-us/kb/3068708)                                                  |

## <a name="next-step"></a>Siguiente paso

-   [Preparar el portal para implementar la matriz Virtual de StorSimple](storsimple-ova-deploy1-portal-prep.md)
