<properties
   pageTitle="Azure AD Connect: Características en la vista previa | Microsoft Azure"
   description="Este tema se describe en más funciones de detalle que se encuentran en vista previa de Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/27/2016"
   ms.author="billmath"/>

# <a name="more-details-about-features-in-preview"></a>Más información sobre las características de vista previa
Este tema describe cómo usar las características actualmente en la vista previa.

## <a name="group-writeback"></a>Reescritura de grupo
La opción de reescritura de grupo en características opcionales le permitirá reescritura **Grupos de Office 365** a un bosque con Exchange instalado. Se trata de un grupo que siempre se controlan en la nube. Si tiene Exchange local, a continuación, se pueden escribir estos grupos en local para los usuarios con un buzón de Exchange local pueden enviar y recibir correos electrónicos de los grupos.

Encontrará más información sobre grupos de Office 365 y cómo usarlas [aquí](http://aka.ms/O365g).

Este grupo se representarán como un grupo de distribución en local AD DS. El servidor de Exchange local debe estar en la actualización acumulativa de Exchange 2013 8 (publicado en marzo de 2015) o Exchange 2016 reconocer este nuevo tipo de grupo.

**Notas durante la vista previa**

- El atributo de libreta de direcciones, no se rellena actualmente en la vista previa. Sin este atributo, el grupo no estarán visible en la lista global de direcciones. La manera más sencilla de rellenar este atributo es usar el cmdlet de PowerShell de Exchange `update-recipient`.
- Solo los bosques con el esquema de Exchange son destinos válidos para los grupos. Si se ha detectado ninguna cambio, grupo reescritura no será posible habilitar.
- Actualmente se admiten implementaciones de organización de Exchange a solo bosque único. Si tiene más de una organización de Exchange en local, deberá una solución local de GALSync para estos grupos que aparezcan en los otros bosques.
- La característica de reescritura de grupo no controla actualmente grupos de seguridad o grupos de distribución.

>[AZURE.NOTE] Una suscripción a Azure AD Premium se requiere para reescritura de grupo.

## <a name="user-writeback"></a>Reescritura de usuario
> [AZURE.IMPORTANT] Se ha quitado la característica de vista previa de reescritura de usuario en la actualización de agosto del de 2015 Azure AD Connect. Si se ha habilitado, debe deshabilitar esta característica.

## <a name="next-steps"></a>Pasos siguientes
Continuar con la [instalación personalizada de Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md).

Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
