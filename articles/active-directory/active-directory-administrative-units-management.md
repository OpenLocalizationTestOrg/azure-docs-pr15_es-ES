<properties
   pageTitle="Administración de unidades administrativas de Azure Active Directory"
   description="Uso de unidades administrativas más granulares delegación de permisos en Azure Active Directory"
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

# <a name="administrative-units-management-in-azure-ad---public-preview"></a>Administración de unidades administrativas en Azure AD - Public Preview

Este artículo describen las unidades administrativas: un nuevo contenedor de Azure Active Directory de recursos que se pueden usar para delegar permisos administrativos sobre subconjuntos de usuarios y aplicar directivas a un subconjunto de los usuarios. En Azure Active Directory, unidades administrativas permiten a los administradores central al delegado permisos a los administradores regionales o para establecer la directiva en un nivel específico.

Esto es útil en organizaciones con divisiones independientes, por ejemplo, una gran Universidad se compone de muchas escuelas autónomas (escuela de empresa, escuela de ingeniería etc.) que son independientes entre sí. Estas divisiones tienen sus propios administradores de TI que controlan el acceso, administrar los usuarios y establecer directivas específicamente para su división. Los administradores central desea poder conceder estos división permisos de administradores sobre los usuarios de sus divisiones. Más concretamente, en este ejemplo, un administrador central puede, por ejemplo, crear una unidad administrativa para una escuela determinada (escuela de negocios) y rellénela con solo los usuarios de la escuela de negocios. A continuación, en otras palabras, un administrador central puede agregar la escuela personal de TI de empresa a un rol de departamento, conceder al personal de TI de permisos administrativos de empresa escuela sólo a través de la unidad administrativa de escuela de empresa.

> [AZURE.IMPORTANT]
> Puede crear y utilizar unidades administrativas solo si habilita Azure Active Directory Premium. Para obtener más información, consulte [Introducción a Azure AD Premium](active-directory-get-started-premium.md).

Desde el punto de vista del administrador central, una unidad administrativa es un objeto de directorio que se pueden crear y rellena con recursos. **En esta versión, estos recursos pueden ser solo los usuarios.** Una vez creado y rellenado, la unidad administrativa puede usarse como ámbito para restringir los permisos concedidos sólo a través de recursos contenidos en la unidad administrativa.

## <a name="managing-administrative-units"></a>Administración de unidades administrativas

En esta versión preliminar, puede crear y administrar unidades administrativas con los cmdlets del módulo Azure Active Directory para Windows PowerShell.

Para obtener más información sobre los requisitos de software e instalar el módulo Azure AD y para obtener información sobre los cmdlets de Azure AD módulo de administración de unidades administrativas, incluida la sintaxis, descripciones de parámetros y ejemplos, vea [Administrar Azure AD con Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).


## <a name="next-steps"></a>Pasos siguientes
[Ediciones de Azure Active Directory](active-directory-editions.md)
