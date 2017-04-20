<properties
   pageTitle="Crear una puerta de enlace de aplicación utilizando el CLI de Azure en el Administrador de recursos | Microsoft Azure"
   description="Aprenda a crear una puerta de enlace de aplicación mediante la CLI de Azure en el Administrador de recursos"
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

# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Crear una puerta de enlace de aplicación mediante la CLI de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](application-gateway-create-gateway-portal.md)
- [Azure PowerShell de administrador de recursos](application-gateway-create-gateway-arm.md)
- [Azure PowerShell clásica](application-gateway-create-gateway.md)
- [Azure plantilla de administrador de recursos](application-gateway-create-gateway-arm-template.md)
- [CLI de Azure](application-gateway-create-gateway-cli.md)

La puerta de enlace de aplicación Azure es un equilibrador de carga de nivel 7. Proporciona migración tras error, las solicitudes HTTP enrutamiento de rendimiento entre diferentes servidores, independientemente de si están en la nube o local. Puerta de enlace de aplicación tiene las siguientes características de entrega de la aplicación: HTTP cargar equilibrio, afinidad sesión basada en cookies y descarga de capa de Sockets seguros (SSL), sondeos de estado personalizados y soporte técnico para varios sitios.

## <a name="prerequisite-install-the-azure-cli"></a>Requisito previo: Instale la CLI de Azure

Para realizar los pasos de este artículo, debe [instalar la interfaz de línea de comandos de Azure para Mac, Linux y Windows (Azure CLI)](../xplat-cli-install.md) y tiene que [iniciar sesión en Azure](../xplat-cli-connect.md). 

> [AZURE.NOTE] Si no tiene una cuenta de Azure, necesita uno. Subir el inicio de sesión para una [prueba gratuita aquí](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Escenario

En este escenario, aprenda a crear una puerta de enlace de la aplicación con el portal de Azure.

Este escenario hará lo siguiente:

- Crear una puerta de enlace de aplicación medio con dos instancias.
- Crear una red virtual denominada AdatumAppGatewayVNET con un bloque CIDR reservado de 10.0.0.0/16.
- Crear una subred denominada Appgatewaysubnet que utiliza 10.0.0.0/28 como su bloque CIDR.
- Configurar un certificado para la descarga SSL.

![Ejemplo de escenario][scenario]

>[AZURE.NOTE] Configuración adicional de la puerta de enlace de aplicación, incluido el estado personalizado sondeos, las direcciones de grupo de back-end y reglas adicionales se configuran una vez configurada la puerta de enlace de la aplicación y no durante la implementación inicial.

## <a name="before-you-begin"></a>Antes de empezar

La puerta de enlace de aplicación Azure requiere su propia subred. Al crear una red virtual, asegúrese de dejar suficiente espacio de direcciones para tener varias subredes. Una vez que se implementa una puerta de enlace de aplicación a una subred, puertas de enlace de aplicación solo adicional son capaces de agregarse a la subred.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure

Abra el **símbolo del sistema de Microsoft Azure**e inicie sesión. 

    azure login

Una vez que escriba en el ejemplo anterior, se proporciona un código. Vaya a https://aka.ms/devicelogin en un explorador para continuar el proceso de inicio de sesión.

![inicio de sesión de cmd que muestra dispositivo][1]

En el explorador, escriba el código que ha recibido. Se le redirigirá a una página de inicio de sesión.

![explorador para escribir código][2]

Una vez que ha introducido el código ha iniciado sesión, cierre el explorador para continuar con el escenario.

![sesión iniciada correctamente][3]

## <a name="switch-to-resource-manager-mode"></a>Cambiar a modo de administrador de recursos

    azure config mode arm

## <a name="create-the-resource-group"></a>Crear el grupo de recursos

Antes de crear la puerta de enlace de aplicación, se crea un grupo de recursos que contengan la puerta de enlace de aplicación. La siguiente imagen muestra el comando.

    azure group create -n AdatumAppGatewayRG -l eastus

## <a name="create-a-virtual-network"></a>Crear una red virtual

Una vez creado el grupo de recursos, se crea una red virtual para la puerta de enlace de aplicación.  En el ejemplo siguiente, el espacio de direcciones era como 10.0.0.0/16 según se define en las notas del escenario anterior.

    azure network vnet create -n AdatumAppGatewayVNET -a 10.0.0.0/16 -g AdatumAppGatewayRG -l eastus

## <a name="create-a-subnet"></a>Crear una subred

Después de crea la red virtual, se agrega una subred de la puerta de enlace de aplicación.  Si planea usar puerta de enlace de aplicación con una aplicación web hospedada en la misma red virtual como la puerta de enlace de aplicación, asegúrese de dejar suficiente espacio para otra subred.

    azure network vnet subnet create -g AdatumAppGatewayRG -n Appgatewaysubnet -v AdatumAppGatewayVNET -a 10.0.0.0/28 

## <a name="create-the-application-gateway"></a>Crear la puerta de enlace de aplicación

Una vez que se crean la red virtual y subred, los requisitos previos de la puerta de enlace de aplicaciones están completos. Además un certificado .pfx exportado previamente y la contraseña para el certificado son necesarios para el siguiente paso: direcciones IP que se usa para el back-end son las direcciones IP de su servidor back-end. Estos valores pueden ser direcciones IP privado en la red virtual, direcciones IP de público o nombres de dominio completo para los servidores de back-end.

    azure network application-gateway create -n AdatumAppGateway -l eastus -g AdatumAppGatewayRG -e AdatumAppGatewayVNET -m Appgatewaysubnet -r 134.170.185.46,134.170.188.221,134.170.185.50 -y c:\AdatumAppGateway\adatumcert.pfx -x P@ssw0rd -z 2 -a Standard_Medium -w Basic -j 443 -f Enabled -o 80 -i http -b https -u Standard

> [AZURE.NOTE] Para obtener una lista de los parámetros que se pueden proporcionar durante la creación, ejecute el siguiente comando: **crear la puerta de enlace red azure aplicaciones--ayuda**.

Este ejemplo crea una puerta de enlace de aplicación básica con la configuración predeterminada para escucha, grupo de back-end, configuración de http de back-end y reglas. También se configura descarga SSL. Puede modificar esta configuración para adaptarla a la implementación una vez que la configuración es correcta.
Si ya tiene la aplicación web definir mediante la agrupación de back-end en los pasos anteriores, una vez creado, el equilibrio de carga comienza.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a crear sondeos de estado personalizados con una visita a [crear un sondeo de estado personalizados](application-gateway-create-probe-portal.md)

Obtenga información sobre cómo configurar la descarga de SSL y tomar el descifrado SSL costoso desactivar los servidores web con una visita a [Configurar la descarga de SSL](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png