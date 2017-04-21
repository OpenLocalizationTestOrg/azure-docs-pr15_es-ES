<properties
   pageTitle="Solución de errores de cliente Docker en Windows con Visual Studio | Microsoft Azure"
   description="Solucionar problemas al usar Visual Studio para crear e implementar aplicaciones web en Docker en Windows con Visual Studio."
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="allclark" />

# <a name="troubleshooting-visual-studio-docker-development"></a>Solución de problemas de desarrollo de Visual Studio Docker

Al trabajar con Visual Studio Tools para Docker Preview, puede encontrar algunos problemas debido a la naturaleza de vista previa.
Los siguientes son algunos problemas y soluciones comunes.


## <a name="unable-to-validate-volume-mapping"></a>No se pudo validar la asignación de volumen
Asignación de volumen es necesario para compartir el código fuente y binarios de la aplicación con la carpeta de la aplicación en el contenedor.  Asignaciones de volumen específico dentro de los archivos docker compose.dev.debug.yml y docker compose.dev.release.yml. Mientras se cambian archivos en su equipo host, los contenedores reflejan estos cambios en una estructura de carpeta similar.

Para habilitar la asignación de volumen, abra **configuración...** desde el icono de bandeja "moby" Docker para Windows y, a continuación, seleccione la ficha **Compartido unidades** .  Asegúrese de que la letra que hospeda el proyecto, así como la letra de la unidad donde reside % PERFILUSUARIO % se comparten protegerlos y, a continuación, haciendo clic en **Aplicar**.

Para probar si está funcionando asignación de volumen, una vez que se han compartido las unidades, recompilar y F5 desde dentro de Visual Studio o intente lo siguiente desde un comando preguntar:

*En el símbolo del sistema de Windows*

*[Nota: se supone que se encuentra la carpeta de usuarios en la unidad "C" y que se ha compartido.  Actualizar según sea necesario si comparten otra unidad]*
```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*En el contenedor de Linux*

```
/ # ls
```

Verá una lista de directorios de la carpeta usuarios públicos.
Si no se muestran archivos y la carpeta /c/Users/Public no está vacía, la asignación de volumen no está configurado correctamente. 

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Cambiar en el directorio de túnel para ver el contenido de la `/c/Users/Public` directorio:

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

**Nota:** *Cuando trabaje con máquinas virtuales de Linux, el sistema de archivos de contenedor distingue mayúsculas de minúsculas.*

##<a name="build--prepareforbuild-task-failed-unexpectedly"></a>Generar: Tarea "PrepareForBuild" Error inesperado.

Microsoft.DotNet.Docker.CommandLine.ClientException: Error al intentar conectarse:

Compruebe que se está ejecutando el host de docker predeterminado. Abra un símbolo del sistema y ejecute:

```
docker info
```

Si devuelve un error, a continuación, intente iniciar la aplicación de escritorio de **Docker para Windows** .  Si la aplicación de escritorio está ejecutando, a continuación, el icono de **moby** en la bandeja debe estar visible. Haga clic con el botón secundario en el icono de la bandeja y abra **la configuración**.  Haga clic en la pestaña **Restablecer** y, a continuación, **reinicie Docker..**.

##<a name="manually-upgrading-from-version-031-to-040"></a>Actualizar manualmente desde la versión 0.31 a 0,40


1. Copia de seguridad del proyecto
1. Elimine los archivos siguientes en el proyecto:

    ```
      Dockerfile
      Dockerfile.debug
      DockerTask.ps1
      docker-compose-yml
      docker-compose.debug.yml
      .dockerignore
      Properties\Docker.props
      Properties\Docker.targets
    ```

1. Cierre la solución y quite las siguientes líneas del archivo .xproj:

    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```

1. Vuelva a abrir la solución
1. Quite las siguientes líneas del archivo de Properties\launchSettings.json:

    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```

1. Quitar los archivos siguientes relacionados con Docker de project.json en la publishOptions:

    ```
    "publishOptions": {
      "include": [
        ...
        "docker-compose.yml",
        "docker-compose.debug.yml",
        "Dockerfile.debug",
        "Dockerfile",
        ".dockerignore"
      ]
    },
    ```

1. Desinstale la versión anterior e instalar herramientas Docker 0,40 y, a continuación, **Agregar -> soporte técnico de Docker** de nuevo en el menú contextual para la aplicación de consola o Core Web de ASP.Net. Esto agregará los nuevos artefactos Docker necesarios volver a su proyecto. 

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>Un cuadro de diálogo de error se produce cuando intenta **Agregar -> soporte técnico de Docker** o depurar (F5) en una aplicación de núcleo de ASP.NET en un contenedor

En ocasiones hemos visto después de desinstalar e instalar extensiones, caché MEF (Managed capacidad de ampliación Framework) de Visual Studio puede dañado. Cuando esto ocurre puede provocar errores de varios cuadros de diálogo al agregar soporte de Docker o intenta ejecutar o depurar (F5) en la aplicación de núcleo de ASP.NET. Como solución alternativa temporal, ejecute los siguientes pasos para eliminar y regenerar la caché MEF.

1. Cierre todas las instancias de Visual Studio
1. Abrir %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\
1. Elimine las siguientes carpetas
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Abra Visual Studio
1. Volver a intentar el escenario 
