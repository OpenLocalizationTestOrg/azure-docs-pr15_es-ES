<properties
    pageTitle="Documentación de gobierno Azure | Microsoft Azure"
    description="Proporciona una comparación de características e instrucciones sobre cómo desarrollar aplicaciones para la administración pública de Azure"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/13/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-storage"></a>Almacenamiento de la administración pública de Azure

##  <a name="azure-storage"></a>Almacenamiento de Azure

Para obtener detalles sobre este servicio y cómo usarla, consulte la [documentación público de almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/).

### <a name="variations"></a>Variaciones

Las direcciones URL para las cuentas de almacenamiento en Azure gobierno son diferentes:

Tipo de servicio|Azure público|Administración pública de Azure
---|---|---
Almacenamiento de blobs|*. blob.core.windows.net|*. blob.core.usgovcloudapi.net
Almacenamiento de cola|*. queue.core.windows.net|*. queue.core.usgovcloudapi.net
Almacenamiento de tablas|*. table.core.windows.net| *. table.core.usgovcloudapi.net

>[AZURE.NOTE] Todas las secuencias de comandos y el código necesario para tener en cuenta los extremos correspondientes.  Vea [Configurar cadenas de conexión de almacenamiento de Azure](../storage-configure-connection-string.md#creating-a-connection-string-to-the-explicit-storage-endpoint). 

Para obtener más información sobre las API de vea el <a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx">Constructor de cuenta de almacenamiento de nube</a>.

El sufijo de extremo para usar en estas sobrecargas es core.usgovcloudapi.net 

### <a name="considerations"></a>Consideraciones

La siguiente información identifica el límite de la administración pública de Azure para el almacenamiento de Azure:

| Datos regulados/controlados permitidos | Datos regulados/controlados no permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Los datos, almacena y se procesan en el almacenamiento de Azure producto puede contener datos de exportación controlada. Autenticadores estáticos, como las contraseñas y los PIN de tarjeta inteligente para obtener acceso a los componentes de la plataforma Windows Azure. Claves privadas de certificados que se utilizan para administrar los componentes de la plataforma Windows Azure. Otros seguridad información/información confidencial, como certificados, las claves de cifrado, claves principales y las claves de almacenamiento almacenadas en servicios de Azure. | No se permiten que los metadatos de almacenamiento de Azure contienen datos de exportación controlada. Estos metadatos incluyen todos los datos de configuración especificados al crear y mantener su producto de almacenamiento.  No escriba datos regulados/controlados en los siguientes campos: grupos de recursos, nombres de implementación, recursos, etiquetas de recursos  

##  <a name="premium-storage"></a>Almacenamiento de Premium

Para obtener detalles sobre este servicio y cómo usarla, vea [almacenamiento Premium: almacenamiento de alto rendimiento para cargas de trabajo de máquina Virtual de Azure](../storage/storage-premium-storage.md).

###  <a name="variations"></a>Variaciones

Almacenamiento de Premium está disponible de forma general en el USGov Virginia. Esto incluye máquinas virtuales de la serie DS. 

### <a name="considerations"></a>Consideraciones

Se aplican las mismas consideraciones de datos de almacenamiento enumeradas anteriormente a las cuentas de almacenamiento premium. 

##  <a name="next-steps"></a>Pasos siguientes

Para información adicional y actualizaciones suscripción a la <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog de Microsoft Azure gobierno.</a>
