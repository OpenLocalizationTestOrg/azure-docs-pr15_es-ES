<properties
   pageTitle="Configurar una puerta de enlace de aplicación existente para alojar varios sitios en el portal de Azure | Microsoft Azure"
   description="Esta página contiene instrucciones para configurar una puerta de enlace de Azure aplicación existente para alojar varias aplicaciones web en la misma puerta de enlace con el portal de Azure."
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
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="configure-an-existing-application-gateway-for-hosting-multiple-web-applications"></a>Configurar una puerta de enlace de aplicación existente para alojar varias aplicaciones web

> [AZURE.SELECTOR]
- [Portal de Azure](application-gateway-create-multisite-portal.md)
- [Azure PowerShell de administrador de recursos](application-gateway-create-multisite-azureresourcemanager-powershell.md)

Alojamiento de varios sitios le permite implementar más de una aplicación web en la misma puerta de enlace de aplicación. Se basa en la presencia del encabezado de host de la solicitud HTTP entrante, para determinar qué escucha recibiría tráfico. La escucha dirige a continuación, el tráfico al grupo de back-end adecuados tal como está configurado en la definición de reglas de la puerta de enlace. En aplicaciones web SSL habilitado, la puerta de enlace de aplicación se basa en la extensión de nombre de servidor indicación (SNI) para elegir el oyente correcto para el tráfico de web. Un uso común para el hospedaje de sitios múltiples es cargar equilibrar las solicitudes de los dominios de web diferentes para grupos de otro servidor back-end. Del mismo modo también se pueden hospedar varios subdominios del mismo dominio raíz en la misma puerta de enlace de aplicación.

## <a name="scenario"></a>Escenario

En el ejemplo siguiente, la puerta de enlace de aplicación sirve tráfico de contoso.com y fabrikam.com con dos agrupaciones de servidor back-end: contoso grupo de servidor y fabrikam server. Programa de instalación similar podría utilizarse para subdominios host como app.contoso.com y blog.contoso.com.

![escenario multisitio][multisite]

## <a name="before-you-begin"></a>Antes de empezar

Este escenario agrega compatibilidad con varios sitio a una puerta de enlace de aplicación existente. Para completar este escenario debe estar disponible para configurar un escenario de una puerta de enlace de aplicación existente. Visite [crear una puerta de enlace de aplicación a través del portal](./application-gateway-create-gateway-portal.md) para obtener información sobre cómo crear una puerta de enlace de aplicación básica en el portal.

## <a name="requirements"></a>Requisitos

- **Grupo de servidores de back-end:** La lista de direcciones IP de los servidores de back-end. Las direcciones IP enumeradas o bien deben pertenecer a la subred virtual o deben ser VIP o IP pública. También puede utilizarse el nombre de dominio completo.
- **Configuración de la agrupación de back-end server:** Cada grupo tiene configuración como afinidad basada en cookies, protocolo y puerto. Estas opciones están vinculadas a un grupo y se aplican a todos los servidores dentro del grupo.
- **Puerto front-end:** Este puerto es el público que se abre en la puerta de enlace de aplicación. El tráfico llega a este puerto y, a continuación, se redirige a uno de los servidores de back-end.
- **Escucha:** La escucha tiene un puerto front-end, un protocolo (Http o Https, estos valores distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se descarga la configuración de SSL). Nombre de host e indicadores SNI también se agregan para puertas de enlace de aplicación habilitada de múltiples sitios.
- **Regla:** La regla enlaza la escucha, la agrupación de servidor back-end y define el tráfico debe dirigirse a cuando llegue a una determinada escucha qué grupo de servidor back-end.
- **Certificados:** Cada escucha requiere un certificado único, en este ejemplo se crea 2 escucha para varios sitios. Es necesario crear dos certificados .pfx y las contraseñas para ellos.

## <a name="create-an-application-gateway"></a>Crear una puerta de enlace de aplicación

Los siguientes son los pasos necesarios para actualizar la puerta de enlace de aplicación:

1. Crear grupos de back-end para cada sitio.
2. Crear una nueva escucha para cada puerta de enlace de la aplicación de sitio se admiten.
3. Crear reglas para asignar cada escucha con el correspondiente back-end.

## <a name="create-back-end-pools-for-each-site"></a>Crear grupos de back-end para cada sitio

Le dicha puerta de enlace de la aplicación de un grupo de back-end para cada sitio de soporte técnico es necesario, en este caso 2 se creará, uno para contoso11.com y otro para fabrikam11.com.

### <a name="step-1"></a>Paso 1

Navegue hasta una puerta de enlace de aplicación existente en el portal de Azure (https://portal.azure.com). Seleccione **grupos de back-end** y haga clic en **Agregar**

![Agregar grupos de back-end][7]

### <a name="step-2"></a>Paso 2

Rellene la información para el grupo de back-end **grupo 1**, agregar las direcciones ip o FQDN de los servidores de back-end y haga clic en **Aceptar**

![configuración del grupo 1 de grupo de back-end][8]

### <a name="step-3"></a>Paso 3

En el módulo de back-end grupos, haga clic en **Agregar** para agregar un grupo de back-end adicionales **pool2**, agregar las direcciones ip o FQDN de los servidores de back-end y haga clic en **Aceptar**

![configuración de la agrupación pool2 back-end][9]

### <a name="create-listeners-for-each-back-end"></a>Crear escucha para cada back-end

Puerta de enlace de aplicación se basa en los encabezados de host HTTP 1.1 hospedar más de un sitio Web en la misma dirección IP pública y puerto. Esta propiedad no contiene el oyente básico creado en el portal.

### <a name="step-1"></a>Paso 1

Haga clic en **escucha** de la puerta de enlace de aplicación existente y haga clic en **varios sitios** para agregar la primera escucha.

![módulo de información general de escucha][1]

### <a name="step-2"></a>Paso 2

Rellene la información para la escucha en este ejemplo terminación SSL está configurado, cree un nuevo puerto front-end. Cargar el certificado .pfx que se usará para terminación SSL. La única diferencia en este módulo en comparación con el módulo estándar oyente básicos es el nombre de host.

![hoja de propiedades de escucha][2]

### <a name="step-3"></a>Paso 3

Haga clic en **varios sitios** y crear otra escucha como se describe en el paso anterior para el segundo sitio. Asegúrese de usar un certificado diferente para la segunda escucha. La única diferencia en este módulo en comparación con el módulo estándar oyente básicos es el nombre de host. Rellene la información que el oyente y haga clic en **Aceptar**.

![hoja de propiedades de escucha][3]

> [AZURE.NOTE] Creación de escucha en el portal de Azure de puerta de enlace de aplicación es una tarea de ejecución larga, puede tardar algún tiempo para crear los dos agentes de escucha en este escenario. Cuando complete la escucha en el portal tal como se muestra en la siguiente imagen.

![información general de escucha][4]

### <a name="create-rules-to-map-listeners-to-backend-pools"></a>Crear reglas para asignar escucha a grupos de back-end

### <a name="step-1"></a>Paso 1

Navegue hasta una puerta de enlace de aplicación existente en el portal de Azure (https://portal.azure.com). Seleccione **reglas** y elija la de regla predeterminada **Regla1** existente y haga clic en **Editar**.

### <a name="step-2"></a>Paso 2

Complete el módulo de reglas tal como se muestra en la siguiente imagen. Elegir la primera escucha y el primer grupo y haga clic en **Guardar** cuando haya terminado.

![Editar regla existente][6]

### <a name="step-3"></a>Paso 3

Haga clic en la **regla básica** para crear la regla 2 º. Rellene el formulario con la segunda escucha y el segundo grupo de back-end y haga clic en **Aceptar** para guardar.

![Agregar módulo de regla básica][10]

Esto completa la configuración de una puerta de enlace de aplicación existente con compatibilidad con varios sitio a través del portal de Azure.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo proteger los sitios Web con la [Puerta de enlace de aplicaciones - servidor de aplicaciones Web](application-gateway-webapplicationfirewall-overview.md)

<!--Image references-->
[1]: ./media/application-gateway-create-multisite-portal/figure1.png
[2]: ./media/application-gateway-create-multisite-portal/figure2.png
[3]: ./media/application-gateway-create-multisite-portal/figure3.png
[4]: ./media/application-gateway-create-multisite-portal/figure4.png
[5]: ./media/application-gateway-create-multisite-portal/figure5.png
[6]: ./media/application-gateway-create-multisite-portal/figure6.png
[7]: ./media/application-gateway-create-multisite-portal/figure7.png
[8]: ./media/application-gateway-create-multisite-portal/figure8.png
[9]: ./media/application-gateway-create-multisite-portal/figure9.png
[10]: ./media/application-gateway-create-multisite-portal/figure10.png
[multisite]: ./media/application-gateway-create-multisite-portal/multisite.png