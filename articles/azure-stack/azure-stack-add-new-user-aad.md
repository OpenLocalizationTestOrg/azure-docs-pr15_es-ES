<properties
    pageTitle="Agregar una nueva cuenta de inquilino de pila de Azure en Azure Active Directory | Microsoft Azure"
    description="Después de implementar la prueba de concepto de Microsoft Azure pila, deberá crear la cuenta de usuario de al menos un inquilino por lo que puede explorar el portal de inquilinos."
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

# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Agregar una nueva cuenta de inquilino de pila de Azure en Azure Active Directory

Después de [implementar la prueba de concepto de pila de Azure](azure-stack-run-powershell-script.md), tendrá una cuenta de usuario de inquilinos para explorar el portal de inquilino y probar los planes y ofertas. Puede crear una cuenta de inquilino a [través del portal de Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) o con [PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Crear una cuenta de inquilino de pila de Azure con el portal de Azure

Debe tener una suscripción de Azure usar el portal de Azure.

1. Inicie sesión en [Azure](http://manage.windowsazure.com).

2.  En la barra de navegación izquierda de Microsoft Azure, haga clic en **Active Directory**.

3.  En la lista de directorio, haga clic en el directorio que desea usar para la pila de Azure o cree uno nuevo.

4.  En esta página de directorio, haga clic en **usuarios**.

5.  Haga clic en **Agregar usuario**.

6.  En el asistente **para agregar un usuario** , en la lista **tipo de usuario** , elija **nuevo usuario de su organización**.

7.  En el cuadro **nombre de usuario** , escriba un nombre para el usuario.

8.  En la **@** cuadro, seleccione la entrada correspondiente.

9.  Haga clic en la flecha siguiente.

10.  En la página de **perfil de usuario** del asistente, escriba un **nombre**, **apellido**y **nombre para mostrar**.

11. En la lista **función** , seleccione el **usuario**.

12. Haga clic en la flecha siguiente.

13. En la página **obtener una contraseña temporal** , haga clic en **crear**.

14. Copiar la **nueva contraseña**.

15. Inicie sesión con la nueva cuenta Microsoft Azure. Cambiar la contraseña cuando se le solicite.

16. Inicie sesión en `https://portal.azurestack.local` con la nueva cuenta para ver el portal del inquilino.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Crear una cuenta de inquilino de pila de Azure con PowerShell

Si no tiene una suscripción de Azure, no puede usar el portal de Azure para agregar una cuenta de usuario del inquilino. En este caso, puede usar el módulo Azure Active Directory para Windows PowerShell en su lugar.

> [AZURE.NOTE] Si usa Microsoft Account (Live ID) para implementar la prueba de concepto de pila de Azure, no puede usar PowerShell AAD para crear la cuenta de inquilino. 

1.  Instale los [servicios en línea sesión Ayudante de Microsoft para profesionales de TI RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).

2.  Instalar el [Azure Active Directory Module para Windows PowerShell (versión de 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297) y ábralo.

3.  Ejecute los cmdlets siguientes:




    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack PoC
   
            $msolcred = get-credential
    
    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".
    
            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId
    
    ```

4.  Inicie sesión con la nueva cuenta Microsoft Azure. Cambiar la contraseña cuando se le solicite.

5.  Inicie sesión en `https://portal.azurestack.local` con la nueva cuenta para ver el portal del inquilino.



