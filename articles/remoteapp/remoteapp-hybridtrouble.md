
<properties
    pageTitle="Solucionar problemas de crear colecciones de híbrido RemoteApp | Microsoft Azure"
    description="Obtenga información sobre cómo solucionar errores de la creación de colección de RemoteApp híbrido"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="troubleshoot-creating-azure-remoteapp-hybrid-collections"></a>Solucionar problemas de crear colecciones de híbrido de RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Una colección de híbrido se hospeda en y almacena datos en la nube de Azure, pero también permite los usuarios acceso a datos y recursos almacenados en su red local. Los usuarios pueden acceder a aplicaciones iniciando sesión con sus credenciales corporativas sincronizados o federado con Azure Active Directory. Puede implementar una colección de híbrido que utiliza una red Virtual de Azure existente, o puede crear una nueva red virtual. Le recomendamos que cree o use una subred de una red virtual con un intervalo CIDR lo suficientemente grande para el crecimiento futuro esperado para RemoteApp de Azure.

¿No ha creado la colección aún? Consulte [crear una colección de híbrido](remoteapp-create-hybrid-deployment.md) para ver los pasos.

Si tienes problemas al crear la colección, o si la colección no funciona del modo esperado, consulte la siguiente información.

## <a name="your-image-is-invalid"></a>La imagen no es válida ##
Si ve un mensaje como "GoldImageInvalid" cuando está esperando Azure proporcionando su colección, significa que la imagen de la plantilla no cumple el [define los requisitos de la imagen](remoteapp-imagereqs.md). Por lo tanto, vaya leer dichos [requisitos](remoteapp-imagereqs.md), arregle la imagen y a continuación, trata de crear la colección de nuevo.



## <a name="does-your-vnet-have-network-security-groups-defined"></a>¿Su VNET tiene grupos de seguridad de red definidos? ##
Si ha definido en la subred que usa para la colección de grupos de seguridad de red, asegúrese de que estas [direcciones URL y puertos](remoteapp-ports.md) son accesibles desde dentro de la subred.

Puede agregar grupos de seguridad de redes adicionales a las VM implementadas por el usuario en la subred para un mayor control.

## <a name="are-you-using-your-own-dns-servers-and-are-they-accessible-from-your-vnet-subnet"></a>¿Está usando sus propios servidores DNS? ¿Y son accesibles desde la subred VNET? ##
>[AZURE.NOTE] Tendrá que asegúrese de que los servidores DNS de su VNET son siempre hacia arriba y siempre puede resolver los equipos virtuales alojados en el VNET. No utilizar Google DNS.


Para las colecciones de híbrido use sus propios servidores DNS. Especificarlos en el esquema de configuración de red o a través del portal de administración cuando se crea la red virtual. Los servidores DNS se utilizan en el orden en que se especifican en forma de migración tras error (en lugar de turnos).  
Consulte [Resolución de nombres para máquinas virtuales y las instancias de función](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) para asegurarse de que los servidores DNS están configurado correcly.

Asegúrese de que los servidores DNS de la colección sean accesibles y disponible a partir de la subred VNET especificada para esta colección.

Por ejemplo:

    <VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="" IPAddress=""/>
      </DnsServers>
    </Dns>
    </VirtualNetworkConfiguration>

![Definir su DNS](./media/remoteapp-hybridtrouble/dnsvpn.png)

## <a name="are-you-using-an-active-directory-domain-controller-in-your-collection"></a>¿Está utilizando un controlador de dominio de Active Directory en la colección? ##
Actualmente solo un dominio de Active Directory se puede asociar con RemoteApp de Azure. La colección de híbrido admite únicamente las cuentas de Azure Active Directory que se han sincronizado con la herramienta de sincronización de directorios de una implementación de Active Directory de Windows Server; más concretamente, ya sea sincronizado con la opción de sincronización de contraseñas o sincronizado con la federación de servicios de federación de Active Directory (AD FS) configurada. Debe crear un dominio personalizado que coincida con el sufijo UPN del dominio de su dominio local y configurar la integración de directorios.

Para obtener más información, vea [Configurar Active Directory para RemoteApp de Azure](remoteapp-ad.md) .

Asegúrese de que los detalles del dominio proporcionados son válidos y el controlador de dominio es accesible desde la máquina virtual que creó en la subred que se utiliza para la aplicación remota de Azure. También, asegúrese de que las credenciales de cuenta de servicio suministrado tienen permisos para agregar equipos al dominio proporcionado y que el nombre de AD proporcionado puede resolver desde el DNS proporcionados en la VNET.

## <a name="what-domain-name-did-you-specify-when-you-created-your-collection"></a>¿Qué nombre de dominio especificó cuando creó la colección? ##

El nombre de dominio se crea o agrega debe ser un nombre de dominio interno (no su nombre de dominio de Azure AD) y debe estar en formato DNS puede resolver (contoso.local). Por ejemplo, tiene un nombre interno de Active Directory (contoso.local) y un UPN de directorio activo (contoso.com), debe usar el nombre interno cuando se crea la colección.
