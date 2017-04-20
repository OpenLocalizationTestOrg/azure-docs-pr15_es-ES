<properties
   pageTitle="Configurar una puerta de enlace de la aplicación de descarga SSL a través del portal | Microsoft Azure"
   description="Esta página contiene instrucciones para crear una puerta de enlace de la aplicación con SSL de descarga a través del portal"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-portal"></a>Configurar una puerta de enlace de la aplicación de descarga SSL a través del portal

> [AZURE.SELECTOR]
-[Portal de Azure](application-gateway-ssl-portal.md)
-[PowerShell del Administrador de recursos de Azure](application-gateway-ssl-arm.md)
-[PowerShell clásico de Azure](application-gateway-ssl.md)

La puerta de enlace de aplicación Azure se puede configurar para finalizar la sesión de Secure Sockets Layer (SSL) de la puerta de enlace para evitar costosas tareas de descifrado SSL para que se producen en el conjunto de servidores web. Descarga SSL también simplifica la configuración del servidor front-end y la administración de la aplicación web.

## <a name="scenario"></a>Escenario

El siguiente escenario atraviesa configurar SSL de descarga de una puerta de enlace de aplicación existente. El escenario, se supone que ya ha seguido los pasos para [crear una puerta de enlace de aplicación](application-gateway-create-gateway-portal.md).

## <a name="before-you-begin"></a>Antes de empezar

Para configurar la descarga SSL con una puerta de enlace de aplicación, se requiere un certificado. Este certificado se carga en la puerta de enlace de la aplicación y se utiliza para cifrar y descifrar el tráfico enviado a través de SSL. El certificado debe estar en formato de intercambio de información Personal (pfx). Este formato de archivo permite para que la clave privada que se pueden exportar para realizar el cifrado y descifrado de tráfico, se requiere la puerta de enlace de aplicación.

## <a name="add-an-https-listener"></a>Agregar una escucha HTTPS

La escucha HTTPS busca tráfico según su configuración y ayuda a enrutar el tráfico a los grupos de back-end.

### <a name="step-1"></a>Paso 1

Vaya al portal de Azure y seleccione una puerta de enlace de aplicación existente

### <a name="step-2"></a>Paso 2

Haga clic en escucha y haga clic en el botón Agregar para agregar una escucha.

![módulo de información general de puerta de enlace de aplicación][1]

### <a name="step-3"></a>Paso 3

Rellene la información necesaria para la escucha y cargar el certificado .pfx, cuando haya terminado, haga clic en Aceptar.

**Nombre** : este valor es un nombre descriptivo de la escucha.

**Configuración de IP de front-end** - este valor es la configuración de IP de front-end que se usa para la escucha.

**Front-end (puerto nombre /)** - un nombre descriptivo para el puerto que se utiliza en el front-end de la puerta de enlace de la aplicación y el puerto que se utiliza.

**Protocolo** - un modificador para determinar si se utiliza https o http para el front-end.

**Certificado (nombre y contraseña)** : descarga de SSL si se usa, se requiere para esta configuración un certificado .pfx y se requiere un nombre descriptivo y la contraseña.

![Agregar placa escucha][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>Crear una regla y asociar a la escucha

Se ha creado la escucha. Es el momento de crear una regla para manejar el tráfico de la escucha.

### <a name="step-1"></a>Paso 1

Haga clic en las **reglas** de la puerta de enlace de la aplicación y, a continuación, haga clic en Agregar.

![módulo de reglas de puerta de enlace de aplicación][3]

### <a name="step-2"></a>Paso 2

En el módulo de **Agregar regla básica** , escriba el nombre descriptivo para la regla y seleccione la escucha creada en el paso anterior. Seleccione el grupo de back-end adecuados y la configuración de http y haga clic en **Aceptar**

![ventana de configuración de HTTPS][4]

Ahora se guarda la configuración de la puerta de enlace de aplicación. El proceso de almacenamiento para esta configuración puede tardar unos minutos antes de que estén disponibles para ver a través del portal o a través de PowerShell. Una vez guardada la puerta de enlace de aplicación controla el cifrado y descifrado de tráfico. Todo el tráfico entre la puerta de enlace de aplicaciones y los servidores web de back-end se tratará sobre http. Cualquier comunicación al cliente si inicia sobre https se devolverá al cliente cifrado.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo configurar un sondeo de estado personalizados con la puerta de enlace de aplicaciones de Azure, consulte [crear un sondeo de estado personalizado](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png