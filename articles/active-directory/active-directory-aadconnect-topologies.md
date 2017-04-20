<properties
    pageTitle="Azure AD Connect: Topologías admitidas | Microsoft Azure"
    description="Este tema describe las topologías compatibles y no compatibles de Azure AD Connect"
    services="active-directory"
    documentationCenter=""
    authors="AndKjell"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="billmath"/>

# <a name="topologies-for-azure-ad-connect"></a>Conectan topologías para Azure AD
El objetivo de este tema es describir diferentes local y topologías de Azure AD con la sincronización de Azure AD Connect como la solución de integración clave. Describe las configuraciones compatibles y no compatibles.

Leyenda de imágenes del documento:

Descripción | Icono
-----|-----
Bosque de Active Directory local| ![AD](./media/active-directory-aadconnect-topologies/LegendAD1.png)
Active Directory con la importación de filtrado| ![AD](./media/active-directory-aadconnect-topologies/LegendAD2.png)
Servidor de sincronización de Azure AD Connect| ![Sincronización](./media/active-directory-aadconnect-topologies/LegendSync1.png)
Azure AD Connect servidor "ensayo modo de sincronización"| ![Sincronización](./media/active-directory-aadconnect-topologies/LegendSync2.png)
GALSync con FIM2010 o MIM2016| ![Sincronización](./media/active-directory-aadconnect-topologies/LegendSync3.png)
Servidor de sincronización de Azure AD Connect, detallada| ![Sincronización](./media/active-directory-aadconnect-topologies/LegendSync4.png)
Directorio de Azure AD |![AAD](./media/active-directory-aadconnect-topologies/LegendAAD.png)
Escenario no admitido | ![No compatible](./media/active-directory-aadconnect-topologies/LegendUnsupported.png)

## <a name="single-forest-single-azure-ad-tenant"></a>Un solo bosque, una inquilino de Azure AD
![Bosque único solo inquilino](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

La topología más común es un único bosque local, con uno o varios dominios y un único Azure AD de inquilinos. Para la autenticación de Azure AD, se usa la sincronización de contraseñas. La instalación rápida de Azure AD Connect admite únicamente la topología.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Bosque único, varios servidores de sincronización a un inquilino de Azure AD
![Bosque único filtrado no compatibles](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

No se admite para tener varios servidores de sincronización de Azure AD Connect conectados al mismo inquilino Azure AD, excepto en un [servidor de ensayo](#staging-server). Es incompatible incluso si estas se haya configurado para sincronizar mutuamente conjunto de objetos. Se debería considerar esto si no se puede llegar a todos los dominios del bosque desde un único servidor o para distribuir la carga entre varios servidores.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Varios bosques, un solo inquilino de Azure AD
![Múltiples bosque único inquilino](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

Muchas organizaciones tienen entornos con varios bosques de Active Directory local. Existen varios motivos para tener más de un bosque de Active Directory local. Ejemplos típicos son diseños de bosques de recursos de la cuenta y a un resultado después de una fusión o adquisición.

Cuando haya varios bosques, todos los bosques debe ser único Azure AD Connect acceso al servidor de sincronización. No es necesario unir el servidor a un dominio. Si es necesario para llegar a todos los bosque, el servidor puede colocarse en una red DMZ.

El Asistente para instalación de Azure AD Connect ofrece varias opciones para consolidar los usuarios representados en varios bosques. El objetivo es que un usuario se representa solo una vez en Azure AD. Existen algunas topologías comunes que se pueden configurar en la ruta de instalación personalizada en el Asistente para instalación. Seleccione la opción correspondiente que representa la topología de la página que **identifica los usuarios**. La consolidación sólo está configurada para los usuarios. Grupos duplicados no están consolidados con la configuración predeterminada.

Topologías comunes se describen en la siguiente sección: [topologías independientes](#multiple-forests-separate-topologies), [malla completa](#multiple-forests-full-mesh-with-optional-galsync)y [Recursos de la cuenta](#multiple-forests-account-resource-forest).

Se supone la configuración predeterminada de la sincronización de Azure AD Connect:

1. Los usuarios tienen solo una cuenta habilitada y el bosque donde se encuentra esta cuenta se usa para autenticar al usuario. Esta suposición es para la sincronización de la contraseña y para la federación. UserPrincipalName y sourceAnchor/immutableID proceden de este bosque.
2. Los usuarios tienen sólo un buzón.
3. El bosque que contiene el buzón de un usuario tiene la mejor calidad de datos para los atributos visibles en lista Global de direcciones (GAL) de Exchange. Si no hay ningún buzón en el usuario, cualquier bosque puede utilizarse para poder colaborar estos valores de atributo.
4. Si tiene un buzón vinculado, a continuación, también hay otra cuenta en un bosque diferente que se utiliza para iniciar sesión.

Si su entorno no cumple estos supuestos, ocurre lo siguiente:

- Si tiene más de una cuenta activa o más de un buzón, el motor de sincronización elige uno y omitir el otro.
- Un buzón vinculado con ninguna otra cuenta activa no se exporta a Azure AD. La cuenta de usuario no se representa con un miembro de cualquier grupo. Un buzón vinculado en DirSync siempre se representa como un buzón normal. Este cambio deliberadamente es un comportamiento diferente a mejorar la compatibilidad con varios bosques escenarios.

Encontrará más detalles en la [Descripción de la configuración predeterminada](active-directory-aadconnectsync-understanding-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Varios bosques, varios servidores de sincronización a un inquilino de Azure AD
![Múltiples bosque múltiple sincronización no compatibles](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

No se admite para tener más de un servidor de Azure AD Sync conectar conectado a una sola inquilino de Azure AD. La excepción es el uso de un [servidor de ensayo](#staging-server).

### <a name="multiple-forests--separate-topologies"></a>Varios bosques – topologías independientes
**Los usuarios están representados solo una vez en todos los directorios**

![Una vez los usuarios de bosque múltiple](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![Topologías de separación de bosque múltiple](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

En este entorno, todos los bosques local se trata como entidades independientes y ningún usuario sería presente en cualquier otro bosque.
Cada bosque tiene su propia organización de Exchange y no hay ningún GALSync entre los bosques. Esta topología podría ser la situación después de una fusión o adquisición o en una organización donde está funcionando cada unidad de negocio aisladas entre sí. Estos bosques están en la misma organización en Azure AD y aparecen con una GAL unificada.
En esta imagen, cada objeto en cada bosque se representa una vez en el metaverso y agregado en el inquilino de Azure AD de destino.

### <a name="multiple-forests--match-users"></a>Varios bosques: los usuarios de coincidencia
**Identidades de usuario existen en varios directorios**

Común para todos estos escenarios es distribución y grupos de seguridad pueden contener una mezcla de usuarios, contactos y FSP (principales de seguridad externa)

FSP se usa en agrega para representar a miembros de otros bosques en un grupo de seguridad. Todos los FSP se resuelven en el objeto real en Azure AD.

### <a name="multiple-forests--full-mesh-with-optional-galsync"></a>Varios bosques – completas encajar con GALSync opcional
**Identidades de usuario existen en varios directorios. Hacer coincidir con: atributo de correo**

![Correo de bosque entre varios usuarios](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![Malla completa de bosque múltiple](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Una topología de malla completa permite a los usuarios y recursos ubicarse en cualquier bosque y comúnmente habría confianzas bidireccionales entre los bosques.

Si Exchange está presente en más de un bosque, opcionalmente, podría ser una solución local de GALSync. Todos los usuarios se representa como un contacto en todos los demás bosques. GALSync normalmente se implementa mediante Forefront identidad Manager 2010 o 2016 de administrador de identidad de Microsoft. Azure AD Connect no se pueden usar para GALSync local.

En este escenario, los objetos de identidad se combinan mediante el atributo de correo. Un usuario con un buzón de un bosque se combina con los contactos de los demás bosques.

### <a name="multiple-forests--account-resource-forest"></a>Varios bosques – bosque de recursos de la cuenta
**Identidades de usuario existen en varios directorios. Hacer coincidir con: atributos ObjectSID y msExchMasterAccountSID**

![Bosque entre varios usuarios ObjectSID](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![Bosque múltiple AccountResource](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

En una topología de bosque de recursos de cuenta tiene uno o más bosques cuenta con cuentas de usuario activo. También tiene uno o más bosques de recursos con cuentas deshabilitadas.

En este escenario uno (o más) **bosque de recursos** confía en todos los **bosques de la cuenta**. El bosque de recursos normalmente tiene un esquema de AD ampliado con Exchange y Lync. Todos los servicios de Exchange y Lync, así como otros servicios compartidos se encuentran en este bosque. Los usuarios tienen una cuenta de usuario deshabilitada en este bosque y el buzón está vinculado al bosque cuenta.

## <a name="office-365-and-topology-considerations"></a>Consideraciones de topología y Office 365
Algunas cargas de trabajo de Office 365 tienen algunas restricciones para topologías compatibles. Si planea usar cualquiera de estos, a continuación, lea el tema de topologías compatibles para la carga de trabajo.

Carga de trabajo |  
--------- | ---------
Exchange Online | Si hay más de una organización de Exchange en local (es decir, Exchange se ha implementado en varios bosques), debe utilizar Exchange 2013 SP1 o posterior. Detalles pueden encontrarse aquí: [implementaciones híbridas con varios bosques de Active Directory](https://technet.microsoft.com/library/jj873754.aspx)
Skype empresarial | Si usa varios bosques local, se admite solo la topología de bosque de recursos de la cuenta. Detalles de las topologías compatibles pueden encontrarse aquí: [requisitos medioambientales para Skype empresarial Server 2015](https://technet.microsoft.com/library/dn933910.aspx)

## <a name="staging-server"></a>Servidor de ensayo
![Servidor de ensayo](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect es compatible con la instalación de un segundo servidor en **modo de ensayo**. Un servidor en este modo lee los datos de todos los directorios conectados pero no escribe nada en directorios conectados. Usa el ciclo de sincronización normal y, por tanto, tiene una copia actualizada de los datos de identidad. En un desastre donde el servidor principal no se puede conmutar al servidor de ensayo. Para ello, en el Asistente de Azure AD Connect. En este segundo servidor preferentemente puede estar ubicado en un centro de datos distintos dado que ninguna infraestructura se comparte con el servidor principal. Debe copiar manualmente cualquier cambio de configuración realizado en el servidor principal para el segundo servidor.

Un servidor de ensayo también puede usarse para probar una nueva configuración personalizada y el efecto que tiene en los datos. Puede obtener una vista previa de los cambios y ajuste la configuración. Cuando esté satisfecho con la nueva configuración, puede convertir el servidor activo en el servidor de ensayo y establecer el antiguo servidor activo en modo de ensayo.

Este método también puede usarse para sustituir el servidor de sincronización de active. Preparar el servidor nuevo y configurarlo en modo de ensayo. Asegúrese de que se encuentra en buen estado, deshabilitar (lo que active), el modo de ensayo y cerrar el servidor activo.

Es posible tener más de un servidor de ensayo cuando desee tener varias copias de seguridad en los centros de datos distintos.

## <a name="multiple-azure-ad-tenants"></a>Varios inquilinos de Azure AD
Microsoft recomienda tener un inquilino solo en Azure AD para una organización.
Antes de que planea usar a varios inquilinos de Azure AD, estos temas de escenarios comunes para que pueda utilizar a un solo usuario.

Tema |  
--------- | ---------
Delegación mediante unidades administrativas | [Administración de unidades administrativas en Azure AD](active-directory-administrative-units-management.md)

![Múltiples bosque múltiple inquilino](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Hay una relación de 1:1 entre un servidor de sincronización de Azure AD Connect y un inquilino de Azure AD. Para cada inquilino Azure AD, necesita una instalación de servidor de sincronización de Azure AD Connect. Las instancias de inquilinos de Azure AD son por diseño aislado y los usuarios en una no pueden ver los usuarios en el otro inquilino. Si pretende esta separación, a continuación, se trata de una configuración compatible, pero en caso contrario, debe usar el único modelo de inquilinos de Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Cada objeto solo una vez en un inquilino de Azure AD
![Bosque único filtrada](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

En esta topología, un servidor de sincronización de Azure AD Connect está conectado a cada inquilino de Azure AD. Los servidores de sincronización de Azure AD Connect deben estar configurados para el filtrado para cada uno de ellos tenga un conjunto de objetos para que funcione en se excluyen mutuamente. Por ejemplo, puede enfocar cada servidor de un dominio en particular o una unidad organizativa. Solo se puede registrar un dominio DNS en un único inquilino de Azure AD. El UPN de los usuarios locales en AD debe utilizar también espacios de nombres independientes. Por ejemplo, en la imagen anterior tres UPN independiente sufijos se registran en las instalaciones AD: contoso.com, fabrikam.com y wingtiptoys.com. Los usuarios en cada dominio de Active Directory local usar otro espacio de nombres.

No hay ningún GALsync entre las instancias de inquilinos de Azure AD. La libreta de direcciones en Exchange Online y Skype para Business solo muestra usuarios en el mismo inquilino.

Esta topología tiene las siguientes restricciones a lo contrario admiten escenarios:

- Solo uno de los inquilinos de Azure AD puede habilitar híbrida de Exchange con Active Directory local.
- Dispositivos de Windows 10 solo se pueden asociados con un inquilino de Azure AD.

El requisito de mutuamente conjunto de objetos también se aplica a reescritura. Algunas características de escritura diferida no son compatibles con esta topología puesto que estas características se supone una sola configuración local:

-   Reescritura de grupo con la configuración predeterminada
-   Reescritura de dispositivo

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Cada objeto varias veces en un inquilino de Azure AD
![Bosque único entre varios inquilinos no compatibles](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Bosque único entre varios conectores no compatibles](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

- No es compatible para sincronizar el mismo usuario para varios inquilinos de Azure AD.
- Es compatible para realizar una configuración de modificar para que los usuarios de un anuncio de Azure aparecen como contactos en otro inquilino de Azure AD.
- No es compatible para modificar la sincronización de Azure AD Connect para conectarse a varios inquilinos de Azure AD.

### <a name="galsync-by-using-writeback"></a>GALsync usando reescritura
![MultiForestMultiDirectoryGALSync1Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![MultiForestMultiDirectoryGALSync2Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Inquilinos de Azure AD están aislado de diseño.

- No es compatible para cambiar la configuración de sincronización de Azure AD Connect leer datos del otro inquilino de Azure AD.
- No es compatible para exportar los usuarios como contactos a otro locales con Azure AD Connect la sincronización de AD.

### <a name="galsync-with-on-premises-sync-server"></a>GALsync con el servidor de sincronización local
![MultiForestMultiDirectoryGALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

Es compatible para utilizar FIM2010/MIM2016 local a los usuarios GALsync entre dos organizaciones de Exchange. Los usuarios de una organización se muestra como usuarios o contactos externas en la otra organización. A continuación, se pueden sincronizar estos anuncios local diferente a sus propio inquilinos de Azure AD.

## <a name="next-steps"></a>Pasos siguientes
Para obtener información sobre cómo instalar Azure AD Connect para estos escenarios, vea [instalación personalizada de Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md).

Más información sobre la configuración de [sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
