<properties
    pageTitle="Novedades de la pila de Azure | Microsoft Azure"
    description="Novedades de la pila de Azure"
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="helaw"/>

# <a name="whats-new-in-azure-stack-technical-preview-2"></a>Novedades de Azure pila Technical Preview 2
Esta versión ofrece nuevas características para los inquilinos y administradores.

## <a name="network"></a>Red   
   - [IDN](azure-stack-understanding-dns-in-tp2.md) proporciona registro de nombre de red interna y la resolución de sistema de nombres de dominio (DNS) sin la infraestructura adicional de DNS.
   - [Puertas de enlace de red virtual](azure-stack-create-vpn-connection-one-node-tp2.md) proporcionan opciones de conectividad VPN a recursos de Azure o local.
   - Rutas de definidas por el usuario puede redirigir el tráfico de red a través de los firewalls, seguridad, otros dispositivos y otros servicios.
   - Puede crear recursos de red desde el catálogo de soluciones.   

## <a name="storage"></a>Almacenamiento de información
 - [Colas de Azure](https://msdn.microsoft.com/library/dd179353.aspx) habilitar mensajería confiable y persistente servicio.
 - Datos de rendimiento de almacenamiento de captura de [análisis de almacenamiento](https://msdn.microsoft.com/library/azure/hh343270.aspx) . Puede usar estos datos para las solicitudes de seguimiento, analizar tendencias de uso y diagnóstico de problemas con su cuenta de almacenamiento.
 - Almacenamiento de blobs es compatible con la [operación de bloque de datos anexados](https://msdn.microsoft.com/library/azure/mt427365.aspx).
 - Soporte de cuenta de la API de almacenamiento de Premium.
 - Soporte de servicio de almacenamiento para SDK como CLI de Azure, PowerShell, .NET, Python y Java SDK y herramientas comunes de inquilinos. 
 - [Firma de acceso compartido de almacenamiento cuenta](https://msdn.microsoft.com/library/azure/mt584140.aspx) habilitar granular delegación de acceso a los servicios de almacenamiento sin tener que compartir la clave de cuenta completa.  
 - Servicios de almacenamiento ahora usar [Cuentas de servicio administradas de grupo](https://technet.microsoft.com/library/hh831477.aspx) de seguridad sólido con sobrecarga de administración baja.
 - Puede reclamar inquilino sin usar recursos a petición.
 - Longitud de retención de la cuenta de almacenamiento eliminados es configurable.
 - Puede recuperar cuentas de almacenamiento del inquilino eliminada.

## <a name="compute"></a>Calcular
- Puede cancelar la asignación de máquinas virtuales de Windows.
- Puede implementar extensiones de máquina virtual para fines de administración de configuración y solución de problemas.
- Puede cambiar el tamaño de los discos de la máquina virtual.
- Máquinas virtuales pueden tener varias interfaces de red.

### <a name="portal-experience"></a>Experiencia de portal
 - Las áreas de pila Azure son una unidad lógica de escala y administración de la pila de Azure. En esta vista previa, puede ver información sobre servicios como proceso, red y almacenamiento por región.
 - Ahora se puede obtener una vista previa de la interfaz de [updates.md de pila de azure] (actualizaciones).

## <a name="key-vault"></a>Depósito clave
- [Depósito de clave en pila de Azure](azure-stack-kv-intro.md) proporciona administración segura de sus claves y las contraseñas de aplicaciones de la nube.
- Puede auditar y supervisar el uso de clave por aplicaciones y máquinas virtuales.

## <a name="billing-and-usage"></a>Facturación y el uso
 - [Facturación y las API de consumo](azure-stack-billing-and-chargeback.md) exponer datos en cómo se consumen los servicios.  
 - Proveedores de delegada habilitar distribuidores ofrecer los servicios de Azure pila a sus clientes.

## <a name="monitoring-and-health"></a>Supervisión y estado
 - Nuevas capacidades de supervisión disponibles en el portal y las API le permiten ver y administrar alertas en su entorno de manera proactiva.  

## <a name="next-steps"></a>Pasos siguientes
- [Comprender la arquitectura de prueba de concepto de pila de Azure](azure-stack-architecture.md)      
- [Comprender los requisitos previos de implementación](azure-stack-deploy.md)
- [Implementar la pila de Azure](azure-stack-run-powershell-script.md)

  
