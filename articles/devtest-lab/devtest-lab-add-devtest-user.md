<properties
    pageTitle="Agregar usuarios y los propietarios de Azure DevTest prácticas | Microsoft Azure"
    description="Agregar usuarios y los propietarios de Azure DevTest prácticas mediante el portal de Azure o PowerShell"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="tarcher"/>

# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Agregar usuarios y los propietarios de Azure DevTest prácticas

> [AZURE.VIDEO how-to-set-security-in-your-devtest-lab]

Acceso en Azure DevTest prácticas está controlado por el [Control de acceso de Azure Role-Based (RBAC)](../active-directory/role-based-access-control-what-is.md). Using RBAC, puede separarlo obligaciones dentro de su equipo en *funciones* que se le concede sólo la cantidad de acceso necesario para los usuarios para realizar su trabajo. Tres de estas funciones RBAC son *propietario*, el *Usuario de prácticas de DevTest*y *Colaborador*. En este artículo, aprenderá las acciones que se pueden realizar en cada una de las tres funciones RBAC principales. Desde allí, obtenga información sobre cómo agregar usuarios a un laboratorio - a través del portal y a través de un script de PowerShell y cómo agregar usuarios en el nivel de suscripción.

## <a name="actions-that-can-be-performed-in-each-role"></a>Acciones que pueden realizar en cada rol

Hay tres funciones principales que se puede asignar a un usuario:

- Propietario
- DevTest prácticas usuario
- Colaborador

La siguiente tabla muestra las acciones que se pueden realizar usuarios de cada una de estas funciones:

| **Pueden realizar acciones que los usuarios de esta función** | **DevTest prácticas usuario**            | **Propietario** | **Colaborador** |
|---|---|---|---|
| **Tareas de la práctica**                          |                              |       |             |
| Agregar usuarios a un laboratorio                     | No                           | Sí   | No          |
| Actualizar la configuración de costo                   | No                           | Sí   | Sí         |
| **Tareas de base de VM**                      |                              |       |             |
| Agregar y quitar imágenes personalizadas           | No                           | Sí   | Sí         |
| Agregar, actualizar y eliminar fórmulas       | Sí                          | Sí   | Sí         |
| Imágenes de la lista blanca Azure Marketplace     | No                           | Sí   | Sí         |
| **Tareas VM**                           |                              |       |             |
| Crear máquinas virtuales                             | Sí                          | Sí   | Sí         |
| Iniciar, detener y eliminar máquinas virtuales            | Solo máquinas virtuales creadas por el usuario | Sí   | Sí         |
| Actualizar las directivas VM                     | No                           | Sí   | Sí         |
| Agregar o quitar discos de datos de máquinas virtuales      | Solo máquinas virtuales creadas por el usuario | Sí   | Sí         |
| **Tareas de muestra**                     |                              |       |             |
| Agregar y quitar repositorios de muestra   | No                           | Sí   | Sí         |
| Aplicar artefactos                        | Sí                          | Sí   | Sí         |

> [AZURE.NOTE] Cuando un usuario crea una máquina virtual, ese usuario se asigna automáticamente a la función de **propietario** de la máquina virtual creada.

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Agregar un propietario o el usuario en el nivel de práctica

Los propietarios y los usuarios pueden agregarse en el nivel de práctica a través del portal de Azure. Esto incluye los usuarios externos con una [cuenta de Microsoft (MSA)](devtest-lab-faq.md#what-is-a-microsoft-account)de válida.
Los pasos siguientes guían a través del proceso de agregar un usuario o propietario a un laboratorio en Azure DevTest prácticas:

1. Inicie sesión en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **más servicios**y, a continuación, seleccione **DevTest prácticas** de la lista.

1. En la lista de prácticas, seleccione la práctica deseada.

1. En el módulo de la práctica, seleccione **configuración**. 

1. En el módulo de **configuración** , seleccione **los usuarios**.

1. En el módulo de **usuarios** , seleccione **+ Agregar**.

    ![Agregar usuario](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. En el módulo **Seleccionar una función** , seleccione la función deseada. La sección [acciones que pueden realizarse en cada rol](#actions-that-can-be-performed-in-each-role) enumera las distintas acciones que se pueden realizar usuarios de propietario, DevTest usuarios y roles de colaborador.

1. En el módulo de **Agregar usuarios** , escriba la dirección de correo electrónico o el nombre del usuario que desea agregar en la función especificada. Si no se encuentra el usuario, un mensaje de error explica el problema. Si se encuentra el usuario, ese usuario se muestran y seleccionado. 

1. Seleccione **Seleccionar**.

1. Seleccione **Aceptar** para cerrar el módulo de **access agregar** .

1. Cuando vuelva a la hoja de **usuarios** , el usuario se ha agregado.  

## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Agregar un usuario externo a un laboratorio con PowerShell

Además de agregar usuarios en el portal de Azure, puede agregar un usuario externo a la práctica mediante un script de PowerShell. En el ejemplo siguiente, basta con modificar los valores de parámetro en el comentario **para cambiar los valores** .
Puede recuperar el `subscriptionId`, `labResourceGroup`, y `labName` valores que aparecen en el módulo de práctica en el portal de Azure.

> [AZURE.NOTE]
> La secuencia de comandos de ejemplo se supone que el usuario especificado se ha agregado como invitado a Active Directory y se producirá un error si no es el caso. Para agregar un usuario no está en Active Directory a un laboratorio, use el portal de Azure para asignar al usuario a una función tal como se muestra en la sección, [Agregar un propietario o usuario en el nivel de práctica](#add-an-owner-or-user-at-the-lab-level).   

    # Add an external user in DevTest Labs user role to a lab
    # Ensure that guest users can be added to the Azure Active directory:
    # https://azure.microsoft.com/en-us/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource name here>"
    $labName = "<Enter lab name here>"
    $userDisplayName = "<Enter user's display name here>"

    # Log into your Azure account
    Login-AzureRmAccount
    
    # Select the Azure subscription that contains the lab. 
    # This step is optional if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    # Retrieve the user object
    $adObject = Get-AzureRmADUser -SearchString $userDisplayName
    
    # Create the role assignment. 
    $labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    New-AzureRmRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Agregar un propietario o el usuario en el nivel de suscripción

Azure permisos se propagarán de ámbito principal a ámbito secundario en Azure. Por lo tanto, los propietarios de una suscripción de Azure que contiene prácticas automáticamente son propietarios de esas prácticas. También poseen las máquinas virtuales y otros recursos creados por los usuarios del laboratorio y el servicio de Azure DevTest prácticas. 

Puede agregar propietarios adicionales a un laboratorio a través del módulo de la práctica en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Sin embargo, el ámbito del propietario agregado de la administración es más restringido que el ámbito del propietario de la suscripción. Por ejemplo, los propietarios de los agregados no tiene acceso completo a algunos de los recursos que se crean en la suscripción en el servicio de prácticas de DevTest. 

Para agregar un propietario de una suscripción de Azure, siga estos pasos:

1. Inicie sesión en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Más servicios**y, a continuación, seleccione **suscripciones** en la lista.

1. Seleccione la suscripción que desee.

1. Seleccione el icono de **Access** . 

    ![Usuarios de Access](./media/devtest-lab-add-devtest-user/access-users.png)

1. En el módulo de **usuarios** , seleccione **Agregar**.

    ![Agregar usuario](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. En el módulo **Seleccionar una función** , seleccione **propietario**.

1. En el módulo de **Agregar usuarios** , escriba la dirección de correo electrónico o el nombre del usuario que desea agregar como propietario. Si no se encuentra el usuario, recibe un mensaje de error que explica el problema. Si se encuentra el usuario, ese usuario aparece en el cuadro de texto de **usuario** .

1. Seleccione el nombre de usuario ubicada.

1. Seleccione **Seleccionar**.

1. Seleccione **Aceptar** para cerrar el módulo de **access agregar** .

1. Cuando vuelva a la hoja de **usuarios** , el usuario se ha agregado como un propietario. Este usuario es ahora un propietario de cualquier prácticas creado en esta suscripción y, por tanto, podrá realizar tareas de propietario. 

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
