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
    ms.date="09/29/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-compute"></a>Cálculo de la administración pública de Azure

##  <a name="virtual-machines"></a>Máquinas virtuales de Windows

Para obtener detalles sobre este servicio y cómo usarla, vea [Tamaños de máquinas virtuales de Windows Azure](../virtual-machines/virtual-machines-windows-sizes.md).

### <a name="variations"></a>Variaciones

Las siguientes SKU VM está disponible de forma general (GA) en la administración pública de Azure:

SKU DE VM|Estados Unidos Gov VA|Estados Unidos Gov IA|Notas
---|---|---|---
RESPUESTAS|GA|GA|Ninguno
Dv1|GA|-|Ninguno
DSv1|GA|-|Ninguno
Dv2|GA|GA|próximamente 15
F|GA|GA|Ninguno
G|Planeado|-|Ninguno

###  <a name="data-considerations"></a>Consideraciones de datos

La siguiente información identifica el límite de la administración pública de Azure para Azure máquinas virtuales de Windows:

| Datos regulados/controlados permitidos | Datos regulados/controlados no permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Datos introducido, almacena y procesa dentro de una máquina virtual puede contener datos de exportación controlada. Archivos binarios que se ejecuta en Azure máquinas virtuales de Windows. Autenticadores estáticos, como las contraseñas y los PIN de tarjeta inteligente para obtener acceso a los componentes de la plataforma Windows Azure. Claves privadas de certificados que se utilizan para administrar los componentes de la plataforma Windows Azure. Cadenas de conexión de SQL.  Otros seguridad información/información confidencial, como certificados, las claves de cifrado, claves principales y las claves de almacenamiento almacenadas en servicios de Azure.  | No se permiten que los metadatos contienen datos de exportación controlada. Estos metadatos incluyen todos los datos de configuración especificados al crear y mantener su máquina Virtual de Azure.  No escriba datos regulados/controlados en los siguientes campos: nombres de función o grupos de recursos, implementación, nombres de los recursos, etiquetas de recursos de inquilinos  

## <a name="next-steps"></a>Pasos siguientes

Para obtener información adicional y actualizaciones, suscribirse a la <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog de Microsoft Azure gobierno.</a>
