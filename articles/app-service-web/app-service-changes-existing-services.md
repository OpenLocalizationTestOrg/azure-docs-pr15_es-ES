<properties
    pageTitle="Azure de aplicación de servicio y su impacto en los servicios de Azure existentes"
    description="Explica cómo el nuevo servicio de aplicación de Azure y sus características afectan los servicios existentes en Azure."
    services="app-service"
    documentationCenter=""
    authors="yochay"
    manager="nirma"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/12/2016"
    ms.author="yochayk"/>


# <a name="azure-app-service-and-existing-azure-services"></a>Azure servicio de aplicaciones y servicios de Azure existentes

Este artículo describen los cambios a los servicios de Azure existentes como parte del cambio para reunir varios servicios de Azure en [Servicio de la aplicación de Azure](https://azure.microsoft.com/services/app-service/), una nueva oferta integrada.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="overview"></a>Información general

[Servicio de aplicación de Azure](https://azure.microsoft.com/services/app-service/) es un servicio de nube nuevo y único que permite a los desarrolladores crear web y aplicaciones móviles en cualquier plataforma y en cualquier dispositivo. Aplicación de servicio es una solución integrada diseñada para simplificar las funciones de codificación repetidas, integrar con sistemas de SaaS y enterprise y automatizar procesos empresariales mientras satisface sus necesidades de seguridad, confiabilidad y escalabilidad.

Aplicación de servicio reúne los siguientes existentes Azure servicios - [sitios Web](https://azure.microsoft.com/services/websites/), [Servicios para dispositivos móviles](https://azure.microsoft.com/services/mobile-services/)y [Servicios de Biztalk](https://azure.microsoft.com/services/biztalk-services/) en un servicio combinado único, al agregar nuevas capacidades.  Aplicación de servicio le permite alojar los siguientes tipos de aplicación:

-   Aplicaciones Web
-   Aplicaciones móviles
-   Aplicaciones de la API
-   Aplicaciones de lógica

En la tabla siguiente se explica cómo existente mapa de Azure servicios a la aplicación de servicio y los tipos de aplicación disponibles dentro de ella.

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">Servicio de Azure existente</th>
<th align="left", style="width:10%">Servicio de aplicaciones de Azure</th>
<th align="left", style="width:80%">¿Qué ha cambiado</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Sitios Web de Azure</td>
<td align="left">Aplicaciones Web</td>
<td align="left"><li>Para sitios Web de Azure, servicio de aplicación es estrictamente limitado para cambiar el nombre de sitios Web para las aplicaciones Web.
<p><li>Todas las instancias existentes de los sitios Web ahora son aplicaciones Web en la aplicación de servicio.</p>
<p><li>Puede obtener acceso a los sitios Web existentes a través del <a href="http://go.microsoft.com/fwlink/?LinkId=529715">Portal de Azure</a>, donde encontrará los sitios existentes en <em>Aplicaciones Web</em>.</p>
<p><li><em>Web hospedaje planear</em> está ahora <em>Plan de servicio de aplicación</em>. Un <em>Plan de servicio de aplicación</em> , puede hospedar cualquier tipo de aplicación de servicio de la aplicación, como las aplicaciones Web, móvil, lógica o API.</p>
<p><li>Azure aplicación de servicio Web Apps en General es disponibilidad.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">Más información sobre las aplicaciones Web</a>.</p></td>
</tr>
<tr class="even">
<td align="left">Servicios de móvil de Azure</td>
<td align="left">Aplicaciones móviles</td>
<td align="left"><p><li>Servicios móviles seguir estarán disponibles como servicio independiente y permanecen totalmente compatibles.</p>
<p><li>Aplicaciones móviles es un tipo de aplicación de servicio de aplicación, que integra toda la funcionalidad de servicios móviles y mucho más.</p>
<p><li>Es fácil <a href="http://go.microsoft.com/fwlink/?LinkID=724279&clcid=0x409">migrar de servicios móviles aplicaciones móviles</a>.</p>
<p><li>Como parte de la aplicación de servicio, aplicaciones Mobile obtener nuevas capacidades más allá de servicios móviles, como la integración con sistemas de SaaS y local de ensayo ranuras, WebJobs, opciones de escala mejor y más.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">Obtenga más información sobre aplicaciones de Mobile</a>.</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Aplicaciones de la API</td>
<td align="left">
<p><li>Aplicaciones de la API es un nuevo tipo de aplicación de servicio de la aplicación que le permite crear fácilmente y consumir API en la nube.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">Más información sobre las aplicaciones de la API</a>.</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Aplicaciones de lógica</td>
<td align="left">
<p><li>Lógica de aplicaciones es un nuevo tipo de aplicación de servicio de la aplicación que le permite automatizar fácilmente los procesos empresariales.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">Más información sobre aplicaciones de lógica</a>.</p></td>
</tr>
<tr class="odd">
<td align="left">Servicios de Azure BizTalk</td>
<td align="left">Aplicaciones de la API de BizTalk</td>
<td align="left">
<li><p>Servicios de BizTalk siguen estarán disponibles como servicio independiente y permanecen totalmente compatibles.</p>
<li><p>Todas las capacidades de servicios de BizTalk se integren en la aplicación de servicio como aplicaciones API permitiendo a los usuarios realizar integraciones y escenarios de integración B2B con cualquiera de los tipos de aplicación de servicio de aplicaciones</p>
<li><p>Con la lógica de aplicaciones, ahora puede automatizar procesos empresariales con una experiencia de diseño visual para crear flujos de trabajo</p></td>
</tr>
</tbody>
</table>

Para obtener más información, visite la [documentación del servicio de aplicación](https://azure.microsoft.com/documentation/services/app-service/).
