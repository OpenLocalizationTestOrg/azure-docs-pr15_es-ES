<properties
    pageTitle="Azure AD y aplicaciones: asignar grupos a una aplicación | Microsoft Azure"
    description="Cómo implementar la asignación a un grupo de aplicaciones de Azure."
    services="active-directory"
    documentationCenter=""
    authors="IHenkel"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/03/2015"
    ms.author="inhenk"/>

# <a name="azure-ad-and-applications-assigning-groups-to-an-application"></a>Azure AD y aplicaciones: asignar grupos a una aplicación
Antes de poder asignar usuarios y grupos para una aplicación, debe requerir asignación de usuario. Para obtener información sobre cómo requerir asignación de usuario, vea el artículo de la [Asignación de usuario que requieren](active-directory-applications-guiding-developers-requiring-user-assignment.md) .

En este artículo se supone que ya ha creado grupos de active directory que se usa para esta aplicación.

## <a name="assigning-groups-to-an-application"></a>Asignación de grupos a una aplicación
1. Inicie sesión en el portal de Azure con una cuenta de administrador.
2. Haga clic en el elemento de **Todos los elementos** en el menú principal.
3. Elija el directorio que está usando para la aplicación.
4. Haga clic en la ficha **aplicaciones** .
5. Seleccione la aplicación de la lista de aplicaciones asociado a este directorio.
6. Haga clic en la pestaña **Usuarios y grupos** .
7. Filtrar la lista de grupos de active directory mediante la lista desplegable de **grupos** .
8. Seleccione el grupo.
9. Haga clic en **asignar**.
10. Haga clic en **Sí** cuando se le solicite.

## <a name="next-steps"></a>Pasos siguientes
[AZURE.INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
