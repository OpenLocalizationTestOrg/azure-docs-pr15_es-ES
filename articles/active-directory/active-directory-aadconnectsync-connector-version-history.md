<properties
   pageTitle="Historial de versiones de conector versión | Microsoft Azure"
   description="Este tema enumeran todas las versiones de los conectores para el Administrador de identidad de Forefront (FIM) y el Administrador de identidad de Microsoft (MIM)"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="billmath"/>

# <a name="connector-version-release-history"></a>Historial de versiones de versión de conector
Los conectores para el Administrador de identidad de Forefront (FIM) y el Administrador de identidad de Microsoft (MIM) se actualizan con frecuencia.

>[AZURE.NOTE]
En este tema solo está disponible en FIM y MIM. Estos conectores no se admiten en Azure AD Connect.

En este tema se enumeran todas las versiones de los conectores que se han publicado.

Vínculos relacionados:

- [Descargar la última conectores](http://go.microsoft.com/fwlink/?LinkId=717495)
- Documentación de referencia de [Conector LDAP genérico](active-directory-aadconnectsync-connector-genericldap.md)
- Documentación de referencia de [Conector de SQL genérico](active-directory-aadconnectsync-connector-genericsql.md)
- Documentación de referencia de [Conector de servicios Web](http://go.microsoft.com/fwlink/?LinkID=226245)
- Documentación de referencia de [Conector de PowerShell](active-directory-aadconnectsync-connector-powershell.md)
- Documentación de referencia de [Conector de Lotus Domino](active-directory-aadconnectsync-connector-domino.md)

## <a name="111170"></a>1.1.117.0
Publicado: marzo de 2016

**Nuevo conector**  
Versión inicial del [Conector de SQL genérico](active-directory-aadconnectsync-connector-genericsql.md).

**Nuevas características:**

- Conector LDAP genérico:
    - Ha agregado compatibilidad para la importación de delta con Isode.
- Conector de servicios Web:
    - Actualizar la actividad de csEntryChangeResult y setImportErrorCode para permitir que los errores de nivel de objeto que se devolverá al motor de sincronización.
    - Actualiza las plantillas de SAP6 y SAP6User para usar la nueva funcionalidad de error de nivel de objeto.
- Conector de Lotus Domino:
    - Para exportar, es necesario un certificador por libreta de direcciones. Ahora puede usar la misma contraseña para los certificadores todos para facilitar la administración.

**Problemas fijos:**

- Conector LDAP genérico:
    - IBM Tivoli DS, algunos atributos de referencia no se detectan correctamente.
    - Para abrir LDAP durante una importación delta, espacios en blanco al principio y al final de cadenas se truncan.
    - Para Novell y NetIQ, una exportación que mueve un objeto entre las unidades organizativas o contenedores y al mismo tiempo, cambiar el nombre del objeto error.
- Conector de servicios Web:
    - Si el servicio web tiene múltiples extremos para el mismo enlace, a continuación, el conector no correctamente descubrió estos extremos.
- Conector de Lotus Domino:
    - Exportación del atributo fullName a una base de datos de correo no funciona.
    - Una exportación que agrega y quita el miembro de un grupo exporta solo los miembros agregados.
    - Si un documento de notas es válido (la isValid atributo establecida en falso), entonces la falla de conector.

## <a name="older-releases"></a>Versiones anteriores
Antes de marzo de 2016, los conectores se han publicado como temas de soporte técnico.

**LDAP genérico**

- [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597, septiembre de 2015
- [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549, marzo de 2015
- [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534, enero de 2015
- [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419, septiembre de 2014
- [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082, marzo de 2014

**Servicios Web**

- [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419, septiembre de 2014

**PowerShell**

- [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419, septiembre de 2014

**Lotus Domino**

- [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597, septiembre de 2015
- [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549, marzo de 2015
- [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712, agosto de 2014
- [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003, febrero de 2014  
- [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721, octubre de 2013
- [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534, agosto de 2013

## <a name="next-steps"></a>Pasos siguientes
Más información sobre la configuración de [sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
