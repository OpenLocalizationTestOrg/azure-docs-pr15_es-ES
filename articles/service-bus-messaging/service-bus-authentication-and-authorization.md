<properties 
    pageTitle="Autenticación de Bus y la autorización del servicio | Microsoft Azure"
    description="Descripción general de autenticación de firma de acceso compartido (SA)."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="service-bus-authentication-and-authorization"></a>Autorización y autenticación de Bus de servicio

Aplicaciones pueden autenticar a Bus de servicio de Azure mediante en autenticación de firma de acceso compartido (SA), o a través de Azure Active Directory Control de acceso (también conocido como servicio de Control de acceso o ACS). Compartir acceso firma autenticación permite aplicaciones para autenticar al Bus de servicio usando una tecla de acceso configurada en el espacio de nombres o en la entidad con la que están asociados los derechos específicos. A continuación, puede usar esta clave para generar un token de firma de acceso compartido que los clientes pueden utilizar para autenticar al Bus de servicio.

>AZURE. Se recomienda SA importantes sobre ACS, ya que proporciona un esquema de autenticación simple, flexible y fácil de usar para Bus de servicio. Las aplicaciones pueden utilizar SA en escenarios en los que no es necesario administrar el concepto de un "usuario" autorizado.

## <a name="shared-access-signature-authentication"></a>Autenticación de firma de Access compartida

[Autenticación de SA](service-bus-sas-overview.md) le permite conceder acceso a un usuario a recursos de Bus de servicio con derechos específicos. Autenticación de SA en Bus de servicio implica la configuración de una clave de cifrado con derechos asociados a un recurso de Bus de servicio. Clientes, a continuación, pueden tener acceso a ese recurso presentando un símbolo de SA que se compone del recurso URI accediendo y expiración firmados con la clave configurada.

Puede configurar claves para asociaciones de seguridad en un espacio de nombres de Bus de servicio. La clave se aplica a todas las entidades de mensajería de ese espacio de nombres. También puede configurar teclas en temas y colas de Bus de servicio. SA también es compatible con Bus de servicio transmite.

Para usar SA, puede configurar un objeto de [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) en un espacio de nombres, una cola o un tema que consta de las siguientes acciones:

- *Clave* que identifica la regla.

- *PrimaryKey* es una clave de cifrado usada para tokens SA de inicio de sesión o validar.

- *Clave secundaria* es una clave de cifrado usada para tokens SA de inicio de sesión o validar.

- *Derechos* que representa la colección de escuchar, enviar o administrar derechos.

Las reglas de autorización configuradas en el nivel de espacio de nombres pueden conceder acceso a todas las entidades de un espacio de nombres para clientes con tokens que ha iniciado sesión con la clave correspondiente. 12 autorización reglas se pueden configurar hasta en un espacio de nombres de Bus de servicio, cola o un tema. De forma predeterminada, un [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) con todos los derechos está configurado para cada espacio de nombres cuando se está configurado en primer lugar.

Para obtener acceso a una entidad, el cliente requiere un símbolo de SA generado mediante una específica [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). El token de SA se genera utilizando la SHA256 HMAC de una cadena de recurso está compuesto por el URI de recurso al que se solicite acceso y expiración con una clave de cifrado asociada a la regla de autorización.

Soporte de autenticación de SA para Bus de servicio está incluido en las versiones de Azure .NET SDK 2.0 y versiones posteriores. SA incluyen compatibilidad con un [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). Todas las API que aceptan una cadena de conexión como parámetro son compatibles con las cadenas de conexión SA.

## <a name="acs-authentication"></a>Autenticación de ACS

Autenticación de Bus de servicio a través de ACS se administra mediante un complemento "-sb" espacio de nombres de ACS. Si desea que un espacio de nombres de ACS companion crearse para un espacio de nombres de Bus de servicio, no se puede crear el espacio de nombres de Bus de servicio con el portal clásico Azure; debe crear el espacio de nombres mediante el cmdlet de PowerShell [AzureSBNamespace de nuevo](https://msdn.microsoft.com/library/azure/dn495165.aspx) . Por ejemplo:

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $true
```

Para evitar la creación de un espacio de nombres de ACS, ejecute el siguiente comando:

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $false
```

Por ejemplo, si crea un espacio de nombres de Bus de servicio llamado **contoso.servicebus.windows.net**, un espacio de nombres de ACS companion denominada **contoso sb.accesscontrol.windows.net** aprovisionado automáticamente. Para todos los espacios de nombres que se crearon antes de agosto de 2014, también se ha creado un espacio de nombres de ACS adjunta.

Se le proporciona una identidad de servicio predeterminado "propietario", con todos los derechos, de forma predeterminada, este espacio de nombres de ACS companion. Puede obtener un control más preciso a cualquier entidad de Bus de servicio a través de ACS mediante la configuración de las relaciones de confianza apropiadas. Puede configurar las identidades de servicio adicionales para administrar el acceso a entidades de Bus de servicio.

Para obtener acceso a una entidad, el cliente solicita un token SWT de ACS con las notificaciones correspondientes al presentar sus credenciales. El token SWT debe, a continuación, enviarse como parte de la solicitud a Bus de servicio para habilitar la autorización del cliente para tener acceso a la entidad.

Soporte de autenticación de ACS para Bus de servicio está incluido en las versiones de Azure .NET SDK 2.0 y versiones posteriores. Esta autenticación incluye compatibilidad con un [SharedSecretTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedsecrettokenprovider.aspx). Todas las API que aceptan una cadena de conexión como parámetro son compatibles con las cadenas de conexión de ACS.

## <a name="next-steps"></a>Pasos siguientes

Continúe leyendo [autenticación de firma de acceso compartido con Bus de servicio](service-bus-shared-access-signature-authentication.md) para obtener más detalles acerca de SA.

Para obtener una descripción general de SA en Bus de servicio, consulte [Firmas de acceso compartido](service-bus-sas-overview.md).

Puede encontrar más información sobre los tokens de ACS en [Cómo: solicitar un Token ACS mediante el protocolo de ajustar OAuth](https://msdn.microsoft.com/library/hh674475.aspx).



