<properties
   pageTitle="Puerta de enlace de datos local | Microsoft Azure"
   description="Una puerta de enlace local es necesario si el servidor de Analysis Services en Azure se conectará a orígenes de datos local."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="on-premises-data-gateway"></a>Puerta de enlace de datos local

La puerta de enlace de datos local actúa como un puente, que permite la transferencia de datos seguro entre orígenes de datos local y el servidor de Analysis Services de Azure en la nube.

Una puerta de enlace está instalado en un equipo de su red. Una puerta de enlace debe estar instalado para cada servidor de Azure Analysis Services que tiene en su suscripción de Azure. Por ejemplo, si tiene dos servidores de su suscripción de Azure que conectan a orígenes de datos local, debe estar instalado en dos equipos diferentes de su red de una puerta de enlace.

## <a name="requirements"></a>Requisitos

**Requisitos mínimos:**

- 4.5 de .NET framework
- versión de 64 bits de Windows 7 o Windows Server 2008 R2 (o posterior)

**Recomendado:**

- CPU núcleo 8
- 8 GB de memoria
- versión de 64 bits de Windows 2012 R2 (o posterior)

**Consideraciones importantes:**

- La puerta de enlace no se puede instalar en un controlador de dominio.

- Solo una puerta de enlace se puede instalar en un único equipo.

- Instale la puerta de enlace en un equipo que permanece en y no entra en suspensión. Si el equipo no está en, el servidor de Azure Analysis Services no puede conectarse a los orígenes de datos local para actualizar los datos.

- No instale la puerta de enlace en un equipo de conexión inalámbrica a su red. Puede disminuir el rendimiento.

- Para cambiar el nombre del servidor de una puerta de enlace que ya se ha configurado, debe volver a instalar y configurar una nueva puerta de enlace.

- En algunos casos, los modelos tabulares de conectarse a orígenes de datos con proveedores nativos como SQL Server Native Client (SQLNCLI11) pueden devolver un error. Para obtener más información, vea [conexiones de origen de datos](analysis-services-datasource.md).

## <a name="supported-on-premises-data-sources"></a>Orígenes de datos locales admitidos
Vista previa, la puerta de enlace admite conexiones entre su servidor de Azure Analysis Services y los orígenes de datos locales siguientes:

- SQL Server
- Almacén de datos SQL
- PUNTOS DE ACCESO
- Oracle
- Teradata


## <a name="download"></a>Descargar
 [Descargar la puerta de enlace](https://aka.ms/azureasgateway)


## <a name="install-and-configure"></a>Instalar y configurar

1. Ejecutar el programa de instalación.

2. Elija una ubicación de instalación y acepte los términos de licencia.

3. Inicie sesión en Azure.

4. Especifique el nombre del servidor de análisis de Azure. Solo puede especificar un servidor por la puerta de enlace. Haga clic en **Configurar** y esté listo.

    ![Inicie sesión en azure](./media\analysis-services-gateway\aas-gateway-configure-server.png)


## <a name="how-it-works"></a>Cómo funciona
La puerta de enlace se ejecuta como un servicio de Windows, **puerta de enlace de datos local**, en un equipo de red de su organización. La puerta de enlace que instalar para su uso con Azure Analysis Services se basa en la misma puerta de enlace utilizado para otros servicios como Power BI, pero con algunas diferencias en cómo está configurado.

![Cómo funciona](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Trabajo de flujo de consultas y datos similar a esta:

1.  Se crea una consulta en el servicio de nube con las credenciales cifradas para el origen de datos local. A continuación, se envía a una cola de la puerta de enlace procesar.

2.  El servicio de nube de puerta de enlace analiza la consulta e inserta la solicitud al [Bus de servicio de Azure](https://azure.microsoft.com/documentation/services/service-bus/).

3.  La puerta de enlace de datos local explora el Bus de servicio de Azure para solicitudes pendientes.

4.  La puerta de enlace obtiene la consulta, descifra las credenciales y se conecta a los orígenes de datos con dichas credenciales.

5.  La puerta de enlace, envía la consulta al origen de datos para la ejecución.

6.  Los resultados se envían desde el origen de datos, vuelva a la puerta de enlace y, a continuación, en el servicio de nube.

## <a name="windows-service-account"></a>Cuenta de servicio de Windows

La puerta de enlace de datos local está configurado para usar las credenciales de inicio de sesión de servicio de Windows *NT SERVICE\PBIEgwService* . De forma predeterminada, tiene el derecho de inicio de sesión como un servicio; en el contexto de la máquina que va a instalar la puerta de enlace. Esta credencial no es la misma cuenta que se usa para conectarse a orígenes de datos locales o su cuenta de Azure.  

Si tiene problemas con el servidor proxy debido a la autenticación, desea cambiar la cuenta de servicio de Windows a un usuario de dominio o administra la cuenta de servicio.

## <a name="ports"></a>Puertos

La puerta de enlace crea una conexión saliente con Bus de servicio de Azure. Se comunica en puertos de salida: 443 TCP (predeterminado), 5671, 5672, 9350 a través de 9354.  La puerta de enlace no requiere puertos de entrada.

Se recomienda blanca las direcciones IP de su región de datos en el firewall. Puede descargar la [lista de IP de centro de datos de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Esta lista se actualiza semanalmente.

> [AZURE.NOTE]  Son las direcciones IP enumeradas en la lista de IP del centro de datos de Azure en la notación CIDR. Por ejemplo, 10.0.0.0/24 no significa 10.0.0.0 a 10.0.0.24. Más información sobre la [notación CIDR](http://whatismyipaddress.com/cidr).

Los siguientes son los nombres de dominio completo usados por la puerta de enlace.

|Nombres de dominio|Puertos de salida|Descripción|
|---|---|---|
|*. powerbi.com|80|HTTP se utiliza para descargar el programa de instalación.|
|*. powerbi.com|443|HTTPS|
|*. analysis.windows.net|443|HTTPS|
|*. login.windows.net|443|HTTPS|
|*. servicebus.windows.net|5671 5672|Mensaje avanzada Queue protocolo (AMQP)|
|*. servicebus.windows.net|443, 9350 9354|Escucha en servicio de retransmisión de Bus sobre TCP (requiere 443 para adquisición de token de Control de acceso)|
|*. frontend.clouddatahub.net|443|HTTPS|
|*. Core.Windows.NET.|443|HTTPS|
|://Login.microsoftonline.com)|443|HTTPS|
|*. msftncsi.com|443|Se usa para probar la conectividad de internet si la puerta de enlace no es accesible por el servicio de Power BI.|
|*.microsoftonline p.com|443|Se usa para la autenticación según la configuración.|


### <a name="forcing-https-communication-with-azure-service-bus"></a>Forzar comunicación HTTPS con Bus de servicio de Azure

Puede hacer que la puerta de enlace para comunicarse con Bus de servicio de Azure mediante HTTPS en lugar de TCP directa; Sin embargo, esto puede reducir en gran medida de rendimiento. Debe modificar el archivo *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* . Cambie el valor de `AutoDetect` a `Https`. Este archivo se encuentra de forma predeterminada, en la *puerta de enlace de datos local de programa\Archivos C:\Program*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```


## <a name="troubleshooting"></a>Solución de problemas
Mostrar opciones avanzadas, la puerta de enlace de datos local que se usa para conectar Azure Analysis Services para los orígenes de datos local es la misma puerta de enlace que se utiliza con Power BI.

Si tiene problemas al instalar y configurar una puerta de enlace, no olvide vea [solucionar problemas de la puerta de enlace de Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem-tshoot/). Si cree que tiene un problema con el firewall, vea las secciones proxy o firewall.

Si cree que se ha encontrado problemas de proxy, con la puerta de enlace, vea [Configurar la configuración de proxy para las puertas de enlace de Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy.md).

## <a name="next-steps"></a>Pasos siguientes
- [Administrar Analysis Services](analysis-services-manage.md)
- [Obtener datos de Azure Analysis Services](analysis-services-connect.md)
