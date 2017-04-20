<properties
   pageTitle="Crear una puerta de enlace de la aplicación con el servidor de aplicaciones web con el portal | Microsoft Azure"
   description="Aprenda a crear una puerta de enlace de la aplicación con el servidor de aplicaciones web a través del portal"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"
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

# <a name="create-an-application-gateway-with-web-application-firewall-by-using-the-portal"></a>Crear una puerta de enlace de la aplicación con el servidor de aplicaciones web mediante el portal

> [AZURE.SELECTOR]
- [Portal de Azure](application-gateway-web-application-firewall-portal.md)
- [Azure PowerShell de administrador de recursos](application-gateway-web-application-firewall-powershell.md)

El servidor de aplicaciones web (WAFS) en la puerta de enlace de aplicaciones de Azure protege aplicaciones web de ataques basados en web comunes como la inserción de SQL, ataques de scripting entre sitios y apropiaciones de sesión. Aplicación Web protege muchos OWASP superior 10 comunes web vulnerabilidad.

La puerta de enlace de aplicación Azure es un equilibrador de carga de nivel 7. Proporciona migración tras error, las solicitudes HTTP enrutamiento de rendimiento entre diferentes servidores, independientemente de si están en la nube o local.
Aplicación proporciona muchas características de controlador de entrega de la aplicación (ADC) incluye Equilibrio de carga HTTP, afinidad sesión basada en cookies, descargar Secure Sockets Layer (SSL), sondeos de estado personalizados, compatibilidad con varios sitios y muchas otras.
Para obtener una lista completa de características compatibles, visite [Introducción de puerta de enlace de aplicaciones](application-gateway-introduction.md)

## <a name="scenarios"></a>Escenarios

En este artículo, hay dos escenarios:

En el primer escenario, aprenderá a [Agregar el servidor de aplicaciones web a una puerta de enlace de aplicación existente](#add-web-application-firewall-to-an-existing-application-gateway).

En el segundo escenario, aprenderá a [crear una puerta de enlace de la aplicación con el servidor de aplicaciones web](#create-an-application-gateway-with-web-application-firewall)

![Ejemplo de escenario][scenario]

>[AZURE.NOTE] Configuración adicional de la puerta de enlace de aplicación, incluido el estado personalizado sondeos, las direcciones de grupo de back-end y reglas adicionales se configuran una vez configurada la puerta de enlace de la aplicación y no durante la implementación inicial.

## <a name="before-you-begin"></a>Antes de empezar

La puerta de enlace de aplicación Azure requiere su propia subred. Al crear una red virtual, asegúrese de dejar suficiente espacio de direcciones para tener varias subredes. Una vez que se implementa una puerta de enlace de aplicación a una subred, puertas de enlace de aplicación solo adicional son capaces de agregarse a la subred.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Agregar el servidor de aplicaciones web a una puerta de enlace de aplicación existente

Este escenario actualiza una puerta de enlace de aplicación existente para admitir el servidor de aplicaciones web en el modo prevención.

### <a name="step-1"></a>Paso 1

Vaya al portal de Azure, seleccione una puerta de enlace de aplicación existente.

![Creación de puerta de enlace de aplicación][1]

### <a name="step-2"></a>Paso 2

Haga clic en **configuración** y actualizar la configuración de puerta de enlace de aplicación. Cuando haya terminado, haga clic en **Guardar**

Los valores para actualizar una puerta de enlace de aplicación existente para admitir el servidor de aplicaciones web son:

- **Nivel** - el nivel seleccionado debe ser **WAFS** a servidor de aplicaciones web de soporte técnico
- **Tamaño SKU** - esta configuración es el tamaño de la puerta de enlace de la aplicación con el servidor de aplicaciones web, las opciones disponibles son (**medio** y **grande**).
- **Estado de firewall** : esta opción deshabilita o habilita el servidor de aplicaciones web.
- **Modo de firewall** : esta opción es cómo trata el tráfico malintencionado firewall de aplicación web. Modo de **detección** sólo registra los eventos, donde el modo **prevención** registra los eventos y deja el tráfico malintencionado.

![configuración básica de módulo que muestra][2]

>[AZURE.NOTE] Para ver los registros de servidor de seguridad de aplicación web, deben estar habilitados diagnósticos y ApplicationGatewayFirewallLog seleccionado. Puede elegir un recuento de la instancia de 1 con fines de pruebas. Es importante conocer que cualquier número de instancias en dos instancias no está cubierto por el SLA y, por tanto, no se recomienda. Pequeñas puertas de enlace no están disponibles cuando se usa el servidor de aplicaciones web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Crear una puerta de enlace de la aplicación con el servidor de aplicaciones web

Este escenario hará lo siguiente:

- Crear una puerta de enlace de aplicación del firewall de aplicación web medio con dos instancias.
- Crear una red virtual denominada AdatumAppGatewayVNET con un bloque CIDR reservado de 10.0.0.0/16.
- Crear una subred denominada Appgatewaysubnet que utiliza 10.0.0.0/28 como su bloque CIDR.
- Configurar un certificado para la descarga SSL.

### <a name="step-1"></a>Paso 1

Vaya al portal de Azure, haga clic en **nuevo** > **redes** > **Puerta de enlace de aplicación**

![Creación de puerta de enlace de aplicación][1-1]

### <a name="step-2"></a>Paso 2

A continuación, rellene la información básica sobre la puerta de enlace de aplicación. Asegúrese de elegir **WAFS** como nivel. Cuando haya terminado, haga clic en **Aceptar**

La información necesaria para la configuración básica es:

- **Nombre** : el nombre de la puerta de enlace de aplicación.
- **Nivel** - el nivel de la puerta de enlace de aplicación, las opciones disponibles son (**estándar** y **WAFS**). Servidor de aplicaciones Web solo está disponible en el nivel de WAFS.
- **Tamaño SKU** - esta configuración es el tamaño de la puerta de enlace de aplicación, las opciones disponibles son (**medio** y **grande**).
- **Recuento de instancia** - el número de instancias, este valor debe ser un número entre **2** y **10**.
- **Grupo de recursos** : el grupo de recursos para mantener la puerta de enlace de aplicación, puede ser un grupo de recursos existente o uno nuevo.
- **Ubicación** : la región de la puerta de enlace de aplicación, es la misma ubicación en el grupo de recursos. *La ubicación es importante como la red virtual y IP pública debe estar en la misma ubicación que la puerta de enlace*.

![configuración básica de módulo que muestra][2-2]

>[AZURE.NOTE] Puede elegir un recuento de la instancia de 1 con fines de pruebas. Es importante conocer que cualquier número de instancias en dos instancias no está cubierto por el SLA y, por tanto, no se recomienda. Pequeñas puertas de enlace no son compatibles para escenarios de servidor de seguridad de aplicaciones web.

### <a name="step-3"></a>Paso 3

Una vez que se define la configuración básica, el siguiente paso es definir una red virtual para usarse. La red virtual aloja la aplicación de la puerta de enlace de aplicación equilibrio de carga para.

Haga clic en **Elegir una red virtual** para configurar la red virtual.

![módulo con la configuración de puerta de enlace de aplicación][3]

### <a name="step-4"></a>Paso 4

En el módulo de **Elegir una red Virtual** , haga clic en **Crear nuevo**

Mientras no se explica en este escenario, puede seleccionar una red Virtual existente en este momento.  Si se utiliza una red virtual existente, es importante saber que la red virtual necesita una subred vacía o una subred sólo aplicación puerta de enlace de recursos para usarse.

![Elija el módulo de red virtual][4]

### <a name="step-5"></a>Paso 5

Rellene la información de red en el módulo de **Crear una red Virtual** como se describe en la descripción del [escenario](#scenario) anterior.

![Crear el módulo de red virtual con información especificada][5]

### <a name="step-6"></a>Paso 6

Una vez creada la red virtual, el siguiente paso es definir el IP front-end para la puerta de enlace de aplicación. En este momento, la opción está entre públicas o una dirección IP privada para el front-end. La elección depende de si la aplicación es internet internos u opuestas solo. En este escenario se supone usando una dirección IP pública. Para elegir una dirección IP privada, puede hacer clic en el botón **privada** . Se selecciona una dirección IP asignada automáticamente o puede hacer clic en la casilla de verificación **Elegir una dirección IP privada específica** para especificar una manualmente.

Haga clic en **Elegir una dirección IP pública**. Si hay una dirección IP pública existente puede seleccionarse en este momento, en este escenario se crea una nueva dirección IP pública. Haga clic en **Crear nuevo**

![Elija el módulo de dirección IP pública][6]

### <a name="step-7"></a>Paso 7

A continuación, asigne un nombre descriptivo a la dirección IP pública y haga clic en **Aceptar**

![Crear el módulo de dirección IP pública][7]

### <a name="step-8"></a>Paso 8

A continuación, configure la configuración de escucha.  Si se utiliza **http** , no quedará nada para configurar y se puede hacer clic en **Aceptar** . Para usar **https** configuración se requieren más.

Para usar **https**, se requiere un certificado. La clave privada del certificado es necesaria para exportar un .pfx de las necesidades de certificados para ofrecer y la contraseña para el archivo.

Haga clic en **HTTPS**, haga clic en el icono de **carpeta** junto al cuadro de texto **PFX cargar certificado** .
Desplácese hasta el archivo de certificado .pfx en el sistema de archivos. Una vez seleccionado, asigne un nombre descriptivo al certificado y escriba la contraseña para el archivo .pfx.

Cuando haya finalizado, haga clic en **Aceptar** para revisar la configuración de la puerta de enlace de aplicación.

![Sección de configuración de escucha en el módulo de configuración][8]

### <a name="step-9"></a>Paso 9

Configurar las opciones específicas de **WAFS** .

- **Estado de firewall** : esta opción activa o desactiva la WAFS.
- **Modo de firewall** - esta configuración determina que la WAFS acciones toma el tráfico malintencionado. Si se elige **la detección** , el tráfico sólo se registra.  Si se elige la **prevención** , el tráfico iniciado y detenido con una no autorizado 403.

![configuración del firewall de aplicación Web][9]

### <a name="step-10"></a>Paso 10

Revise la página Resumen y haga clic en **Aceptar**.  Ahora en la cola y crea la puerta de enlace de aplicación.

### <a name="step-12"></a>Paso 12

Una vez creada la puerta de enlace de aplicación, vaya a él en el portal para continuar con la configuración de la puerta de enlace de aplicación.

![Vista de recursos de puerta de enlace de aplicación][10]

Estos pasos crean una puerta de enlace de aplicación básica con la configuración predeterminada para escucha, grupo de back-end, configuración de http de back-end y reglas. Puede modificar esta configuración para adaptarla a la implementación una vez que la configuración es correcta

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo configurar el registro de diagnóstico para registrar los eventos que se hayan detectado o impide con el servidor de aplicaciones Web con una visita a [Diagnósticos de puerta de enlace de aplicaciones](application-gateway-diagnostics.md)

Aprenda a crear sondeos de estado personalizados con una visita a [crear un sondeo de estado personalizados](application-gateway-create-probe-portal.md)

Obtenga información sobre cómo configurar la descarga de SSL y tomar el descifrado SSL costoso desactivar los servidores web con una visita a [Configurar la descarga de SSL](application-gateway-ssl-portal.md)

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[1-1]: ./media/application-gateway-web-application-firewall-portal/figure1-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[4]: ./media/application-gateway-web-application-firewall-portal/figure4.png
[5]: ./media/application-gateway-web-application-firewall-portal/figure5.png
[6]: ./media/application-gateway-web-application-firewall-portal/figure6.png
[7]: ./media/application-gateway-web-application-firewall-portal/figure7.png
[8]: ./media/application-gateway-web-application-firewall-portal/figure8.png
[9]: ./media/application-gateway-web-application-firewall-portal/figure9.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png