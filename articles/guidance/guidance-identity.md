<properties
   pageTitle="Administración de identidades en Azure | Microsoft Azure"
   description="Explica y compara los métodos diferentes para administrar la identidad de sistemas híbrido que abarcan el límite en local y la nube con Azure."
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>
<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/26/2016"
   ms.author="telmosampaio"/>
   
# <a name="managing-identity-in-azure"></a>Administración de identidades en Azure

En los sistemas empresariales más basados en Windows, usará Active Directory (AD) para proporcionar servicios de administración de identidades para las aplicaciones. AD funciona bien en un entorno local, pero cuando se extiende la infraestructura de red en la nube tiene algunas decisiones importantes para hacer sobre cómo administrar la identidad. ¿Debe expandir sus dominios locales para incorporar máquinas virtuales en la nube? Debe crear nuevos dominios en la nube y si es así, ¿cómo? ¿Debe implementar su propio bosque en la nube o debe hacer usar de Azure Active Directory (AAD)?

En este artículo se describe algunas opciones comunes para los desafíos que supone este escenario de la reunión y, a continuación, le ayudan a que determinar qué solución mejor que satisfaga sus necesidades según sus necesidades.

## <a name="using-azure-active-directory"></a>Uso de Azure Active Directory

Puede usar AAD para crear un dominio de Active Directory en la nube y vincularlo a la implementación local dominio de Active Directory. AAD le permite configurar el inicio de sesión único (SSO) para los usuarios que ejecutan aplicaciones de acceso a través de la nube.

[! [0]][0]

AAD es una forma sencilla de implementar un dominio de seguridad en la nube. La utilizan muchas aplicaciones de Microsoft, como Microsoft Office 365. 

Ventajas del uso de AAD:

- No es necesario mantener una infraestructura de Active Directory en la nube. AAD es administrada por completo y se mantiene por Microsoft.

- AAD proporciona la misma información de identidad que se encuentra disponible en las instalaciones.

- Autenticación puede ocurrir en Azure, se reduce la necesidad de aplicaciones externas y los usuarios ponerse en contacto con el dominio local.

Puntos para tener en cuenta al usar AAD:

- Servicios de identidad están limitados a usuarios y grupos. No hay ninguna capacidad para autenticar cuentas de servicio y su PC.

- Configurar la conectividad con su dominio local para mantener el directorio AAD sincronizado. 

- Usted es responsable de la publicación de aplicaciones que pueden tener acceso los usuarios en la nube mediante AAD.

Para obtener información detallada, lea la [Implementación de Azure Active Directory][implementing-aad].

## <a name="using-active-directory-in-the-cloud-joined-to-an-on-premises-forest"></a>Uso de Active Directory en la nube unido a un bosque local

Puede hospedar servicios de directorio de AD (AD DS) local, pero en un escenario híbrido donde se encuentran los elementos de una aplicación en Azure puede ser más eficaz para replicar esta funcionalidad y el repositorio de AD a la nube. Este enfoque puede ayudar a reducir la latencia causada mediante el envío de autenticación y solicitudes de autorización local de la nube a AD DS ejecutando local. 

[! [1]][1]

Este enfoque requiere que cree su propio dominio en la nube y unirse al bosque local. Crear máquinas virtuales para hospedar los servicios de AD DS.

Ventajas de usar un dominio distinto en la nube:

- Proporciona la capacidad para autenticar el usuario, el servicio y el equipo cuentas locales y en la nube.

- Proporciona acceso a la misma información de identidad que se encuentra disponible en las instalaciones.

- No es necesario para administrar un bosque independiente de AD; el dominio en la nube que se puede pertenecer al bosque local.

- Puede aplicar la directiva de grupo definida por los objetos GPO local en el dominio en la nube.

Consideraciones sobre el uso de un dominio distinto en la nube:

- Requiere que crear y administrar sus propios servidores de AD DS y el dominio en la nube.

- Es posible que algunos latencia de sincronización entre los servidores de dominio en la nube y los servidores que ejecutan local.

Para obtener información sobre cómo configurar esta arquitectura, vea [Ampliación de Active Directory directorio servicios (agrega) a Azure][extending-adds].

## <a name="using-active-directory-with-a-separate-forest"></a>Uso de Active Directory con un bosque independiente

Una organización que se ejecute en local de Active Directory (AD) puede tener un bosque que incluya varios dominios. Puede usar dominios para proporcionar aislamiento entre las áreas funcionales que se deben mantener independientes, posiblemente por motivos de seguridad, pero puede compartir información entre dominios mediante el establecimiento de relaciones de confianza.

[! [2]][2]

Una organización que utiliza dominios independientes puede sacar partido de Azure cambiando uno o varios de estos dominios en un bosque independiente en la nube. Como alternativa, una organización que desee conservar todos los recursos de nube lógicamente distintas de esos local mantenidos y almacenar información sobre los recursos de la nube en su propio directorio, como parte de un bosque también se almacenan en la nube.

Ventajas de usar un bosque independiente en la nube:

- Puede implementar identidades locales y separar las identidades solo Azure.

- No es necesario para replicar la locales bosque de AD a Azure, reducción de los efectos de latencia de red.

Consideraciones:

- Autenticación de identidades local en la nube realiza de red adicional *saltos* a local en servidores de AD.

- Requiere que implementar sus propios servidores de AD DS y bosques en la nube y a continuación, establezca las relaciones de confianza apropiadas entre bosques.

El documento [creando un bosque de recursos de servicios de directorio de Active Directory (agrega) en Azure] [ adds-forest-in-azure] describe cómo implementar este enfoque con más detalle.

## <a name="using-active-directory-federation-services-adfs-with-azure"></a>Uso de federación de Active Directory (ADFS) de servicios con Azure

ADFS puede ejecutar local, pero en un escenario híbrido donde las aplicaciones se encuentran en Azure puede ser más eficaz para implementar esta funcionalidad en la nube, como se muestra a continuación.

[! [3]][3]

Esta arquitectura es especialmente útil para:

- Soluciones que utilizan federada autorización para exponer aplicaciones web para las organizaciones asociadas.

- Sistemas que admiten el acceso de exploradores web que se ejecutan fuera de la organización.

- Sistemas que permiten a los usuarios acceso a aplicaciones web mediante una conexión desde dispositivos externos autorizados como equipos remotos, los blocs de notas y otros dispositivos móviles. 

Ventajas de usar ADFS con Azure:

- Puede aprovechar las aplicaciones para notificaciones.

- Proporciona la capacidad de confiar en sus socios externos para la autenticación.

- Proporciona compatibilidad con amplio conjunto de protocolos de autenticación.

Consideraciones sobre el uso de ADFS con Azure:

- Requiere implementar sus propios servidores agrega, ADFS y Proxy de aplicación Web de ADFS en la nube.

- Esta arquitectura puede ser difícil de configurar.

Para obtener información detallada, lea la [Implementación de Active Directory Federation Services (ADFS) en Azure][adfs-in-azure].

## <a name="next-steps"></a>Pasos siguientes

Los siguientes recursos explican cómo implementar las arquitecturas descritas en este artículo.

- [Implementación de Azure Active Directory][implementing-aad]
- [Extensión de servicios de directorio de Active Directory (suma) en Azure][extending-adds]
- [Crear un bosque de recursos de servicios de directorio de Active Directory (suma) en Azure][adds-forest-in-azure]
- [Implementación de servicios de federación de Active Directory (ADFS) en Azure][adfs-in-azure]

<!-- Links -->
[0]: ./media/guidance-identity/figure1.png "Arquitectura de identidad de nube con Azure Active Directory"
[1]: ./media/guidance-identity/figure2.png "Proteger la arquitectura de red híbrida con Active Directory"
[2]: ./media/guidance-identity/figure3.png "Arquitectura de red híbrido con bosques y dominios de Active Directory independiente segura"
[3]: ./media/guidance-identity/figure4.png "Arquitectura de red híbrido con ADFS segura"
[implementing-aad]: ./guidance-identity-aad.md
[extending-adds]: ./guidance-identity-adds-extend-domain.md
[adds-forest-in-azure]: ./guidance-identity-adds-resource-forest.md
[adfs-in-azure]: ./guidance-identity-adfs.md