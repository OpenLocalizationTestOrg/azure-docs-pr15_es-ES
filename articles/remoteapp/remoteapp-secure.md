
<properties
    pageTitle="Proteger aplicaciones y recursos de RemoteApp de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo bloquear aplicaciones y recursos de RemoteApp de Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="secure-apps-and-resources-in-azure-remoteapp"></a>Proteger aplicaciones y recursos de RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

RemoteApp Azure proporciona a los usuarios acceso a aplicaciones de Windows administrada centralmente, que le permite controlar lo que los usuarios no pueden hacer.  Esto es especialmente útil cuando el usuario se conecta desde un dispositivo no administrado (por ejemplo, su personal Macbook) y desea controlar el acceso de usuario o experiencia.

Por ejemplo, si está utilizando Active Directory para la autenticación de usuario y desea evitar que los usuarios copien los datos de una aplicación, puede configurar una directiva de grupo de escritorio remoto para bloquear usuarios copien los datos.

Otro ejemplo es si desea bloquear el acceso a internet para una aplicación en particular en la colección. Puede crear una regla de Firewall de Windows que bloquea el acceso al crear la imagen de la colección.

## <a name="implementation-options"></a>Opciones de implementación

  Estas son las opciones de implementación de claves, que pueden usarse de forma individual o conjuntamente según sea necesario:

1.  Si la colección de RemoteApp está unido al dominio puede aplicar una [Directiva de grupo](https://technet.microsoft.com/library/cc725828.aspx) (con la excepción de la Idle y desconectar tiempo de espera directivas descrito [a continuación](../azure-subscription-service-limits.md)).
2.  Como alternativa a la directiva de grupo (si la colección no está unido al dominio o no tiene los privilegios adecuados en Active Directory), puede configurar [Directivas locales](https://technet.microsoft.com/library/cc775702.aspx) en su imagen de la plantilla.  Tenga en cuenta que ese grupo directivas directivas locales de triunfo cuando hay un conflicto.
3.  Algunas configuraciones de sistema operativo o aplicación no son configurables a través de la directiva, pero se pueden establecer mediante la clave del registro mediante la [herramienta de RegEdit](./remoteapp-hybridtrouble.md) al configurar la imagen de la plantilla.
4.  Puede usar [Firewall de Windows](http://windows.microsoft.com/en-US/windows-8/Windows-Firewall-from-start-to-finish) para controlar el acceso de red a y desde el equipo donde se ejecuta la aplicación. Asegúrese de que no bloquear las direcciones URL y puertos definidos aquí.
5.  Puede usar [AppLocker](https://technet.microsoft.com/library/hh831440.aspx) para controlar qué aplicaciones y archivos que los usuarios pueden ejecutar. Por ejemplo, los usuarios avanzados pueden averiguar cómo ejecutar aplicaciones que no publica pero que están disponibles en la imagen que utiliza para crear la colección - AppLocker puede bloquear esto.

## <a name="detailed-information"></a>Información detallada

- Las siguientes directivas RDS suelen ser muy útiles:
    - [Dispositivo y la redirección de recursos](https://technet.microsoft.com/library/ee791794.aspx)
    - [Redirección de impresora](https://technet.microsoft.com/library/ee791784.aspx)
    - [Perfiles](https://technet.microsoft.com/library/ee791865.aspx).
- Tenga en cuenta que configurar redireccionamientos mediante RemoteApp PowerShell módulo (como se muestra [aquí](./remoteapp-redirection.md)) se basa en el equipo cliente para aplicar la directiva, por lo que si la seguridad es el objetivo principal que desee aplicar la directiva a la directiva local de imagen de plantilla o a través de la directiva de grupo.
- [Las directivas de Windows Server 2012 R2](https://technet.microsoft.com/library/hh831791.aspx).
- [Las directivas de Office 2013](https://technet.microsoft.com/library/cc178969.aspx) (incluyendo [cómo personalizar la barra de herramientas de Office](https://technet.microsoft.com/library/cc179143.aspx)).
