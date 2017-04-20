<properties
   pageTitle="Administrar servicios de análisis de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo administrar un servidor de Analysis Services en Azure."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="manage-analysis-services"></a>Administrar Analysis Services

Una vez que haya creado un servidor de Analysis Services en Azure, es posible que algunas tareas de administración que necesita realizar inmediato o en algún momento más adelante. Por ejemplo, ejecute el procesamiento a los datos de la actualización, controlar quién puede tener acceso a los modelos en su servidor o supervisar el mantenimiento de su servidor. Algunas tareas de administración sólo pueden realizarse en el portal de Azure, otros usuarios en SQL Server Management Studio (SSMS), y pueden hacer algunas tareas en uno.

## <a name="azure-portal"></a>Portal de Azure
El [portal de Azure](http://portal.azure.com/) es donde puede crear y eliminar servidores, supervisar los recursos de servidor, cambiar el tamaño y administrar quién tiene acceso a los servidores.  Si tiene problemas, también puede enviar una solicitud de soporte técnico.

![Obtener el nombre del servidor de Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Conectarse a su servidor de Azure es igual que conectarse a una instancia del servidor en su propia organización. De SSMS, puede realizar muchas de las tareas, como los datos de proceso o crear un script de procesamiento, administrar roles y usar PowerShell.

![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

 Una de las diferencias más grandes es la autenticación que utilice para conectarse al servidor. Para conectarse al servidor de Azure Analysis Services, debe seleccionar **La autenticación de contraseña de Active Directory**.

### <a name="to-connect-with-ssms"></a>Para conectarse con SSMS
1. Antes de conectarse, debe obtener el nombre del servidor. En **el portal de Azure** > servidor > **información general sobre** > **nombre del servidor**, copie el nombre del servidor.

    ![Obtener el nombre del servidor de Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

2. En SSMS > **Explorador de objetos**, haga clic en **Conectar** > **Analysis Services**.

3. En el cuadro de diálogo **Conectar con el servidor** , pegue en el nombre del servidor, a continuación, en **autenticación**, elija uno de estos procedimientos:

    **Autenticación integrada de active Directory** para usar el inicio de sesión único con Active Directory para la federación de Active Directory de Azure.

    **Autenticación de contraseña de active Directory** para usar una cuenta de la organización. Por ejemplo, cuando conexión de un elemento que no es de dominio unido equipo.

    Nota: Si no ve la autenticación de Active Directory, debe [Habilitar la autenticación de Azure Active Directory](#enable-azure-active-directory-authentication) de SSMS.

    ![Conectar en SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

Puesto que la administración de su servidor de Azure mediante SSMS es igual que la administración de un servidor local, no vamos a entrar en detalles. La Ayuda necesita puede encontrarse en la [Administración de instancias de Analysis Services](https://msdn.microsoft.com/library/hh230806.aspx) en MSDN.

## <a name="server-administrators"></a>Administradores del servidor
Puede usar **Los administradores de Analysis Services** en el módulo de control del servidor en el portal de Azure o SSMS para administrar los administradores del servidor. Los administradores de servicios de análisis son los administradores del servidor de base de datos con derechos para las tareas comunes de administración de base de datos, como agregar y quitar las bases de datos y administración de usuarios. De forma predeterminada, el usuario que crea el servidor en el portal de Azure se agrega automáticamente como un administrador de servicios de análisis.

También debe conocer:

-   Windows Live ID no es un tipo de identidad admitidos para los servicios de análisis de Azure.  
-   Los administradores de Analysis Services deben ser usuarios válidos de Azure Active Directory.
-   Si crea un servidor de Azure Analysis Services a través de plantillas de administrador de recursos de Azure, los administradores de servicios de análisis toma una matriz JSON de usuarios que deben agregarse como administradores.

Los administradores de servicios de análisis pueden ser diferentes de los administradores de recursos de Azure, que pueden administrar los recursos para suscripciones de Azure. Mantiene la compatibilidad con XMLA y TSML existentes administrar los comportamientos de Analysis Services y para que pueda separar obligaciones entre la administración de recursos de Azure y análisis de administración de base de datos de servicios.

Para ver todas las funciones y acceder a los tipos de su recurso de Azure Analysis Services, use el control de acceso (IAM) en el módulo de control.

## <a name="database-users"></a>Usuarios de la base de datos
Usuarios de base de datos de modelo de Azure Analysis Services deben estar en Azure Active Directory. Nombres de usuario especificada para la base de datos del modelo deben ser por la dirección de correo de organización o UPN. Esto es diferente de las bases de datos de modelo de local que admiten usuarios nombres de usuario de dominio de Windows.

Puede agregar usuarios mediante el uso de [las asignaciones de roles de Azure Active Directory](../active-directory/role-based-access-control-configure.md) o mediante el [Lenguaje de secuencias de comandos de modelo Tabular](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL) en SQL Server Management Studio.

**Secuencia de comandos de ejemplo TMSL**

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Users"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@contoso.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="enable-azure-active-directory-authentication"></a>Habilitar la autenticación de Azure Active Directory
Para habilitar la característica de autenticación de Azure Active Directory para SSMS en el registro, cree un archivo de texto denominado EnableAAD.reg, a continuación, copie y pegue lo siguiente:


```
Windows Registry Editor Version 5.00
[HKEY_CURRENT_USER\Software\Microsoft\Microsoft SQL Server\Microsoft Analysis Services\Settings]
"AS AAD Enabled"="True"
```

Guarde y ejecute el archivo.



## <a name="next-steps"></a>Pasos siguientes
Si ya no ha implementado un modelo tabular en su servidor nuevo, ahora es un buen momento. Para obtener más información, vea [implementar Azure Analysis Services](analysis-services-deploy.md).

Si ha implementado un modelo en su servidor, está listo para conectarse a ella mediante un explorador o un cliente. Para obtener más información, vea [obtener datos del servidor de Azure Analysis Services](analysis-services-connect.md).
