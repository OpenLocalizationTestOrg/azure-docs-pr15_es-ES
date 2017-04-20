<properties
    pageTitle="Administrar permisos a los recursos por usuario en pila de Azure (Administrador del servicio e inquilino) | Microsoft Azure"
    description="Como administrador del servicio o inquilino, obtenga información sobre cómo administrar permisos a los recursos por usuario."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="manage-user-permissions"></a>Administrar permisos de usuario

Un usuario en pila de Azure puede ser un lector, el propietario o el colaborador para cada instancia de una suscripción, el grupo de recursos o el servicio. Por ejemplo, el usuario A puede tiene permisos de lectura para 1 de suscripción, pero tiene permisos de propietario a 7 de máquina Virtual.

-   Lector: El usuario puede ver todo el contenido, pero no puede realizar los cambios.

-   Colaborador: Usuario puede administrar todos los elementos excepto el acceso a los recursos.

-   Propietario: Usuario puede administrar todo, incluido el acceso a los recursos.


## <a name="set-access-permissions-for-a-user"></a>Establecer permisos de acceso para un usuario

1.  Inicie sesión con una cuenta que tenga permisos de propietario para el recurso que desee administrar.

2.  En el módulo para el recurso, haga clic en el icono de **Access** ![](media/azure-stack-manage-permissions/image1.png).

3.  En el módulo de **usuarios** , haga clic en **Roles**.

4.  En el módulo de **Roles** , haga clic en **Agregar** para agregar los permisos para el usuario.

## <a name="next-steps"></a>Pasos siguientes

[Agregar a un inquilino de pila de Azure](azure-stack-add-new-user-aad.md)
