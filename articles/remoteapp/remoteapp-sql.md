<properties
   pageTitle="SQL Azure con RemoteApp Azure | Microsoft Azure"
   description="Aprenda a usar SQL Azure con RemoteApp de Azure."
   services="remoteapp"
   documentationCenter=""
   authors="ericorman"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="sql-azure-with-azure-remoteapp"></a>SQL Azure con RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

A menudo, cuando eligen clientes hospedar sus aplicaciones de Windows en la nube con Azure RemoteApp también desee migrar sus datos como SQL Server en la nube para una implementación de nube todo. Esto permite solución todo nube hospedado que cualquier dispositivo en cualquier lugar con Azure RemoteApp puede tener acceso en cualquier momento. A continuación se muestran vínculos y referencias junto con instrucciones para ayudarle con este proceso.  

## <a name="migrate-your-sql-data"></a>Migrar los datos SQL

Iniciar con la [migración de una base de datos de SQL Server a la base de datos de SQL Azure](../sql-database/sql-database-cloud-migrate.md). 

## <a name="configure-azure-remoteapp"></a>Configuración de RemoteApp de Azure
Hospedar la aplicación de Windows en Azure RemoteApp. A continuación encontrará instrucciones paso a paso un muy alto nivel:

1.     Crear la [plantilla de RemoteApp de Azure VM](remoteapp-imageoptions.md). 
2.     Instale la aplicación necesaria en la máquina virtual.
3.     Configurar la aplicación de modo que se conecta a la base de datos de SQL y confirme que funciona.
4.     Sysprep y cierre la máquina virtual. Capturar esto como una imagen para su uso con Azure. **Nota:** Debe asegurarse de que la aplicación es capaz de conservar la información de conectividad de base de datos a través del proceso de sysprep. Si la aplicación no puede conservar la información de conexión de base de datos, desea contratar el proveedor de la aplicación para comprobar cómo podemos especificar la cadena de conexión.
5.     Importar la imagen personalizada en la biblioteca de Azure RemoteApp seleccionando la ubicación geográfica correcta que se encuentra la implementación de SQL Azure. 
6.     Implementar una colección de RemoteApp en el centro de datos de la misma como la implementación de SQL Azure con la plantilla anterior y publicar la aplicación. Implementación de RemoteApp de Azure en el centro de datos de la misma como su SQL Azure implementación le ayuda a garantizar la velocidad de conexión más rápida y reducir la latencia. 

## <a name="app-and-sql-configuration-considerations"></a>Consideraciones de configuración de aplicación y SQL:
Hay algunos puntos para tener en cuenta al usar SQL Azure con RemoteApp:

Obtenga información sobre [cómo configurar un servidor de seguridad de base de datos de SQL Azure](../sql-database/sql-database-firewall-configure.md). Un extracto de los Estados de artículo "inicialmente, todo el acceso a su servidor de base de datos SQL Azure está bloqueado por el firewall. Para empezar a usar su servidor de base de datos de SQL Azure, debe vaya al Portal clásica y especifique una o más reglas de firewall de nivel de servidor que permiten tener acceso a su servidor de base de datos de SQL Azure. Usar las reglas de firewall para especificar permitidos los intervalos de direcciones IP de Internet y, a continuación, o no pueden intentar conectarse al servidor de base de datos de SQL Azure Azure applications".

Además, cuando un equipo intenta conectarse al servidor de base de datos de Internet, el firewall comprueba la dirección IP de origen de la solicitud con el conjunto completo de nivel de servidor y (si es necesario) las reglas de firewall de nivel de base de datos. "Si la dirección IP de la solicitud se encuentra en uno de los intervalos especificados en las reglas de firewall de nivel de servidor, la conexión se concede en su servidor de base de datos de SQL Azure". Por lo tanto, podemos hacer uso de intervalos IP y no únicamente direcciones IP de origen individuales.

Siga las instrucciones paso a paso de [Cómo: configurar el firewall en base de datos de SQL con el Portal de Azure](../sql-database/sql-database-configure-firewall-settings.md) para especificar el intervalo IP. Al configurar las reglas de Firewall de SQL, indique el rango IP de la subred especificada para la colección de RemoteApp de Azure. Debe permitir a los servidores de ARA para conectarse a la base de datos de SQL, aunque también habrá asignadas dinámicamente direcciones IP.

## <a name="troubleshooting"></a>Solución de problemas
Si la experiencia de uso de una aplicación cliente alojado en Azure RemoteApp que se conecta a un SQL de base de datos donde se hospeda en Azure o local es lento puede haber varias razones por qué.  

- Latencia de la red de su dispositivo en Azure es alta. Mover a la conexión de red mejor y más rápida que puede para mejorar el rendimiento. Utilizar [azurespeed.com](http://azurespeed.com/) como una herramienta general para probar la latencia de dispositivos al centro de datos de Azure.  
- Aplicación de cliente alojado en Azure RemoteApp está bajo presión. Seleccionar un plan de facturación diferente como facturación Premium mejorará el rendimiento. Otro truco es supervisar los recursos que consume la aplicación: durante una sesión activa, realizar una combinación de teclas ctrl-alt-fin que iniciará la pantalla SA, seleccione Administrador de tareas y observe la utilización de los recursos de la aplicación.
- SQL server está bajo carga o no optimizada. Siga las instrucciones SQL para solucionar este problema. 

