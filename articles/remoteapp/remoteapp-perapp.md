<properties
   pageTitle="Publicar aplicaciones para usuarios individuales en una colección de Azure RemoteApp (vista preliminar) | Microsoft Azure"
   description="Obtenga información sobre cómo puede publicar aplicaciones para usuarios individuales, en lugar de según grupos de RemoteApp de Azure."
   services="remoteapp-preview"
   documentationCenter=""
   authors="piotrci"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="piotrci"/>

# <a name="publish-applications-to-individual-users-in-an-azure-remoteapp-collection-preview"></a>Publicar aplicaciones para usuarios individuales en una colección de Azure RemoteApp (vista preliminar)

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

En este artículo se explica cómo publicar aplicaciones para usuarios individuales en una colección de RemoteApp de Azure. Esta es la nueva funcionalidad de RemoteApp de Azure, actualmente en "preview privada" y disponible solo para seleccionar los primeros usuarios fines de evaluación.

Originalmente Azure RemoteApp habilitado sólo una manera de "publicación" aplicaciones: el administrador debería publicar aplicaciones de la imagen y estén visibles para todos los usuarios de la colección.

Un escenario común es incluir muchas aplicaciones en una sola imagen e implementar una colección con el fin de reducir los costos de administración. A menudo no todas las aplicaciones son relevantes para todos los usuarios: los administradores preferiría publicar aplicaciones para usuarios individuales para que no verán aplicaciones innecesarias en su aplicación fuente.

Ahora es posible en Azure RemoteApp – actualmente como una característica de vista previa limitado. Aquí es un breve resumen de las nuevas funciones:

1. Una colección de se puede establecer en uno de dos modos:
 
  - el original "modo de colección", donde pueden ver todos los usuarios de una colección de todas las aplicaciones publicadas. Este es el modo predeterminado.
  - el texto nuevo, "modo de aplicación," donde los usuarios verán solo las aplicaciones que se han asignado explícitamente

2. En el momento en el modo de aplicación sólo se puede habilitar usar cmdlets de RemoteApp PowerShell de Azure.

  - Cuando se establece en modo de aplicación, no se puede administrar asignaciones de usuario de la colección a través del portal de Azure. Asignación de usuario tiene que administrarse a través de los cmdlets de PowerShell.

3. Los usuarios verán solo las aplicaciones que se publican directamente en ellos. Sin embargo, es posible seguir para un usuario iniciar las aplicaciones disponibles en la imagen mediante el acceso a ellos directamente en el sistema operativo.
  - Esta característica no proporciona un bloqueo seguro de las aplicaciones. sólo limita la visibilidad en la aplicación de la fuente.
  - Si necesita aislar a los usuarios de aplicaciones, debe usar colecciones separadas para.

## <a name="how-to-get-azure-remoteapp-powershell-cmdlets"></a>Cómo obtener cmdlets de RemoteApp PowerShell de Azure

Para probar la nueva funcionalidad de vista previa, debe usar cmdlets de PowerShell de Azure. Actualmente, no es posible usar el portal de administración de Azure para habilitar el modo de publicación de aplicación nueva.

En primer lugar, asegúrese de que tiene instalado el [módulo de PowerShell de Azure](../powershell-install-configure.md) .

A continuación, inicie la consola de PowerShell en modo de administrador y ejecute el siguiente cmdlet:

        Add-AzureAccount

Le pedirá su nombre de usuario de Azure y la contraseña. Una vez que haya iniciado sesión, podrá ejecutar los cmdlets de RemoteApp de Azure en las suscripciones de Azure.

## <a name="how-to-check-which-mode-a-collection-is-in"></a>¿Cómo puedo comprobar el modo en que un conjunto está en

Ejecute el cmdlet siguiente:

        Get-AzureRemoteAppCollection <collectionName>

![Active el modo de recopilación](./media/remoteapp-perapp/araacllelvel.png)

La propiedad AclLevel puede tener los valores siguientes:

- Colección: la publicación modo original. Todos los usuarios ver que todas las aplicaciones de publicadas.
- Aplicación: el nuevo modo de publicación. Los usuarios ver solo las aplicaciones que se publican directamente en ellos.

## <a name="how-to-switch-to-application-publishing-mode"></a>Cómo cambiar al modo de publicación de aplicación

Ejecute el cmdlet siguiente:

        Set-AzureRemoteAppCollection -CollectionName -AclLevel Application

Se conserva el estado de publicación de aplicación: inicialmente todos los usuarios verán las aplicaciones publicadas originales.

## <a name="how-to-list-users-who-can-see-a-specific-application"></a>Cómo los usuarios de la lista que se puede ver una aplicación específica

Ejecute el cmdlet siguiente:

        Get-AzureRemoteAppUser -CollectionName <collectionName> -Alias <appAlias>

Enumera todos los usuarios que pueden ver la aplicación.

Nota: Puede ver el alias de la aplicación (denominados "alias de aplicación" en la sintaxis anterior) ejecutando AzureRemoteAppProgram Get - CollectionName <collectionName>.

## <a name="how-to-assign-an-application-to-a-user"></a>Cómo asignar una aplicación a un usuario

Ejecute el cmdlet siguiente:

        Add-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

El usuario podrá ver la aplicación en el cliente de RemoteApp de Azure y podrá conectarse a él.

## <a name="how-to-remove-an-application-from-a-user"></a>Cómo quitar una aplicación de un usuario

Ejecute el cmdlet siguiente:

        Remove-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

## <a name="providing-feedback"></a>Proporcionar comentarios
Agradecemos sus comentarios y sugerencias sobre esta característica de vista previa. Rellene la [encuesta](http://www.instant.ly/s/FDdrb) para comunicar su opinión.

## <a name="havent-had-a-chance-to-try-the-preview-feature"></a>¿No ha tenido la oportunidad de probar la característica Vista previa?
Si no ha participado en la vista previa todavía, utilice esta [encuesta](http://www.instant.ly/s/AY83p) para solicitar acceso.
