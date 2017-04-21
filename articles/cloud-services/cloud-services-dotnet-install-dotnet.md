<properties
   pageTitle="Instalar .NET en una función de servicio de nube | Microsoft Azure"
   description="En este artículo se describe cómo instalar manualmente .NET framework en nube servicio Web y las funciones de trabajo"
   services="cloud-services"
   documentationCenter=".net"
   authors="thraka"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/10/2016"
   ms.author="adegeo"/>

# <a name="install-net-on-a-cloud-service-role"></a>Instalar .NET en una función de servicio de nube 

En este artículo se describe cómo instalar .NET framework en nube servicio Web y las funciones de trabajo. Puede utilizar estos pasos para instalar .NET 4.6.1 en 4 de familia de Azure invitado OS. Para obtener la información más reciente en el sistema operativo invitado versiones vea [Azure invitado OS libera y matriz de compatibilidad de SDK](cloud-services-guestos-update-matrix.md).

El proceso de instalación .NET en los roles de web y trabajador implica incluido el paquete de instalación de .NET como parte de su proyecto de nube e iniciar el programa de instalación como parte de las tareas de inicio de la función.  

## <a name="add-the-net-installer-to-your-project"></a>Agregar al instalador de .NET a su proyecto
- Descargar el instalador de web para .NET framework que desea instalar
    - [.NET 4.6.1 web Installer](http://go.microsoft.com/fwlink/?LinkId=671729)
- Para una función de Web
  1. En el **Explorador de soluciones**, bajo en **Roles** en el proyecto de servicio de nube derecho haga clic en su función y seleccione **Agregar > nueva carpeta**. Cree una carpeta denominada *Papelera*
  2. Haga clic con el botón secundario en la carpeta **bin** y seleccione **Agregar > elemento existente**. Seleccione al instalador de .NET y agregar a la carpeta de la Papelera.
- Para una función de trabajo
  1. Haga clic con el botón secundario en el rol y seleccione **Agregar > elemento existente**. Seleccione al instalador de .NET y agregar a la función. 

Agregar archivos esta forma a la carpeta de contenido de la función automáticamente se agrega para el paquete de servicio de nube y se implementa en una ubicación coherente en la máquina virtual. Repita este proceso para todos los roles de web y trabajador en el servicio de nube para que todos los roles tienen una copia del instalador.

> [AZURE.NOTE] Debe instalar .NET 4.6.1 del rol del servicio de nube incluso si la aplicación está destinada a .NET 4.6. El sistema operativo invitado de Azure incluye actualizaciones [3098779](https://support.microsoft.com/kb/3098779) y [3097997](https://support.microsoft.com/kb/3097997). Instalar .NET 4.6 encima de estas actualizaciones puede causar problemas cuando se ejecuta las aplicaciones. NET, por lo que debe instalar directamente .NET 4.6.1 en lugar de .NET 4.6. Para obtener más información, vea [KB 3118750](https://support.microsoft.com/kb/3118750).

![Contenido de la función con los archivos de programa de instalación][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definir tareas de inicio para los roles
Tareas de inicio le permiten realizar operaciones antes de una función. Instalar .NET Framework como parte de la tarea de inicio se asegurará de que el marco de trabajo está instalado antes de ejecutar el código de la aplicación. Para obtener más información sobre el inicio de tareas, vea: [Ejecutar las tareas de inicio en Azure](cloud-services-startup-tasks.md). 

1. El archivo *ServiceDefinition.csdef* bajo el nodo **WebRole** o **WorkerRole** para todos los roles, agregue lo siguiente:
    
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```

    La configuración anterior ejecutará el comando de consola *install.cmd* con privilegios de administrador para que pueda instalar .NET framework. La configuración también crea un LocalStorage con el nombre *NETFXInstall*. La secuencia de comandos de inicio establecerá la carpeta temp para utilizar este recurso de almacenamiento local para que el instalador de .NET framework se descargará y se instala desde este recurso. Es importante establecer el tamaño de este recurso al menos 1024 MB para asegurarse de que el marco de trabajo se instalará correctamente. Para obtener más información acerca del inicio de tareas, vea: [las tareas de inicio de servicio de nube comunes](cloud-services-startup-tasks-common.md) 

2. Crear un archivo **install.cmd** y agréguelo a todas las funciones derecha, haga clic en la función y seleccione **Agregar > elemento existente...**. Para que todos los roles ahora deberían tener el archivo del instalador. NET, así como el archivo install.cmd.
    
    ![Contenido de la función con todos los archivos][2]

    > [AZURE.NOTE] Use un editor de textos como el Bloc de notas para crear este archivo. Si usa Visual Studio para crear un archivo de texto y, a continuación, cambiarle el nombre a 'cmd' el archivo puede contener todavía una marca de orden de bytes UTF-8 y ejecuta la primera línea de la secuencia de comandos se producirá un error. Si desea utilizar Visual Studio para crear la dejar de archivo agregar un REM (comentario) a la primera línea del archivo para que se omite al ejecutar. 

3. Agregue el siguiente script al archivo **install.cmd** :

    ```
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    set netfx="NDP461"
    
    REM ***** Set script start timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    set "log=install.cmd started %timestamp%."
    
    REM ***** Exit script if running in Emulator *****
    if %ComputeEmulatorRunning%=="true" goto exit
    
    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%
    
    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP462" goto NDP462
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
        set "netfxinstallfile=NDP452-KB2901954-Web.exe"
        set netfxregkey="0x5cbf5"
        goto logtimestamp
    
    :NDP46
    set "netfxinstallfile=NDP46-KB3045560-Web.exe"
    set netfxregkey="0x6004f"
    goto logtimestamp
    
    :NDP461
    set "netfxinstallfile=NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6040e"
    goto logtimestamp
    
    :NDP462
    set "netfxinstallfile=NDP462-KB3151802-Web.exe"
    set netfxregkey="0x60632"
    
    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
    echo %log% >> %startuptasklog%
    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%
    
    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    set /A netfxregkeydecimal=%netfxregkey%
    set foundkey=0
    FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
    echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
    if %foundkey% GEQ %netfxregkeydecimal% goto installed
    
    REM ***** Installing .NET *****
    echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
    if %ERRORLEVEL%== 0 goto installed
        echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%   
        if %ERRORLEVEL%== 3010 goto restart
        if %ERRORLEVEL%== 1641 goto restart
        echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
    
    :restart
    echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
    EXIT /B %ERRORLEVEL%
    
    :installed
    echo .NET (%netfx%) is installed >> %startuptasklog%
    
    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
    
    :exit
    EXIT /B 0
    ```
        
    La secuencia de comandos de instalación comprueba si la versión de .NET framework especificada ya está instalada en el equipo consultando el registro. Si no está instalada la versión .NET, .net instalador Web inicia. Para ayudar a solucionar los problemas de la secuencia de comandos registrará todas las actividades en un archivo denominado *startuptasklog-(currentdatetime) .txt* almacenado en *InstallLogs* local.

    > [AZURE.NOTE] La secuencia de comandos aún muestra cómo instalar .NET 4.5.2 o .NET 4.6 para continuidad. No es necesario instalar manualmente .NET 4.5.2 que ya esté disponible en el sistema operativo invitado de Azure. En lugar de instalar .NET 4.6 deben instalar directamente .NET 4.6.1 debido a [3118750 KB](https://support.microsoft.com/kb/3118750).
      

## <a name="configure-diagnostics-to-transfer-the-startup-task-logs-to-blob-storage"></a>Configurar la característica de diagnóstico para transferir los registros de tareas de inicio para el almacenamiento de blobs 
Puede configurar diagnósticos de Azure para transferir los archivos de registro generados por el script de inicio o el instalador de .NET con el almacenamiento de blobs para simplificar la resolución de problemas de instalación. Con este enfoque puede ver los registros de descargar los archivos de registro de almacenamiento de blobs simplemente en lugar de tener a Escritorio remoto en el rol.

Para configurar diagnósticos abrir la *diagnostics.wadcfgx* y agregue lo siguiente bajo el nodo **directorios** : 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Esto configurará diagnóstico de azure para transferir todos los archivos en el directorio del *registro* en el recurso *NETFXInstall* a la cuenta de almacenamiento de diagnósticos en el contenedor de blobs de *netfx instalar* .

## <a name="deploying-your-service"></a>Implementar el servicio 
Cuando se implementa el servicio de las tareas de inicio ejecutará e instalará .NET framework si ya no está instalado. Las funciones estarán en el estado de disponibilidad mientras el marco de trabajo está configurado para instalar e incluso puede reiniciar si lo requiere la instalación de framework. 

## <a name="additional-resources"></a>Recursos adicionales

- [Instalar .NET Framework][]
- [Cómo: determinar qué versiones de .NET Framework están instalados][]
- [Solucionar problemas de instalación de .NET Framework][]

[Cómo: determinar qué versiones de .NET Framework están instalados]: https://msdn.microsoft.com/library/hh925568.aspx
[Instalar .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Solucionar problemas de instalación de .NET Framework]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png

 
