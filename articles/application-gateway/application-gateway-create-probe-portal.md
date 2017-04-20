<properties
   pageTitle="Crear un sondeo personalizada para una puerta de enlace de aplicación a través del portal | Microsoft Azure"
   description="Aprenda a crear un sondeo personalizada de puerta de enlace de aplicación a través del portal"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Crear un sondeo personalizada de puerta de enlace de aplicación a través del portal

> [AZURE.SELECTOR]
- [Portal de Azure](application-gateway-create-probe-portal.md)
- [Azure PowerShell de administrador de recursos](application-gateway-create-probe-ps.md)
- [Azure PowerShell clásica](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## <a name="scenario"></a>Escenario

El siguiente escenario pasa por crear un sondeo de estado personalizados en una puerta de enlace de aplicación existente.
El escenario, se supone que ya ha seguido los pasos para [crear una puerta de enlace de aplicación](application-gateway-create-gateway-portal.md).

## <a name="createprobe"></a>Crear el sondeo

Sondeos se configuran en un proceso de dos pasos a través del portal. El primer paso es crear el sondeo, a continuación, agregue el sondeo a la configuración de http back-end de la puerta de enlace de aplicación.

### <a name="step-1"></a>Paso 1

Vaya a http://portal.azure.com y seleccione una puerta de enlace de aplicación existente.

![Información general sobre la puerta de enlace de aplicaciones][1]

### <a name="step-2"></a>Paso 2

Haga clic en **sondeos** y haga clic en el botón **Agregar** para agregar un nuevo sondeo.

![Agregar módulo de sondeo con información rellenado][2]

### <a name="step-3"></a>Paso 3

Rellene la información necesaria para el sondeo y cuando haya finalizado, haga clic en **Aceptar**.

- **Nombre** : se trata de un nombre descriptivo para el sondeo que es accesible en el portal.
- **Host** : este es el nombre de host que se usa para el sondeo. Aplicable únicamente cuando varios sitios está configurado en puerta de enlace de aplicación, en caso contrario, use '127.0.0.1'. Esto es diferente del nombre de host de máquina virtual.
- **Ruta de acceso** : el resto de la dirección url completa para el sondeo personalizado. Una ruta válida comienza con '/'
- **(En segundos)** - ¿con qué frecuencia se ejecuta el sondeo para comprobar estado No se recomienda para establecer la parte inferior de 30 segundos.
- **Tiempo de espera (en segundos)** : la cantidad de tiempo que espera el sondeo antes de tiempo de espera. El intervalo de tiempo de espera debe ser suficientemente alto para que una llamada http pueden realizarse para asegurarse de que la página de estado de back-end está disponible.
- **Umbral de mal estado** - número de intentos de considerarse mal estado. Umbral de 0 significa que si se produce un error de comprobación de un mantenimiento back-end se determinará mal estado inmediatamente.

> [AZURE.IMPORTANT] el nombre de host no es el nombre del servidor. Este es el nombre de host virtual que se ejecuta en el servidor de aplicaciones. El sondeo se envía a http://(host name):(port from httpsetting)/urlPath

![Opciones de configuración de sondeo][3]

## <a name="add-probe-to-the-gateway"></a>Agregar sondeo a la puerta de enlace

Ahora que se ha creado el sondeo, es hora para agregarlo a la puerta de enlace. Configuración de sondeo se establece en la configuración de http back-end de la puerta de enlace de aplicación.

### <a name="step-1"></a>Paso 1

Haga clic en la **configuración de HTTP** de la puerta de enlace de la aplicación y, a continuación, haga clic en la configuración de http de back-end actual de la ventana para que aparezca el módulo de configuración.

![ventana de configuración de HTTPS][4]

### <a name="step-2"></a>Paso 2

En el módulo de configuración de **appGatewayBackEndHttp** , haga clic en **sondeo de uso personalizada** y elija el sondeo creado en la sección [crear el sondeo](#createprobe) .
Cuando haya terminado, haga clic en **Aceptar** y se aplica a la configuración.

![módulo de configuración de appgatewaybackend][5]

El sondeo de forma predeterminada comprueba el acceso predeterminado a la aplicación web. Ahora que se ha creado un sondeo personalizada, la puerta de enlace de aplicación utiliza la ruta personalizada definida para supervisar el mantenimiento para el servidor seleccionado. En función de los criterios que definió, la puerta de enlace de aplicación comprueba el archivo especificado en el sondeo. Si la llamada al host: puerto / ruta de acceso no devuelve una respuesta de estado Http 200 OK, el servidor se toma fuera de la rotación, una vez alcanzado el umbral de mal estado. Sondeo continúa en la instancia de mal estada para determinar cuándo vuelve a correcto. Una vez agregada la instancia vuelva a grupo de servidores correcto tráfico comienza que fluye en él nuevamente y sondeo a la instancia continúa en intervalo especificado de usuario como normal.


## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo configurar descarga SSL con la puerta de enlace de aplicaciones de Azure vea [Configurar la descarga de SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png