<properties
   pageTitle="Administrar los registros DNS inversos de los servicios de Azure mediante CLI de Azure | Microsoft Azure"
   description="Cómo administrar los registros DNS inversos o registros PTR para los servicios de Azure mediante la CLI de Azure en el Administrador de recursos"
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/28/2016"
   ms.author="smalone" />

# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-using-the-azure-cli"></a>Cómo administrar los registros DNS inversos para sus servicios de Azure mediante la CLI de Azure

[AZURE.INCLUDE [DNS-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implementación clásico](dns-reverse-dns-record-operations-classic-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Validación de registros DNS inversas
Para asegurarse de que un tercero no puede crear los registros de DNS inverso asignación a sus dominios DNS, Azure solo permite la creación de un registro DNS inversa donde uno de los siguientes sea verdadera:

- "ReverseFqdn" es el mismo que el "Fqdn" para el recurso de dirección IP pública para la que se ha especificado, o "Fqdn" para cualquier dirección IP pública dentro de la misma suscripción por ejemplo, "ReverseFqdn" es "contosoapp1.northus.cloudapp.azure.com.".

- "ReverseFqdn" Reenviar resuelve en el nombre o la dirección IP a la dirección IP pública para que se ha especificado, o a cualquier dirección IP pública "Fqdn" o IP dentro de la misma suscripción por ejemplo, "ReverseFqdn" es "app1.contoso.com". que es un alias CName "contosoapp1.northus.cloudapp.azure.com."

Comprobaciones de validación se realizan solo cuando se establece la propiedad DNS inversa de una dirección IP pública o se modificó. No se realiza la validación de volver a periódico.

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>Agregar DNS inversa a direcciones IP públicas existentes
Puede agregar DNS inversa a una dirección IP pública existente con el conjunto de red azure ip pública:

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -f contosoapp1.westus.cloudapp.azure.com.

Si desea agregar DNS inversa a una dirección IP pública existente que ya no tiene un nombre DNS, también debe especificar un nombre DNS. Puede agregar conseguirlo con el conjunto de red azure ip pública:

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.

## <a name="create-a-public-ip-address-with-reverse-dns"></a>Crear una dirección IP pública con DNS inversa
Puede agregar una nueva dirección IP pública con la propiedad DNS inversa especificada mediante la red de azure público ip crear:

    azure network public-ip create -n PublicIp3 -g NRP-DemoRG-PS -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>DNS inversa de la vista para las direcciones IP públicas existente
Puede ver el valor configurado para una dirección IP pública existente con la presentación de azure red ip pública:

    azure network public-ip show -n PublicIp3 -g NRP-DemoRG-PS

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Quitar DNS inversa de las direcciones IP públicas existente
Puede quitar una propiedad DNS inversa de una dirección IP pública existente con conjunto de azure red ip pública. Para ello, establezca el valor de propiedad de ReverseFqdn en blanco:

    azure network public-ip set -n PublicIp3 -g NRP-DemoRG-PS –f “”

[AZURE.INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[AZURE.INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]
