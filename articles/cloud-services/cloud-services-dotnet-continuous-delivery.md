<properties
    pageTitle="Servicios de entrega continua de nube con TFS en Azure | Microsoft Azure"
    description="Obtenga información sobre cómo configurar la entrega continua para las aplicaciones de nube de Azure. Ejemplos de código de instrucciones de línea de comandos de MSBuild y secuencias de comandos de PowerShell."
    services="cloud-services"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/30/2016"
    ms.author="tarcher"/>

# <a name="continuous-delivery-for-cloud-services-in-azure"></a>Entrega continua para los servicios de nube de Azure

El proceso descrito en este artículo muestra cómo configurar la entrega continua para las aplicaciones de nube de Azure. Este proceso le permite crear automáticamente los paquetes e implemente el paquete de Azure después de cada código de verificación. El proceso de generación de paquete descrito en este artículo es equivalente al comando de **paquete** en Visual Studio y los pasos de publicación son equivalentes para el comando **Publicar** en Visual Studio.
Este artículo describen los métodos que usaría para crear un servidor de compilación con instrucciones de línea de comandos de MSBuild y secuencias de comandos de Windows PowerShell y también se muestra cómo configurar opcionalmente Visual Studio Team Foundation Server - definiciones de generación de equipo para usar los comandos de MSBuild y las secuencias de comandos de PowerShell. El proceso es personalizable para su entorno de creación y entornos de destino de Azure.

También puede usar servicios de equipo de Visual Studio, una versión de TFS que se hospeda en Azure, para hacerlo más fácilmente. Para obtener más información, vea [Entrega continua a Azure mediante servicios de Visual Studio Team][].

Antes de empezar, debe publicar su aplicación de Visual Studio.
Esto se asegurará de que todos los recursos están disponibles e inicializado al intentar automatizar el proceso de publicación.

## <a name="1-configure-the-build-server"></a>1: configurar el servidor de generación

Antes de crear un paquete de Azure mediante MSBuild, debe instalar las herramientas y el software necesario en el servidor de compilación.

No es necesario que esté instalado en el servidor de generación de Visual Studio. Si desea usar el servicio de compilación de Team Foundation para administrar su servidor de compilación, siga las instrucciones en la documentación del [Servicio de compilación de Team Foundation][] .

1.  En el servidor de generación, instale [.NET Framework 4.5.2][], que incluye MSBuild.
2.  Instale las últimas [Herramientas de creación de Azure para .NET](https://azure.microsoft.com/develop/net/).
3.  Instalar las [bibliotecas de Azure para .NET](http://go.microsoft.com/fwlink/?LinkId=623519).
4.  Copie el archivo de Microsoft.WebApplication.targets desde una instalación de Visual Studio al servidor de compilación.

    En un equipo con instalado Visual Studio, este archivo se encuentra en el directorio C:\\programa (x 86)\\MSBuild\\Microsoft\\Visual Studio\\v14.0\\WebApplications. Debe copiarla en el mismo directorio en el servidor de compilación.
5.  Instale las [Herramientas de Azure para Visual Studio](https://www.visualstudio.com/features/azure-tools-vs.aspx).

## <a name="2-build-a-package-using-msbuild-commands"></a>2: crear un paquete con los comandos de MSBuild

Esta sección describe cómo crear un comando de MSBuild que genera un paquete de Azure. Ejecutar este paso en el servidor de compilación para comprobar que todo está configurado correctamente y que el comando MSBuild no lo que desea. Puede agregar esta línea de comandos para secuencias de comandos de compilación existentes en el servidor de compilación, o puede usar la línea de comandos en una definición de compilación de TFS, como se describe en la siguiente sección. Para obtener más información acerca de los parámetros de línea de comandos y MSBuild, vea [Referencia de la línea de comandos de MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).

1.  Si Visual Studio está instalado en el servidor de generación, busque y elija **Visual Studio comando mensaje** en la carpeta de **Visual Studio Tools** en Windows.

    Si no está instalado Visual Studio en el servidor de compilación, abra un símbolo del sistema y asegúrese de que MSBuild.exe es accesible en la ruta de acceso. MSBuild se instala con .NET Framework en la ruta de acceso % WINDIR %\\Microsoft.NET\\Framework\\*versión*. Por ejemplo, para agregar MSBuild.exe a la variable de entorno PATH cuando tenga instalado .NET Framework 4, escriba el comando siguiente en el símbolo del sistema:

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"

2.  En el símbolo del sistema, vaya a la carpeta que contiene el archivo de proyecto Azure que desea crear.

3.  Ejecutar MSBuild con la/target: publicar opción como en el ejemplo siguiente:

        MSBuild /target:Publish

    Esta opción se puede abreviar como /t: publicar. La opción /t:Publish en MSBuild no debe confundirse con los comandos de publicar disponibles en Visual Studio cuando haya instalado el SDK de Azure. El modificador /t: publicar versiones de opción sólo los paquetes de Azure. No implementa los paquetes de manera los comandos de publicar en Visual Studio.

    Si lo desea, puede especificar el nombre del proyecto como un parámetro de MSBuild. Si no se especifica, se utiliza el directorio actual. Para obtener más información acerca de las opciones de línea de comandos de MSBuild, vea [Referencia de la línea de comandos de MSBuild](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).

4.  Busque el resultado. De forma predeterminada, este comando crea un directorio con respecto a la carpeta raíz del proyecto, como *ProjectDir*\\Papelera\\*configuración*\\app.publish\\. Al crear un proyecto de Azure, se generan dos archivos, el archivo de paquete y el archivo adjunto de configuración:

    -   Project.cspkg
    -   ServiceConfiguration. *TargetProfile*.cscfg

    De forma predeterminada, cada proyecto Azure incluye un archivo de configuración de servicio (archivo .cscfg) para las versiones locales de (depuración) y otro para las versiones de nube (provisional o producción), pero puede agregar o quitar los archivos de configuración de servicio según sea necesario. Si genera un paquete de Visual Studio, se le pedirá que archivo de configuración de servicio para incluir junto con el paquete.

5.  Especifique el archivo de configuración de servicio. Al crear un paquete mediante MSBuild, el archivo de configuración de servicio local se incluye de forma predeterminada. Para incluir un archivo de configuración de servicio diferente, establezca la propiedad TargetProfile del comando MSBuild, como en el ejemplo siguiente:

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  Especifique la ubicación para la salida. Establecer la ruta de acceso mediante el /p:PublishDir =*directorio* \\ opción, incluido el separador de barra diagonal inversa al final, como en el ejemplo siguiente:

        MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

    Una vez que haya la construcción y probar una línea de comandos de MSBuild apropiada para generar los proyectos y combinarlos en un paquete de Azure, puede agregar esta línea de comandos para los scripts de compilación. Si su servidor de compilación usa scripts personalizados, este proceso dependerá de las características específicas de un proceso de generación personalizada. Si está utilizando TFS como un entorno de creación, a continuación, puede seguir las instrucciones en el siguiente paso para agregar la generación de Azure paquete a su proceso de generación.

## <a name="3-build-a-package-using-tfs-team-build"></a>3: crear un paquete mediante la compilación de grupo de TFS

Si tiene configurado como un controlador de compilación y el servidor de generación de Team Foundation Server (TFS) configurada como una máquina de compilación TFS y, opcionalmente, puede configurar una generación automatizada para el paquete de Azure. Para obtener información sobre cómo configurar y usar Team Foundation server como un sistema de compilación, consulte [escalado de su sistema de compilación][]. En particular, el procedimiento siguiente asume que ha configurado el servidor de compilación como se describe en [implementar y configurar un servidor de compilación][], y que se ha creado un proyecto, se crea un proyecto de servicio de nube en el proyecto.

Para configurar TFS para generar paquetes de Azure, realice los pasos siguientes:

1.  En Visual Studio en el equipo de desarrollo, en el menú Ver, elija **Team Explorer**o elija Ctrl +\\, CTRL+m. En la ventana Explorador de equipo, expanda el nodo **crea** o elija la página **crea** y elija **Nueva definición de compilación**.

    ![Nueva opción de definición de compilación][0]

2.  Elija la ficha **desencadenador** y especifique las condiciones que desee para cuando desee que el paquete de generarse. Por ejemplo, especifique **La integración continua** para generar el paquete si una comprobación de control de origen se produce.

3.  Elija la pestaña **Configuración de origen** y asegúrese de que la carpeta del proyecto se muestra en la columna de la **Carpeta de Control de código fuente** y el estado es **activo**.

4.  Elija la ficha **Valores predeterminados de compilación** y, en el controlador de compilación, compruebe el nombre del servidor de compilación.  Además, elija la opción **copia generar resultados en la siguiente carpeta de entrega** y especifique la ubicación de entrega que desee.

5.  Elija la pestaña **proceso** . En la pestaña proceso, elija la plantilla predeterminada, en **crear**, elija el proyecto si no está activada y expandir la sección **Avanzadas** en la sección **Generar** de la cuadrícula.

6.  Elija **Argumentos de MSBuild**y establecer los argumentos de línea de comandos de MSBuild correspondientes, tal como se describe en el paso 2 anterior. Por ejemplo, escriba **/t: publicar /p:PublishDir =\\\\MiServidor\\quita\\ ** para crear un paquete y copiar los archivos de paquete a la ubicación \\ \\MiServidor\\quita\\:

    ![Argumentos de MSBuild][2]

    **Nota:** Copiar los archivos a un recurso compartido público facilita implementar manualmente los paquetes desde el equipo de desarrollo.

5.  Probar el éxito del paso de generación activando un cambio en el proyecto o una nueva compilación en cola. Para poner en cola una compilación nueva, en el Explorador de grupo, haga clic en **Todas las definiciones de generación** y elija **Nueva compilación en cola**.

## <a name="4-publish-a-package-using-a-powershell-script"></a>4: publicar un paquete mediante una secuencia de comandos de PowerShell

Esta sección describe cómo crear un script de Windows PowerShell que se publicará la salida de paquete de aplicación de nube en Azure con parámetros opcionales. Esta secuencia de comandos se puede llamar después de que la compilación paso en la automatización de generación personalizada. También puede denominarse de actividades de flujo de trabajo de plantilla de procesos en Visual Studio TFS equipo de compilación.

1.  Instalar los [cmdlets de PowerShell de Azure][] (v0.6.1 o posterior).
    Durante la fase de instalación de cmdlet elija instalar como un complemento. Tenga en cuenta que esta versión compatible oficialmente reemplaza la versión anterior a través de CodePlex, aunque las versiones anteriores estaban numeradas 2.x.x.

2.  Iniciar PowerShell Azure usando el menú Inicio o la página de inicio. Si iniciar de este modo, se cargarán los cmdlets de PowerShell de Azure.

3.  En el símbolo del sistema de PowerShell, compruebe que se cargan los cmdlets de PowerShell escribiendo el comando parcial `Get-Azure` y, a continuación, presionando la tecla Tab para completar las instrucciones.

    Si presiona la tecla Tab varias veces, debería ver varios comandos de PowerShell de Azure.

4.  Compruebe que puede conectarse a su suscripción de Azure importando la información de la suscripción desde el archivo .publishsettings.

    `Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings`

    A continuación, escriba el comando

    `Get-AzureSubscription`

    Muestra información acerca de la suscripción. Compruebe que todo está correcto.

4.  Guardar la plantilla de secuencia de comandos que proporcionan al final de este artículo a la carpeta de scripts como c:\\secuencias de comandos\\WindowsAzure\\**PublishCloudService.ps1**.

5.  Revise la sección de parámetros de la secuencia de comandos. Agregar o modificar los valores predeterminados. Estos valores se pueden reemplazar siempre pasando parámetros explícitos.

6.  Asegúrese de que hay cuentas de almacenamiento y servicio de nube válida creadas en la suscripción que se puede elegir la secuencia de comandos de la publicación. La cuenta de almacenamiento (almacenamiento de blobs) se utilizará para cargar y almacenar temporalmente el archivo de paquete y configuración de implementación mientras se crea la implementación.

    -   Para crear un nuevo servicio de nube, puede llamar a esta secuencia de comandos o usar el [portal de clásico de Azure](http://go.microsoft.com/fwlink/?LinkID=213885). El nombre de servicio de nube que se utilizará como prefijo un nombre de dominio completo y, por tanto, debe ser único.

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   Para crear una nueva cuenta de almacenamiento, puede llamar a esta secuencia de comandos o usar el [portal de clásico de Azure](http://go.microsoft.com/fwlink/?LinkID=213885). El nombre de la cuenta de almacenamiento que se utilizará como prefijo un nombre de dominio completo y, por tanto, debe ser único. Puede intentar usar el mismo nombre que el servicio de nube.

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

7.  Llamar a la secuencia de comandos directamente en Azure PowerShell o conectar esta secuencia de comandos para la automatización de generación de host sea posterior a la compilación de paquete.

    >[AZURE.IMPORTANT] La secuencia de comandos siempre se eliminar o reemplazar las implementaciones existentes de forma predeterminada si se detectan. Esto es necesario para habilitar la entrega continua de automatización cuando no sea posible intervención del usuario.

    **Situación de ejemplo 1:** implementación continua al entorno de ensayo de un servicio:

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    Normalmente se hace un seguimiento en ejecución intercambio VIP y comprobación de pruebas. Intercambio de VIP puede realizarse a través del [portal clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885) o mediante el cmdlet de implementación de mover.

    **Situación de ejemplo 2:** implementación continua al entorno de producción de un servicio de prueba dedicada

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **Escritorio remoto:**

    Si se habilita Escritorio remoto en su proyecto Azure, que debe realizar algunos pasos adicionales para asegurarse de que el certificado de servicio de nube correcto se carga a todos los servicios de nube el objetivo de esta secuencia de comandos de una sola vez.

    Busque los valores de la huella digital de certificado esperados por sus funciones. Los valores de la huella digital están visibles en la sección de certificados del archivo de configuración de la nube (es decir, ServiceConfiguration.Cloud.cscfg). También está visible en el cuadro de diálogo Configuración de escritorio remoto en Visual Studio al mostrar opciones y ver el certificado seleccionado.

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    Cargar certificados de escritorio remoto como un paso de configuración única mediante el siguiente script de cmdlet:

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)

    Por ejemplo:

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    También puede exportar el archivo de certificado PFX con clave privada y cargar certificados para cada servicio de nube de destino con el [portal clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885). 
    Lea el siguiente artículo para obtener más información: [] [http://msdn.microsoft.com/library/windowsazure/gg443832.aspx].

    **Actualización de implementación de implementación frente a eliminar -\> implementación nueva**

    La secuencia de comandos predeterminada realizará una implementación de actualización ($enableDeploymentUpgrade = 1) cuando no se pasa ningún parámetro o el valor 1 se pasa explícitamente. Para instancias únicas tiene las ventajas de menos tiempo que una implementación completa. Para instancias que requieren alta disponibilidad que esto también tiene la ventaja de dejar algunas instancias que se ejecutan mientras que otros se actualizan (recorrido de su dominio de actualización), además de la dirección VIP no se eliminarán.

    Implementación de actualización puede deshabilitarse en la secuencia de comandos ($enableDeploymentUpgrade = 0) o pasando *enableDeploymentUpgrade - 0* como un parámetro, que modifica el comportamiento de la secuencia de comandos para eliminar cualquier implementación existente y, a continuación, crear una nueva implementación.

    >[AZURE.IMPORTANT] La secuencia de comandos siempre se eliminar o reemplazar las implementaciones existentes de forma predeterminada si se detectan. Esto es necesario para habilitar la entrega continua de automatización donde preguntar usuario/operador no es posible.

## <a name="5-publish-a-package-using-tfs-team-build"></a>5: publicar un paquete mediante la compilación de grupo de TFS

Este paso opcional conecta TFS grupo generar a la secuencia de comandos que creó en el paso 4, que controla la publicación de la versión de paquete para Azure. Esto implica modificar la plantilla de proceso utilizada por la definición de compilación para que se ejecute una actividad publicar al final del flujo de trabajo. La actividad de publicación ejecutará los comandos de PowerShell pasando parámetros de la compilación. Salida de MSBuild está destinado y publicar script se pueden por departamento en el resultado de compilación estándar.

1.  Editar la definición de compilación responsable continuo implementar.

2.  Seleccione la pestaña **proceso** .

3.  Siga [estas instrucciones](http://msdn.microsoft.com/library/dd647551.aspx) para agregar un proyecto de actividad para la plantilla de proceso de generación, descargue la plantilla predeterminada, agregar al proyecto y comprobar. Asigne un nombre nuevo, como AzureBuildProcessTemplate a la plantilla de proceso de compilación.

3.  Volver a la pestaña **proceso** y use **Mostrar detalles** para mostrar una lista de plantillas de procesos de compilación disponible. Elija el botón **nuevo...** y desplácese al proyecto que acaba de agrega y protegido. Busque la plantilla que acaba de crea y elija **Aceptar**.

4.  Abra la plantilla de proceso seleccionada para su edición. Puede abrir directamente en el Diseñador de flujo de trabajo o en el editor XML para trabajar con el XAML.

5.  Agregue la siguiente lista de argumentos nuevos como separar elementos de línea en la pestaña argumentos del Diseñador de flujo de trabajo. Todos los argumentos deben tener dirección = en y escriba = cadena. Estos se utilizará para parámetros de flujo de la definición de compilación en el flujo de trabajo, que, a continuación, se usan para llamar a la secuencia de comandos de la publicación.

        SubscriptionName
        StorageAccountName
        CloudConfigLocation
        PackageLocation
        Environment
        SubscriptionDataFileLocation
        PublishScriptLocation
        ServiceName

    ![Lista de argumentos.][3]

    El XAML correspondiente es similar a esta:

        <Activity  _ />
          <x:Members>
            <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
            <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
            <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
            <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
            <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
            <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
            <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
            <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
            <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
            <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
            <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
            <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
            <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
            <x:Property Name="GetVersion" Type="InArgument(x:String)" />
            <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
            <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
            <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
            <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
            <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
            <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
            <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
            <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
            <x:Property Name="Environment" Type="InArgument(x:String)" />
            <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
            <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
            <x:Property Name="ServiceName" Type="InArgument(x:String)" />
          </x:Members>

          <this:Process.MSBuildArguments>

6.  Agregar una secuencia nueva al final de ejecutar en el agente:

    1.  Empiece agregando una actividad de instrucción If para comprobar si un archivo de script válida. Establecer la condición en este valor:

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  A continuación, el caso de la instrucción If, agregar una nueva actividad de la secuencia. Establecer el nombre para mostrar 'Publicar de inicio'

    3.  Con el inicio publicar secuencia todavía seleccionada, agregue la siguiente lista de nuevas variables como separar elementos de línea en la ficha variables del Diseñador de flujo de trabajo. Todas las variables deben tener el tipo de Variable = cadena y ámbito = comienzo publicar. Estos se utilizará para parámetros de flujo de la definición de compilación en el flujo de trabajo, que, a continuación, se usan para llamar a la secuencia de comandos de la publicación.

        -   SubscriptionDataFilePath de tipo cadena

        -   PublishScriptFilePath de tipo cadena

            ![Nuevas variables][4]

    4.  Si está utilizando TFS 2012 o con versiones anteriores, agregar una actividad de ConvertWorkspaceItem al principio de la secuencia nueva. Si está utilizando TFS 2013 o posterior, agregue una actividad GetLocalPath al principio de la secuencia nueva. Para un ConvertWorkspaceItem, establezca las propiedades como sigue: dirección = ServerToLocal, DisplayName = 'Convertir publicar nombre de archivo de script', entrada = 'PublishScriptLocation', resultado = 'PublishScriptFilePath', área de trabajo = 'Área de trabajo'. Para una actividad GetLocalPath, establezca la propiedad IncomingPath a 'PublishScriptLocation' y a continuación, el resultado para 'PublishScriptFilePath'. Esta actividad convierte la ruta de acceso a la secuencia de comandos de publicación de ubicaciones del servidor TFS (si procede) a una ruta de acceso de disco local estándar.

    5.  Si está utilizando TFS 2012 o con versiones anteriores, agregue otra actividad ConvertWorkspaceItem al final de la secuencia nueva. Dirección = ServerToLocal, DisplayName = 'Convertir el nombre de archivo de suscripción', entrada = 'SubscriptionDataFileLocation', resultado = 'SubscriptionDataFilePath', área de trabajo = 'Área de trabajo'. Si está utilizando TFS 2013 o posterior, agregue otro GetLocalPath. IncomingPath = 'SubscriptionDataFileLocation' y resultado = 'SubscriptionDataFilePath'.

    6.  Agregar una actividad de InvokeProcess al final de la secuencia nueva.
        Esta actividad llamadas PowerShell.exe con los argumentos pasados por la definición de compilación.

        1.  Argumentos = String.Format ("-archivo""{0}" "- serviceName {1} - storageAccountName {2} - packageLocation""{3}" "- cloudConfigLocation""{4}" "- subscriptionDataFile""{5}" "- selectedSubscription 6}-entorno""{7}" "", PublishScriptFilePath, ServiceName, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, entorno)

        2.  DisplayName = Execute publicar script

        3.  Nombre de archivo = "PowerShell" (incluya las comillas)

        4.  OutputEncoding = System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)

    7.  En el cuadro de texto de sección de **Controlar la salida estándar** de la InvokeProcess, establezca el valor del cuadro de texto a 'datos'. Se trata de una variable para almacenar los datos de salida estándar.

    8.  Agregar una actividad WriteBuildMessage justo debajo de la sección **Controlar la salida estándar** . Definir la importancia = 'Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High' y el mensaje = 'datos'. Así se garantiza que la salida estándar de la secuencia de comandos obtener escribirá en el resultado de la compilación.

    9.  En la sección de **Salida de Error de controlador de** cuadro de texto de la InvokeProcess, establezca el valor de cuadro de texto "datos". Se trata de una variable para almacenar los datos de error estándar.

    10. Agregar una actividad WriteBuildError justo debajo de la sección **Controlador de salida de Error** . Establecer el mensaje = 'datos'. Así se garantiza que los errores de estándar de la secuencia de comandos obtener se escriben en el resultado de error de compilación.

    11. Corregir los errores, indicados por signos de exclamación azules. Mantenga el mouse sobre los signos de exclamación para obtener una sugerencia sobre el error. Guardar el flujo de trabajo para borrar errores.

    En el diseñador, el resultado final de las actividades de flujo de trabajo de publicación tendrá tener el siguiente aspecto:

    ![Actividades de flujo de trabajo][5]

    El resultado final de las actividades de flujo de trabajo de publicación tendrá un aspecto similar a este en XAML:

        <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap2010:WorkflowViewState.IdRef="If_1">
            <If.Then>
              <Sequence DisplayName="Start Publish" sap2010:WorkflowViewState.IdRef="Sequence_4">
                <Sequence.Variables>
                  <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                  <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
                </Sequence.Variables>
                <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_1" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
                <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_2" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
                <mtbwa:InvokeProcess Arguments="[String.Format(&quot; -File &quot;&quot;{0}&quot;&quot; -serviceName {1}&#xD;&#xA;            -storageAccountName {2} -packageLocation &quot;&quot;{3}&quot;&quot;&#xD;&#xA;            -cloudConfigLocation &quot;&quot;{4}&quot;&quot; -subscriptionDataFile &quot;&quot;{5}&quot;&quot;&#xD;&#xA;            -selectedSubscription {6} -environment &quot;&quot;{7}&quot;&quot;&quot;,&#xD;&#xA;            PublishScriptFilePath, ServiceName, StorageAccountName,&#xD;&#xA;            PackageLocation, CloudConfigLocation,&#xD;&#xA;            SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="'Execute Publish Script'" FileName="[PowerShell]" sap2010:WorkflowViewState.IdRef="InvokeProcess_1">
                  <mtbwa:InvokeProcess.ErrorDataReceived>
                    <ActivityAction x:TypeArguments="x:String">
                      <ActivityAction.Argument>
                        <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                      </ActivityAction.Argument>
                      <mtbwa:WriteBuildError Message="{x:Null}" sap2010:WorkflowViewState.IdRef="WriteBuildError_1" />
                    </ActivityAction>
                  </mtbwa:InvokeProcess.ErrorDataReceived>
                  <mtbwa:InvokeProcess.OutputDataReceived>
                    <ActivityAction x:TypeArguments="x:String">
                      <ActivityAction.Argument>
                        <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                      </ActivityAction.Argument>
                      <mtbwa:WriteBuildMessage sap2010:WorkflowViewState.IdRef="WriteBuildMessage_2" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                    </ActivityAction>
                  </mtbwa:InvokeProcess.OutputDataReceived>
                </mtbwa:InvokeProcess>
              </Sequence>
            </If.Then>
          </If>
        </Sequence>


7.  Guardar el flujo de trabajo de plantilla de proceso de generación y proteger este archivo.

8.  Editar la definición de compilación (cerrarla si ya está abierto) y seleccione el botón de **nuevo** si aún no ve la nueva plantilla en la lista de plantillas de proceso.

9.  Establezca el parámetro valores de propiedad de la sección varios como sigue:

    1.  CloudConfigLocation ='c:\\quita\\app.publish\\ServiceConfiguration.Cloud.cscfg' *se deriva de este valor: ($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation = ' c:\\quita\\app.publish\\ContactManager.Azure.cspkg' *se deriva de este valor: ($PublishDir)($ProjectName) .cspkg*

    3.  PublishScriptLocation = ' c:\\secuencias de comandos\\WindowsAzure\\PublishCloudService.ps1'

    4.  ServiceName = 'mycloudservicename' *Use el nombre de servicio de nube adecuado*

    5.  Entorno = 'Ensayo'

    6.  StorageAccountName = 'mystorageaccountname' *Use el nombre de cuenta de almacenamiento adecuado*

    7.  SubscriptionDataFileLocation = ' c:\\secuencias de comandos\\WindowsAzure\\Subscription.xml'

    8.  SubscriptionName = 'predeterminado'

    ![Valores de parámetro (propiedad)][6]

10. Guarde los cambios en la definición de compilación.

11. Poner en cola una compilación para ejecutar la compilación de paquete y publicar. Si tiene un desencadenador de integración continua, se ejecutará este comportamiento en cada verificación.

### <a name="publishcloudserviceps1-script-template"></a>Plantilla de script PublishCloudService.ps1

```
Param(  $serviceName = "",
        $storageAccountName = "",
        $packageLocation = "",
        $cloudConfigLocation = "",
        $environment = "Staging",
        $deploymentLabel = "ContinuousDeploy to $servicename",
        $timeStampFormat = "g",
        $alwaysDeleteExistingDeployments = 1,
        $enableDeploymentUpgrade = 1,
        $selectedsubscription = "default",
        $subscriptionDataFile = ""
     )


function Publish()
{
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue
    if ($a[0] -ne $null)
    {
        Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected. Creating a new deployment. "
    }
    #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
    if ($deployment.Name -ne $null)
    {
        switch ($alwaysDeleteExistingDeployments)
        {
            1
            {
                switch ($enableDeploymentUpgrade)
                {
                    1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Upgrading deployment."
                        UpgradeDeployment
                    }
                    0  #Delete then create new deployment
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Deleting deployment."
                        DeleteDeployment
                        CreateNewDeployment

                    }
                } # switch ($enableDeploymentUpgrade)
            }
            0
            {
                Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename.  Script execution cancelled."
                exit
            }
        } #switch ($alwaysDeleteExistingDeployments)
    } else {
            CreateNewDeployment
    }
}

function CreateNewDeployment()
{
    write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

    $opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"

    StartInstances
}

function UpgradeDeployment()
{
    write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

    # perform Update-Deployment
    $setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
}

function DeleteDeployment()
{

    write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

    #WARNING - always deletes with force
    $removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

    write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"

}

function StartInstances()
{
    write-progress -id 4 -activity "Starting Instances" -status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $runstatus = $deployment.Status

    if ($runstatus -ne 'Running')
    {
        $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
    }
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $oldStatusStr = @("") * $deployment.RoleInstanceList.Count

    while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
    {
        $i = 1
        foreach ($roleInstance in $deployment.RoleInstanceList)
        {
            $instanceName = $roleInstance.InstanceName
            $instanceStatus = $roleInstance.InstanceStatus

            if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
            {
                $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
            }

            write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
            $i = $i + 1
        }

        sleep -Seconds 1

        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    }

    $i = 1
    foreach ($roleInstance in $deployment.RoleInstanceList)
    {
        $instanceName = $roleInstance.InstanceName
        $instanceStatus = $roleInstance.InstanceStatus

        if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
        {
            $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
            Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
        }

        $i = $i + 1
    }

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $opstat = $deployment.Status

    write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
}

function AllInstancesRunning($roleInstanceList)
{
    foreach ($roleInstance in $roleInstanceList)
    {
        if ($roleInstance.InstanceStatus -ne "ReadyRole")
        {
            return $false
        }
    }

    return $true
}

#configure powershell with Azure 1.7 modules
Import-Module Azure

#configure powershell with publishsettings for your subscription
$pubsettings = $subscriptionDataFile
Import-AzurePublishSettingsFile $pubsettings
Set-AzureSubscription -CurrentStorageAccountName $storageAccountName -SubscriptionName $selectedsubscription
Select-AzureSubscription $selectedsubscription

#set remaining environment variables for Azure cmdlets
$subscription = Get-AzureSubscription $selectedsubscription
$subscriptionname = $subscription.subscriptionname
$subscriptionid = $subscription.subscriptionid
$slot = $environment

#main driver - publish & write progress to activity log
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

Publish

$deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
$deploymentUrl = $deployment.Url

Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."
```

## <a name="next-steps"></a>Pasos siguientes

Para habilitar la depuración remota cuando se utiliza la entrega continuo, vea [Habilitar depuración cuando utiliza la entrega continua para publicar en Azure remota](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md).

  [Entrega continua a Azure mediante Visual Studio Team Services]: cloud-services-continuous-delivery-use-vso.md  
  [Servicio de generación de Team Foundation]: https://msdn.microsoft.com/library/ee259687.aspx
  [.NET Framework 4]: https://www.microsoft.com/download/details.aspx?id=17851
  [.NET Framework 4.5]: https://www.microsoft.com/download/details.aspx?id=30653
  [.NET framework 4.5.2]: https://www.microsoft.com/download/details.aspx?id=42643
  [Escalar su sistema de compilación]: https://msdn.microsoft.com/library/dd793166.aspx
  [Implementar y configurar un servidor de compilación]: https://msdn.microsoft.com/library/ms181712.aspx
  [Cmdlets de PowerShell de Azure]: powershell-install-configure.md
  [the .publishsettings file]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0
  [0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01bc.png
  [2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
  [3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
  [4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
  [5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
  [6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png
