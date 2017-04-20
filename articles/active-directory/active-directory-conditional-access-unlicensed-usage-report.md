<properties
    pageTitle="Informe de uso sin licencia | Microsoft Azure"
    description="La Ayuda de informe de uso sin licencia que identificar los usuarios sin licencia que están utilizando pagado características de Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="unlicensed-usage-report"></a>Informe de uso sin licencia

La Ayuda de informe de uso sin licencia que identificar los usuarios sin licencia que están utilizando pagado características de Azure AD. Esto permite hacer mejor uso de licencias compradas y para identificar sabe que puede necesitar licencias adicionales. 

El informe muestra active uso de las características de pagadas en los últimos 30 días. 

## <a name="report-structure"></a>Estructura de informe
 
| Nombre de columna          |    Descripción |
| :--                  | :--         |
| Usuarios sin licencia      |    Nombre del usuario |
| Característica              | El nombre de la función. Por ejemplo: acceso condicional |
| Tener acceso a aplicación | El nombre de la aplicación que se tiene acceso con la característica. Por ejemplo: Office 365 SharePoint Online |

 
> [AZURE.NOTE] Si se ha eliminado una cuenta de usuario de la columna 'Usuarios sin licencia' se rellenará con un Id., como 1003000090D8B285


## <a name="conditional-access-feature"></a>Característica de acceso condicional

Los usuarios sin licencia se marcarán cuando tengan acceso a un servicio que tiene la directiva de acceso condicional aplicada si no tiene una licencia de Azure AD Premium. 

Esto se aplica a AMF / directivas de ubicación, así como dispositivo directivas que usar Intune.
 

## <a name="see-also"></a>Vea también

- [Usar Access condicional con Office 365 y otros Azure Active Directory conectado aplicaciones](active-directory-conditional-access.md)
- [Introducción a access condicional a Azure AD](active-directory-conditional-access-azuread-connected-apps.md) 


