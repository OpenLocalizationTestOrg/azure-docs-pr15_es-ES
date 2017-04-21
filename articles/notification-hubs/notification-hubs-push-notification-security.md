<properties
    pageTitle="Seguridad de la notificación Hubs"
    description="Este tema explica la seguridad de hubs de notificación de Azure."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="security"></a>Seguridad

##<a name="overview"></a>Información general

Este tema describe el modelo de seguridad de Azure Hubs de notificación. Porque Hubs de notificación es una entidad de Bus de servicio, implementan el mismo modelo de seguridad que Bus de servicio. Para obtener más información, vea los temas de [Autenticación de Bus de servicio](https://msdn.microsoft.com/library/azure/dn155925.aspx) .

##<a name="shared-access-signature-security-sas"></a>Seguridad de la firma de acceso compartido (SA) 

Notificación Hubs implementa un esquema de seguridad de la entidad denomina SA (firma de acceso compartido). Este esquema permite mensajería entidades declarar hasta 12 reglas de autorización en su descripción que conceden derechos de esa entidad.

Cada regla contiene un nombre, un valor de clave (secreta compartida) y un conjunto de derechos, como se explica en la sección "Notificaciones de seguridad". Al crear un concentrador de notificación, dos reglas se crean automáticamente: uno con derechos de escuchar (que usa la aplicación cliente) y otro con todos los derechos (que usa el servidor de la aplicación).

Al realizar la administración de registro de aplicaciones de cliente, si la información se envía a través de notificaciones no es confidencial (por ejemplo, las actualizaciones de tiempo), una manera de acceder a un concentrador de notificación para proporcionar el valor de clave de la regla de acceso de solo escuchar a la aplicación cliente y darle el valor de clave del acceso completo de la regla a la aplicación de back-end.

No se recomienda que se incrusta el valor de clave en las aplicaciones cliente de la tienda Windows. Una manera de evitar la incrustación el valor de clave es que la aplicación cliente recuperar desde el servidor de la aplicación en el inicio.

Es importante comprender que la clave con acceso de escuchar permite una aplicación de cliente registrar para cualquier etiqueta. Si su aplicación debe restringir registros a etiquetas específicas para clientes específicos (por ejemplo, cuando etiquetas representan los identificadores de usuario), la aplicación de back-end debe realizar los registros. Para obtener más información, vea Administración de registro. Tenga en cuenta que, de este modo, la aplicación cliente no tendrán acceso directo a Hubs de notificación.

##<a name="security-claims"></a>Notificaciones de seguridad

Similar a otras entidades, que se permiten operaciones de concentrador de notificación para las notificaciones de seguridad de tres: escuchar, enviar y administrar.

| Reclamar | Descripción | Operaciones permitidas |
|-------|-------------|--------------------|
| Escuchar | Crear o actualizar, lea y eliminar registros único | Crear o actualizar el registro<br><br>Registro de lectura<br><br>Leer todos los registros para un controlador<br><br>Eliminar registro |
| Enviar | Enviar mensajes al concentrador de notificación | Enviar mensaje |
| Administrar | CRUDs en Hubs de notificación (incluyendo la actualización PNS credenciales y las claves de seguridad) y a continuación, obtenga registros basados en etiquetas | Hubs de notificación de crear, actualizar o lectura/eliminar<br><br>Leer registros por etiqueta |


Notificación Hubs aceptan notificaciones concedidos tokens de Control de acceso de Microsoft Azure y tokens de firma generados con claves compartidas configuradas directamente en el Hub de notificación.