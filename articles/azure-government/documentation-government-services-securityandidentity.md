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
    ms.date="10/12/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-security-and-identity"></a>Identidad y seguridad de la administración pública de azure

##  <a name="key-vault"></a>Depósito clave
Para obtener detalles sobre este servicio y cómo usarla, vea el <a href="https://azure.microsoft.com/documentation/services/key-vault">documentación pública de Azure clave depósito.</a>

### <a name="data-considerations"></a>Consideraciones de datos
La siguiente información identifica el límite de la administración pública de Azure para Azure depósito de clave:

| Datos regulados/controlados permitidos | Datos regulados/controlados no permitidos |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Todos los datos cifrados con una clave de Azure clave depósito pueden contener datos regulados/controlados. | No se permiten que los metadatos de Azure depósito de clave contienen datos de exportación controla. Estos metadatos incluyen todos los datos de configuración especificados al crear y mantener su cámara clave.  No escriba datos regulados/controlados en los siguientes campos: nombres de grupo de recursos o depósito de clave, nombre de suscripción |

Depósito de clave está generalmente disponible en la administración pública de Azure. Como en público, no hay ninguna extensión, por lo que solo está disponible a través de PowerShell y CLI depósito de clave.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información adicional y actualizaciones, suscribirse a la <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog de Microsoft Azure gobierno.</a>
