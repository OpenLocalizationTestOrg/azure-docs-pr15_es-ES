<properties
   pageTitle="Sincronización de Azure AD Connect: extensiones Directory | Microsoft Azure"
   description="En este tema se describe la característica de extensiones de directorio de Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/19/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-directory-extensions"></a>Sincronización de Azure AD Connect: extensiones de directorio
Extensiones de directorio le permite ampliar el esquema de Azure AD con sus propios atributos de local de Active Directory. Esta característica le permite crear aplicaciones LOB consumo atributos que puede seguir administrando local. Estos atributos pueden consumir a través de [las extensiones de directorio de Azure AD gráfico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) o [Microsoft Graph](https://graph.microsoft.io/). Puede ver los atributos disponibles con [el Explorador de Azure AD gráfico](https://graphexplorer.cloudapp.net) y el [Explorador de Microsoft Graph](https://graphexplorer2.azurewebsites.net/) , respectivamente.

En este momento no hay carga de trabajo de Office 365 consume estos atributos.

Configurar qué atributos adicionales que desee sincronizar en la ruta de acceso de una configuración personalizada en el Asistente para instalación.
![Asistente para la extensión de esquema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png) la instalación muestra los siguientes atributos, que son candidatos válidos:

- Tipos de objeto de usuario y de grupo
- Atributos de un solo valor: cadena, booleano, entero, binario
- Atributos de valores múltiple: cadena, binaria

La lista de atributos se lee de la memoria caché creada durante la instalación de Azure AD Connect. Si ha ampliado el esquema de Active Directory con atributos adicionales, el [esquema debe actualizarse](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) antes de los nuevos atributos están visibles.

Un objeto puede tener hasta 100 atributos de extensiones de directorio. La longitud máxima es de 250 caracteres. Si un valor de atributo es más largo, se trunca el motor de sincronización.

Durante la instalación de Azure AD Connect, se registra una aplicación donde estos atributos están disponibles. Puede ver esta aplicación en el portal de Azure.  
![Aplicación de la extensión de esquema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3.png)

Estos atributos ahora están disponibles a través de gráfico:  
![Gráfico](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Los atributos son el prefijo con extensión\_{AppClientId}\_. El AppClientId tiene el mismo valor para todos los atributos en el directorio de Azure AD.

## <a name="next-steps"></a>Pasos siguientes
Más información sobre la configuración de [sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
