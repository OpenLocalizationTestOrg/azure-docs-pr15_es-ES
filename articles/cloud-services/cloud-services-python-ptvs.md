<properties
    pageTitle="Roles de web y trabajador Python con Visual Studio | Microsoft Azure"
    description="Información general acerca de Python Tools para Visual Studio para crear los servicios de nube de Azure incluyendo funciones de web y las funciones de trabajo."
    services="cloud-services"
    documentationCenter="python"
    authors="thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.date="08/03/2016"
    ms.author="adegeo"/>


# <a name="python-web-and-worker-roles-with-python-tools-for-visual-studio"></a>Roles de web y trabajador Python con Python Tools para Visual Studio

En este artículo se proporciona información general sobre el uso de funciones de web y trabajador Python usando [Python Tools para Visual Studio][]. Aprenderá cómo usar Visual Studio para crear e implementar un servicio de nube básico que usa Python.

## <a name="prerequisites"></a>Requisitos previos

 - 2013 de Visual Studio o de 2015
 - [Python Tools para Visual Studio][] (PTVS)
 - [Herramientas del SDK Azure para VS 2013][] o [Herramientas del SDK Azure VS 2015][]
 - [Python 2.7 32 bits][] o [Python 3.5 32 bits][]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>¿Qué son los roles de web y trabajador de Python?

Azure ofrece tres modelos para ejecutar aplicaciones de calcular: [característica de aplicaciones Web de servicio de la aplicación de Azure][execution model-web sites], [máquinas virtuales de Azure][execution model-vms]y [Servicios de nube de Azure][execution model-cloud services]. Los tres modelos admiten Python. Servicios de nube, que incluyen funciones web y trabajo, proporcionan la *plataforma como servicio (PaaS)*. Dentro de un servicio de nube una función web proporciona un servidor web de Internet Information Services (IIS) dedicado a las aplicaciones web front-end de host, mientras una función de trabajo puede ejecutar asincrónicas, ejecución larga o perpetuos tareas independientes de entrada o interacción del usuario.

Para obtener más información, vea [¿Qué es un servicio de nube?].

> [AZURE.NOTE]*¿Desea crear un sitio Web simple?*
Si su caso implica simplemente un simple sitio Web front-end, considere la posibilidad de mediante la característica de aplicaciones Web ligera de servicio de aplicaciones de Azure. Puede actualizar fácilmente a un servicio de nube que crece de su sitio Web y cambian los requisitos. Consulte el <a href="/develop/python/">Centro para desarrolladores de Python</a> para artículos que tratan el desarrollo de la característica de aplicaciones Web de servicio de aplicaciones de Azure.
<br />


## <a name="project-creation"></a>Creación de un proyecto

En Visual Studio, puede seleccionar el **Servicio de nube de Azure** en el cuadro de diálogo **Nuevo proyecto** , en **Python**.

![Cuadro de diálogo nuevo proyecto](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

En el Asistente de servicio de nube de Azure, puede crear nuevas funciones de web y trabajador.

![Cuadro de diálogo de servicio de nube de Azure](./media/cloud-services-python-ptvs/new-service-wizard.png)

La plantilla de rol de trabajo se suministra con código reutilizable para conectarse a una cuenta de almacenamiento de Azure o Bus de servicio de Azure.

![Solución de servicio de nube](./media/cloud-services-python-ptvs/worker.png)

Puede agregar funciones web o de trabajo a un servicio de nube existente en cualquier momento.  Puede agregar proyectos existentes de la solución o cree otros nuevos.

![Agregar comando de función](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Su servicio de nube puede contener roles implementados en diferentes idiomas.  Por ejemplo, puede tener una función de web Python implementa mediante Django, Python o con funciones de trabajo de C#.  Puede comunicarse fácilmente entre las funciones con colas de Bus de servicio o almacenamiento.

## <a name="install-python-on-the-cloud-service"></a>Instalar Python en el servicio de nube

>[AZURE.WARNING] No funcionan las secuencias de comandos de configuración que se instalan con Visual Studio (en el momento en este artículo se actualizó por última vez). Esta sección describe una solución.

El problema principal con las secuencias de comandos de configuración son no instale python. En primer lugar, defina dos [tareas de inicio](cloud-services-startup-tasks.md) en el archivo [ServiceDefinition.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) . La primera tarea (**PrepPython.ps1**) descarga e instala el tiempo de ejecución de Python. La segunda tarea (**PipInstaller.ps1**) ejecuta puntos para instalar las dependencias es posible que tenga.

Las secuencias de comandos siguientes se escribieron identificación Python 3.5. Si desea usar la versión 2.x de python, establecer el archivo de variables de **PYTHON2** **en** las dos tareas de inicio y la tarea de ejecución: `<Variable name="PYTHON2" value="<mark>on</mark>" />`.


```xml
<Startup>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
  </Task>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
    
  </Task>

</Startup>
```

Las variables **PYTHON2** y **PYPATH** debe agregarse a la tarea de inicio de trabajo. Solo se utiliza la variable **PYPATH** si la variable **PYTHON2** se establece en **activado**.

```xml
<Runtime>
  <Environment>
    <Variable name="EMULATED">
      <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
    </Variable>
    <Variable name="PYTHON2" value="off" />
    <Variable name="PYPATH" value="%SystemDrive%\Python27" />
  </Environment>
  <EntryPoint>
    <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
  </EntryPoint>
</Runtime>
```

#### <a name="sample-servicedefinitioncsdef"></a>Ejemplo ServiceDefinition.csdef

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicePython" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
      <Setting name="Python2" />
    </ConfigurationSettings>
    <Startup>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="EMULATED">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
        </Variable>
        <Variable name="PYTHON2" value="off" />
        <Variable name="PYPATH" value="%SystemDrive%\Python27" />
      </Environment>
      <EntryPoint>
        <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
      </EntryPoint>
    </Runtime>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
  </WorkerRole>
</ServiceDefinition>
```



A continuación, cree los archivos **PrepPython.ps1** y **PipInstaller.ps1** de la **. / bin** carpeta de su rol.

#### <a name="preppythonps1"></a>PrepPython.ps1

Esta secuencia de comandos instala python. Si la variable de entorno **PYTHON2** se establece en **** luego Python 2.7 se instalará, en caso contrario, se instalará Python 3.5.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if python is installed...$nl"
    if ($is_python2) {
        & "${env:SystemDrive}\Python27\python.exe"  -V | Out-Null
    }
    else {
        py -V | Out-Null
    }

    if (-not $?) {

        $url = "https://www.python.org/ftp/python/3.5.2/python-3.5.2-amd64.exe"
        $outFile = "${env:TEMP}\python-3.5.2-amd64.exe"

        if ($is_python2) {
            $url = "https://www.python.org/ftp/python/2.7.12/python-2.7.12.amd64.msi"
            $outFile = "${env:TEMP}\python-2.7.12.amd64.msi"
        }
        
        Write-Output "Not found, downloading $url to $outFile$nl"
        Invoke-WebRequest $url -OutFile $outFile
        Write-Output "Installing$nl"

        if ($is_python2) {
            Start-Process msiexec.exe -ArgumentList "/q", "/i", "$outFile", "ALLUSERS=1" -Wait
        }
        else {
            Start-Process "$outFile" -ArgumentList "/quiet", "InstallAllUsers=1" -Wait
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Already installed"
    }
}
```

#### <a name="pipinstallerps1"></a>PipInstaller.ps1

Este script llamadas de puntos e instala todas las dependencias en el archivo **requirements.txt** . Si la variable de entorno **PYTHON2** se establece como se usan **en** luego Python 2.7, en caso contrario, se usará Python 3.5.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if requirements.txt exists$nl"
    if (Test-Path ..\requirements.txt) {
        Write-Output "Found. Processing pip$nl"

        if ($is_python2) {
            & "${env:SystemDrive}\Python27\python.exe" -m pip install -r ..\requirements.txt
        }
        else {
            py -m pip install -r ..\requirements.txt
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Not found$nl"
    }
}
```

#### <a name="modify-launchworkerps1"></a>Modificar LaunchWorker.ps1

>[AZURE.NOTE] En el caso de un proyecto de la **función de trabajador** **LauncherWorker.ps1** archivo es necesario para ejecutar el archivo de inicio. En un proyecto de la **función de web** , el archivo de inicio se define en las propiedades del proyecto.

La **bin\LaunchWorker.ps1** se creó originalmente para realizar una gran cantidad de trabajo de preparación pero realmente no funciona. Reemplace el contenido de ese archivo con la siguiente secuencia de comandos.

Esta secuencia de comandos llama al archivo de **worker.py** de su proyecto python. Si la variable de entorno **PYTHON2** se establece como se usan **en** luego Python 2.7, en caso contrario, se usará Python 3.5.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated)
{
    Write-Output "Running worker.py$nl"

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
else
{
    Write-Output "Running (EMULATED) worker.py$nl"

    # Customize to your local dev environment

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
```

#### <a name="pscmd"></a>PS.cmd

Las plantillas de Visual Studio deben haber creado un archivo **ps.cmd** en la **. / bin** carpeta. Esta secuencia de comandos de shell indica las secuencias de comandos de PowerShell contenedor anteriores y proporciona un registro basado en el nombre del contenedor de PowerShell denominado. Si este archivo no se ha creado, aquí es lo que debe ser en él. 

```bat
@echo off

cd /D %~dp0

if not exist "%DiagnosticStore%\LogFiles" mkdir "%DiagnosticStore%\LogFiles"
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Unrestricted -File %* >> "%DiagnosticStore%\LogFiles\%~n1.txt" 2>> "%DiagnosticStore%\LogFiles\%~n1.err.txt"
```



## <a name="run-locally"></a>Ejecutar localmente

Si establece el proyecto de servicio de nube como proyecto de inicio y presione F5, el servicio de nube se ejecutará en el emulador Azure local.

Aunque PTVS es compatible con inicio en el emulador, depuración (por ejemplo, los puntos de interrupción) no funcionarán.

Para depurar los roles de web y trabajador, puede establecer el proyecto de rol como proyecto de inicio y depurar en su lugar.  También puede establecer varios proyectos de inicio.  Haga clic en la solución y, a continuación, seleccione **Establecer proyectos de inicio**.

![Propiedades del proyecto de inicio de solución](./media/cloud-services-python-ptvs/startup.png)

## <a name="publish-to-azure"></a>Publicar en Azure

Para publicar, haga clic en el proyecto de servicio de nube de la solución y, a continuación, seleccione **Publicar**.

![Microsoft Azure publicar de inicio de sesión](./media/cloud-services-python-ptvs/publish-sign-in.png)

Siga al asistente. Si necesita, habilite Escritorio remoto. Escritorio remoto es útil cuando necesita depurar algo.

Cuando haya terminado de configurar las opciones, haga clic en **Publicar**.

Algunos progreso aparecerá en la ventana de resultados y luego verá la ventana de registro de actividad de Microsoft Azure.

![Ventana de registro de actividad de Microsoft Azure](./media/cloud-services-python-ptvs/publish-activity-log.png)

Implementación puede tardar varios minutos en completarse y, a continuación, los roles de web o de trabajo se ejecutará en Azure!

### <a name="investigate-logs"></a>Investigar registros

Después de la máquina virtual de servicio de nube se inicia y se instala Python, puede buscar los registros para buscar mensajes de error. Estos registros se encuentran en la **C:\Resources\Directory\\\LogFiles {función}** carpeta. **PrepPython.err.txt** tendrá al menos un error en ella desde cuando el script intenta detectar si está instalado Python y puede presentar una queja **PipInstaller.err.txt** sobre una versión antigua de puntos.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo trabajar con los roles de web y trabajador en herramientas de Python para Visual Studio, consulte la documentación de PTVS:

- [Proyectos de servicio de nube][]

Para obtener más detalles sobre el uso de servicios de Azure los roles de web y trabajador, como el uso de almacenamiento de Azure o Bus de servicio, consulte los artículos siguientes.

- [Servicio de blobs][]
- [Servicio de tabla][]
- [Servicio de cola][]
- [Colas de Bus de servicio][]
- [Temas de Bus de servicio][]


<!--Link references-->

[¿Qué es un servicio de nube?]: cloud-services-choose-me.md
[execution model-web sites]: ../app-service-web/app-service-web-overview.md
[execution model-vms]: ../virtual-machines/virtual-machines-windows-about.md
[execution model-cloud services]: cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[Servicio de blobs]: ../storage/storage-python-how-to-use-blob-storage.md
[Servicio de cola]: ../storage/storage-python-how-to-use-queue-storage.md
[Servicio de tabla]: ../storage/storage-python-how-to-use-table-storage.md
[Colas de Bus de servicio]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md
[Temas de Bus de servicio]: ../service-bus-messaging/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python Tools para Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: http://aka.ms/ptvsdocs
[Proyectos de servicio de nube]: http://go.microsoft.com/fwlink/?LinkId=624028
[Herramientas del SDK Azure para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Herramientas del SDK Azure VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32 bits]: https://www.python.org/downloads/
[Python 3.5 32 bits]: https://www.python.org/downloads/
