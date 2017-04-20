<properties
   pageTitle="Agregar y administrar varios directorios de Azure Active Directory | Microsoft Azure"
   description="Instrucciones y los procedimientos recomendados para agregar y administrar los directorios de Azure Active Directory, explicar directorios como recursos de totalmente independiente"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="add-and-manage-multiple-azure-active-directory-directories"></a>Agregar y administrar varios directorios de Azure Active Directory

En Azure Active Directory (AD Azure), cada directorio sea un recurso totalmente independiente: un punto, completas y lógicamente independiente de otros directorios que administre. No hay ninguna relación de elementos primarios y secundarios entre directorios. Esta independencia entre directorios incluye independencia de recursos, independencia administrativa e independencia de sincronización.

##<a name="resource-independence"></a>Independencia de recursos

Si crear o eliminar un recurso en un directorio, tiene ningún efecto en los recursos en otro directorio, con la excepción parcial de usuarios externos, que se describen a continuación. Si usa un dominio personalizado 'contoso.com' con un directorio, no pueden usarse con cualquier otro directorio.

##<a name="administrative-independence"></a>Independencia administrativa

Si un usuario no administrativo del directorio 'Contoso' crea un directorio de prueba 'Prueba' después:
- De forma predeterminada, el usuario que crea un directorio había agregado como un usuario externo en el nuevo directorio y asigna el rol de administrador global en el directorio.
- Los administradores del directorio 'Contoso' no tienen privilegios directas al directorio 'Prueba' a menos que un administrador de 'Prueba' específicamente les concede estos privilegios. Los administradores de 'Contoso' pueden controlar el acceso a directorios 'Prueba' Si control de la cuenta de usuario que creó 'Prueba'.
- Si cambia (Agregar o quitar) un rol de administrador de un usuario en un directorio, el cambio no afecta a cualquier otro rol de administrador que puede tener el usuario en otro directorio.

##<a name="synchronization-independence"></a>Independencia de sincronización

Puede configurar cada directorio de Azure AD independientemente para obtener los datos que se sincronizan desde una única instancia de uno de ellos:
  - La herramienta de sincronización de directorios (DirSync), para sincronizar los datos con un único bosque de AD.
  - Azure Active Directory Connector para el Administrador de Forefront identidades, para sincronizar los datos con uno o más bosques local u orígenes de datos no Azure AD.

##<a name="add-an-azure-ad-directory"></a>Agregar un directorio de Azure AD

Para agregar un directorio de Azure AD en el portal de clásico Azure, seleccione la extensión de Azure Active Directory de la izquierda y puntee en **Agregar**.

> [AZURE.NOTE]   A diferencia de otros recursos de Azure, los directorios no son recursos secundarios de una suscripción de Azure. Si se cancela o permitir que su suscripción de Azure expire, aún puede acceder a los datos de directorio con PowerShell de Azure, la API de gráfico de Azure u otras interfaces como el centro de administración de Office 365. También puede asociar otra suscripción con el directorio.

Para obtener una introducción amplia de problemas de licencias de Azure AD y los procedimientos recomendados, consulte [¿Qué es licencias de Azure Active Directory?](active-directory-licensing-what-is.md).
