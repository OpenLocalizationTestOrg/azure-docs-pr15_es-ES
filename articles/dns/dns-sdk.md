<properties 
   pageTitle="Crear zonas DNS y conjuntos de registros de DNS de Azure mediante .NET SDK | Microsoft Azure" 
   description="Cómo crear las zonas DNS y conjuntos de registros de DNS de Azure mediante el SDK de .NET." 
   services="dns" 
   documentationCenter="na" 
   authors="jtuliani" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/19/2016"
   ms.author="jtuliani"/>


# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Crear zonas DNS y los conjuntos de registros mediante el SDK de .NET

Puede automatizar acciones para crear, eliminar o actualizar registros, conjuntos de registros y zonas DNS mediante DNS SDK con una biblioteca de administración de DNS de .NET. Hay un proyecto de Visual Studio completo [aquí.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Crear una cuenta de servicio principal

Normalmente, puede obtener acceso a los recursos de Azure se concede mediante una cuenta dedicada en lugar de sus propias credenciales de usuario. Estas cuentas dedicadas se denominan cuentas 'servicio principal'. Para usar el proyecto de ejemplo de SDK de DNS de Azure, primero debe crear una cuenta de servicio principal y asignar los permisos correctos.

1. Siga [estas instrucciones](../resource-group-authenticate-service-principal.md) para crear una cuenta de servicio principal (el proyecto de ejemplo de Azure DNS SDK supone autenticación basada en contraseña).

2. Crear un grupo de recursos ([le mostramos cómo](../azure-portal/resource-group-portal.md)).

3. Utilice Azure RBAC para conceder permisos de 'Colaborador de zona DNS' al grupo de recursos de la cuenta principal del servicio ([le mostramos cómo](../active-directory/role-based-access-control-configure.md).)

4. Si usa el proyecto de ejemplo de SDK de DNS de Azure, editar el archivo 'program.cs' como sigue:
    * Insertar los valores correctos para tenantId, clientId (también conocido como ID de cuenta), secreto (contraseña de la cuenta principal de servicio) y subscriptionId como se utiliza en el paso 1.
    * Escriba el nombre del grupo de recursos en el paso 2.
    * Escriba un nombre de zona DNS de su elección.

## <a name="nuget-packages-and-namespace-declarations"></a>Paquetes de NuGet y declaraciones de espacio de nombres

Para usar el SDK de .NET Azure DNS, debe instalar el paquete de NuGet de la **Biblioteca de administración de DNS de Azure** y otros paquetes de Azure necesarios.
 
1. En **Visual Studio**, abra un proyecto o un nuevo proyecto. 

2. Vaya a **Herramientas** **>** **Administrador de paquetes de NuGet** **>** **Administrar paquetes de NuGet para solución...**. 

3. Haga clic en **Examinar**, habilitar la casilla de verificación **incluir la versión preliminar** y escriba **Microsoft.Azure.Management.Dns** en el cuadro de búsqueda.

4. Seleccione el paquete y haga clic en **instalar** para agregar a un proyecto de Visual Studio.
 
5. Repita el proceso descrito anteriormente para instalar también los siguientes paquetes: **Microsoft.Rest.ClientRuntime.Azure.Authentication** y **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Agregue las declaraciones de espacio de nombres

Agregue las siguientes declaraciones de espacio de nombres

    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.Dns;
    using Microsoft.Azure.Management.Dns.Models;

## <a name="initialize-the-dns-management-client"></a>Inicializar al cliente de administración de DNS

El *DnsManagementClient* contiene los métodos y las propiedades necesarias para administrar conjuntos de registros y zonas DNS.  El código siguiente inicia sesión en la cuenta principal del servicio y crea un objeto DnsManagementClient.

    // Build the service credentials and DNS management client
    var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
    var dnsClient = new DnsManagementClient(serviceCreds);
    dnsClient.SubscriptionId = subscriptionId;

## <a name="create-or-update-a-dns-zone"></a>Crear o actualizar una zona DNS

Para crear una zona DNS, en primer lugar "Zona" se crea un objeto que contengan los parámetros de zona DNS. Dado que las zonas DNS no están vinculadas a un área específica, la ubicación se establece en 'global'. En este ejemplo, un [Administrador de recursos de Azure 'etiqueta'](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) también se agrega a la zona.

Para realmente crear o actualizar la zona de DNS de Azure, el objeto de zona que contiene los parámetros de zona se pasa al método *DnsManagementClient.Zones.CreateOrUpdateAsyc* .

>[AZURE.NOTE] DnsManagementClient admite tres modos de operación: sincrónico ('CreateOrUpdate'), asincrónica ('CreateOrUpdateAsync'), o asincrónico con acceso a la respuesta HTTP ('CreateOrUpdateWithHttpMessagesAsync').  Puede elegir cualquiera de estos modos, según las necesidades de la aplicación.

Azure DNS admite la simultaneidad optimista, denominado [Etags](dns-getstarted-create-dnszone.md). En este ejemplo, que especifica "*" para la 'If-None-coincidencia' encabezado indica Azure DNS para crear una zona DNS, si todavía no existe.  Se produce un error en la llamada si ya existe una zona con el nombre especificado en el grupo de recursos determinado.

    // Create zone parameters
    var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"
    
    // Create a Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
    dnsZoneParams.Tags = new Dictionary<string, string>();
    dnsZoneParams.Tags.Add("dept", "finance");
    
    // Create the actual zone.
    // Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
    // Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
    // Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
    var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");

## <a name="create-dns-record-sets-and-records"></a>Crear registros y conjuntos de registros de DNS

Registros DNS se administran como un conjunto de registros. Un conjunto de registros es un conjunto de registros con el mismo nombre y tipo de registro dentro de una zona.  Es el nombre del conjunto de registros en relación con el nombre de zona, no el nombre completo de DNS.

Para crear o actualizar un registro establecer un "conjunto de registros" objeto de parámetros se crea y se pasa a *DnsManagementClient.RecordSets.CreateOrUpdateAsync*. Como con las zonas DNS, han tres modos de funcionamiento: sincrónico ('CreateOrUpdate'), asincrónica ('CreateOrUpdateAsync'), o asincrónico con acceso a la respuesta HTTP ('CreateOrUpdateWithHttpMessagesAsync').

Al igual que con las zonas DNS, operaciones en conjuntos de registros son compatibles con simultaneidad optimista.  En este ejemplo, ya que se especifica 'If coincidir' ni 'If-None-coincidir', siempre se crea el conjunto de registros.  Esta llamada sobrescribe cualquier registro existente establecido con el mismo nombre y tipo de registro de esta zona DNS.

    // Create record set parameters
    var recordSetParams = new RecordSet();
    recordSetParams.TTL = 3600;

    // Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
    recordSetParams.ARecords = new List<ARecord>();
    recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

    // Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
    recordSetParams.Metadata = new Dictionary<string, string>();
    recordSetParams.Metadata.Add("user", "Mary");

    // Create the actual record set in Azure DNS
    // Note: no ETAG checks specified, will overwrite existing record set if one exists
    var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);

## <a name="get-zones-and-record-sets"></a>Obtener zonas y conjuntos de registros

Los métodos *DnsManagementClient.Zones.Get* y *DnsManagementClient.RecordSets.Get* recuperan zonas individuales y conjuntos de registros, respectivamente. Conjuntos de registros se identifican mediante su tipo, nombre y que se encuentran en el grupo de zona y recursos. Las zonas se identifican por su nombre y que se encuentran en el grupo de recursos.

    var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
    
## <a name="update-an-existing-record-set"></a>Actualizar un conjunto de registros existente.

Para actualizar un conjunto de registros de DNS existente, primero recuperar el conjunto de registros y actualizar el contenido del conjunto de registros, a continuación, envíe el cambio.  En este ejemplo, se especifica 'Etag' desde el conjunto de registros recuperados en el parámetro 'If coincidir'. Se produce un error en la llamada si una operación simultánea ha modificado el registro establecer mientras tanto.

    var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

    // Add a new record to the local object.  Note that records in a record set must be unique/distinct
    recordSet.ARecords.Add(new ARecord("5.6.7.8"));

    // Update the record set in Azure DNS
    // Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
    recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);

## <a name="list-zones-and-record-sets"></a>Las zonas de la lista y conjuntos de registros

Para las zonas de la lista, utilice los métodos *DnsManagementClient.Zones.List...* , que admiten la entrada correspondiente en todas las zonas de un grupo de recursos determinado o en todas las zonas de una suscripción de Azure dada (a través de los grupos de recursos). Conjuntos de registros de la lista, utilice los métodos de *DnsManagementClient.RecordSets.List...* , que enumera todos los conjuntos de registros en una zona determinada o solo los conjuntos de registros de un tipo específico de soporte técnico.

Tenga en cuenta cuando se enumeran las zonas y conjuntos de registros que resulta podrán paginar.  En el ejemplo siguiente se muestra cómo recorrer las páginas de resultados. (Se utiliza un tamaño de página de forma artificial pequeña de '2' obligar a paginación; en la práctica se debe omitir este parámetro y utiliza el tamaño de página predeterminado).

    // Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
    // In practice, to improve performance you would use a large page size or just use the system default
    int recordSets = 0;
    var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
    recordSets += page.Count();

    while (page.NextPageLink != null)
    {
        page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
        recordSets += page.Count();
    }

## <a name="next-steps"></a>Pasos siguientes

Descargar el [proyecto de ejemplo de Azure DNS .NET SDK](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), que incluye más ejemplos de cómo usar el SDK de .NET Azure DNS, junto con ejemplos para otros tipos de registros de DNS.
