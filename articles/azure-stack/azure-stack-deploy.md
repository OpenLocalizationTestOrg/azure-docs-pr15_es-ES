<properties
    pageTitle="Antes de implementar la prueba de concepto de pila de Azure | Microsoft Azure"
    description="Ver los requisitos de hardware y el entorno de prueba de concepto de pila de Azure (Administrador de servicios)."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/12/2016"
    ms.author="erikje"/>

# <a name="azure-stack-deployment-prerequisites"></a>Requisitos previos de implementación de pila Azure

Antes de implementar la prueba de concepto de pila de Azure ([Prueba de concepto](azure-stack-poc.md)), asegúrese de que su equipo cumple los siguientes requisitos.
Los requisitos de implementación de Technical Preview 2 para la prueba de concepto son iguales a los necesarios para Technical Preview 1. Por lo tanto, puede usar el mismo hardware que usa para la vista previa de un solo cuadro anterior.

## <a name="hardware"></a>Hardware

| Componente | Mínimo  | Se recomienda |
|---|---|---|
| Unidades de disco: sistema operativo | 1 disco del SO con mínimo de 200 GB disponible para partición de sistema (SSD o unidad de disco duro) | 1 disco del SO con mínimo de 200 GB disponible para partición de sistema (SSD o unidad de disco duro) |
| Unidades de disco: información General de prueba de concepto de pila de Azure | 4 discos. Cada disco proporciona un mínimo de 140 GB de capacidad (SSD o unidad de disco duro). Se usará todos los discos disponibles. | 4 discos. Cada disco proporciona un mínimo de 250 GB de capacidad (SSD o unidad de disco duro). Se usará todos los discos disponibles.|
| Calcular: CPU | Los sistemas: 12 núcleos físicos (total)  | Los sistemas: 16 núcleos físicos (total) |
| Calcular: memoria | 96 GB DE RAM  | 128 GB DE RAM |
| Calcular: BIOS | Hyper-V habilitado (con el soporte técnico de LISTÓN)  | Hyper-V habilitado (con el soporte técnico de LISTÓN) |
| Red: NIC | Windows Server 2012 R2 certificación necesarios para NIC; No hay características especializadas obligatorios | Windows Server 2012 R2 certificación necesarios para NIC; No hay características especializadas obligatorios |
| Certificación del logotipo de hardware | [Certificado para Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certificado para Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0)|

**Configuración de la unidad de disco de datos:** Todas las unidades de datos deben ser del mismo tipo (todas las SA o todas las SATA) y la capacidad. Si se utilizan unidades de disco de SA, las unidades de disco debe estar vinculadas a través de una ruta de acceso (no se proporciona ningún MPIO, compatibilidad con múltiples ruta).

**Opciones de configuración de HBA**
 
- (Recomendado) HBA simple
- RAID HBA – adaptador debe estar configurado en modo de "pasar"
- RAID HBA: discos deben configurarse como un solo disco, RAID-0

**Medios y bus compatibles escriba combinaciones**

-   DISCO DURO SATA

-   UNIDAD DE DISCO DURO SA

-   UNIDAD DE DISCO DURO RAID

-   RAID SSD (si el tipo de medio es desconocido o sin especificar\*)

-   SATA SSD + DISCO DURO SATA

-   SA SSD + SA UNIDAD DE DISCO DURO

\*Controladores RAID sin la capacidad de paso a través no reconocen el tipo de medio. Estos controladores marcará la unidad de disco duro y SSD como no especificado. En ese caso, se utilizará la SSD como almacenamiento persistente en lugar de almacenamiento en caché de dispositivos. Por lo tanto, puede implementar la prueba de concepto de Microsoft Azure pila en esas SSDs.

**Ejemplo HBAs**: LSI 9207 8i, LSI-9300-8i o LSI-9265-8i en modo de paso a través

Ejemplos de configuraciones de OEM están disponibles.

## <a name="operating-system"></a>Sistema operativo

| | **Requisitos**  |
|---|---|
| **Versión del SO** | Windows Server 2012 R2 o posterior. La versión del sistema operativo no crítica antes de que se inicie la implementación, deberá arrancar el equipo host en el disco duro virtual que se incluye en zip de instalación de pila de Azure. El sistema operativo y todas las revisiones necesarias ya están integradas en la imagen. No use las teclas para activar las instancias de Windows Server utilizadas en la prueba de concepto.|

## <a name="deployment-requirements-check-tool"></a>Herramienta de comprobación de requisitos de implementación

Después de instalar el sistema operativo, puede usar el [Comprobador de implementación de Azure pila Technical Preview 2](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) para confirmar que el hardware cumple todos los requisitos.



## <a name="microsoft-azure-active-directory-accounts"></a>Cuentas de Microsoft Azure Active Directory

La implementación de prueba de concepto de Microsoft Azure pila debe estar conectada a Azure. Por consiguiente, debe preparar una cuenta de Microsoft Azure Active Directory antes de ejecutar la secuencia de comandos de PowerShell de implementación. Esta cuenta se convierte en el administrador Global para el inquilino de Azure Active Directory. Se utilizará para aprovisionar y delegar aplicaciones y principales de servicio para todos los servicios de Azure pila interactúan con Azure Active Directory y las API de gráfico. También se utilizará como propietario de la suscripción de proveedor predeterminada (que puede cambiar más adelante). Puede iniciar sesión al portal de administración de su sistema de pila de Azure mediante esta cuenta.

1. Crear una cuenta de Azure AD que es el Administrador de directorio de al menos un Azure Active Directory. Si ya tiene una, que pueda usarlo. En caso contrario, puede crearla gratuitamente en [http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (en China, visite <http://go.microsoft.com/fwlink/?LinkID=717821> en su lugar.)

    Guardar estas credenciales para su uso en el paso 6 de [ejecutar el script de implementación de PowerShell](azure-stack-run-powershell-script.md#run-the-powershell-deployment-script). Esta cuenta de *servicio Administrador* puede configurar y administrar nubes de recursos, las cuentas de usuario, planes de inquilinos, cuotas y precios. En el portal puede crear sitio Web nubes, nubes privadas de máquina virtual, crear planes y administrar suscripciones de usuario.

2. [Crear](azure-stack-add-new-user-aad.md) al menos una cuenta de modo que puede iniciar sesión en la prueba de concepto de pila de Azure como un inquilino.

  	| **Cuenta de Azure Active Directory**  | **¿Compatibles?** |
  	|---|---| 
  	| Cuenta profesional o educativa con suscripción válida de Azure público  | Sí |
  	| Microsoft Account con suscripción válida de Azure público  | No |
  	| Cuenta profesional o educativa con suscripción válida de Azure China  | Sí |
  	| Cuenta profesional o educativa con nosotros gobierno Azure suscripción válida  | Sí |


## <a name="network"></a>Red

### <a name="switch"></a>Cambiar

Un puerto disponible en un conmutador para el equipo de prueba de concepto.  

El equipo de prueba de concepto de pila de Azure admite la conexión a un puerto de conmutador access o tronco. No hay características especializadas son necesarios en el modificador. Si está utilizando un puerto de tronco, o si necesita configurar un ID, tendrá que proporcionar el identificador de VLAN como un parámetro de implementación. Puede ver ejemplos de la [lista de los parámetros de implementación](azure-stack-run-powershell-script.md).

### <a name="subnet"></a>Subred

No conecte el equipo de prueba de concepto a las siguientes subredes:
- 192.168.200.0/24
- 192.168.100.0/27
- 192.168.101.0/26
- 192.168.102.0/24
- 192.168.103.0/25
- 192.168.104.0/25

Estas subredes están reservadas para las redes internas dentro del entorno de prueba de concepto de pila de Microsoft Azure.

### <a name="ipv4ipv6"></a>IPv4 o IPv6

Solo IPv4 es compatible. No puede crear redes IPv6.

### <a name="dhcp"></a>DHCP

Asegúrese de que hay un servidor DHCP en la red que se conecta la NIC. Si DHCP no está disponible, debe preparar una red IPv4 estática adicional además usado por host. Debe proporcionar esa dirección IP y la puerta de enlace como un parámetro de implementación. Puede ver ejemplos de la [lista de los parámetros de implementación](azure-stack-run-powershell-script.md).

### <a name="internet-access"></a>Acceso a Internet

Pila de Azure requiere acceso a Internet, ya sea directa o a través de un proxy transparente. Pila de Azure no admite la configuración de un proxy web para habilitar el acceso a Internet. La dirección IP de host y la dirección IP nueva o asignada a la BGPNAT01 MAS (DHCP IP estática) debe tener acceso a Internet. Puertos 80 y 443 se usan en los dominios graph.windows.net y login.windows.net.

### <a name="telemetry"></a>Telemetría

Para admitir el flujo de datos de telemetría, puerto 443 (HTTPS) debe estar abierto en su red. El extremo del cliente es https://vortex-win.data.microsoft.com.


## <a name="next-steps"></a>Pasos siguientes

[Descargar el paquete de implementación de prueba de concepto de pila de Azure](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Implementar la prueba de concepto de pila de Azure](azure-stack-run-powershell-script.md)
