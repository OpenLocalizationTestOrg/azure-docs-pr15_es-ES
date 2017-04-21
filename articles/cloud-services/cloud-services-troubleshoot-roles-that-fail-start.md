<properties
   pageTitle="Solucionar problemas de funciones que no se inicie | Microsoft Azure"
   description="Estas son algunas de las razones por qué puede fallar una función de servicio de nube para empezar. También se ofrecen soluciones para estos problemas."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Solucionar problemas de los roles de servicio de nube que no se inicie

Estos son algunos problemas comunes y soluciones relacionados con los servicios de nube de Azure roles que no se inicie.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>DLL faltantes o dependencias

No responde funciones y funciones que se ciclo entre **Inicializando**, **ocupado**y **Detener** Estados pueden deberse a archivos DLL que faltan o ensamblados.

Síntomas de falta dll o ensamblados pueden ser:

- La instancia de rol es recorrer **Inicializando**, **ocupado**y **Detener** Estados.
- La instancia de la función se ha movido a **Listo** , pero si se desplaza a la aplicación web, la página no aparece.

Hay varios métodos recomendados para investigar estos problemas.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnosticar problemas DLL que faltan en una función web

Cuando vaya a un sitio Web que se implementa en un sitio web rol y el explorador muestra un error de servidor similar al siguiente, puede indicar que falta un archivo DLL.

![Error de servidor en la aplicación '/'.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnosticar problemas desactivando errores personalizados

Información de error más completa puede verse mediante la configuración de web.config para la función de web para establecer el modo de error personalizados en desactivado y volver a implementar el servicio.

Para ver los errores más completos sin usar Escritorio remoto:

1. Abra la solución en Microsoft Visual Studio.

2. En el **Explorador de soluciones**, busque el archivo web.config y abrirlo.

3. En el archivo web.config, busque la sección system.web y agregue la línea siguiente:

    ```xml
    <customErrors mode="Off" />
    ```

4. Guarde el archivo.

5. Empaquetar y volver a implementar el servicio.

Una vez que se vuelve a instalar el servicio, verá un mensaje de error con el nombre del ensamblado o DLL que falta.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnosticar problemas viendo el error de forma remota

Puede usar Escritorio remoto para tener acceso a la función y ver información de error más completa de forma remota. Para ver los errores mediante Escritorio remoto, siga estos pasos:

1. Asegúrese de que está instalado Azure SDK 1.3 o posterior.

2. Durante la implementación de la solución mediante Visual Studio, elija "Configurar conexiones a Escritorio remoto...". Para obtener más información sobre cómo configurar la conexión a Escritorio remoto, vea [Uso de escritorio remoto con las funciones de Azure](../vs-azure-tools-remote-desktop-roles.md).

3. En el portal de clásico de Microsoft Azure, una vez que la instancia muestra el estado de **Listo**, haga clic en una de las instancias de la función.

4. Haga clic en el icono de **Conectar** en el área de **Acceso remoto** de la cinta de opciones.

5. Inicie sesión con las credenciales que se especificaron durante la configuración de escritorio remoto en la máquina virtual.

6. Abra una ventana de comandos.

7. Tipo de `IPconfig`.

8. Anote el valor de dirección IPV4.

9. Abra Internet Explorer.

10. Escriba la dirección y el nombre de la aplicación web. Por ejemplo, `http://<IPV4 Address>/default.aspx`.

Navegar al sitio Web devolverá más explícitos mensajes de error:

* Error de servidor en la aplicación '/'.

* Descripción: Se ha producido una excepción no controlada durante la ejecución de la solicitud web actual. Revise el seguimiento de pila para obtener más información sobre el error y donde se ha originado en el código.

* Detalles de excepción: System.IO.FIleNotFoundException: no se puede cargar el archivo o ensamblado ' Microsoft.WindowsAzure.StorageClient, versión = 1.1.0.0, referencia cultural = neutral, PublicKeyToken = 31bf856ad364e35' o uno de sus dependencias. El sistema no encuentra el archivo especificado.

Por ejemplo:

![Error de servidor explícito en la aplicación '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnóstico de problemas de uso del cálculo

Puede usar el emulador de cálculo de Microsoft Azure para diagnosticar y solucionar problemas de que faltan las dependencias y los errores de web.config.

Para obtener mejores resultados con este método de diagnóstico, debe usar un equipo o máquina virtual que tiene una instalación limpia de Windows. Para simular mejor el entorno de Azure, use Windows Server 2008 R2 x64.

1. Instalar la versión independiente de [Azure SDK](https://azure.microsoft.com/downloads/).

2. En el equipo de desarrollo, cree el proyecto de servicio de nube.

3. En el Explorador de Windows, vaya a la carpeta bin\debug del proyecto de servicio de nube.

4. Copie el archivo de carpeta y .cscfg de .csx en el equipo que va a usar para depurar los problemas.

5. En la máquina, abra una ventana del símbolo de Azure SDK y tipo `csrun.exe /devstore:start`.

6. En el símbolo del sistema, escriba `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.

7. Cuando se inicia el rol, verá la información detallada del error en Internet Explorer. También puede usar herramientas de solución de problemas estándar de Windows para diagnosticar aún más el problema.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnosticar problemas mediante IntelliTrace

Para el trabajo y las funciones web que usan .NET Framework 4, puede usar [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx), que está disponible en [Microsoft Visual Studio Ultimate](https://www.visualstudio.com/products/visual-studio-ultimate-with-MSDN-vs).

Siga estos pasos para implementar el servicio con IntelliTrace habilitado:

1. Confirme que está instalado Azure SDK 1.3 o posterior.

2. Implemente la solución mediante Visual Studio. Durante la implementación, active la casilla de verificación **Habilitar IntelliTrace para las funciones de .NET 4** .

3. Una vez que se inicia la instancia, abra el **Explorador de servidores**.

4. Expanda la **Azure\\servicios de nube** nodo y busque la implementación.

5. Expanda la implementación hasta que vea las instancias de la función. Haga clic en una de las instancias.

6. Elija **vista IntelliTrace registros**. Se abrirá el **Resumen de IntelliTrace** .

7. Busque la sección excepciones de resumen. Si hay excepciones, la sección tendrá la etiqueta **Datos de excepción**.

8. Expanda los **Datos de excepción** y busque errores **System.IO.FileNotFoundException** similares al siguiente:

![Datos de excepción, archivo que falta o ensamblado](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Falta dll y ensamblados de direcciones

Para solucionar errores de ensamblado y DLL que faltan, siga estos pasos:

1. Abra la solución en Visual Studio.

2. En el **Explorador de soluciones**, abra la carpeta de **referencias** .

3. Haga clic en el ensamblado identificado en el error.

4. En el panel de **Propiedades** , busque la **propiedad copia Local** y establezca el valor **true**.

5. Volver a implementar el servicio de nube.

Una vez que haya comprobado que se han corregido todos los errores, puede implementar el servicio sin marcando la casilla de verificación **Habilitar IntelliTrace para las funciones de .NET 4** .

## <a name="next-steps"></a>Pasos siguientes

Ver más [artículos de solución de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) de servicios en la nube.

Para obtener información sobre cómo solucionar problemas de rol del servicio de nube con los datos de diagnósticos de Azure PaaS equipo, consulte [serie de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
