<properties
    pageTitle="Crear programas de autenticación no interactivo .NET HDInsight | Microsoft Azure"
    description="Aprenda a crear aplicaciones de .NET HDInsight de autenticación no interactivo."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="create-non-interactive-authentication-net-hdinsight-applications"></a>Crear aplicaciones de .NET HDInsight de autenticación no interactivo

Puede ejecutar la aplicación .NET HDInsight de Azure en la identidad de la aplicación (no interactiva) o en la identidad del usuario que ha iniciado sesión en de la aplicación (interactiva). Para obtener un ejemplo de la aplicación interactivo, vea [trabajos de enviar subárbol/Sqoop/cerdo con HDInsight .NET SDK](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). En este artículo se muestra cómo crear la aplicación de autenticación no interactivo .NET para conectarse a HDInsight de Azure y enviar un trabajo de la sección.

Desde la aplicación .NET, necesitará:

- el identificador del inquilino de suscripción de Azure
- el identificador de cliente de la aplicación de directorio de Azure
- el directorio de Azure aplicación clave.  

El proceso principal incluye los siguientes pasos:

2. Crear una aplicación de directorio de Azure.
2. Asignar roles a la aplicación de AD.
3. Desarrollar su aplicación de cliente.


##<a name="prerequisites"></a>Requisitos previos

- Clúster de HDInsight. Puede crear una siguiendo las instrucciones que se encuentra en el [tutorial de introducción](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). 




## <a name="create-azure-directory-application"></a>Crear aplicaciones de directorio de Azure 
Cuando se crea una aplicación de Active Directory, en realidad crea la aplicación y un servicio principal. Puede ejecutar la aplicación en la identidad de la aplicación.

Actualmente, debe usar el portal de clásico Azure para crear una nueva aplicación de Active Directory. Esta capacidad se agregará el portal de Azure en una versión posterior. También puede realizar estos pasos mediante PowerShell Azure o CLI de Azure. Para obtener más información sobre cómo usar PowerShell o CLI con el servicio principal, vea [servicio de autenticación principal con el Administrador de recursos de Azure](../resource-group-authenticate-service-principal.md).

**Para crear una aplicación de directorio de Azure**

1.  Inicie sesión en el [portal de clásico de Azure]( https://manage.windowsazure.com/).
2.  Seleccione **Active Directory** en el panel izquierdo.

    ![Azure clásica portal active directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\active-directory.png)
    
3.  Seleccione el directorio que desea usar para crear la nueva aplicación. Deberá ser uno existente.
4.  Haga clic en **aplicaciones** desde la parte superior para las aplicaciones existentes de la lista.
5.  Haga clic en **Agregar** desde la parte inferior para agregar una nueva aplicación.
6.  Escriba el **nombre**, seleccione **aplicación Web o Web API**y, a continuación, haga clic en **siguiente**.

    ![nueva aplicación de azure active directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application.png)

7.  Escriba la **dirección URL de inicio de sesión en** y **URI de ID de aplicación**. Para la **Dirección URL de inicio de sesión**, proporcione la URI a un sitio web que describa la aplicación. No se valida la existencia del sitio web. URI de ID de aplicación, proporcione el URI que identifica la aplicación. Y, a continuación, haga clic en **completado**.
Se necesitan unos minutos para crear la aplicación.  Una vez creada la aplicación, el portal muestra la página Glace rápido de la nueva aplicación. No cierre el portal. 

    ![nuevas propiedades de aplicación de azure active directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application-properties.png)

**Para obtener a la aplicación cliente ID y la clave secreta**

1.  Desde la página de aplicación AD recién creada, haga clic en **Configurar** en el menú superior.
2.  Hacer una copia de **Identificador de cliente**. Que necesitará en su aplicación .NET.
3.  En **las teclas**, haga clic en el menú desplegable **Seleccionar duración** y seleccione **año 1** o **2 años**. El valor de clave no se mostrará hasta que se guarda la configuración.
4.  Haga clic en **Guardar** en la parte inferior de la página. Cuando aparezca la clave, hacer una copia de la clave. Que necesitará en su aplicación .NET.

##<a name="assign-ad-application-to-role"></a>Asignar la aplicación de AD a función

Debe asignar la aplicación a un [rol](../active-directory/role-based-access-built-in-roles.md) para conceder permisos para realizar acciones. Puede establecer el ámbito en el nivel de la suscripción, el grupo de recursos o el recurso. Los permisos se heredan a niveles inferiores de ámbito (por ejemplo, agregar que una aplicación a la función Lector de un grupo de recursos significa que puede leer el grupo de recursos y los recursos que contiene). En este tutorial, se establece el ámbito en el nivel de grupo de recursos.  Como el portal clásico Azure no admite grupos de recursos, este elemento debe realizarse desde el portal de Azure. 

**Para agregar el rol de propietario a la aplicación de AD**

1.  Inicie sesión en el [portal de Azure](https://portal.azure.com).
2.  En el panel izquierdo, haga clic en **Grupo de recursos** .
3.  Haga clic en el grupo de recursos que contiene el clúster HDInsight donde se ejecutará la consulta de la sección más adelante en este tutorial. Si hay demasiados grupos de recursos, puede usar el filtro.
4.  Haga clic en **acceso** desde el módulo de clúster.

    ![icono de nube y thunderbolt = tutorial rápido](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)
5.  Haga clic en **Agregar** desde el módulo de **los usuarios** .
6.  Siga las instrucciones para agregar el rol de **propietario** a la aplicación de AD que creó en el último procedimiento. Cuando se completa correctamente, se verá la aplicación que aparece en el módulo de usuarios con el rol de propietario.


##<a name="develop-hdinsight-client-application"></a>Desarrolle aplicaciones cliente de HDInsight

Crear una aplicación de consola de C# .net siguiendo las instrucciones que se encuentra en [Hadoop enviar trabajos en HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). A continuación, reemplace el método GetTokenCloudCredentials con lo siguiente:

    public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
    {
        var authFactory = new AuthenticationFactory();

        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };

        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

        var accessToken =
            authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never)
                .AccessToken;

        return new TokenCloudCredentials(accessToken);
    }

Para recuperar el identificador del inquilino a través de PowerShell:

    Get-AzureRmSubscription

O bien, CLI Azure:

    azure account show --json

      
## <a name="see-also"></a>Vea también

- [Enviar trabajos de Hadoop en HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Crear la aplicación de Active Directory y el servicio principal con portal](../resource-group-create-service-principal-portal.md)
- [Autenticar a principal del servicio con el Administrador de recursos de Azure](../resource-group-authenticate-service-principal.md)
- [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md)
