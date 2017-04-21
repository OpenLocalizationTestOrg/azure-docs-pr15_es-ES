<properties
   pageTitle="Administrar los registros DNS inversos de los servicios de Azure (clásico) con PowerShell | Microsoft Azure"
   description="Cómo administrar los registros DNS inversos o registros PTR para los servicios de Azure con PowerShell en el modelo de implementación clásico. "
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/28/2016"
   ms.author="smalone" />

# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-classic-using-azure-powershell"></a>Cómo administrar los registros DNS inversos de los servicios de Azure (clásico) con PowerShell de Azure

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Obtenga información sobre cómo llevar a [cabo estos pasos con el modelo de administrador de recursos](dns-reverse-dns-record-operations-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Validación de registros DNS inversas
Para asegurarse de que un tercero no puede crear los registros de DNS inverso asignación a sus dominios DNS, Azure solo permite la creación de un registro DNS inversa donde uno de los siguientes sea verdadera:

- La inversa el FQDN de DNS es el nombre del servicio de nube para el que se ha especificado o un nombre de servicio de nube dentro de la misma suscripción por ejemplo, DNS inversa es "contosoapp1.cloudapp.net.".
- Reenviar el FQDN de DNS inversa resuelve en el nombre o la IP del servicio de nube para que se ha especificado, o en cualquier nombre de servicio de nube o dentro de la misma suscripción por ejemplo, DNS inversa es "app1.contoso.com". que es un alias CName contosoapp1.cloudapp.net.

Comprobaciones de validación se realizan solo cuando se establece la propiedad DNS inversa de un servicio de nube o se modificó. No se realiza la validación de volver a periódicos.

## <a name="add-reverse-dns-to-existing-cloud-services"></a>Agregar DNS inversa a los servicios de nube existentes
Puede agregar un registro DNS inverso a un servicio de nube existente mediante el cmdlet "Conjunto AzureService":

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="create-a-cloud-service-with-reverse-dns"></a>Crear un servicio de nube con DNS inversa
Puede agregar un nuevo servicio de nube con la propiedad DNS inversa especificada mediante el cmdlet "Conjunto AzureService":

    PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="view-reverse-dns-for-existing-cloud-services"></a>DNS inversa de la vista de los servicios de nube existentes
Puede ver el valor configurado para un servicio de nube existente mediante el cmdlet "Get-AzureService":

    PS C:\> Get-AzureService "contosoapp1"

## <a name="remove-reverse-dns-from-existing-cloud-services"></a>Quitar DNS inversa de los servicios de nube existentes
Puede quitar una propiedad DNS inversa de un servicio de nube existente mediante el cmdlet "Set-AzureService". Para ello, establezca el valor de propiedad DNS inverso en blanco:

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[AZURE.INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[AZURE.INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]
