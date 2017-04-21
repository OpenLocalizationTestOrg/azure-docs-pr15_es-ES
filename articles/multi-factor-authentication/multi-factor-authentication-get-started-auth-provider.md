<properties
    pageTitle="Obtener inició el proveedor de autenticación multifactor de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo crear un proveedor de autenticación multifactor de Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>



# <a name="getting-started-with-an-azure-multi-factor-auth-provider"></a>Introducción a un proveedor de autenticación multifactor de Azure
Activó la verificación predeterminada está disponible para los administradores globales que tienen los usuarios de Office 365 y Azure Active Directory. Sin embargo, si desea sacar partido de [las características avanzadas](multi-factor-authentication-whats-next.md) debe comprar la versión completa de autenticación de varios factores de Azure (AMF).

> [AZURE.NOTE]  Un proveedor de autenticación multifactor de Azure se usa para aprovechar las características de la versión completa de Azure AMF. Para los usuarios que **no tengan licencias a través Azure AMF, Azure AD Premium o EMS**.  Azure AMF, Azure AD Premium y EMS incluyen la versión completa de Azure AMF de forma predeterminada.  Si tiene suficientes licencias, no es necesario un proveedor de autenticación multifactor de Azure.

Para descargar el SDK, es necesario un proveedor de servicios de autenticación multifactor de Azure.

> [AZURE.IMPORTANT]  Para descargar el SDK, cree un proveedor de autenticación multifactor de Azure aunque tenga AMF Azure, AAD Premium o licencias EMS.  Si crea un proveedor de autenticación multifactor de Azure para ello y ya tiene licencias, asegúrese de crear el proveedor con el modelo **Por usuario habilitado** . A continuación, vincular el proveedor en el directorio que contiene las licencias de Azure AMF, Azure AD Premium o EMS.  Así se garantiza que se cargarán solo si tiene más usuarios únicos mediante el SDK de que el número de licencias que posee.


## <a name="to-create-a-multi-factor-auth-provider"></a>Para crear un proveedor de autenticación multifactor

Use los siguientes pasos para crear un proveedor de autenticación multifactor de Azure.

1. Inicie sesión como administrador en el [portal de clásico de Azure](https://manage.windowsazure.com) .
2. En la parte izquierda, seleccione **Active Directory**.
3. En la página de Active Directory, en la parte superior, seleccione **Los proveedores de autenticación multifactor**.
![Crear un proveedor AMF](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. En la parte inferior, haga clic en **nuevo**.
![Crear un proveedor AMF](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. En los servicios de aplicación, seleccione el **Proveedor de autenticación multifactor**
![crear un proveedor AMF](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. Seleccione **crear rápido**.
![Crear un proveedor AMF](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
5. Rellene los siguientes campos y seleccione **crear**.
    1. **Nombre** : el nombre del proveedor de autenticación multifactor.
    2. **Modelo de uso** : si desea permitir que los usuarios individuales o paga por verificación. Elija una de estas dos opciones:
        - Por autenticación: modelo de compra cargos por autenticación. Normalmente se utiliza para escenarios que usan autenticación multifactor de Azure en una aplicación de consumidor orientación.
        - Por usuario habilitado: modelo de compra cargos por usuario habilitado para. Normalmente se utiliza para el acceso de los empleados a aplicaciones como Office 365. Elija esta opción si tiene algunos usuarios que ya tiene una licencia para MFA de Azure.
    2. **Directorio de** inquilinos de Azure Active Directory que está asociado el proveedor de autenticación multifactor. Tenga en cuenta lo siguiente:
        - No es necesario un directorio de Azure AD para crear un proveedor de autenticación multifactor. Deje el cuadro en blanco si solo va a utilizar el servidor de autenticación multifactor de Azure o SDK.
        - El proveedor de servicios de autenticación multifactor debe estar asociado con un directorio de Azure AD sacar partido de las características avanzadas.
        - Azure AD Connect, AAD Sync o DirSync son solo un requisito si está sincronizando su entorno local de Active Directory con un directorio de Azure AD.  Si solo utiliza un directorio de Azure AD no está sincronizado, a continuación, esto no es necesario.
![Crear un proveedor AMF](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
5. Una vez que haga clic en crear, se crea el proveedor de autenticación multifactor y verá un mensaje que indica: **creado correctamente el proveedor de autenticación multifactor**. Haga clic en **Aceptar**.
![Crear un proveedor AMF](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)
