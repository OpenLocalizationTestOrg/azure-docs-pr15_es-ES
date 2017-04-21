<properties
 pageTitle="Clúster HPC Pack para Excel y el inicio de autoridad | Microsoft Azure"
 description="Introducción a ejecutar cargas de trabajo de Excel y el inicio de autoridad a gran escala en un clúster de HPC Pack en Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>

<tags
 ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/25/2016"
 ms.author="danlep"/>

# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Empezar a ejecutar las cargas de trabajo de Excel y el inicio de autoridad en un clúster de HPC Pack en Azure

Este artículo le muestra cómo implementar un clúster de Microsoft HPC Pack en máquinas virtuales de Windows Azure mediante una plantilla de Azure tutorial u opcionalmente una secuencia de comandos de implementación de Azure PowerShell. El clúster utiliza imágenes de Azure Marketplace VM diseñadas para ejecutar Microsoft Excel o cargas de trabajo de arquitectura orientada a servicios (SOA) con HPC Pack. Puede usar el clúster para ejecutar simple HPC de Excel y servicios de inicio de autoridad desde un equipo de cliente local. Los servicios de Excel HPC incluyen descarga de libro de Excel y las funciones definidas por el usuario de Excel o UDF.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

En un nivel alto, el siguiente diagrama muestra el clúster HPC Pack que crear.

![Clúster HPC con nodos de cargas de trabajo de Excel][scenario]

## <a name="prerequisites"></a>Requisitos previos

*   **Equipo cliente** - necesita un equipo cliente basado en Windows para enviar trabajos de Excel y el inicio de autoridad de ejemplo para el clúster. También necesitará un equipo de Windows para ejecutar el script de implementación de Azure PowerShell clúster (si elige ese método de implementación) y

*   **Azure suscripción** : si no tiene una suscripción de Azure, puede crear un [libre cuenta](https://azure.microsoft.com/pricing/free-trial/) de dos minutos.

*   **Cuota de núcleos** - posible que necesite aumentar la cuota de núcleos, especialmente si implementa varios nodos de clúster con tamaños VM multinúcleo. Si está utilizando una plantilla de Azure tutorial, es la cuota de núcleos en el Administrador de recursos por región Azure. En ese caso, debe aumentar la cuota de una región específica. Vea [los límites de suscripción de Azure, cuotas y las restricciones](../azure-subscription-service-limits.md). Para aumentar una cuota, [Abra una solicitud de soporte técnico en línea para clientes](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) sin cargo.

*   **Licencia de Microsoft Office** : si implementa nodos de cálculo mediante una imagen de Marketplace HPC Pack VM con Microsoft Excel, una versión de evaluación de 30 días de Microsoft Excel Professional Plus 2013 está instalado. Después del punto de evaluación, debe proporcionar una licencia válida de Microsoft Office para activar Excel para continuar ejecutar las cargas de trabajo. Consulte [activación de Excel](#excel-activation) más adelante en este artículo. 


## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>Paso 1. Configurar un clúster de HPC Pack de Azure

Le mostraremos dos opciones para configurar el clúster: en primer lugar, mediante una plantilla de Azure tutorial y el portal de Azure y segundo, utilizando una secuencia de comandos de implementación de Azure PowerShell.


### <a name="option-1-use-a-quickstart-template"></a>Opción 1. Usar una plantilla de tutorial rápido
Usar una plantilla de Azure tutorial para implementar rápida y fácilmente un clúster HPC Pack en el portal de Azure. Al abrir la plantilla en el portal de, obtiene una interfaz de usuario simple donde especificar la configuración para el clúster. Estos son los pasos. 

>[AZURE.TIP]Si lo desea, use una [plantilla de Azure Marketplace](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) que crea un clúster similar específicamente para cargas de trabajo de Excel. Los pasos difieren ligeramente de las siguientes acciones.

1.  Visite la [página de plantilla de crear el clúster de HPC en GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster). Si lo desea, revise la información acerca de la plantilla y el código fuente.

2.  Haga clic en **implementar a Azure** para iniciar una implementación con la plantilla en el portal de Azure.

    ![Implementar plantilla en Azure][github]

3.  En el portal, siga estos pasos para especificar los parámetros de la plantilla de clúster HPC.

    una. En la página **parámetros** , escriba o modifique los valores de los parámetros de plantilla. (Haga clic en el icono junto a cada valor de la información de Ayuda). En la siguiente pantalla se muestran los valores de ejemplo. Este ejemplo crea un clúster denominado *hpc01* en el dominio *hpc.local* , que consta de un nodo principal y 2 nodos de cálculo. Los nodos de cálculo se crean desde una imagen de HPC Pack VM que incluye Microsoft Excel.

    ![Especificar los parámetros][parameters]

    >[AZURE.NOTE]El nodo principal VM se crea automáticamente desde la [imagen más reciente del catálogo de soluciones](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) de HPC Pack 2012 R2 en Windows Server 2012 R2. Actualmente, la imagen se basa en HPC Pack 2012 R2 actualización 3.
    >
    >Crean máquinas virtuales de nodo de cálculo de la imagen más reciente de la familia de nodo de cálculo seleccionadas. Seleccione la opción de **ComputeNodeWithExcel** para la imagen de nodo de cálculo de HPC Pack más reciente que incluye una versión de evaluación de Microsoft Excel Professional Plus 2013. Para implementar un clúster para sesiones de inicio de autoridad generales o para descargar UDF de Excel, elija la opción **ComputeNode** (sin instalado Excel).

    b. Elija la suscripción.

    c. Crear un grupo de recursos para el clúster, como *hpc01RG*.

    d. Elija una ubicación para el grupo de recursos, como Central de Estados Unidos.

    e. En la página **condiciones legales** , revise los términos. Si está de acuerdo, haga clic en **comprar**. A continuación, cuando haya terminado de configurar los valores de la plantilla, haga clic en **crear**.

4.  Cuando finalice la implementación (normalmente tarda alrededor de 30 minutos), exportar el archivo de certificado de clúster desde el nodo central. En un paso posterior, importar este certificado público en el equipo cliente para proporcionar la autenticación de servidor para enlace HTTP seguro.

    una. Conectar con el nodo principal escritorio remoto desde el portal de Azure.

     ![Conectar con el nodo principal][connect]

    b. Utilice procedimientos estándar en el Administrador de certificados para exportar el certificado de nodo principal (que se encuentra en el certificado: \LocalMachine\My) sin la clave privada. En este ejemplo, exportar *CN = hpc01.eastus.cloudapp.azure.com*.

    ![Exporte el certificado][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>Opción 2. Usar el script de implementación de HPC Pack IaaS

La secuencia de comandos de implementación de HPC Pack IaaS proporciona otra forma flexible para implementar un clúster de HPC Pack. Crea un clúster en el modelo de implementación clásico, mientras que la plantilla utiliza el modelo de implementación de administrador de recursos de Azure. Además, la secuencia de comandos es compatible con una suscripción de servicio Global de Azure o Azure China.

**Requisitos previos adicionales**

* **PowerShell Azure** - [instalar y configurar Azure PowerShell](../powershell-install-configure.md) (versión 0.8.10 o posterior) en el equipo cliente.

* **Script de implementación de HPC Pack IaaS** : descargar y descomprimir la última versión de la secuencia de comandos desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Comprobar la versión de la secuencia de comandos ejecutando `New-HPCIaaSCluster.ps1 –Version`. En este artículo se basa en la versión 4.5.0 o posterior de la secuencia de comandos.

**Crear el archivo de configuración**

 La secuencia de comandos de implementación de HPC Pack IaaS utiliza un archivo de configuración de XML como entrada que describa la infraestructura de clúster HPC. Para implementar un clúster que consta de un nodo principal y 18 nodos de cálculo creados a partir de la imagen de nodo de cálculo que incluye Microsoft Excel, sustituir valores en su entorno en el archivo de configuración de ejemplo siguientes. Para obtener más información sobre el archivo de configuración, consulte el archivo Manual.rtf en la carpeta de secuencia de comandos y [crear un clúster HPC con el script de implementación de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**Notas acerca del archivo de configuración**

* La **VMName** del nodo principal **debe** ser el mismo que **ServiceName**o trabajos de inicio de autoridad no se ejecuta.

* Asegúrese de que especifique **EnableWebPortal** para que se genera y exportar el certificado de nodo principal.

* El archivo especifica un script de PowerShell posteriores a la configuración PostConfig.ps1 que se ejecuta en el nodo principal. La siguiente secuencia de comandos configura la cadena de conexión de almacenamiento de Azure, quita la función de nodo de cálculo desde el nodo principal y obtiene todos los nodos en línea cuando se implementan. 

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**Ejecutar la secuencia de comandos**

1.  Abra la consola de PowerShell en el equipo cliente como administrador.

2.  Cambiar el directorio a la carpeta de script (E:\IaaSClusterScript en este ejemplo).

    ```
    cd E:\IaaSClusterScript
    ```
    
3.  Para implementar el clúster HPC Pack, ejecute el siguiente comando. En este ejemplo se presupone que el archivo de configuración se encuentra en E:\HPCDemoConfig.xml.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

La secuencia de comandos de implementación de HPC Pack se ejecute durante algún tiempo. Un elemento que hace la secuencia de comandos es exportar y descargar el certificado de clúster y guardarla en la carpeta documentos del usuario actual en el equipo cliente. La secuencia de comandos genera un mensaje similar al siguiente. En un paso siguiente, importar el certificado en el almacén de certificados adecuado.    
    
    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>Paso 2. Libros de Excel de descarga y ejecute UDF desde un cliente local

### <a name="excel-activation"></a>Activación de Excel

Al usar la imagen de VM ComputeNodeWithExcel para cargas de trabajo de producción, debe proporcionar una clave de licencia válida de Microsoft Office para activar los nodos de cálculo en Excel. En caso contrario, la versión de evaluación de Excel caduca después de 30 días y ejecuta los libros de Excel se producirá COMException (0x800AC472). 

Puede reactivación Excel otro 30 días de tiempo de evaluación: inicie sesión en el nodo principal y clusrun `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe` en Excel todos los nodos mediante el Administrador de clúster de HPC de cálculo. Puede reactivación un máximo de dos veces. Después de eso, debe proporcionar una clave de licencia válida de Office.

Office Professional Plus 2013 instalados en la imagen de VM es una edición por volumen con una clave de licencia de volumen genérico (GVLK). Puede activar mediante el servicio de administración de claves (KMS) / Active Directory-Based activación (AD BA) o clave de activación múltiple (MAK). 

    * Para utilizar KMS, AD-BA, use un servidor KMS existente o configurar una nueva utilizando el paquete de licencia de volumen de Microsoft Office 2013. (Si lo desea, configurar el servidor en el nodo principal.) A continuación, activar la clave de host KMS a través de Internet o por teléfono. A continuación, clusrun `ospp.vbs` para establecer el servidor KMS y puerto y activar Office en todos los nodos de cálculo de Excel. 
    
    * Usar MAK, primera clusrun `ospp.vbs` a la clave de entrada y, a continuación, activar todos los nodos a través de Internet o por teléfono de cálculo de Excel. 

>[AZURE.NOTE]Claves de producto comercial para Office Professional Plus 2013 no se puede utilizar con esta imagen de la máquina virtual. Si tiene claves válidas y medio de instalación de Office o Excel ediciones distintas esta edición por volumen Office Professional Plus 2013, puede usarlos en su lugar. En primer lugar, desinstale esta edición por volumen e instalar la edición que tiene. Puede capturar el nodo reinstalar de cálculo de Excel como una imagen VM personalizada para usar en una implementación a escala.

### <a name="offload-excel-workbooks"></a>Descarga de libros de Excel

Siga estos pasos para descargar un libro de Excel para que se ejecute en el clúster HPC Pack en Azure. Para ello, debe tener Excel 2010 o 2013 ya instalados en el equipo cliente.

1. Utilice una de las opciones en el paso 1 para implementar un clúster de HPC Pack con Excel para calcular imagen de nodo. Obtener el archivo de certificado de clúster (.cer) y el nombre de usuario de clúster y la contraseña.

2. En el equipo cliente, importar el certificado de clúster en el certificado: \CurrentUser\Root.

3. Asegúrese de que Excel está instalado. Crear un archivo Excel.exe.config con el siguiente contenido en la misma carpeta que Excel.exe en el equipo cliente. Este paso asegura que el complemento COM de Excel de HPC Pack 2012 R2 carga correctamente.

    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
    
4.  Configurar el cliente para enviar trabajos al clúster HPC Pack. Una opción es la completa [HPC Pack 2012 R2 actualización 3 instalación](http://www.microsoft.com/download/details.aspx?id=49922) de descargar e instalar al cliente de HPC Pack. Como alternativa, descargue e instale las [Herramientas de cliente de HPC Pack 2012 R2 actualización 3](https://www.microsoft.com/download/details.aspx?id=49923) y la Visual adecuado C++ 2010 redistribuible en su equipo ([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555)).

5.  En este ejemplo, se utiliza un libro de Excel de muestra denominado ConvertiblePricing_Complete.xlsb. Puede descargarla [aquí](https://www.microsoft.com/en-us/download/details.aspx?id=2939).

6.  Copiar el libro de Excel en una carpeta de trabajo como D:\Excel\Run.

7.  Abra el libro de Excel. En la cinta de **desarrollar** , haga clic en **Complementos COM** y confirme que el complemento COM de Excel de HPC Pack se cargó correctamente.

    ![Complemento para HPC paquetes de Excel][addin]

8.  Modificar la macro VBA HPCControlMacros en Excel cambiando las líneas comentadas tal como se muestra en la siguiente secuencia de comandos. Sustituir los valores apropiados para su entorno.

    ![Macro de Excel para HPC Pack][macro]

    ```
    'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
    Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"

    'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
    Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.Initialize ActiveWorkbook
    HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles

    'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
    HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
    HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
```

9.  Copiar el libro de Excel en un directorio de carga como D:\Excel\Upload. Este directorio especificado en la constante HPC_DependsFiles en la macro de VBA.

10. Para ejecutar el libro en el clúster en Azure, haga clic en el botón de **clúster** de cálculo.

### <a name="run-excel-udfs"></a>Ejecutar UDF de Excel

Para ejecutar UDF de Excel, siga los pasos anteriores 1 a 3 para configurar el equipo cliente. Para UDF de Excel, no es necesario tener la aplicación de Excel instalada en los nodos de cálculo. Por lo tanto, al crear el clúster de nodos de cálculo, puede elegir una normal calcular imagen de nodo en lugar de la imagen de nodo de cálculo con Excel.

>[AZURE.NOTE] Hay un límite de 34 caracteres en Excel 2010 y cuadro de diálogo de conector de clúster de 2013. Use este cuadro de diálogo para especificar el clúster que se ejecuta la UDF. Si el nombre de clúster completa es más largo (por ejemplo, hpcexcelhn01.southeastasia.cloudapp.azure.com), no se ajusta en el cuadro de diálogo. La solución alternativa consiste en establecer una variable de todo el equipo como *CCP_IAASHN* con el valor del nombre del clúster larga. A continuación, escriba *% CCP_IAASHN %* en el cuadro de diálogo como el nombre de nodo principal del clúster. 

Después de que el clúster se ha implementado correctamente, continúe con los pasos siguientes para ejecutar un ejemplo integrado UDF de Excel. Para UDF Excel personalizada, consulte estos [recursos](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) para generar el XLL e implementar a ellos en el clúster de IaaS.

1.  Abra un nuevo libro de Excel. En la cinta de **desarrollar** , haga clic en **Complementos**. A continuación, en el cuadro de diálogo, haga clic en **Examinar**, vaya a la carpeta %CCP_HOME%Bin\XLL32 y seleccione el ejemplo ClusterUDF32.xll. Si el ClusterUDF32 no existe en el equipo cliente, cópiela desde la carpeta %CCP_HOME%Bin\XLL32 en el nodo principal.

    ![Seleccione el archivo UDF][udf]

2.  Haga clic en **archivo** > **Opciones** > **Avanzadas**. En **las fórmulas**, active **Permitir de funciones XLL definidas por el usuario para que se ejecute en un clúster de cálculo**. A continuación, haga clic en **Opciones** y escriba el nombre de clúster completa en **nombre de clúster nodo principal**. (Como se indicó anteriormente este cuadro de entrada se limita a 34 caracteres, por lo que no puede ajustar un nombre de clúster larga. Puede utilizar una variable de todo el equipo aquí un nombre de clúster largo).

    ![Configurar la UDF][options]

3.  Para ejecutar el cálculo UDF en el clúster, haga clic en la celda con el valor =XllGetComputerNameC() y presione ENTRAR. La función simplemente recupera el nombre del nodo de cálculo en el que se ejecuta el archivo UDF. Para la primera ejecución, un cuadro de diálogo credenciales le pide el nombre de usuario y contraseña para conectarse al clúster IaaS.

    ![Ejecutar archivo UDF][run]

    Cuando hay varias celdas para calcular, presione Alt-Mayús-Ctrl + F9 para ejecutar el cálculo en todas las celdas.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>Paso 3. Ejecutar una carga de trabajo de inicio de autoridad desde un cliente local

Para ejecutar aplicaciones de inicio de autoridad general en el clúster HPC Pack IaaS, primero use uno de los métodos en el paso 1 para implementar el clúster. Especifique un genérico calcular imagen de nodo en este caso, ya no necesita Excel en los nodos de cálculo. A continuación, siga estos pasos.

1. Después de recuperar el certificado de clúster, importar en el equipo cliente en el certificado: \CurrentUser\Root.

2. Instale la [Actualización 3 SDK de HPC Pack 2012 R2](http://www.microsoft.com/download/details.aspx?id=49921) y [Utilidades de cliente de HPC Pack 2012 R2 actualización 3](https://www.microsoft.com/download/details.aspx?id=49923). Estas herramientas permiten programar y ejecutar aplicaciones de cliente de inicio de autoridad.

3. Descargar el [código de ejemplo](https://www.microsoft.com/download/details.aspx?id=41633)de HelloWorldR2. Abra la HelloWorldR2.sln en Visual Studio 2010 o 2012.

4. Generar primero el proyecto EchoService. A continuación, implementar el servicio en el clúster de IaaS de la misma manera que se implementa en un clúster local. Para conocer los pasos detallados, consulte el archivo Léame.doc en HelloWordR2. Modificar y generar el HellWorldR2 y otros proyectos, como se describe en la sección siguiente para generar las aplicaciones de cliente de inicio de autoridad que se ejecutan en un clúster de Azure IaaS.

### <a name="use-http-binding-with-azure-storage-queue"></a>Usar enlace Http con cola de almacenamiento de Azure

Para usar enlace Http con una cola de almacenamiento de Azure, realizar algunos cambios en el código de ejemplo.

* Actualizar el nombre de clúster.

    ```
// Before
const string headnode = "[headnode]";
// After e.g.
const string headnode = "hpc01.eastus.cloudapp.azure.com";
or
const string headnode = "hpc01.cloudapp.net";
```

* Si lo desea, use el valor predeterminado TransportScheme en SessionStartInfo o establezca explícitamente Http.

```
    info.TransportScheme = TransportScheme.Http;
```

* Usar enlace predeterminado para la BrokerClient.

    ```
// Before
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
// After
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
```

    O establezca explícitamente utilizando el basicHttpBinding.

    ```
BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
```

* Opcionalmente, establezca el indicador UseAzureQueue en true en SessionStartInfo. Si no ha establecido, se establecerá true de forma predeterminada cuando el nombre de clúster tiene sufijos de dominio Azure y la TransportScheme es Http.

    ```
    info.UseAzureQueue = true;
```

###<a name="use-http-binding-without-azure-storage-queue"></a>Utilizar el enlace de Http sin cola de almacenamiento de Azure

Para utilizar el enlace de Http sin una cola de almacenamiento de Azure, establezca explícitamente el indicador de UseAzureQueue falso en la SessionStartInfo.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>Utilizar el enlace de NetTcp

Para utilizar el enlace de NetTcp, la configuración es similar a conectarse a un clúster local. Debe abrir algunos extremos en el nodo principal VM. Si utiliza el script de implementación de HPC Pack IaaS para crear el clúster, por ejemplo, establezca los extremos en el portal de clásico Azure como sigue.


1. Detener la máquina virtual.

2. Agregue los puertos TCP 9090, 9087, 9091, 9094 de la sesión, adaptar, intermediario trabajador y servicios de datos, respectivamente

    ![Configurar extremos][endpoint]

3. Inicie la máquina virtual.

La aplicación de cliente de inicio de autoridad no requiere cambios excepto modificar el nombre para el nombre completo del clúster IaaS cabeza.

## <a name="next-steps"></a>Pasos siguientes

* Vea [estos recursos](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) para obtener más información acerca de cómo ejecutar cargas de trabajo de Excel con HPC Pack.

* Para obtener más información sobre cómo implementar y administrar servicios de inicio de autoridad con HPC Pack, vea [Administración de servicios de inicio de autoridad en Microsoft HPC Pack](https://technet.microsoft.com/library/ff919412.aspx) .

<!--Image references-->
[scenario]: ./media/virtual-machines-windows-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-windows-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-windows-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-windows-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-windows-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-windows-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-windows-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-windows-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-windows-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-windows-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-windows-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-windows-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-windows-excel-cluster-hpcpack/udf.png
