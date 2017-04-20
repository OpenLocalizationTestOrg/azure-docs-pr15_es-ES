<properties
    pageTitle="Azure AD Connect: Instancias del servicio de sincronización | Microsoft Azure"
    description="Consideraciones especiales para instancias de Azure AD de documentos de esta página."
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

# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: Consideraciones especiales para instancias
Azure AD Connect suele utilizarse con la instancia de todo el mundo de Azure AD y Office 365. Pero también existen otras instancias y tienen requisitos distintos para las direcciones URL y otras consideraciones especiales.

## <a name="microsoft-cloud-germany"></a>Alemania de nube de Microsoft
La [nube de Microsoft Alemania](http://www.microsoft.de/cloud-deutschland) es una nube soberano a través de un administrador de confianza de datos alemán.

Esta nube está en vista previa. Muchos de los escenarios que normalmente puede hacer por usted mismo, como comprobar dominios, debe hacerse por el operador. Póngase en contacto con su representante de Microsoft para obtener más información acerca de cómo participar en la vista previa.

Direcciones URL para abrir en el servidor proxy |
--- |
\*. microsoftonline.de |
\*. windows.net |
+ Listas de revocación de certificados |

Cuando inicie sesión su directorio de Azure AD debe utilizar una cuenta en el dominio onmicrosoft.de.

Características actualmente no están presentes en la Alemania Microsoft Cloud:

- Estado de conexión de Azure AD no está disponible.
- Actualizaciones automáticas no está disponible.
- Reescritura de contraseña no está disponible.

## <a name="microsoft-azure-government-cloud"></a>Nube de Microsoft Azure gobierno
La [nube de Microsoft Azure gobierno](https://azure.microsoft.com/features/gov/) es una nube para EE.

Se admite esta nube por versiones anteriores de sincronización de directorios. Desde la compilación 1.1.180 de Azure AD Connect la próxima generación de la nube es compatible. Esta generación utiliza solo US extremos en función y tiene una lista diferente de direcciones URL para abrirlo en su servidor proxy.

Direcciones URL para abrir en el servidor proxy |
--- |
\*. microsoftonline.com |
\*. gov.us.microsoftonline.com |
+ Listas de revocación de certificados |

Azure AD Connect no podrá detectar automáticamente que el directorio de Azure AD se encuentra en la nube de gobierno. En su lugar debe realizar las siguientes acciones cuando se instala Azure AD Connect.

1. Iniciar la instalación de Azure AD Connect.
2. Tan pronto como se ve la primera página donde se supone que acepte al CLUF, no continúe pero dejar a ejecutando el Asistente para la instalación.
3. Inicie regedit y cambiar la clave del registro `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` al valor `2`.
4. Vaya al Asistente para instalación de Azure AD Connect, acepte al CLUF y continuar. Durante la instalación, asegúrese de usar la ruta de instalación de **configuración personalizada** (y no Express instalación). Continúe con la instalación como de costumbre.

Características actualmente no están presentes en la nube de Microsoft Azure gobierno:

- Estado de conexión de Azure AD no está disponible.
- Actualizaciones automáticas no está disponible.
- Reescritura de contraseña no está disponible.

## <a name="next-steps"></a>Pasos siguientes
Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
