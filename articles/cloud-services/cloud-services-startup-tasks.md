<properties 
pageTitle="Ejecutar las tareas de inicio en los servicios de nube de Azure | Microsoft Azure" 
description="Tareas de inicio ayudan a preparar el entorno de servicio de nube para la aplicación. Esto le enseña cómo funcionan las tareas de inicio y cómo hacerlos" 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>



# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Cómo configurar y ejecutar las tareas de inicio de un servicio de nube

Puede usar las tareas de inicio para realizar operaciones antes de una función. Las operaciones que desea realizar incluyen instalar un componente, registrar componentes COM, configuración de claves de registro o iniciar un proceso de ejecución larga.

>[AZURE.NOTE] Tareas de inicio no son aplicables a máquinas virtuales de Windows, solo a Web de servicio de nube y las funciones de trabajo.

## <a name="how-startup-tasks-work"></a>Cómo funcionan las tareas de inicio

Tareas de inicio son acciones que se realizan antes de que los roles comienzan y se definen en el archivo [ServiceDefinition.csdef] mediante el elemento de [tarea] dentro del elemento de [Inicio] . Las tareas de inicio son archivos por lotes, pero también pueden ser aplicaciones de consola o archivos por lotes que iniciar secuencias de comandos de PowerShell.

Variables de entorno pasan información a una tarea de inicio y almacenamiento local puede utilizarse para pasar información de una tarea de inicio. Por ejemplo, una variable de entorno puede especificar la ruta de acceso a un programa que desea instalar y poder escribir archivos al almacenamiento local que puede leer más adelante por los roles.

La tarea de inicio puede registrar información y errores en el directorio especificado por la variable de entorno **TEMP** . Durante la tarea de inicio de la variable de entorno **TEMP** se resuelve en la *C:\\recursos\\temp\\[guid]. [ nombre de rol]\\RoleTemp* directorio cuando se ejecuta en la nube.

Tareas de inicio también se pueden ejecutar varias veces entre reiniciar el equipo. Por ejemplo, la tarea de inicio se ejecutará cada vez que la función recicla y función reciclados no pueden incluir siempre reiniciar el equipo. Tareas de inicio deben escribirse de forma que les permite ejecutarse varias veces sin problemas.

Tareas de inicio deben terminar con un **errorlevel** o código de salida de cero para el proceso de inicio para completar. Si una tarea de inicio termina con un cero **errorlevel**, no se iniciará el rol.


## <a name="role-startup-order"></a>Orden de inicio de la función

A continuación muestra el procedimiento de inicio de la función en Azure:

1. La instancia está marcada como **inicial** y no recibe tráfico.

2. Todas las tareas de inicio se ejecutan según su atributo **taskType** .
    - Las tareas **simples** se ejecutan sincrónicamente, uno en uno.
    - Las tareas en **segundo** **plano** y se inician asincrónica, en paralelo a la tarea de inicio.  
       
    > [AZURE.WARNING] IIS no esté completamente configurado durante la fase de tareas de inicio en el proceso de inicio para que datos específicos de la función no esté disponibles. Tareas de inicio que requieren datos específicos de la función deben usar [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).

3. Se inicia el proceso de host de la función y se crea el sitio en IIS.

4. El método [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) se denomina.

5. La instancia está marcada como **Listo** y enrutar el tráfico a la instancia.

6. El método [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) se denomina.


## <a name="example-of-a-startup-task"></a>Ejemplo de una tarea de inicio

Tareas de inicio se definen en el archivo [ServiceDefinition.csdef] , en el elemento de **tarea** . El atributo de **línea de comandos** que especifica el nombre y los parámetros del archivo por lotes de inicio o comando de la consola, el atributo **executionContext** especifica el nivel de privilegios de la tarea de inicio y el atributo **taskType** especifica cómo se ejecutará la tarea.

En este ejemplo, una variable de entorno **MyVersionNumber**, es creada para la tarea de inicio y establezca el valor "**1.0.0.0**".

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

En el ejemplo siguiente, el archivo por lotes **Startup.cmd** escribe la línea "la versión actual es 1.0.0.0" en el archivo StartupLog.txt en el directorio especificado por la variable de entorno TEMP. La `EXIT /B 0` línea se asegura de que la tarea de inicio termina con **errorlevel** cero.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [AZURE.NOTE] En Visual Studio, se debe establecer la propiedad **Copiar en el directorio de resultados** para el archivo por lotes de inicio para **Copiar siempre** para asegurarse de que el archivo por lotes de inicio esté correctamente implementado en su proyecto en Azure (**approot\\Papelera** para funciones Web y **approot** para funciones de trabajo).

## <a name="description-of-task-attributes"></a>Descripción de los atributos de la tarea

A continuación, describen los atributos del elemento de **tarea** en el archivo [ServiceDefinition.csdef] :

**línea de comandos** - especifica la línea de comandos de la tarea de inicio:

- El comando, con parámetros de línea de comandos opcionales, que comienza la tarea de inicio.
- Con frecuencia es el nombre de archivo de un archivo por lotes cmd o bat.
- La tarea está en relación con la AppRoot\\carpeta Bin para la implementación. Variables de entorno no se expanden en la determinación de la ruta de acceso y de la tarea. Si la expansión de entorno es necesaria, puede crear un script de cmd pequeña que llama a la tarea de inicio.
- Puede ser una aplicación de consola o un archivo por lotes que se inicia un [script de PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** - especifica el nivel de privilegios de la tarea de inicio. El nivel de privilegios puede ser limitado o elevado:

- **limitado**  
La tarea de inicio se ejecuta con los mismos privilegios que el rol. Cuando el atributo **executionContext** para el elemento de [tiempo de ejecución] también es **limitado**, se usan los privilegios de usuario.

- **elevados**  
La tarea de inicio se ejecuta con privilegios de administrador. Permite las tareas de inicio para instalar programas, realizar cambios de configuración de IIS, realizar cambios en el registro y otras tareas de nivel de administrador, sin aumentar el nivel de privilegios de la función propiamente dicho.  

> [AZURE.NOTE] El nivel de privilegios de una tarea de inicio no es necesario ser idénticos a la propia función.

**taskType** - especifica la forma en que se ejecuta una tarea de inicio.

- **simple**  
Las tareas se ejecutan sincrónicamente, de uno en uno, en el orden especificado en el archivo [ServiceDefinition.csdef] . Cuando una tarea de inicio **simple** termina con **errorlevel** de cero, se ejecuta la siguiente tarea **simple** de inicio. Si no hay ningún más **sencillas** las tareas de inicio para ejecutar, se iniciará la propia función.   

    > [AZURE.NOTE] Si la tarea **simple** termina con un cero **errorlevel**, se bloquearán la instancia. Las tareas posteriores **simple** de inicio y a continuación, la función, no se iniciarán.

    Para asegurarse de que el archivo por lotes termina con **errorlevel** cero, ejecute el comando `EXIT /B 0` al final de un proceso de archivo por lotes.

- **fondo**  
Las tareas se ejecutan de forma asincrónica, en paralelo con el inicio de la función.

- **primer plano**  
Las tareas se ejecutan de forma asincrónica, en paralelo con el inicio de la función. La diferencia clave entre un **plano** y una tarea en **segundo plano** es que una tarea de **primer plano** impide el rol de reciclaje o cerrando hasta que haya finalizado la tarea. Las tareas de **fondo** no tienen esta restricción.

## <a name="environment-variables"></a>Variables de entorno

Variables de entorno son una manera de pasar información a una tarea de inicio. Por ejemplo, puede poner la ruta de acceso a un objeto binario que contiene un programa de instalación, o números de puerto que se va a utilizar la función o configuración de las funciones de control de la tarea de inicio.

Existen dos tipos de variables de entorno para las tareas de inicio; las variables de entorno estático y variables de entorno en función de los miembros de la clase [RoleEnvironment] . Ambos están en la sección [entorno] del archivo [ServiceDefinition.csdef] y usan el atributo de elemento y **nombre de** [Variable] .

Variables de entorno estático utiliza el atributo de **valor** del elemento [Variable] . El ejemplo anterior, crea la variable de entorno **MyVersionNumber** que tiene un valor estático**1.0.0.0"**. Otro ejemplo sería crear una variable de entorno **StagingOrProduction** que puede establecer manualmente los valores de "**provisional**" o "**producción**" para realizar acciones de inicio diferente en función del valor de la variable de entorno **StagingOrProduction** .

Variables de entorno en función de los miembros de la clase RoleEnvironment no utilizan el atributo de **valor** del elemento [Variable] . En su lugar, el elemento secundario [RoleInstanceValue] , con el valor de atributo **XPath** correspondiente, se usan para crear una variable de entorno basada en un miembro específico de la clase [RoleEnvironment] . Valores para el atributo **XPath** tener acceso a los distintos valores de [RoleEnvironment] pueden encontrarse [aquí](cloud-services-role-config-xpath.md).



Por ejemplo, para crear una variable de entorno es "**true**" cuando se ejecuta la instancia en el cálculo emulador y "**false**" cuando se ejecuta en la nube, use los siguientes elementos de [variables] y [RoleInstanceValue] :

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
    
            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->
    
            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>
    
        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Pasos siguientes
Obtenga información sobre cómo llevar a cabo algunas [tareas comunes de inicio](cloud-services-startup-tasks-common.md) con el servicio de nube.

[Paquete de](cloud-services-model-and-package.md) su servicio de nube.  


[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Tarea]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Inicio]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Tiempo de ejecución]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Entorno]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx