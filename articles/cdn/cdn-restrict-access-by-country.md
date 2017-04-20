<properties
    pageTitle="Restringir el acceso a su contenido de Azure CDN por país | Microsoft Azure"
    description="Obtenga información sobre cómo restringir el acceso a su contenido de Azure CDN con la característica de filtrado Geo."
    services="cdn"
    documentationCenter=""
    authors="camsoper, rli"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="casoper"/>

#<a name="restrict-access-to-your-content-by-country---verizon"></a>Restringir el acceso a su contenido por país - Verizon

> [AZURE.SELECTOR]
- [Verizon](cdn-restrict-access-by-country.md)
- [Akamai estándar](cdn-restrict-access-by-country-akamai.md)

##<a name="overview"></a>Información general

Cuando un usuario solicita el contenido de forma predeterminada, el contenido se sirve con independencia de que el usuario realizado esta solicitud de. En algunos casos, desea restringir el acceso a su contenido por país. En este tema se explica cómo usar la característica de **Filtrado Geo** para configurar el servicio para permitir o bloquear el acceso por país.

> [AZURE.IMPORTANT] Los productos de Verizon y Akamai proporcionan la misma funcionalidad de filtrado geo, pero difiere de la interfaz de usuario. Este documento describe la interfaz de **Azure CDN estándar de Verizon** y **Azure CDN Premium de Verizon**. Para filtrar geo con **Azure CDN estándar de Akamai**, vea [restringir el acceso a su contenido por país - Akamai](cdn-restrict-access-by-country-akamai.md).

Para obtener información sobre consideraciones que se aplican a la configuración de este tipo de restricción, vea la sección [Consideraciones](cdn-restrict-access-by-country.md#considerations) al final del tema.  

>[AZURE.NOTE] Una vez configurada, la configuración se aplicará a todos los extremos de **Azure CDN de Verizon** en este perfil CDN de Azure.

![Filtrado de país](./media/cdn-filtering/cdn-country-filtering.png)

##<a name="step-1-define-the-directory-path"></a>Paso 1: Definir la ruta del directorio

Al configurar un filtro de país, debe especificar la ruta de acceso relativa a la ubicación a la que los usuarios se les permitirá o acceso denegados. Puede aplicar el filtrado de país para todos los archivos con "/" o las carpetas seleccionadas especificando las rutas de directorio.

Filtro de ruta de acceso del directorio de ejemplo:

    /                                 
    /Photos/
    /Photos/Strasbourg

##<a name="step-2-define-the-action-block-or-allow"></a>Paso 2: Definir la acción: bloquear o permitir

**Bloque:** Los usuarios de los países especificados se se denegado acceso a activos solicitados desde esa ruta recursiva. Si no hay otras opciones filtrado de país se han configurado para esta ubicación, todos los demás usuarios se le permitirá acceso.

**Permitir:** Solo los usuarios de los países especificados tendrán acceso a los activos solicitado desde esa ruta recursiva.

##<a name="step-3-define-the-countries"></a>Paso 3: Definir los países

Seleccione los países que desee bloquear o permitir para la ruta de acceso. Para obtener más información, vea [Azure CDN de Verizon códigos de país](https://msdn.microsoft.com/library/mt761717.aspx).

Por ejemplo, la regla de bloqueo /Photos/Estrasburgo/filtrará archivos incluidos:

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


##<a name="country-codes"></a>Códigos de país

La característica de **Filtrado Geo** utiliza códigos de país para definir los países desde la que una solicitud se permitidos o bloqueada de un directorio protegido. Encontrará los códigos de país en [Azure CDN de Verizon códigos de país](https://msdn.microsoft.com/library/mt761717.aspx). Si se especifica "UE" (Europa) o "PA" (Asia Pacífico), un subconjunto de direcciones IP que proceden de cualquier país en que se bloqueados o permitidas regiones.


##<a id="considerations"></a>Consideraciones

- Puede tardar hasta 90 minutos para cambios a la configuración filtrado de país surta efecto.
- Esta característica no admite caracteres comodín (por ejemplo, ' *').
- El país filtrado configuración asociada a la ruta de acceso relativa será aplica de forma recursiva a esa ruta.
- Solo regla se puede aplicar a la misma ruta de acceso relativa (no se puede crear varios filtros de país que apuntan a la misma ruta de acceso relativa. Sin embargo, una carpeta puede tener varios filtros de país. Esto es debido a la naturaleza recursiva de filtros de país. En otras palabras, se puede asignar un filtro de país diferente a una subcarpeta de una carpeta configurada previamente.
