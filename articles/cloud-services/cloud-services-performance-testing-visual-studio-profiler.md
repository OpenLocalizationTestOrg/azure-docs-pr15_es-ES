<properties 
    pageTitle="Generación de perfiles de un servicio de nube localmente en el emulador de cálculo | Microsoft Azure" 
    services="cloud-services"
    description="Investigar problemas de rendimiento en servicios de nube con el generador de perfiles de Visual Studio" 
    documentationCenter=""
    authors="TomArcher" 
    manager="douge" 
    editor=""
    tags="" 
    />

<tags 
    ms.service="cloud-services" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="07/30/2016" 
    ms.author="tarcher"/>

# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Probar el rendimiento de un servicio de nube localmente en el emulador de Azure cálculo usando al analizador de Visual Studio

Una gran variedad de herramientas y técnicas están disponibles para probar el rendimiento de servicios en la nube.
Cuando se publica un servicio de nube en Azure, puede que Visual Studio recopilar datos de perfiles y analizarla localmente, como se describe en [perfiles de una aplicación de Azure][1].
También puede usar diagnósticos para realizar un seguimiento de una gran variedad de contadores de rendimiento, como se describe en [contadores de rendimiento de uso en Azure][2].
También puede perfil de su aplicación localmente en el emulador de cálculo antes de implementarlo en la nube.

En este artículo trata sobre el método de muestreo de la CPU de perfiles, lo que puede realizar localmente en el emulador. Muestreo de la CPU es un método de generación de perfiles que no es muy intrusivo. En un intervalo de muestreo designado, el generador de perfiles toma una instantánea de la pila de llamadas. Los datos se recopila durante un período de tiempo y se muestra en un informe. Este método de generación de perfiles tiende indicar dónde en una aplicación de cálculo intensiva está realizando la mayoría del trabajo de la CPU.  Esto le ofrece la oportunidad de centrarse en la "ruta de acceso rápida" donde la aplicación invierte más tiempo.



## <a name="1-configure-visual-studio-for-profiling"></a>1: configurar Visual Studio para de perfiles

En primer lugar, hay varias opciones de configuración de Visual Studio que podrían ser útiles de generación de perfiles. Para facilitar la lectura de los informes de generación de perfiles, necesitará símbolos (archivos .pdb) para su aplicación y también símbolos para las bibliotecas del sistema. Querrá asegurarse de que hace referencia a los servidores de símbolos disponibles. Para ello, en el menú **Herramientas** de Visual Studio, elija **Opciones**y luego elija **Depurar**, a continuación, **símbolos**. Asegúrese de que los servidores de símbolos de Microsoft aparece en **ubicaciones de archivo (.pdb) de símbolo**.  También puede hacer referencia a http://referencesource.microsoft.com/symbols, que podría tener otros archivos de símbolos.

![Opciones de símbolo][4]

Si lo desea, puede simplificar los informes que genera el analizador estableciendo sólo mi código. Con sólo mi código habilitado, pilas de llamadas de función se simplifican para que las llamadas totalmente internas .NET Framework y bibliotecas están ocultas de los informes. En el menú **Herramientas** , elija **Opciones**. A continuación, expanda el nodo **Herramientas de rendimiento** y elija **General**. Seleccione la casilla de verificación **Habilitar Solo mi**código para los informes del generador de perfiles.

![Solo las opciones de mi código][17]

Puede usar estas instrucciones con un proyecto existente o con un nuevo proyecto.  Si crea un nuevo proyecto para probar las técnicas que se describen a continuación, elija un proyecto de C# **Servicio de nube de Azure** y seleccione una **Función Web** y una **Función de trabajo**.

![Funciones de proyecto de servicio de nube de Azure][5]

Por ejemplo, efectos, agregar código a un proyecto que tiene una gran cantidad de tiempo y muestra algún problema de rendimiento obvios. Por ejemplo, agregue el siguiente código a un proyecto de la función de trabajo:

    public class Concatenator
    {
        public static string Concatenate(int number)
        {
            int count;
            string s = "";
            for (count = 0; count < number; count++)
            {
                s += "\n" + count.ToString();
            }
            return s;
        }
    }

Llamar a este código desde el método de RunAsync en la clase derivada de RoleEntryPoint de la función trabajo. (Omitir la advertencia sobre el método ejecutarse de forma asincrónica).

        private async Task RunAsync(CancellationToken cancellationToken)
        {
            // TODO: Replace the following with your own logic.
            while (!cancellationToken.IsCancellationRequested)
            {
                Trace.TraceInformation("Working");
                Concatenator.Concatenate(10000);
            }
        }

Crear y ejecutar el servicio de nube localmente sin depurar (Ctrl + F5), con la configuración de solución establecida en **liberar**. Esto garantiza que todos los archivos y carpetas se crean para ejecutar la aplicación localmente y garantiza que todos los emuladores se han iniciado. Iniciar la interfaz de usuario de emulador calcular desde la barra de tareas para comprobar que su rol de trabajo se está ejecutando.

## <a name="2-attach-to-a-process"></a>2: asociar a un proceso

En lugar de perfiles de la aplicación iniciando desde el IDE de Visual Studio 2010, debe asociar el generador de perfiles a un proceso en ejecución. 

Para adjuntar el generador de perfiles a un proceso, en el menú **analizar** , elija **Analizador** y **Asociar o desasociar**.

![Adjuntar la opción de perfil][6]

Para una función de trabajo, busque el proceso WaWorkerHost.exe.

![Proceso de WaWorkerHost][7]

Si la carpeta del proyecto está en una unidad de red, el generador de perfiles le pedirá que proporcione otra ubicación para guardar los informes de generación de perfiles.

 También puede adjuntar a un rol de web adjuntando a WaIISHost.exe.
Si hay varios procesos de rol de trabajo en la aplicación, debe usar el ID para distinguirlos. Puede consultar el ID mediante programación obtener acceso al objeto de proceso. Por ejemplo, si agrega este código al método de ejecución de la clase derivada de RoleEntryPoint en una función, puede mirar el registro de la interfaz de usuario de emulador calcular saber qué proceso para conectarse a.

    var process = System.Diagnostics.Process.GetCurrentProcess();
    var message = String.Format("Process ID: {0}", process.Id);
    Trace.WriteLine(message, "Information");

Para ver el registro, inicie la interfaz de usuario de emulador calcular.

![Iniciar la interfaz de usuario de cálculo emulador][8]

Abra la ventana de consola de registro de rol de trabajo en la interfaz de usuario de emulador calcular haciendo clic en la barra de título de la ventana de consola. Puede ver el identificador del proceso en el registro.

![Ver el ID de proceso][9]

Uno que ha adjuntado, realice los pasos de la interfaz de usuario de la aplicación (si es necesario) para reproducir el escenario.

Cuando desee detener perfil, seleccione el vínculo **Dejar de perfiles** .

![Detener la opción de generación de perfiles][10]

## <a name="3-view-performance-reports"></a>3: ver informes de rendimiento

Se muestra el informe de rendimiento de la aplicación.

En este momento, el generador de perfiles dejará de ejecutarse, guarda los datos en un archivo .vsp y muestra un informe que muestra un análisis de datos.

![Informe del generador de perfiles][11]


Si ve String.wstrcpy en la ruta de acceso rápido, haga clic en sólo mi código para cambiar la vista para mostrar solo el código de usuario.  Si ve String.Concat, pruebe a presionar el botón Mostrar todo el código.

Vea el método concatenar y String.Concat ocupar una gran parte del tiempo de ejecución.

![Análisis de informe][12]

Si agrega el código de concatenación de cadenas en este artículo, verá una advertencia en la lista de tareas para este. También puede ver una advertencia que hay una cantidad excesiva de recolección, que es debido a la cantidad de cadenas que se crean y se eliminan.

![Advertencias de rendimiento][14]

## <a name="4-make-changes-and-compare-performance"></a>4: realice cambios y comparar el rendimiento

También puede comparar el rendimiento antes y después de un cambio de código.  Detener el proceso en ejecución y editar el código para reemplazar la operación de concatenación de cadenas con el uso de StringBuilder:

    public static string Concatenate(int number)
    {
        int count;
        System.Text.StringBuilder builder = new System.Text.StringBuilder("");
        for (count = 0; count < number; count++)
        {
             builder.Append("\n" + count.ToString());
        }
        return builder.ToString();
    }

Realice otra de rendimiento y, a continuación, comparar el rendimiento. En el Explorador de rendimiento, si se ejecuta el que se encuentra en la misma sesión, puede simplemente seleccionar ambos informes, abra el menú contextual y elija **Comparar informes de rendimiento**. Si desea comparar con una ejecución en otra sesión de rendimiento, abra el menú **analizar** y elija **Comparar informes de rendimiento**. Especificar ambos archivos en el cuadro de diálogo que aparece.

![Comparar la opción de informes de rendimiento][15]

Los informes de resaltan las diferencias entre las dos ejecuciones.

![Informe de comparación][16]

¡Felicidades! Ha disminuido comenzó con el generador de perfiles.

## <a name="troubleshooting"></a>Solución de problemas

- Asegúrese de que son una versión de lanzamiento de generación de perfiles e iniciar sin depurar.

- Si la opción asociar o desasociar no está habilitada en el menú de analizador, ejecute al Asistente de rendimiento.

- Usar la interfaz de usuario de emulador calcular para ver el estado de la aplicación. 

- Si tiene problemas al iniciar aplicaciones en el emulador o adjuntando el generador de perfiles, cierre hacia abajo el emulador de cálculo y reinícielo. Si no se soluciona el problema, intente reiniciar. Este problema puede ocurrir si se usarlo calcular para suspender y quitar implementaciones de ejecución.

- Si ha utilizado cualquiera de los comandos de generación de perfiles de la línea de comandos, especialmente la configuración global, asegúrese de que se ha llamado VSPerfClrEnv /globaloff y que se ha cerrado VsPerfMon.exe.

- Si al realizar muestreos, verá el mensaje "PRF0025: datos no recopilados," Compruebe que el proceso ha adjuntado a tiene actividad de la CPU. Aplicaciones que no se realiza ningún cálculo trabajo no pueden producir los datos de muestra.  También es posible que el proceso ha terminado antes de realiza cualquier muestreo. Compruebe que el método de ejecución de una función que genera perfiles no termina.

## <a name="next-steps"></a>Pasos siguientes

No se admite la instrumentación Azure binarios en el emulador en el generador de perfiles de Visual Studio, pero si desea probar la asignación de memoria, puede elegir esta opción cuando perfiles. También puede elegir simultaneidad perfiles, que le ayudará a determinar la subprocesos perder tiempo competir para bloqueos o la generación de perfiles de interacción de capas, lo que ayuda a realizar un seguimiento de problemas de rendimiento cuando la interacción entre los niveles de una aplicación, con más frecuencia entre el nivel de datos y una función de trabajo.  Puede ver las consultas de base de datos que genera la aplicación y utilizar los datos de perfiles para mejorar el uso de la base de datos. Para obtener información sobre la generación de perfiles de interacción de capas, consulte la entrada de blog [Tutorial: usar el generador de perfiles de interacción de nivel en Visual Studio Team System 2010][3].



[1]: http://msdn.microsoft.com/library/azure/hh369930.aspx
[2]: http://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
 